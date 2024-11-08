# Projektowanie aplikacji webowych, semestr 2024Z

## Lab 4
---
### **1. Więcej możliwości modeli w ekosystemie Django.**


Oprócz typowego mapowania obiektowo-relacyjnego klasy modeli frameworka Django oferują dużo więcej możliwości, m.in. implementację funkcji pomocniczych, określenie domyślnego porządku sortowania zwracanych krotek, określenie parametrów walidacji i inne. Poniżej zostaną zaprezentowane niektóre z możliwości wraz z przykładami użycia.

Przydatne linki do dokumentacji, które mogą się przydać w trakcie rozwiązywania zadań:
1. Lista dostępnych pól klas modeli: https://docs.djangoproject.com/pl/4.2/ref/models/fields/#model-field-types

W Django modele definiują strukturę tabel w bazie danych, a typy pól (field types) określają, jakie dane mogą być przechowywane w tych tabelach. Poniżej znajduje się lista najpopularniejszych typów pól w Django, wraz z przykładami zastosowań i sposobem ich użycia:

### 1. **CharField**
   Przechowuje tekst o ograniczonej długości.
   - **Przykład**: Imię, nazwisko, tytuł książki.
   - **Użycie**:
     ```python
     from django.db import models

     class Person(models.Model):
         first_name = models.CharField(max_length=50)
         last_name = models.CharField(max_length=50)
     ```

### 2. **TextField**
   Przechowuje długi tekst, bez limitu długości (przydatne dla treści jak opisy, artykuły).
   - **Przykład**: Opis produktu, artykuł na blogu.
   - **Użycie**:
     ```python
     class Article(models.Model):
         title = models.CharField(max_length=100)
         content = models.TextField()
     ```

### 3. **IntegerField**
   Przechowuje liczby całkowite.
   - **Przykład**: Wiek, liczba przedmiotów.
   - **Użycie**:
     ```python
     class Product(models.Model):
         name = models.CharField(max_length=100)
         stock_quantity = models.IntegerField()
     ```

### 4. **FloatField**
   Przechowuje liczby zmiennoprzecinkowe.
   - **Przykład**: Cena produktu, współrzędne geograficzne.
   - **Użycie**:
     ```python
     class Product(models.Model):
         name = models.CharField(max_length=100)
         price = models.FloatField()
     ```

### 5. **BooleanField**
   Przechowuje wartość logiczną (`True` lub `False`).
   - **Przykład**: Czy produkt jest dostępny.
   - **Użycie**:
     ```python
     class Product(models.Model):
         name = models.CharField(max_length=100)
         is_available = models.BooleanField(default=True)
     ```

### 6. **DateField**
   Przechowuje daty.
   - **Przykład**: Data urodzenia, data publikacji.
   - **Użycie**:
     ```python
     class Event(models.Model):
         event_name = models.CharField(max_length=100)
         event_date = models.DateField()
     ```

### 7. **DateTimeField**
   Przechowuje daty i godziny.
   - **Przykład**: Data i czas rejestracji użytkownika, czas utworzenia posta.
   - **Użycie**:
     ```python
     class Post(models.Model):
         title = models.CharField(max_length=100)
         created_at = models.DateTimeField(auto_now_add=True)
     ```

### 8. **EmailField**
   Specjalizacja `CharField` przechowująca adresy email.
   - **Przykład**: Email użytkownika.
   - **Użycie**:
     ```python
     class User(models.Model):
         name = models.CharField(max_length=100)
         email = models.EmailField(unique=True)
     ```

### 9. **URLField**
   Przechowuje adresy URL.
   - **Przykład**: Strona internetowa firmy.
   - **Użycie**:
     ```python
     class Company(models.Model):
         name = models.CharField(max_length=100)
         website = models.URLField()
     ```

### 10. **ForeignKey**
   Tworzy relację jeden-do-wielu (One-to-Many) z inną tabelą.
   - **Przykład**: Posty powiązane z użytkownikiem.
   - **Użycie**:
     ```python
     class User(models.Model):
         name = models.CharField(max_length=100)

     class Post(models.Model):
         title = models.CharField(max_length=100)
         content = models.TextField()
         author = models.ForeignKey(User, on_delete=models.CASCADE)
     ```

### 11. **ManyToManyField**
   Tworzy relację wiele-do-wielu (Many-to-Many) z inną tabelą.
   - **Przykład**: Grupy użytkowników w systemie.
   - **Użycie**:
     ```python
     class Group(models.Model):
         name = models.CharField(max_length=100)

     class User(models.Model):
         name = models.CharField(max_length=100)
         groups = models.ManyToManyField(Group)
     ```

