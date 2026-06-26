# МДК 01.04 Системное программирование — Подготовка к экзамену

## 📖 Теоретические вопросы

### 1. Подсистемы управления ресурсами

<details>
<summary><b>Показать ответ</b></summary>

Операционная система управляет аппаратными и программными ресурсами через несколько ключевых подсистем. 

**Подсистема управления процессором (планировщик)** решает, какой процесс или поток получит время CPU, используя кванты времени. 

**Подсистема управления памятью** выделяет и освобождает оперативную память, организует виртуальную память и работает с файлом подкачки. 

**Подсистема ввода-вывода** общается с устройствами через драйверы, буферизует данные и обрабатывает аппаратные прерывания. 

**Подсистема файловой системы** отвечает за создание, удаление и организацию файлов и каталогов, а также управляет правами доступа. 

Наконец, **подсистема межпроцессного взаимодействия (IPC)** предоставляет механизмы для обмена данными между процессами, такие как каналы, разделяемая память и семафоры. 

В Windows эти подсистемы реализованы в ядре и работают в привилегированном режиме ядра.

</details>

---

### 2. Управление процессами

<details>
<summary><b>Показать ответ</b></summary>

**Процесс** — это экземпляр выполняющейся программы, которому операционная система выделяет изолированное адресное пространство (код, данные, стек, куча) и системные ресурсы. 

Процесс проходит через несколько состояний: создание (New), готовность к выполнению (Ready), непосредственное выполнение (Running), ожидание события (Waiting) и завершение (Terminated). 

В Windows процессы создаются с помощью функции `CreateProcess`, которая загружает исполняемый файл в память и создает главный поток. 

Завершение может быть нормальным (через `ExitProcess`) или принудительным (через `TerminateProcess`). ОС также позволяет получать информацию о процессе, например, код его завершения или время выполнения.

</details>

---

### 3. Управление потоками

<details>
<summary><b>Показать ответ</b></summary>

**Поток** — это минимальная единица выполнения внутри процесса. В отличие от процессов, потоки одного процесса разделяют общее адресное пространство и системные ресурсы, но имеют собственный стек, регистры и контекст выполнения. 

Это делает создание и переключение потоков гораздо быстрее и экономичнее по памяти. 

Основные операции включают создание потока (например, через `CreateThread` в Windows), его завершение и синхронизацию. 

Для предотвращения конфликтов при доступе к общим данным используются механизмы синхронизации: мьютексы, семафоры, события и критические секции.

</details>

---

### 4. Параллельная обработка потоков

<details>
<summary><b>Показать ответ</b></summary>

**Параллельная обработка** подразумевает одновременное выполнение нескольких потоков на разных ядрах процессора. 

При этом возникают классические проблемы параллелизма: состояние гонки (когда результат зависит от порядка выполнения потоков), взаимная блокировка или дедлок (когда потоки бесконечно ждут ресурсы друг друга) и голодание (когда поток не может получить доступ к ресурсу). 

Для решения этих проблем применяются средства синхронизации. 

**Мьютекс** гарантирует, что только один поток может находиться в критической секции. 

**Семафор** ограничивает количество потоков, одновременно работающих с ресурсом. 

**Критическая секция** — это облегченный мьютекс, работающий только в пределах одного процесса.

</details>

---

### 5. Создание процессов и потоков

<details>
<summary><b>Показать ответ</b></summary>

**Процессы** обеспечивают полную изоляцию: у каждого свое адресное пространство, поэтому падение одного процесса не влияет на другие, но обмен данными между ними сложен и требует механизмов IPC. 

**Потоки** же делят память процесса, что делает обмен данными тривиальным (через общие переменные), но снижает надежность: ошибка в одном потоке может обрушить весь процесс. 

Процессы используются, когда нужна стабильность и изоляция (например, вкладки браузера), а потоки — когда требуется интенсивный обмен данными и высокая скорость отклика (например, фоновая загрузка файлов в приложении).

</details>

---

### 6. Обмен данными между процессами. Передача сообщений

<details>
<summary><b>Показать ответ</b></summary>

**Межпроцессное взаимодействие (IPC)** необходимо, так как процессы изолированы друг от друга. 

Основные механизмы IPC в Windows включают каналы (потоки байтов), именованные каналы (каналы с именем в системе), разделяемую память (общий участок RAM, самый быстрый способ), очереди сообщений и сетевые сокеты. 

Передача сообщений может быть синхронной (отправитель блокируется до получения подтверждения) или асинхронной (отправитель продолжает работу, данные буферизуются). 

Выбор механизма зависит от требуемой скорости, объема данных и того, связаны ли процессы между собой.

</details>

---

### 7. Анонимные и именованные каналы

<details>
<summary><b>Показать ответ</b></summary>

**Анонимные каналы** создаются функцией `CreatePipe` и представляют собой однонаправленный поток байтов. Они не имеют имени в файловой системе, существуют только во время работы создавшего их процесса и используются преимущественно для связи между родительским и дочерним процессом. 

**Именованные каналы** (создаются через `CreateNamedPipe`) имеют уникальное имя в системе (в формате `\\.\pipe\имя`), могут быть двунаправленными и позволяют общаться несвязанным между собой процессам. 

Сервер создает именованный канал и ждет подключения через `ConnectNamedPipe`, а клиент подключается к нему как к обычному файлу через `CreateFile`.

</details>

---

### 8. Сетевое программирование сокетов

<details>
<summary><b>Показать ответ</b></summary>

**Сокет** — это абстракция конечной точки сетевого соединения. Для работы с сетью в Windows используется библиотека WinSock. 

Процесс начинается с инициализации через `WSAStartup`. Сервер создает сокет, привязывает его к IP-адресу и порту (`bind`), переходит в режим прослушивания (`listen`) и принимает подключения (`accept`). 

