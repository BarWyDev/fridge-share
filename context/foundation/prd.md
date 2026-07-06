---
project: "FridgeShare"
version: 1
status: draft
created: 2026-07-06
context_type: greenfield
product_type: web-app
target_scale:
  users: medium
  qps: low
  data_volume: small
timeline_budget:
  mvp_weeks: 3
  hard_deadline: null
  after_hours_only: true
---

# PRD — FridgeShare

## Vision & Problem Statement

Nadmiar jedzenia (przed wyjazdem, po zakupach, po imprezie) nie ma prostej drogi do osób w pobliżu, a współdzielone lodówki i jadłodzielnie działają "w ciemno" — nie widać z zewnątrz, co w nich jest, więc ludzie nie sprawdzają ich regularnie i jedzenie się psuje. Koszt dziś: jedzenie ląduje w koszu, jadłodzielnie stoją niesprawdzane. Ból jest trójwarstwowy: uwięzione dane (zawartość lodówki niewidoczna dla otoczenia — teza wiodąca), koszt koordynacji (okno między "wystawiłem" a "ktoś zajrzał" bywa dłuższe niż życie produktu) i brakująca zdolność (nie istnieje prosty kanał przekazania nadmiaru osobom w pobliżu).

Insight: podgląd zawartości na żywo odwraca rachunek "czy warto zajrzeć" i buduje nawyk sprawdzania; próg dodania produktu musi być bliski zeru (skan QR + zdjęcie + rozpoznanie AI — kilkanaście sekund zamiast ogłoszenia i negocjacji); fizyczna lodówka sama definiuje mikrospołeczność (biuro, klatka, akademik) z wbudowanym zaufaniem i zasięgiem pieszym — czego grupy FB i portale ogłoszeniowe nie mają.

## User & Persona

Persona pierwotna: **pracownik biura** korzystający ze wspólnej lodówki biurowej. Kontekst: znajome osoby, wysokie zaufanie, codzienna obecność przy lodówce. Moment sięgnięcia po produkt: "mam nadmiar i zaraz się zmarnuje" (przed wyjazdem, po zakupach, po imprezie) — po stronie oddającego; "co jest teraz w lodówce?" — po stronie biorącego. Konteksty odroczone (nie-MVP jako persona wiodąca): akademik, kamienica/blok, publiczna jadłodzielnia.

## Success Criteria

### Primary
- ≥ 60% dodanych produktów oznaczonych jako "wzięte" przed upływem terminu przydatności (bezpośrednia miara ograniczenia marnowania).
- ≥ 5 aktywnych użytkowników na lodówkę (żywotność mikrospołeczności).

### Secondary
- Adopcja: 10–20 aktywnych lodówek w pierwszych 2 miesiącach pilotażu.
- Retencja: ≥ 30% użytkowników wraca w ciągu 2 tygodni od pierwszego użycia.
- Aktywność: co najmniej kilka dodanych produktów tygodniowo na lodówkę.

### Guardrails
- Od zeskanowania QR do dodania pierwszego produktu < 2 minuty.
- Rozpoznanie AI poprawne w ≥ 80% przypadków, zawsze z możliwością korekty przez użytkownika przed zapisem.
- Zawartość lodówki widoczna wyłącznie dla jej członków (brak publicznego indeksowania).
- Lista zawsze aktualna — oznaczenie "wzięte" i wygaszanie po terminie nie mogą się psuć.

## User Stories

### US-01: Nowy użytkownik dodaje pierwszy produkt przez skan QR

- **Given** naklejony kod QR lodówki i użytkownik bez konta
- **When** skanuje kod, loguje się (e-mail/Google) i dodaje produkt ze zdjęciem
- **Then** produkt jest widoczny dla wszystkich członków lodówki, a całość zajęła < 2 minuty

