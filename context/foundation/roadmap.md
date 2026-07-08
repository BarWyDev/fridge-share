---
project: "FridgeShare"
version: 1
status: draft
created: 2026-07-07
updated: 2026-07-07
prd_version: 1
main_goal: market-feedback
top_blocker: time
---

# Roadmap: FridgeShare

> Derived from `context/foundation/prd.md` (v1) + auto-researched codebase baseline.
> Edit-in-place; archive when superseded.
> Slices below are listed in dependency order. The "At a glance" table is the index.

## Vision recap

Nadmiar jedzenia nie ma prostej drogi do osób w pobliżu, a współdzielone lodówki działają "w ciemno" — nie widać z zewnątrz, co w nich jest, więc ludzie ich nie sprawdzają i jedzenie się psuje. FridgeShare odwraca ten rachunek żywym podglądem zawartości: skan QR + zdjęcie + rozpoznanie AI sprowadzają próg dodania produktu do kilkunastu sekund, a fizyczna lodówka (biuro w pilotażu) sama definiuje zaufaną mikrospołeczność. Sukces mierzony operacyjnie: ≥ 60% produktów wziętych przed terminem i ≥ 5 aktywnych osób na lodówkę.

## North star

**S-02: Nowy użytkownik skanuje QR, loguje się, dodaje pierwszy produkt i widzi go na liście lodówki — w mniej niż 2 minuty** — to plaster, który najszybciej testuje główną hipotezę produktu (próg wejścia bliski zeru + żywy podgląd budują nawyk sprawdzania), zgodnie z celem sekwencjonowania: sygnał z pilotażu.

> "Gwiazda przewodnia" (north star) oznacza tutaj: najmniejszy przepływ od początku do końca, którego udane dostarczenie dowodzi głównej hipotezy produktu — umieszczony tak wcześnie, jak pozwalają wymagania wstępne, bo wszystko inne ma znaczenie tylko wtedy, gdy ten przepływ działa.

## At a glance

| ID   | Change ID             | Outcome (user can …)                                                              | Prerequisites | PRD refs                       | Status   |
| ---- | --------------------- | --------------------------------------------------------------------------------- | ------------- | ------------------------------ | -------- |
| F-01 | pilot-deploy-pipeline | (foundation) każdy merge do main ląduje automatycznie na publicznym URL produkcji | —             | §NFR (przeglądarka mobilna, bez instalacji) | ready    |
| S-01 | create-fridge-qr      | użytkownik może utworzyć lodówkę (nazwa + lokalizacja) i wydrukować jej unikalny kod QR | —        | FR-003                         | ready    |
| S-02 | qr-join-first-product | nowy użytkownik może zeskanować QR, zalogować się, dołączyć i dodać pierwszy produkt widoczny dla członków | S-01, F-01 | US-01, FR-002, FR-005, FR-007 | proposed |
| S-03 | ai-photo-recognition  | członek może dodać produkty ze zdjęcia przez AI (rozpoznanie + szacunek terminu + korekta) | S-02     | FR-006                         | blocked  |
| S-04 | take-and-cleanup      | członek może oznaczyć produkt jako "wzięte" i usunąć własny wpis                   | S-02          | FR-008, FR-009                 | proposed |
| S-05 | expiry-lifecycle      | użytkownik widzi produkty po terminie wyraźnie oznaczone, a po kilku dniach zarchiwizowane automatycznie | S-02 | FR-010                    | proposed |
| S-06 | fridge-owner-admin    | właściciel może zarządzać lodówką (nazwa, kod QR, usunięcie)                       | S-01          | FR-004                         | proposed |
| S-07 | google-sign-in        | użytkownik może zalogować się kontem Google                                        | —             | FR-001                         | ready    |

## Streams

Navigation aid — groups items that share a Prerequisites chain. Canonical ordering still lives in the dependency graph below; this table is the proposed reading order across parallel tracks.

| Stream | Theme                  | Chain                                        | Note                                                                          |
| ------ | ---------------------- | -------------------------------------------- | ----------------------------------------------------------------------------- |
| A      | Pętla podstawowa       | `F-01` → `S-01` → `S-02` → `S-04` → `S-05`   | Ścieżka do sygnału z pilotażu: gwiazda przewodnia + miara "wzięte przed terminem". |
| B      | Rozpoznawanie AI       | `S-03`                                       | Dołącza do strumienia A po `S-02`; zablokowany do czasu wyboru modelu wizyjnego. |
| C      | Administracja lodówką  | `S-06`                                       | Dołącza do strumienia A po `S-01`; równoległy z całą resztą.                   |
| D      | Logowanie Google       | `S-07`                                       | Samodzielny — bez wymagań wstępnych; do zrównoleglenia w dowolnym momencie.    |