### 12. **OneToOneField**
   Tworzy relację jeden-do-jednego (One-to-One) z inną tabelą.
   - **Przykład**: Profil użytkownika.
   - **Użycie**:
     ```python
     class User(models.Model):
         name = models.CharField(max_length=100)

     class Profile(models.Model):
         user = models.OneToOneField(User, on_delete=models.CASCADE)
         bio = models.TextField()
     ```

### 13. **DecimalField**
   Przechowuje liczby dziesiętne z określoną precyzją (przydatne dla walut, wartości finansowych).
   - **Przykład**: Cena produktu.
   - **Użycie**:
     ```python
     class Product(models.Model):
         name = models.CharField(max_length=100)
         price = models.DecimalField(max_digits=10, decimal_places=2)
     ```

### 14. **SlugField**
   Przechowuje krótkie etykiety (slug), często używane w adresach URL.
   - **Przykład**: Etykieta dla artykułu.
   - **Użycie**:
     ```python
     class Article(models.Model):
         title = models.CharField(max_length=100)
         slug = models.SlugField(unique=True)
     ```

### 15. **FileField**
   Przechowuje ścieżkę do pliku (np. dokumentu lub obrazu).
   - **Przykład**: Załączniki do wiadomości, zdjęcia użytkowników.
   - **Użycie**:
     ```python
     class Document(models.Model):
         title = models.CharField(max_length=100)
         file = models.FileField(upload_to='documents/')
     ```

### 16. **ImageField**
   Specjalizacja `FileField` dla plików graficznych.
   - **Przykład**: Awatar użytkownika.
   - **Użycie**:
     ```python
     class UserProfile(models.Model):
         user = models.OneToOneField(User, on_delete=models.CASCADE)
         avatar = models.ImageField(upload_to='avatars/')
     ```

### 17. **TimeField**
   Przechowuje czas (bez daty).
   - **Przykład**: Czas rozpoczęcia spotkania.
   - **Użycie**:
     ```python
     class Meeting(models.Model):
         title = models.CharField(max_length=100)
         start_time = models.TimeField()
     ```

### 18. **DurationField**
   Przechowuje czas trwania.
   - **Przykład**: Czas trwania filmu, sesji.
   - **Użycie**:
     ```python
     class Movie(models.Model):
         title = models.CharField(max_length=100)
         duration = models.DurationField()
     ```

Każde pole w modelu jest przetwarzane i walidowane automatycznie przez Django ORM, co znacznie upraszcza pracę z danymi. Można dodatkowo używać atrybutów takich jak `null=True`, `blank=True`, `default=...`, aby dostosować sposób przechowywania danych w bazie.

2. Atrybuty modeli: https://docs.djangoproject.com/pl/4.2/topics/db/models/#field-options

Atrybuty modeli w Django definiują różne aspekty zachowania pól i modeli w interakcji z bazą danych oraz walidacją. Oto najczęściej używane atrybuty modeli Django, ich przykłady i zastosowanie:

### 1. **`null`**
   - Określa, czy pole może przechowywać wartość `NULL` w bazie danych.
   - **Typ pól**: Wszystkie pola (oprócz `ManyToManyField`, `OneToOneField` i `ForeignKey`).
   - **Domyślna wartość**: `False` (pole musi mieć wartość).
   - **Przykład**:
     ```python
     class Person(models.Model):
         name = models.CharField(max_length=100, null=True)
     ```

### 2. **`blank`**
   - Określa, czy pole może być puste podczas walidacji formularzy (dotyczy walidacji na poziomie formularzy, a nie bazy danych).
   - **Typ pól**: Wszystkie pola.
   - **Domyślna wartość**: `False` (pole nie może być puste w formularzach).
   - **Przykład**:
     ```python
     class Person(models.Model):
         email = models.EmailField(blank=True)
     ```

#### Jaka jest różnica?

![Tu powinien być obrazek :)](./blank_vs_null.png)


### 3. **`default`**
   - Ustala domyślną wartość dla pola, jeśli użytkownik jej nie poda.
   - **Typ pól**: Wszystkie pola.
   - **Przykład**:
     ```python
     class Product(models.Model):
         price = models.DecimalField(max_digits=10, decimal_places=2, default=0.00)
     ```

### 4. **`unique`**
   - Sprawia, że wartość w danym polu musi być unikalna w całej tabeli.
   - **Typ pól**: Wszystkie pola (oprócz pól relacyjnych jak `ManyToManyField`).
   - **Przykład**:
     ```python
     class User(models.Model):
         username = models.CharField(max_length=50, unique=True)
     ```

