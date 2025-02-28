# Wielka księga przewinień programistów osadzonych

## Wstęp

Jak wiadomo, dokumentacji nikt nie będzie czytał dokładnie, słowo w słowo.
Więc proszę się nie zdziwić jak pojawią się tutaj jajka wielkanocno świąteczne, a forma nie będzie w pełni oficjalna.

Każdy punkt będzie wyglądał na zasadzie **Rozwiązanie** -> **Problemy tego rozwiązania** -> **Lepsze rozwiązanie** + potencjalne wyjątki

Będzie używany C++20, skoro robimy coś mega, to będzie nowocześnie.

## Założenia
Projekt tworzenia studenckiego satelity, **nie** jest projektem drogiej todo listy.

Programiści, przychodzą, odchodzą, stale... Programiści w projekcie będa się przewijać, będa z różnym doświadczeniem, z różną wiedzą. 
To jest powód dla którego kod powinien być jak najprostszy, to znaczy, bez fikuśnych tworów np `std::views`, z dużą ilością dokumentacji. W takim wypadku wdrożenie nowej osoby do projektu powinno też przebiegać szybko. 

Programiści będa przychodzić z innych języków. 

### *todo*

- proces wdrażania nowych ludzi do projektu

## Setup Projektu 

### *todo*
- jak wszystko będzie gotowe to jakiś filmik coś ten powstanie

## Filozofia pisania kodu

### Wydajność 
Wydajność nie jest najważniejsza, ale nie jest pomijalna. Wydajność kodu bezpośrednio przekłada się na zużycie energii przez satelitę. 

### 1. Czytanie Kodu.
 Kod będzie czytany przez większość twojego czasu pracy i to tak sporo...
To jest pierwszy i najważniejszy punkt tego dokumentu, kod pisany ma 

### 2. Kod ma wyrażać intencje.
### 3. Type safety first
Zgodność typów jest jednym z najpotężniejszych narzędzi walki z robakami (en. bug) w kodzie

unsafe

    void print_arr(void* arr, size_t len){
        for(int i = 0; i < len; i++)
            std::cout << arr[i];
    }

safe

    template <typename T>
    void print_arr(std::span<T> arr){
        for(const auto& item : arr)
            std::cout << item;
    }

### 4. compile time >>> run time
### 5. abstrakcja nie jest zła
### 6. KISS
Utrzymuj kod jak najprostszy, jeżeli zaawansowane struktury danych nie są niezbędne, to ich nie używaj

```cpp
std::string percentage_to_rounds(float percentage){
    std::string result{};

    for (int i = 0; i < 10; i++){
        if (result/10.0f <= percentage)
            result += "🔵";
        else
            result += "⚪";
    }

    return result;
}
```


```cpp
std::string percentage_to_rounds(float percentage) {
    if (percentage == 0)
        return "⚪⚪⚪⚪⚪⚪⚪⚪⚪⚪"
    if (percentage > 0.0 && percentage <= 0.1)
        return "🔵⚪⚪⚪⚪⚪⚪⚪⚪⚪"
    if (percentage > 0.1 && percentage <= 0.2)
        return "🔵🔵⚪⚪⚪⚪⚪⚪⚪⚪"
    if (percentage > 0.2 && percentage <= 0.3)
        return "🔵🔵🔵⚪⚪⚪⚪⚪⚪⚪"
    if (percentage > 0.3 && percentage <= 0.4)
        return "🔵🔵🔵🔵⚪⚪⚪⚪⚪⚪"
    if (percentage > 0.4 && percentage <= 0.5)
        return "🔵🔵🔵🔵🔵⚪⚪⚪⚪⚪"
    if (percentage > 0.5 && percentage <= 0.6)
        return "🔵🔵🔵🔵🔵🔵⚪⚪⚪⚪"
    if (percentage > 0.6 && percentage <= 0.7)
        return "🔵🔵🔵🔵🔵🔵🔵⚪⚪⚪"
    if (percentage > 0.7 && percentage <= 0.8)
        return "🔵🔵🔵🔵🔵🔵🔵🔵⚪⚪"
    if (percentage > 0.8 && percentage <= 0.9)
        return "🔵🔵🔵🔵🔵🔵🔵🔵🔵⚪"

    return "🔵🔵🔵🔵🔵🔵🔵🔵🔵🔵"
}
```