Клиент создает сокет и подключается к серверу (`connect`). После установки соединения обе стороны обмениваются данными через `send` и `recv`. 

По завершении работы сокет закрывается (`closesocket`), а библиотека выгружается (`WSACleanup`).

</details>

---

### 9. Динамически подключаемые библиотеки (DLL)

<details>
<summary><b>Показать ответ</b></summary>

**DLL** — это модуль, содержащий код и данные, который может использоваться несколькими программами одновременно. Это экономит память и позволяет обновлять функционал без перекомпиляции основных программ. 

Подключить DLL можно неявно (через LIB-файл, библиотека грузится автоматически при старте EXE) или явно (через `LoadLibrary` и `GetProcAddress` во время работы). 

При экспорте функций из DLL на C++ обязательно используется `extern "C"`, чтобы запретить компилятору искажать имена функций (name mangling), иначе клиент не сможет найти их по точному имени.

</details>

---

### 10. Сервисы

<details>
<summary><b>Показать ответ</b></summary>

**Сервис (или служба)** — это фоновое приложение Windows, которое запускается автоматически при загрузке системы, работает без пользовательского интерфейса и управляется Диспетчером управления службами (SCM). 

Сервисы обычно запускаются от имени системных учетных записей. Разработка сервиса требует реализации функции `ServiceMain` (основная логика) и обработчика управляющих команд (например, остановки). 

Управление сервисами (создание, запуск, остановка, удаление) осуществляется через консольную утилиту `sc` или графический интерфейс `services.msc`.

</details>

---

### 11. Виртуальная память. Выделение памяти процессам

<details>
<summary><b>Показать ответ</b></summary>

**Виртуальная память** — это механизм, при котором каждый процесс видит собственное непрерывное адресное пространство, которое не привязано напрямую к физической RAM. 

Память делится на страницы (обычно по 4 КБ). При обращении к странице, которой нет в RAM, происходит исключение Page Fault: процессор передает управление ОС, которая находит свободную физическую страницу, загружает в нее данные (с диска или обнуляет) и обновляет таблицу страниц. 

В Windows для ручного управления виртуальной памятью используются функции `VirtualAlloc` (выделение с флагами RESERVE и COMMIT), `VirtualQuery` (получение информации о регионе) и `VirtualFree` (освобождение).

</details>

---

### 12. Работа с буфером экрана

<details>
<summary><b>Показать ответ</b></summary>

**Буфер экрана консоли** — это область памяти, где Windows хранит матрицу символов и их цветовых атрибутов. 

Прямой вывод через `cout` или `system("cls")` вызывает мерцание и тормоза, так как каждый вызов требует системного прерывания, а cls вообще запускает внешний процесс. 

Для плавной анимации используются функции WinAPI: `GetStdHandle` (получение дескриптора консоли), `FillConsoleOutputCharacter` (быстрая пакетная запись символов в буфер) и `FillConsoleOutputAttribute` (установка цвета). 

Для полного устранения мерцания применяется двойная буферизация: кадр рисуется во втором невидимом буфере (`CreateConsoleScreenBuffer`), а затем мгновенно переключается на экран (`SetConsoleActiveScreenBuffer`).

</details>

---

## 💻 Практические задачи

### Задача 1: Управлять процессами (Аналог fork/exec в Windows)

<details>
<summary><b>Показать решение</b></summary>

**Условие:** Создать родительский процесс, который запускает дочерний процесс.

#### Как создать проект в Visual Studio
1. **Файл → Создать → Проект** → **"Консольное приложение"** (C++)
2. Имя проекта: `ProcessDemo`
3. Вставляю код в `.cpp` файл, собираю через `Ctrl+Shift+B`, запускаю через `Ctrl+F5`

```cpp
// Подключаю windows.h — здесь лежат все основные функции WinAPI для работы с процессами
#include <windows.h>
// Подключаю iostream для удобного вывода текста в консоль
#include <iostream>

using namespace std;

int main() {
    // Устанавливаю русскую кодировку, чтобы консоль корректно отображала кириллицу
    SetConsoleCP(1251);
    SetConsoleOutputCP(1251);

    cout << "[Родитель] Мой PID: " << GetCurrentProcessId() << endl;

    // Готовлю структуры, которые требует функция CreateProcess
    // STARTUPINFO хранит информацию о главном окне нового процесса (размер, позиция и т.д.)
    STARTUPINFOA si;
    // Обнуляю структуру и задаю размер — это обязательное требование WinAPI
    ZeroMemory(&si, sizeof(si));
    si.cb = sizeof(si);

    // PROCESS_INFORMATION сюда Windows вернет дескрипторы и идентификаторы созданного процесса
    PROCESS_INFORMATION pi;
    ZeroMemory(&pi, sizeof(pi));

    cout << "[Родитель] Запускаю дочерний процесс (Блокнот)...\n";

    // Создаю дочерний процесс
    // NULL — имя модуля (берем из командной строки)
    // "notepad.exe" — командная строка запуска
    // NULL, NULL — атрибуты безопасности по умолчанию
    // FALSE — дочерний процесс НЕ наследует дескрипторы родителя
    // 0 — флаги создания (обычный режим)
    // NULL, NULL — используем окружение и текущую папку родителя
    // &si, &pi — указатели на структуры для настройки и получения результата
    if (CreateProcessA(
        NULL,
        (LPSTR)"notepad.exe",
        NULL, NULL,
        FALSE,
        0,
        NULL, NULL,
        &si, &pi
    )) {
        cout << "[Родитель] Процесс успешно создан! Его PID: " << pi.dwProcessId << endl;

        // Жду, пока пользователь сам не закроет Блокнот
        // pi.hProcess — это дескриптор процесса, INFINITE — ждать бесконечно
        WaitForSingleObject(pi.hProcess, INFINITE);
        cout << "[Родитель] Дочерний процесс завершился.\n";

        // Обязательно закрываю дескрипторы, иначе будет утечка ресурсов ядра
        CloseHandle(pi.hProcess);
        CloseHandle(pi.hThread);
    } else {
        // Если CreateProcess вернула false, вывожу код ошибки
        cerr << "[Родитель] Ошибка запуска! Код: " << GetLastError() << endl;
    }

    cout << "Нажмите Enter для выхода...";
    cin.get();
    return 0;
} 
```
</details>
---