#### Acceptance Criteria
- Skan QR otwiera widok lodówki bez konieczności instalacji natywnej aplikacji.
- Po zalogowaniu użytkownik staje się członkiem lodówki, którą zeskanował.
- Dodany produkt pojawia się na liście lodówki widocznej dla wszystkich członków.
- Cała ścieżka (skan → pierwszy produkt) mieści się w < 2 minutach.

## Functional Requirements

### Konta i dostęp
- FR-001: Użytkownik może zalogować się e-mailem lub kontem Google. Priority: must-have
  > Socrates: rozważono: "OAuth w webview po skanie QR bywa tarciem uderzającym w guardrail < 2 min". Rezolucja: brak kontrargumentu — FR stoi jak jest.
- FR-002: Zalogowany użytkownik może dołączyć do lodówki, skanując jej kod QR. Priority: must-have
  > Socrates: rozważono: "QR to klucz, który wycieka — zdjęcie kodu wpuszcza obcych". Rezolucja: brak kontrargumentu — FR stoi jak jest.

### Lodówki
- FR-003: Zalogowany użytkownik może utworzyć lodówkę (nazwa + lokalizacja); aplikacja generuje unikalny kod QR do wydruku. Priority: must-have
  > Socrates: rozważono: "druk QR to ukryta zależność od drukarki w dniu zero". Rezolucja: brak kontrargumentu — FR stoi jak jest.
- FR-004: Właściciel lodówki może nią zarządzać (nazwa, kod QR, usunięcie lodówki). Priority: must-have
  > Socrates: rozważono: "osierocona lodówka po odejściu właściciela — brak transferu własności w MVP". Rezolucja: brak kontrargumentu — FR stoi jak jest.

### Produkty
- FR-005: Członek może dodać produkt ręcznie (zdjęcie, nazwa, opis/komentarz, opcjonalna data przydatności). Priority: must-have
  > Socrates: rozważono: "opcjonalna data dziurawi FR-010 — produkt bez daty nigdy nie wygaśnie". Rezolucja: brak kontrargumentu — FR stoi jak jest.
- FR-006: Członek może dodać produkty przez AI (zdjęcie → rozpoznanie + szacunek terminu przydatności → korekta przed zapisem). Priority: must-have
  > Socrates: rozważono: "AI poniżej 80% w realnych warunkach = korekta droższa niż dodanie ręczne; koszt API; podwójny UX w 3 tygodnie". Rezolucja: brak kontrargumentu — FR stoi jak jest.
- FR-007: Członek widzi aktualną zawartość lodówki (zdjęcia, opisy, terminy przydatności). Priority: must-have
  > Socrates: rozważono: "lista bez porządku wg terminu ukrywa pilne produkty na dole". Rezolucja: brak kontrargumentu — FR stoi jak jest.
- FR-008: Członek może oznaczyć produkt jako "wzięte". Priority: must-have
  > Socrates: rozważono: "moment wzięcia jest offline — ludzie biorą i nie oznaczają, lista gnije mimo funkcji". Rezolucja: brak kontrargumentu — FR stoi jak jest.
- FR-009: Członek może usunąć własny wpis. Priority: must-have
  > Socrates: rozważono: "cudzych porzuconych wpisów nikt nie sprzątnie poza wygaszaniem". Rezolucja: brak kontrargumentu — FR stoi jak jest.
- FR-010: System automatycznie oznacza produkty po terminie przydatności jako przeterminowane, a po kilku dniach archiwizuje wpis. Priority: must-have
  > Socrates: rozważono: "'oznaczane LUB archiwizowane' nierozstrzygnięte; błędna data AI może wygasić dobre jedzenie". Rezolucja: alternatywa rozstrzygnięta w fazie 5 — oznacz, potem archiwizuj (widoczny sygnał "sprzątnij fizyczną lodówkę", potem czysta lista).

## Non-Functional Requirements

