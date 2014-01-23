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

Som vi har sett tidligere, så er informasjonen fra feltene i forrige form tilgjengelig gjennom en spesiell parameter

For Client så het den "client_params"

Logisk nok så heter ikke Project sin "client_params", men "project_params"

Refresh feilmeldingen

(Bekreft at du ønsker å sende din form submission på nytt, dermed trenger du ikke å gå tilbake, fylle inn feltene og sende inn skjemaet på nytt)

Undefined local variable or method "project_params"

Hvor kom egentlig "client_params" fra sist gang?

Jo, det var en privat metode som sørget for å begrense hvilke felter vi fikk tilgang til fra våre Views

Vi må derfor lage en tilsvarende privat metode i vår projects_controller

Legg til følgende nedenfor den eksisterende koden (men før siste "end")

```ruby
private

  def project_params
    params.require(:project).permit(:name, :description, :delivered_at, :url)
  end
```

Forsøk nå å refreshe siden på nytt

Nå ser vi litt bedre hvilken informasjon som kommer fra skjemaet i forrige View

project_params returnerer altså en Hash som inneholder feltene i skjemaet, med navnet på feltet som Key og innholdet som Value

Ettersom navnet på feltene stemmer over ens med feltene i Project Modellen, så kan vi bruke dette for å bygge et nytt Project objekt

Erstatt din "throw" linje med følgende:

```ruby
@project = Project.new(project_params)

throw @project
```

Denne gangen fikk vi kastet ut et Project objekt

Objekter har fått verdiene fra skjemaet

Det er også noen tomme felter:

- id
- created_at
- updated_at

Dette er fordi disse feltene fylles av Rails og ActiveRecord når objektet lagres i databasen

Det er derfor neste sted i prosessen

Når vi skal lagre prosjektet, så må vi også ta hensyn til at noe kan gå galt

Erstatt derfor nå "throw" linjen med følgende kode:

```ruby
if @project.save
  redirect_to @project, notice: 'Project was successfuly created.'
else
  render action: 'new'
end
```

Ved å legge til dette, så kan vi nå få lagret et prosjekt i databasen

Dersom noe går galt, så kan vi sende brukeren tilbake til skjemaet

Dersom prosjektet lar seg lagre, så sender vi brukeren videre til visningen av prosjektet som ble lagret

Forsøk nå å refreshe siden igjen

Da fikk vi ikke feilmelding lenger

Nok en gang kom vi til en side som henviser oss til en View fil

Det er fordi vi nå har kommet videre til vår neste action: Show

## Show

Planen nå er å vise brukeren prosjektet som nettopp ble lagt inn

Filene vi skal jobbe med nå er projects_controller.rb og show.html.erb

Formålet her er å vise all informasjonen om prosjektet

Vi bruker ikke så mye tid på layout og design nå ettersom vi etterhvert skal legge til bilder her også

Øverst på siden så kan vi starte med å vise navnet på prosjektet

For å få tilgang til prosjektet så trenger vi å be vår Modell om å finne det fra databasen først

Ser vi noe som kan indikere hvilket prosjekt vi ser etter noe sted?

```
http://localhost:3000/projects/1
```

Det siste tallet refererer til ID'en til prosjektet

Om vi sjekker i terminalen med "rake routes" så finner vi det vi ser etter

```
/projects/:id(.:format) projects#show
```

Vi bruker derfor :id som ligger pakket inn i "params"

For å kunne bruke Project-objektet i vår View fil, så må den tilornes i en instans-variabel

```ruby
@project = Project.find(params[:id])
```

Dermed kan vi i vårt View skrive følgende:

```erb
<h1>
  <%= @project.name %>
</h1>
```

Om du får feilmeldingen "ActiveRecord::RecordNotFound in ProjectsController#show", så er det fordi det ikke finnes noe prosjekt med "id=1"

Du kan da enten teste med en annen id, eller legge inn et nytt prosjekt fra projects#index