### Задача 2: Создавать и управлять потоками

<details>
<summary><b>Показать решение</b></summary>

**Условие:** Создать несколько потоков для параллельных вычислений.

#### Как создать проект в Visual Studio
1. **Файл → Создать → Проект** → **"Консольное приложение"** (C++)
2. Имя проекта: `ThreadDemo`
3. Вставляю код в `.cpp` файл, собираю через `Ctrl+Shift+B`, запускаю через `Ctrl+F5`

```cpp
// windows.h нужен для CreateThread, WaitForMultipleObjects и Sleep
#include <windows.h>
// iostream для вывода результатов
#include <iostream>

using namespace std;

// Создаю структуру, чтобы передать в поток сразу несколько параметров
struct ThreadData {
    int id;             // Номер потока для логов
    int start;          // С какого числа начинать сумму
    int end;            // По какое число считать
    long long* result;  // Указатель на ячейку, куда поток запишет ответ
};

// Функция, которую будет выполнять каждый поток
// Она обязательно должна иметь такой прототип: DWORD WINAPI и параметр LPVOID
DWORD WINAPI ThreadFunc(LPVOID param) {
    // Привожу безтиповый указатель обратно к моему типу ThreadData
    ThreadData* data = (ThreadData*)param;
    
    cout << "[Поток " << data->id << "] Считаю от " << data->start << " до " << data->end << "\n";
    
    long long sum = 0;
    // Обычный цикл сложения чисел в заданном диапазоне
    for (int i = data->start; i <= data->end; i++) {
        sum += i;
    }
    
    // Записываю результат по указателю в общий массив
    *data->result = sum;
    
    cout << "[Поток " << data->id << "] Готово. Сумма: " << sum << "\n";
    return 0;
}

int main() {
    SetConsoleCP(1251);
    SetConsoleOutputCP(1251);

    const int THREAD_COUNT = 4;
    const int MAX_VALUE = 1000000;
    int chunk = MAX_VALUE / THREAD_COUNT;

    // Массивы для хранения дескрипторов потоков, их параметров и результатов
    HANDLE hThreads[THREAD_COUNT];
    ThreadData threadData[THREAD_COUNT];
    long long results[THREAD_COUNT] = {0};

    cout << "Запускаю " << THREAD_COUNT << " потоков...\n";

    // В цикле создаю потоки
    for (int i = 0; i < THREAD_COUNT; i++) {
        threadData[i].id = i + 1;
        threadData[i].start = i * chunk + 1;
        // Последний поток забирает весь "хвост", если число не делится нацело
        threadData[i].end = (i == THREAD_COUNT - 1) ? MAX_VALUE : (i + 1) * chunk;
        threadData[i].result = &results[i];

        // Создаю поток: передаю функцию ThreadFunc и указатель на данные
        hThreads[i] = CreateThread(NULL, 0, ThreadFunc, &threadData[i], 0, NULL);
    }

    // Жду завершения ВСЕХ потоков одновременно
    // hThreads — массив дескрипторов, TRUE — ждать все, INFINITE — без таймаута
    WaitForMultipleObjects(THREAD_COUNT, hThreads, TRUE, INFINITE);

    // Собираю общую сумму и закрываю дескрипторы
    long long total = 0;
    for (int i = 0; i < THREAD_COUNT; i++) {
        total += results[i];
        CloseHandle(hThreads[i]); // Освобождаю ресурсы ядра
    }

    cout << "\nИтоговая сумма от 1 до " << MAX_VALUE << ": " << total << "\n";
    cout << "Нажмите Enter...";
    cin.get();
    return 0;
}
```
</details>

---

### Задача 3: Реализовать параллельную обработку данных

<details>
<summary><b>Показать решение</b></summary>

**Условие:** Обработать массив данных параллельно в нескольких потоках.

#### Как создать проект в Visual Studio
1. **Файл → Создать → Проект** → **"Консольное приложение"** (C++)
2. Имя проекта: `ParallelProcessing`

