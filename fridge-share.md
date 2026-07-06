# Aplikacja dzielenia — FridgeShare (MVP)

FridgeShare to aplikacja społecznościowa do dzielenia się jedzeniem za pomocą współdzielonych lodówek. Użytkownik rejestruje lodówkę (np. w biurze, akademiku, kamienicy, jadłodzielni), a aplikacja generuje dla niej unikalny kod QR. Każdy, kto zeskanuje kod, może dodać produkt, którym chce się podzielić — ręcznie (zdjęcie + opis + komentarz) lub automatycznie, robiąc zdjęcie, na podstawie którego AI rozpoznaje produkty i szacuje termin przydatności. Wszyscy użytkownicy powiązani z daną lodówką widzą jej aktualną zawartość i mogą korzystać z dostępnych produktów.

## Główny problem

W Polsce i na świecie marnują się ogromne ilości jedzenia — często dlatego, że ktoś ma nadmiar produktów (przed wyjazdem, po zakupach, po imprezie), a nie ma prostego sposobu, by przekazać je osobom w pobliżu. Istniejące jadłodzielnie działają "w ciemno": nie wiadomo, co aktualnie się w nich znajduje, więc ludzie nie sprawdzają ich regularnie, a jedzenie się psuje. Brakuje cyfrowej warstwy, która pokazuje zawartość lodówki w czasie rzeczywistym i obniża próg wejścia do dzielenia się jedzeniem.

### Najmniejszy zestaw funkcjonalności:

- **Rejestracja lodówki** — utworzenie lodówki z nazwą i lokalizacją oraz automatyczne wygenerowanie unikalnego kodu QR do wydruku/naklejenia.
- **Dołączanie przez skan QR** — zeskanowanie kodu telefonem dodaje użytkownika do lodówki i otwiera jej widok.
- **Dodawanie produktu ręcznie** — zdjęcie produktu, nazwa, opis/komentarz (np. "otwarte wczoraj"), opcjonalna data przydatności.
- **Dodawanie produktu przez AI** — zdjęcie produktów, rozpoznanie ich przez AI, oszacowanie terminu przydatności i automatyczne dodanie pozycji do lodówki (z możliwością korekty przez użytkownika przed zapisem).
- **Widok zawartości lodówki** — lista aktualnych produktów ze zdjęciami, opisami i terminami przydatności, widoczna dla wszystkich użytkowników danej lodówki.
- **Oznaczenie "wzięte"** — użytkownik zabierający produkt oznacza go jako pobrany, dzięki czemu lista pozostaje aktualna.
- **Automatyczne wygaszanie** — produkty po terminie przydatności są wyraźnie oznaczane lub archiwizowane.
- **Proste konto użytkownika** — logowanie (np. e-mail lub konto Google), aby powiązać produkty z osobami i lodówkami.

### Co NIE wchodzi w zakres MVP

- Powiadomienia push (np. o nowych produktach czy zbliżającym się terminie przydatności).
- Mapa publicznych lodówek i wyszukiwanie lodówek w okolicy.
- System reputacji, ocen i komentarzy między użytkownikami.
- Czat i rezerwowanie produktów.
- Moderacja treści i zgłaszanie nadużyć (poza podstawowym usuwaniem własnych wpisów).
- Statystyki i grywalizacja (np. "uratowałeś 5 kg jedzenia").
- Obsługa alergenów, filtrowanie diet (wege, bezglutenowe itp.).
- Integracje z bankami żywności i organizacjami charytatywnymi.
- Aplikacje natywne na oba systemy — na start wystarczy PWA lub jedna platforma.

### Kryteria sukcesu

- **Adopcja lodówek:** co najmniej 10–20 aktywnych lodówek zarejestrowanych w pierwszych 2 miesiącach pilotażu.
- **Aktywność użytkowników:** minimum 5 aktywnych użytkowników na lodówkę i co najmniej kilka dodanych produktów tygodniowo na lodówkę.
- **Skuteczność dzielenia:** ≥ 60% dodanych produktów oznaczonych jako "wzięte" przed upływem terminu przydatności (to bezpośrednia miara ograniczenia marnowania).
- **Jakość AI:** rozpoznanie produktu poprawne w ≥ 80% przypadków bez konieczności ręcznej korekty nazwy.
- **Retencja:** ≥ 30% użytkowników wraca do aplikacji w ciągu 2 tygodni od pierwszego użycia.
- **Prostota wejścia:** od zeskanowania QR do dodania pierwszego produktu w mniej niż 2 minuty.