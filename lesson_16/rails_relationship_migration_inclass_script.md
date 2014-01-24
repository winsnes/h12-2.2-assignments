Tema 17 - Økt 12 - Rails Project Implementation Relationship & Migrations Workshop
==================================================================================

## Relationship: Project - Client

Relasjonen mellom modeller i en relasjonsdatabase skjer ved bruk av primærnøkler og fremmednøkler.

Ettersom det er en “én til mange” relasjon hvor én kunde kan ha flere prosjekter, så trenger vi å legge til fremmednøkkelen på prosjektene

Et prosjekt har ansvar for å vite hvilken kunde det tilhører

Vi må derfor legge til client_id på prosjektene

For å gjøre dette mulig i databasen så må dette feltet legges til i Project tabellen

Rails har også en generator for dette, nemlig “rails generate migration”

Hver migrasjon trenger å ha et navn, og kommandoen forsøker å tolke hva du ønsker å gjøre ut i fra dette navnet

Syntaksen for dette kan enten være AddXXXToYYY eller RemoveXXXFromYYY etterfulgt av feltene som skal legges til og hvilken felttype de skal ha

Det kan også være nyttig å huske på at alle id’er på modellene våre er av typen integer

La oss derfor forsøke å skrive følgende:

```
rails generate migration AddClientIdToProjects client_id:integer
```

Om vi åpner den nye migrasjonsfilen så ser vi hva vi har fått opprettet for oss

```ruby
class AddClientIdToProjects < ActiveRecord::Migration
  def change
    add_column :projects, :client_id, :integer
  end
end
```

Her legges det altså til en kolonne i tabellen “projects”

Denne kolonnen heter “client_id” og er av typen “integer”

Denne migrasjonen ser riktig ut, og vi kan da migrere opp vår database

```
rake db:migrate
```

For å også informere Rails om denne relasjonen, så må vi også legge til litt informasjon i modell-filene

Åpne derfor project.rb og legg til følgende helt øverst i klassen (husk entall i “client”):

```ruby
belongs_to :client
```

Åpne også client.rb og legg til følgende helt øverst i klassen (husk flertall i “projects”):

```ruby
has_many :projects
```

På denne måten er Rails informert om at feltet :client_id faktisk er en relasjon, og ikke bare et tall

Om vi nå går inn på http://localhost:3000/projects så kan vi forsøke å legge til et nytt prosjekt

Men, her har jo ikke noe forandret seg?

Det er fordi vi ikke har gjort noen endringer i våre View filer for å vise eller gjøre tilgjengelig denne nye egenskapen

Vi trenger derfor nå å legge til muligheten for å bruke og vise denne nye relasjonen

## Add relationships to views

Et logisk første skritt kan være å legge til muligheten for å legge inn en client_id i new og edit, eller i _form ettersom det er vår felles fil for dette

Først kan vi kopiere :url elementene og endre navn og type til :client_id og :number

Det gir oss muligheten til å skrive inn ID’en til en kunde manuelt

Ikke spesielt sexy, men vi kommer tilbake til dette litt senere

For å teste om det fungerer, så må vi finne ut hva som er ID’en til en av våre registrerte kunder, så gå til http://localhost:3000/clients og klikk på “edit” på en kunde

I URL’en kan vi nå se denne kundens ID, gå så tilbake til http://localhost:3000/projects og klikk “New Project”

Legg først inn ID’en til kunden, mens vi fremdeles husker den, og fyll så inn de andre feltene

Hvordan kan vi nå sjekke at det ble lagret?

La oss vise prosjektets client_id i view filen for “show”

La oss midlertidig legge det til etter leveransetidspunktet slik at det står “Delivered 15 days ago to :client_id”

Hmm, her burde det dukket opp noe

La oss sjekke i terminalen og se om vi finner noen feilmeldinger

Rett etter at vi forsøkte å opprette et prosjekt (GET “/projects/new” og så POST “/projects”) så ser vi en feilmelding:

```
“Unpermitted parameters: client_id”
```

Om vi husker tilbake til vår projects_controller så husker vi at vi spesifiserte hvilke parametere vi tillot å bli sendt inn fra nettleseren

Åpne projects_controller.rb og bla ned til metoden “project_params”

Legg så til :client_id i listen med symbolder til metoden “permit”

Med denne endringen på plass, så kan vi forsøke å redigere prosjektet og legge til client_id på nytt

Ah, nå ser det ut til å fungere igjen

Om vi nå besøker det første prosjektet i listen vår, så ligger det fremdeles uten noen :client_id

Vi ser derfor at det nå står “Delivered 20 days ago to”, men ikke noe tall

Vi kan nå gå inn på “Edit” og legge det til der, men la oss utforske et litt annet verktøy for å legge til denne relasjonen

## Rails Console

Rails kommer med en innebygget konsoll som ligner på Ruby sin IRB

Den store forskjellen er at Rails sin konsoll gir direkte tilgang inn i den Rails applikasjonen du jobber med

Det betyr at du kan gjøre søk og manipulasjon i eksisterende og kjørende modeller