```cpp
#include <windows.h>
#include <iostream>
#include <ctime>

using namespace std;

const int ARRAY_SIZE = 1000000;
const int THREAD_COUNT = 4;
int* dataArray;
bool* isPrime;

// Критическая секция — аналог мьютекса, но быстрее. 
// Нужна, чтобы потоки не писали в консоль одновременно и не портили вывод.
CRITICAL_SECTION printCS;

// Функция проверки числа на простоту
bool checkPrime(int n) {
    if (n < 2) return false;
    if (n == 2) return true;
    if (n % 2 == 0) return false;
    // Проверяю делители только до квадратного корня из n для скорости
    for (int i = 3; i * i <= n; i += 2) {
        if (n % i == 0) return false;
    }
    return true;
}

// Функция потока
DWORD WINAPI PrimeChecker(LPVOID param) {
    int threadId = *(int*)param;
    
    int chunk = ARRAY_SIZE / THREAD_COUNT;
    int start = threadId * chunk;
    int end = (threadId == THREAD_COUNT - 1) ? ARRAY_SIZE : (threadId + 1) * chunk;
    
    // Захватываю критическую секцию перед выводом
    EnterCriticalSection(&printCS);
    cout << "[Поток " << threadId << "] Обрабатываю диапазон " << start << " - " << end << "\n";
    LeaveCriticalSection(&printCS); // Сразу освобождаю, чтобы не тормозить другие потоки
    
    // Каждый поток пишет в СВОЮ часть массива, поэтому здесь синхронизация не нужна
    for (int i = start; i < end; i++) {
        isPrime[i] = checkPrime(dataArray[i]);
    }
    return 0;
}

int main() {
    SetConsoleCP(1251);
    SetConsoleOutputCP(1251);

    // Инициализирую критическую секцию до создания потоков
    InitializeCriticalSection(&printCS);

    // Выделяю память в куче
    dataArray = new int[ARRAY_SIZE];
    isPrime = new bool[ARRAY_SIZE](); // () гарантирует, что все значения станут false

    srand(time(NULL));
    for (int i = 0; i < ARRAY_SIZE; i++) {
        dataArray[i] = rand() % 10000 + 1;
    }

    HANDLE hThreads[THREAD_COUNT];
    int threadIds[THREAD_COUNT];

    for (int i = 0; i < THREAD_COUNT; i++) {
        threadIds[i] = i;
        hThreads[i] = CreateThread(NULL, 0, PrimeChecker, &threadIds[i], 0, NULL);
    }

    WaitForMultipleObjects(THREAD_COUNT, hThreads, TRUE, INFINITE);

    int primeCount = 0;
    for (int i = 0; i < ARRAY_SIZE; i++) {
        if (isPrime[i]) primeCount++;
    }

    cout << "\nНайдено простых чисел: " << primeCount << " из " << ARRAY_SIZE << "\n";

    // Удаляю критическую секцию и освобождаю память
    DeleteCriticalSection(&printCS);
    for (int i = 0; i < THREAD_COUNT; i++) CloseHandle(hThreads[i]);
    delete[] dataArray;
    delete[] isPrime;

    cout << "Нажмите Enter...";
    cin.get();
    return 0;
}
```
</details>

---
---

### Задача 3: Реализовать параллельную обработку данных

<details>
<summary><b>Показать решение</b></summary>

**Условие:** Обработать массив данных параллельно в нескольких потоках.

#### Как создать проект в Visual Studio
1. **Файл → Создать → Проект** → **"Консольное приложение"** (C++)
2. Имя проекта: `ParallelProcessing`

```cpp
#include <windows.h>
#include <iostream>
#include <ctime>

using namespace std;

const int ARRAY_SIZE = 1000000;
const int THREAD_COUNT = 4;
int* dataArray;
bool* isPrime;

// Критическая секция — аналог мьютекса, но быстрее. 
// Нужна, чтобы потоки не писали в консоль одновременно и не портили вывод.
CRITICAL_SECTION printCS;

// Функция проверки числа на простоту
bool checkPrime(int n) {
    if (n < 2) return false;
    if (n == 2) return true;
    if (n % 2 == 0) return false;
    // Проверяю делители только до квадратного корня из n для скорости
    for (int i = 3; i * i <= n; i += 2) {
        if (n % i == 0) return false;
    }
    return true;
}

// Функция потока
DWORD WINAPI PrimeChecker(LPVOID param) {
    int threadId = *(int*)param;
    
    int chunk = ARRAY_SIZE / THREAD_COUNT;
    int start = threadId * chunk;
    int end = (threadId == THREAD_COUNT - 1) ? ARRAY_SIZE : (threadId + 1) * chunk;
    
    // Захватываю критическую секцию перед выводом
    EnterCriticalSection(&printCS);
    cout << "[Поток " << threadId << "] Обрабатываю диапазон " << start << " - " << end << "\n";
    LeaveCriticalSection(&printCS); // Сразу освобождаю, чтобы не тормозить другие потоки
    
    // Каждый поток пишет в СВОЮ часть массива, поэтому здесь синхронизация не нужна
    for (int i = start; i < end; i++) {
        isPrime[i] = checkPrime(dataArray[i]);
    }
    return 0;
}

int main() {
    SetConsoleCP(1251);
    SetConsoleOutputCP(1251);

    // Инициализирую критическую секцию до создания потоков
    InitializeCriticalSection(&printCS);

    // Выделяю память в куче
    dataArray = new int[ARRAY_SIZE];
    isPrime = new bool[ARRAY_SIZE](); // () гарантирует, что все значения станут false

    srand(time(NULL));
    for (int i = 0; i < ARRAY_SIZE; i++) {
        dataArray[i] = rand() % 10000 + 1;
    }

    HANDLE hThreads[THREAD_COUNT];
    int threadIds[THREAD_COUNT];

    for (int i = 0; i < THREAD_COUNT; i++) {
        threadIds[i] = i;
        hThreads[i] = CreateThread(NULL, 0, PrimeChecker, &threadIds[i], 0, NULL);
    }

    WaitForMultipleObjects(THREAD_COUNT, hThreads, TRUE, INFINITE);

    int primeCount = 0;
    for (int i = 0; i < ARRAY_SIZE; i++) {
        if (isPrime[i]) primeCount++;
    }

    cout << "\nНайдено простых чисел: " << primeCount << " из " << ARRAY_SIZE << "\n";

    // Удаляю критическую секцию и освобождаю память
    DeleteCriticalSection(&printCS);
    for (int i = 0; i < THREAD_COUNT; i++) CloseHandle(hThreads[i]);
    delete[] dataArray;
    delete[] isPrime;

    cout << "Нажмите Enter...";
    cin.get();
    return 0;
}
```
</details>

