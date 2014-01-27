Tema 17 - Økt 17 - Rails Project Implementation Images & CarrierWave Workshop
==================================================================================

## Images model

Strukturen vi benytter krever at vi først trenger en Images modell

Denne modellen vil inneholde informasjon om hvor bildefilen ligger, en caption til bildet, og hvilket prosjekt bildet hører til

Steg én er derfor å legge til denne modellen

```
rails g model Image caption file project:references
```

project:references er en alternativ måte å koble sammen modellene ved å også legge til en ekstra indeksering av koblingene

Denne indekseringen kan gjøre det raskere å søke i databasen på et senere tidspunkt

I tillegg legger dette også til relasjonsinformasjonen på Image modellen

```ruby
belongs_to :project
```

Relasjonen på Project må vi legge til selv, åpne derfor modellfilen **project.rb**

```ruby
has_many :images
```

Vi må også huske på å migrere inn disse endringene i databasen vår

```
rake db:migrate
```

For å kunne behandle bildene som ressurser i applikasjonen vår så trenger vi å legge til disse i **routes.rb** også

Denne gangen ser det litt anderledes ut

Vi skal nå nøste :images som en ressurs under :projects

Det fører til at hvert bilde vil få en egen URI som også inneholder hvilket prosjekt det hører til

Finn derfor frem linjen med **resources :projects** og endre den til følgende

```ruby
resources :projects do |project|
  resources :images
end
```

Nå trenger vi en controller som kan ta i mot de nødvendige forespørselene fra brukeren om bildene

Ettersom de fleste av handlingene vi trenger ikke behøver egne view filer, så kan vi utelate hvilke metoder vi trenger

```
rails g controller Images
```

Deretter kan vi legge til de metodene vi har behov for

```ruby
def create
end

def edit
end

def update
end

def destroy
end
```

De controller metodene vi har utelatt er da **new**, **show** og **index**

Grunnen til dette er at vi vil legge inn muligheten til å laste opp et bilde og opplistningen av bildene i projects#show

Visningen av ett enkelt bilde vil vi også integrere i et eksisterende view

Vi kan nå starte på funksjonaliteten med å legge inn bilder på prosjektete

Det første vi trenger er et skjema, åpne derfor **app/views/projects/show.html.erb**

Legg så til følgende kode

```erb

<h2>Project images</h2>

<section class="new_image">
  <h3>Upload new image</h3>

  <%= form_for [@project, @project.images.new] do |f| %>

    <div class="field">
      <%= f.label :caption %><br>
      <%= f.text_field :caption %>
    </div>

    <div class="actions">
      <%= f.submit %>
    </div>

    
  <% end %>
</section>
```

Vi har ikke lage funksjonaliteten for å ta i mot en bildefil enda

Metoden form_for har i dette tilfellet en litt annen form enn vi har sett tidligere

Dette er fordi vi har nøstet bildene under prosjektet

Resultatet av dette er at når vi nå sender inn skjemaet så sendes dette som en POST request til /projects/:id/images

Den klager også over at det ikke finnes noe view under **images/create**, og det er denne funksjonaliteten vi nå trenger å lage

Åpne **images_controller.rb** og gå til metoden **create**

La oss se hva som ble sendt inn

```ruby
throw params
```

Her får vi en lite informativ feilmelding

Grunnen til dette er at parameterene ikke er tilgjengelige direkte i kontrollerens metode

Vi må derfor gjøre disse tilgjengelige ved å implementere metoden **image_params**

Legg til følgende nederst i kontrolleren

```ruby
private

  def image_params
    params.require(:image).permit(:caption)
  end
```

Vi må også endre **params** til **image_params** i images#create metoden

Nå kan vi skrive koden for å lagre "bildet" på prosjektet

```ruby
def create
  @project = Project.find(params[:project_id])
  @image = @project.images.build(image_params)
  
  if @image.save
    redirect_to @project, notice: "Bildet ble lagt til på prosjektet"
  else
    redirect_to @project, alert: "En feil oppsto dessverre"
  end
end
```

For å kunne vise tilbakemeldingen vi legger i **notice** og **alert** feltene, så må det printes ut et sted

Det er noe som kan være nyttig for hele applikasjonen, så la oss legge det ett felles sted: **views/layouts/application.html.erb**

