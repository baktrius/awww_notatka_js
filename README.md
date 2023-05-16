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

Uwaga na boku jest taka, że miejsce dołączenia skryptu w kodzie html ma znaczenia i na tym etapie najbezpieczniej jest to zrobić na końcu html'a - tuż przed końcem taga `<body>` (inną metodę zaproponuję później przy okazji poznawania eventów).

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
    alert('cóż żeś zrobił?')
}

// zarejestrowanie funkcji myEventHandler do obsługi zdarzenia kliknięcia elementu
// html odpowiadającego odnośnikowi el
// teraz będzie się działo ...
el.addEventListener('click', myEventHandler)
```
Znowu przykład jest prosty, zdarzeń do obsługi jest wiele, ciężko wymienić je wszystkie. Jakąś pomocą może być wzięcie dowolnego elementu html wypisanie go na konsoli i przejrzenie atrybutów zaczynających się od `on` np. `onclick`. Odpowiadają one obsługiwanym zdarzeniom (bez prefiksu `on`), a nazwa może zasugerować co robią. Dalej niezastąpioną pomocą oczywiście pozostaje internet.

Dużo więcej szczegółów można znaleźć [tutaj](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events). Link przedstawia między innymi:
- szczegóły działania `addEventListener`
- inny (niezalecany) sposoby dodawania obsługi zdarzeń
- usuwanie eventów przez `removeEventListener`
- zapobieganie domyślnej obsłudze różnych zdarzeń (np. żeby klawisz `F11` nie włączał fullscreen'a)
- zasady propagacji eventów - czyli w szczegółach to jak zdarzenie z elementu dziecka (np. jego kliknięcie) jest przekazywane później do rodzica (w strukturze DOM) i z powrotem (ważne!).
- przykłady! (więcej i fajne)

Na koniec tego akapitu jako wzór dobrego stylu chciałem podać jeszcze jeden przykład zastosowania eventów. Wspomniałem wcześniej, że skrypty najłatwiej dołączać na końcu html'a, żeby uciec od problemu kolejności ładowania strony. Jednak dawanie nieformalnego wymagania w pakiecie ze skryptem, aby był on dołączony na końcu strony, nie jest powszechnie uważane w środowisku za dobrą praktykę programistyczną i może narazić nas na krzywe spojrzenie współziomków. Na ratunek przed tą niekomfortową sytuacją przychodzą nam eventy.
```javascript
addEventListener("DOMContentLoaded", (event) => {
    // anonimowa funkcja obsługi zdarzenia pełnego załadowania DOM'a (mniej więcej html'a)

    // w tym miejscu możemy bezpiecznie odwoływać się już do wszystkich elementów html
    // będących na stronie - z pewnością już zdążyły się załadować

    // często w takiej klamrze bezpieczeństwa umieszcza się po prostu wszystko

    const el = document.getElementById("coDuszaZapragnie"); // bezpieczne
    el.innerText = "strona załadowana!";
});
```

### Interakcja z serwerem przez AJAX'sy
Dotychczas wywoływanie funkcjonalności na serwerze następowało niejako przez "przeładowanie całej strony" (wywołane czy to kliknięciem odpowiedniego linku, czy wysłaniem formularza). Powyższa praktyka zaczyna być uciążliwa w miarę jak nasza strona "rośnie" a wykonywane akcje dotyczą tylko jej fragmentu (każdorazowo trzeba przeładować dużą stronę i choć cache pewnie troche pomaga to można lepiej).

Na pomoc przychodzą nam AJAX'sy czyli technika wysyłania żądań http z poziomu js'a. Z grubsza dzieje się to co przeładowaniu całej strony, ale odpowiedź zamiast być przetwarzana przez przeglądarkę jako nowa-strona-do-wyświetlenia trafia z powrotem do js'a. Dzięki temu możemy nie tylko obsługiwać akcje użytkownika nie przeładowując strony ale też na przykład doładować dalszą zawartość naszej strony później, już po jej pierwotnym wyświetleniu zmniejszając czas ładowania strony i szczęście użytkownika.

To idea, detale są nieco bardziej skomplikowane głównie ze względu na to, że zrealizowanie i obsługa pełnego żądania http może trwać długo. Gdybyśmy wymienili możliwość wykonania żądania "całą przeglądarką" na wykonanie żądania js'em i zablokowanie strony do czasu końca jego obsługi to niewiele byśmy zyskali. Z pomocą przychodzi nam eventowa struktura js'a: wysyłając żądanie mówimy co ma się stać z jego wynikiem w przyszłości specyfikując to przez funkcję... Bardzo z grubsza tak to wygląda. Szczegóły np. [tutaj](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Fetching_data) (przy okazji sytuacja z callbackami w js'ie jest tak popularna, że ma nawet specjalne usprawnienie składniowe w postaci słów kluczowych `async` i `await`, niestety [to](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous) zaawansowana! dłuższa historia).

### Asynchroniczny js
Opowiem o składni, która jest relatywnie nowa (w standardzie jest chyba od 2017 roku) i niezupełnie uniwersalna (nie wszystko da się nią osiągnąć). Niemniej ma ten wielki atut, że jest niesłychanie elegancka i sprawia, że programowanie asynchroniczne wygląda praktycznie jak zwykły sekwencyjny kod. Bohaterami tego ustepu będą słowa kluczowe `await` i `async`.

Zacznijmy od tego, że kod js'owy jest "jednowątkowy" lub inaczej cały wykonuje się pod monitorem / w sekcji krytycznej! Tuż po odbyciu kursu z programowania współbieżnego ta nowina może nas bardzo przerazić - jak to jednowątkowy? Gdzie wydajność? Gdzie co? Halo.

Szkopuł w tym, że js jest językiem skryptowym i zazwyczaj funkcjonuje w środowisku które naturalnie rozszerza go o api pozwalające na interakcję ze światem (lub może poprawniej będzie stwierdzić, że to coś zewnętrznego rozszerza się o js'a). To api pisze się np. w c czy c++ i od tych języków nikt nie wymaga już żeby były jednowątkowe (ze wszystkimi dobrymi i złymi tego konsekwencjami). Zatem jeszcze raz podkreślmy ten doniosły fakt: kod js'owy wykonuje się niepodzielnie (z perspektywy innego kodu js'owego) w sekcji krytycznej. Wywołana funkcja zakończy swoje wykonanie zanim będzie się mogła zacząć następna.

Znacząco ułatwia to wnioskowanie o programach js'owych - skoro wszystko jest w sekcji krytycznej to my nie musimy już pisać swoich własnych. Jednak jak w takim przypadku wywołać funkcję która może zająć dużo czasu? Nie chcielibyśmy przecież aby całe nasze środowisko na ten czas się zatrzymało. Otóż możemy skorzystać z faktu, że c, czy c++ pod spodem nie są jednowątkowe i zlecić im brudną robotę wykonania długiego zadania (co jest wygodne jeszcze z tej perspektywy, że c czy c++ jako języki kompilowane mają szansę szybciej sobie z tą pracą poradzić). Musimy tylko pamiętać, aby zlecając dłubie zadanie, powiedzieć co ma się stać z jego wynikiem specyfikując tak zwanego callback'a. W międzyczasie oczekiwania na wykonanie długiego zadania, js'owa sekcja krytyczna będzie pusta i gotowa do podjęcia się obsługi innego zadania.

Wydaje mi się to kluczowe do zrozumienia js'a: w przeciwieństwie do c jego runtime to nie jedna główna funkcja main czy nawet grupa wątków. Js to jedna wielka sekcja krytyczna z kolejką zadań do wykonania. Kod dodaje callback'i obsługujące zdarzenia. Zdarzenia dodają odpowiednie callback'i do kolejki. Sekcja krytyczna po kolei wykonuje te zadania (mogą one dodawać obsługę następnych zdarzeń). I tak koło się kręci.

Z tej perspektywy wykonanie funkcji wykonującej duże zadanie przebiega następująco:
1) sekcja krytyczna js zaczyna obsługę długiej funkcji
2) w pewnym momencie ta funkcja zleca środowisku przez api wykonanie długiego zadani specyfikując co ma się stać z jego wynikiem
3) sekcja krytyczna js zostaje zwolniona i może obsługiwać dalszy kod js'owy, praca związana z dużym zadaniem dzieje się w tly / innym wątku bez udziału js'a...
4) długie zadanie zostaje zakończone, przekazany wcześniej callback z dołożonym wynikiem zadania zostaje położony na kolejkę zadań js - wydarzył się event zakończenia obsługi długiego zadania.
5) Sekcja krytyczna js'a w końcu podejmuje z kolejki z powrotem obsługę długiej funkcji.

Można na to patrzeć w ten sposób, że sposobem na wykonywanie długich funkcji w sekcji krytycznej jest rozbijanie ich na mniejsze części.

#### Promisy
Wartość, która będzie obliczona dopiero w przyszłości można spróbować związać z obiektem. Obiekt ten będący obietnicą, że wartość ta się kiedyś pojawi, pozwala określić co ma się stać w przypadku otrzymania wyniku - metoda `then`, ale też błędu (powstałego gdy ktoś się z czegoś nie wywiązał) - meotda `catch`. Taki wzorzec projektowy jest dość ogólnym pomysłem i mieliśmy już okazję zobaczyć go na przykład w javie czy c++.

Tak więc promisy są jedynie pewnym pomysłem / wzorcem narzuconym samemu sobie celem uporządkowania kodu asynchronicznego. Jako byt istniejący na skraju programowania współbieżnego są nieco zawiłe. Niemniej szczęśliwie dla nas js ma specjalną składnię, która trochę nam z nimi pomaga. Są to zapowiedziane (i długo wyczekiwane) `await` i `async`.

#### Await i Async
`await` poprzedzające jakąś obietnicę długo obliczanej wartości mówi, hej zaczekaj na nią i wznów wykonanie bieżącej funkcji dopiero gdy ta wartość będzie gotowa (pod spodem tak na prawdę dzieją się callback'i i zwalnianie sekcji krytycznej jak w powyżej przedstawionym schemacie). Niemniej takie czekanie na długą wartość nie może pozostać bezkarne - wszak funkcja która czeka na obliczenie długiej obietnicy samo długo się wykonuje i tym samym musi zostać poprzedzona słowem kluczowym `async` (znacznik, że długo się wykonuje). To zaś sprawi, że dowolna wartość z niej zwrócona z perspektywy zewnętrznej funkcji będzie obietnicą. Można na nią zaczekać używając `await`... I tak w kółko długość obliczenia rozszerza się jak jakaś niepowstrzymana zaraza. Jedyny ratunek to nie czekać i uciekać.

Zanim pokaże przykład nadmienię tylko, że znaczącą bolączką pisania kodu asynchronicznego jest obsługa błędów (callback'i mnożą się w tempie królików Fibonacciego). `await` pozwala nam przeskoczyć tę niedogodność propagując informację o sytuacji wyjątkowe (errory) z callback'ów do ich potencjalnej obsługi w funkcji macierzystej (catch'y). Zaiste piękne.

A więc przykład (odpowiednio zmodyfikowany, ze strony o fetch api, bo może być to teraz w państwa szczególnym zainteresowaniu):
```javascript
// przed async i await

