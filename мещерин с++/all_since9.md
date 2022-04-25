
- [Lection 9](#lection-9)
- [VI) Шаблоны](#vi-шаблоны)
- [VI) Шаблоны](#vi-шаблоны-1)
  - [6)](#6)
  - [7) Non-type template parameters](#7-non-type-template-parameters)
  - [# 10 Lection](#-10-lection)
  - [8) Variadic templates, переменное количество аргументов](#8-variadic-templates-переменное-количество-аргументов)
- [VII) Exceptions (исключения)](#vii-exceptions-исключения)
- [Lection 12](#lection-12)
- [VIII Аллокаторы](#viii-аллокаторы)
  - [Разновидности new](#разновидности-new)
  - [1) placement new](#1-placement-new)
  - [2) Перегрузка оператора new. Самостоятельное выделение памяти](#2-перегрузка-оператора-new-самостоятельное-выделение-памяти)
  - [3) Nothrow new](#3-nothrow-new)
  - [4) new_handler](#4-new_handler)
  - [5) `std::allocator<T>`](#5-stdallocatort)
  - [7) Пример нестандартного аллокатора](#7-пример-нестандартного-аллокатора)
- [Lection 13](#lection-13)
- [IX Контейнеры](#ix-контейнеры)
  - [1)  std::vector](#1--stdvector)
    - [б) [] и at()](#б--и-at)
    - [в) Конструкотры, деструктор, оператор =;](#в-конструкотры-деструктор-оператор-)
    - [г) swap для двух векторов.](#г-swap-для-двух-векторов)
- [Lection 14](#lection-14)
    - [1д)`vector<bool>`](#1дvectorbool)
  - [2)](#2)
    - [2а) `std::deque` [дэк]](#2а-stddeque-дэк)
    - [2б) `std::stack` [стэк]](#2б-stdstack-стэк)
    - [2в) `std::queue`](#2в-stdqueue)
  - [3) `std::list`](#3-stdlist)
  - [Стандартные вопросы на собесах про list](#стандартные-вопросы-на-собесах-про-list)
  - [3) std::forward_list](#3-stdforward_list)
  - [4) std::map](#4-stdmap)
- [Lection 15](#lection-15)
  - [5) std::set](#5-stdset)
  - [6) multimap](#6-multimap)
  - [7) std::unordered_map, std::unordered_set](#7-stdunordered_map-stdunordered_set)
- [X Итераторы](#x-итераторы)
- [XI Move-semantics. rvalue-arrays](#xi-move-semantics-rvalue-arrays)


# Lection 9
Это было
# VI) Шаблоны
# VI) Шаблоны
## 6)

```c++
template<class T>
void f(T& x);

int main() {
    int x; f(x);
    int& y; f(y);  тут & забудется
    int const z; f(z);  тут const сохраняется
    int x; f(x);
}
```
А если бы в функции принималось значение, а не ссылка, и конст и ссылки забывались бы, так как ни на что не влияют.

## 7) Non-type template parameters
``` c++
template<typename T, int n>
class array{

};
```
разные типы для массивов разной длины. Константа в классе не нужна и тогда например нельзя присвавивать массивы разных рамеров.

# 10 Lection
----
## 8) Variadic templates, переменное количество аргументов

```c
template<typename ... Args>
void f(Args ... arays);
```

```c
#include <iostream>
using std::cout;
using std::cin;
using std::endl;
void f() {}

template<typename Head, typename ... Tail>
void f(Head x, Tail ... t) {
    cout << sizeof...(t) << endl;
    cout << x << endl;
    f(t...);
}

int main() {

    f("sfasf", 14, 3.5123, 'r');
    return 0;
}
```
б) tuple - кортеж, обобщение пары
```c
tuple<int, double, std::string>
```

9) Сортировка
```c
template<typename T, typename Cmp=std::less<T>>
void sort(T* begin, T* end, Cmp cmp) {
    ...
}
cmp(*x, *y);

int main(){
    sort(...);
}
```
упр. Реализовать оператор less
(поддерживает круглые скобки от двух типов, маскирует примерение оператора меньше)
упр. от себя (написать сортировку для своей сортировки)

10) CRTP curious recurring template pattern

```c
template<typename T>
struct Base { 
    void f(){
        static_cast<T*>(this)->f();
    }
};

struct Derived:public Base<Derived> {
    void f(){
        cout << "I am derived\n";
    }
};

int main(){
    Derived d;
    Base<Derived>* b = &d;
    (*b).f();
}
```
Эта штука работает. 
Позволяет по сути самим реализовать аналог виртупальных функций. Это вызовет функцию наследника.

# VII) Exceptions (исключения)
1) Болтовня

Например если хотим выделить память, а памяти нет. Сразу падать в такой ситуации не хочется, так как программа может делать что-то важное, например поддерживать сервер.

dinamic_cast может не справиться скастовать.

1.2) Реализация

```
throw C();
```

это означает создать в стеке этот объект и рекурсивно выходить из облостей видимостей до тех пор, пока не встретится обработчик ошибки. Если его не будет, программа завершится.
``` c
try {
    f(x);
} catch (int x) {
    ...
} catch (MemoryError t) {
    ...
} catch ( ... ) {   //  такой синтаксис ловит любую ошибку

}
```
С такой штукой, runtime error не случится.
2) Разница между  RuntimeError и Exception

Не любую ошибку можно поймать. Не все ошибки являются исключениями. Например лазать в чужую память -- не исключение, а ошибка.

Exceptions может генерироваться динамическими объектами: new и dinamic_cast

Также не любой эксептион ошибка. Например можно так выходить их цикла, если вы извращенец.
3) Последовательность обработки

Не производится преведение типов кроме от наследника к родителю.
Выбирается первый подходящий, а все прочие игнорируются.


# Lection 12
# VIII Аллокаторы
Это нужно, чтобы вызвать конструктор на определенную область памяти. И туда положить новый объект.
## Разновидности new
## 1) placement new

new (p) Type(params);

p - указатель на память.

p->~T(); - явный вызов деструктора по адресу;

## 2) Перегрузка оператора new. Самостоятельное выделение памяти
``` c++
static void* operator new(size_t n) {
    ...
}
```
Это для всех типов сразу.
Там внутри можно вызвать старый new через ``` ::operator new() ```

Оператор new сначала вызывает функцию operator new, потом вызывает конструктор на память, которая выделилась. Перегрузить можно только первую часть.

Так можно перегрузить placement-new
``` c++
static void* operator new(size_t n, void* p) {
    return p;
}
```


Delete p 

Сначала вызывает деструктор той штуки, потом освобождает память. Тут можно перегрузить только вторую штуку. 
Аналогично работает с  `new[]` и `delete[]`  

Рекомендуется писать delete с кастомными параметрами для каждного new  кастомными параметрами, чтобы компилятор мог в случае исключения сам освободить память.
## 3) Nothrow new

```c++
new(std::nothrow) T();
```
это не исключение выкинет, а вернет nullptr

`#include <new>` бибилиотека с оригинальными new
`#include <memory>` бибилиотека для аллокаторов

## 4) new_handler

 Перехватывает ошибку, если не хватило памяти

 set::set_new_handler()

 Если не хватило памяти, а new_handler не nullptr, он в бесконечном цикле будет вызывать new_handler пока не хватит памяти или функция не станет nullptr-ом.


## 5) `std::allocator<T>`

```c
template<class T>
struct std::allocator<T> {

T* allocate(size_t n)  const {
    return (T*)new char[n*size_of(T)];
}
    
void deallocate(T* p, size_t n) const  {
    delete[] p;
}

template<typename ... Args>
void construct(T* p, const Args&& ... args) const {
    return new(p) T(args...);
}

void destroy(T* p)  const {
    p->~T();
}

}
```

По сути это оболочка над `new` и `delete`
`construct` и `destory` можно не реализовывать, они всегда одинковые, так что их за вас реализуют (вызовут реализацию из allocator traits)

## 7) Пример нестандартного аллокатора

StackAllocator<typename T>

Выделяет сразу много памяти и по очереди заполняет память значениями. Освобождением памяти он не занимается. Это экономит время работы раза в 2, так как операционная система не думает, где выделить памяти.

# Lection 13
# IX Контейнеры
хранят наботы других типов данных
## 1)  std::vector

Это динамический массив. Можно удалять и создавать элементы.
Поддерживает

a) push_back, pop_back, emplace_back

``` c++
#include <memory>
#include <iostream>
#include <exception>

template<class T, class Alloc=std::allocator<T>>
class vector{
    T* arr;
    size_t size;
    size_t cap;
    Alloc alloc;
    // using traits = std::allocator_traits<Alloc>;
public:
    vector() {
        size = 0;
        cap = 1;
        alloc = Alloc();
        arr = std::allocator_traits<Alloc>::allocate(alloc, cap);
    }
    ~vector() {
        for (size_t i = 0; i < size; i++)
        {
            std::allocator_traits<Alloc>::destroy(alloc, arr+i);
        }
        std::allocator_traits<Alloc>::deallocate(alloc, arr, cap);
    }
    void push_back(const T& x) {
        if (size < cap)
        {
            // arr[size++]=x;  // так нельзя, так как там лежить сырая память. У типа T может не быть констуктора по умолчанию.
            // new(arr+size) T(x);     // через аллокатор надо к памяти обращаться
            // alloc.construct(arr+size, x);   // может не быть реализован метод
            std::allocator_traits<Alloc>::construct(alloc, arr+size, x);
            size++;
            return;
        }
        T* newarr = std::allocator_traits<Alloc>::allocate(alloc, cap*2);
        for (size_t i = 0; i < cap; i++)
        {
            std::allocator_traits<Alloc>::construct(alloc, newarr+i, arr[i]); // в новом с++ это делается по-другому
        }
        std::allocator_traits<Alloc>::construct(alloc, newarr+cap, x);
        ++size;
        for (size_t i = 0; i < cap; i++)
        {
            std::allocator_traits<Alloc>::destroy(alloc, arr+i);
        }
        // std::allocator_traits<Alloc>::deallocate(alloc, arr, cap);
        arr = newarr;
        cap<<=1;        
    }
    T& operator[](int i) {
        if (i<size)
        {
            return arr[i];
        }
        else {
            throw std::out_of_range("Index " + std::to_string(i) + " is not valid");
            return arr[0];
        }
    }
};
```

pop_back(); - удаление элемента из конца. Например можно, если занято только 1/4, уменьшить в два раза.
```c++
template<class ... Args>
emplace_back(const Args ... args); // принимает аругменты конструктора для объектов, которые хранятся в контейнере. Это делается чтобы не делать лишнюю копию.
```
### б) [] и at()
```c++
T& operator[](int i) {
    return arr[i];
}
const T& operator[](int i) const {  // это для const векторов перегрузка
    ...
}

T& at(int i) {  // медленнее работает, но безопаснее
    if (i<size)
    {
        return arr[i];
    }
    else {
        throw std::out_of_range("Index " + std::to_string(i) + " is not valid");
        return arr[0];
    }
}
```
это не noexcept функции, так как может быть undefined behavior, который может бросить исключение.

### в) Конструкотры, деструктор, оператор =;

Нужно ли копировать аллокаторы?
Тут вызывают специальную функцию, которая либо дает копию, либо что-то другое специальное. Например новый аллокатор генерировать.
```traits::select_on_container_copy_construction(a);```

### г) swap для двух векторов.
Оно переставляет местами указатели, а не занимается копированием.

Есть size(), compacity(),

shrink to fit() освобождает остальную память. 

есть front и back, возарящающие ссылки на первый и последний элемент, чтобы не писать size-i.

```c++
vector<int> v(100);
sizeof(v);
```
Это константа, так как элементы храняться не в полях, а в динамической памяти.


# Lection 14
### 1д)`vector<bool>`

присвоение не возвращяет `bool`, а специальный класс, который симулирует `bool`, но присваевает не себе, а элементы массива.
```c++
BoolProxy operator[](int i) {      // это friend класса вектор
    return BoolProxy(i); 
}
```

Это экономит память, но работает очень долго.

## 2) 

### 2а) `std::deque` [дэк]

Работает как вектор, но можно в начало вставлять со средним временем работы константным.
### 2б) `std::stack` [стэк]

Это надстройка над дэком

### 2в) `std::queue`

`priority_queue`

Это очередь. Надстройка над `deque`, которая добавляет в конец, а вынимает из начала.

Приоритетная очередь хранит в бинарной куче по ключам элементы. Достает минимум.

## 3) `std::list`

Двусвязный список. Хранятся элементы и ссылки на прошлый и предыдущий. Умеет удалять и добавлять из любого места элементы, объединять листы.
Обращение по индексу работает только за линейное время.
```c++
size
push_back
pop_back
insert(std::list<T>::const_iterator& it, const T& x);   // вставляет элемент
insert(std::list<T>::const_iterator& it, std::list<T>::const_iterator& begin, std::list<T>::const_iterator& end);   // вставляет диапазон
splice () // тоже вставляет диапазон, но без копирования, так что очень быстро
```
Получить итератор можно например через `begin` или `end`.

## Стандартные вопросы на собесах про list
```c++
list.reverse(begin, end) {
    old begin = begin;
    prev = begin;
    begin++;
    while (begin != end) {
        next = begin + 1;
        begin.next = prev;
        prev = begin;
        begin = next;
    } 
    old begin.next = nullptr;
}
```

## 3) std::forward_list
Только вперед можно итерироваться.

У аллокаторов есть стандартный метод 
```c++
template<class U>
std::allocator<U> rebind(); 
```

## 4) std::map
(dict) в питоне
Это ассоциативный контейнер. По ключу хранятся значения.
Это красно-черные деревья.

есть обращение по ключу квадратными скобками. Если нет элемента по квадратным скобкам, он создаст элемент по умолчанию в мапе и вернет его.
Чтобы не делать так, есть метод at() который кидает исключение и у контстантых мапов работает только он.

```c++
std::map<Key, Value, Cmp=std::less, Alloc=std::allocator<const Key, Value>>
m.insert(std::make_pair(a, b));
чтобы не писать названия классов a и b при создании пары.
m.erase(m)
```

# Lection 15
## 5) std::set
Хранит только ключи упорядоченно.\
Методы
``` c++
 s.find(); // возвращает либо итератор, либо ссылку на end()
 s.find() != s.end() // проверяет принадлежность
 s.count() // считает количество элементов. Полезнее для multimap
```

## 6) multimap

Нельзя обращаться по ключу, так как может быть несколько значений с таким ключем.
```c++
lower_bound(key);
upper_bound(key);
```
можно итерироваться по множеству с таким значением.

## 7) std::unordered_map, std::unordered_set

Это работает на hash таблицах.  Работает в среднем за константное время. По таким можно итерироваться
```c++
[] at, 
insert
erase
find
count
std::unordered_map<Key, Value, Hash=std::hash<Key>, Equality, Alloc=std::Allocator<const Key, Value>>
```
Нельзя менять Key, чтобы не ломалось дерево.\
Для своих классов нужно писать свой hash и сравнение.

# X Итераторы
Структура,  позволяющая проходиться по подряд идущим элементам. Есть метод возьми следующий элемент.

``` c++
std::map<int, int>::iterator it = nullptr;
*it;    // всегда можно
++it;   // тоже
```

1)_Классификация итераторов:
- InputIterator (множно разименововать, нельзя менять, можно пройтись только один раз вперед)(iostream)
- ForwardInterator(можно много раз пойти вперед)(forward_list, unordered_...)
- BidirectionalIteraror(можно ходить в обе стороны)(list, map, set)
- RandomAccessIterator( поддерживает смещение оп индексу на любое количество элементов)(vector, deque)

Еще итератор может быть OutputInterator или нет. То есть можно менять значение по нему или нет.

3)  const, reverse - iterator

нельзя менять значение под итератором 

``` c++
for(map<std::string, int>iterator it = m.begin(); it != m.end(); ++it) {
    ...
}

m.cbegin(); // константный итератор на начало
rbegin();   // reverse iterator. Можно увеличивать, идя в начало пока не достигнем rend()
rcbegin(); // тоже есть.


template<class BidirectionalIterator>
class std::reverse_iterator {
    Он реализует методы этого итератора, делая противоположные действия с обычным итератором.

    operator++() {
        return --it;
    }
}
```

4) Написание своего итератора

std::iterator_traits<Iterator>
Обращаться нужно к итераторам тольк через этот класс, который доопределит то, что не определено в самом классе итератора.

5) std::distance, std::advance()
```c++
template<class Iter, >
size_t distance(Iter first, Iter last) {
    distamce_impl<std::iterator_traits<Iter>::iterator_category>(first, last); //dist impl шаблон у которого есть специализации для разных типов итераторов и таким образом компилируется нужный код.
}
```
6) \<algorithm>

