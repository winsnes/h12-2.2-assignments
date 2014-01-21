Tema 17 - Økt 12 - Rails Project Implementation Workshop
========================================================

## Model

La oss starte med å bygge opp modellen

For å unngå unødvendig kompleksitet i første omgang så utelater vi relasjonen til Client i første omgang

Som vi så tidligere så har Rails generatorer for mange forskjellige ting

En av disse var Scaffolds

En annen er en generator for Modeller

La oss benytte denne

```
rails generate model Project name description:text url delivered_at:date
```

Denne generatoren gir oss to filer vi skal fokusere på nå:

- db/migrate/[datestamp]_create_projects.rb
- app/models/project.rb

Før vi går videre så tar vi en kikk på disse to

Modell-filen til Prosjektet er ganske tom og ligner så langt veldig på modell-filen til Client

Migration-filen har derimot litt mer innhold

Vi ser her at vi har oppskriften for å lage database-tabellen for å lagre prosjekter

Det er også verdt å merke at feltene som vi kun ga navn til i terminal-kommandoen "rails generate model" har blitt satt med String som type

Nederst har vi igjen t.timestamps, som forrige gang

Når vi har sjekket at alt er riktig skrevet her så kan vi gå videre i kodingen

### Migration

Vi kan nå migrere inn disse endringene i vår database

```
rake db:migrate
```

Når vi nå har lagt til denne delen av koden så kan det være verdt å lage seg et veimerke, eller en "commit"

På den måten har vi et holdepunkt å gå tilbake til dersom noe skulle gå galt i den neste delen av prosessen

Vi har kun kjørt én generator og én migration, så vi kan være relativt sikre på at alle filene skal være med denne gangen

```
git status
```

```
git add .
```

```
git commit -m 'Added basic model for Projects. Now starting implementation of Controller and Views.'
```

Deretter kan vi bevege oss inn i implementering av selve funksjonaliteten

## Controller

På samme måte som Rails har en generator for Scaffold og Model, så er det også en for Controller

Denne generatoren er noe mer utfyllende, og kan generere litt flere filer enn Model generatoren

Der Model generatoren tok i mot navn på feltene i modellen, tar Controller generatoren i mot navn på metoder / actions

De metodene vi listet opp i sted er like fordi de er en del av et sett med standard RESTful handlinger

Det vil si at de aller fleste ressurser som vi trenger å utføre CRUD på vil inneholde disse metodene

Det finnes en generator som gjør en stor del av jobben for oss, men ettersom vi nå ønsker å bli litt kjent med Rails så skal vi ikke bruke den

Vår Rails kommando vil derfor ende opp med å se slik ut (merk flertallsform av Project)

```
rails generate controller Projects index show new create edit update destroy
```

Ok, hva har blitt generert for oss nå da?

Vi ser en lang liste som involverer både nye filer ("create") og endringer i routes.rb filen ("route")

Vi starter øverst, med route-endringene

Åpne config/routes.rb

Her er det lagt til en "get" rute for hver metode på Project controlleren

Det virker litt unødvendig holdt opp mot den ene linjen som refererer til :clients

Ettersom alle metodene vi har lagt til er en del av standard RESTful-settet så kan vi allerede her gjøre vår første endring

Fjern "get"-linjene og legg heller inn prosjekter som en ressurs

```ruby
resources :projects
```

Rails vet selv hvilke handlinger som dette gjør tilgjengelig "by default"

Det er mulig å dobbeltsjekke at du har det du trenger ved å spørre Rake

```
rake routes
```

Her ser vi alle de forskjellige metodene for både Project og Client

De neste filene som ble opprettet ble lagt inn i en struktur som ligner Client sitt oppsett;
En rekke .html.erb-filer under app/views/projects/

Enkelte av disse view-filene trenger vi egentlig ikke ettersom metodene kun er mellomlandinger for mottak av data

Disse filene er create.html.erb, update.html.erb og destroy.html.erb

Vi kan derfor slette disse like godt først som sist

Det kan enten gjøres fra Finder, Sublime eller terminalen

```
rm app/views/projects/create.html.erb app/views/projects/update.html.erb app/views/projects/destroy.html.erb
```

Dermed har vi et utgangspunkt for å starte programmeringen

Åpne app/controllers/projects_controller.rb og så starter vi på toppen

### Index

Her har vi en side for å liste opp prosjektene

