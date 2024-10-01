# Projektowanie aplikacji webowych, semestr 2024Z

## Lab 1
---


# 1. Warto zacząć od...

1.1 Zanim rozpoczniesz przygotowanie swojego pierwszego projektu z użyciem Django 4.1 rzuć okiem na release notes dla tej wersji: https://docs.djangoproject.com/pl/4.1/releases/4.1/

1.2 Jeżeli Twoja wiedza na temat działania protokołu HTTP wymaga odświeżenia to polecam dobry artykuł na ten temat: https://www.samouczekprogramisty.pl/protokol-http/

1.3 Zachęcam również do zaglądnięcia do kilku ciekawych tutoriali (poza oficjalnym oczywiście) wprowadzających do tworzenia aplikacji z użyciem Django:
* https://realpython.com/get-started-with-django-1/ (i więcej kursów z tej strony w tematyce Django: https://realpython.com/tutorials/django/ )
* https://www.w3schools.com/django/
* https://tutorial.djangogirls.org/pl/

# 2. Teoria Tworzenia Aplikacji Webowych

Aplikacje webowe to programy, które działają na serwerze i są dostępne dla użytkowników przez przeglądarki internetowe. Ich celem jest dostarczanie interaktywnych i funkcjonalnych rozwiązań, które użytkownicy mogą łatwo wykorzystać. 

### Kluczowe komponenty aplikacji webowej:

- **Frontend**: Jest to część aplikacji, z którą użytkownik wchodzi w interakcję. Frontend jest zazwyczaj zbudowany przy użyciu HTML, CSS i JavaScript. Popularne frameworki do budowy frontendów to React, Angular czy Vue.js.

- **Backend**: Jest to część aplikacji, która obsługuje logikę biznesową, zarządza bazą danych i przetwarza zapytania. Backend jest zazwyczaj zbudowany w takich językach jak Python (Django, Flask), Ruby (Ruby on Rails), Java (Spring) czy JavaScript (Node.js).

- **Baza Danych**: Używana do przechowywania danych aplikacji. Może to być relacyjna baza danych (np. MySQL, PostgreSQL) lub nierelacyjna baza danych (np. MongoDB).

## 2. Aplikacje typu REST

REST (Representational State Transfer) to architektura, która określa zasady i konwencje dotyczące komunikacji między klientem a serwerem. Aplikacje typu REST wykorzystują protokół HTTP do wymiany danych.

### Kluczowe zasady aplikacji REST:

- **Zasoby**: W aplikacji REST, zasoby są reprezentowane przez unikalne identyfikatory URI (Uniform Resource Identifier). Przykład: `https://api.example.com/users`.

- **Metody HTTP**: Aplikacje REST używają standardowych metod HTTP do wykonywania operacji na zasobach:
  - **GET**: Pobiera dane z serwera.
  - **POST**: Tworzy nowy zasób.
  - **PUT**: Aktualizuje istniejący zasób.
  - **DELETE**: Usuwa zasób.

- **Stateless**: Każde zapytanie od klienta do serwera powinno być niezależne i zawierać wszystkie informacje potrzebne do jego przetworzenia. Serwer nie powinien przechowywać żadnych informacji o stanie klienta.

- **Format danych**: Dane przesyłane między klientem a serwerem są zazwyczaj w formacie JSON (JavaScript Object Notation) lub XML.


# **Zadania**

1. Jeżeli korzystasz na zajęciach ze swojego sprzętu to zainstaluj niezbędne oprogramowanie.
2. Stwórz środowisko wirtualne z interpreterem python na potrzeby swojego projektu.
3. Zainstaluj niezbędne paczki (aktualnie pakiet o nazwie `django` powinien wystarczyć). W razie problemów z instalacją rzuć okiem na oficjalną dokumentację pod adresem https://docs.djangoproject.com/pl/4.1/intro/install/
4. Przygotuj swój pierwszy projekt oraz aplikację Django (warto znać różnicę między tymi pojęciami) bazując na oficjalnym tutorialu znajdującym się pod adresem https://docs.djangoproject.com/pl/4.1/intro/tutorial01/. Uruchom wbudowany serwer www (polecenie znajdziesz w tutorialu) i sprawdź czy wszystko działa.
5. Skonfiguruj lokalne i zdalne repozytorium git. Przygotuj odpowiedni plik `.gitignore` dla projektu Django i wykonaj inicjalny commit oraz push do zdalnego repo. Przetestuj czy konfiguracja jest odpowiednia.
6. Zastanów się nad tematem Twojego projektu i schematem bazy danych, który będzie potrzebny do jej działania. Możesz również wybrać na tym etapie silnik bazy danych, na którym będziesz chciał osadzić swoje dane.

## Przydatne instrukcje do zadań

### Zadanie 1: Instalacja Niezbędnego Oprogramowania