## Baseline

What's already in place in the codebase as of `2026-07-07` (auto-researched + user-confirmed).
Foundations below assume these are present and do NOT re-scaffold them.

- **Frontend:** present — scaffold Astro 6 + React 19 + Tailwind 4 + shadcn/ui (`src/pages/`, `src/components/`), per tech-stack.md.
- **Backend / API:** partial — trasy API wpięte tylko dla auth (`src/pages/api/auth/{signin,signup,signout}.ts`); zero endpointów domenowych.
- **Data:** partial — klient Supabase i lokalna konfiguracja (`src/lib/supabase.ts`, `supabase/config.toml`), ale zero migracji / brak schematu domenowego.
- **Auth:** partial — logowanie e-mail/hasło działa end-to-end (`src/middleware.ts`, `src/pages/auth/*`); logowanie Google (FR-001) niewpięte.
- **Deploy / infra:** partial — `wrangler.jsonc` + CI lint/build (`.github/workflows/ci.yml`); brak auto-deployu po merge, mimo że tech-stack.md go deklaruje.
- **Observability:** absent — brak bibliotek logowania / śledzenia błędów / metryk; miary pilotażu liczone operacyjnie z danych (zgodnie z §Non-Goals PRD), więc świadomie bez fundamentu.

## Foundations

### F-01: Ścieżka produkcyjna pilotażu (auto-deploy po merge)

- **Outcome:** (foundation) każdy merge do main ląduje automatycznie na publicznym, stabilnym URL produkcji; aplikacja dostępna z przeglądarki mobilnej bez instalacji.
- **Change ID:** pilot-deploy-pipeline
- **PRD refs:** §Wymagania niefunkcjonalne ("pełna funkcjonalność w aktualnych przeglądarkach mobilnych, bez instalacji ze sklepu"), §Kryteria sukcesu — guardrail "< 2 minuty od skanu do pierwszego produktu"
- **Unlocks:** S-01 (drukowane kody QR muszą zawierać stabilny publiczny URL), S-02 (weryfikacja progu < 2 min na prawdziwym telefonie wymaga produkcji) oraz ciągły sygnał z pilotażu przy celu `market-feedback`.
- **Prerequisites:** —
- **Parallel with:** S-01, S-07
- **Blockers:** —
- **Unknowns:**
  - Subdomena domyślna platformy czy własna domena na pilotaż? Drukowane naklejki QR utrwalają URL — późniejsza zmiana oznacza przedruk. — Owner: user. Block: no.
- **Risk:** Bez publicznego URL nie da się wydrukować działających kodów QR ani zmierzyć progu < 2 min — to najmniejszy krok odblokowujący sygnał z pilotażu; CI z lint/build już istnieje, więc zakres to wyłącznie brakujący krok wdrożenia.
- **Status:** ready

## Slices

### S-01: Utworzenie lodówki z kodem QR do wydruku

- **Outcome:** użytkownik może utworzyć lodówkę (nazwa + lokalizacja), zostaje jej właścicielem i członkiem, a aplikacja generuje unikalny kod QR do wydruku.
- **Change ID:** create-fridge-qr
- **PRD refs:** FR-003, §Access Control (rola właściciela = twórca)
- **Prerequisites:** —
- **Parallel with:** F-01, S-07
- **Blockers:** —
- **Unknowns:** —
- **Risk:** Pierwszy schemat domenowy (lodówka, członkostwo) z widocznością wyłącznie dla członków — tu powstaje wzorzec kontroli dostępu (obszar głębokiej inwestycji), a błąd w nim propaguje się na wszystkie kolejne plastry.
- **Status:** ready

### S-02: Skan QR → dołączenie → pierwszy produkt (gwiazda przewodnia)

- **Outcome:** nowy użytkownik może zeskanować kod QR, zalogować się, automatycznie dołączyć do lodówki, dodać produkt ręcznie (zdjęcie, nazwa, opis, opcjonalna data przydatności) i zobaczyć go na liście widocznej tylko dla członków — całość w < 2 minuty.
- **Change ID:** qr-join-first-product
- **PRD refs:** US-01, FR-002, FR-005, FR-007, §NFR (< 2 min; widoczność wyłącznie dla członków — zobowiązanie binarne)
- **Prerequisites:** S-01, F-01
- **Parallel with:** S-06, S-07
- **Blockers:** —
- **Unknowns:** —
- **Risk:** Najszerszy plaster roadmapy (cała jedyna historyjka PRD); jeśli `/10x-plan` uzna go za zbyt szeroki, naturalna linia podziału to "dołączenie + podgląd" vs "dodanie produktu" — nie dziel go po warstwach technicznych.
- **Status:** proposed