- Nowy użytkownik przechodzi od zeskanowania kodu QR do dodania pierwszego produktu w mniej niż 2 minuty.
- Pełna funkcjonalność dostępna w aktualnych wersjach głównych przeglądarek mobilnych, bez instalacji ze sklepu z aplikacjami.
- Rozpoznanie produktu poprawne bez konieczności ręcznej korekty nazwy w ≥ 80% przypadków (mierzalne z odsetka korekt przed zapisem).
- Zawartość lodówki nigdy nie jest dostępna publicznie ani indeksowalna — widoczna wyłącznie dla jej członków (zobowiązanie binarne).

## Business Logic

Aplikacja sama szacuje termin przydatności produktu i wygasza przeterminowane wpisy.

Reguła konsumuje wejścia od użytkownika: zdjęcie produktów oraz opcjonalny opis i datę przydatności, z korektą proponowanych wartości przed zapisem. Jej wyjściem są pozycje lodówki z oszacowanym terminem przydatności oraz cykl życia wpisu: świeży → przeterminowany (wyraźnie oznaczony na liście) → zarchiwizowany po kilku dniach.

Użytkownik spotyka regułę w dwóch miejscach przepływu: przy dodawaniu produktu (propozycja nazwy i terminu do korekty przed zapisem) oraz w widoku lodówki (status świeżości każdej pozycji; przeterminowane wpisy działają jako sygnał do sprzątnięcia fizycznej lodówki, a po kilku dniach znikają do archiwum, utrzymując listę czystą).

## Access Control

Logowanie kontem e-mail lub Google — konto wiąże produkty z osobami i lodówkami. Skan kodu QR otwiera widok lodówki; każda akcja (dodanie produktu, oznaczenie "wzięte") wymaga zalogowania. Zeskanowanie kodu przez zalogowanego użytkownika dołącza go do lodówki.

Model ról: płaski z jedną nadrolą.
- **Członek lodówki** — widzi zawartość, dodaje produkty, oznacza produkty jako wzięte, usuwa własne wpisy.
- **Właściciel lodówki** (jej twórca) — dodatkowo zarządza lodówką: nazwa, kod QR, usunięcie lodówki.

## Non-Goals

Funkcjonalne (czego MVP nie robi):
- Powiadomienia push — wartość dowodzi żywy widok zawartości, nie powiadomienia.
- Mapa publicznych lodówek i wyszukiwanie w okolicy — pilotaż to zamknięte mikrospołeczności; publiczne odkrywanie później.
- System reputacji, ocen i komentarzy — zaufanie w pilocie zapewnia sama mikrospołeczność biurowa.
- Czat i rezerwowanie produktów — koordynację załatwia aktualna lista; kto pierwszy, ten lepszy.
- Moderacja treści i zgłaszanie nadużyć (poza usuwaniem własnych wpisów) — zbędna przy znajomych użytkownikach pilotażu.
- Statystyki i grywalizacja ("uratowałeś 5 kg jedzenia") — metryki sukcesu mierzone operacyjnie, nie w produkcie.
- Obsługa alergenów i filtrowanie diet — opis/komentarz produktu wystarczy w MVP.
- Integracje z bankami żywności i organizacjami charytatywnymi — poza zasięgiem pilotażu.
- Aplikacje natywne na oba systemy — wejście bez instalacji jest NFR-em; ścieżka przez sklep łamie próg < 2 min.
- Budowa własnego modelu rozpoznawania produktów — decyzja "kup, nie buduj": korzystamy z gotowego modelu wizyjnego.
- Zarządzanie członkostwem i transfer własności lodówki — QR jest jedynym mechanizmem członkostwa; ryzyko "osieroconej lodówki" przyjęte świadomie.

Niefunkcjonalne (w co celowo nie celujemy): brak dodatkowych zobowiązań jakościowych ponad zapisane NFR-y.

## Open Questions

Brak otwartych pytań. Cross-check jakości w shape-notes zakończył się statusem `accepted` — wszystkie szare strefy (12 tematów) rozstrzygnięte w trakcie sesji shapingu.