Tak, to jest przykład z mema...

## Pisanie Kodu 

| Feature    | Używamy | Komentarz |
| -------- | ------- | ------- |
| std::views  | 🟨 | Może wprowadzać zamieszanie           | 
| Makra       | 🟨 | Nie jest type safe ale czasami może p |
| constexpr   | ✅ |                                       | 
| STL         | 🟨 | Alternatywą często jest ETL           | 
| koncepty    | ✅ |                                       | 
| new         | ❌ |                                       | 
| malloc      | ❌ |                                       | 


### Nie używamy 



## Styl Pisania Kodu
Punkty zawarte w tym rozdziale nie mają bezpośredniego wpływu na działanie samego kodu. Zawarte tutaj wymogi dotyczą się tylko wizualnej oprawy kodu 

### Długość funkcji
Funkcje nie powinny być dłuższe niż 40 linijek. Nie wymagamy od programistów pionowego monitora 29' do czytania kodu.
Jeżeli funkcja jest za długa powinieneś podzielić ją na mniejsze funkcje

### Formatowanie

#### Odstępy  
    1 tabulacja - 4 spacje

#### Nawiasy wąsate
[K&R](https://en.wikipedia.org/wiki/Indentation_style#K&R)

### Nazewnictwo zmiennych
nazwy zmiennych powinny przedewszystkim informować o tym co jest w środku, używaj deskryptywnych nazw  

| Rodzaj    | Case |
| -------- | ------- |
| Zmienne globalne  | UPPER_SNAKE_CASE    |
| Makra |    UPPER_SNAKE_CASE  |
| constexpr    | UPPER_SNAKE_CASE    |
| Funkcje     | snake_case    |
| Metody    | snake_case    |
| zmienne    | snake_case    |
| Klasy    | PascalCase    |
| Namespace    | PascalCase    |
| Aliasy    | PascalCase    |



ps. nie używamy emoji jako nazwe zmiennej

Przykład ❌

```cpp
    class Kernel;

    Kernel 🥚;
```

Przykład ✅
```cpp
    class Kernel;

    Kernel kernel;
```


### [?] Funkcje

```cpp
auto get_raw_data() -> void {
    ... 
}
```

### Używanie auto


### [?] Używanie this->... 
- ### Powód
    Jak przekazać informację o tym że zmienna jest polem klasy? rozwiązań jest kilka 

    - używanie `_` przed nazwą pola np. `_count`
    - `m` przed nazwą pola np. `m_count`
    - `this->` this->count



## Obsługa gita
Każdy branch mergowany do mastera musi posiadać:

- Stworzoną dokumentację nowych funkcjionalności
- Zostać zatwierdzonym przez 2 innych programistów
- Być zgodnym ze stylem pisania kodu

## Zawsze private i public

## Tworzenie dokumentacji
Dokumentacja powinna przedewszystkim istnieć i być aktualna. 
Brak dokumentacji czy jej niezgodność z rzeczywistością nie tylko powoduje spowolnienie prac ale przede wszystkim frustracje. 
A jak dobrze wiadomo jedyne osoby które mogą pisać dokumetancje to są ludzie którzy stworzyli pewny fragment kodu, 
z tego powodu te zasady mające na celu uprościć do minimum, ułatwić i unifikować tooling, sposób i styl pisania dokumentacji.

[Doxygen](https://www.doxygen.nl/)

## Testowanie

### Testy jednostkowe

### Hardware in the loop

### Data injection