1. **Zainstaluj Python**: 
   - Sprawdź, czy Python jest zainstalowany na twoim komputerze. Możesz to zrobić, uruchamiając polecenie w terminalu:
     ```bash
     python --version
     ```
   - Jeśli nie masz Pythona zainstalowanego, pobierz go ze strony [oficjalnej Pythona](https://www.python.org/downloads/) i zainstaluj.

2. **Zainstaluj pip**: `pip` to menedżer pakietów Pythona. W większości instalacji Pythona `pip` jest domyślnie instalowany. Możesz to sprawdzić poleceniem:
   ```bash
   pip --version
   ```


3. **Zainstaluj Django**: Użyj `pip`, aby zainstalować Django:
   ```bash
   pip install django
   ```

## Zadanie 2: Stworzenie Środowiska Wirtualnego

1. **Utwórz folder projektu**:
   - Otwórz terminal i przejdź do folderu, w którym chcesz utworzyć projekt.
   - Utwórz nowy folder:
     ```bash
     mkdir myproject
     cd myproject
     ```

2. **Stwórz środowisko wirtualne**:
   - Uruchom polecenie, aby stworzyć wirtualne środowisko:
     ```bash
     python -m venv venv
     ```
   - Aktywuj środowisko wirtualne:
     - Na Windows:
       ```bash
       venv\\Scripts\\activate
       ```
     - Na macOS/Linux:
       ```bash
       source venv/bin/activate
       ```

## Zadanie 3: Instalacja Niezbędnych Paczek

1. **Zainstaluj Django**:
   - W aktywowanym środowisku wirtualnym, upewnij się, że masz zainstalowany pakiet `django`:
     ```bash
     pip install django
     ```

2. **Rozwiązywanie problemów z instalacją**:
   - W razie problemów z instalacją, sprawdź oficjalną dokumentację [Django](https://docs.djangoproject.com/pl/4.1/intro/install/) w celu uzyskania wskazówek dotyczących instalacji.

## Zadanie 4: Przygotowanie Pierwszego Projektu i Aplikacji Django

1. **Stwórz nowy projekt Django**:
   - W terminalu, upewnij się, że jesteś w katalogu głównym swojego projektu. Następnie użyj polecenia:
     ```bash
     django-admin startproject myproject
     ```
   - Przejdź do folderu projektu:
     ```bash
     cd myproject
     ```

2. **Stwórz aplikację Django**:
   - W folderze projektu utwórz nową aplikację:
     ```bash
     python manage.py startapp myapp
     ```
   - **Rozróżnienie**: Projekt Django to kontener dla aplikacji, podczas gdy aplikacja to konkretna część projektu, która wykonuje określone zadania (np. obsługa użytkowników, bloga itp.).

3. **Uruchom wbudowany serwer**:
   - Aby uruchomić serwer, użyj polecenia:
     ```bash
     python manage.py runserver
     ```
   - Sprawdź, czy aplikacja działa, otwierając przeglądarkę i wpisując adres `http://127.0.0.1:8000/`.

## Zadanie 5: Konfiguracja Repozytorium Git

1. **Zainicjalizuj lokalne repozytorium**:
   - W folderze projektu, zainicjalizuj repozytorium Git:
     ```bash
     git init
     ```

2. **Przygotuj plik `.gitignore`**:
   - Utwórz plik `.gitignore` w katalogu głównym projektu. Dodaj następujące linie, aby zignorować pliki środowiska wirtualnego i inne pliki, które nie powinny być wersjonowane:
     ```
     venv/
     *.pyc
     __pycache__/
     db.sqlite3
     ```

3. **Wykonaj inicjalny commit**:
   - Dodaj wszystkie pliki do repozytorium:
     ```bash
     git add .
     ```
   - Wykonaj commit:
     ```bash
     git commit -m "Initial commit"
     ```

4. **Skonfiguruj zdalne repozytorium**:
   - Utwórz zdalne repozytorium na platformie, takiej jak GitHub, GitLab lub Bitbucket.
   - Po utworzeniu zdalnego repozytorium, połącz je z lokalnym repozytorium:
     ```bash
     git remote add origin https://github.com/username/repo.git
     ```

5. **Wykonaj push do zdalnego repo**:
   - Prześlij zmiany do zdalnego repozytorium:
     ```bash
     git push -u origin master
     ```
   - Sprawdź, czy konfiguracja jest odpowiednia, przeglądając zdalne repozytorium.

## Zadanie 6: Temat Projektu i Schemat Bazy Danych

1. **Zastanów się nad tematem projektu**:
   - Wybierzcie temat, który Was interesuje, np. blog, system rejestracji użytkowników, sklep internetowy itp.

3. **Wybór silnika bazy danych**:
   - Rozważ wybór silnika bazy danych (np. SQLite, PostgreSQL, MySQL) w zależności od wymagań projektu.