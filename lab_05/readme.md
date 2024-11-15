# Projektowanie aplikacji webowych, semestr 2024Z

## Lab 5
---
### **1. Praca z obiektami QuerySet.**

> Dokumentacja Django QuerySet: https://docs.djangoproject.com/en/4.2/ref/models/querysets/

API dostarczane przez klasę QuerySet służy do komunikowania się z poziomu aplikacji z bazą danych, która jest skonfigurowana w projekcie Django. Ten mechanizm jest warstwą mechanizmu ORM (Object Relational Mapping) czyli mapowania obiektowo-relacyjnego, które umożliwia konwersję obiektów modeli na tabele w bazie danych. API QuerySet umożliwia pobieranie, tworzenie, aktualizację oraz usuwanie obiektów z bazy danych. Operacje te wykonaliśmy już niejawnie wcześniej zarządzając obiektami z poziomu przeglądarki w formularzach dostarczonych przez panel administracyjny Django, który z API QuerySet korzysta.

Obiekty QuerySet, które stworzymy będą w ostatniej fazie tłumaczyły nasz kod na odpowiedni język zapytań SQL w zależności od silnika baz danych skonfigurowanego w naszym projekcie. Jest to więc wygodna warstwa pośrednia, która nie wymusza na nas znajomości danego dialektu SQL, ale co bardziej istotne, pozwala na zmianę silnika bazy danych w projekcie bez konieczności przepisywania zapytań! Wystarczy, że przygotujemy zrzut danych z aktualnej bazy do ogólnego formatu (np. JSON), zmienimy konfigurację w pliku settings.py na nowy silnik, wykonany migracje aby odtworzyć strukturę w nowej bazie i załadujemy wcześniej zrzucone dane.

#### **1.1 Wykorzystanie Django shell do pracy z API QuerySet.**

W obecnym stanie projektu najwygodniej będzie nam korzystać z QuerySet poprzez Django shell.
Uruchamiamy je poprzez wpisanie w terminalu poniższego polecenia (pamiętaj o ustawieniu wcześniej odpowiedniej ścieżki, tak aby plik manage.py znajdował się w folderze, z którego poniższe polecenie jest wywoływane)

_Listing 1_
```python
python manage.py shell
```

Powinniśmy zobaczyć w konsoli znak zachęty charakterystyczny dla interaktywnego Pythona czyli **>>>**.

W zależności od klas modeli, które posiadamy oraz chcielibyśmy wykorzystać w poniższych zapytaniach powinniśmy je najpierw zaimportować, tak aby interpreter Pythona "widział" je w aktualnej sesji i aby QuerySet "wiedział" jakie cechy (pola) tych obiektów ma do dyspozycji.

Przykładowy import klas modeli `Person` oraz `Team`.

_Listing 2_
```python
# zakładając, że nasza aplikacji, z której chcemy zaimportować modele nazywa się myapp
from myapp.models import Person, Team
```

#### **1.2 Pobieranie danych.**

Pobieranie danych może mieć wiele postaci zgodnie z tym co możemy wykonać tworząc zapytania wybierające `SELECT` zawarte w języku SQL, który to zostanie użyty aby finalnie nasze obiekty pobrać z bazy. Można się więc domyślać, że aby zapewnić całe spektrum dostępnych zapytań SQL poprzez obiekt QuerySet, musi on dostarczać wielu metod. W związku z tym w tej dokumentacji zostaną przedstawione tylko wybrane z nich, a do pełnej listy jego możliwości odsyłam do dokumentacji podanej na początku tego dokumentu.

Zaczniemy od najprostrzego zapytania, które pobierze listę wszystkich obiektów danego typu (modelu) z naszej bazy. Warto tutaj zaznaczyć, że nie będziemy tworzyć obiektów klasy QuerySet w sposób bezpośredni czyli tworząc jawnie obiekt tego typu, a poprzez wywołanie metod naszych modeli, które zostały dostarczone przez mechanizm dziedziczenia z klasy `models.Model` API Django (zobacz plik models.py w swojej aplikacji).

_Listing 3_
```python
>>> Person.objects.all()
# w moim przypadku wyświetlone zostało
<QuerySet [<Person: Someone>, <Person: DrWho>]>
```

