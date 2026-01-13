# Analiza Wpływu Czynników Pogodowych i Jakości Powietrza na Ruch Rowerowy w Londynie

## Przegląd Projektu
Kompleksowy projekt inżynierii i analizy danych, którego celem jest analiza historycznych danych wypożyczeń rowerów w Londynie. System integruje dane pogodowe, informacje o zanieczyszczeniu powietrza (PM10) oraz historyczne trendy ruchu, aby zrozumieć czynniki wpływające na mobilność miejską.

Dodatkowo, projekt zawiera moduł **Machine Learning** (Random Forest), który przewiduje zapotrzebowanie na rowery, a wyniki modelu są weryfikowane poprzez zapytania SQL i dashboard Business Intelligence.

## Architektura i Technologie

Projekt oparty jest na architekturze kontenerowej, zapewniającej powtarzalność i izolację środowiska.

* **Docker & Docker Compose:** Orkiestracja kontenerów (Baza danych + Środowisko Python).
* **PostgreSQL:** Relacyjna hurtownia danych.
* **Python (Pandas / SQLAlchemy):** Silnik ETL oraz uczenia maszynowego.
* **Power BI:** Warstwa wizualizacji i raportowania.

### Przepływ Danych
1.  **Data Ingestion:** Skrypt Python łączy się z zewnętrznym repozytorium danych, pobiera surowe zbiory i zapisuje je w lokalnie na dysku.
2.  **Preprocessing & ETL:** Czyszczenie danych, konwersja typów, wyodrębnianie cech czasowych oraz integracja danych o smogu.
3.  **Data Loading:** Załadowanie przetworzonych danych do struktury tabelarycznej w PostgreSQL.
4.  **Analytics & ML:** Trenowanie modelu predykcyjnego i zapis wyników (prognoza vs rzeczywistość) z powrotem do bazy.

## Struktura Bazy Danych

Hurtownia danych składa się z trzech kluczowych tabel analitycznych:

### 1. `fact_bike_rentals`
Główny rejestr zdarzeń zawierający pełną historię wypożyczeń z rozdzielczością godzinową.
* **Zastosowanie:** Analiza trendów, wpływ pogody, korelacje ekologiczne.

### 2. `dim_top_stations`
Tabela wymiarów przechowująca ranking najpopularniejszych punktów startowych w Londynie.
* **Zastosowanie:** Identyfikacja kluczowych węzłów komunikacyjnych.

### 3. `ml_predictions`
Tabela ewaluacyjna modelu Machine Learning.
* **Zastosowanie:** Monitorowanie skuteczności algorytmu i detekcja anomalii.

## Machine Learning
W projekcie zastosowano algorytm **Random Forest Regressor** do predykcji popytu.
* **Cel:** Liczba wypożyczeń rowerów w danej godzinie.
* **Weryfikacja:** Model jest monitorowany pod kątem odchyleń w poszczególnych latach.

## Analityka SQL

W celu weryfikacji hipotez biznesowych i jakości modelu, zaimplementowano trzy zaawansowane widoki analityczne w Power BI oparte na bezpośrednich zapytaniach SQL:

### 1. Wpływ warunków termicznych na średnią liczbę wypożyczeń
Analiza porównawcza średniego wolumenu ruchu w dniach "Ciepłych" (>=15°C) vs "Zimnych" (<15°C) w podziale na lata.
> **Wniosek:** SQL potwierdza silną korelację dodatnią między temperaturą a liczbą wypożyczeń.

### 2. Charakterystyka dobowa ruchu rowerowego
Pełny profil godzinowy pokazujący natężenie ruchu dla każdej godziny doby w ujęciu rocznym.
> **Wniosek:** Wykresy ujawniają klasyczne szczyty komunikacyjne (8:00 i 17:00), charakterystyczne dla ruchu dojazdowego.

### 3. Diagnostyka modelu: Analiza największych odchyleń
Zapytanie wykorzystujące funkcje okna (`ROW_NUMBER() OVER PARTITION`), identyfikujące 3 największe pomyłki modelu dla każdego roku (czerwca).
> **Cel:** Wykrywanie anomalii, np. strajków metra lub wydarzeń masowych, których model pogodowy nie mógł przewidzieć.

## Dashboard Power BI

Raport analityczny w Power BI został podzielony na **dwie dedykowane strony**, oddzielające warstwę wizualną od weryfikacyjnej:

### Strona 1: Dashboard Wykresy
Główny widok zawierający 4 kluczowe wykresy analityczne:
1.  **Trendy Czasowe:** Wykres liniowy pokazujący dynamikę wypożyczeń rok do roku.
2.  **Top Stacje:** Wykres słupkowy rankingujący najpopularniejsze lokalizacje startowe.
3.  **Analiza Smogu:** Wykres punktowy (Scatter Plot) badający korelację między stężeniem PM10 a liczbą rowerzystów.
4.  **Skuteczność AI:** Wizualizacja porównawcza (Rzeczywistość vs Prognoza) oceniająca jakość modelu w czasie.

### Strona 2: Dashboard Zapytania SQL
Strona techniczna zawierająca wyniki bezpośrednich zapytań do bazy:
* Tabela wpływu temperatury na średnią liczbę wypożyczeń (Ciepło/Zimno).
* Tabela charakterystyki dobowej (pełny profil godzinowy 0-23).
* Tabela diagnostyki błędów AI (Top 3 odchylenia modelu dla każdego roku).

## Jak uruchomić projekt

**Wymagania:** Docker Desktop, Power BI Desktop.

1.  **Uruchomienie środowiska:**
    ```bash
    docker-compose up --build
    ```
2.  **Analiza:**
    Otwórz plik `BigData-512.pbix` w Power BI i odśwież dane.

*Autor: Bartosz Filipiak oraz Filip Dzięcioł grupa nr. 512*