Nå som @project er på plass, så kan vi presentere resten av informasjonen også

```erb
<p>
  Delivered at <%= @project.delivered_at %>
</p>

<p>
  <%= link_to @project.url %>
</p>

<p>
  <%= @project.description %>
</p>
```

La oss blande inn litt HTML5 semantikk her

Vår Ruby kode kan blandes inn hvor som helst i HTML koden, også i argumentene

```erb
<p>
  Delivered at <time datetime="<%= @project.delivered_at %>"><%= @project.delivered_at %></time>
</p>
```

Bedre semantisk sett, men mindre lesbart for våre besøkende

La oss utvide litt til

Rails har et par hendige metoder for å presentere informasjon, som tidsrom, på litt forskjellige måter

```erb
<p>
  Delivered
  <time datetime="<%= @project.delivered_at %>">
    <%= distance_of_time_in_words_to_now @project.delivered_at %>
  </time>
  ago
</p>
```

Da har vi fått presentert alt vi har av informasjon om ett prosjekt så langt

La oss til slutt legge til tre linker:

- rediger prosjektet
- slett prosjektet
- gå tilbake til listen over prosjekter

For enkelhets skyld så legger vi disse nederst på siden

Husk at vi alltid kan sjekke hvilke paths vi kan bruke med "rake routes"

Husk også hvordan vi ber Rails om å lage en link for å slette prosjektet

```erb
<%= link_to 'Edit', edit_project_path(@project) %> |
<%= link_to 'Destroy', @project, method: :delete, data: { confirm: 'Are you sure?' } %> |
<%= link_to 'Back', projects_path %>
```erb

Med disse tre linkene på plass så kan vi nå gå tilbake til oversikten over prosjektene

Her hadde vi ikke implementert så mye enda

Nå som vi har minst ett prosjekt, så kan vi gi projects#index et nytt forsøk

## Index

La oss gjøre dette så enkelt som mulig, slik at dere står fritt til å designe innholdet selv i etterkant

For å ha noe å bla gjennom så trenger vi en instanse-variabel

Ettersom den skal inneholde alle prosjektene, så kaller vi den @projects

Gå derfor til projects_controller#index og legg til følgende linje

```ruby
@projects = Project.all
```

Dermed kan vi starte å presentere prosjektene i View filen

Ruby sin måte å bla gjennom en liste med objekter er å ta objektet og kalle på metoden ".each"

```erb
<ul class="projects">
  <% @projects.each do |project| %>
    <li class="project">
      <%= project.name %>
    </li>
  <% end %>
</ul>
```

Da ser vi at det dukker opp prosjekter i listen

Legg nå inn et par prosjekter til slik at vi har litt mer å jobbe med

La oss nå utvide hvert prosjekt med litt mer informasjon

```erb
<li class="project">
  <%= link_to project.name, project %>
</li>
```

Vi kan nå klikke på prosjektet for å få se detaljene

Det bør kanskje også være mulig å redigere og slette prosjektet herfra

```erb
<li class="project">
  <%= link_to project.name, project %>
  (
  <%= link_to 'Edit', edit_project_path(project) %> |
  <%= link_to 'Destroy', project, method: :delete, data: { confirm: 'Are you sure?' } %>
  )
</li>
```

Da har vi en grunnleggende liste over prosjektene på plass

Men, jeg skulle gjerne gitt det første prosjektet et nytt navn, så jeg klikker på Edit

Nok en fil som ikke inneholder noe, jaja

Off to projects#edit!

## Edit

Når vi nå skal redigere et prosjekt så trenger vi to ting:

- å vite hvilket prosjekt
- en instanse-variabel

Gå derfor først til projects_controller og lag variabelen basert på prosjektets ID

Når vi nå skal lage vårt View så er det verdt å tenke tilbake til hva vi har gjort så langt

Som vi så på View filene til Client så var det en rekke fellestrekk mellom New og Edit