To co widzimy to obiekt QuerySet, który zwrócił dwa obiekty typu Person jako wynik tej operacji. Każdy z tych obiektów jest tu reprezentowany przez imie, a właściwie wartość pola `name`, które zostało w tym modelu zdefiniowane. To że widzimy je tutaj jest już efektem metody `__str__`, która do definicji modelu Person została przez nas wcześniej dodana w postaci:

_Listing 4_
``` python
def __str__(self):
    return self.name
```

Oznacza to, że za każdym razem gdy będziemy chcieli uzyskać obiekt klasy `Person` w postaci łańcucha znaków metoda `__str__()` zostanie wywołana i zwróci nam wartość pola name tego konkretnego obiektu. Jeżeli ten metody nie zdefiniujemy w klasie modelu efekt zapytania z listingu 3 może wyglądać tak: `<QuerySet [<Person: Person object (1)>, <Person: Person object (2)>]>`

**Filtrowanie obiektów**

Filtrowanie listy obiektów polega na określeniu wartości pól, które nas interesują spośród wszystkich obiektów w bazie. Poniżej kilka przykładów dla obiektów typu Person. Poniższy przykład wykorzystania metody `get()` nie zwraca obiektu typu QuerySet, tylko obiekt konkretnego typu (lub rzuca (ang. raise) wyjątek, jeżeli nie ma obiektów spełniających kryteria).

_Listing 5_
```python
# pobranie obiektu Person, który posiada atrybut id równy 1
>>> Person.objects.get(id=1)  
<Person: Someone>

# jeżeli obiektów spełniających kryteria nie ma, możemy zobaczyć poniższy wynik
>>> Person.objects.get(id=10) 
Traceback (most recent call last):
  File "<console>", line 1, in <module>
  File "c:\Users\Krzysztof\__projects\__swps_www_c1\.venv\Lib\site-packages\django\db\models\manager.py", line 87, in manager_method
    return getattr(self.get_queryset(), name)(*args, **kwargs)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "c:\Users\Krzysztof\__projects\__swps_www_c1\.venv\Lib\site-packages\django\db\models\query.py", line 637, in get
    raise self.model.DoesNotExist(
myapp.models.Person.DoesNotExist: Person matching query does not exist.

# zgłoszony został wyjątek DoesNotExist, czyli takiego obiektu typu Person nie ma w naszej bazie
```

Metoda, dzięki której możemy uzyskać listę obiektów (tu obiekt typu QuerySet), które spełniają zadane kryteria jest metoda `filter()`. Poniżej kilka przykładów.

_Listing 6_
```python
# szukamy obiektów z konkretną wartością pola name
>>> Person.objects.filter(name='Someone')
<QuerySet [<Person: Someone>]>

# szukamy obiektów, dla których pole age jest większy niż 18
>>> Person.objects.filter(age__gt=18)     
<QuerySet [<Person: Someone>, <Person: DrWho>]>

# i tutaj widzimy dość specyficzny sposób definiowania niektórych warunków filtrowania
# gdzie do pola o nazwie age dodaliśmy część __gt i ponownie użyliśmy znaku = do określenia wartości
# do porównania
```

Tutaj na chwilę się zatrzymamy. A co gdybysmy chcieli wyświetlić trochę więcej szczegółów na temat obiektu/obiektów, które te zapytania nam zwracają?
Możemy zrobić z tymi obiektami dokładnie to samo co z każdym innym obiektem w Pythonie. Jeżeli posiada pola, możemy się do nich odwołać, jeżeli posiadają metody możemy je wywołać. Wykorzystajmy najpierw wcześniejszy przykład z listą wszystkich obiektów, czyli obiekt typu QuerySet z listą oiektów typu Person. Możemy dostać się do poszczególnych obiektów tej listy poprzez indeksowanie lub cięcie (ang. slicing).

