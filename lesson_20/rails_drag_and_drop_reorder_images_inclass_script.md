Tema 17 - Økt 17 - Rails Project Bonus Implementation Drag & Drop Images Workshop
=================================================================================

## Images model

For å ta vare på hvilken plass hvert bilde skal ha på prosjektet, så trengs det en ekstra verdi

Denne verdien er kun en nummerverdi som viser til bildets posisjon innenfor prosjektet

Vi lager derfor en migration

```
rails generate migration AddPositionToImages position:integer
```

Deretter påfører vi Image tabellen i databasen dette nye feltet

```
rake db:migrate
```

Vi har nå mulighet til å sortere bildene som vises etter **position** i stigende rekkefølge

Dette legger vi til i Image modellen som et **default_scope**

```ruby
default_scope { order('position') }
```

## Images controller

Vi trenger nå en måte å påføre bildene denne egenskapen

Det krever at vi legger til en ny controller metode som ikke er en av de vanlige REST handlingene

Vi skal derfor legge til denne nye metoden i vår **images_controller.rb**

Metoden kan gjerne ligge langt nede i filen, men den _må_ ligge over linjen med **private**

```ruby
def reorder
  @images = @project.images

  for image in @images
    image.position = params['image'].index(image.id.to_s) + 1
    image.save
  end

  render nothing: true
end
```

Variabelen **@project** kommer fra **before_action :set_project** som blir kjørt før alle metodene i kontrolleren

Denne nye **reorder** metoden tar i mot en forespørsel som inneholder en parameter kalt _image_

Denne parameteren inneholder ID’en til alle bildene lagt i den rekkefølgen som de skal ha på prosjektet

Tanken er da at hver gang denne forespørselen taes i mot, så oppdateres **position** egenskapen til alle bildene på prosjektet

## Images routes

Det neste vi må gjøre er å informere Rails applikasjonen om at denne metoden finnes og kan ta i mot forespørsler

Vi gjør dette ved å legge den til i vår **routes.rb** fil, men denne gangen på en litt annen måte

Finn frem linjen hvor det nå står

```ruby
resources :images
```

Erstatt denne med følgende:

```ruby
resources :images do
  collection do
    post :reorder
  end
end
```

På denne måten har vi gjort det mulig å kalle på metoden **reorder** med en POST request sendt til samlingen med bilder på prosjektet

Vi kan se hvordan dette ser ut ved å kjøre kommandoen

```
rake routes
```

Resultatet inneholder nå en ekstra route:

```
reorder_project_images  POST  /projects/:project_id/images/reorder(.:format)  images#reorder
```

Dette er routen vi skal kalle på med JavaScript

## Images view

Visningen av bildene i projects#show viewet er nå bygget opp av _lister_ som inneholder _figures_ og en ny _liste_

Vi skal nå først gjøre en liten endring ved å gi hver **li.image** element i listen en egen ID basert på bildet sin ID

Endre derfor denne linjen

```erb
<li class=“image”>
```

Til dette

```erb
<li class=“image id=“image_<“%= image.id %>”>
```

Vi skal også legge til en ekstra egenskap på **ul.show_images** som indikerer hvilket prosjekt vi befinner oss på

Endre derfor denne linjen

```erb
<section class=“show_images”>
```

Til dette

```erb
<section class=“show_images” data-project-id=“<%= @project.id %>”>
```

Nå kan vi legge til JavaScript koden for å implementere endringene

## Images assets

For å få en relativt stabil implementasjon av drag & drop, så benytter vi jQuery UI

Den enkleste måten å legge til jQuery UI er å bruke gem’en [jquery-ui-rails](https://github.com/joliss/jquery-ui-rails)

Åpne prosjektets **Gemfile** og finn følgende linje:

```ruby
gem ‘jquery-rails’
```

Rett etter denne kan du legge inn følgende:

```ruby
gem 'jquery-ui-rails'
```

Oppdater deretter prosjektets gems med bundler

```
bundle install
```

Åpne deretter **app/assets/javascripts/application.js** og finn følgende linje

```js
//= require jquery
```

Legg inn følgende linje rett nedenfor denne

```js
//= require jquery.ui.all
```

Åpne også **app/assets/css/application.css** og finn linjen med

```css
*= require_self
```

Legg inn følgende linje rett etter denne

```css
*= require jquery.ui.all
```

_Husk å restarte serveren etter disse endringene_

Nå har vi rammeverket vi trenger, og nå kan vi starte på koden for å lage funksjonaliteten vi ønsker

## Images Drag & Drop JavaScript

I mappen **app/assets/javascripts/** så ligger det en rekke filer med navn som slutter på **.js.coffee**

Disse er CoffeeScript-filer 

[CoffeeScript](http://coffeescript.org/) er ikke noe vi rekker å bruke tid på i dette temaet, så vi lar de ligge urørt akkurat nå, men om man skal skrive mye JavaScript så kan det være verdt å ta en kikk på

Opprett heller en ny fil her: **/app/assets/javascripts/images.js**

I denne filen skal vi skrive funksjonaliteten for å endre rekkefølgen på bildene:

```js
$(function() {

  var projectId = $('.show_images').data('project-id');

  $('.show_images .images').sortable({
    items: '.image',
    axis: 'y',
    placeholder: ‘ui-state-highlight’,
    update: function(event, ui) {
      $.ajax({
        type: 'post',
        data: $('.show_images .images').sortable('serialize'),
        dataType: 'script',
        url: "/projects/" + projectId + "/images/reorder"
      })
    }
  })

  $('.show_images .images').disableSelection();

})
```

Restart nå serveren, forsøk å dra bildene rundt, refresh siden og se at de beholder sin nye posisjon