Legg til følgende inne i **#content_wrapper**, men _før_ **yield**

``erb
<% if notice %>
  <p class="notice"><%= notice %></p>
<% end %>

<% if alert %>
  <p class="alert"><%= alert %></p>
<% end %>
```

Nå blir det opprettet Image objekter som lagres i databasen, men de vises ikke noe sted

Dette skal vi nå legge til i **app/views/projects/show.html.erb** rett over vår section.new_image

```erb
<section class="show_images">
  <ul class="images">
    <% for image in @project.images %>
      <li class="image">
        <figure>
          <figcaption>
            <%= image.caption %>
          </figcaption>
        </figure>
      </li>
    <% end %>
  </ul>
</section>
```

Så langt er dette alt som finnes av informasjon om våre lagrede "bilder"

Vi skal nå legge til funksjonaliteten for å redigere "bildene"

Legg derfor til en link på slutten av hvert bilde (inne i **li.image** elementet, rett etter **figure**) på denne måten

```erb
<li class="image">
  <figure>
    <figcaption>
      <%= image.caption %>
    </figcaption>
  </figure>
  <ul class="manage">
    <li class="edit">
      <%= link_to 'Edit image', edit_project_image_path(@project, image) %>
    </li>
    <li class="destroy">
      <%= link_to 'Destroy image', project_image_path(@project, image), method: :delete, data: {confirm: 'Are you sure you wish to delete this image?'} %>
    </li>
  </ul>
</li>
```

Disse _path anvisningene er også litt mer avanserte takket være nøstingen av ressursene

Vi trenger derfor å informere om både hvilket prosjekt og hvilket bilde vi ønsker å redigere og slette

Nå kan vi legge til koden for å utføre disse handlingene i **images_controller.rb**

```ruby
def edit
  @project = Project.find(params[:project_id])
  @image = @project.images.find(params[:id])
end
```

Deretter må vi bygge opp de nødvendige feltene for å redigere "bildet" i **app/views/images/edit.html.erb**

```erb
<h2>Edit image</h2>

<%= form_for [@project, @image] do |f| %>

  <div class="field">
    <%= f.label :caption %><br>
    <%= f.text_field :caption %>
  </div>

  <div class="actions">
    <%= f.submit %>
  </div>

<% end %>
```

Og nå, funksjonaliteten for å ta i mot oppdateringen av "bildet"

```ruby
def update
  @project = Project.find(params[:project_id])
  @image = @project.images.find(params[:id])

  if @image.update_attributes(image_params)
    redirect_to @project, notice: "Bildet ble oppdatert"
  else
    render action: 'edit'
  end
end
```

Til slutt mangler vi bare funksjonaliteten for å slette "bildet"

```ruby
def destroy
  @project = Project.find(params[:project_id])
  @image = @project.images.find(params[:id])

  @image.delete
  redirect_to @project, notice: "Bildet ble slettet"
end
```

Da har vi fått på plass funksjonaliteten for å behandle bilder

Det vil si, funksjonaliteten for å behandle bilder, bare uten bilder

Vi trenger nå å legge til selve bildefilene

Til dette skal vi benytte [CarrierWave](https://github.com/carrierwaveuploader/carrierwave)

Før vi starter på dette, så skal vi lage en commit

```
git commit -am 'Added basic Image model with basic management, now adding CarrierWave'
```

## CarrierWave

For å få tilgang til CarrierWave sin funksjonalitet, så trenger vi å legge til gem'en i vår **Gemfile**

```ruby
gem 'carrierwave'
```

Og deretter kjøre 

```
bundle install
```

CarrierWave er bygget opp for å fungere som et behandlingsverktøy for opplastning av de fleste typer filer

Vi må derfor først be CarrierWave om å bygge en "uploader" for oss

```
rails generate uploader File
```

Dette gir oss filen

```
app/uploaders/file_uploader.rb
```

Inneholdet i denne filen gir oss noen hint om mulige konfigurasjoner og innstillinger, men vi skal ikke se så veldig nærme på disse helt enda

Vi skal nå koble sammen **Image** modellen med **CarrierWave** sin **FileUploader**

Dette gjør vi ved å legge til følgende i **app/models/image.rb**

```ruby
mount_uploader :file, FileUploader
```

Dette kobler sammen CarrierWave med feltet _file_ som vi opprettet på Image modellen vår i starten

Vi kan nå utvide funksjonaliteten vår med muligheten til å laste opp bilder

Vi starter med vårt new_image form som ligger i **app/views/projects/show.html.erb**

Når vi utvidet et vanlig HTML form med muligheten til å ta i mot filer, så måtte vi utvide åpnings-taggen til form'et

Utvidelsen informerte serveren om at det som kom nå var et vedlegg av én eller annen type

Vi må nå gi rails sin form builder denne informasjonen

Endre linjen med **form_for** til følgende:

```erb
<%= form_for [@project, @project.images.new]**, html: {multipart: true}** do |f| %>
```

Deretter skal vi legge til feltet for å kunne laste opp en fil

```erb
<div class="field">
  <%= f.label :file %><br>
  <%= f.file_field :file %>