### S-03: Dodawanie produktów przez rozpoznanie AI

- **Outcome:** członek może dodać produkty ze zdjęcia — aplikacja rozpoznaje produkt i szacuje termin przydatności, a użytkownik koryguje propozycje przed zapisem.
- **Change ID:** ai-photo-recognition
- **PRD refs:** FR-006, §NFR (rozpoznanie poprawne w ≥ 80% przypadków bez korekty nazwy), §Business Logic (propozycja nazwy i terminu do korekty)
- **Prerequisites:** S-02
- **Parallel with:** S-04, S-05, S-06, S-07
- **Blockers:** —
- **Unknowns:**
  - Który gotowy model wizyjny (decyzja "kup, nie buduj" z §Non-Goals) osiąga ≥ 80% trafności na realnych zdjęciach produktów i jakim kosztem na wpis? — Owner: user. Block: yes.
- **Risk:** To zakład o próg wejścia bliski zeru — jeśli model nie osiąga ≥ 80%, korekta staje się droższa niż dodanie ręczne (ryzyko nazwane wprost w PRD przy FR-006); dlatego wybór i szybki test modelu musi poprzedzić planowanie tego plastra.
- **Status:** blocked

### S-04: Oznaczenie "wzięte" i sprzątanie własnych wpisów

- **Outcome:** członek może oznaczyć produkt jako "wzięte" oraz usunąć własny wpis, utrzymując listę aktualną.
- **Change ID:** take-and-cleanup
- **PRD refs:** FR-008, FR-009, §Kryteria sukcesu — guardrail "lista zawsze aktualna"
- **Prerequisites:** S-02
- **Parallel with:** S-03, S-05, S-06, S-07
- **Blockers:** —
- **Unknowns:** —
- **Risk:** Oznaczenie "wzięte" zasila główną miarę sukcesu (≥ 60% wziętych przed terminem), a moment wzięcia jest offline — PRD odnotowuje, że ludzie biorą i nie oznaczają; interakcja musi być maksymalnie jednokrokowa, inaczej lista gnije mimo funkcji.
- **Status:** proposed

### S-05: Cykl życia wpisu — wygaszanie i archiwizacja

- **Outcome:** użytkownik widzi produkty po terminie wyraźnie oznaczone jako przeterminowane (sygnał "sprzątnij fizyczną lodówkę"), a po kilku dniach wpisy znikają automatycznie do archiwum.
- **Change ID:** expiry-lifecycle
- **PRD refs:** FR-010, §Business Logic (cykl: świeży → przeterminowany → zarchiwizowany), §Kryteria sukcesu — guardrail "wygaszanie po terminie nie może się psuć"
- **Prerequisites:** S-02
- **Parallel with:** S-03, S-04, S-06, S-07
- **Blockers:** —
- **Unknowns:**
  - Ile dokładnie dni od terminu do archiwizacji ("po kilku dniach" w FR-010)? — Owner: user. Block: no.
- **Risk:** Produkty bez daty (opcjonalnej w FR-005 — luka nazwana w PRD) nigdy nie wygasną i plaster musi jawnie obsłużyć ten przypadek; to też pierwsze zadanie cykliczne w tle w projekcie (flaga background-jobs z tech-stack.md), wprowadzane dopiero tu, bo dopiero tu jest potrzebne.
- **Status:** proposed

### S-06: Zarządzanie lodówką przez właściciela

- **Outcome:** właściciel lodówki może zmienić jej nazwę, ponownie pobrać/wygenerować kod QR i usunąć lodówkę.
- **Change ID:** fridge-owner-admin
- **PRD refs:** FR-004, §Access Control (nadrola właściciela)
- **Prerequisites:** S-01
- **Parallel with:** S-02, S-03, S-04, S-05, S-07
- **Blockers:** —
- **Unknowns:** —
- **Risk:** Niskie ryzyko funkcjonalne, ale usunięcie lodówki to jedyna operacja destrukcyjna MVP — plaster musi zdefiniować los wpisów i członkostw; ryzyko "osieroconej lodówki" pozostaje świadomie przyjęte (§Non-Goals: brak transferu własności).
- **Status:** proposed

### S-07: Logowanie kontem Google