_Listing 7_
```python
>>> lista = Person.objects.all()
>>> lista 
<QuerySet [<Person: Someone>, <Person: DrWho>]>
>>> lista[0]
<Person: Someone>
>>> lista[0].name
'Someone'
>>> lista[0].age 
21
>>> lista[0].team    
<Team: GoodGuys>
>>> lista[0].team.name
'GoodGuys'

# lub aby wyświetlić wartość wszystkich pól, możemy użyć metody values()
>>> lista.values()  
<QuerySet [{'id': 1, 'name': 'Someone', 'shirt_size': 'S', 'month_added': 4, 'team_id': 2, 'age': 21}, {'id': 2, 'name': 'DrWho', 'shirt_size': 'S', 'month_added': 5, 'team_id': 1, 'age': 35}]>
```

A teraz zobaczmy jakie zapytanie jest faktycznie wysyłane do silnika bazy danych, aby zrealizować nasze wywołanie QyerySet API.

_Listing 8_
```python
>>> print(lista.query)   
SELECT "myapp_person"."id", "myapp_person"."name", "myapp_person"."shirt_size", "myapp_person"."month_added", "myapp_person"."team_id", "myapp_person"."age" FROM "myapp_person"
```

Możemy wypisać SQL dla każdego zapytania z tego API, również po to, aby sprawdzić lub lepiej zrozumieć co dana funkcja faktycznie spowoduje w kontekście zapytania do bazy.

Inne przykłady wykorzystania metody `filter()`.

_Listing 9_
```python
# pole name rozpoczyna się od litery S
>>> Person.objects.filter(name__startswith='S')
<QuerySet [<Person: Someone>]>

# istnieje również metoda, dla której nie będzie miało znaczenia czy to będzie S czy s
# tzw. case insensitive - niewrażliwa na wielkość znaków
>>> Person.objects.filter(name__istartswith='S') 
<QuerySet [<Person: Someone>]>

# obiekty, których pole name zawiera literę 'o'
>>> Person.objects.filter(name__contains='o') 
<QuerySet [<Person: Someone>, <Person: DrWho>]>

# lub tylko wybrane pola
>>> lista.values('name', 'age') 
<QuerySet [{'name': 'Someone', 'age': 21}, {'name': 'DrWho', 'age': 35}]>
```

Czasem będzie nas interesowała lista obiektów, dla której łatwiej jest zdefiniować warunek, którego nie powinny spełniać niż ten, który spełniony być powinien. Możemy wtedy wykorzystać metodę `exclude()` jak w przykładzie poniżej.

_Listing 10_
```python
# wszystkie obiekty Person gdzie id team jest różne od 2
>>> Person.objects.exclude(team_id=2) 
<QuerySet [<Person: DrWho>]>

# wszystkie obiekty Person gdzie nazwa nie rozpoczyna się od S
>>> Person.objects.exclude(name__startswith='S') 
<QuerySet [<Person: DrWho>]>

# ta metoda zezwala na dodanie kolejnych parametrów w jednym wywołaniu, ale
# efekt działania może być na początku nieoczywisty jeżeli nie wczytamy się
# w dokumentację, przykład
>>> Person.objects.exclude(name__startswith='S', team_id=1)              
<QuerySet [<Person: Someone>, <Person: DrWho>]>
>>> print(Person.objects.exclude(name__startswith='S', team_id=1).query)
SELECT "myapp_person"."id", "myapp_person"."name", "myapp_person"."shirt_size", "myapp_person"."month_added", "myapp_person"."team_id", "myapp_person"."age" FROM "myapp_person" WHERE NOT ("myapp_person"."name" LIKE S% ESCAPE '\' AND "myapp_person"."team_id" = 1 AND "myapp_person"."team_id" IS NOT NULL)

# widać, że pierwszy atrybut exclude zostanie wyłączony z wyników, ale już kolejny będzie warunkiem
# którego będziemy w bazie szukać, aby dodać kolejne wykluczenie należy
# wsposób wywołania łańcuchowego exclude dodawać kolejne warunki
>>> Person.objects.exclude(name__startswith='S').exclude(team_id=1)             
<QuerySet []>
```

Istnieją jeszcze inne metody i parametry filtrowania wartości, ale odsyłam po szczegóły do oficjalnej dokumentacji.

#### **1.3 Tworzenie, edycja i usuwanie obiektów poprzez API QuerySet**

