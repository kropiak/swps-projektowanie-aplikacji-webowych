# Projektowanie aplikacji webowych, semestr 2024Z

## 1. Cel i zakres przedmiotu

Celem przedmiotu jest przybliżenie osobie studenckiej zagadnień związanych z projektowaniem aplikacji WWW z wykorzystaniem języka Python oraz frameworka Django. W trakcie zajęć główny nacisk zostanie położony na backend (czyli część aplikacji związana z implementacją funkcjonalności po stronie serwera), ale zajmiemy się też budową prostych widoków, czyli frontendem.

W trakcie zajęć osoba studencka pozna m.in. zagadnienia takie jak:
* konfiguracja bazy danych na potrzeby aplikacji
* obsługa narzędzia Git
* tworzenie i zarządzanie modelami we frameworku Django
* migracja bazy i rozwiązywanie problemów z migracjami
* implementacja logiki biznesowej (wymagań klienta) w projekcie
* tworzenie API REST-owego na potrzeby różnych technologii frontendowych
* widoki i szablony Django z wykorzystaniem modeli i formularzy
* obsługa i implementacja autentykacji i uwierzytelniania z użyciem Django
* poznanie narzędzi zarządzania projektem Django oraz panelu administratora i jego możliwości


## 2. Oprogramowanie

W trakcie zajęć do pracy niezbędne będzie posiadanie:
* zainstalowanego interpretera **Pythona** w wersji 3.11 lub nowszej
* **framework Django** w wersji 4.2.*
* narzędzie IDE, preferowane **PyCharm Professional** (licencja studencka) lub wersja Community. Może to być również inne oprogramowanie ze wsparciem dla języka Python.
* narzędzie Git do zarządzania kodem projektu
* możliwe, że w zależności od konfiguracji projektu niezbędne będzie zainstalowanie i konfiguracja odpowiedniego serwera bazy danych

## 3. Warunki zaliczenia przedmiotu.

- Efektem finalnym pracy na zajęciach będzie **projekt API** stworzony w Django Rest Framework lub po uzgodnieniu w innym frameworku,
- osoby studenckie mogą dobrać się w pary lub pracować samodzielnie (w wyjątkowych przypadkach dozwolona jest praca w 3-osobowej grupie),
- Projekt osoby studenckiej będzie przechowywany w **publicznym repozytorium GitHub**, do którego prowadzący będzie miał wgląd przez cały okres trwania zajęć,
   - W przypadku pracy grupowej, obie osoby muszą być dodane jako collaborator do repozytorium,
- Oceniane będą:
   - Systematyczność commitów (oraz ich [sens](https://medium.com/@steveamaza/how-to-write-a-proper-git-commit-message-e028865e5791)),
   - Jakość kodu programistycznego,
   - Implementacja zagadnień przedstawionych na zajęciach,
   - Poziom skomplikowania projektu (np. ilość modeli, działanie endpointów, itp.)
- Proponowany temat API (co te API będzie robić) jest dowolny i będzie przedstawiony prowadzącemu na drugich zajęciach.
- Prowadzący rezerwuje możliwość przeprowadzenia kolokwium w przypadku braku pracy na zajęciach przez grupę osób studenckich.

## 4. Wymagania projektu.

Zakres projektu zaliczeniowego:
1.	**Modele**:
* 4-5 adekwatnych do projektu modeli
2.	**Uwierzytelnianie (preferowany token) i autoryzacja.**
* Co najmniej 2 konta z różnym poziomem dostępu do endpointów (np. admin, user)
3.	**Endpointy:**
* Rejestracja użytkownika aplikacji
* CRUD
* Minimum 2 endpointy, które wyjdą poza schemat CRUD, np. zestawienie miesięczne zamówień, lista wypożyczeń dla użytkownika, lista towarów zaczynających się od itp.

## Kontakt do prowadzących
- Krzysztof Ropiak ([kropiak@swps.edu.pl](mailto:kropiak@swps.edu.pl))
- Adrian Albrecht ([aalbrecht@swps.edu.pl](mailto:aalbrecht@swps.edu.pl))