Tema 17 - Økt 6 - Ruby Class / Inheritance Lekse
================================================

## Informasjon om oppgavesett og krav

- **Antall oppgaver:** 5
- **Antall svar påkrevd:** 3

**Skriv inn svar på oppgavene i en tekstfil med navn homework_03.txt og last opp denne på It's Learning**

Linjer som starter med > indikerer kommandoer som skal skrives inn i irb konsollen
(Ikke ta med hakeparantesen)

## 1.0 Class

1.1 Lag en Person-klasse basert på beskrivelsen nedenfor

Person-klassen skal ha følgende attributter:

- navn (String)
- kjønn (String)
- fødselsdato (Date)
- briller (Boolean)

Det skal være mulig å opprette et Person objekt ved å skrive følgende

    > new_person = Person.new "Ken Paulsen", "male"

1.2 Gjør attributten "briller" tilgjengelig fra utsiden, slik at man kan gi personen briller ved å skrive følgende

    > new_person.glasses = true

Det skal også være mulig å sjekke om personen har briller ved å skrive følgende

    > new_person.glasses

1.3 Gi Person-klassen en metode for å introdusere seg selv

Denne metoden skal kunne kalles på ved å skrive følgende

    > new_person.present_yourself

Person-objektet skal da printe ut følgende:

    "Hello, my name is Ken Paulsen, and I was born 2013-10-17 09:41:12 +0100"

*(Dato og klokkeslett skal matche tidspunktet da personen ble opprettet)*

## 2.0 Inheritance

2.1 Skriv en Toddler-klasse som arver fra Person-klassen

Du skal nå kunne opprette et nytt Toddler-objekt ved å skrive følgende:

    > new_toddler = Toddler.new "Olivier Klaver", "male"

2.2 Overskriv metoden "present_yourself" på Toddler-klassen slik at når du kjører følgende:

    > new_toddler.present_yourself

Så printer Toddler-objektet ut følgende:

    "Bllblblb lblbbl bllbl blb lblbblblb waaaaaaagghhh!!"