Tema 17 - Økt 11 - Rails Generate Scaffold Workshop
===================================================

Naviger til din USB stick

    cd /Volumes

    ls

    cd /Voyager\ Flash

Opprett Rails applikasjonen "My Portfolio"

    rails new my_portfolio

Bla inn i mappen

    cd my_portfolio

Sjekk at alt er klart ved å starte serveren
Dette kan gjøres enten med "rails server" eller den forkortede versjonen "rails s"

    rails s

Besøk så serverens startside ( http://localhost:3000 )

Her ser alt ut til å være i orden, så da kan vi avslutte serveren igjen (Ctrl-C)

Vi kan nå bruke vår gode venn "rails generate scaffold" for å komme i gang

Husk hvilke felter vi ønsket å lagre om kundene våre:

- Navn
- Webside

Det er også verdt å nevne nå at "rails generate" kan forkortes på samme måte som "rails server"
Kommandoen blir da "rails g"

Vi kan da opprette en scaffold for vår kundebehandling

    rails g scaffold Client name website

Et par ting å merke seg med denne kommandoen:

- Modellens navn skrives med stor forbokstav og i entall
- Både "navn" og ""website" skal lagres som tekst-strenger, og vi trenger derfor ikke deklarere noen verditype

Kjør nå kommandoen

Resultatet av kommandoen ligner på vår kjøring av "rails new"
Listen over endringer inneholder en del nøkkelord:

- invoke
- create
- route

Invoke betyr at en oppskrifts-modul blir iverksatt
Vi skal ikke bruke tid på den enkelte modulen nå

Create betyr, som forrige gang, at filer eller mappe opprettes

Route forteller hvordan serveren skal tolke innkommende forespørsler
Routing beskriver våre modeller som ressurser som kan benyttes av vår applikasjon
Vi kommer tilbake til detaljene her litt senere

Vi kan nå se nærmere på hvilke filer som ble opprettet for oss

Åpne Sublime Text 2 i prosjektmappen vår, og ha terminalvinduet ved siden av

    subl .

La oss se på filene som ble opprettet

## Migrate

Den første filen ligger under db/migrate/
Filnavnet består av en tallrekke etterfulgt av _create_clients.rb
Tallet er et tidsstempel, og det brukes for å fortelle applikasjonen hvilken rekkefølge migrasjons-filene skal kjøres i

Strukturen i filen forteller oss et par ting
Ytterst finner vi følgende

```ruby
class CreateClients < ActiveRecord::Migration
end
```

Det er altså en fil som definerer en klasse "CreateClients"
Og denne klassen arver fra "ActiveRecord::Migration"
De to "::" tegnene indikererer at "Migration" er en modul under "ActiveRecord"

Deretter har vi denne strukturen

```ruby
def change
end
```

Change er en metode som forteller hva som skal gjøres med databasen når vi "migrerer" inn disse endringene

Inne i Change ser vi et kall til metoden "create_table"
"create_table" tar i mot navnet på tabellen som et symbol (":clients") i flertall
Deretter, inne i en blokk, beskrives hvilke attributter vi trenger å lagre i databasen, sammen med hvilken verditype det gjelder

```ruby
t.string :name
t.string :website
```

Til slutt har vi denne

```ruby
t.timestamps
```

Dette er en standard tilordning som tar seg av å gi noen grunnleggende felter på hvert innlegg i tabellen
Vi kommer tilbake til hvilke felter dette er litt senere

## Model

Den neste filen ligger i app/models/ og heter client.rb

Denne filen inneholder ikke veldig mye
Det er fordi mesteparten av funksjonaliteten er lik på tvers av forskjellige modeller
Det arves derfor fra "ActiveRecord::Base"

De neste to filene er filer som gjør det klart for å skrive tester av koden vår
Vi vil komme tilbake til tester mot slutten av temaet, men vi hopper over det for nå

## Routes

Deretter har vi en deklarasjon av kunder som ressurser i vår applikasjon

Endringen som skjer her er synlig i filen config/routes.rb

Denne filen inneholder en rekke utkommentert kode som kan være nyttig å beholde inntil videre
Den linjen vi skal se på nå ligger nest øverst

```ruby
resources :clients
```

Dette definerer at kundene vi oppretter i applikasjonen er ressurser som kan behandles i applikasjonen, enten av oss eller av andre deler av systemet

Det kan også her defineres hvilke handlinger som kan være tilgjengelige på denne ressursen
Dette gjenspeiles i controlleren

## Controller

Videre ser vi at det opprettes en controller
Denne ligger i mappen app/controllers/ og heter clients_controller.rb

Denne filen beskriver hvilke handlinger vi kan utføre på vår Client ressurs, og hvordan disse fungerer

Om vi bryter ned filen så finner vi følgende

```ruby
class ClientsController < ApplicationController
end
```

Altså arves en del funksjonalitet fra en annen controller
Kanskje vi kan se nærmere på den også?

```ruby
class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
end
```

Dette er controlleren som alle våre fremtidige controllere vil arve fra
protect_from_forgery er en samling funksjonalitet som beskytter applikasjonen mot CSRF (Cross-site request forgery)
CSRF består i at nettleseren sender forespørsler til en webside, på vegne av en bruker som allerede er autentisert (logget inn)
Det kan blandt annet manifestere seg i at brukeren klikker på linker til en side de tror de kan stole på, men som innebærer at andre handlinger utløses enn de brukeren trodde eller var klar over
Dersom brukeren er administrator kan dette fort bli katastrofalt

Rails motarbeider denne typen angrep ved å bruke en skjult autentiserings-token
Det er mulig å deaktivere dette, men det absolutt ikke å anbefale

Men, tilbake til vår ClientsController

Inne i denne applikasjonen så er det deklarert en rekke metoder:

- index
  - List opp alle kundene
- show
  - Vis én kunde
- new
  - Legg inn en ny kunde
- edit
  - Rediger en eksisterende kunde
- create
  - Funksjonaliteten som faktisk tar i mot data om ny kunde og lagrer denne
- update
  - Funksjonaliteten som faktisk tar i mot oppdatert data om en kunde og lagrer denne
- destroy
  - Sletter en kunde fra databasen

Nederst har vi også to metoder som skiller seg ut:

- set_client
- client_params

De står begge under teksten "private"
Om dere husker tilbake til OOP tankegang, så er private metoder kun tilgjengelig fra innsiden av en klasse

Funksjonaliteten i disse kommer vi tilbake til i detalj senere, men i korte trekk har de å gjøre med DRY og tilgangskontroll

## View

Videre på listen finner vi en rekke filer som legger seg i app/views/clients/
Først opprettes mappen, og deretter de enkelte filene

Det er verdt å merke seg hvilken endelse disse filene har (".html.erb")
Dette indikerer at det er en HTML fil som har "embedded ruby"
Altså skal den tolkes av serveren som å inneholde både HTML- og Ruby-kode

Filene er som følger:

- app/views/clients/index.html.erb
- app/views/clients/edit.html.erb
- app/views/clients/show.html.erb
- app/views/clients/new.html.erb
- app/views/clients/_form.html.erb

Dette er filene som utgjør hva vi som besøkende ser når vi besøker siden med vår nettleser
Det kan være verdt å merke seg sammenhengen mellom metodene i vår controller og navnene på disse filene

Den siste filen har et filnavn som starter med underscore
Det indikerer at det er en såkalt "partial"

Det vil si at den innholder kun et lite bruddstykke med kode som skal vises i flere andre filer

For å få noe nytte ut av å se på disse filene så skal vi først teste koden vi har fått generert
Husk nå at vi kun har skrevet inn én kommando:

    rails generate scaffold Client name website

Start derfor opp serveren

    rails s

Og besøk applikasjonen

    http://localhost:3000

Dette var jo nyttig, her får vi en relativt klar beskjed om hva som mangler

Hver gang vi legger til en ny modell og dermed har en ny migration-fil så krever Rails at vi faktisk kjører denne migreringen
Altså at vi oppgraderer databasen til nyeste versjon

Dette gjøres ved å kalle på en av Rails sine støttespillere, Rake

Vi skal ikke bruke noe mer tid på hvordan det fungerer akkurat nå, så la oss bare kjøre kommandoen vi blir bedt om å kjøre
(Vi kan utelate "RAILS_ENV=development")

    rake db:migrate

Besøk nå siden igjen (Du trenger ikke restarte web-serveren med mindre du kjørte migreringen i samme terminavindu)

Tadaa!

Jaha.. her var vi jo virkelig i sted...?

Om vi tar en alternativ kikk på vår routes.rb fil så får vi kanskje et hint

La oss ta en tur til terminalen og se på en annen bruk av Rake-kommandoen

    rake routes

Dette er en måte å få innblikk i hvilke routes som finnes for vår applikasjon

Vi ønsker nå å komme til oversikten over våre kunder, altså vår "index" action
Om vi følger høyre kolonne ("Controller#Action") så ser vi høyt oppe "clients#index"
Rett flr dette står følgende "URI Pattern": "/clients(.:format)"

La oss modifisere adressen vi besøker

Først besøkte vi http://localhost:3000
La oss nå legge til URI Pattern'et vi fant med "rake routes"

    http://localhost:3000/clients

TADAAA!

Nå begynner det å ligne på noe ja
Nå kan vi se hvilken funksjonalitet vi har så langt

Husk igjen på at vi kun har skrevet to kommandoer i terminalen (rails g scaffold, rake db:migrate) i tillegg til å starte serveren

### Test scaffold funksjonalitet

La oss legge inn noen kunder

    "New Client"
    Name: "Norges Kreative Høyskole"
    Website: "http://nkh.no"
    "Create Client"

Vi får nå en bekreftelse på at det er lagt inn en ny kunde

    "Client was successfully created."

Følg "Back"-linken på siden
Nå har det dukket opp en kunde i listen

La oss legge inn en kunde til

    "New Client"
    Name: "PaulsenNet"
    Website: "http://paulsennet.no"
    "Create Client"

Nok en gang får vi bekreftet at kunden er lagt til
Kanskje vi ser en feil som vi vil korrigere med en gang?
Følg denne gangen "Edit"-linken

Fiks det som skal fikses

    "Update Client"

Følg igjen "Back"-linken
Nå ligger det to kunder i listen

Den siste kunden var ikke så interessant likevel, så vi kan slette den
Følg denne gangen "Destroy"-linken
Her blir vi bedt om å bekrefte før kunden slettes, det er jo bra

Vi har nå, bare ved å kjøre to kommandoer i terminalen, fått en grunnleggende CRUD funksjonalitet
I sin aller enkleste form har vi faktisk på plass det vi trenger av kundebehandling

Men hvordan?
Vi skal nå grave litt i koden som gjorde mulig det vi nettopp brukte

### CRUD

Åpne clients_controller.rb ved siden av, så kan vi holde et øye med den i samme slengen

#### Index

Siden vi nå er inne på er definert i metoden "index"

Åpne også app/clients/index.html.erb

Dette er view filen som beskriver hvordan HTML koden til denne siden skal se ut
Filen heter .html.erb for å indikere at den inneholder "embedded ruby" som skal tolkes før det skal "printes ut" som HTML

HTML taggene i filen bør være relativt lett å tolke
Iblandet HTML koden har vi to nye "tagger"

```erb
<%  %>
<%= %>
```

Disse to indikerer at teksten mellom skal tolkes som Ruby-kode

Forskjellen mellom dem ligger i hvorvidt resultatet av koden skal printes ut i HTML dokumentet
"Taggen" med "=" vil vises i HTML dokumentet, og "taggen" uten indikerer kun at det skal gjøres en beregning som ikke er synlig for de besøkende

Midt i filen finner vi hoveddelen av innholdet; Å bla gjennom kundene, og vise nødvendig informasjon

Først kan vi legge merke til instance-variabelen @clients
Defineringen av denne finner vi i vår controller under metoden "index"

```ruby
@clients = Client.all
```

Dette er vår måte å si at @clients skal inneholde alle kundene som ligger i databasen
Vi får disse tilbake ved å be vår modell (Client) om å finne alle sine registrerte kunder (.all)

Alle instanse-variabler som ligger i controlleren under samme metode blir tilgjengelige i det tilhørende view'et
For å teste det så kan vi legge til en ekstra instansevariabel

Gå in i clients#index og legg til følgende linje

```ruby
@my_variable = Time.now
```

Om vi nå refresher siden så skjer det ikke stort
Det er fordi vi så langt bare har laget variabelen, men vi har ikke brukt den til noe

Vi kan nå åpne vår app/views/clients/index.html.erb og legge til følgende linje rett under overskriften

```erb
<p>
  <%= @my_variable %>
</p>
```

Når vi nå refresher siden så ser vi at klokkeslettet hentes inn hver gang

Det var ikke så nyttig eller hensiktsmessig, så la oss slette disse linjene
(Sletter nå begge instansevariablene fra controlleren, i tillegg til paragrafen i view)

Oisann?
Hva skjedde nå?

Her har vi en henvisning til hva slags type error som har skjedd (NoMethodError) og hvor feilen har skjedd (Clients#index)
Vi får vite hvilken fil det gjelder (app/views/clients/index.html.erb) og hvilken linje (#15)
En mer utfyllende beskrivelse av feilen (undefined method 'each' for nil:NilClass) og et utsnitt av koden hvor feilen oppstår
I tillegg får vi se hvilken mappe som er roten til prosjektet dette gjelder

Med denne informasjonen så kan vi begynne å lete etter hva som har skjedd

Metoden "each" blir forsøkt kjørt, men objektet den kjøres på har ikke den metoden definert
Objektet vi prøver å kjøre "each" på i koden er @clients
Hvor var @clients definert igjen?

Om vi ser i controlleren, så ser vi at jeg har slettet en linje for mye med kode
Er det noen som husker hvilken linje det var, og hva det sto der?

Variabelen vi mangler er @clients
Og den skulle inneholde alle kundene
Vi fikk alle kundene ved å be modellen Client om .all
Resultatet skulle da bli omtrent slik

```ruby
@clients = Client.all
```

Da var vi tilbake på riktig spor

#### Show

La oss går inn på visningen av én kunde

Informasjonen for denne visningen er fordelt over to steder:
- show metoden i clients_controller.rb
- app/views/clients/show.html.erb

I view'et ser vi at informasjon hentes fra instance-variabelen @client
Det burde derfor være en linje i controlleren hvor denne instance-variabelen fylles

Men når vi ser i show metoden til controlleren så finner vi ikke noen @client

Øverst i controlleren så har vi en linje som hjelper oss å holde koden vår DRY

```ruby
before_action :set_client
```

Denne første delen indikerer at det skal kjøres én metode (set_client) før vi kjører innholdet i våre andre metoder

Videre beskrives det hvilke metoder som ber om få kjørt set_client før seg selv

```ruby
only: [:show, :edit, :update, :destroy]
```

Felles for disse fire metodene er at de refererer til étt objekt
F.eks. index, som ikke er med i denne listen, skal ikke ha @client men @clients (flertall) ettersom alle kundene skal listes opp

Men hvor er denne metoden?

Om vi ser nederst i controlleren så ser vi en seksjon med overskriften "private"
Ved å tenke tilbake til OOP programmering så vet vi at en metode som er "private" kun er tilgjengelig fra innsiden av controlleren

Her inne setter vi @client variabelen som da blir tilgjengelig inne i de metodene som ble listet opp i toppen
Og hvordan blir denne satt?
Vi ber modellen Client om å finne én kunde basert på en eller annen id som ligger i en liste som heter "params"

Om vi nok en gang kjører "rake routes" så kan vi begynne å se hvor denne params[:id] kommer fra

    rake routes

Foran "clients#show" så ser vi følgende URI Pattern:

    "/clients/:id(.:format)"

Om vi ser i adressefeltet når vi følger "Show" på vår kunde så ser vi at det står noe slikt:

    "http://localhost:3000/clients/1"

Det siste tallet der er altså kundens ID, og det er det som sendes til "find" metoden

```ruby
@client = Client.find(params[:id])
```

"params" inneholder en hel del annen informasjon også, men la oss holde det enkelt for nå

Når vi nå har styring på hvor variabelen kommer fra, så kan vi styre hva som skal skrives ut

Når vi så i migrasjons-filen så hadde vi den siste linjen som vi ikke brukte så mye tid på

```ruby
t.timestamps
```

En av de attributtene som hvert objekt får herfra er attributten "created_at"

Kanskje den er interessant å vise på hver kunde?

La oss vise dette når vi ser på én enkelt kunde

```erb
<p>
  <strong>Created At:</strong>
  <%= @client.created_at %>
</p>
```

Men hva annet finnes i @client objektet?

erstatt forrige kode med dette:

```erb
<% throw @client %>
```

Når vi nå refresher siden så får vi opp en feilmelding med blandt annet hele objektet @client

Her kan vi se at hver Client har følgende informasjon:

- id
- name
- website
- created_at
- updated_at

Attributtene created_at og updated_at kommer fra t.timestamps i migrasjonen
Attributten id kommer fra Rails og er satt til å automatisk stige for hvert objekt

Fjern nå det vi la til i filen slik at den er tilbake til sin originale form

#### New / Create

For å opprette et nytt objekt så trenger vi to metoder i controlleren:

- new
- create

New henviser til siden hvor HTML form-elementene vises
Create henviser til adressen som mottar informasjonen fra New sine form-elementer og oppretter det faktiske objektet

La oss starte med view filen for clients#new

    app/views/clients/new.html.erb

Her var det ikke stort

Det er fordi både clients#new og clients#edit deler samme form-elementene
Når du skal opprette en kunde så har du de samme feltene som når du oppdaterer den

Dette vises ved at vi ser følgende:

```erb
<%= render 'form' %>
```

Dette henviser til en "partial"
Partials har filnavn som starter med en underscore ("_")

Vi kan derfor også åpne app/views/clients/_form.html.erb

Her inne finner vi selve form-strukturen

Her er det en hel del Ruby kode, og relativt lite ren HTML
Det er fordi vi overlater til Rails å bygge store deler av vårt HTML form

Funksjonaliteten vi bruker til dette kalles en "form builder"

Vi kan ignorere linjene 2-12 for nå og fokusere på hovedstrukturen

Ved å midlertidig fjerne disse linjene så står vi igjen med 13 korte linjer, og en litt jevnere fordeling av Ruby- og HTML-kode

Først kan vi observere at vi jobber med en blokk med kode

```erb
<%= form_for(@client) do |f| %>
<% end %>
```

Dette er koden som bygger selve form elementet med sin action og method attributt

Inne i denne blokken bygger vi enkelt opp label og text_field for hver verdi vi skal kunne redigere

```erb
<%= f.label :name %>
<%= f.text_field :name %>
```

Og til slutt en knapp for å sende inn det utfylte skjemaet

```erb
<%= f.submit %>
```

Det er verdt å merke seg at vi ikke har noen felter for å endre ID'en til objektet
Dette overlater vi helt til Rails

Grunnen til at vi kan ha samme blokken med HTML form for både new og edit er at Rails vet forskjell på et nytt objekt og et eksisterende
Avhengig av om objektet eksisterer eller ikke, så sender HTML form'en nettleseren videre til enten clients#create eller clients#update i controlleren

Denne gangen skal vi til clients#create

Her inne ser vi at det opprettes et nytt objekt av klassen Client

```ruby
@client = Client.new(client_params)
```

Metoden .new tar i mot ett argument
Men hvor kommer dette argumentet fra?

Vi så tidligere at vi hadde en params[:id] som tok ID'en til et objekt fra URL'en

Om vi ser i bunnen av filen, så finner vi en privat metode som heter client_params

```ruby
def client_params
  params.require(:client).permit(:name, :website)
end
```

Dette er en metode som begrenser hvilke attributter vi har mulighet for å sende med i et form
Vi ønsket bl.a. ikke å kunne oppdatere objektets ID, så det er ikke med her
Både created_at og updated_at vedlikeholdes også av Rails, så det er ikke relevant å ha med

Dermed har vi igjen de to verdiene som vi finner felter for i vår HTML form

Koden videre kan være noe mer kompleks, men gir i utgangspunktet anledning til å besvare forespørsler både som HTML og JSON
Disse er standardformatene i Rails 4.0

Ellers består funksjonaliteten i hovedsak i at Rails forsøker å kjøre @client objektet sin .save metode
Dersom det fungerer, så sendes brukeren tilbake til siden til @client med beskjeden "Client was successfully created."
Om kunden ikke lar seg lagre av en eller annen grunn, så sendes brukeren tilbake til clients#new og får en ny anledning til å taste inn informasjonen

JSON informasjonen kan vi ignorere inntil videre

Før vi går videre, la oss hente tilbake koden vi fjernet fra toppen av clients#new view-filen

I korte trekk er dette en kodeblokk for å bla gjennom eventuelle feilmeldinger brukeren har fått ved forsøk på å opprette objektet, og vise dem på siden

#### Update

Update fungerer veldig likt som clients#new med ett unntak

```ruby
before_action :set_client, only: [:show, :edit, :update, :destroy]
```

Dermed har vi et @client objekt som kan vises (i HTML form feltene) og oppdateres (i clients#update)

#### Destroy

Til slutt har vi metoden for å slette en kunde

Vi har så langt hoppet glatt over et par linjer med kode i forskjellige views, så la oss addressere det først

Linker på clients#index:

- New Client
- Show
- Edit
- Destroy

Linker på clients#show:

- Back

Rails bruker i de fleste tilfeller såkalte "view helper" metoder for å bygge opp forskjellige HTML elementer

Den vanligste helper-metoden vi har sett så langt er "link_to"
Det er rett og slett en metode for å bygge en HTML link

```ruby
link_to 'Show', client
```

Metoden tar her i mot to argumenter:

- Teksten på linken
- hvor linken skal peke

Ettersom Client er deklarert som en ressurs på roten av routes så kan den pekes til ved kun å peke til objektet

```ruby
link_to 'Edit', edit_client_path(client)
```

Her er informasjon om hvor linken skal peke mer utbygget

Jukselappen her ligger et sted vi har vært før

    rake routes

Helt til høyre seg vi kolonnen Prefix
Det er hvordan vi kan beskrive veien til den controller-action'en vi ønsker
Derfor, når vi skal til clients#edit, så finner vi "edit_client" til venstre

I tillegg til Prefix kolonnen så ser vi i URI Pattern følgende: "/clients/:id/edit(.:format)"
Ettersom :id står i adressen, så er det også noe Rails trenger for å bygge riktig link
Rails er også smart nok til at vi bare kan sende selve objektet, så lenge vi informerer om at vi ønsker å bygge en "path"

Resultatet blir derfor følgende:

```ruby
edit_client_path(client)
```

Dette gjelder også når vi skal lage en ny kunde nederst op clients#index

```ruby
link_to 'New Client', new_client_path
```

Ved å sjekke "rake routes" så ser vi at det ikke er nødvendig å sende med noen id, og vi kan derfor bare kalle på "new_client_path"

Men, tilbake til den metoden vi skulle se nærmere på

Destroy

Når vi ser på linken til clients#destroy, så er den ganske anderledes

```ruby
link_to 'Destroy', client, method: :delete, data: { confirm: 'Are you sure?' }
```

Om vi fjerner alt etter de to første argumentene, så er det i utgangspunktet bare en link for å vise en kunde

Ettersom HTTP ikke har en skikkelig definert DELETE metode, så "faker" Rails det ved å inkludere litt JavaScript
Egentlig inkluderer dette at Rails legger til en data-attributt å informere om den ønskede HTTP metoden, og sender deretter forespørselen som en POST

Det er dette "method: :delete" gjør

Til slutt legger vi til en data-attributt som JavaScript bruker for å utløse en bekreftelse fra brukeren

Rails legger også til en "rel='nofollow'" på linker med "method: :delete" for å unngå at roboter skal slette innholdet ditt når siden crawles

## Recap

Som vi har sett så gjør "rails generate scaffold" ganske mye for oss
Men det oppretter også en del ekstra kode som vi egentlig ikke trenger
Vi har heller ikke berørt en del av den allerede eksisterende koden som omfavner det "rails generate scaffold" laget for oss

Dette skal vi se på neste gang