Много алгоритмов для последовательностей. 
Работают с итераторами.

```c++
std::copy(Interator<T> first, Interator<T> last,  Interator<T> output);
С vector так не получится.
Нужен back_insert_iterator, чтобы по итератору делать push_back в vector. Присваивание делает pushback и ++. Просто ++ ничего не делает.
std::back_inserter(container) -> back_insert_iterator от container  
```

# XI Move-semantics. rvalue-arrays

Пришло в с++11
1) мотиворовка


По умолчанию присваивание копирует.
```c++
vector<int> v = vector<int>(100);
```
Это будет 2 раза создавать массив. Тут можно обойтись, но если передавать в функцию, все равно будет копирование, если хотим использовать на лету созданный объект.
swap тоже будет долго работать для векторов, если копировать.

1) std::move

```c++
#include <utility>
void swap(T& x, T& y) {
    T t = std::move(x);
    x = std::move(y);
    y = std::move(t);
}
```
Так не будет просиходить копирование, если можно его избежать, как в случае с массивом например.

Создается такой объект, которые при присвоении без копирования инициализирует объект. При этом объект от которого создается становится не валидным.

Правда в с++11 инициализация временным объектом в любом случае работает по-умному, без копирования.

3) move-конструктор, move-assignment оператор

Эти функции вызываются при использовании move.

