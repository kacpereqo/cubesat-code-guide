# Wielka księga przewinień programistów osadzonych

Jakieś tam code guidy które podobno działają

- [NASA](http://pixelscommander.com/wp-content/uploads/2014/12/P10.pdf)
- [Google](https://google.github.io/styleguide/cppguide.html)
- [ISO C++](http://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)
- [SAT](https://sat-polsl.gitlab.io/wiki/sat-firmware-launcher-wiki/#/Coding-guidelines)

## Wstęp

Jak wiadomo, dokumentacji nikt nie będzie czytał dokładnie, słowo w słowo.
Więc proszę się nie zdziwić jak pojawią się tutaj jajka wielkanocno świąteczne, a forma nie będzie w pełni oficjalna.

Każdy punkt będzie wyglądał na zasadzie **Rozwiązanie** -> **Problemy tego rozwiązania** -> **Lepsze rozwiązanie** + potencjalne wyjątki

Będzie używany C++23, skoro robimy coś mega, to będzie nowocześnie.
A tak naprawde to w C++23 zostały dodane m.in. takie rzeczy `std::expected`

## Założenia
Projekt tworzenia studenckiego satelity, **nie** jest projektem drogiej todo listy.

Programiści, przychodzą, odchodzą, stale... Programiści w projekcie będa się przewijać, będa z różnym doświadczeniem, z różną wiedzą. 
To jest powód dla którego kod powinien być jak najprostszy, to znaczy, bez fikuśnych tworów np `std::views`, z dużą ilością dokumentacji. W takim wypadku wdrożenie nowej osoby do projektu powinno też przebiegać szybko. 

Programiści będa przychodzić z innych języków. 

### *todo*

- proces wdrażania nowych ludzi do projektu

## Setup Projektu 

### Flagi kompilatora
Kompilator powinen krzyczeć na wszystkie erorry i warningi wszystko na czerwono

taka litania
```
-Wall -Wextra -Wpedantic -Werror -Wconversion -Wshadow -Wstrict-overflow=5 -Wfloat-equal -Wformat=2 \
-Wundef -Wpointer-arith -Wcast-qual -Wcast-align=strict -Wstrict-prototypes -Wwrite-strings -Wstack-protector \
-Wswitch-enum -Wswitch-default -Wunreachable-code -Wduplicated-cond -Wduplicated-branches -Wlogical-op \
-Wnull-dereference -Wuseless-cast -Wdouble-promotion -Walloca -Walloc-zero -Wvla -Wformat-signedness \
-Wstrict-aliasing=3 -Wdisabled-optimization -Wunsafe-loop-optimizations -Wvector-operation-performance \
-Wpessimizing-move -Wredundant-move -Wmisleading-indentation -Wvolatile-register-var -Wdate-time
```

### *todo*
- jak wszystko będzie gotowe to jakiś filmik coś ten powstanie

## Filozofia pisania kodu
Czyli bardzo wysoko poziomowo o tym jakie cechy powinien mieć pisany kod i co jest dla nas ważne. 

### Wydajność 
Wydajność nie jest najważniejsza, ale nie jest pomijalna. Wydajność kodu bezpośrednio przekłada się m.in na zużycie energii przez satelitę. 

### Piszemy kod zgodny ze standardami ISO, wolnym od UB i zależnym od kompilatora

### 1. Czytanie Kodu.
Kod będzie czytany więcej razy, niż będzie pisany, dlatego czytelność powinna być zawsze priorytetem. Dobrze napisany kod to taki, który można łatwo zrozumieć bez konieczności głębokiej analizy. Przejrzysty kod ułatwia jego utrzymanie, modyfikację oraz współpracę w zespole.

### 2. Kod ma wyrażać intencje.
Pierwszym punktem żeby uczynić kod czytelniejszym, programista ma przekazywać swoje intencje w kodzie, przez nazewnictwo, dobór algorytmów i struktur danych.
Sposób pisania kodu powinien być jak najbardziej przewidywalny dla innych.

❌Funkcja c nie mówi nic o swojej intencji – nazwa jest nieczytelna.
Parametry a i b nic nie wyjaśniają.
``` cpp
    int A(int a, int b) {  
        return a * b;  
}  
```

✅Funkcja ma teraz nazwę calculateArea, co jasno określa jej cel.
Parametry width i height mówią, do czego służą.
```cpp
    int calculateArea(int width, int height) {  
        return width * height;  
    }  
```


### 3. Type safety first
Zgodność typów jest jednym z najpotężniejszych narzędzi walki z robakami (en. bug) w kodzie

❌
```cpp
    void print_arr(void* arr, size_t len){
        for(int i = 0; i < len; i++)
            std::cout << arr[i];
    }

✅
```cpp
    template <typename T>
    void print_arr(std::span<T> arr){
        for(const auto& item : arr)
            std::cout << item;
    }
```

**Materiały**
[CppCon](https://www.youtube.com/watch?v=I8UvQKvOSSw)

### 4. Wybieraj wykonanie w compile time zamiast run time 
**Powód**
Jeżeli jesteś w stanie zrobić coś w compile to tym zwiększysz performance kodu.

*TODO*
opisać `consteval`

**Przykład**

❌ Funkcja zostanie wywołana dopiero w run time co go niepotrzebnie obciąży
```cpp
    constexpr float PI { 3.14159 };

    float circle_area(const float r){
        return PI * r * r;  
    }

    const area = circle_area(10.0);

    std::cout << area;
```

✅ teraz ta funkcja zostanie wywołana podczas kompilacji i run time jedyne co będzie musiał zrobić to wypisać pole
```cpp
    constexpr float PI { 3.14159 };

    constexpr float circle_area(const float r){
        return PI * r * r;  
    }

    constexpr area = circle_area(10.0);

    std::cout << area;
```

**Materiały**

https://www.learncpp.com/cpp-tutorial/constexpr-functions/

### 5. abstrakcja nie jest zła
Wyabstrachowanie skomplikowanych fragmentów kodu do funkcji. Czy odizolowanie pewnego kodu ma zalety nie tylko wizualne ale i praktyczne. Jeżeli tworzymy jakiś niskopoziomy driver dla przykładu UART to chcemy wszyskto wyizolować do tego stopnia że końcowy użytkownik który będzie używał tego uarta będzie miał jedną funkcje do wysylania danych i odbierania, wszystko co jest pod spodem go nie interesuje i tak powinno zostać

**Przykład**


### 6. Pisz kod Jak najprościej sie da 
**Powód**
Utrzymuj kod jak najprostszy, jeżeli zaawansowane struktury danych nie są niezbędne, to ich nie używaj. 
Chciałbym żeby kod był pisany jak C z pełnym type safety i compile time featurami. Odpuść ego wielkiego programisty i nie używaj czegoś czego do końca sam nie rozumiesz tylko po to żeby komuś to tłumaczyć.

**Przykład**
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

| Feature      | Używamy | Komentarz                             |
| ------------ | ------- | ------------------------------------- |
| `std::views` | 🟨       | Może wprowadzać zamieszanie           |
| Makra        | 🟨       | Nie jest type safe ale czasami może p |
| `constexpr`  | ✅       |                                       |
| STL          | 🟨       | Alternatywą często jest ETL           |
| koncepty     | ✅       |                                       |
| `new`        | ❌       |                                       |
| `malloc`     | ❌       |                                       |
| C Array      | ❌       | Mamy `std::array`                     |
| C Enum       | ❌       | Mamy `enum class`                     |

### Używaj STL / ETL
**Powód**
Nie warto marnować czasu na pisanie własnych algorytmów i kontenerów

**Przykład**
nazwa została funkcji celowo wypikana.

❌ Algorytm działa poprawnie, ale nie widać na pierwszy rzut oka co robi, trzeba przekazać dwa argumenty, generalnie kongo. 
```cpp
    float ...(int* tab, size_t len){
        float sum = 0;
        
        for(int i = 0; i < len; i++)
            sum += tab[i];
        
        return sum / len;
    }
```

🟨 Jest już type safe i ekspresywnie ze względu na użycie `std::span` i `std::accumulate` ale można lepiej...
```cpp 
    float ...(std::span<int> tab){
        return std::accumulate(tab.begin(), tab.end(), 0.0) / tab.size();
    }
```

✅ Używanie wersji algorytmów z `::ranges` ukrywa użycie iteratorów więc mniej kodu + mniejsza szansa na błąd. 
```cpp 
    float ...(std::span<int> tab){
        return std::ranges::accumulate(tab, 0.0) / tab.size();
    }
```


### Do operacji bitowych zawsze std::byte
std::byte użyjemy jeżeli wczytujemy coś z pamięci które potem ma być jakoś zinterpretowane

std::byte jest type safe i dobrze oddaje intencje.



## Funkcje

## Wskaźniki do pamięci dynamicznej
Nie używaj gołych wskaźników, są bezpieczniejsze i lepsze rozwiązania prosto z C++ w `<memory>` mamy do dyspozycji `std::unique_ptr` `std::shared_ptr` `std::weak_ptr`  

### Asercje w funkcjach
**Powód**
Każda Funkcja powinna zawierać asercję dla przypadków które nie powinny nigdy się wydarzyć.
Prawda jest taka im więcej checków tym lepiej dla nas, przy debugowaniu jesteśmy w stanie odrazu wykluczyć pewne przypadki,
przy pisaniu nowych funkcjonalności będa to dla nas pierwsze testy. W ten sposób możemy też sprawdzić poprawność danych latających w programie.

**Przykład**
❌ Jeżeli zostanie przekazany nullptr to może być nieciekawie...
```cpp
    int sum(int * tab, size_t len) {
        int sum = 0;
        
        for (int i = 0; i < len; i++)
            sum += tab[i];

        return sum;
    }
```

✅ W ten sposób upewnimy się czy `tab` nie jest nullptr i czy nie mamy błędu w data flow
```cpp
    int sum(int * tab, size_t len) {
        assert(tab != nullptr);
        assert(len != 0);
        assert(len < 0);

        int sum = 0;
        
        for (int i = 0; i < len; i++)
            sum += tab[i];

        return sum;
    }
```



**Źródła**
[NASA pkt.5](http://pixelscommander.com/wp-content/uploads/2014/12/P10.pdf) 

### Error handling
std::expected


**Materiały**
[CppCon](https://www.youtube.com/watch?v=n1sJtsjbkKo&t=2605s)

### Długość funkcji
Funkcje nie powinny być dłuższe niż 40 linijek. Nie wymagamy od programistów pionowego monitora 29' do czytania kodu.
Jeżeli funkcja jest za długa powinieneś podzielić ją na mniejsze funkcje. 

### Jedna funkcja = Jedno zadanie
Funkcje powinny być 

### [?] Trailing Return Type

```cpp
auto get_raw_data() -> void {
    ... 
}
```

### Sprawdzanie poprawności danych
**Powód**
Jeżeli są jakieś wymagania od danych wejściowych funkcji, to **nie** możesz założyć że zostaną one przekazane prawidłowe.
 Więc na początku każdej funkcji muszą zostać sprawdzone argumenty pod kątem poprawności.

**Przykład** 

❌ 
```cpp
    float divide(float a, float b) {
        return a / b;
    }
```

✅ Można generycznie obsłużyć błąd; 
```cpp
    enum class DivisionError{
        division_by_zero,
    }

    std::expected<float, DivisionError> divide(float a, float b) {
        if (b == 0) return DivisionError::division_by_zero;

        return a / b;
    }
```

## Const

### const zmienne / argumenty 
Jeżeli używasz gdzieś zmiennych czy to argumentów i ta zmienna nie będzie modyfikowana, dodaj jej modyfikator `const` żeby lepiej 

**Przykład**
✅
```cpp
    int 
```

❌

**Źródło**
[Google](https://google.github.io/styleguide/cppguide.html#Use_of_const)

### const metody
Jeśli metoda nie ma side-effect'ów na instancji, czyli nie modyfikuje pól obiektu, weź no dorzuć tam const na koniec metody, żeby było wiadomo o co chodzi. Takie zachowanie pomaga po prostu w poprawnej enkapsulacji ,

**Przykład**

✅

❌

### [?] noexcept

## Struktury danych

### C-array vs std::array 
**Powód**
Nigdzie nie chcemy używać C-array, bo mają całkiem sporo problemów, tracą informację o długośći tablicy po przekazaniu do funkcji, 
potrzebny jest dodatkowy argument informujący o długości, przekazany wskaźnik może być `nullptr`

**uproszczona implementacja std::array**

```cpp
template <typname T, std::size_t SIZE>
struct array {
    data T[SIZE];
    
    size_t size() {
        return SIZE;
    }
};
```

Jeżeli znasz już templaty i przeczytałeś powyższy kod, to powinieneś już wiedzieć że `std::array` jest mega wersją C-array bo jest type safe, nie traci informacji o rozmiarze podczas przekazywania do funkcji, posiada iteratory `begin()` i `end()` i nie ma problemów z przekazywaniem do funkcji jako referencje lub wartość.

**Przykład**

❌ Jest tutaj dużo miejsc gdzie coś może pójść nie tak, w `len` może nie być długościi tablicy, `arr` może być `nullptr` 
```cpp
    int sum(int * arr, size_t len){
        int sum{0};
        for(int i = 0; i < len; i++){
            sun += arr[i];
        }

        return sum;
    }
```

✅ Co prawda więcej pisania i kod wykonywalny będzie większy ale powyższe błędy nie mają prawa tutaj wystąpić, dodatkowo nie ma problemu z przekazaniem tablicy po wartości i składnia jest dużo bardziej czytelna.
```cpp
    template<std::size_t SIZE>
    int sum(std::array<int, SIZE> & arr) {
        int sum {0};
        for(auto num : arr) { 
            sum += num;
        }

        return arr;
    }
```

**Materiały**
[cpp reference](https://en.cppreference.com/w/cpp/container/array)

### int* arr + size_t len vs 

## Zmienne 

### Inicjalizacja zmiennych
**Powód**
Zmienne będziemy inicjalizować używając `{}` nie `=` może się to wydawać dziwne i nie intuicyjne ale w ten sposób kompilator będzie mógł mieć wielki problem jeżeli chcemy zaincjalizować zmienne czymś co będzie stratne.

**Przykład**

❌ Trzeba się modlić o error
```cpp   
    int y = 7.9;
```

✅ Kompilator wyrzuci błąd że chcemy niejawnie przekonwertować `double` na `int`
```cpp
    int x {7.9};
```

**Źródło**
[ISO C++](http://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#es23-prefer-the--initializer-syntax)


### Używanie auto
**Powód**
Auto może w dużym stopniu uprościć kod, ale w pewnych przypadkach jest to po prostu nie wskazane.

**Przykład**
❌ Kiedy kompilator będzie musiał z powietrza dedukować typ 
```cpp
    auto num = 1;
```

❌ Bez sprawdzenia typu `divide` nie jesteśmy w stanie określić jaki będzie miało to typ
```cpp
    auto = divide(1,2);
```

✅ auto w for-range loop 
  ``` cpp
    for (auto num : numbers)
  ``` 

✅ Kiedy w tej linijce pojawia się już typ 
```cpp
    auto ptr = std::make_unique<int>(some_num);
```

✅ Jeżeli typ jest logiczny
```cpp
    auto begin = vec.begin();
```


## Nazewnictwo
Nazwy zmiennych powinny przedewszystkim informować o tym co jest w środku, używaj deskryptywnych nazw
Nie bój się długich nazw, większość z nas ma takie coś jak TAB, więc to czy zmienna ma 5 znaków czy 15 nie robi różnicy bo i tak użyjemy autocomplete.

| Rodzaj           | Case             |
| ---------------- | ---------------- |
| Zmienne globalne | UPPER_SNAKE_CASE |
| Makra            | UPPER_SNAKE_CASE |
| constexpr        | UPPER_SNAKE_CASE |
| Funkcje          | snake_case       |
| Metody           | snake_case       |
| zmienne          | snake_case       |
| Klasy            | PascalCase       |
| Namespace        | PascalCase       |
| Aliasy           | PascalCase       |
| Enum             | PascalCase       |
| Wartości enuma   | snake_case       |

### nazwa Enuma w liczbie pojedyńczej
Nazwa enuma zawsze powinna być w liczbie pojedyńczej.

❌ Nie Color**s**, tylko Color
```cpp
enum class Colors {
    red,
    green,
    blue,
} ```

✅ 
```cpp 
enum class Color {
    red   = 0,
    green = 1,
    blue  = 2,
}
```

### [?] Typy do reprezentacji jednostek SI
https://github.com/mpusz/mp-units
https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2020/p1935r2.html#revision-history

### Nazewnictwo for-range loop
Jak wiadomo jeżeli iterujemy przez pewną kolekcje i interesuje nas tylko zawartość tej kolekcji użyjemy for-range loop,
co jest dobre, ale żeby jeszcze lepiej to zrobić możemy nazywać iterator zgodnie z tym co będzie w nim przechowywane. 

❌ Typowy fragment kodu w którym bez szerszego kontekstu nie wiadomo o co chodzi 
```cpp
    float total_distance = 0.0f;
    
    for (auto x : vec){
        total_distance += sqrt(x.x * x.x + x.y * x.y)    
    }
```

✅ Wiadomo przez co iterujemy i wiadomo co jest 
```cpp
    float total_distance = 0.0f

    for (auto point : points){
        total_distance += sqrt(point.x * point.x + point.y * point.y)    
    }
```

### [?] Skróty w nazewnictwie 
Używamy tylko skrótów które znajdują się w słowniczku

### Używanie Namespace

### Nie używamy emoji jako nazw
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

## Szablony

### Koncepty
Koncepty zostały wprowadzone w C++20, jest to niejako typ parametru szablonu, służą one do polepszenia jakości błędów generowanych przez templaty, z tego każda klasa/funkcja używająca szablonu mająca pewne ograniczenia powinna być ograniczona konceptem

❌ Jednak informacja `'const char*' and 'const char*' to binary 'operator+'` nie jest taka czytelna
```cpp
template <typename T>
T add(T a, T b) {
    return a + b;
}

add("PIT", "BULL");
```

**Błędy**
> error: invalid operands of types 'const char*' and 'const char*' to binary 'operator+' 

✅ Dostaniemy czytelny błąd i jeszcze IDE będzie podkreślało błąd
```cpp
template <typename T>
concept Addable = requires(T a, T b) {
    { a + b } -> same_as<T>;
};


template <Addable T>
T add(T a, T b) {
    return a + b;
}

add("SUPER", "BLEDY!");
```

**Błędy**
> note: candidate: 'template<class T>  requires  Addable<T> T add(T, T)'

**Materiały**

[Cpp reference](https://en.cppreference.com/w/cpp/language/constraints)


## Enumy 

### Nie korzystamy z enum
**Powód**
Po to bozia C++ wymyśliła `enum class` żeby z tego korzystać. `enum` de facto tworzy zmienne globalne, `enum class` robi to podobnie tylko że robi to w przestrzeni nazw i klucze `enum class` są silnie typowane, to znaczy że trzeba specjalnie to zrzutować na jakiś typ liczbowy 

**Przykład**

*TODO* 
- Przykłady

❌
```cpp
```

✅ 
``` cpp 
```

**Materiały**

https://agrawalsuneet.github.io/blogs/enum-vs-enum-class-in-c++/

### Wartości enuma
**Powód**
Wartości enuma powinny informować o jego zastosowaniu, to znaczy że jeżeli enum będzie używany tylko jako typ i wartość nie będzie nas interesować to wtedy nie musimy pisać wartości bo jeszcze sie pomylimy i buga będziemy tydzień szukać.

Jeżeli interesuje nas wartość to zapisujemy ją we wszystkich kluczach enuma, najczęściej będziemy używać tego do konfiguracji czegoś więc zapis w hexie będzie najodpowiedniejszy  

**Przkyłady**

✅ Jest okej, raczej nie będzie interesować nas wartość tego. 
```cpp
    enum class Gender{
        male,
        female,
        🚀, 
    }
```


✅ Jest super, dokumentacja korzysta z liczb zapisanych w hexie, użyjemy tego elegancko
```cpp
    enum class BME280Mode{
        i2c          = 0x04,
        spi          = 0x10,
        two_wire_spi = 0x20,
    }
```


## Formatowanie

### Forward Declaration 

### Nawiasy wąsate
[K&R](https://en.wikipedia.org/wiki/Indentation_style#K&R)


### Odstępy  
    1 tabulacja - 4 spacje

### Struktura projektu
Używamy plików `.hpp` i `.cpp`


## Komentarze
Kod powinien być pisany w taki sposób żeby komentarze były zbędne.

**Wyjątek**
Czasmi oczywiście nie jest to możliwe.
Nie zawsze da się przekazać intencje używajac tylko kodu, szczególnie jeżeli jest to ekstremalnie zoptymalizowany fragment kodu lub implementacja jakiegoś interfejsu.

### Link do Manuala
**Powód**
Tworząc implementację drivera pewnego hardwaru będziemy chcieli w komentarzu zostawić link do datasheeta z którego korzystaliśmy, ma to na celu wyeliminowania problemów gdzie w komentarzu/dokumentacji jest strona z datasheeta ale nie pokrywa się z naszym datasheetem. Również zmniejsza czas na szukanie datasheetów. 

✅ Elegancko jest link do ukochanego BME280
```cpp
// Datasheet
// https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bme280-ds002.pdf

class BME280{
    ... 
};
```

### Klasy

### Dziedziczenie
Dziedziczenie OK, Dziedziczenie wielokrotne nie ok, mega rozpierdol itp.

### [?] Struct vs Class
**Powód**
Struct powinien służyć tylko wyłącznie jako DTO, jedyne metody jakie może mieć to akcesory do pól i proste metody obliczające swoje własności. Klasa z kolei jest opisem obiektu, ma to służyć rozróżnieniu z czym mamy doczynienia.

**Przykład**

struct
```cpp
    struct Vec2 {
        float x;
        float y;

        float length(){
            return sqrt(x*x + y*y);
        }
    }
```

class 
```cpp
    class BME280{
        void read_raw_data();
        void read_data(); 
    }
```

### Inicjaizaca pól 
Każda Klasa każde pole które może powinna zaincjalizować używając `{}` unikniemy w ten sposób dużej ilości pisania kodu w konstruktorze, jeżeli konstruktor przyjmuje wartości to powinny zostać przypisane w liscie inicjalizacyjnej


### Funkcja inicjalizacyjna
**Powód**
Każda klasa powinna funkcje `init()` w której zostaną wykonane wszysktie operacje ciężkie i takie które mogą wyrzucić błąd.

**Przykład**

❌ Obsługa błędów jest prawie niemożliwa, po utworzeniu instancji odrazu będzie chciała się zaincjalizować
```cpp
class BME280 {
    
    BME280(){
        if (read_id() != 0x80){
            std::cout << "BME280 connection failed";
            while(1);
        }
    }
}
```

✅ Można generycznie obsłużyć błąd, można powtórzyć inicjalizację przy błędzie.
```cpp
enum class I2CError{
    id_read = 0;
    some_error = 999;
}

class BME280 {
    
    BME280(){}

    //[?] czy nie robić tego w ten sposób bo wartość nas nie interesuje i tak I2CEror init()
    std::expected<bool,I2CEror> init(){
        if (read_id() != 0x80)
            return I2CError::id_read;
        return true;
    }
}
```

**Źródła**
https://google.github.io/styleguide/cppguide.html#Doing_Work_in_Constructors

### Akcja pełna enkapsulacja i akcesory

### [?] Używanie this->... 
**Powód**
    Jak przekazać informację o tym że zmienna jest polem klasy? rozwiązań jest kilka 

    - używanie `_` przed nazwą pola np. `_count`
    - `m` przed nazwą pola np. `m_count`
    - `this->` this->count
  
### Używaj private,protected i public
**Powód**
Definiuj dobrze interfejsy klas zgodnie z ich przeznaczeniem, jeżeli użycie metody poza klasą nie jest wymagane oznacz ją jako private


**Przykład**
```cpp

```

## Obsługa gita
Każdy branch mergowany do mastera musi posiadać:

- Stworzoną dokumentację nowych funkcjionalności
- Zostać zatwierdzonym przez 2 innych programistów
- Być zgodnym ze stylem pisania kodu


## Tworzenie dokumentacji
Dokumentacja powinna przedewszystkim istnieć i być aktualna. 
Brak dokumentacji czy jej niezgodność z rzeczywistością nie tylko powoduje spowolnienie prac ale przede wszystkim frustracje. 
A jak dobrze wiadomo jedyne osoby które mogą pisać dokumetancje to są ludzie którzy stworzyli pewny fragment kodu, 
z tego powodu te zasady mające na celu uprościć do minimum, ułatwić i unifikować tooling, sposób i styl pisania dokumentacji.

[Doxygen](https://www.doxygen.nl/)

### Brief funkcji 

## Inne 

### #pragma once
**Powód**
Każdy plik `.hpp` MUSI mieć coś żeby przeciwdziałać wielokrotnym dołączeniom, w C++ są 2 sposoby na zrobienie tego
używając `#define` guarda i `#pragma once`, ten pierwszy jest działający niezależnie od platformy, ale ma kilka problemów:

- Dużo pisania
- Możliwa podwójna definicja makra
- Żeby gwarantować unikalność trzeba by używać pełnej ścieżki do pliku
- Trzeba ręcznie uwzględnić zmiane makra po zmianie nazwy pliku 

`#pragma once` to wszystko rozwiązuje, robi wszystko automatycznie i bezpiecznie.
Jedyne problemy z `#pragma once` to że nie jest oficjalną częścią standardu więc nie musi działać wszędzie, ale u nas akurat działa :3

**Przykład**

❌
```cpp
    #ifndef DRIVERS_UART_LL_HPP_
    #define DRIVERS_UART_LL_HPP_

    ... 

    #endif // FILE_HPP
```

✅
```cpp
    #pragma once
    
    ...

```


## Testowanie

### Testy jednostkowe

### Hardware in the loop

### Data injection

## Design Paterny

### Maszyna stanu

### Singleton

### Factory 

### Decorator

## Tooling

## Lista rzeczy do zrobienia

**Krytyczne**
- Telekomendy
- System updateowania 
  - Bootloader
    - https://interrupt.memfault.com/blog/how-to-write-a-bootloader-from-scratch#why-you-may-need-a-bootloader
  - Lua
- File system
- Połączenie radiowe

**Dobrze zrobić**
- Zabezpieczenie połączenia