Oprócz pobierania daych z bazy poprzez wywołania API QuerySet możemy rówież takie obiekty zapisywać, modyfikować oraz usuwać.
Zacznijmy od przykładu stworzenia nowego obiektu typu Person, który możemy zainicjalizowac na kilka sposóbów, ale jego utrwalenie, czyli zapisanie w bazie odbywa się poprzez wywołanie na tym obiekcie metody `save()`.

_Listing 11_
```python
new_person = Person()
>>> new_person.name = 'Waldek'
>>> new_person.shirt_size = 'L'
>>> new_person.age = 33        
>>> new_person.save()
>>> Person.objects.get(name='Waldek')
<Person: Waldek>
# pobranie utworzonej automatycznie wartości klucza głównego (stąd zmienna pk -> primary key)
# której faktyczna nazwa (jeżeli w pliku models.py nie zdefiniowaliśmy inaczej) to id
>>> Person.objects.get(name='Waldek').pk           
3
>>> Person.objects.get(name='Waldek').id
3
```

Jeżeli imię naszej osoby nam się nie podoba lub popełniliśmy błąd, który chcemy naprawić, to zapewne chcemy wykonać operację `UPDATE` na naszej bazie. Zobaczmy przykład poniżej.

_Listing 12_
```python
>>> waldek = Person.objects.get(pk=3)
>>> waldek
<Person: Waldek>
>>> waldek.name = 'Mieczysław'
>>> waldek.name
'Mieczysław'
>>> waldek.save()
>>> waldek.id
3
>>> Person.objects.all()
<QuerySet [<Person: Someone>, <Person: DrWho>, <Person: Mieczysław>]>
>>>
```
Aktualizacja wartości pola nie powoduje dodania nowego obiektu typu Person do bazy co widać po tym, że po wywołaniu metody save() wartość pola id nie uległa zmianie, ale pola name już tak.

Operację update możemy wykonać również w inny sposób. Co jeżeli chcemy zaktualizować większą liczbę obiektów? Czy musimy pobierać każdy z nich i zmieniać wartość? Co jeżeli dla wielu obiektów chcemy zwienić wartość na taką samą? To byłaby bardzo żmudna i powtarzalna praca, której zapewne nie chcielibyśmy wykonywać.
Z pomocą przychodzi nam metoda `update()` klasy QuerySet, którą możemy wywałać na pobranym wcześniej zbiorze obiektów. Przykład poniżej.

_Listing 13_
```python
# sprawdzamy jakie drużyny są aktualnie przypisane do naszych osób
Person.objects.all().values('team_id') 
<QuerySet [{'team_id': None}, {'team_id': 1}, {'team_id': 2}]>
# aktualizujemy drużyny, ustawiając wszystkim obiektom wartość id drużyny na 1
>>> Person.objects.all().update(team_id=1)
# ta liczba poniżej to liczba zmodyfikowanych rekordów w bazie
3
# i na koniec sprawdzamy czy aktualizacja się powiodła
>>> Person.objects.all().values('team_id') 
<QuerySet [{'team_id': 1}, {'team_id': 1}, {'team_id': 1}]>
>>>
```

Usuwanie obiektów działa w podobny sposób, tu używamy metody `delete()`.

_Listing 14_
```python
# wywołanie metody delete bezpośrednio na obiekcie typu Person pobranym z bazy
# pamiętajmym, że metoda get() nie wzraca obiektu typu QuerySet
Person.objects.get(pk=3).delete()
(1, {'myapp.Person': 1})
>>> Person.objects.all()                   
<QuerySet [<Person: Someone>, <Person: DrWho>]>

# możemy usunąć więcej obiektów wywołując delete() na obiekcie QuerySet
>>> p1 = Person()                          
>>> p1.name = 'Adam'
>>> p1.age = 40
>>> p1.save()
>>> p2 = Person()
>>> p2.name = 'Adam' 
>>> p2.age = 44      
>>> p2.save()
# dlaczego nie definiujemy wartości pól shirt_size, month_added oraz team ?
# posiadają one wartości domyślne, które zostaną tutaj wstawione
>>> Person.objects.all()
<QuerySet [<Person: Someone>, <Person: DrWho>, <Person: Adam>, <Person: Adam>]>
>>> Person.objects.filter(name='Adam')
<QuerySet [<Person: Adam>, <Person: Adam>]>
>>> Person.objects.filter(name='Adam').delete()
(2, {'myapp.Person': 2})
```