Start opp serveren og se hvordan dette ser ut så langt

```
http://localhost:3000/projects
```

View-filene som ble generert inneholder en hjelpsom tekst som henviser oss til hvor de ligger

Åpne denne filen i Sublime ( app/views/projects/index.html.erb )

Ettersom vi ikke har noen prosjekter å liste opp så har ikke denne så stor rolle helt enda

Men kanskje vi skulle legge inn en link for å legge til et nytt prosjekt?
(Bare slett det innholdet som er der allerede)

Når vi ønsker å lage en link så trenger vi tre ting:

- En kommando for å bygge linken
- Teksten som utgjør linken
- Hvor linken skal peke til

Kommandoen vi trenger er link_to

Teksten velger vi selvsagt selv, men la oss inspirere av måten Rails gjorde det sist: "New Project"

Om du er usikker på hvor du ønsker å peke, sjekk gjerne "$ rake routes" på nytt (hint: legg til _path på slutten)

Resultatet av dette gir oss følgende:

```erb
<%= link_to 'New Project', new_project_path %>
```

Når vi følger denne linken så kommer vi videre til neste Metode / View

### New

Også her får vi et hint om hvor denne filen befinner seg

Åpne nå både projects_controller.rb og new.html.erb

Her er siden hvor vi kan legge inn informasjon om et nytt prosjekt

Vi trenger derfor et HTML form

Nok en gang kan vi lene oss på Rails sine view helpers, denne gangen i form av en form_builder

Strukturen her er i hovedsak en blokk som starter med form_for

Vi starter derfor med selve blokken (husk på å bruke <%= %> og <% %> rundt Ruby koden, da dette fremdeles er en .html.erb fil)
(Slett innholdet som er der fra før av)

```erb
<%= form_for(@project) do |f| %>

<% end %>
```

Denne strukturen ser kjent ut fra Client implementasjonen

Refresh nå nettleseren og se hva som skjer

Her har vi en feilmelding, men det var forutsett

"First argument in form cannot contain nil or be empty"

@project er nil

Hvor skulle vi få @project fra?

Legg til @project i projects#new

Nå får vi ikke feilmelding lenger

Da kan vi begynne å fylle inn HTML form-elementene

For å få det litt oversiktlig så kan vi pakke inn disse elementene på samme måte som de ble pakket inn i vårt forrige form

Husk å bygge opp med både label og input felt

```erb
<div class="field">
  <%= f.label :name %><br>
  <%= f.text_field :name %>
</div>
```

Description feltet er litt anderledes ettersom det er en :text og ikke en :string

Vi bruker derfor f.text_area her for å få riktig type felt

```erb
<div class="field">
  <%= f.label :description %><br>
  <%= f.text_area :description %>
</div>
```

DeliveredAt har vi merket som en dato, og vi kan nå lene oss på HTML5 sin nye input type "date"

Vi sender med dette som det andre argumentet til f.text_field

```erb
<div class="field">
  <%= f.label :delivered_at %><br>
  <%= f.text_field :delivered_at, type: :date %>
</div>
```

Url har også en egen input type i HTML5, så vi benytter dette her også

Det har ingen praktisk betydning utenom det tilpassede tastaturet for mobile brukere, men det er en viktig touch

```erb
<div class="field"> 
  <%= f.label :url %><br>
  <%= f.text_field :url, type: :url %>
</div>
```

Til slutt trenger vi å legge til en knapp for å sende inn skjemaet

```erb
<div class="actions">
  <%= f.submit %>
</div>
```

Dermed har vi bygget opp skjemaet vi trenger for å registrere et nytt prosjekt

Det er ikke spesielt pent, men det skal fungere for oss inntil videre

Forsøk nå å fylle feltene og så sende inn skjemaet

Template is missing?

Dette ser ut til å referere til en av filene vi slettet etter "$ rails generate controller"?

Skulle vi kanskje ikke slettet disse likevel?

Joda, vi skulle slette de

Men nå er vi videre inn i neste controller action

### Create

Oppgave til Create er å ta i mot data fra feltene i forrige action, og sende disse til Modellen

Dette gjøres ved at vi oppretter et objekt med innholdet fra feltene, og forsøker å be Modellen om å lagre det

Dette er en metode som ikke trenger et eget View (det var derfor vi slettet View filen)

Vi skal derfor kun skrive kode i Controlleren

