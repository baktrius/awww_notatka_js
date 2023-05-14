# Javascript notatka
JS powstał w latach 90 jako rozszerzenie do przeglądarki internetowej firmy Netscape umożliwiające tworzenie interaktywnych stron internetowych. Przez następne lata, aż po teraz wykorzystuje się go głównie właśnie w tym zastosowaniu.

Niemniej warto według mnie patrzeć na niego nie jako integralną część przeglądarek, a raczej zobaczyć w nim dwie rozłączne części:
1) język ogólnego zastosowania wysokiego poziomu
2) api przeglądarkowe umożliwiające przeglądanie i manipulację stroną internetową opisaną html'em i css'em, a także obsługiwanie akcji użytkownika

## Część I - język wysokiego poziomu
Javascript (oficjalna nazwa kolejnych standardów to ECMAscript coś) jest pełnoprawnym językiem wysokiego poziomu ogólnego przeznaczenia. W składni jest nieco podobny do języka c, niemniej biorąc go pod lupę i patrząc na szczegóły jest znacząco inny. Ciężko wymienić po kolei wszystkie różnice, bo jest ich bardzo dużo (celem zgłębienia niuansów i meandrów semantyki i składni js'a  polecam przejrzenie porządnego internetowego tutorial'a np. [tego](https://javascript.info/first-steps)). W tym momencie wystarczy wiedzieć, że js jest jak c, ale:
- jest dynamicznie typowany (jak python). Zmienne nie mają z góry przypisanych typów i mogą się one zmieniać, a także są determinowane w czasie typowania,
- istnieje namiastka klas i dziedziczenia, ale zrealizowana inaczej niż w popularnych językach kompilowanych (zastosowany mechanizm to prototypowanie, ale to teraz mało ważne),
- wykonanie js'a jest oparte o system event'ów i callback'ów w przeciwieństwie do znanej z innych języków funkcji `main`. Odpowiada to dobrze jego głównego zastosowaniu jako języka skryptowego do obsługi stron internetowych. Zrozumienie dobrze tego systemu jest kluczowe do pomyślnej pracy z tym językiem.

## Część II - api przeglądarkowe

API przeglądarkowe rozszerza możliwości js'a jako ogólnego języka programowania o interakcję ze stroną internetową. Dostęp do dodatkowej funkcjonalności jest zapewniony za pomocą dostępnego wszędzie obiektu `window` (może wydawać się, że globalnych obiektów i funkcji jest więcej, ale tak na prawdę następuje po prostu automatyczne 'prefiksowanie' innych zmiennych nie znalezionych w środowisku i np. `document` jest równe `window.document`).

Js'owe api przeglądarkowe pozwala między innymi na:
- manipulacje DOM (drzewem dokumentu - strukturą html'a)
  * adresowanie istniejących elementów
  * modyfikację własności czy wyglądu elementów
  * podpinanie obsługi zdarzeń pod elementy
- interakcję z serwerem przez AJAX'sy

### Dołączanie js'a do strony
Jako że to js jest dodatkiem do strony html'owej, a nie na odwrót, to na samym początku należy jakoś przeglądarce podpowiedzieć aby dodała do strony konkretny skrypt.

Robi się to za pomocą tagu `<script>`. Skrypt js'owy można napisać w środku tagu, ale lepiej dołączyć zewnętrzny plik (przez specyfikację relatywnej ścieżki na serwerze w atrybucie `src`). W ten sposób zyskujemy nie tylko lepszą separację kodu, ale często również lepsze wskazówki od naszego środowiska programistycznego.

Uwaga na boku jest taka, że miejsce dołączenia skryptu w kodzie html ma znaczenia i na tym etapie najbezpieczniej jest to zrobić na końcu html (tuż przed końcem taga `<body>`).

### Uzyskiwanie dostępu do elementów DOM
Aby wykonać jakiekolwiek operacje na elementach html'owy należy najpierw uzyskać "odnośniki" do nich po stronie js'a. Możemy tego dokonać posługując się metodami obiektu `window.document` (wystarczy samo `document`, bo `window` nie trzeba pisać). Najpopularniejsze z nich to chyba metody:
- [getElementById](https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementById) - zwraca odnośnik do elementu za pomocą jego html'owego atrybutu `id`. Przydatne zazwyczaj wtedy kiedy chcemy uzyskać dostęp do jednego konkretnego obiektu.
- [querySelectorAll](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelectorAll) - Pozwala uzyskać odnośniki do całej listy obiektów html'owych za jednym zamachem, na podstawie selector'a css


```javascript
// Prawie równoważne opcje.

// 1)
let el = document.getElementById('elId');

// 2)
let el = document.querySelectorAll('#elId')[0];

// Kiedy będzie inaczej?
```

#### Uwagi
API przeglądarkowe zawiera wiele innych możliwości adresowania dokumentów między innymi, relatywne atrybuty już uzyskanych wcześniej odnośników jak na przykład `parentNode` czy `childNodes` i wiele innych, które są wygodne do używania w niektórych sytuacjach. Niemniej powyższy zestaw jest "uniwersalny" i prosty.

`window.document` to mniej więcej odnośnik do elementu będącego korzenia DOM, czyli `<html>`. Jest on nieco wyróżniony z innych odnośników, ale np. metodę `querySelectorAll` posiadają również inne odnośniki pozwalając nam przeszukiwać swoje poddrzewa.

### Manipulacja elementami DOM
Mając już w garści odnośnik do jakiegoś elementu html możemy przystąpić do odczytu czy zmiany jego właściwości. W tym celu wystarczy użyć odpowiednich atrybutów czy metod odnośnika (zmiennej po stronie js'a odnoszącej się do elementu html).

```javascript
const el = document.getElementById('costam');

// Będzie miało efekt mniej więcej taki jak ustawienie za pomocą css'a koloru tekstu na czerwony
// w stylu inline.
el.style.color = 'red'

// Spowoduje wklejenie podanej wartości jako wewnętrznego html obiektu el
// (wraz z poprzednim punktem będzie to teraz czerwony duży napis 'Hello world').
el.innerHTML = '<h1>Hello world</h1>'

// Co się stanie teraz?
el.innerHTML = `
    <script>
        document.getElementById("costam").innerHTML="<script>alert('zhakowany')</script>"
    </script>`
// Dlaczego używanie atrybuty innerHTML może nie być bezpieczne kiedy wklejamy
// tam dowolne wartości np. pochodzące od innych użytkowników?

// W takich wypadkach można rozważyć atrybut innerText.
```

Atrybutów i metod odnośników elementów jest mnóstwo i lepiej niż je pamiętać w razie potrzeby szukać ich posługując się internetu. Jedną z rzeczy może wartych zapamiętania jest to, że dostęp do stylów css elementu html uzyskujemy przez atrybut `style` a następnie pod-atrybut odpowiadający nazwie atrybuty css (zamieniając kebab-case na camelCase).

### Obsługa zdarzeń
Potrafimy już zmieniać właściwości elementów html interaktywnie za pomocą js (czyli niejako modyfikować interface naszej aplikacji). Żeby móc się bawić w pełni brakuje nam jeszcze możliwości reagowania na to co użytkownik robi z naszą stroną. Akcje, które moglibyśmy chcieć obsługiwać to między innymi:
- kliknięcie przycisku
- przesunięcie myszki nad obrazkiem
- wysłanie formularza
- zminimalizowanie okna całej przeglądarki
- ...

Aby obsłużyć jakieś zdarzenie na stronie zazwyczaj wystarcza zgrabne posługiwanie się metodą `addEventListener` wcześniej pozyskanego odnośnika elementu html.

```javascript
// zgarnięcie odnośnika do elementu
const el = document.getElementById('costam');

// funkcja definiująca obsługę zdarzenia
function myEventHandler(event) {
    // wyświetlenie pierwszego atrybutu przekazanego funkcji na 'konsolę'
    // pierwszy argument przekazywany do funkcji obsługującej zdarzenie to opis co się wydarzyło
    // jest to obiekt zawierający informacje takie jak czas zdarzenia, czy docelowy element html itp.
    console.log(event)
    // wyświetlenie alertu
    alert('coś rzesz zrobił?')
}

// zarejestrowanie funkcji myEventHandler do obsługi zdarzenia kliknięcia elementu
// html odpowiadającego odnośnikowi el
// teraz będzie się działo ...
el.addEventListener('click', myEventListener)
```
Znowu przykład jest prosty, zdarzeń do obsługi jest wiele, ciężko wymienić je wszystkie. Jakąś pomocą może być wzięcie dowolnego elementu html wypisanie go na konsoli i przejrzenie atrybutów zaczynających się od `on` np. `onclick`. Odpowiadają one obsługiwanym zdarzeniom (bez prefiksu `on`), a nazwa może zasugerować co robią. Dalej oczywiście niezastąpioną pomocą zostaje internet.

Dużo więcej szczegółów można znaleźć [tutaj](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events). Link przedstawia między innymi:
- szczegóły działania `addEventListener`
- inny (niezalecany) sposoby dodawania obsługi zdarzeń
- usuwanie eventów przez `removeEventListener`
- zapobieganie domyślnej obsłudze różnych zdarzeń (np. żeby klawisz `F11` nie włączał fullscreen'a)
- zasady propagacji eventów - czyli w szczegółach to jak zdarzenie z elementu dziecka (np. jego kliknięcie) jest przekazywane później do rodzica (w strukturze DOM) i z powrotem (ważne!).
- przykłady! (więcej i fajne)

### Interakcja z serwerem przez AJAX'sy
Dotychczas wywoływanie funkcjonalności na serwerze następowało niejako przez "przeładowanie całej strony" (wywołane czy to kliknięciem odpowiedniego linku, czy wysłanie formularza). Powyższa praktyka zaczyna być uciążliwa w miarę jak nasza strona "rośnie" a wykonywane akcje dotyczą tylko jej fragmentu (każdorazowo trzeba przeładować dużą stronę i choć cache pewnie troche pomaga to można lepiej).

Na pomoc przychodzą nam AJAX'sy czyli technika wysyłania żądań http z poziomu js'a. Z grubsza dzieje się to co przeładowaniu całej strony, ale odpowiedź zamiast być przetwarzana przez przeglądarkę jako nowa-strona-do-wyświetlenia trafia z powrotem do js'a. Dzięki temu możemy nie tylko obsługiwać akcje użytkownika nie przeładowując strony ale też na przykład doładować dalszą zawartość naszej strony później, już po jej pierwotnym wyświetleniu zmniejszając czas ładowania strony i szczęście użytkownika.

To idea, detale są nieco bardziej skomplikowane głównie ze względu na to, że zrealizowanie i obsługa pełnego żądania http może trwać długo. Gdybyśmy wymienili możliwość wykonania żądania "całą przeglądarką" na wykonanie żądania js'em i zablokowanie strony do czasu końca jego obsługi to niewiele byśmy zyskali. Z pomocą przychodzi nam eventowa struktura js'a: wysyłając żądanie mówimy co ma się stać z jego wynikiem w przyszłości specyfikując to przez funkcję... Z grubsza (bardzo) tak to wygląda. Szczegóły np. [tutaj](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Fetching_data) (przy okazji sytuacja z callbackami w js'ie jest tak popularna, że ma nawet specjalne usprawnienie składniowe w postaci słów kluczowych `async` i `await`, niestety [to](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous) zaawansowana! dłuższa historia).

## Poboczne nieporuszone tematy
- środowisko programistyczne (też npm)
- narzędzia programisty w przeglądarce i debugowanie
- jquery - biblioteka ułatwiająca api js'a w szczególności manipulacje na dom'ie
- środowisko serwerowe oparte na js'ie - nodejs