---
---

### Задача 4: Создавать процессы и потоки

<details>
<summary><b>Показать решение</b></summary>

**Условие:** Создать программу, демонстрирующую разницу между процессами и потоками.

#### Как создать проект в Visual Studio
1. **Файл → Создать → Проект** → **"Консольное приложение"** (C++)
2. Имя проекта: `ProcessAndThread`

```cpp
#include <windows.h>
#include <iostream>

using namespace std;

// Функция для потока
DWORD WINAPI ThreadWorker(LPVOID param) {
    int threadId = *(int*)param;
    // GetCurrentProcessId() и GetCurrentThreadId() — WinAPI функции для получения ID
    cout << "[Поток " << threadId << "] PID: " << GetCurrentProcessId() 
         << ", TID: " << GetCurrentThreadId() << "\n";
    
    for (int i = 0; i < 3; i++) {
        Sleep(100); // Приостанавливаю поток на 100 мс
        cout << "[Поток " << threadId << "] Итерация " << i + 1 << "\n";
    }
    return 0;
}

int main() {
    SetConsoleCP(1251);
    SetConsoleOutputCP(1251);

    cout << "=== ЧАСТЬ 1: ПОТОКИ ===\n";
    cout << "Главный поток, PID: " << GetCurrentProcessId() << "\n\n";

    HANDLE hThreads[2];
    int ids[2] = {1, 2};

    for (int i = 0; i < 2; i++) {
        // Создаю два потока внутри текущего процесса
        hThreads[i] = CreateThread(NULL, 0, ThreadWorker, &ids[i], 0, NULL);
    }

    // Жду завершения обоих потоков
    WaitForMultipleObjects(2, hThreads, TRUE, INFINITE);
    for (int i = 0; i < 2; i++) CloseHandle(hThreads[i]);

    cout << "\n=== ЧАСТЬ 2: ПРОЦЕССЫ ===\n";
    // Запускаю два независимых процесса (Калькулятор)
    for (int i = 0; i < 2; i++) {
        STARTUPINFOA si = {sizeof(si)};
        PROCESS_INFORMATION pi;

        if (CreateProcessA(NULL, (LPSTR)"calc.exe", NULL, NULL, FALSE, 0, NULL, NULL, &si, &pi)) {
            cout << "Запущен процесс с PID: " << pi.dwProcessId << "\n";
            // Жду закрытия калькулятора
            WaitForSingleObject(pi.hProcess, INFINITE);
            CloseHandle(pi.hProcess);
            CloseHandle(pi.hThread);
        }
    }

    cout << "\nВсе завершено. Нажмите Enter...";
    cin.get();
    return 0;
}
```
</details>

---
---

### Задача 5: Организовать обмен данными между процессами

<details>
<summary><b>Показать решение</b></summary>

**Условие:** Два процесса обмениваются данными через разделяемую память.

#### Как создать проекты в Visual Studio
Создаю **два проекта в одном решении**: `SharedWriter` и `SharedReader`. Собираю оба, запускаю сначала Writer, потом Reader.

**Код Writer (Писатель)**

<pre><code class="language-cpp">#include &lt;windows.h&gt;
#include &lt;iostream&gt;
#include &lt;cstring&gt;

using namespace std;

int main() {
    SetConsoleCP(1251);
    SetConsoleOutputCP(1251);

    // Создаю объект разделяемой памяти в файле подкачки
    // INVALID_HANDLE_VALUE означает, что физический файл на диске не создается
    // "MySharedMem" — уникальное имя, по которому второй процесс найдет эту память
    HANDLE hMap = CreateFileMapping(
        INVALID_HANDLE_VALUE, NULL, PAGE_READWRITE, 0, 4096, "MySharedMem"
    );

    // Отображаю эту память в адресное пространство своего процесса
    // pBuf — это обычный указатель, но ведет он в общую память
    LPVOID pBuf = MapViewOfFile(hMap, FILE_MAP_ALL_ACCESS, 0, 0, 4096);

    cout &lt;&lt; "[Writer] Память создана. Отправляю данные...\n";
    
    // Записываю строку в общую память
    strcpy((char*)pBuf, "Привет от процесса Writer!");

    cout &lt;&lt; "[Writer] Данные отправлены. Жду 5 секунд...\n";
    Sleep(5000); // Даю время второму процессу прочитать

    // Освобождаю ресурсы
    UnmapViewOfFile(pBuf);
    CloseHandle(hMap);
    
    cout &lt;&lt; "Нажмите Enter...";
    cin.get();
    return 0;
}</code></pre>

**Код Reader (Читатель)**

<pre><code class="language-cpp">#include &lt;windows.h&gt;
#include &lt;iostream&gt;

using namespace std;

int main() {
    SetConsoleCP(1251);
    SetConsoleOutputCP(1251);

    // Открываю существующую разделяемую память по имени
    HANDLE hMap = OpenFileMapping(FILE_MAP_ALL_ACCESS, FALSE, "MySharedMem");
    if (!hMap) {
        cerr &lt;&lt; "Память не найдена! Запустите Writer.\n";
        return 1;
    }

    // Отображаю в свой процесс
    LPVOID pBuf = MapViewOfFile(hMap, FILE_MAP_ALL_ACCESS, 0, 0, 4096);

    // Читаю строку из общей памяти
    cout &lt;&lt; "[Reader] Получено: " &lt;&lt; (char*)pBuf &lt;&lt; "\n";

    UnmapViewOfFile(pBuf);
    CloseHandle(hMap);
    
    cout &lt;&lt; "Нажмите Enter...";
    cin.get();
    return 0;
}</code></pre>

</details>

---

### Задача 6: Работать с анонимными и именованными каналами