</div>
```

Om vi nå forsøker å legge til et bilde, så ser det meste helt likt ut

Hvor ble da bildet av?

For å se det, så må vi utvide listingen av bildene

Utvid derfor **<figure>** koden til følgende:

```erb
<figure>
  <%= image_tag image.file.url %>
  <figcaption>
    <%= image.caption %>
  </figcaption>
</figure>
```

Det ser fremdeles ganske likt ut..?

Forsøk å laste opp et nytt bilde, men hold denne gangen et øye med loggen i terminalvinduet

```
Unpermitted parameters: file
```

Vi må altså bekrefte at vi ønsker muligheten til å ta i mot parameterene for bildefilen i kontrolleren

Utvid **image_params** i **app/controllers/images_controller.rb** til å også godta **:file**

```ruby
params.require(:image).permit(:caption, :file)
```

Nå skal du kunne laste opp bildet

Om du nå følger **edit** linken så kommer du til et noe begrenset skjema

Her må du bestemme deg for noe avgjørende

Ettersom det ikke er snakk om å redigere selve bildefilen, så er det muligens ikke nødvendig å ha med bildet i ditt edit view

Alternativet er å kunne laste opp et nytt bilde over det eksisterende

Vi kommer til å implementere dette for å kunne demonstrere hvordan det fungerer, men det er ikke nødvendigvis noe som er nødvendig eller praktisk for alle tilfeller

Legg til følgende i **app/views/images/edit.html.erb**

```erb
<div class="field">
  <%= image_tag @image.file.url %>
  <p>
    Upload a new image to overwrite the current one
  </p>

  <%= f.label :file %><br>
  <%= f.file_field :file %>
</div>
```

Brukeren kan nå laste opp et nytt bilde "over" det gamle

Den grunnleggende funksjonaliteten er nå på plass, og vi kan starte en liten **refactoring**

## Refactor

I vår kontroller har vi et par linjer som føles overflødige og gjentagende

```ruby
@project = Project.find(params[:project_id])
```

Denne linjen blir kjørt i toppen av alle metodene våre, og kan trygt flyttes til en egen **before_action**

```ruby
before_action :set_project
```

```ruby
private
  def set_project
    @project = Project.find(params[:project_id])
  end
```

Den neste linjen som gjentas i 3 av 4 metoder er denne:

```ruby
@image = @project.images.find(params[:id])
```

La oss også flytte denne fil en egen **before_action**, men husk å legge denne inn **under** :set_project sin before_action

```ruby
before_action :set_image, only: [:edit, :update, :destroy]
```

Legg så til metoden :set_image under :set_project

```ruby
def set_image
  @image = @project.images.find(params[:id])
end
```

Til slutt kan vi krympe vår **edit** metode

```ruby
def edit; end
```

Da kan vi si oss fornøyd med vår refactoring inntil videre

Det neste punktet på listen er presentasjonen av bildene

Slik de ser ut nå, så brukes de i en 1:1 skala

Vi kan alltids tilpasse dem med CSS, men det virker unødvendig å la brukeren laste ned hele bildet i en visning som kun trenger det i 1/2 eller 1/4 størrelse

Dette er noe vi kan bruke CarrierWave til

CarrierWave har også en rekke annen praktisk funksjonalitet som vi kan utnytte

Men først må vi committe vårt arbeid

```
git commit -am 'Added CarrierWave to enable uploading of image files'
```