Fellestrekkene var faktisk så mange at de fikk lov til å bruke akkurat samme HTML form

Dette gjorde vi ved hjelp av en "partial"

La oss derfor gjøre det her også

Åpne både new.html.erb og edit.html.erb

Ettersom Rails bygger vår Form basert på @project objektet, så kan vi dele hele form_for på tvers av new og edit

Lag derfor en ny fil:

```
app/views/projects/_form.html.erb
```

Kopier så inn hele form_for blokken fra new.html.erb

I den nå tomme new.html.erb så kan du legge inn følgende

```erb
<h1>New Project</h1>

<%= render 'form' %>
```erb

Og i **edit.html.erb*** kan du legge inn det samme bare med en annen overskrift

```erb
<h1>Edit Project</h1>

<%= render 'form' %>
```

Ved å gjøre dette så deler disse samme Form

Om vi nå går til /projects og klikker på Edit linken så får vi riktig skjema

Forsøk nå å endre navnet på et prosjekt og lagre dette

"Template is missing"

"Missing template projects/update"

Stemmer, vi har ikke skrevet logikken for å sende de nye verdiene til Modellen

Tilbake til **projects_controller.rb** for å fikse får update metode

## Update

Om vi tar en kikk på hva vi gjør i vår Create metode, så skal vi gjøre noe lignende i Update også

Den eneste forskjellen er at vi nå skal finne et eksisterende prosjekt, heller enn å opprette et nytt

start derfor metoden med følgende:

```ruby
@project = Project.find(params[:id])
```

Vi skal nå gjøre tre ting samtidig:

- oppdatere verdiene i objektet
- be om å få disse lagret i databasen
- sende brukeren til riktig sted avhengig av om lagringen går bra

Legg derfor til følgende:

```ruby
if @project.update(project_params)
  redirect_to @project, notice: 'Project was successfully updated.'
else
  render action: 'edit'
end
```

Vi kan nå redigere prosjekter og få disse lagret med oppdatert informasjon i databasen

Hva med den siste gjenstående metoden?

Forsøk å slette et prosjekt

"Template is missing"

Da får vi vel skrive den metoden også

## Destroy

Denne metoden er relativt rett frem

Ettersom vi allerede har bedt brukeren bekrefte at prosjektet skal slettes, så kan vi bare slette det

```ruby
@project = Project.find(params[:id])

@project.destroy

redirect_to projects_url
```

Da har vi på plass det grunnleggende for å behandle prosjektene

Det eneste som gjenstår er å bla gjennom koden og se om vi kan optimalisere

Denne prosessen kalles ofte "refactoring"

## Refactor

Vi har skrevet relativt enkel kode så langt, så det er ikke veldig mye som skal refactores

Om vi ser i projects_controller så kan vi likevel se at det er en ting som gjentas flere ganger

```ruby
@project = Project.find(params[:id])
```

Denne finnes både i show, edit, update og destroy

La oss derfor separere det ut til en egen metode

Ettersom denne metoden ikke trenger å være tilgjengelig fom omverdenen, så gjør vi den privat

```ruby
private
  
  def set_project
    @project = Project.find(params[:id])
  end
```

Deretter kan vi fjerne dette fra disse fire metodene

Og til slutt be om å få denne private metoden kjørt automatisk før hvert kall til disse metodene

Skriv da øverst i projects_controller følgende:

```ruby
before_action :set_project, only: [:show, :edit, :update, :destroy]
```

Vi har nå et par metoder som ikke inneholder noe kode

Dette skjer fordi de kun hadde som ansvar å finne frem et prosjekt slik at det kunne presenteres i View'et

Dette gjelder både Show og Edit

Det er da vanlig i Rails å forkorte disse metodene til såkalte "one-liners"

Resultatet blir da som følger:

```ruby
def show; end

def edit; end
```ruby

Dermed har vi hatt en liten refactoring helt på slutten

Da gjenstår det bare å sørge for å lage en commit