<details>
<summary><b>Показать решение</b></summary>

**Условие:** Система из трех процессов. Анонимный канал для связи A и B, именованный для A и C.

#### Как создать проекты в Visual Studio
Создаю два проекта: `Coordinator` (процессы A и B) и `OddProcessor` (процесс C). Запускаю сначала OddProcessor, потом Coordinator.

**Код Coordinator (Процессы A и B)**

<pre><code class="language-cpp">#include &lt;windows.h&gt;
#include &lt;iostream&gt;

using namespace std;

// Функция для Процесса B (работает в потоке)
DWORD WINAPI ProcessB(LPVOID param) {
    HANDLE* pipes = (HANDLE*)param;
    HANDLE hRead = pipes[0];
    HANDLE hWrite = pipes[1];

    int number;
    DWORD bytesRead;
    // Читаю из анонимного канала, пока родитель не закроет его
    while (ReadFile(hRead, &amp;number, sizeof(int), &amp;bytesRead, NULL) &amp;&amp; bytesRead &gt; 0) {
        int square = number * number;
        WriteFile(hWrite, &amp;square, sizeof(int), &amp;bytesRead, NULL);
    }
    return 0;
}

int main() {
    SetConsoleCP(1251);
    SetConsoleOutputCP(1251);

    // 1. Создаю анонимный канал (CreatePipe)
    HANDLE hRead, hWrite;
    SECURITY_ATTRIBUTES sa = { sizeof(SECURITY_ATTRIBUTES), NULL, TRUE }; // TRUE = наследовать
    CreatePipe(&amp;hRead, &amp;hWrite, &amp;sa, 0);

    // 2. Запускаю Процесс B в потоке
    HANDLE hThread = CreateThread(NULL, 0, ProcessB, new HANDLE[2]{hRead, hWrite}, 0, NULL);

    // 3. Подключаюсь к Процессу C через ИМЕНОВАННЫЙ канал
    cout &lt;&lt; "Подключение к Процессу C...\n";
    HANDLE hNamed = CreateFileA("\\\\.\\pipe\\coord_fifo", GENERIC_WRITE | GENERIC_READ, 0, NULL, OPEN_EXISTING, 0, NULL);
    
    if (hNamed == INVALID_HANDLE_VALUE) {
        cerr &lt;&lt; "Процесс C не запущен!\n";
        return 1;
    }

    // 4. Отправляю числа
    int numbers[] = {5, 12, 7, 4, 9};
    DWORD written, bytesRead;

    for (int num : numbers) {
        if (num % 2 == 0) {
            // Четное -&gt; в анонимный канал (Процессу B)
            WriteFile(hWrite, &amp;num, sizeof(int), &amp;written, NULL);
            int res;
            ReadFile(hRead, &amp;res, sizeof(int), &amp;bytesRead, NULL);
            cout &lt;&lt; "Число " &lt;&lt; num &lt;&lt; " (четное). Квадрат от B: " &lt;&lt; res &lt;&lt; "\n";
        } else {
            // Нечетное -&gt; в именованный канал (Процессу C)
            WriteFile(hNamed, &amp;num, sizeof(int), &amp;written, NULL);
            long long res;
            ReadFile(hNamed, &amp;res, sizeof(long long), &amp;bytesRead, NULL);
            cout &lt;&lt; "Число " &lt;&lt; num &lt;&lt; " (нечетное). Факториал от C: " &lt;&lt; res &lt;&lt; "\n";
        }
    }

    // 5. Освобождаю ресурсы
    CloseHandle(hWrite);
    CloseHandle(hRead);
    CloseHandle(hNamed);
    WaitForSingleObject(hThread, INFINITE);
    CloseHandle(hThread);

    cout &lt;&lt; "Нажмите Enter...";
    cin.get();
    return 0;
}</code></pre>

**Код OddProcessor (Процесс C)**

<pre><code class="language-cpp">#include &lt;windows.h&gt;
#include &lt;iostream&gt;

using namespace std;

long long Factorial(int n) {
    long long res = 1;
    for (int i = 2; i &lt;= n; i++) res *= i;
    return res;
}

int main() {
    SetConsoleCP(1251);
    SetConsoleOutputCP(1251);

    // Создаю именованный канал как сервер
    HANDLE hNamed = CreateNamedPipeA("\\\\.\\pipe\\coord_fifo", PIPE_ACCESS_DUPLEX, PIPE_TYPE_BYTE | PIPE_WAIT, 1, 256, 256, 0, NULL);
    
    cout &lt;&lt; "Ожидание подключения...\n";
    ConnectNamedPipe(hNamed, NULL); // Блокируюсь, пока не подключится клиент
    cout &lt;&lt; "Клиент подключился!\n";

    int number;
    DWORD bytesRead, written;

    while (ReadFile(hNamed, &amp;number, sizeof(int), &amp;bytesRead, NULL) &amp;&amp; bytesRead &gt; 0) {
        long long fact = Factorial(number);
        WriteFile(hNamed, &amp;fact, sizeof(long long), &amp;written, NULL);
    }

    DisconnectNamedPipe(hNamed);
    CloseHandle(hNamed);
    
    cout &lt;&lt; "Нажмите Enter...";
    cin.get();
    return 0;
}</code></pre>

</details>

---

### Задача 7: Создавать и использовать DLL библиотеки

<details>
<summary><b>Показать решение</b></summary>

**Условие:** Создать DLL с функциями и протестировать её через явную загрузку.

#### Как создать проекты в Visual Studio
1. Проект **"Динамически подключаемая библиотека (DLL)"** -> имя `MathDLL`. В начало кода добавить `#include "pch.h"`.
2. Проект **"Консольное приложение"** -> имя `MathClient`.
3. Скопировать `MathDLL.dll` из папки Debug DLL в папку Debug Клиента.