// Call `fetch()`, passing in the URL.
fetch(url)
  // fetch() returns a promise. When we have received a response from the server,
  // the promise's `then()` handler is called with the response.
  .then((response) => {
    // Our handler throws an error if the request did not succeed.
    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }
    // Otherwise (if the response succeeded), our handler fetches the response
    // as text by calling response.text(), and immediately returns the promise
    // returned by `response.text()`.
    return response.text();
  })
  // When response.text() has succeeded, the `then()` handler is called with
  // the text, and we copy it into the `poemDisplay` box.
  .then((text) => {
    poemDisplay.textContent = text;
  })
  // Catch any errors that might happen, and display a message
  // in the `poemDisplay` box.
  .catch((error) => {
    poemDisplay.textContent = `Could not fetch verse: ${error}`;
  });

```

```javascript
// po async i await

// Call `fetch()`, passing in the URL.
// fetch() returns a promise. We wait for it to resolve.
try {
    const response = await fetch(url);
    if (!response.ok) {
        throw new Error(`HTTP error: ${response.status}`);
    }
    // Otherwise (if the response succeeded), fetch the response
    // as text by calling response.text(), and wait for resulting promise
    const text = await response.text();
    // When response.text() has succeeded await returns
    // the text, and we copy it into the `poemDisplay` box.
    poemDisplay.textContent = text;
} catch(error) {
    // Catch any errors that might happen, and display a message
    // in the `poemDisplay` box.
    poemDisplay.textContent = `Could not fetch verse: ${error}`;
}
```
Przynajmniej według mniej druga wersja wygląda dużo, dużo lepiej. Co ważne, należy pamiętać, że pod spodem tak na prawdę dzieją się callback'i itp. rzeczy więc funkcja `async` nie wykonuje się już niepodzielnie (wywołania `await` to potencjalne miejsca przerwań i przeplotów z innymi funkcjami) - trzeba uważać.

Dodatkowo wspominałem, że dodanie `await` sprawia, że z perspektywy zewnętrza jesteśmy długo działającym kodem. Jeśli podobny długi kod byłby na najwyższym poziomie w skrypcie (nieopakowany funkcja `async`) to mogą (nie muszą) się stać rzeczy nieprzyjemne pokroju: `error: top level await`. Wraz z nowymi standardami takie zachowanie jest już dozwolone, ale wcześniej było niepoprawne. W takich wypadkach najlepiej opakować długi kod długą funkcja i ją wywołać z najwyższego poziomu. Nikt nie będzie się na nas złościł, że nie czekamy na najwyższym poziomie na obietnicę długo obliczanej wartości, dopóki jej obliczanie się powiedzie (albo się nie skończy). Jeśli się nie powiedzie zobaczymy przykre `error: uncaught exception in promise`. Już ostatnia uwaga (obiecuję): o ile `await`, `async`, promisy i js są super i fajne, to dalej trzeba być ostrożnym parając się ogniem (programowaniem współbieżnym).

## Poboczne nieporuszone tematy
- środowisko programistyczne (też npm)
- narzędzia programisty w przeglądarce i debugowanie
- jquery - biblioteka ułatwiająca api js'a w szczególności manipulacje na dom'ie
- środowisko serwerowe oparte na js'ie - nodejs