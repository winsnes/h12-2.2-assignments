Tema 17 - Økt 12 - Rails Project Implementation Validate & Notify Workshop
==========================================================================

## Validation :name

Validering skjer i modellen, så la oss starte der

Åpne app/models/project.rb

Det enkleste å validere er at et felt er fylt inn

Vi krever på det minste at et navn er fylt inn

```ruby
validates :name, presence: true
```

Test valideringen med å opprette et nytt prosjekt

Fyll inn alle feltene utenom navnet

Alle feltene beholder sine verdier, men brukeren blir kastet tilbake til skjemaet

Grunnen til at det feiler er at det mangler et navn, men vi har ikke informert brukeren om dette enda

## Notification

Vi trenger derfor å sørge for at brukeren får vite hva som er feil

Åpne app/views/projects/_form.html.erb

Her skal vi sjekke om det finnes noen feil på @projects objektet

Om det finnes noen, så skal vi liste dem opp

Vi sjekker dette mot @project sin .errors egenskap, og sjekker om det finnes noen feil ved å kjøre metoden .any?

```erb
<% if @project.errors.any? %>

<% end %>
```

Når vi har sjekket dette så kan vi lage en struktur for å presentere feilmeldingene

```erb
<div id="error_explanation">

  <ul>
    <% @project.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
  </ul>

</div>
```

For ekstra forklaring legger vi også til en liten overskrift for å vise hva denne listen er

```erb
<h2><%= pluralize(@project.errors.count, "error") %> prohibited this project from being saved:</h2>
```

Med denne strukturen kan vi liste opp alle feilene som skjer ved lagring av modellen

Forsøk nå å legge inn et prosjekt uten navn, og se at du får opp feilmeldingen

Inspiser også koden og legg merke til noe annet nyttig

Label og input for prosjektnavn er nå pakket inn i hver sin div med klassen "field_with_errors"

Dette er veldig nyttig ettersom vi kan bruke dette for å lede brukerens oppmerksomhet til feltet som er problematisk

Lag en ny fil:

```
app/assets/stylesheets/forms.css.scss
```

Legg så inn følgende

```sass
form {

  .field_with_errors {

    input {
      border-color: rgba(191, 64, 64, 1);
      background-color: rgba(255, 191, 191, 1);
    }

  }
  
}

#error_explanation {
  margin-bottom: 2em;
  padding: 10px;
  border-radius: 10px;
  border: 2px solid rgba(255, 191, 191, 1);
  background-color: rgba(255, 223, 223, 1);
  color: rgba(191, 64, 64, 1);
}
```

Nå har vi en egen CSS fil som kan ha ansvar for å stilsette HTML forms, og vi har stilsatt feilmeldinger på en synlig måte

Det andre problemet vi merket var at man kunne skrive inn noe som ikke var en URL

## Validation :url

Ettersom vi har satt input type til "url" så vil de aller fleste nettlesere prøve å forhindre oss fra å sende inn noe annet på egenhånd

Men det skal ikke mye til for å komme rundt dette

(Bruk Inspection Tool for å endre type til "text", og send inn skjema)

Vi må derfor ta ansvar for å validere input på modellen også

Gå tilbake til app/models/project.rb

I tillegg til å kun validere at noe er med, så kan vi også validere formatet på feltet
  
Metoden validates_format_of gir oss muligheten til å validere formatet mot en regular expression

Metoden ser slik ut:
  
```ruby
validates_format_of :url, with: [REGEXP]
```

Vi har vært så vidt borti regular expressions tidligere, og husker at de veldig fort kan bli veldig komplekse

Når vi nå skal matche mot en URL struktur, så er det mange ting vi må ta med i beregningen

Heldigvis har Rails en liten hjelper til, URI.regexp

I tillegg ønsker vi å tillate at feltet er tomt i tilfelle prosjektet ikke er en webside

Skriv derfor inn følgende
  
```ruby
validates_format_of :url, with: URI.regexp, allow_blank: true
```

Om vi nå forsøker å legge inn et prosjekt med en illegal URL (ved å "hacke" med Developer Tools), så får vi en feilmelding om dette

Om vi nå prøver å ha tomt navn og samtidig en feil url, så får vi da listet opp begge disse feilene øverst på siden

Og det er fremdeles lov å ikke ha en URL

Nyttig!

## Presentation

Til slutt skal vi gjøre én endring til

Når vi ser på et prosjekt med projects#show så viser vi nå prosjektets URL

Men om det ikke er lagt inn en URL, så vises fremeles paragrafen hvor URL'en skulle stått

Vi må muligens bruke Developer Tools for å finne linken, men den er der likevel

Om URL er blank, så kan vi like greit droppe å ha den med i HTML'en

Legg derfor inn følgende rundt visningen av linken:

```erb
<% if !@project.url.blank? %>
  <p>
    <%= link_to @project.url %>
  </p>
<% end %>
```

Ettersom "if !something" ikke er veldig intuitivt å lese, så har Ruby inkludert en alternativ måte å skrive dette på

Vi kan derfor erstatte vår "if !something" linje med følgende

```erb
<% unless @project.url.blank? %>
```

Convenient!