### 5. **`choices`**
   - Definiuje listę dostępnych wartości dla pola (jako pary: wyświetlana wartość i wewnętrzna wartość).
   - **Typ pól**: Najczęściej `CharField`, `IntegerField`, itp.
   - **Przykład**:
     ```python
     class Person(models.Model):
         STATUS_CHOICES = [
             ('A', 'Active'),
             ('I', 'Inactive'),
         ]
         status = models.CharField(max_length=1, choices=STATUS_CHOICES, default='A')
     ```

### 6. **`primary_key`**
   - Określa, czy pole jest kluczem głównym (zastępuje automatyczne tworzenie pola `id`).
   - **Typ pól**: Zazwyczaj `IntegerField` lub `CharField`.
   - **Przykład**:
     ```python
     class Person(models.Model):
         ssn = models.CharField(max_length=9, primary_key=True)
     ```

### 7. **`db_index`**
   - Tworzy indeks na kolumnie, co przyspiesza operacje wyszukiwania.
   - **Typ pól**: Wszystkie pola.
   - **Przykład**:
     ```python
     class Product(models.Model):
         sku = models.CharField(max_length=30, db_index=True)
     ```

### 8. **`verbose_name`**
   - Określa bardziej opisową nazwę pola, wyświetlaną w panelu administracyjnym Django.
   - **Typ pól**: Wszystkie pola.
   - **Przykład**:
     ```python
     class Person(models.Model):
         first_name = models.CharField(max_length=50, verbose_name="First Name")
     ```

### 9. **`verbose_name_plural`**
   - Definiuje liczbę mnogą nazwy modelu, wyświetlaną w panelu administracyjnym.
   - **Typ modeli**: Wszystkie modele.
   - **Przykład**:
     ```python
     class Person(models.Model):
         name = models.CharField(max_length=100)

         class Meta:
             verbose_name_plural = "People"
     ```

### 10. **`help_text`**
   - Dodaje pomocniczy tekst wyświetlany w formularzach, opisujący pole.
   - **Typ pól**: Wszystkie pola.
   - **Przykład**:
     ```python
     class Product(models.Model):
         price = models.DecimalField(max_digits=10, decimal_places=2, help_text="Enter price in USD")
     ```

### 11. **`auto_now`**
   - Automatycznie ustawia pole na bieżący czas i datę przy każdej aktualizacji obiektu.
   - **Typ pól**: `DateField`, `DateTimeField`.
   - **Przykład**:
     ```python
     class Post(models.Model):
         updated_at = models.DateTimeField(auto_now=True)
     ```

### 12. **`auto_now_add`**
   - Automatycznie ustawia pole na bieżący czas i datę przy tworzeniu obiektu.
   - **Typ pól**: `DateField`, `DateTimeField`.
   - **Przykład**:
     ```python
     class Post(models.Model):
         created_at = models.DateTimeField(auto_now_add=True)
     ```

### 13. **`on_delete`**
   - Określa zachowanie relacyjnych pól (np. `ForeignKey`) przy usunięciu obiektu, do którego odnosimy się.
   - **Typ pól**: `ForeignKey`, `OneToOneField`.
   - **Przykład**:
     ```python
     class Post(models.Model):
         author = models.ForeignKey(User, on_delete=models.CASCADE)
     ```

   - Najczęściej używane wartości dla `on_delete`:
     - `CASCADE`: Usuwa powiązane obiekty.
     - `SET_NULL`: Ustawia `NULL` (pole musi mieć `null=True`).
     - `PROTECT`: Blokuje usunięcie powiązanego obiektu.
     - `SET_DEFAULT`: Ustawia wartość domyślną (pole musi mieć `default`).
     - `DO_NOTHING`: Nie wykonuje żadnej akcji.

### 14. **`related_name`**
   - Określa nazwę odwrotnej relacji dla pól relacyjnych (`ForeignKey`, `ManyToManyField`, `OneToOneField`).
   - **Typ pól**: `ForeignKey`, `OneToOneField`, `ManyToManyField`.
   - **Przykład**:
     ```python
     class Book(models.Model):
         title = models.CharField(max_length=100)

     class Author(models.Model):
         name = models.CharField(max_length=100)
         books = models.ManyToManyField(Book, related_name='authors')
     ```

### 15. **`unique_together`**
   - Definiuje zestaw pól, które muszą być unikalne razem (np. kombinacja pól).
   - **Typ modeli**: Meta klasy modeli.
   - **Przykład**:
     ```python
     class Order(models.Model):
         customer = models.ForeignKey(User, on_delete=models.CASCADE)
         order_date = models.DateField()

         class Meta:
             unique_together = ('customer', 'order_date')
     ```

### 16. **`ordering`**
   - Określa domyślne sortowanie rekordów w zapytaniach do bazy danych.
   - **Typ modeli**: Meta klasy modeli.
   - **Przykład**:
     ```python
     class Product(models.Model):
         name = models.CharField(max_length=100)
         price = models.DecimalField(max_digits=10, decimal_places=2)

         class Meta:
             ordering = ['price']
     ```