- **Outcome:** użytkownik może zalogować się kontem Google (obok istniejącego logowania e-mail), obniżając tarcie na ścieżce < 2 minut.
- **Change ID:** google-sign-in
- **PRD refs:** FR-001, US-01, §NFR (< 2 min)
- **Prerequisites:** —
- **Parallel with:** F-01, S-01, S-02, S-03, S-04, S-05, S-06
- **Blockers:** —
- **Unknowns:** —
- **Risk:** Konfiguracja zgody OAuth Google to praca poza kodem (konsola dostawcy); PRD odnotowuje przy FR-001 tarcie OAuth w przeglądarce otwartej ze skanera QR — do zweryfikowania na prawdziwym telefonie przed pilotażem.
- **Status:** ready

## Backlog Handoff

| Roadmap ID | Change ID             | Suggested issue title                                      | Ready for `/10x-plan` | Notes                                           |
| ---------- | --------------------- | ---------------------------------------------------------- | --------------------- | ----------------------------------------------- |
| F-01       | pilot-deploy-pipeline | Auto-deploy na produkcję po merge do main                   | yes                   | Run `/10x-plan pilot-deploy-pipeline`           |
| S-01       | create-fridge-qr      | Utworzenie lodówki z kodem QR do wydruku                    | yes                   | Run `/10x-plan create-fridge-qr`                |
| S-02       | qr-join-first-product | Skan QR → dołączenie → pierwszy produkt (< 2 min)           | no                    | Czeka na S-01 i F-01                            |
| S-03       | ai-photo-recognition  | Dodawanie produktów przez rozpoznanie AI ze zdjęcia         | no                    | Zablokowany: wybór modelu wizyjnego (≥ 80%)     |
| S-04       | take-and-cleanup      | Oznaczenie "wzięte" i usuwanie własnych wpisów              | no                    | Czeka na S-02                                   |
| S-05       | expiry-lifecycle      | Wygaszanie przeterminowanych i archiwizacja wpisów          | no                    | Czeka na S-02                                   |
| S-06       | fridge-owner-admin    | Panel właściciela: nazwa, kod QR, usunięcie lodówki         | no                    | Czeka na S-01                                   |
| S-07       | google-sign-in        | Logowanie kontem Google                                     | yes                   | Run `/10x-plan google-sign-in`                  |

## Open Roadmap Questions

Brak pytań przekrojowych. PRD zamknięto z zerem otwartych pytań (cross-check jakości w shape-notes: `accepted`). Pytania dotyczące pojedynczych plastrów pozostają przy nich: wybór modelu wizyjnego (S-03, blokujące), liczba dni do archiwizacji (S-05), domena pilotażu (F-01).

## Parked

- **Powiadomienia push** — Why parked: §Non-Goals PRD; wartość dowodzi żywy widok zawartości, nie powiadomienia.
- **Mapa publicznych lodówek i wyszukiwanie w okolicy** — Why parked: §Non-Goals PRD; pilotaż to zamknięte mikrospołeczności.
- **System reputacji, ocen i komentarzy** — Why parked: §Non-Goals PRD; zaufanie zapewnia mikrospołeczność biurowa.
- **Czat i rezerwowanie produktów** — Why parked: §Non-Goals PRD; koordynację załatwia aktualna lista, kto pierwszy ten lepszy.
- **Moderacja treści i zgłaszanie nadużyć** — Why parked: §Non-Goals PRD; zbędna przy znajomych użytkownikach pilotażu.
- **Statystyki i grywalizacja** — Why parked: §Non-Goals PRD; metryki sukcesu mierzone operacyjnie, nie w produkcie.
- **Obsługa alergenów i filtrowanie diet** — Why parked: §Non-Goals PRD; opis/komentarz produktu wystarczy w MVP.
- **Integracje z bankami żywności i organizacjami charytatywnymi** — Why parked: §Non-Goals PRD; poza zasięgiem pilotażu.
- **Aplikacje natywne (iOS/Android)** — Why parked: §Non-Goals PRD; wejście bez instalacji jest NFR-em, sklep łamie próg < 2 min.
- **Własny model rozpoznawania produktów** — Why parked: §Non-Goals PRD; decyzja "kup, nie buduj" — gotowy model wizyjny.
- **Zarządzanie członkostwem i transfer własności lodówki** — Why parked: §Non-Goals PRD; QR jest jedynym mechanizmem członkostwa, ryzyko przyjęte świadomie.

## Done

(Empty on first generation. `/10x-archive` appends an entry here — and flips that item's `Status` to `done` — when a change whose `Change ID` matches the item is archived. Do NOT pre-populate.)
