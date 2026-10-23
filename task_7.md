---
title: "Практическая работа №7: Основные типы данных и  безопасность типов в Dart"
discipline: "Разработка мобильных приложений"
tags:
  [
    dart,
    flutter,
    data-types,
    sound-null-safety,
    collections,
    records,
    pattern-matching,
  ]
---

# Практическая работа: Основные типы данных в языке Dart (Flutter Foundation)

> [!NOTE]
> **Цель работы:** Освоить систему типов языка Dart в контексте кроссплатформенной разработки на Flutter. Изучить числа (`int`, `double`, `num`), строки (`String`), логический тип (`bool`), базовые коллекции (`List`, `Set`, `Map`), механизм  безопасности null (Null Safety), а также современные возможности Dart 3+ (Records, Pattern Matching, Collection if/for).

---

## 📋 Содержание

1. [Архитектура системы типов Dart и Null Safety](#1-архитектура-системы-типов-dart-и-null-safety)
2. [Тема 1. Числовые типы: int, double, num и математические операции](#тема-1-числовые-типы-int-double-num-и-математические-операции)
3. [Тема 2. Строки (String), интерполяция и кодовые точки (Runes)](#тема-2-строки-string-интерполяция-и-кодовые-точки-runes)
4. [Тема 3. Логический тип (bool) и строгость условий](#тема-3-логический-тип-bool-и-строгость-условий)
5. [Тема 4. Списки (List): списковые включения и spread-операторы](#тема-4-списки-list-списковые-включения-и-spread-операторы)
6. [Тема 5. Множества (Set) и Словари (Map): парсинг JSON-структур](#тема-5-множества-set-и-словари-map-парсинг-json-структур)
7. [Тема 6. Null, Records (кортежи) и Pattern Matching](#тема-6-null-safety-records-кортежи-и-pattern-matching)
8. [50 Смешанных заданий по обработке данных (Flutter Logic Focus)](#50-смешанных-заданий-по-обработке-данных)

---

## 1. Архитектура системы типов Dart и  Null Safety

Dart — строго типизированный язык со статической проверкой типов. Благодаря механизму **Null Safety** (безопасности типов) переменные по умолчанию не могут принимать значение `null`, если они явно не объявлены как допускающие null (Nullable).

### Иерархия объявления переменных

```
                  ┌────────────────────────┐
                  │      Object? (Top)     │
                  └───────────┬────────────┘
                              │
               ┌──────────────┴──────────────┐
               ▼                             ▼
        ┌─────────────┐               ┌─────────────┐
        │   Object    │               │    Null     │
        └──────┬──────┘               └─────────────┘
   ┌───────────┼───────────┬───────────┐
   ▼           ▼           ▼           ▼
┌─────┐    ┌────────┐  ┌───────┐  ┌────────┐
│ num │    │ String │  │ bool  │  │ List<T>│ ...
└──┬──┘    └────────┘  └───────┘  └────────┘
 ┌─┴──────┐
 ▼        ▼
int     double
```

### Модификаторы объявления

| Ключевое слово | Назначение                      | Время инициализации               | Изменяемость значения                         |
| :------------- | :------------------------------ | :-------------------------------- | :-------------------------------------------- |
| `var`          | Автовывод типа компилятором     | В момент выполнения               | Можно изменять значение того же типа          |
| `final`        | Неизменяемая константа          | В рантайме (при первом обращении) | Нельзя переприсвоить                          |
| `const`        | Константа времени компиляции    | При компиляции программы          | Нельзя переприсвоить; глубокая неизменяемость |
| `late`         | Отложенная инициализация        | До первого чтения переменной      | Можно (если не указан `final`)                |
| `dynamic`      | Отключение статической проверки | В рантайме                        | Допускает присвоение любых типов              |

---

## Тема 1. Числовые типы: int, double, num и математические операции

В Dart числа представлены двумя основными подтипами абстрактного класса `num`:

- `int` — целые 64-битные числа на мобильных платформах (нативного компилятора AOT).
- `double` — 64-битные числа с плавающей точкой (стандарт IEEE 754).
- `num` — общий предок; используется, когда переменная может быть как целым, так и дробным числом.

### Пример кода

```dart
void main() {
  int screenWidthPx = 1080;
  double devicePixelRatio = 2.625;

  // Безопасный парсинг строк
  String rawInput = "450.50";
  double? parsedPrice = double.tryParse(rawInput);

  // Округление и форматирование
  double itemPrice = 129.897;
  print("Округление до целого: ${itemPrice.round()}"); // 130
  print("Округление вниз: ${itemPrice.floor()}");       // 129
  print("Округление вверх: ${itemPrice.ceil()}");        // 130
  print("До 2 знаков: ${itemPrice.toStringAsFixed(2)}"); // "129.90"

  // Целочисленное деление (~/) и остаток (%)
  int totalMinutes = 135;
  int hours = totalMinutes ~/ 60; // 2 часа
  int minutes = totalMinutes % 60; // 15 минут
  print("Время: $hours ч. $minutes мин.");
}
```

### Задания для закрепления

1. **Конвертер валют:** Напишите функцию, принимающую сумму в рублях (`int`) и курс доллара (`double`), возвращающую строку с результатом, отформатированным строго до 2 знаков после запятой.

<img width="1920" height="1080" alt="2026-10-23_08-46-50" src="https://github.com/user-attachments/assets/9049e46d-8241-4be2-995c-4429c7e7a951" />

```dart
void main() {
 
  print(convertCurrency(10000, 90.5)); 
  print(convertCurrency(5000, 92.25)); 
  print(convertCurrency(100, 0)); 
}

String convertCurrency(int rubles, double usdRate) {
  if (usdRate <= 0) {
    return "Ошибка: неверный курс";
  }
  
  double dollars = rubles / usdRate;
  
  return dollars.toStringAsFixed(2);
}
```

2. **Безопасный калькулятор возраста:** Примите ввод пользователя в виде `String`. Используя `int.tryParse()`, верните возраст или `-1`, если пользователь ввел некорректный текст.

<img width="1920" height="1080" alt="2026-10-23_08-49-53" src="https://github.com/user-attachments/assets/0af33cee-7381-4a2e-8337-302a0b02c0e5" />

```dart
void main() {
  // Примеры использования
  print(parseAge("25"));
  print(parseAge(" 30 ")); 
  print(parseAge("двадцать пять"));
  print(parseAge("-5")); 
}


int parseAge(String input) {
 
  int? age = int.tryParse(input.trim());
  
  return age ?? -1; 
}
```

3. **Расчет плотности пикселей (DP):** Реализуйте формулу `dp = px / density` с использованием оператора целочисленного или дробного деления.

<img width="1920" height="1080" alt="2026-10-23_08-52-26" src="https://github.com/user-attachments/assets/4b64a12c-2d4c-4d70-a211-37c7a0ba58d0" />

```dart
void main() {
  
  print(calculateDp(100, 2.75)); 
  print(calculateDp(50, 1.0)); 
  print(calculateDp(10, 0)); 
}


double calculateDp(double px, double density) {
  if (density == 0) {
    return 0.0; 
  }
  return px / density;
}
```

4. **Побитовые флаги сенсоров:** Используя побитовые операторы `&`, `|` и `<<`, закодируйте состояние трех мобильных датчиков в одну переменную `int`.

<img width="1920" height="1080" alt="2026-10-23_08-55-32" src="https://github.com/user-attachments/assets/7e41d4f6-17ec-4a00-bee6-73306c5aab12" />

```dart
const int SENSOR_ACCELEROMETER = 1 << 0;
const int SENSOR_GYROSCOPE = 1 << 1;
const int SENSOR_MAGNETOMETER = 1 << 2;
void main() {
  int sensors = encodeSensors(accel: true, gyro: false, magnet: true);
  print('Код состояния: $sensors'); 
  print('Двоичный вид: ${sensors.toRadixString(2).padLeft(3, '0')}'); 
  print('Акселерометр: ${isSensorActive(sensors, SENSOR_ACCELEROMETER)}'); 
  print('Гироскоп: ${isSensorActive(sensors, SENSOR_GYROSCOPE)}'); 
  print('Магнитометр: ${isSensorActive(sensors, SENSOR_MAGNETOMETER)}'); 
}
int encodeSensors({required bool accel, required bool gyro, required bool magnet}) {
  int flags = 0;
  if (accel) flags |= SENSOR_ACCELEROMETER;
  if (gyro) flags |= SENSOR_GYROSCOPE;
  if (magnet) flags |= SENSOR_MAGNETOMETER;
  return flags;
}

bool isSensorActive(int flags, int sensor) {
  return (flags & sensor) != 0;
}
```

5. **Проверка четности без остатка от деления:** Проверьте, является ли целое число четным, используя побитовое умножение `(number & 1) == 0`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/87b32152-1281-4148-9ed5-5ed7c32763f7" />

```dart
void main() {
  
  print('Число 4 четное? ${isEven(4)}'); 
  print('Число 7 четное? ${isEven(7)}'); 
  print('Число 0 четное? ${isEven(0)}');
  print('Число -2 четное? ${isEven(-2)}'); 
}


bool isEven(int number) {
  
  return (number & 1) == 0;
}
```

---

## Тема 2. Строки (String), интерполяция и кодовые точки (Runes)

Строка `String` в Dart представляет собой неизменяемую (immutable) последовательность символов в кодировке UTF-16.

### Особенности работы со строками

- Интерполяция: `$variable` для простых переменных и `${expression}` для выражений.
- Многострочные литералы: тройные кавычки `'''многострочный текст'''`.
- Raw-строки: `r'C:\flutter\bin'` (символ обратного слэша не экранируется).
- Кодовые точки (Runes): используются для корректной обработки 32-битных эмодзи и спецсимволов.

### Пример кода

```dart
void main() {
  String appTitle = "Marketplace";
  double discount = 0.15;

  // Интерполяция выражений
  String promoBanner = "В приложении $appTitle скидка составляет ${(discount * 100).toInt()}%!";
  print(promoBanner);

  // Методы трансформации
  String rawPhone = "  +7 (999) 000-11-22 \n";
  String cleanPhone = rawPhone.trim().replaceAll(RegExp(r'[^0-9+]'), '');
  print("Чистый номер: $cleanPhone"); // "+79990001122"

  // Дополнение нулями (Padding)
  int receiptNumber = 42;
  String formattedReceipt = receiptNumber.toString().padLeft(6, '0');
  print("Чек: #$formattedReceipt"); // "#000042"

  // Работа с Runes (эмодзи)
  String statusMessage = "Статус заказа: Доставлен 🚀";
  print("Длина в UTF-16: ${statusMessage.length}");
  print("Количество символов (Runes): ${statusMessage.runes.length}");
}
```

### Задания для закрепления

1. **Маскирование email:** Напишите функцию, преобразующую `ivanov.flutter@gmail.com` в `i************r@gmail.com`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0e43c0bf-bba9-490f-8e20-26ef19426295" />

```dart
String maskEmail(String email) {
  final parts = email.split('@');
  if (parts.length != 2) return email;
  
  final name = parts[0];
  final domain = parts[1];
  
  if (name.length <= 2) return email;
  
  final firstChar = name[0];
  final lastChar = name[name.length - 1];
  final stars = '*' * (name.length - 2);
  
  return '$firstChar$stars$lastChar@$domain';
}

void main() {
  print(maskEmail('ivanov.flutter@gmail.com'));
}
```

2. **Валидатор префикса ссылки:** Проверьте, начинается ли переданная строка с безопасного протокола `https://` с помощью метода `.startsWith()`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7089834f-f884-4438-a572-57dbcc1e31ee" />

```dart
bool isSecureLink(String url) {
  return url.startsWith('https://');
}

void main() {
  print(isSecureLink('https://dart.dev'));
  print(isSecureLink('http://dart.dev'));
}
```

3. **Генератор инициалов (Monogram):** Примите строку из двух слов («Александр Пушкин») и верните заглавные буквы «АП».

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/42aeb589-ee0b-4bf7-a97d-107313fd43aa" />

```dart
String getInitials(String fullName) {
  final words = fullName.trim().split(RegExp(r'\s+'));
  if (words.length < 2) return '';
  
  return words[0][0].toUpperCase() + words[1][0].toUpperCase();
}

void main() {
  print(getInitials('Александр Пушкин'));
}
```

4. **Разбор CSV-строки корзины:** Разбейте строку вида `"Яблоко,3,150.5"` методом `.split(',')` и извлеките наименование, количество и стоимость.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/27f6a912-b572-4d14-9aa9-1fe5e0e04da3" />

```dart
Map<String, dynamic> parseCartItem(String csv) {
  final parts = csv.split(',');
  
  return {
    'name': parts[0],
    'quantity': int.tryParse(parts[1]) ?? 0,
    'price': double.tryParse(parts[2]) ?? 0.0,
  };
}

void main() {
  final item = parseCartItem('Яблоко,3,150.5');
  print('Наименование: ${item['name']}');
  print('Количество: ${item['quantity']}');
  print('Стоимость: ${item['price']}');
}
```

5. **Подсчет реальной длины строки с эмодзи:** Напишите утилиту, которая считает точное количество символов в тексте с комбинированными эмодзи (через `.runes`).

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/498821c6-17b5-45c5-9d01-ba386630e051" />

```dart
int getRealLength(String text) {
  return text.runes.length;
}

void main() {
  const textWithEmoji = 'Привет 👨‍👩‍👧‍👦!';
  print(getRealLength(textWithEmoji));
}
```

---

## Тема 3. Логический тип (bool) и строгость условий

В отличие от JavaScript, Python или C++, в Dart **нет концепции "truthy/falsy" значений**.

```dart
// В JavaScript: if ("hello") { ... } сработает!
// В Dart: ошибка компиляции! Тип String не может быть приведен к bool.
```

### Пример кода

```dart
void main() {
  bool isAuthorized = true;
  bool hasActiveSubscription = false;
  int userBalance = 0;

  // Строгая проверка логического выражения
  bool canWatchContent = isAuthorized && hasActiveSubscription;

  // Обязательное явное сравнение чисел с нулем
  bool hasMoney = userBalance > 0;

  // Тернарный оператор для выбора стиля UI во Flutter
  String buttonText = isAuthorized ? "В личный кабинет" : "Войти в систему";
  print(buttonText);
}
```

### Задания для закрепления

1. **Проверка права доступа к экрану:** Сформируйте булево выражение: пользователь авторизован, подтвердил email и не находится в списке заблокированных.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/189a5c30-b2d9-4f60-af26-679b35697b44" />

```dart
bool checkScreenAccess({
  required bool isAuthorized,
  required bool isEmailConfirmed,
  required bool isBlocked,
}) {
  return isAuthorized && isEmailConfirmed && !isBlocked;
}

void main() {
  print(checkScreenAccess(isAuthorized: true, isEmailConfirmed: true, isBlocked: false));
}
```

2. **Определитель пустоты строки:** Напишите функцию `bool isBlank(String? input)`, возвращающую `true`, если строка равна `null`, пуста или состоит только из пробелов.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7aa9cda3-9d8d-458b-830e-4b588cda2ee6" />

```dart
bool isBlank(String? input) {
  return input == null || input.trim().isEmpty;
}

void main() {
  print(isBlank(null));
  print(isBlank(''));
  print(isBlank(' '));
  print(isBlank('Текст'));
}
```

3. **Тоггл состояния (Инвертор):** Напишите функцию, переключающую флаг темной темы `isDarkMode` на противоположное значение.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6cb01083-3bb8-45c8-9af4-6462b4b83dbf" />

```dart
bool toggleDarkMode(bool isDarkMode) {
  return !isDarkMode;
}

void main() {
  bool currentTheme = false;
  currentTheme = toggleDarkMode(currentTheme);
  print(currentTheme);
}
```

4. **Валидатор возраста для аренды авто:** Проверьте совокупность условий: возраст \(\ge 21\) года И стаж вождения \(\ge 2\) лет.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/544663c9-e01c-42af-868c-ad75e0f8cc50" />

```dart
bool canRentCar(int age, int drivingExperience) {
  return age >= 21 && drivingExperience >= 2;
}

void main() {
  print(canRentCar(25, 3));
  print(canRentCar(20, 3));
  print(canRentCar(25, 1));
}
```

5. **Строгая фильтрация коллекций:** Напишите проверку, что баланс пользователя строго положителен, избегая неявных сравнений.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/da026787-7d39-4141-98fc-ecc5c13da506" />

```dart
bool isBalancePositive(num balance) {
  return balance > 0;
}

void main() {
  print(isBalancePositive(100.50));
  print(isBalancePositive(0));
  print(isBalancePositive(-10));
}
```

---

## Тема 4. Списки (List): списковые включения и spread-операторы

`List<E>` — упорядоченная коллекция элементов с доступом по целочисленному индексу. В экосистеме Flutter списки служат основой для построения виджетов `Column`, `Row` и `ListView`.

### Специальные возможности списков в Dart

- **Spread-оператор (`...` и `...?`):** Вставка элементов одной коллекции внутрь другой.
- **Collection `if`:** Условное включение виджета или элемента в список.
- **Collection `for`:** Генерация элементов списка на основе итерации.

### Пример кода

```dart
void main() {
  bool isAdmin = true;
  List<String>? optionalBadges = ["PRO", "TOP"];

  // Использование Collection if, Collection for и Spread-оператора
  List<String> menuItems = [
    "Главная",
    "Профиль",
    if (isAdmin) "Панель администратора",
    ...?optionalBadges,
    for (int i = 1; i <= 3; i++) "Бонус #$i",
  ];

  print(menuItems);

  // Функциональные методы обработки
  List<int> rawScores = [45, 80, 12, 95, 60];

  // Цепочка фильтрации и трансформации (Iterable)
  List<String> passedResults = rawScores
      .where((score) => score >= 60)
      .map((score) => "Сдал: $score баллов")
      .toList();

  print(passedResults);

  // Агрегация через fold
  int totalScore = rawScores.fold(0, (prev, element) => prev + element);
  print("Сумма баллов: $totalScore");
}
```

### Задания для закрепления

1. **Удаление дубликатов с сохранением порядка:** Дан список `List<int>` с повторами. Верните список уникальных элементов через `.toSet().toList()`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ba6ffbc4-05be-4f97-8f24-a21bd397aad1" />

```dart
List<int> removeDuplicates(List<int> list) {
  return list.toSet().toList();
}

void main() {
  final numbers = [1, 2, 2, 3, 1, 4, 5, 5];
  print(removeDuplicates(numbers));
}

```

2. **Пагинация среза (Slice):** Реализуйте метод `getPage(List<T> list, int page, int pageSize)`, возвращающий соответствующий подсписок с помощью `.sublist()`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/15a753a9-1a87-4a76-b195-c7edb55b1a1e" />

```dart
List<T> getPage<T>(List<T> list, int page, int pageSize) {
  final start = (page - 1) * pageSize;
  
  if (start < 0 || start >= list.length) {
    return [];
  }
  
  final end = (start + pageSize < list.length) ? start + pageSize : list.length;
  
  return list.sublist(start, end);
}

void main() {
  final list = ['A', 'B', 'C', 'D', 'E', 'F', 'G'];
  print(getPage(list, 2, 3));
}
```

3. **Сортировка товаров по цене:** Дан список цен `List<double>`. Отсортируйте его по убыванию с помощью метода `.sort()`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/77ad081c-908a-4a0e-a674-add7af7f43fe" />

```dart
void main() {
  final prices = [150.5, 20.0, 300.99, 45.0];
  
  prices.sort((a, b) => b.compareTo(a));
  
  print(prices);
}
```

4. **Поиск первого подходящего элемента:** Найдите в списке строк первое слово длиннее 8 символов с помощью `.firstWhere()`, обработав случай отсутствия через `orElse`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ccbf1d99-d33b-4114-9170-2c590b7bd6f5" />

```dart
String findLongWordClean(List<String> words) {
  return words.firstWhere(
    (word) => word.length > 8,
    orElse: () => 'Не найдено',
  );
}

void main() {
  final words = ['яблоко', 'апельсин', 'виноград', 'мандарин'];
  print(findLongWordClean(words));
}
```

5. **Генератор виджетов через List.generate:** Создайте список строк вида `["Слот 1", "Слот 2", ... "Слот 10"]` с помощью конструктора `List.generate()`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/cc006294-6c06-4247-8b99-1d0043a9cc81" />

```dart
List<String> generateSlots() {
  return List.generate(10, (index) => 'Слот ${index + 1}');
}

void main() {
  final slots = generateSlots();
  print(slots);
}
```

---

## Тема 5. Множества (Set) и Словари (Map): парсинг JSON-структур

- `Set<E>` — неупорядоченная коллекция уникальных элементов (быстрый поиск за \(O(1)\)).
- `Map<K, V>` — структура хранения пар «ключ-значение». Стандартный тип для десериализации JSON в Flutter — `Map<String, dynamic>`.

### Пример кода

```dart
void main() {
  // 1. Множества (Set) и операции над ними
  Set<String> localTags = {"mobile", "dart", "flutter"};
  Set<String> remoteTags = {"flutter", "android", "ios"};

  Set<String> allTags = localTags.union(remoteTags); // Объединение
  Set<String> commonTags = localTags.intersection(remoteTags); // Пересечение {"flutter"}
  print("Общие теги: $commonTags");

  // 2. Словари (Map): имитация ответа REST API
  Map<String, dynamic> userJson = {
    "id": 101,
    "name": "Алексей",
    "isPremium": true,
    "settings": {
      "theme": "dark",
      "notifications": true,
    },
    "roles": ["developer", "tester"]
  };

  // Безопасное чтение значений
  String userName = userJson["name"] as String? ?? "Неизвестный";
  int userId = userJson["id"] as int;
  bool isDark = userJson["settings"]?["theme"] == "dark";

  print("Пользователь: $userName (ID: $userId), Темная тема: $isDark");

  // Мутация карты
  userJson.putIfAbsent("lastLogin", () => DateTime.now().toIso8601String());
  print("Обновленная карта: $userJson");
}
```

### Задания для закрепления

1. **Подсчет частоты слов:** Дана строка текста. С помощью `Map<String, int>` подсчитайте, сколько раз каждое слово встречается в тексте.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4cb9f98b-814f-4194-ae75-2440e1a3d9fd" />

```dart
Map<String, int> countWordFrequency(String text) {
  final words = text.toLowerCase().split(RegExp(r'\s+'));
  final frequencyMap = <String, int>{};

  for (var word in words) {
    if (word.isNotEmpty) {
      frequencyMap[word] = (frequencyMap[word] ?? 0) + 1;
    }
  }

  return frequencyMap;
}

void main() {
  const text = 'яблоко банан яблоко апельсин банан яблоко';
  print(countWordFrequency(text));
}
```

2. **Симметрическая разность тегов:** Найдите теги, которые присутствуют только в одном из двух множеств `Set<String>`, но не в обоих одновременно.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/fca6fd43-8355-4e97-8762-e6cebca9722e" />

```dart
Set<String> symmetricDifference(Set<String> set1, Set<String> set2) {
  final diff1 = set1.difference(set2);
  final diff2 = set2.difference(set1);
  return diff1.union(diff2);
}

void main() {
  final setA = {'flutter', 'dart', 'android'};
  final setB = {'dart', 'ios', 'swift'};
  
  print(symmetricDifference(setA, setB));
}
```

3. **Безопасное извлечение настроек:** Напишите функцию, извлекающую значение `String` из вложенной `Map<String, dynamic>` с гарантированным возвратом дефолтного значения при отсутствии ключа.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b0945470-1cda-40da-be17-6fb92ea2f794" />

```dart
String getStringSetting(Map<String, dynamic> settings, String key, {String defaultValue = ''}) {
  final value = settings[key];
  if (value is String) {
    return value;
  }
  return defaultValue;
}

void main() {
  final Map<String, dynamic> config = {
    'appName': 'MyApp',
    'version': 1.0,
    'isDebug': true,
  };

  print(getStringSetting(config, 'appName', defaultValue: 'Unknown'));
  print(getStringSetting(config, 'version', defaultValue: '1.0.0'));
  print(getStringSetting(config, 'missingKey', defaultValue: 'Default'));
}
```

4. **Трансформация карты (Map Entries):** Преобразуйте `Map<String, double>` с ценами в рублях в карту с ценами в долларах с помощью `.map()`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2c1499f8-6e18-4a6a-b6a5-a2e96d17513c" />

```dart
Map<String, double> convertToDollars(Map<String, double> rubles, double usdRate) {
  return rubles.map((key, value) => MapEntry(key, value / usdRate));
}

void main() {
  final pricesInRubles = {
    'Хлеб': 50.0,
    'Молоко': 80.0,
    'Сыр': 400.0,
  };
  
  final pricesInDollars = convertToDollars(pricesInRubles, 90.0);
  print(pricesInDollars);
}
```

5. **Группировка пользователей по городу:** Дан список карт пользователей. Сгруппируйте их в структуру `Map<String, List<Map<String, dynamic>>>`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3c73453d-e8bb-46e0-99a4-09f1062c5ea4" />

```dart
Map<String, List<Map<String, dynamic>>> groupUsersByCity(List<Map<String, dynamic>> users) {
  final groupedUsers = <String, List<Map<String, dynamic>>>{};

  for (final user in users) {
    final city = user['city'] as String? ?? 'Неизвестно';
    
    if (!groupedUsers.containsKey(city)) {
      groupedUsers[city] = [];
    }
    
    groupedUsers[city]!.add(user);
  }

  return groupedUsers;
}

void main() {
  final users = [
    {'name': 'Иван', 'city': 'Москва'},
    {'name': 'Анна', 'city': 'Санкт-Петербург'},
    {'name': 'Олег', 'city': 'Москва'},
    {'name': 'Мария', 'city': 'Казань'},
  ];

  final result = groupUsersByCity(users);
  print(result);
}
```

---

## Тема 6. Null Safety, Records (кортежи) и Pattern Matching

### Операторы звуковой безопасности Null (Null Safety)

- `T?` — переменная может содержать значение типа `T` или `null`.
- `?.` (Null-aware access) — вызов метода/свойства только если объект не `null`.
- `??` (If-null operator) — подстановка значения по умолчанию.
- `??=` (Null-aware assignment) — присваивание только если текущее значение равно `null`.
- `!` (Null assertion) — утверждение разработчика, что значение точно не `null` (опасно: при ошибке выбрасывает исключение).

### Кортежи (Records) и Pattern Matching (Dart 3+)

Records позволяют возвращать несколько разнородных значений из функции без создания отдельного класса-модели.

### Пример кода

```dart
// Функция возвращает кортеж из двух значений: статус успеха и сообщение
(bool isSuccess, String message) authenticate(String login, String password) {
  if (login == "admin" && password == "secret") {
    return (true, "Авторизация успешна!");
  }
  return (false, "Неверный логин или пароль");
}

void main() {
  // 1. Деструктуризация Record
  var (success, msg) = authenticate("admin", "secret");
  print("Результат: $success, Сообщение: $msg");

  // 2. Именованный Record
  ({int code, String error}) errorResponse = (code: 404, error: "Страница не найдена");
  print("Код ошибки: ${errorResponse.code}");

  // 3. Pattern Matching со switch-выражением
  int httpStatus = 200;
  String statusDescription = switch (httpStatus) {
    200 => "Успешно",
    >= 400 && < 500 => "Ошибка клиента",
    >= 500 && < 600 => "Ошибка сервера",
    _ => "Неизвестный статус",
  };
  print("Статус: $statusDescription");
}
```

### Задания для закрепления

1. **Координаты экрана через Record:** Напишите функцию, возвращающую кортеж `(double x, double y)` центра экрана смартфона.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ec4ad12a-e3ed-4d9a-b3a8-17af8a898a80" />

```dart
(double x, double y) getScreenCenter(double screenWidth, double screenHeight) {
  return (screenWidth / 2, screenHeight / 2);
}

void main() {
  final center = getScreenCenter(1080.0, 2400.0);
  print(center);
}
```

2. **Безопасная распаковка профиля:** Дана переменная `String? bio`. Если она `null`, запишите в переменную `displayBio` текст «Информация отсутствует».

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5cc981c0-2ea0-4584-8cdd-b02286143911" />

```dart
void main() {
  String? bio; // Может быть null или содержать текст
  
  String displayBio = bio ?? 'Информация отсутствует';
  
  print(displayBio);
}
```

3. **Pattern Matching над JSON:** Используя конструкцию `switch (json)`, проверьте формат входящего пакета: валидный объект с `id` или объект с полем ошибки.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/51190af8-3435-4808-a382-3bc20abbdcc8" />

```dart
void checkJsonFormat(Map<String, dynamic> json) {
  switch (json) {
    case {'id': var id}:
      print('Валидный объект с id: $id');
    case {'error': var errorMessage}:
      print('Объект с ошибкой: $errorMessage');
    default:
      print('Неизвестный формат пакета');
  }
}

void main() {
  checkJsonFormat({'id': 1, 'name': 'Ivan'});
  checkJsonFormat({'error': 'Not Found'});
  checkJsonFormat({'status': 'ok'});
}
```

4. **Служебная переменная с `late`:** Объявите переменную `late final double expensiveCalculation`, которая вычисляется только при первом реальном обращении.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b5949c9f-874e-43ac-afeb-f1636683ec8d" />

```dart
late final double expensiveCalculation = calculateSomething();

double calculateSomething() {
  print('Выполнение сложных вычислений...');
  return 3.14159;
}

void main() {
  print('Программа запущена');
  print('Переменная еще не вычислена');
  
  // Первое обращение - запускается вычисление
  print('Результат: $expensiveCalculation');
  
  // Второе обращение - берется готовое значение
  print('Результат снова: $expensiveCalculation');
}
```

5. **Безопасный поиск в списке:** Извлеките первый элемент списка `List<int>? numbers` через операторы `?.[0]` и `?? -1`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/079c0bbc-717c-4405-8484-aef749f3a4e2" />

```dart
void main() {
  List<int>? numbers = [10, 20, 30];
  // List<int>? numbers = null; // Для проверки случая с null
  
  int firstElement = numbers?[0] ?? -1;
  
  print(firstElement);
}
```

---

## 50 Смешанных заданий по обработке данных

Каждое задание моделирует практическую задачу мобильного приложения на Flutter (UI-стейт, расчеты, форматирование, фильтрация каталога, сериализация).

### Блок 1: Числа, финансы и расчеты корзины (1–10)

1. **Калькулятор скидки с промокодом:** Рассчитайте итоговую стоимость заказа при условии: скидка 15%, но не более 500 рублей.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0b05de7f-24b8-404f-b0e0-47c5d3f3214b" />

```dart
double calculateTotalCost(double price) {
  double discount = price * 0.15;
  if (discount > 500) {
    discount = 500;
  }
  return price - discount;
}

void main() {
  print(calculateTotalCost(1000)); // 850.0
  print(calculateTotalCost(5000)); // 4500.0
}
```

2. **Форматирование цены по стандарту валют:** Преобразуйте число `15400900.5` в строку `"15 400 900.50 ₽"`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5f2325a1-1ed3-446d-b25a-7306630f25a0" />

```dart
String formatPrice(double price) {
  final parts = price.toStringAsFixed(2).split('.');
  final integerPart = parts[0];
  final decimalPart = parts[1];
  
  final buffer = StringBuffer();
  int count = 0;
  
  for (int i = integerPart.length - 1; i >= 0; i--) {
    buffer.write(integerPart[i]);
    count++;
    if (count == 3 && i != 0) {
      buffer.write(' ');
      count = 0;
    }
  }
  
  final formattedInteger = buffer.toString().split('').reversed.join('');
  return '$formattedInteger.$decimalPart ₽';
}

void main() {
  print(formatPrice(15400900.5)); // 15 400 900.50 ₽
}
```

3. **Расчет кешбэка баллами:** Начислите 1 балл за каждые полные 100 рублей чека, используя оператор целочисленного деления `~/`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9236ecb7-6e4e-42d2-ab6c-5936f9cce04e" />

```dart
int calculateCashback(double checkAmount) {
  return checkAmount ~/ 100;
}

void main() {
  print(calculateCashback(350)); // 3
  print(calculateCashback(99)); // 0
}
```

4. **Ограничитель диапазона (Clamp):** Напишите функцию, ограничивающую уровень громкости динамика в диапазоне от `0.0` до `1.0`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c306bd36-c644-4dc4-8062-c8643e26f788" />

```dart
double clampVolume(double volume) {
  return volume.clamp(0.0, 1.0);
}

void main() {
  print(clampVolume(-0.5)); // 0.0
  print(clampVolume(0.5)); // 0.5
  print(clampVolume(1.5)); // 1.0
}
```
 
5. **Конвертер байтов в мегабайты:** Преобразуйте размер файла из байтов в МБ с округлением до одного знака после запятой.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2e1fe2c9-0bb1-4fff-8ed0-a28c52ca5c03" />

```dart
double convertBytesToMb(int bytes) {
  final mb = bytes / (1024 * 1024);
  return double.parse(mb.toStringAsFixed(1));
}

void main() {
  print(convertBytesToMb(1572864)); // 1.5
  print(convertBytesToMb(5000000)); // 4.8
}
```

6. **Вычисление процентного прогресса:** По текущему шагу `current` и общему числу шагов `total` верните `double` от `0.0` до `1.0`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/15a0d05a-1eba-4f41-8e67-fa8d8be3ec3f" />

```dart
double calculateProgress(int current, int total) {
  if (total <= 0) return 0.0;
  return (current / total).clamp(0.0, 1.0);
}

void main() {
  print(calculateProgress(5, 10)); // 0.5
  print(calculateProgress(15, 10)); // 1.0
}
```

7. **Округление до ближайшего десятка:** Округлите переданное число вверх до ближайшего числа, кратного 10 (например, `43` -> `50`).

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a2febeed-ea51-4fba-b1d7-f9177ad6d6cf" />

```dart
int roundUpToNearestTen(int number) {
  return ((number + 9) ~/ 10) * 10;
}

void main() {
  print(roundUpToNearestTen(43)); // 50
  print(roundUpToNearestTen(40)); // 40
  print(roundUpToNearestTen(41)); // 50
}
```

8. **Калькулятор стоимости доставки:** Базовая ставка 200 руб. + 30 руб. за каждый километр свыше 5 км.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/496898b6-a181-4b86-a4ac-5e0fcb75e751" />

```dart
double calculateDeliveryCost(double distanceKm) {
  double baseCost = 200;
  if (distanceKm > 5) {
    baseCost += (distanceKm - 5) * 30;
  }
  return baseCost;
}

void main() {
  print(calculateDeliveryCost(3)); // 200.0
  print(calculateDeliveryCost(10)); // 350.0
}
```

9. **Проверка делимости без остатка:** Определите, делится ли размер скачиваемого пакета ровно на блоки по 1024 байта.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5f34bcc8-5f08-451e-99e9-b3ae26a127a3" />

```dart
bool isDivisibleBy1024(int sizeBytes) {
  return sizeBytes % 1024 == 0;
}

void main() {
  print(isDivisibleBy1024(2048)); // true
  print(isDivisibleBy1024(2050)); // false
}
```

10. **Парсер диапазона цен:** Извлеките минимальную и максимальную цену из строки `"1500 - 4500 руб"`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/03082f2d-ffa5-45c9-bc33-4a874fa0ed4a" />

```dart
({int min, int max}) parsePriceRange(String input) {
  final matches = RegExp(r'\d+').allMatches(input).map((m) => int.parse(m.group(0)!)).toList();
  if (matches.length >= 2) {
    return (min: matches[0], max: matches[1]);
  }
  return (min: 0, max: 0);
}

void main() {
  final range = parsePriceRange("1500 - 4500 руб");
  print('Min: ${range.min}, Max: ${range.max}');
}
```

### Блок 2: Строки, валидация полей и парсинг текста (11–20)

11. **Валидатор пароля на регулярных выражениях:** Проверьте пароль на наличие минимум 8 символов, одной цифры и одной заглавной буквы.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/50916fe7-366c-4447-97ca-f9f19d530d65" />

```dart
bool isValidPassword(String password) {
  final regex = RegExp(r'^(?=.*[A-Z])(?=.*\d).{8,}$');
  return regex.hasMatch(password);
}

void main() {
  print(isValidPassword('Password1'));
  print(isValidPassword('password1'));
}
```

12. **Очистка поискового ввода:** Удалите из поискового запроса двойные пробелы, спецсимволы `#`, `$`, `%` и приведите к нижнему регистру.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4e0331d7-f631-4797-bbb7-c44e8751d376" />

```dart
String cleanSearchQuery(String query) {
  String result = query.replaceAll(RegExp(r'[#\$%]'), '');
  result = result.replaceAll(RegExp(r'\s+'), ' ');
  return result.trim().toLowerCase();
}

void main() {
  print(cleanSearchQuery(' Flutter #разработка \$код %сложно '));
}
```

13. **Генератор сокращенного имени автора:** Преобразуйте `"Иванов Иван Иванович"` в `"Иванов И. И."`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3ea74013-6206-4062-9aad-0eaf72836589" />

```dart
String shortenAuthorName(String fullName) {
  final parts = fullName.trim().split(RegExp(r'\s+'));
  if (parts.length < 3) return fullName;
  
  final lastName = parts[0];
  final firstNameInitial = parts[1][0].toUpperCase();
  final middleNameInitial = parts[2][0].toUpperCase();
  
  return '$lastName $firstNameInitial. $middleNameInitial.';
}

void main() {
  print(shortenAuthorName('Иванов Иван Иванович'));
}
```

14. **Парсинг HEX-цвета для Flutter:** Преобразуйте строку вида `"#FF5722"` в целочисленное значение цвета `int` (`0xFFFF5722`).

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/17028209-2d9b-429e-8729-808270b851b8" />

```dart
int parseHexColor(String hexColor) {
  String cleanHex = hexColor.replaceAll('#', '');
  if (cleanHex.length == 6) {
    cleanHex = 'FF$cleanHex';
  }
  return int.parse(cleanHex, radix: 16);
}

void main() {
  print(parseHexColor('#FF5722').toRadixString(16).toUpperCase());
}
```

15. **Форматирование таймера звонка:** Переведите количество секунд `int seconds = 125` в строку вида `"02:05"`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/27c1cbe2-e2b5-403a-8342-a9dba737c45a" />

```dart
String formatCallTimer(int totalSeconds) {
  final minutes = (totalSeconds ~/ 60).toString().padLeft(2, '0');
  final seconds = (totalSeconds % 60).toString().padLeft(2, '0');
  return '$minutes:$seconds';
}

void main() {
  print(formatCallTimer(125));
}
```

16. **Скрытие номера телефона:** Преобразуйте `"+79991234567"` в `"+7 (999) ***-**-67"`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/574fc28d-e518-445f-bf84-b4e77a5f934c" />

```dart
String maskPhoneNumber(String phone) {
  if (phone.length < 12) return phone;
  
  final prefix = phone.substring(0, 2);
  final operatorCode = phone.substring(2, 5);
  final suffix = phone.substring(phone.length - 2);
  
  return '$prefix ($operatorCode) ***-**-$suffix';
}

void main() {
  print(maskPhoneNumber('+79991234567'));
}
```

17. **Определитель языка текста (Кириллица/Латиница):** Определите, содержит ли строка символы русского алфавита с помощью проверки диапазонов кодовых точек.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/fcffec6f-fe79-4fa9-a615-fa9c9d599339" />

```dart
bool isCyrillic(String text) {
  for (final rune in text.runes) {
    if (rune >= 0x0400 && rune <= 0x04FF) {
      return true;
    }
  }
  return false;
}

void main() {
  print(isCyrillic('Hello'));
  print(isCyrillic('Привет'));
}
```

18. **Генератор слага (Slugify):** Преобразуйте заголовок статьи `"Быстрый старт во Flutter!"` в `"bystryi-start-vo-flutter"`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c7db6707-5931-4e87-a4dd-e8c968c25ed6" />

```dart
String slugify(String title) {
  final translitMap = {
    'а': 'a', 'б': 'b', 'в': 'v', 'г': 'g', 'д': 'd', 'е': 'e', 'ё': 'e',
    'ж': 'zh', 'з': 'z', 'и': 'i', 'й': 'i', 'к': 'k', 'л': 'l', 'м': 'm',
    'н': 'n', 'о': 'o', 'п': 'p', 'р': 'r', 'с': 's', 'т': 't', 'у': 'u',
    'ф': 'f', 'х': 'h', 'ц': 'ts', 'ч': 'ch', 'ш': 'sh', 'щ': 'sch', 'ъ': '',
    'ы': 'y', 'ь': '', 'э': 'e', 'ю': 'yu', 'я': 'ya'
  };
  
  final lowerText = title.toLowerCase();
  final buffer = StringBuffer();
  
  for (int i = 0; i < lowerText.length; i++) {
    final char = lowerText[i];
    if (translitMap.containsKey(char)) {
      buffer.write(translitMap[char]);
    } else if (RegExp(r'[a-z0-9]').hasMatch(char)) {
      buffer.write(char);
    } else if (char == ' ' || char == '!' || char == '?' || char == ',' || char == '.') {
      buffer.write('-');
    }
  }
  
  return buffer.toString().replaceAll(RegExp(r'-+'), '-').replaceAll(RegExp(r'^-|-$'), '');
}

void main() {
  print(slugify('Быстрый старт во Flutter!'));
}
```

19. **Подсчет слов в заметке:** Подсчитайте количество слов в тексте заметки, разделенных пробелами и переносами строк.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/48f6a3a0-c5c9-4d31-b5ea-adf1f7980e9e" />

```dart
int countWords(String text) {
  final trimmed = text.trim();
  if (trimmed.isEmpty) return 0;
  return trimmed.split(RegExp(r'\s+')).length;
}

void main() {
  print(countWords('Это простой\nтекст с переносами\nи пробелами'));
}
```

20. **Выделение тегов из текста:** Извлеките все хештеги из сообщения блога (слова, начинающиеся с `#`).

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/65310495-864b-4507-ae3a-a40656b9e90e" />

```dart
List<String> extractHashtags(String text) {
  final regex = RegExp(r'#\w+');
  return regex.allMatches(text).map((match) => match.group(0)!).toList();
}

void main() {
  print(extractHashtags('Изучаю #Flutter и #Dart. Отличный #блог!'));
}
```

### Блок 3: Списки, пагинация и фильтрация каталога (21–30)

21. **Фильтр каталога по диапазону цен:** Из списка карточек товаров отберите только те, у которых цена находится в заданном диапазоне.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/df45a782-9ce5-47b9-99d8-417ba1995138" />

```dart
class Product {
  final String name;
  final double price;
  Product(this.name, this.price);
}

List<Product> filterByPriceRange(List<Product> products, double minPrice, double maxPrice) {
  return products.where((product) => product.price >= minPrice && product.price <= maxPrice).toList();
}

void main() {
  final products = [
    Product('Товар 1', 150.0),
    Product('Товар 2', 50.0),
    Product('Товар 3', 300.0),
  ];
  final filtered = filterByPriceRange(products, 100.0, 200.0);
  print(filtered.map((p) => p.name).toList());
}
```

22. **Удаление завершенных задач из To-Do списка:** Отфильтруйте список задач, оставив только элементы со статусом `isCompleted == false`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1f378e3f-9cb9-448b-b437-bf7d266958bf" />

```dart
class Task {
  final String title;
  final bool isCompleted;
  Task(this.title, this.isCompleted);
}

List<Task> getActiveTasks(List<Task> tasks) {
  return tasks.where((task) => task.isCompleted == false).toList();
}

void main() {
  final tasks = [
    Task('Купить молоко', true),
    Task('Сделать зарядку', false),
    Task('Прочитать книгу', false),
  ];
  final active = getActiveTasks(tasks);
  print(active.map((t) => t.title).toList());
}
```

23. **Инверсия порядка чатов:** Разверните список сообщений без создания промежуточных копий в памяти.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/03eca34d-44f5-4dca-8407-4d074a1853c9" />

```dart
void reverseInPlace<T>(List<T> list) {
  int start = 0;
  int end = list.length - 1;
  while (start < end) {
    final temp = list[start];
    list[start] = list[end];
    list[end] = temp;
    start++;
    end--;
  }
}

void main() {
  final messages = ['Привет', 'Как дела?', 'Что нового?'];
  reverseInPlace(messages);
  print(messages);
}
```

24. **Склейка двух списков баннеров:** Объедините системные промо-акции и персональные предложения с помощью spread-оператора `...`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d59e3295-da88-4d6c-982d-a646a83d1448" />

```dart
List<String> mergeBanners(List<String> systemPromos, List<String> personalOffers) {
  return [...systemPromos, ...personalOffers];
}

void main() {
  final system = ['Скидка 10%', 'Новинки'];
  final personal = ['Специально для вас', 'Бонусы'];
  print(mergeBanners(system, personal));
}
```

25. **Поиск максимального элемента без библиотек:** Найдите товар с наивысшим рейтингом в списке с помощью метода `.reduce()`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/21c6179b-e05c-49d0-8d39-758d0476dffd" />

```dart
class RatedProduct {
  final String name;
  final double rating;
  RatedProduct(this.name, this.rating);
}

RatedProduct findBestProduct(List<RatedProduct> products) {
  return products.reduce((current, next) => current.rating > next.rating ? current : next);
}

void main() {
  final products = [
    RatedProduct('Смартфон', 4.5),
    RatedProduct('Наушники', 4.8),
    RatedProduct('Чехол', 4.2),
  ];
  print(findBestProduct(products).name);
}
```

26. **Чередование элементов списка:** Вставьте рекламный баннер после каждого 5-го элемента ленты новостей.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3b28aae6-7b9a-4a02-b336-8662042bcaef" />

```dart
List<String> insertAds(List<String> newsFeed, String ad) {
  final result = <String>[];
  for (int i = 0; i < newsFeed.length; i++) {
    result.add(newsFeed[i]);
    if ((i + 1) % 5 == 0 && i != newsFeed.length - 1) {
      result.add(ad);
    }
  }
  return result;
}

void main() {
  final news = ['Новость 1', 'Новость 2', 'Новость 3', 'Новость 4', 'Новость 5', 'Новость 6'];
  print(insertAds(news, 'РЕКЛАМА'));
}
```

27. **Проверка идентичности двух списков:** Напишите функцию, проверяющую, содержат ли два списка одинаковые элементы на тех же позициях.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/85c5f1b9-210a-438f-843d-6c0682e8dc9a" />

```dart
bool areIdentical<T>(List<T> list1, List<T> list2) {
  if (list1.length != list2.length) return false;
  for (int i = 0; i < list1.length; i++) {
    if (list1[i] != list2[i]) return false;
  }
  return true;
}

void main() {
  print(areIdentical([1, 2, 3], [1, 2, 3]));
  print(areIdentical([1, 2, 3], [1, 2, 4]));
}
```

28. **Извлечение уникальных категорий:** Из списка всех товаров магазина сформируйте `List<String>` уникальных названий категорий.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6a28c23f-e49d-4535-8831-e0e67ba4ca33" />

```dart
class StoreItem {
  final String name;
  final String category;
  StoreItem(this.name, this.category);
}

List<String> getUniqueCategories(List<StoreItem> items) {
  return items.map((item) => item.category).toSet().toList();
}

void main() {
  final items = [
    StoreItem('Яблоко', 'Фрукты'),
    StoreItem('Банан', 'Фрукты'),
    StoreItem('Молоко', 'Молочные продукты'),
  ];
  print(getUniqueCategories(items));
}
```

29. **Сортировка списка по нескольким полям:** Отсортируйте список товаров сначала по доступности на складе, а затем по возрастанию цены.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2daf6b8b-137a-4aa0-b1e6-b86f961a65c5" />

```dart
class InventoryProduct {
  final String name;
  final bool inStock;
  final double price;
  InventoryProduct(this.name, this.inStock, this.price);
}

void sortProducts(List<InventoryProduct> products) {
  products.sort((a, b) {
    if (a.inStock != b.inStock) {
      return a.inStock ? -1 : 1;
    }
    return a.price.compareTo(b.price);
  });
}

void main() {
  final products = [
    InventoryProduct('Товар 1', false, 100.0),
    InventoryProduct('Товар 2', true, 200.0),
    InventoryProduct('Товар 3', true, 150.0),
  ];
  sortProducts(products);
  print(products.map((p) => '${p.name} (В наличии: ${p.inStock}, Цена: ${p.price})').toList());
}
```

30. **Постраничная группировка (Chunking):** Разбейте длинный список из 100 элементов на пачки (страницы) по 10 элементов.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/343435e1-fe6e-4d7a-9ae8-908432374611" />

```dart
List<List<T>> chunkList<T>(List<T> list, int chunkSize) {
  final chunks = <List<T>>[];
  for (int i = 0; i < list.length; i += chunkSize) {
    final end = (i + chunkSize < list.length) ? i + chunkSize : list.length;
    chunks.add(list.sublist(i, end));
  }
  return chunks;
}

void main() {
  final list100 = List.generate(100, (index) => index + 1);
  final pages = chunkList(list100, 10);
  print('Количество страниц: ${pages.length}');
  print('Первая страница: ${pages.first}');
}
```

### Блок 4: Map, JSON-сериализация и кэширование (31–40)

31. **Десериализация профиля:** Напишите парсер словаря `Map<String, dynamic>` в безопасные локальные переменные с дефолтными значениями.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/be5c4463-f5d8-45f6-89c7-da091cf3289c" />

```dart
void parseProfile(Map<String, dynamic> json) {
  final String name = json['name'] as String? ?? 'Гость';
  final int age = json['age'] as int? ?? 0;
  final bool isVerified = json['isVerified'] as bool? ?? false;

  print('Имя: $name, Возраст: $age, Верифицирован: $isVerified');
}

void main() {
  final serverResponse = {'name': 'Иван', 'age': 25};
  parseProfile(serverResponse);
}
```

32. **Конвертация списка в ассоциативную карту:** Преобразуйте `List<User>` в `Map<int, User>`, где ключом выступает уникальный `id`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d5282b73-a790-4416-9adf-7da0da311139" />

```dart
class User {
  final int id;
  final String name;
  User(this.id, this.name);
}

Map<int, User> convertListToMap(List<User> users) {
  return {for (var user in users) user.id: user};
}

void main() {
  final users = [
    User(1, 'Иван'),
    User(2, 'Анна'),
  ];
  final userMap = convertListToMap(users);
  print(userMap[1]?.name);
}
```

33. **Слияние локального и серверного профиля:** Объедините настройки приложения из кэша и свежие настройки с бэкенда с приоритетом сервера.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8f07511f-4280-4748-a2f1-1b43141bb85c" />

```dart
Map<String, dynamic> mergeSettings(Map<String, dynamic> cache, Map<String, dynamic> server) {
  return {...cache, ...server};
}

void main() {
  final localCache = {'theme': 'dark', 'lang': 'ru', 'fontSize': 14};
  final serverSettings = {'lang': 'en', 'fontSize': 16};
  
  final result = mergeSettings(localCache, serverSettings);
  print(result);
}
```

34. **Удаление ключей с null-значениями:** Очистите словарь от всех пар, где значение равно `null`, перед отправкой POST-запроса.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/fcbbad77-cbf0-431c-9a0f-9db0e3828d3f" />

```dart
Map<String, dynamic> removeNullValues(Map<String, dynamic> data) {
  final cleanedData = Map<String, dynamic>.from(data);
  cleanedData.removeWhere((key, value) => value == null);
  return cleanedData;
}

void main() {
  final requestBody = {
    'id': 101,
    'name': 'Товар',
    'description': null,
    'price': 500,
    'discount': null,
  };
  
  print(removeNullValues(requestBody));
}
```

35. **Кэш экранов (LRU-концепт):** Реализуйте простейшую карту последних 5 открытых страниц, удаляя самый старый элемент при переполнении.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a5cd4aac-1c4c-46b9-8d32-3459e6e49709" />

```dart
class ScreenCache {
  final int capacity;
  final Map<String, bool> _cache = {};

  ScreenCache(this.capacity);

  void openScreen(String screenName) {
    if (_cache.containsKey(screenName)) {
      _cache.remove(screenName);
    } else if (_cache.length >= capacity) {
      _cache.remove(_cache.keys.first);
    }
    _cache[screenName] = true;
  }

  void printCache() {
    print(_cache.keys.toList());
  }
}

void main() {
  final cache = ScreenCache(5);
  cache.openScreen('Главная');
  cache.openScreen('Профиль');
  cache.openScreen('Настройки');
  cache.openScreen('Корзина');
  cache.openScreen('Каталог');
  cache.openScreen('Оплата');
  cache.printCache();
}
```

36. **Инверсия карты (Значения становятся ключами):** Поменяйте местами ключи и значения в словаре `Map<String, String>` (гарантируя уникальность).

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e3a6d026-db15-4bd4-862c-f1fdc3b19645" />

```dart
Map<String, String> invertMap(Map<String, String> original) {
  return original.map((key, value) => MapEntry(value, key));
}

void main() {
  final dictionary = {
    'apple': 'яблоко',
    'dog': 'собака',
  };
  print(invertMap(dictionary));
}
```

37. **Подсчет суммы товаров по категориям:** На основе списка покупок верните `Map<String, double>`, где ключ — категория, а значение — общая сумма затрат.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/066512e9-51ab-4e1f-b6eb-82d9256e92b0" />

```dart
class Purchase {
  final String category;
  final double amount;
  Purchase(this.category, this.amount);
}

Map<String, double> calculateCategoryTotals(List<Purchase> purchases) {
  final totals = <String, double>{};
  for (final purchase in purchases) {
    totals[purchase.category] = (totals[purchase.category] ?? 0.0) + purchase.amount;
  }
  return totals;
}

void main() {
  final purchases = [
    Purchase('Еда', 1500.0),
    Purchase('Транспорт', 500.0),
    Purchase('Еда', 700.0),
  ];
  print(calculateCategoryTotals(purchases));
}
```

38. **Валидация обязательных JSON-полей:** Проверьте, содержит ли карта ответа сервера ключи `"status"`, `"data"` и `"timestamp"`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6aa440eb-9eff-44ec-a9fc-c7b9be0e2c1c" />

```dart
bool validateResponse(Map<String, dynamic> response) {
  return response.containsKey('status') &&
         response.containsKey('data') &&
         response.containsKey('timestamp');
}

void main() {
  final validResponse = {'status': 'ok', 'data': [], 'timestamp': 123456789};
  final invalidResponse = {'status': 'ok', 'data': []};
  
  print(validateResponse(validResponse));
  print(validateResponse(invalidResponse));
}
```

39. **Построение дерева категорий:** Преобразуйте плоский список категорий с `parentId` в иерархическую карту подкатегорий.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d15d6ce1-3e93-46e0-aadf-8b3c11018f8a" />

```dart
class Category {
  final int id;
  final int? parentId;
  final String name;
  Category(this.id, this.parentId, this.name);
}

Map<int?, List<Category>> buildCategoryTree(List<Category> categories) {
  final tree = <int?, List<Category>>{};
  for (final category in categories) {
    tree.putIfAbsent(category.parentId, () => []).add(category);
  }
  return tree;
}

void main() {
  final flatCategories = [
    Category(1, null, 'Электроника'),
    Category(2, null, 'Одежда'),
    Category(3, 1, 'Смартфоны'),
    Category(4, 1, 'Ноутбуки'),
    Category(5, 2, 'Футболки'),
  ];
  
  final tree = buildCategoryTree(flatCategories);
  print(tree[1]?.map((c) => c.name).toList());
}
```

40. **Сериализация параметров фильтра в URL:** Соберите из словаря `{"brand": "Apple", "minPrice": 50000}` строку параметров `brand=Apple&minPrice=50000`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1ac6eb89-dbfe-416d-b6ba-09e183e1f481" />

```dart
String buildQueryString(Map<String, dynamic> params) {
  return params.entries.map((e) => '${e.key}=${e.value}').join('&');
}

void main() {
  final filters = {
    'brand': 'Apple',
    'minPrice': 50000,
  };
  
  print(buildQueryString(filters));
}
```

### Блок 5: Sound Null Safety, Records и архитектурные пайплайны (41–50)

41. **Парсер геолокации через Record:** Напишите функцию, принимающую строку `"54.01, 38.29"` и возвращающую кортеж `(double lat, double lon)?`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d6c5cc4e-2537-405b-aac9-b385bcb46d8f" />

```dart
(double lat, double lon)? parseCoordinates(String input) {
  final parts = input.split(',');
  if (parts.length != 2) return null;

  final lat = double.tryParse(parts[0].trim());
  final lon = double.tryParse(parts[1].trim());

  if (lat != null && lon != null) {
    return (lat, lon);
  }
  return null;
}

void main() {
  print(parseCoordinates("54.01, 38.29"));
  print(parseCoordinates("ошибка"));
}
```

42. **Резолвер состояний загрузки (Pattern Matching):** С помощью `switch` верните текст сообщения для состояний `(isLoading: true, hasError: false)`, `(isLoading: false, hasError: true)` и `(isLoading: false, hasError: false)`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4ed444d3-6a04-4bf8-a265-fb07aa91ff1a" />

```dart
String resolveState(({bool isLoading, bool hasError}) state) {
  return switch (state) {
    (isLoading: true, hasError: false) => 'Идет загрузка...',
    (isLoading: false, hasError: true) => 'Произошла ошибка',
    (isLoading: false, hasError: false) => 'Успешно загружено',
    _ => 'Неизвестное состояние',
  };
}

void main() {
  print(resolveState((isLoading: true, hasError: false)));
  print(resolveState((isLoading: false, hasError: true)));
}
```

43. **Безопасная цепочка вызовов (Deep Null Traversal):** Считайте улицу из вложенного объекта компании: `user?.company?.address?.street ?? "Адрес не указан"`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/33ef8870-4e62-4b8a-a322-d7e53bc034b7" />

```dart
class Address {
  final String? street;
  Address(this.street);
}

class Company {
  final Address? address;
  Company(this.address);
}

class User {
  final Company? company;
  User(this.company);
}

String getStreet(User? user) {
  return user?.company?.address?.street ?? "Адрес не указан";
}

void main() {
  final user = User(Company(Address(null)));
  print(getStreet(user));
}
```

44. **Сопоставление типа ответа API:** Напишите функцию обработки ответа через сопоставление типов: `case int code`, `case String error`, `case Map data`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4ece36fe-6d59-43cf-9aff-54acb170a003" />

```dart
void handleApiResponse(Object response) {
  switch (response) {
    case int code:
      print('Получен код: $code');
    case String error:
      print('Текст ошибки: $error');
    case Map data:
      print('Данные: $data');
    default:
      print('Неизвестный формат ответа');
  }
}

void main() {
  handleApiResponse(404);
  handleApiResponse("Not Found");
  handleApiResponse({"id": 1});
}
```

45. **Деструктуризация координат точки в цикле:** Пройдитесь по списку кортежей `List<(int x, int y)>` с прямой деструктуризацией `for (var (x, y) in points)`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d29f7d70-17cc-4052-8c1b-986f93d08695" />

```dart
void main() {
  final List<(int, int)> points = [(1, 2), (3, 4), (5, 6)];

  for (var (x, y) in points) {
    print('Координата X: $x, Координата Y: $y');
  }
}
```

46. **Обмен значений двух переменных без третьей:** Поменяйте местами значения переменных `a` и `b` с помощью синтаксиса кортежей: `(a, b) = (b, a)`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/27035727-6e59-4234-aaf7-7cfd16500f03" />


```dart
void main() {
  int a = 10;
  int b = 20;
  
  print('До обмена: a = $a, b = $b');
  
  (a, b) = (b, a);
  
  print('После обмена: a = $a, b = $b');
}
```

47. **Валидатор комплексной формы регистрации:** Функция возвращает Record `({bool isValid, List<String> errors})` с перечнем всех найденных ошибок формы.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/32300c98-46d9-4181-8dbe-3fe139fecd01" />

```dart
({bool isValid, List<String> errors}) validateForm(String email, String password) {
  final errors = <String>[];

  if (!email.contains('@')) {
    errors.add('Некорректный email');
  }
  if (password.length < 8) {
    errors.add('Пароль должен быть не менее 8 символов');
  }

  return (isValid: errors.isEmpty, errors: errors);
}

void main() {
  final result = validateForm('test', '123');
  print('Валидно: ${result.isValid}');
  print('Ошибки: ${result.errors}');
}
```

48. **Обработка диапазона громкости через Switch Guard:** Используйте паттерн с условием `when`: `case int v when v > 80 => "Слишком громко!"`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e89331a4-d9f5-43f8-b255-5ca3e5fbbe14" />

```dart
String checkVolume(int volume) {
  return switch (volume) {
    int v when v > 80 => "Слишком громко!",
    int v when v < 10 => "Слишком тихо",
    _ => "Комфортная громкость",
  };
}

void main() {
  print(checkVolume(95));
  print(checkVolume(50));
}
```

49. **Пайплайн очистки телефонной книги:** Дан список `List<String?>` с поврежденными записями. Очистите его от `null`, пустых строк и верните `Set<String>` уникальных нормализованных номеров.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2304cc0a-366a-4593-8584-ae4b7f6489cc" />

```dart
Set<String> cleanPhoneBook(List<String?> rawNumbers) {
  return rawNumbers
      .whereType<String>()
      .map((e) => e.replaceAll(RegExp(r'[^\d+]'), ''))
      .where((e) => e.isNotEmpty)
      .toSet();
}

void main() {
  final rawData = [null, "+7 (999) 123-45-67", "", "89991234567", "+79991234567"];
  print(cleanPhoneBook(rawData));
}
```

50. **Итоговый аналитический модуль корзины:**
    - Примите корзину покупок в виде `List<Map<String, dynamic>>`.
    - Рассчитайте общую стоимость позиций с учетом индивидуальных скидок.
    - Выделите категории товаров и проверьте бесплатность доставки (от 2000 руб.).
    - Верните результат в виде именованного кортежа `({double total, double discountAmount, bool isFreeDelivery})`.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2028c3d2-0df6-4127-b726-9da53e31892c" />

```dart
({double total, double discountAmount, bool isFreeDelivery}) analyzeCart(List<Map<String, dynamic>> cart) {
  double total = 0.0;
  double discountAmount = 0.0;

  for (final item in cart) {
    final double price = (item['price'] as num).toDouble();
    final int quantity = item['quantity'] as int;
    final double discountPercent = (item['discount'] as num).toDouble();

    final originalSum = price * quantity;
    final currentDiscount = originalSum * (discountPercent / 100);

    discountAmount += currentDiscount;
    total += (originalSum - currentDiscount);
  }

  final bool isFreeDelivery = total >= 2000.0;

  return (
    total: total,
    discountAmount: discountAmount,
    isFreeDelivery: isFreeDelivery
  );
}

void main() {
  final cart = [
    {'name': 'Телефон', 'price': 2000.0, 'quantity': 1, 'discount': 10.0},
    {'name': 'Чехол', 'price': 500.0, 'quantity': 2, 'discount': 5.0},
  ];

  final result = analyzeCart(cart);
  print('Итого: ${result.total} руб.');
  print('Скидка: ${result.discountAmount} руб.');
  print('Бесплатная доставка: ${result.isFreeDelivery}');
}
```

---