### 17. **`editable`**
   - Określa, czy pole może być edytowane w formularzach Django (np. panelu administracyjnym).
   - **Typ pól**: Wszystkie pola.
   - **Przykład**:
     ```python
     class Product(models.Model):
         sku = models.CharField(max_length=30, editable=False)
     ```

### 18. **`limit_choices_to`**
   - Ogranicza wybory dla pól relacyjnych, np. `ForeignKey` lub `ManyToManyField`, na podstawie filtrów.
   - **Typ pól**: `ForeignKey`, `ManyToManyField`.
   - **Przykład**:
     ```python
     class ActiveManager(models.Manager):
         def get_queryset(self):
             return super().get_queryset().filter(is_active=True)

     class Product(models.Model):
         name = models.CharField(max_length=100)
         manager = models.ForeignKey('self', limit_choices_to={'is_active': True}, on_delete=models.CASCADE)
     ```

Każdy atrybut pomaga dostosować sposób, w jaki pola modeli są przechowywane, walidowane i prezentowane w formularzach lub administracji Django. Kombinacja tych atrybutów pozwala na szczegółowe definiowanie zachowań aplikacji.

#### **1.1 Statyczna lista wartości pola wyboru.** 

Bardzo często w wielu aplikacjach potrzebujemy przechować dane w postaci tabel słownikowych, czyli takich, które są statycznymi skończonymi zbiorami wartości definiowanymi za zwyczaj w momencie wdrażania aplikacji, a potrzebnymi w procesie jej eksploatacji. Takim przykładem może być lista miesięcy do wyboru, nazw województw itp. Często informacje te są przechowywane w bazie danych co ułatwia zarządzanie (nie są rozproszone w wielu miejscach systemu), ale istnieją również możliwości określenia takich list na poziomie modelu Django. Przykład poniżej.

**_Listing 1_**
```python
# kod z oficjalnej dokumentacji Django
class Person(models.Model):
    # lista wartości do wyboru w formie krotek
    SHIRT_SIZES = (
        ('S', 'Small'),
        ('M', 'Medium'),
        ('L', 'Large'),
    )
    name = models.CharField(max_length=60)
    # wskazanie listy poprzez przypisanie do parametru choices
    shirt_size = models.CharField(max_length=1, choices=SHIRT_SIZES)
```

Dodanie klasy do pliku `models.py` a następnie przygotowanie, wykonanie migracji oraz dodanie modelu do panelu administracyjnego pozwoli na przetestowanie działania takiego podejścia.

**Zadanie 1**  
Pracę rozpocznij od utworzenia nowego brancha o nazwie `lab_3` w swoim repozytorium. Następnie stwórz nowy model o nazwie `Osoba` z polami:
* `imie` - pole tekstowe, wymagane, niepuste (sprawdź dokumentację z pkt. 2)
* `nazwisko` - pole tekstowe, wymagane, niepuste
* `plec` - pole wyboru (kobieta, mężczyzna, inne)
* `stanowisko` - klucz obcy do modelu `Stanowisko` (do utworzenia w kolejnym kroku).

Stwórz model `Stanowisko` z polami:
* `nazwa` - pole tekstowe, wymagane, niepuste
* `opis` - pole tekstowe, opcjonalne

Przygotuj i wykonaj migrację. Dodaj modele do panelu administracyjnego i zapisz do bazy po 3 instancje obiektu `Stanowisko` i `Osoba`.

**Zadanie 2**  
W dokumentacji pola typu `DateField` znajdź informacje o sposobie automatycznego wstawiania wartości znacznika czasu (timestamp) w momencie utworzenia obiektu. Ustaw taką własność dla nowego pola `data_dodania` modelu `Osoba`.

**Zadanie 3**  
Znajdź w dokumentacji modułu admin możliwość ustawienia danego pola modelu tylko do odczytu i ustaw taką własność dla pola `data_dodania` modelu `Osoba`.

**Zadanie 4**  
Z dokumentacji typów wyliczeniowych https://docs.djangoproject.com/en/4.2/ref/models/fields/#enumeration-types wykorzystaj klasę `models.IntegerChoices` i zmień deklarację pola `plec`, jeżeli użyłeś/-aś innej metody.

**Zadanie 5**  
Dodaj nowe modele do panelu admina. Użyj panelu admina, żeby dodać nowe obiekty danych modeli. Co trzeba zrobić gdy tworzymy obiekt z modelu, który wykorzytsuje klucz obcy?

**Zadanie 6**  
PROJEKT Zastanów się jak zmodyfikować (albo dodać) modele w swoim projekcie. Zatanów się nad atrybutami.