**Код DLL (MathDLL.cpp)**

<pre><code class="language-cpp">#include "pch.h" // Обязательно для Visual Studio
#include &lt;windows.h&gt;

// extern "C" отключает искажение имен, __declspec(dllexport) добавляет в таблицу экспорта
#define EXPORT extern "C" __declspec(dllexport)

EXPORT int Add(int a, int b) {
    return a + b;
}

EXPORT long long Factorial(int n) {
    if (n &lt; 0) return 0;
    long long res = 1;
    for (int i = 2; i &lt;= n; i++) res *= i;
    return res;
}

BOOL APIENTRY DllMain(HMODULE hModule, DWORD ul_reason_for_call, LPVOID lpReserved) {
    return TRUE;
}</code></pre>

**Код Клиента (MathClient.cpp)**

<pre><code class="language-cpp">#include &lt;windows.h&gt;
#include &lt;iostream&gt;

using namespace std;

// Объявляю типы указателей на функции, которые буду искать в DLL
typedef int (*AddFunc)(int, int);
typedef long long (*FactFunc)(int);

int main() {
    SetConsoleCP(1251);
    SetConsoleOutputCP(1251);

    // Явно загружаю DLL в память процесса
    HINSTANCE hDll = LoadLibrary("MathDLL.dll");
    if (!hDll) {
        cerr &lt;&lt; "DLL не найдена!\n";
        return 1;
    }

    // Получаю адреса функций по их точным именам
    AddFunc pAdd = (AddFunc)GetProcAddress(hDll, "Add");
    FactFunc pFact = (FactFunc)GetProcAddress(hDll, "Factorial");

    if (pAdd &amp;&amp; pFact) {
        cout &lt;&lt; "Add(5, 3) = " &lt;&lt; pAdd(5, 3) &lt;&lt; "\n";
        cout &lt;&lt; "Factorial(5) = " &lt;&lt; pFact(5) &lt;&lt; "\n";
    }

    // Выгружаю DLL из памяти
    FreeLibrary(hDll);
    
    cout &lt;&lt; "Нажмите Enter...";
    cin.get();
    return 0;
}</code></pre>

</details>

---

### Задача 8: Разрабатывать системные сервисы/демоны

<details>
<summary><b>Показать решение</b></summary>

**Условие:** Создать простой Windows-сервис.

#### Как создать проект в Visual Studio
1. **Файл → Создать → Проект** → **"Консольное приложение"** (C++)
2. Имя: `SimpleService`
3. Установить и запустить сервис нужно через командную строку **от имени администратора**.

<pre><code class="language-cpp">#include &lt;windows.h&gt;
#include &lt;iostream&gt;
#include &lt;fstream&gt;

using namespace std;

SERVICE_STATUS ServiceStatus;
SERVICE_STATUS_HANDLE hStatus;
bool bRunning = true;

// Функция записи в лог-файл
void WriteLog(const char* msg) {
    ofstream log("C:\\service_log.txt", ios::app);
    log &lt;&lt; msg &lt;&lt; "\n";
}

// Обработчик команд от Диспетчера служб (SCM)
void WINAPI CtrlHandler(DWORD control) {
    if (control == SERVICE_CONTROL_STOP) {
        bRunning = false;
        ServiceStatus.dwCurrentState = SERVICE_STOPPED;
    }
    SetServiceStatus(hStatus, &amp;ServiceStatus);
}

// Главная функция сервиса
void WINAPI ServiceMain(DWORD argc, LPTSTR* argv) {
    hStatus = RegisterServiceCtrlHandler("MySimpleService", CtrlHandler);
    
    ServiceStatus.dwServiceType = SERVICE_WIN32_OWN_PROCESS;
    ServiceStatus.dwCurrentState = SERVICE_START_PENDING;
    ServiceStatus.dwControlsAccepted = SERVICE_ACCEPT_STOP;
    SetServiceStatus(hStatus, &amp;ServiceStatus);

    ServiceStatus.dwCurrentState = SERVICE_RUNNING;
    SetServiceStatus(hStatus, &amp;ServiceStatus);
    WriteLog("Сервис запущен");

    // Основной цикл работы
    while (bRunning) {
        WriteLog("Сервис работает...");
        Sleep(5000); // Работает каждые 5 секунд
    }

    WriteLog("Сервис остановлен");
    ServiceStatus.dwCurrentState = SERVICE_STOPPED;
    SetServiceStatus(hStatus, &amp;ServiceStatus);
}

int main() {
    // Таблица сервисов — связывает имя сервиса с функцией ServiceMain
    SERVICE_TABLE_ENTRY ServiceTable[] = {
        {"MySimpleService", ServiceMain},
        {NULL, NULL}
    };
    
    // Передаю управление Диспетчеру служб Windows
    StartServiceCtrlDispatcher(ServiceTable);
    return 0;
}</code></pre>

**Команды для установки (в CMD от админа):**
- `sc create MySimpleService binPath= "C:\путь\к\SimpleService.exe"`
- `sc start MySimpleService`
- `sc delete MySimpleService`

</details>

---

### Задача 9: Управлять виртуальной памятью процесса

<details>
<summary><b>Показать решение</b></summary>

**Условие:** Выделить блоки памяти, показать их адреса и освободить.

#### Как создать проект в Visual Studio
1. **Файл → Создать → Проект** → **"Консольное приложение"** (C++)
2. Имя: `VirtualMemory`

<pre><code class="language-cpp">#include &lt;windows.h&gt;
#include &lt;iostream&gt;
#include &lt;iomanip&gt;

using namespace std;