#### **1.4 Inne wybrane metody API QuerySet**

Istnieją jeszcze inne metody tego API, które mogą być pomocne. Kilka przykładów zostało zaprezentowanych poniżej.

_Listing 15_
```python
# pobranie pierwszego rekordu z tabeli Team
>>> Team.objects.first() 
<Team: BadGuys>

# pobranie ostatniego rekordu
>>> Team.objects.last()  
<Team: GoodGuys>

# pobranie unikalnego zbioru wartości
>>> Person.objects.distinct()          
<QuerySet [<Person: Someone>, <Person: DrWho>]>

# nasza baza (sqlite) nie obsługuje możliwości pobrania wartości unikalnych dla danej kolumny
>>> Person.objects.distinct('team_id')
# komunikat
# raise NotSupportedError(
# django.db.utils.NotSupportedError: DISTINCT ON fields is not supported by this database backend

# zliczenie ilości obiektów w QuerySet
>>> Person.objects.count()
2
>>> Person.objects.filter(name__startswith='S').count()
1

# sortowanie wyników, tutaj po kolumnie name
>>> Person.objects.all().order_by('name') 
<QuerySet [<Person: DrWho>, <Person: Someone>]>

# widzimy poniżej, że domyślne sortowanie po id zostało zastąpione
>>> Person.objects.all().order_by('name').values()
<QuerySet [{'id': 2, 'name': 'DrWho', 'shirt_size': 'S', 'month_added': 5, 'team_id': 1, 'age': 35}, {'id': 1, 'name': 'Someone', 'shirt_size': 'S', 'month_added': 4, 'team_id': 1, 'age': 21}]>

# a kierunek sortowania domyślny to rosnąco, możemy to zmienić dodając do nazwy
# pole, po którym sortujemy znak -
>>> Person.objects.all().order_by('-name').values() 
<QuerySet [{'id': 1, 'name': 'Someone', 'shirt_size': 'S', 'month_added': 4, 'team_id': 1, 'age': 21}, {'id': 2, 'name': 'DrWho', 'shirt_size': 'S', 'month_added': 5, 'team_id': 1, 'age': 35}]>
```

#### **Zadania**

W swoim folderze z projektem (zapewne główny folder projektu\myproject) stwórz folder o nazwie `zadania` i tam umieść plik `zadania_lab_5.md`, w którym umieść polecenia będące rozwiązaniem poniższych zadań. Przykład struktury pliku poniżej.

```markdown
# Rozwiązania zadań lab 5

## zadanie 1

tu kod ...

## zadanie 2
...
```

**Zadanie 1**  
Uruchom Django shell tak jak w przykładach zaprezentowanych w tym labie i dodaj po 3 nowe obiekty typu Person oraz Team.

**Zadanie 2**  
Wykonaj zapytanie filtrujące obiekty typu Person, których nazwa rozpoczyna się od wybranej przez Ciebie litery, tak aby zwrócone były wyniki.

**Zadanie 3**  
Dla danych zwróconych w zadaniu 2 wyświetl listę wszystkich wartości tych obiektów (funkcja values()).

**Zadanie 4**  
Wykonaj pobranie wszystkich obiektów typu Person i zapisz to do zmiennej `persons`.

**Zadanie 5**  
Z listy stworzonej w zadaniu 4 za pomocą cięcia (slicing) wyświetl:
* pierwszy element
* ostatni element
* wszystkie elementy w odwróconej kolejności (od ostatniego do pierwszego)
* co drugi element

**Zadanie 6**  
Wyświetl liczbę obiektów typu Person, które nie mają przypisanej drużyny (pole team).

**Zadanie 7**  
Wyświetl wszystkie drużyny sortując po nazwie drużyny w porządku literowym Z-A.

**Zadanie 8**  
Wyświetl wszystkie obiekty Team, których id jest mniejsze niż 3 uzywając metody `exclude()`.