La oss teste noen av tingene vi kan gjøre med denne konsollen

```
rails console
```

For å finne et prosjekt så kan vi be modellen Project om å søke med metoden “find” basert på prosjektets ID

Finn frem IDen til det første prosjektet du la inn, og søk opp dette i konsollen

```ruby
Project.find(1)
```

Det vi ser her er databasespørringen som kjøres, og deretter prosjekt-objektet som returneres fra databasen

Dette er prosjektet som vises øverst på http://localhost:3000/projects

Gå inn på selve prosjektet i nettleseren, og la oss teste er par småting

I konsollen har vi mulighet til å gjøre endringer direkte på prosjektet

Dette kan gjøres på en rekke forskjellige måter

Den ene måten er ved å følge samme oppskriften som vi har i projects_controller#update

Skriv derfor følgende i vår konsoll:

```ruby
project = Project.find(1)
```

Nå har vi det første prosjektet tilgjengelig i variabelen “project”

Denne står vi fritt til å gjøre endringer på

La oss først endre navnet

```ruby
project.name = “A whole new project name”
```

Refresh nå nettleseren

Hmm, det hjalp ikke stort

Hva annet gjorde vi i projects_controller#update ?

Det stemmer, vi lagret objektet ved å kjøre metoden “save”

```ruby
project.save
```

Det vi får tilbake nå er databasespørringen, og en tilbakemelding på om lagringen ble gjennomført (true || false)

Om vi nå refresher nettleseren så er vi at navnet på prosjektet har endret seg

Kult!

Vi kan også gjøre endringer på de andre egenskapene, og siden vi fremdeles har fårt “project” objekt, så kan vi jobbe videre med det

```ruby
project.client_id = 1

project.url = “trallala”

project.save
```

Denne gangen returneres “false”, og ingen av verdiene oppdateres i nettleseren

Hvorfor skjer dette?

I vår modell så satte vi noen validatorer, deriblandt en validering av feltet :url

Kriteriet var at dersom det skulle stå noe der, så måtte det se ut som en URL

Det er relativt greit å huske ettersom vi bare har forsøkt å endre to egenskaper, men om vi var litt usikre, så kan vi sjekke det i konsollen også

```ruby
project.errors
```

Eller, for å være mer lesbart

```ruby
project.errors.messages
```

Nå ser vi at feilen ligger på feltet :url, og at feilmeldingen er “is invalid”

Det er kjapt å fikse, enten ved å tømme feltet, eller ved å legge inn en faktisk URL

```ruby
project.url = “”

project.save
```

Slik, da har vi fått lagret prosjektet, både med den nye client_id’en, det nye navnet og uten noen URL

Ved å legge til en :client_id, så har vi opprettet relasjonen mellom prosjektet og kunden

Relasjonen tilsier at om vi ser på et prosjekt og ber om prosjektets kunde, så vil vi spørre i databasen etter kunden som har samme id som prosjektets client_id

Vi kan teste dette ved å skrive

```ruby
project.client
```

Rails finner nå frem Client objektet som “tilhører” Project objektet

Vi kan også utvide denne spørringen ved å be om detaljer om prosjektets kunde

```ruby
project.client.name
```

Dette kan bli nyttig

For å avslutte konsollen, så er det bare å skrive

```ruby
exit
```

## Add Client name to view

Det er fremdeles ikke spesielt spennende å vise kundens ID, så la oss bruke det vi nettopp lærte og heller vise navnet på kunden

Om vi åpner view filen for “show” så kan vi gå tilbake til der vi la til

```erb
<%= @project.client_id %>
```

Endre nå dette til

```erb
<%= @project.client.name %>
```

Ahh, det var mye bedre!

Vi kan til og med utvide dette til å faktisk kunne linke til kundens webside

```erb
<%= link_to @project.client.name, @project.client.website %>
```

Og for å unngå at besøkende forsvinner ut av siden vår helt, så kan vi åpne denne linken i et nytt vindu

```erb
<%= link_to @project.client.name, @project.client.website, target: :_blank %>
```

Nå er vi i business!

## Add exception handling to view

I listen over prosjekter så har du nå et par prosjekter som fremdeles ikke er knyttet til en kunde

Besøk nå en av disse, for eksempel nummer 2 på http://localhost:3000/projects

```
NoMethodError in Projects#show

undefined method ‘name’ for nil:NilClass
```

Denne feilen oppstår rundt linje #11 i show.html.erb

Spørsmålet er nå hva er det som årsaker feilen?

Metoden som ikke er definert er metoden “name”, så hvor er det vi forsøker å kalle på den?

```ruby
@project.client.name
```

Feilen informerer oss også om hvilket objekt vi forsøker å kalle metoden “name” på

```ruby
nil:NilClass
```

Men i koden så prøver vi å kalle “name” på “client”?

Det virker altså som om “client” fremstår som “nil” på denne siden

Grunnen til dette er at vi har forsøkt å be om kunden til et prosjekt som ikke har fått noen verdi på :client_id