int main() {
    SetConsoleCP(1251);
    SetConsoleOutputCP(1251);

    const size_t BLOCK_SIZE = 1024 * 1024; // 1 МБ
    void* blocks[3];

    cout &lt;&lt; "Выделяю 3 блока по 1 МБ:\n";
    for (int i = 0; i &lt; 3; i++) {
        // VirtualAlloc: NULL = адрес выберет ОС, MEM_COMMIT | MEM_RESERVE = выделить и зафиксировать
        blocks[i] = VirtualAlloc(NULL, BLOCK_SIZE, MEM_COMMIT | MEM_RESERVE, PAGE_READWRITE);
        // Вывожу адрес в шестнадцатеричном формате
        cout &lt;&lt; "Блок " &lt;&lt; i &lt;&lt; ": " &lt;&lt; hex &lt;&lt; blocks[i] &lt;&lt; dec &lt;&lt; "\n";
    }

    cout &lt;&lt; "\nРасстояние между блоками:\n";
    for (int i = 0; i &lt; 2; i++) {
        // Привожу указатели к числам, чтобы вычесть их друг из друга
        uintptr_t addr1 = reinterpret_cast&lt;uintptr_t&gt;(blocks[i]);
        uintptr_t addr2 = reinterpret_cast&lt;uintptr_t&gt;(blocks[i + 1]);
        cout &lt;&lt; "Разница: " &lt;&lt; (addr2 - addr1) &lt;&lt; " байт\n";
    }

    cout &lt;&lt; "\nОсвобождаю память:\n";
    for (int i = 0; i &lt; 3; i++) {
        // VirtualFree с флагом MEM_RELEASE полностью освобождает регион
        VirtualFree(blocks[i], 0, MEM_RELEASE);
        cout &lt;&lt; "Блок " &lt;&lt; i &lt;&lt; " освобожден\n";
    }

    cout &lt;&lt; "Нажмите Enter...";
    cin.get();
    return 0;
}</code></pre>

</details>

---

### Задача 10: Реализовать вывод в буфер экрана/консоли

<details>
<summary><b>Показать решение</b></summary>

**Условие:** Создать анимацию "дождя из символов" без мерцания.

#### Как создать проект в Visual Studio
1. **Файл → Создать → Проект** → **"Консольное приложение"** (C++)
2. Имя: `MatrixRain`
3. Запуск через `Ctrl+F5`, выход по клавише **Esc**.

<pre><code class="language-cpp">#include &lt;windows.h&gt;
#include &lt;cstdlib&gt;
#include &lt;ctime&gt;
#include &lt;iostream&gt;

using namespace std;

char getRandomSymbol() {
    // Возвращаю случайный печатный символ (ASCII от 33 до 126)
    return 33 + rand() % 94;
}

int main() {
    srand(time(NULL));
    system("mode con cols=80 lines=25");
    Sleep(100);

    // Получаю дескриптор стандартного вывода (консоли)
    HANDLE hConsole = GetStdHandle(STD_OUTPUT_HANDLE);

    // Узнаю размеры буфера экрана
    CONSOLE_SCREEN_BUFFER_INFO csbi;
    GetConsoleScreenBufferInfo(hConsole, &amp;csbi);
    int width = csbi.dwSize.X;
    int height = csbi.dwSize.Y;

    // Скрываю курсор, чтобы он не мигал во время анимации
    CONSOLE_CURSOR_INFO cursorInfo;
    GetConsoleCursorInfo(hConsole, &amp;cursorInfo);
    cursorInfo.bVisible = FALSE;
    SetConsoleCursorInfo(hConsole, &amp;cursorInfo);

    // Параметры капель
    const int DROPS = 40;
    int x[40], y[40], speed[40], counter[40];
    char symbol[40];

    for (int i = 0; i &lt; DROPS; i++) {
        x[i] = rand() % width;
        y[i] = rand() % height;
        symbol[i] = getRandomSymbol();
        speed[i] = 1 + rand() % 3;
        counter[i] = 0;
    }

    COORD pos;
    DWORD written;
    bool running = true;

    while (running) {
        // Выход по Esc
        if (GetAsyncKeyState(VK_ESCAPE) &amp; 0x8000) running = false;

        // Быстрая очистка экрана через WinAPI (вместо system("cls"))
        pos.X = 0; pos.Y = 0;
        FillConsoleOutputCharacter(hConsole, ' ', width * height, pos, &amp;written);
        FillConsoleOutputAttribute(hConsole, 7, width * height, pos, &amp;written);

        // Обновление и отрисовка капель
        for (int i = 0; i &lt; DROPS; i++) {
            counter[i]++;
            if (counter[i] &gt;= speed[i]) {
                counter[i] = 0;
                y[i]++;
                if (y[i] &gt;= height) {
                    x[i] = rand() % width;
                    y[i] = 0;
                    symbol[i] = getRandomSymbol();
                }
            }
            // Вывод символа
            pos.X = (SHORT)x[i];
            pos.Y = (SHORT)y[i];
            FillConsoleOutputCharacter(hConsole, symbol[i], 1, pos, &amp;written);
            // Установка ярко-зеленого цвета (атрибут 10)
            FillConsoleOutputAttribute(hConsole, 10, 1, pos, &amp;written);
        }

        // Возвращаю курсор в начало, чтобы консоль не скроллилась
        pos.X = 0; pos.Y = 0;
        SetConsoleCursorPosition(hConsole, pos);
        Sleep(33); // Задержка для ~30 FPS
    }

    // Восстанавливаю курсор перед выходом
    cursorInfo.bVisible = TRUE;
    SetConsoleCursorInfo(hConsole, &amp;cursorInfo);
    return 0;
}</code></pre>

</details>
