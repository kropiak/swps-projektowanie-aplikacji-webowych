# Projektowanie aplikacji webowych, semestr 2024Z

## Lab 7 - Django Rest Framework i serializacja danych.
---


`Serializer` pozwala na konwersję złożonych danych na rodzime typy danych w języku Python, które można następnie łatwo przekształcić w JSON, XML lub inne typy treści. Serializatory zapewniają również deserializację, umożliwiając przekształcenie przeanalizowanych danych z powrotem w złożone typy po uprzednim sprawdzeniu poprawności danych przychodzących.

`Serializer` w środowisku REST działa bardzo podobnie do klas `Form` i `ModelForm` w Django. W naszych projektach będziemy korzystać z:

- `Serializer` - ogólny sposób kontrolowania requestów,
- `ModelSerializer` - sprawdzanie modeli, inicjalizacja.

Po dokładne informacje o serializacji odsyłamy do informacji z wykładu oraz [dokumentacji DRF](https://www.django-rest-framework.org/api-guide/serializers/).

## Przykład klasy do serializacji danych (dziedziczącej po klasie Serializer)

**__Listing 1__**
```python
from rest_framework import serializers
from .models import Person, Team, MONTHS, SHIRT_SIZES


class PersonSerializer(serializers.Serializer):

    # pole tylko do odczytu, tutaj dla id działa też autoincrement
    id = serializers.IntegerField(read_only=True)

    # pola wymagane
    firstname = serializers.CharField(required=True)
    lastname = serializers.CharField(required=True)

    # pole mapowane z klasy modelu, z podaniem wartości domyślnych
    # zwróć uwagę na zapisywaną wartość do bazy dla default={wybór}[0] oraz default={wybór}[0][0]
    # w pliku models.py SHIRT_SIZES oraz MONTHS zostały wyniesione jako stałe do poziomu zmiennych skryptu
    # (nie wewnątrz modelu)
    shirt_size = serializers.ChoiceField(choices=SHIRT_SIZES, default=SHIRT_SIZES[0][0])
    month_added = serializers.ChoiceField(choices=MONTHS.choices, default=MONTHS.choices[0][0])

    # odzwierciedlenie pola w postaci klucza obcego
    # przy dodawaniu nowego obiektu możemy odwołać się do istniejącego poprzez inicjalizację nowego obiektu
    # np. team=Team({id}) lub wcześniejszym stworzeniu nowej instancji tej klasy
    team = serializers.PrimaryKeyRelatedField(queryset=Team.objects.all())

    # przesłonięcie metody create() z klasy serializers.Serializer
    def create(self, validated_data):
        return Person.objects.create(**validated_data)

    # przesłonięcie metody update() z klasy serializers.Serializer
    def update(self, instance, validated_data):
        instance.firstname = validated_data.get('firstname', instance.firstname)
        instance.lastname = validated_data.get('lastname', instance.lastname)
        instance.shirt_size = validated_data.get('shirt_size', instance.shirt_size)
        instance.month_added = validated_data.get('month_added', instance.month_added)
        instance.team = validated_data.get('team', instance.team)
        instance.save()
        return instance
```

Przetestowanie działania serializera możemy również przeprowadzić z poziomu shella Django. Przykład kolejnych operacji poniżej.

_**Listing 2**_
```python

# uruchamiamy shell poleceniem: python manage.py shell

from myapp.models import Person
from myapp.serializers import PersonSerializer
from rest_framework.renderers import JSONRenderer
from rest_framework.parsers import JSONParser

# 1. stworzenie nowej instancji klasy Person (opcjonalne, mamy panel admin do tego również)
person = Person(firstname='Adam', lastname='Bułka', month_added=1)
# utrwalenie w bazie danych
person.save()

# 2. inicjalizacja serializera
serializer = PersonSerializer(person)
serializer.data
# output - natywny typ danych Pythona (dictionary)
{'id': 16, 'firstname': 'Adam', 'lastname': 'Bułka', 'shirt_size': ('S', 'Small'), 'month_added': 1, 'team': None}

# warto również zwrócić uwagę na wartość zmiennej shirt_size, która przyjęła wartość jako krotkę (która została zamieniona na typ str),
# gdyż w modelu został domyślny wybór określony jako SHIRT_SIZE[0] co jest pierwszą krotką dla tej kolekcji o wartości ('S', 'Small')
# zamiana na SHIRT_SIZE[0][0] wskazałaby wartość 'S' i powinna również działać poprawnie dla modelu Person
# output po zmianach w modelu, np.
# {'id': 19, 'firstname': 'Genowefa', 'lastname': 'Niepomaga', 'shirt_size': 'S', 'month_added': 1, 'team': None}

# 3. serializacja danych do formatu JSON
content = JSONRenderer().render(serializer.data)
content

# output
b'{"id":16, "firstname": "Adam", "lastname": "Bułka', "shirt_size":"S","month_added":1,"team":null}'

# w takiej formie możemy przesłać obiekt (lub cały graf obiektów) przez sieć i po "drugiej stronie" dokonać deserializacji odtwarzając graf i stan obiektów

import io

stream = io.BytesIO(content)
data = JSONParser().parse(stream)

# tworzymy obiekt dedykowanego serializera i przekazujemy sparsowane dane
deserializer = PersonSerializer(data=data)
# sprawdzamy, czy dane przechodzą walidację (aktualnie tylko domyślna walidacja, dedykowana zostanie przedstawiona na kolejnych zajęciach)
deserializer.is_valid()
# output
# False

# to oznacza pojawienie się błędu walidacji
deserializer.errors
# output
# {'team': [ErrorDetail(string='Pole nie może mieć wartości null.', code='null')]}

# w samym modelu określone są dwa atrybuty null=True, blank=True, ale jak widać serializer nie bierze tego pod uwagę
# musimy w klasie PersonSerializer zmodyfikować wartość dla pola team
# dodając atrybut allow_null=True i uruchomić całe testowanie raz jeszcze

# aby upewnić się w jaki sposób wyglądają pola wczytanego serializera/deserializera, możemy wywołać zmienną deserializer.fields, aby wyświetlić te dane
deserializer.fields

# lub
repr(deserializer)

# po powyższych zmianach walidacja powinna już się powieść
# możemy sprawdzić jak wyglądają dane obiektów po deserializacji i walidacji
deserializer.validated_data
# output
# OrderedDict([('firstname', 'Adam'), ('lastname', 'Bułka'), ('shirt_size', 'S'), ('month_added', 1), ('team', None)])

# oraz utrwalamy dane
deserializer.save()
# sprawdzamy m.in. przyznane id
deserializer.data
```


W przykładzie powyżej widać sporo nadmiarowej pracy w stosunku do zdefiniowanych wcześniej modeli (możemy oczywiście chcieć serializować również inne obiekty niż modele z naszego projektu) i na pewno pojawiła się refleksja  - "Czy można wykorzystać jakąś część kodu z klas modeli?". Otóż można, wykorzystując klasę `ModelSerializer` z `Django Rest Framework`.


## Przykład klasy do serializacji danych (dziedziczącej po klasie ModelSerializer)

Dokumentacja: https://www.django-rest-framework.org/api-guide/serializers/#modelserializer

_**Listing 3**_
```python
class PersonModelSerializer(serializers.ModelSerializer):
    class Meta:
        # musimy wskazać klasę modelu
        model = Person
        # definiując poniższe pole możemy określić listę właściwości modelu,
        # które chcemy serializować
        fields = ['id', 'firstname', 'lastname', 'month_added', 'shirt_size', 'team']
        # definicja pola modelu tylko do odczytu
        read_only_fields = ['id']
```

Powyższa klasa serializera wykorzystuje wszystkie własności pól z klasy modelu, co znacznie zmniejsza ilość powielanego kodu i redukuje czas i ilość pracy niezbędny do dokonania zmian walidacji pól modelu. Te cechy zostaną pobrane do serializera z definicji modelu, więc nie musimy ich przechowywać w dwóch miejscach.

Testowanie kodu odbywa się adekwatnie do przykładu z listingu nr 1.


# Zadania

Celem ćwiczeń będzie stworzenie klas odpowiedzialnych za serializację danych w naszym projekcie API. Następujące polecenia powinny być wykonane dla każdego modelu w projekcie, do którego będziemy mieli dostęp zewnętrzny (przez dostęp do endpointu, np. dodanie nowej osoby do systemu).


1. Tworzymy nowy branch na potrzeby tego labu.
2. Instalujemy moduł `djangorestframework` do naszego środowiska.
3. W **aplikacji**, w której znajduje się nasz model otwieramy/tworzymy plik `serializers.py`.

W Django zaleca się definiowanie serializerów w pliku o nazwie **`serializers.py`** w aplikacji, w której znajdują się modele, które będą serializowane. Plik ten organizuje kod i sprawia, że jest on łatwy do znalezienia oraz utrzymania, podobnie jak pliki `models.py` dla modeli czy `views.py` dla widoków.

### Struktura pliku `serializers.py`

W typowej aplikacji Django plik `serializers.py` będzie zawierał importy oraz klasy serializerów dla modeli w tej aplikacji. Przykład struktury pliku:

```python
# myapp/serializers.py

from rest_framework import serializers
from .models import Article, Author

class ArticleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = ['id', 'title', 'content', 'published_date']

class AuthorSerializer(serializers.ModelSerializer):
    class Meta:
        model = Author
        fields = ['id', 'name', 'bio']
```

### Korzyści z użycia `serializers.py`

- **Organizacja**: Ułatwia zarządzanie kodem, szczególnie gdy aplikacja rośnie.
- **Łatwiejsze testowanie i debugowanie**: Wszystkie serializery są w jednym miejscu, więc łatwiej jest je przetestować lub zaktualizować.
- **Konwencja i czytelność**: Inni programiści pracujący nad projektem zrozumieją, że serializery można znaleźć w pliku `serializers.py`, co przyspiesza współpracę.

### Kiedy dodawać serializery do innych plików?

W dużych projektach, gdzie liczba serializerów jest bardzo duża, można podzielić serializery na kilka plików lub folderów. Wtedy można stworzyć katalog `serializers/` i tam umieścić pliki o nazwach odpowiadających logice aplikacji, np.:

```
myapp/
├── serializers/
│   ├── article_serializers.py
│   ├── author_serializers.py
│   └── __init__.py
```

W pliku `__init__.py` można zaimportować i udostępnić wszystkie serializery, np.:

```python
# myapp/serializers/__init__.py
from .article_serializers import ArticleSerializer
from .author_serializers import AuthorSerializer
```

Dzięki temu zachowasz czytelność i strukturę projektu, a jednocześnie unikniesz zbyt dużych, trudnych do zarządzania plików.

4. Napisz 1 klasę serializera dla swojej aplikacji dziedziczącą po klasie `serializers.Serializer`.
5. Resztę modeli zaimplementuj w postaci serializerów `ModelSerializer` jeżeli to możliwe.
6. Napisz kod prezentujący wykorzystanie wybranych dwóch serializerów (patrz listing 2) ze swojej aplikacji i umieść go w pliku markdown o nazwie `drf_serializer_test.md` w folderze `./aplikacja/docs/` (utwórz folder `docs`).

Do konsoli django można również przekazać cały plik z kodem testującym przekazując go jako potok wejściowy:
```console
python manage.py shell < ./sciezka/do_pliku.py
```
Należy jednak pamiętać o tym, że w ten sposób musimy wykonywac importy z podaniem nazwy aplikacji np. `from ankiety.models import Person`, a nie `from .models import Person`.

Minusem jest niestety dość nieczytelny output, gdzie znaczniki wyjścia konsoli Pythona (czyli >>>) mogą się wielokrotnie powtarzać.


Dodatkowe wskazówki:

* Nadpisujemy odpowiednie funkcje `create`, `update` itp. w momencie gdy musimy zapisać obiekty w inny sposób (np. chcemy dodać aktualnie zalogowanego użytkownika jako właściciela stworzonego modelu, chcemy zamienić wielkość znaków, usunąć jakieś znaki z tekstu itp.).