Dette kan enkelt løses ved å legge inn en :client_id på prosjektet, men det kan også være greit å ha en fallback dersom et annet prosjekt også mangler :client_id

Vi kan derfor fikse dette relativt enkelt i view filen

Måten vi kan løse det på akkurat nå er ved å pakke inn henvisningen til kunden i en conditional statement

```erb
<% if !@project.client.nil? %>
  to <%= link_to @project.client.name, @project.client.website, target: :_blank %>
<% end %>
```

Da ser siden ut til å fungere selv uten å ha en kunde

I samme slengen kan vi også huske at det finnes en annen måte å skrive den conditional sjekken

Hver gang vi ønsker å sjekke noe med “if !something”, altså å sjekke om noe ikke er tilfelle, så kan vi også bruke den litt mer beskrivende “unless”

Erstatt derfor “if” sjekken med følgende

```erb
<% unless @project.client.nil? %>
```

Det funker fremdeles, men er nå litt mer lesbart og litt renere skrevet

## Improve Client Selection Form Element

For å gjøre dette litt mer brukervennlig så kan vi fremdeles forbedre hvordan du velger en kunde på et prosjekt.

Å huske en ID er ikke spesielt brukervennlig eller intuitivt

Vi kan derfor lage en dropdown hvor vi velger en kunde

For denne prosessen er det viktig å huske på hvordan modellene faktisk er teknisk koblet sammen

Relasjonen består av to ting

```ruby
:client_id

has_many :projects
belongs_to :client
```

Dette er fremdeles slik relasjonen skal se ut, men det legger også en del retningslinjer for hvordan vi kan forbedre brukergrensesnittet

For å gjøre de nødvendige endringene trenger vi å ha åpen følgende filer:

```
projects_controller.rb
projects/_form.html.erb
```

La oss starte i viewet

Når vi bygger et form så har Rails en rekke hjelpere som gjør dette enklere

En av disse hjelperene er midt i blinken for oss nå: “collection_select”

Syntaksen for denne metoden ser slik ut:

```ruby
collection_select object, method, collection, value_method, text_method, options = {}, html_options = {}
```

De to siste (options, html_options) trenger vi ikke å ta hensyn til, men de første fire trenger vi

“Method” henviser til hvilken egenskap vi ønsker å fylle med innhold, og i dette tilfellet er det egenskapen ID

```ruby
:client_id
```

“Collection” er en samling med verdier som vi kan velge blandt, altså alle kundene som er registrert

```ruby
Client.all
```

“Value method” tilsier hvilken metode på objektene i “collection” som skal brukes som verdien i egenskapen du ville fylle

Det vi trenger fra kundene er deres ID, ettersom det er det som er fremmednøkkelen i prosjekt tabellen

```ruby
:id
```

“Value text” beskriver hvilken metode på objektene i “collection” som skal være teksten i dropdown menyen

I dette tilfellet vil det da være kundenes navn, ettersom hensikten med øvelsen er å gjøre det mer brukervennlig enn å basere seg på kundens ID

```ruby
:name
```

Til slutt så trenger vi å koble form helperen “collection_select” til det skjemaet vi jobber med

Om vi ser på de øvrige feltene så kan vi se at alt kobles mot den lokale variabelen “f”, og vi skal også bruke denne her

Den endelige koden vil derfor kunne se slik ut:

```ruby
f.collection_select :client_id, Client.all, :id, :name
```

Forsøk nå å redigere en av dine kunder

Dette fremstår som litt mer brukervennlig

Når vi velger kunden og lagrer prosjektet, så blir disse koblet sammen gjennom prosjektets :client_id

Når funksjonen nå er på plass, så er det viktig å rydde i koden slik at riktige ting skjer på riktig sted

Det som står mest ut her er vår forespørsel om alle kundene for å fylle listen

```ruby
Client.all
```

Denne typen forespørsel om mer data fra databasen er noe som aldri bør forekomme i en view-fil

Vi trenger derfor å flytte dette til vår controller, og heller gjøre kundelisten tilgjengelig gjennom en instansvariabel

Gå derfor til din controller og legg inn følgende linje, både under #new, #create, #edit og #update

```ruby
@clients = Client.all
```

Vi trenger det alle fire stedene fordi vi må ha kundelisten både på nye og eksisterende kunder

Deretter kan vi erstatte “Client.all” i “_form” med “@clients”

Test nå at alt fremdeles fungerer

For å rydde opp enda litt til, så kan vi gjøre controlleren enda mer DRY

Øverst i controlleren har vi et pattern som vi kan benytte oss av igjen

```ruby
before_filter
```

Fjern derfor de fire tilfellene av “@clients = Client.all” igjen og lag følgende metode under “private” i bunnen av filen

```ruby
def set_clients
  @clients = Client.all
end
```

Deretter kan du be om å kjørt denne metoden før #new, #create, #edit og #update

```ruby
before_action :set_clients, only: [:new, :create, :edit, :update]
```

Vi kan nå gå gjennom våre prosjekter og redigere disse for å legge til kunderelasjonen