```c++
C(const C& x) {

} // так было раньше. Тут придется копировать все данные.

C(C&& x) {  // move-конструктор. Может принимать ссылку на временный объект. У такого можно забрать ресурсы. 

}
аналогично
С& operator=(C&& x) {

}
```
std::move просто приводит к типу &&.

**Правило пяти** - если есть нетривиально оприеделенное что-то из конструктор, оператор копирования, деструктор, move-конструктор, move-assignment оператор. Тогда нужно писать все пять.

Конструктор move по умалчанию пытается переместить все поля.

4) Реализация std::move

Идейно это static_cast к rvalue type (C&&)

Сначала нужно снять все амперсанты.
```c++
template<typename T>
typename std::remove_reference<T>::type&& move(T&& x) {
    return static_cast<typename std::remove_reference<T>::type&&>x
}
```

5) rvalue-ссылки


```c++
type&&

int x = 5;
// int&& y = x; так нельзя в явном виде
int&& z = 5; так можно. С временными объектами связываться.

int& t = z; так можно.
// int&& p = z; нельзя,так как z не временный объект.
Можно
int&& p = move(z);
```

То есть rvalue ссылки можно инициализировать времеными объектами или объектами, которые так себя ведут.

6) виды value

lvalue - это например идентификаторы (название переменной), ссылки, ++it. У таких штук всегда можно брать ссылку.

