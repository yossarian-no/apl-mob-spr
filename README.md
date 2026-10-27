# Sprawozdanie z projektu – aplikacja mobilna „Kalorik”
 
**Przedmiot:** Aplikacje mobilne
**Temat projektu:** Licznik kalorii – dziennik posiłków na system Android
**Autor:** _[imię i nazwisko]_, nr indeksu _[…]_
**Data:** _[…]_
 
---
 
## Spis treści
 
1. [Cel i opis aplikacji](#1-cel-i-opis-aplikacji)
2. [Funkcjonalność i ekrany](#2-funkcjonalność-i-ekrany)
3. [Wykorzystane technologie](#3-wykorzystane-technologie)
4. [Architektura aplikacji](#4-architektura-aplikacji)
5. [Realizacja kryteriów oceny](#5-realizacja-kryteriów-oceny)
6. [Testowanie](#6-testowanie)
7. [Podsumowanie punktacji](#7-podsumowanie-punktacji)
8. [Możliwe kierunki rozwoju](#8-możliwe-kierunki-rozwoju)
9. [Źródła](#9-źródła)
---
 
## 1. Cel i opis aplikacji
 
Celem projektu było stworzenie aplikacji mobilnej, która pomaga kontrolować dzienną liczbę spożywanych kalorii i makroskładników (białka, tłuszczów i węglowodanów).
 
**Kalorik** to dziennik posiłków. Użytkownik zapisuje, co i w jakiej ilości zjadł, a aplikacja:
- sumuje kalorie i makroskładniki z każdego dnia,
- porównuje wynik z indywidualnym limitem wyliczonym na podstawie profilu użytkownika,
- powiększa limit o kalorie spalone podczas chodzenia, licząc kroki krokomierzem telefonu,
- pokazuje statystyki z ostatniego tygodnia lub miesiąca.
Produkty można dodawać na trzy sposoby:
1. **wyszukując je po nazwie** w otwartej bazie produktów spożywczych Open Food Facts,
2. **skanując kod kreskowy** z opakowania aparatem telefonu,
3. **tworząc własny produkt** (np. domowe danie) wraz ze **zdjęciem** zrobionym aparatem.
Dane są przechowywane lokalnie w telefonie, więc aplikacja działa także bez internetu. Po zalogowaniu są synchronizowane z bazą w chmurze, dzięki czemu użytkownik nie traci ich po zmianie telefonu.
 
> _[Miejsce na zrzut ekranu: animacja startowa i ekran dziennika]_
 
---
 
## 2. Funkcjonalność i ekrany
 
Aplikacja składa się z jednej aktywności. Nawigację zapewnia dolny pasek z czterema kartami oraz ekrany szczegółowe.
 
### 2.1. Ekran startowy (animacja)
Po uruchomieniu wyświetla się krótka (ok. 2 s) animacja z logo aplikacji. Dotknięcie ekranu ją pomija. Animacja nie powtarza się po obrocie ekranu.
 
### 2.2. Dziennik (funkcja chroniona logowaniem)
- przełączanie dni (strzałki, a dotknięcie daty wraca do dzisiaj),
- **pierścień kalorii**: zjedzone kalorie na tle dziennego limitu, z informacją „Zostało X kcal” albo „Przekroczono o X kcal”,
- **paski makroskładników**: białko, tłuszcze i węglowodany na tle celów,
- **karta kroków**: liczba kroków z bieżącego dnia i spalone kalorie,
- cztery sekcje posiłków: śniadanie, obiad, kolacja, przekąski. Każda ma przycisk „+” i listę wpisów,
- dotknięcie wpisu otwiera okno do zmiany porcji albo usunięcia wpisu,
- przy pustym dniu wyświetla się ilustracja pustego talerza.
### 2.3. Produkty (dostępne także bez logowania)
- wyszukiwarka po nazwie: najpierw wyniki z lokalnej bazy, potem z Open Food Facts,
- przycisk **„Skanuj kod”** (aparat) i opcja ręcznego wpisania kodu kreskowego,
- przycisk **„Własny”**, który otwiera formularz własnego produktu,
- listy „Moje produkty” i „Ostatnio używane”,
- jeśli zeskanowanego kodu nie ma w bazie, aplikacja proponuje dodanie go jako produktu własnego, z wypełnionym już kodem.
### 2.4. Szczegóły produktu
- zdjęcie, nazwa, marka, źródło danych,
- tabela wartości odżywczych na 100 g i na wybraną porcję, przeliczana na bieżąco,
- szybki wybór porcji (25–250 g) i wybór posiłku,
- przycisk „Dodaj do dziennika”. Dla gościa zamiast niego pojawia się przycisk „Zaloguj się, aby dodać do dziennika”.
### 2.5. Własny produkt (funkcja chroniona logowaniem)
Formularz z polami: nazwa, marka, kaloryczność i makroskładniki na 100 g, kod kreskowy. Do produktu można dodać **zdjęcie zrobione aparatem**.
 
### 2.6. Statystyki (funkcja chroniona logowaniem)
- wykres słupkowy kalorii z 7 lub 30 dni z przerywaną linią limitu. Słupki ponad limitem mają kolor ostrzegawczy,
- średnia liczba kalorii, liczba dni w limicie,
- średnie dzienne makroskładniki i ich procentowy udział w diecie.
### 2.7. Profil
- dane konta, czas ostatniej synchronizacji, liczba zmian czekających na wysłanie, przyciski **„Synchronizuj”** i **„Wyloguj”**,
- formularz profilu: płeć, wiek, wzrost, waga, poziom aktywności i cel (schudnąć / utrzymać wagę / przytyć),
- dzienny limit kalorii i celów makroskładników przeliczany na żywo,
- wybór motywu: systemowy, jasny albo ciemny.
### 2.8. Logowanie i rejestracja
Logowanie, zakładanie konta (z powtórzeniem hasła) i reset hasła przez e-mail. Formularz sprawdza dane po stronie aplikacji, a błędy wyświetla po polsku.
 
### 2.9. Sposób liczenia limitu kalorii
Podstawowa przemiana materii (BMR) jest liczona **wzorem Mifflina–St Jeora**:
 
- mężczyźni: BMR = 10 · masa [kg] + 6,25 · wzrost [cm] − 5 · wiek + 5
- kobiety: BMR = 10 · masa [kg] + 6,25 · wzrost [cm] − 5 · wiek − 161
Dzienny limit = BMR × współczynnik aktywności (1,2 / 1,375 / 1,55 / 1,725) + korekta celu (−500 / 0 / +300 kcal), nie mniej niż 1200 kcal. Cele makroskładników wynoszą 25% energii z białka, 30% z tłuszczów i 45% z węglowodanów. Kalorie spalone w krokach są szacowane jako ok. 0,5 kcal na kilogram masy ciała na każde 1000 kroków.
 
---
 
## 3. Wykorzystane technologie
 
| Obszar | Technologia |
|---|---|
| Język | Kotlin |
| Interfejs | Jetpack Compose, Material Design 3 |
| Nawigacja | Navigation Compose |
| Stan aplikacji | ViewModel (`AndroidViewModel`), stan Compose, korutyny Kotlina |
| Lokalna baza danych | SQLite (`SQLiteOpenHelper`) |
| Ustawienia | `SharedPreferences` |
| Logowanie | Firebase Authentication (e-mail + hasło) |
| Zdalna baza danych | Cloud Firestore |
| Synchronizacja w tle | WorkManager (`CoroutineWorker`) |
| Usługa zewnętrzna | Open Food Facts API (HTTP + JSON, `HttpURLConnection`, `org.json`) |
| Skaner kodów kreskowych | Google ML Kit Code Scanner (`play-services-code-scanner`) |
| Zdjęcia z aparatu | `ActivityResultContracts.TakePicture` + `FileProvider` |
| Wyświetlanie obrazów | Coil |
| Sensory | `SensorManager`, `Sensor.TYPE_STEP_COUNTER` |
| Animacje | Compose Animation (`Animatable`, `tween`, `spring`, `Crossfade`, `AnimatedVisibility`), rysowanie na `Canvas` |
| Grafika | własne grafiki wektorowe (`VectorDrawable`), ikona adaptacyjna |
| Środowisko | Android Studio, Gradle (Kotlin DSL) |
 
---
 
## 4. Architektura aplikacji
 
### 4.1. Struktura kodu
 
```
com.example.kalorik
├── MainActivity.kt            – jedyna aktywność: animacja startowa, potem aplikacja
├── data/
│   ├── Models.kt              – modele: Product, DiaryEntry, Meal, Profile (obliczanie limitu)
│   ├── LocalDatabase.kt       – lokalna baza SQLite
│   ├── OpenFoodFactsApi.kt    – komunikacja z Open Food Facts
│   ├── CloudRepository.kt     – Firebase Authentication i Firestore
│   ├── SyncWorker.kt          – synchronizacja lokalna ↔ zdalna (WorkManager)
│   ├── Settings.kt            – SharedPreferences (profil, motyw, czas synchronizacji)
│   └── Dates.kt               – operacje na datach
├── sensors/
│   └── StepCounter.kt         – obsługa krokomierza
└── ui/
    ├── MainViewModel.kt       – wspólny stan aplikacji
    ├── KalorikApp.kt          – nawigacja i dolny pasek
    ├── Components.kt          – wspólne komponenty (pierścień kalorii, paski makroskładników…)
    ├── theme/                 – kolory, motyw jasny i ciemny, typografia
    └── screens/               – ekrany: Splash, Login, Diary, Products, Product,
                                 CustomProduct, Stats, Profile
```
 
### 4.2. Przepływ danych – podejście offline-first
1. Każda zmiana (nowy wpis, zmiana porcji, usunięcie, nowy produkt) jest od razu zapisywana w **lokalnej bazie SQLite** z flagą `synced = 0`, więc interfejs pokazuje ją natychmiast, także bez internetu.
2. Aplikacja zleca zadanie **WorkManagerowi**, który przy dostępnej sieci wysyła wszystkie zaległe zmiany do **Firestore** i oznacza je jako zsynchronizowane.
3. Usunięcie wpisu działa w dwóch krokach. Najpierw wpis dostaje lokalnie flagę `deleted = 1`, a po skasowaniu go w chmurze zostaje usunięty fizycznie.
4. Po zalogowaniu (także na nowym urządzeniu) dane konta są pobierane z Firestore do lokalnej bazy.
### 4.3. Lokalna baza danych (SQLite)
 
**Tabela `products`** – pamięć podręczna produktów z Open Food Facts i produkty własne:
 
| Kolumna | Opis |
|---|---|
| `id` | kod kreskowy albo `own_<uuid>` dla produktu własnego |
| `name`, `brand` | nazwa, marka |
| `kcal100`, `protein100`, `fat100`, `carbs100` | wartości na 100 g |
| `image_url` / `photo_path` | zdjęcie z Open Food Facts / zdjęcie z aparatu |
| `barcode`, `is_custom`, `owner_uid` | kod, czy produkt własny, właściciel |
| `last_used` | czas ostatniego użycia (lista „Ostatnio używane”) |
| `synced` | czy produkt własny jest już w chmurze |
 
**Tabela `entries`** – wpisy dziennika:
 
| Kolumna | Opis |
|---|---|
| `id` | UUID wpisu |
| `uid` | identyfikator użytkownika (Firebase) |
| `date`, `meal` | dzień (`yyyy-MM-dd`) i posiłek |
| `product_id`, `product_name`, `grams` | produkt i porcja |
| `kcal`, `protein`, `fat`, `carbs` | wartości odżywcze porcji |
| `created_at` | czas dodania |
| `synced`, `deleted` | stan synchronizacji |
 
### 4.4. Zdalna baza danych (Cloud Firestore)
 
```
users/{uid}                     – profil użytkownika
users/{uid}/entries/{id}        – wpisy dziennika
users/{uid}/products/{id}       – produkty własne
```
 
Reguły bezpieczeństwa (`firestore.rules`) pozwalają czytać i zapisywać dane tylko zalogowanemu właścicielowi:
```
match /users/{userId}/{document=**} {
  allow read, write: if request.auth != null && request.auth.uid == userId;
}
```
 
---
 
## 5. Realizacja kryteriów oceny
 
### 5.1. Aplikacja wykonana samodzielnie (1 pkt)
Projekt został wykonany jednoosobowo.
 
### 5.2. Czytelny i przyjazny interfejs (1 pkt)
- interfejs zgodny z **Material Design 3**: karty, dolny pasek nawigacji, przycisk FAB „Dodaj posiłek”, chipy, przyciski segmentowe,
- **motyw jasny i ciemny** (systemowy lub wybrany ręcznie) we własnej, zielono-pomarańczowej kolorystyce nawiązującej do ikony,
- wszystkie teksty i komunikaty błędów po polsku,
- najważniejsza informacja (ile kalorii zostało) widoczna od razu na ekranie dziennika,
- obsługa pustych stanów (ilustracja pustego talerza, podpowiedzi w wyszukiwarce), wskaźniki ładowania, walidacja formularzy,
- szybkie skróty: gotowe porcje, domyślny posiłek dobierany do pory dnia, lista ostatnio używanych produktów.
### 5.3. Własna grafika rastrowa i/lub wektorowa – także jako ikona (1 pkt)
Wszystkie grafiki zostały zaprojektowane specjalnie dla aplikacji:
- **ikona aplikacji**: adaptacyjna ikona wektorowa przedstawiająca jabłko w pierścieniu dziennego limitu kalorii (`ic_launcher_foreground.xml`, `ic_launcher_background.xml` z gradientem), wersja monochromatyczna dla ikon tematycznych Androida 13+ (`ic_launcher_monochrome.xml`) oraz wersje rastrowe PNG dla starszych systemów (`mipmap-*dpi`),
- **logo** na ekranie logowania i w ekranach chronionych (`logo.xml`),
- **grafika jabłka** wykorzystywana w animacji startowej (`logo_apple.xml`),
- **ilustracja pustego talerza** ze sztućcami, wyświetlana przy pustym dniu w dzienniku (`illustration_empty_plate.xml`).
Ten sam motyw graficzny (jabłko, pierścień, kolory) powtarza się w ikonie, animacji startowej, pierścieniu kalorii na ekranie dziennika i w palecie kolorów aplikacji.
 
### 5.4. Własna animacja (1 pkt)
**Animacja startowa** (`SplashScreen.kt`) składa się z kilku zsynchronizowanych etapów:
1. żółty pierścień dziennego limitu dorysowuje się do 3/4 obwodu z krzywą przyspieszenia `FastOutSlowInEasing` (zgodną z Material Motion),
2. jabłko „wskakuje” do środka ze sprężystym odbiciem: animacja fizyczna `spring` z tłumieniem `DampingRatioMediumBouncy`,
3. z końca pierścienia rozlatują się zanikające iskierki, symbolizujące spalane kalorie (rysowanie na `Canvas`),
4. nazwa aplikacji wsuwa się od dołu z jednoczesnym pojawianiem się (przezroczystość i przesunięcie),
5. przejście do aplikacji odbywa się płynnym przenikaniem (`Crossfade`).
**Animacje w interfejsie:**
- **pierścień kalorii** w dzienniku „dorysowuje się” od zera do aktualnej wartości. Po przekroczeniu limitu zmienia kolor i rysuje drugi, czerwony łuk nadwyżki, a liczba kalorii w środku rośnie razem z łukiem,
- **paski makroskładników** płynnie się wypełniają,
- **słupki wykresu** w statystykach „wyrastają” po każdej zmianie zakresu (7/30 dni),
- pole „Powtórz hasło” w formularzu rejestracji pojawia się animowanie (`AnimatedVisibility`).
### 5.5. Logowanie do wybranych chronionych funkcjonalności (2 pkt)
Logowanie zrealizowano przez **Firebase Authentication** z kontem e-mail + hasło (`CloudRepository.kt`, `LoginScreen.kt`):
- rejestracja nowego konta, logowanie, **reset hasła** wysyłany e-mailem, wylogowanie,
- walidacja (poprawny e-mail, hasło co najmniej 6 znaków, zgodność haseł) i czytelne komunikaty błędów (np. „Konto z tym adresem już istnieje”, „Nieprawidłowy e-mail lub hasło”, „Brak połączenia z internetem”),
- sesja jest pamiętana, więc po ponownym uruchomieniu nie trzeba logować się od nowa.
**Podział funkcji:**
 
| Dostępne bez logowania | Chronione logowaniem |
|---|---|
| wyszukiwarka produktów (Open Food Facts) | dziennik posiłków |
| skaner kodów kreskowych | statystyki |
| podgląd wartości odżywczych produktu | profil i dzienny limit |
| zmiana motywu | produkty własne (ze zdjęciem) |
| | synchronizacja z chmurą |
 
Gość, który wejdzie na ekran chroniony, widzi ekran z kłódką, opisem funkcji i przyciskiem „Zaloguj się”. Po zalogowaniu wraca do miejsca, z którego przyszedł. Dane w chmurze są dodatkowo chronione regułami Firestore: każdy użytkownik ma dostęp wyłącznie do swojej gałęzi `users/{uid}`.
 
### 5.6. Sensory smartfona (0,5 pkt za każdy)
 
#### Krokomierz (`Sensor.TYPE_STEP_COUNTER`) – 0,5 pkt
Plik `StepCounter.kt`. Kroki zrobione danego dnia są zamieniane na spalone kalorie, które **powiększają dzienny limit** w dzienniku (np. „w tym +150 kcal za kroki”). To naturalne zastosowanie: aplikacja do kontroli bilansu energetycznego uwzględnia aktywność użytkownika.
 
Szczegóły realizacji:
- sprzętowy licznik podaje kroki od ostatniego uruchomienia telefonu, więc aplikacja zapamiętuje punkt startowy każdego dnia i od niego liczy dzisiejsze kroki,
- obsługa restartu telefonu (licznik zaczyna wtedy od zera, a dotychczasowe kroki z danego dnia są doliczane),
- prośba o uprawnienie `ACTIVITY_RECOGNITION` (Android 10+) wyświetlana w karcie kroków dopiero wtedy, gdy użytkownik zechce włączyć funkcję,
- czytelny komunikat, gdy telefon nie ma krokomierza.
#### Aparat – 0,5 pkt
Aparat jest wykorzystany na dwa sposoby, oba związane z dodawaniem jedzenia:
1. **skanowanie kodu kreskowego** z opakowania (formaty EAN-13, EAN-8, UPC-A, UPC-E) przez Google ML Kit Code Scanner, z automatycznym przybliżeniem. Odczytany kod jest wyszukiwany w lokalnej bazie i w Open Food Facts,
2. **zdjęcie produktu własnego** (`ActivityResultContracts.TakePicture`): plik zapisywany w prywatnym katalogu aplikacji i udostępniany aplikacji aparatu przez `FileProvider`. Zdjęcie pojawia się potem na listach produktów i na ekranie szczegółów.
### 5.7. Wykorzystanie zdalnej / zewnętrznej bazy danych (2 pkt)
**Cloud Firestore** (Firebase) przechowuje profil użytkownika, wpisy dziennika i produkty własne (struktura w punkcie 4.4).
- dane są wysyłane w tle przez `SyncWorker` i pobierane po zalogowaniu (`SyncWorker.pull`),
- w profilu widać czas ostatniej synchronizacji i liczbę zmian czekających na wysłanie, jest też przycisk ręcznej synchronizacji,
- po zalogowaniu na innym telefonie cały dziennik, profil i produkty własne są odtwarzane z chmury.
### 5.8. Wykorzystanie lokalnej bazy danych (1 pkt)
**SQLite** (`LocalDatabase.kt`, opis tabel w punkcie 4.3):
- dziennik posiłków (z zapytaniami agregującymi `SUM … GROUP BY date` dla statystyk),
- **pamięć podręczna produktów** z Open Food Facts: raz pobrany produkt jest dostępny offline, a zeskanowany ponownie kod nie wymaga połączenia,
- produkty własne i lista ostatnio używanych produktów,
- **kolejka zmian do synchronizacji** (flagi `synced` i `deleted`).
Dzięki lokalnej bazie dziennik, statystyki i wyszukiwanie wśród zapisanych produktów działają w pełni bez internetu.
 
### 5.9. Darmowa usługa zewnętrzna zasilająca aplikację danymi (2 pkt)
**Open Food Facts** (`OpenFoodFactsApi.kt`) to otwarta, darmowa baza produktów spożywczych z całego świata, z dobrym pokryciem produktów dostępnych w Polsce. Nie wymaga klucza API ani opłat.
- wyszukiwanie po kodzie kreskowym: `GET https://world.openfoodfacts.org/api/v2/product/{kod}.json`,
- wyszukiwanie po nazwie: `GET https://pl.openfoodfacts.org/cgi/search.pl?search_terms=…&json=1` (polska wersja serwisu, lepsze wyniki dla polskich nazw),
- z odpowiedzi JSON pobierane są: nazwa (polska, jeśli istnieje), marka, kaloryczność, białko, tłuszcze, węglowodany i miniatura zdjęcia. Gdy energia jest podana tylko w kJ, aplikacja przelicza ją na kcal, a produkty bez kaloryczności są pomijane,
- zgodnie z zasadami serwisu zapytania mają własny nagłówek `User-Agent`, a ich liczba jest ograniczona: wyszukiwanie dopiero po zatwierdzeniu frazy, wyniki zapisywane lokalnie,
- obsługa błędów: przy braku połączenia aplikacja pokazuje produkty zapisane w telefonie i komunikat, a przy nieznanym kodzie proponuje dodanie produktu własnego.
### 5.10. Uwzględnienie aspektów energochłonności (1–2 pkt)
| Element | Rozwiązanie oszczędzające energię |
|---|---|
| Synchronizacja | **WorkManager** zamiast stałego połączenia: zmiany są grupowane (start po 20 s, kolejna zmiana przesuwa start, więc kilka wpisów idzie jednym pakietem), zadanie uruchamia się tylko przy dostępnej sieci (`NetworkType.CONNECTED`), a nieudane próby są ponawiane z wykładniczo rosnącym odstępem |
| Krokomierz | **sprzętowy licznik kroków**, liczony przez energooszczędny koprocesor ruchu, zamiast ciągłej analizy akcelerometru. Nasłuchiwanie tylko wtedy, gdy ekran dziennika jest na pierwszym planie (`LifecycleResumeEffect`, wyrejestrowanie w `onPause`), a zdarzenia grupowane przez czujnik (`maxReportLatency` = 10 s) |
| Aparat | włączany wyłącznie na czas skanowania kodu lub robienia zdjęcia (systemowy skaner / aplikacja aparatu), bez stałego podglądu |
| Sieć | zapytania do Open Food Facts dopiero po zatwierdzeniu wyszukiwania (nie przy każdej wpisanej literze), lokalna pamięć podręczna produktów, najpierw sprawdzanie kodu w lokalnej bazie |
| Obrazy | biblioteka Coil z pamięcią podręczną, pobierane są małe miniatury zdjęć |
| Ekran | **ciemny motyw**, który na ekranach OLED zmniejsza pobór prądu |
| Wątki | operacje na bazie i sieci w korutynach na `Dispatchers.IO`, bez blokowania wątku interfejsu |
 
### 5.11. Uruchomienie na urządzeniu mobilnym (1 pkt)
Aplikacja została przetestowana i jest prezentowana na fizycznym telefonie z Androidem. Dopiero na nim działają w pełni aparat (skanowanie kodów, zdjęcia) i krokomierz.
 
_Urządzenie testowe: [model telefonu], Android [wersja]._
 
### 5.12. Dodatkowo: logowanie zdarzeń (logi)
Wybrane operacje są zapisywane w dzienniku systemowym (Logcat, tagi `Kalorik` i `SyncWorker`): dodanie wpisu do dziennika (produkt, porcja, kalorie), zapisanie produktu własnego, przebieg synchronizacji oraz błędy połączenia z Open Food Facts i skanerem. Ułatwia to diagnozowanie problemów podczas testów.
 
---
 
## 6. Testowanie
 
| Scenariusz | Oczekiwany wynik |
|---|---|
| Uruchomienie aplikacji | animacja startowa, potem dziennik (dla gościa ekran z prośbą o logowanie) |
| Rejestracja i logowanie | konto widoczne w Firebase Console → Authentication |
| Uzupełnienie profilu | wyliczony dzienny limit kalorii, profil zapisany w Firestore |
| Wyszukanie produktu (np. „skyr”) | lista produktów z Open Food Facts z kalorycznością |
| Zeskanowanie kodu z opakowania | otwiera się ekran produktu z wartościami odżywczymi |
| Nieznany kod | propozycja dodania produktu własnego z wypełnionym kodem |
| Produkt własny ze zdjęciem | produkt na liście „Moje produkty” ze zdjęciem |
| Dodanie posiłku | pierścień i paski makroskładników się aktualizują, wpis po chwili widoczny w Firestore |
| Edycja / usunięcie wpisu | zmiana widoczna w dzienniku i w chmurze |
| Tryb samolotowy | dziennik i zapisane produkty działają, zmiany wysyłają się po odzyskaniu sieci |
| Spacer z telefonem | rośnie liczba kroków i limit kalorii |
| Logowanie na innym urządzeniu | dziennik i profil pobrane z chmury |
| Obrót ekranu | stan ekranów zachowany, animacja startowa się nie powtarza |
| Zmiana motywu | cała aplikacja w motywie jasnym lub ciemnym |
 
> _[Miejsce na zrzuty ekranu: dziennik, wyszukiwarka, skaner, produkt, produkt własny, statystyki, profil, logowanie, motyw ciemny]_
 
---
 
## 7. Podsumowanie punktacji
 
| Kryterium | Punkty |
|---|---|
| Aplikacja wykonana samodzielnie | 1 |
| Czytelny i przyjazny interfejs | 1 |
| Własna grafika (w aplikacji i jako ikona) | 1 |
| Własna animacja | 1 |
| Logowanie do chronionych funkcji | 2 |
| Sensor: krokomierz | 0,5 |
| Sensor: aparat | 0,5 |
| Zdalna baza danych (Cloud Firestore) | 2 |
| Lokalna baza danych (SQLite) | 1 |
| Darmowa usługa zewnętrzna (Open Food Facts) | 2 |
| Oszczędzanie energii | 1 |
| Uruchomienie na urządzeniu mobilnym | 1 |
| **Razem (szacunkowo)** | **14** |
 
---
 
## 8. Możliwe kierunki rozwoju
- powiadomienia przypominające o zapisaniu posiłku,
- zapisywanie zdjęć produktów własnych w chmurze,
- dziennik wody i wagi z wykresem zmian,
- propozycje przekąsek dopasowanych do pozostałych kalorii i makroskładników,
- widżet na ekran główny z liczbą pozostałych kalorii.
---
 
## 9. Źródła
- Dokumentacja Android Developers – Jetpack Compose, Navigation, WorkManager, sensory: https://developer.android.com
- Firebase – Authentication i Cloud Firestore: https://firebase.google.com/docs
- Open Food Facts API: https://openfoodfacts.github.io/openfoodfacts-server/api/ (dane na licencji ODbL)
- Google ML Kit – Code Scanner: https://developers.google.com/ml-kit/vision/barcode-scanning/code-scanner
- M. D. Mifflin i in., „A new predictive equation for resting energy expenditure in healthy individuals”, *American Journal of Clinical Nutrition*, 1990.
 
