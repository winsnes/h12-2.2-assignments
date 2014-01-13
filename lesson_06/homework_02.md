Tema 17 - Økt 6 - Ruby Lekse
============================

## Informasjon om oppgavesett og krav

- **Antall oppgaver:** 22
- **Antall svar påkrevd:** 17

**Skriv inn svar på oppgavene i en tekstfil med navn homework_02.txt og last opp denne på It's Learning**

Linjer som starter med > indikerer kommandoer som skal skrives inn i irb konsollen
(Ikke ta med hakeparantesen)

## 1.0 Syntaks

*Kodelinejene i oppgavene nedenfor er skrevet med JavaScript. Skriv dem om til det tilsvarende skrevet med Ruby.*

1.1 Skriv om til Ruby

    console.log( "This is a little text" );

1.2 Skriv om til Ruby

    if ( variable_1 > variable_2 ) {
        console.log( "The first variable is bigger than the second variable!" );
    } else if ( variable_1 < variable_2) {
        console.log( "The second variable is bigger than the first variable!" );
    } else {
        console.log( "The two variables are equally big" );
    }

## 2.0 Datatyper

### 2.1 Boolean

2.1.1 Hva blir resultatet av denne sammenligningen?

    > 350.00 == 350

2.1.2 Hva blir resultatet av følgende?

    > (350 == "350").class

2.1.3 Hva blir da resultatet av følgende?

    > (!(350 == "350")).class

### 2.2 Number

2.2.1 Endre følgende linje slik at resultatet blir Float

    > (250 + 50012).class

2.2.2 Hva er forskjellen på Fixnum og Bignum?

2.2.3 Skriv regnestykket som gir den største Fixnum verdien

2.2.4 Skriv regnestykket som gir den minste Bignum verdien

2.2.5 Sjekk klassen til resultatet fra de to forrige regnestykkene. Hva skjer her?

### 2.3 String

2.3.1 Fiks problemet med følgende String

    > my_string = 'some random letters'
    > 'This is just #{my_string} written out in irb.'

2.3.2 Skriv ut følgende String slik at det kun er første bokstaven som er stor

    > some_string = "this is just A REALLY NOISY STRING"

### 2.4 Array

Array-oppgavene tar alle utgangspunkt i følgende Array

    > my_array = [ 5, 9, 12, 36, "dog", "cat", false ]

2.4.1 Skriv kommandoen for at du skal få skrevet ut "dog"

2.4.2 Skriv kommandoen for at du skal få skrevet ut den nest siste verdien i listen

2.4.3 Legg til tallet 7928.125 bakert i listen

2.4.4 Fjern den første verdien fra listen

### 2.5 Hash

Hash-oppgavene tar alle utgangspunkt i følgende Hash

    > my_hash = {
        turtles:  12,
        giraffes: 7,
        baboons:  3
    }

2.5.1 Skriv ut kun nøklene fra listen

2.5.2 Skriv ut kun verdiene fra listen

2.5.3 Skriv ut verdien til nøkkelen :giraffes

## 3.0 Funksjoner

3.1 Skriv en funksjon som tar i mot tre tall og legger dem sammen

3.2 Hva skjer når du kjører følgende kode? Hvorfor?

    > def silly_function( a, b, c, d ) {
        sum = a + b + c + d
        product = a * b * c * d
        something_else = 7
    }

    > silly_function( 150, 9, 123, 65 )

3.3 Skriv en funksjon som tar i mot fem tall, legger sammen de to første, og returnerer det siste