rvalue - это литералы(5, 'a', "tr"). Тут нельзя брать сслыку.

xvalue(expired value просроченное выражение) - это такие штуки, которые на самом деле не временные, но их скастовали к таким.
Например результат работы функции type&& f(), static_cast<type&&>.

7) Универсальные ссылки

```c++
template<typename T>
typename std::remove_reference<T>::type&& move(T&& x) {
    return static_cast<typename std::remove_reference<T>::type&&>x
}
```
Тут аргумент функции - костыль. Если шаблонная функция принимет T&& x, как шаблонный аргумент. Тогда если вызывается f(lvalue), подставляется не T&&, а T&

reference collapsing.

Выражение type & && сворачивается до type &. 

Конструкция
```
template<type> type&&
``` 
называется универсальной ссылкой, так как может принимать и lvalue и rvalue.

8) std::forward

```c++
template<typename T>
void log_push_back(T&& x) {
    cout << "";
    v.push_back(x); // нужно как-то передавать x таким же образом, как мы его получили. Написать move нельзя, так как к rvalue такое лучше не примернять.
}

// так будет например если мы используем аллокатор

construct(Args&& ... args) {
    new (p) T( std::forward<Args>(args) ... );
}

```
forward будет работать, как move для lvalue и ничего не делать с rvalue. То есть осущствлять прямую передачу. Отдавать в том же типе данных, в котором пролучили.

9) Реализация std::forward
```c++
template<typename T>
T&& forward(std::remove_reference_t<T>& x) {
    return static_cast<T&&>(x);
}
f(args);

```

У vector<> есть emplace_back(Args&&... args)
Он вызывает конструктор и создает объект с такими параметрами.
10)std::move_if_noexcept

Когда происходит реалокация в векторе, все элементы перемещаются в новый кусок памяти. В с++03 пришлось бы при переносе вектора векторов пришлось бы копировать каждый внутренний вектор. А в с++11 можно перенос осуществлять. Но если в процессе такого переноса выскочило исключение, мы испортили половину старого массива, а новый еще не готов.

Тогда нужно переносить, если не может вылететь исключение при конструировании.

11) return value optimization

Если объект в функции создается, чтобы потом его вернуть в качестве результата, компилятор сразу будет создавать этот объект в том месте, куда его нужно будет вернуть. Если не получилось, применяется автоматически move. Поэтому самом писать его не нужно, иначе точно не будет rvo.


12) reference qualifiers

Позволяет обозначить, что функция только для объектов типа rvalue или только для lvalue.

```c++
void f(...) & (или &&) {

}
```
