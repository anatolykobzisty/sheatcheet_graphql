# Шпаргалка по GraphQL

- [Преимущества GraphQL над REST](#Преимущества-GraphQL-над-REST)
- [Как работает GraphQL](#Как-работает-GraphQL)
- [REST vs GraphQL](#REST-vs-GraphQL)
- [Типы данных](#Типы-данных)
- [Три операции GraphQL](#Три-операции-GraphQL)
- [Queries](#Queries)
- [Mutations](#Mutations)
- [Subscribtions](#Subscribtions)
- [Директивы](#Директивы)
- [Security](#Security)
- [Meta Fields](#Meta-Fields)
- [Работа с авторизацией](#Работа-с-авторизацией)
- [Работа с пагинациями](#Работа-с-пагинациями)

### **Преимущества GraphQL над REST**

- Решение проблемы множества запросов REST
- Решение проблемы избыточности данных REST

[назад](#Шпаргалка-по-GraphQL)

### **Как работает GraphQL**

GraphQL - применяет декларативный графо-подобный подход для работы с данными. Возможность формировать структуру ответа на клиенте

- Все данные внутри GraphQL описываются с помощью Type Defenition (Схемы типов)
- При создании вопроса просто спрашиваем то ,что нам нужно
- Получаем предсказуемый результат от бэкенда

![alt text](https://i.imgur.com/ahJ5xva.png)

[назад](#Шпаргалка-по-GraphQL)

### **REST vs GraphQL**

![alt text](https://i.imgur.com/JVlsrRX.png)

[назад](#Шпаргалка-по-GraphQL)

### **Типы данных**

- Скалярные типы
  - **Int**: 32-битное целое число
  - **Float**: Числовое значение с плавающей точкой
  - **String**: Символьная последовательность UTF-8
  - **Boolean**: True или False
  - **ID**: уникальный идентификатор (строка)
- Кастомные типы
  - **scalar Date**
- **Enum (перечисление одного из возможных типов)**
  ```typescript
   enum Episode {
     NEWHOPE
     EMPIRE
     JEDI
   }
  ```
- **Null и Non-null**

  ```typescript
   type Character {
     name: String! // Non-null
     city: String  // по умолчанию Null
   }
  ```

  ```typescript
     type Character {
      myField: [String!]
     }

     myField: null // valid
     myField: [] // valid
     myField: ['a', 'b'] // valid
     myField: ['a', null, 'b'] // error
  ```

  ```typescript
     type Character {
      myField: [String]!
     }

     myField: null // error
     myField: [] // valid
     myField: ['a', 'b'] // valid
     myField: ['a', null, 'b'] // valid
  ```

  ```typescript
     type Character {
      myField: [String!]!
     }

     myField: null // error
     myField: [] // valid
     myField: ['a', 'b'] // valid
     myField: ['a', null, 'b'] // error
  ```

- Абстрактные типы

  - **Interfaces**

  ```typescript
  interface Character {
    id: ID!
    name: String!
    friends: [Character]
    appearsIn: [Episode]!
  }

  type Human imlements Character {
    id: ID!
    name: String!
    friends: [Character]
    appearsIn: [Episode]!
    starships:[Starship]
    totalCredits:Int
  }

  type Droid imlements Character {
    id: ID!
    name: String!
    friends: [Character]
    appearsIn: [Episode]!
    primaryFunction: String
  }
  ```

  - **Union**

  ```typescript
  union SearchResult = Human | Droid | Starship
  ```

  - **Input**

  ```typescript
  input ReviewInput {
    stars: Int!
    commentary: String
  }
  ```

[назад](#Шпаргалка-по-GraphQL)

### **Три операции GraphQL**

- Query - чтение данных
- Mutation - создание, обновление, удаление данных
- Subscribtions- подписка на изменение данных

![alt text](https://i.imgur.com/5qylhIM.png)

[назад](#Шпаргалка-по-GraphQL)

### **Queries**

- Простой Query

  ![alt text](https://i.imgur.com/3Zilvvy.png)

- Вложенный Query

  ![alt text](https://i.imgur.com/DLWPDQs.png)

- Query с аргументами

  ![alt text](https://i.imgur.com/zFwEKIo.png)

- Variables

  ![alt text](https://i.imgur.com/9qjK9r8.png)

- Defaults variables

  ![alt text](https://i.imgur.com/MX8OTHc.png)

- Aliases

  ![alt text](https://i.imgur.com/qGsCBEI.png)

- Fragments

  ![alt text](https://i.imgur.com/BE0zPey.png)

- Inline fragments

  ![alt text](https://i.imgur.com/lP7jOHd.png)

[назад](#Шпаргалка-по-GraphQL)

### **Mutations**

![alt text](https://i.imgur.com/u486bOf.png)

прим. Мутация может возвращать все что угодно

[назад](#Шпаргалка-по-GraphQL)

### **Subscribtions**

- Подписки позволяют автоматически получать новые данные
- GraphQL не декларирует работу с подписками
- Apollo предоставляет удобный механизм по работе с подписками
- Под капотом подписки работают с помощью технологии WebSocket
- Подписки хорошо и с коробки работают только на Node.js

Пример

```typescript
subscription {
  petReturned {
    pet {
      id
      name
    }
  }
}
```

[назад](#Шпаргалка-по-GraphQL)

### **Директивы**

```typescript

// Поле будет присутсвовать, если внутри if: true
@include(if: Boolean)

// Поле будет пропущено, если внутри if: true
@skip(if: Boolean)

```

![alt text](https://i.imgur.com/Y1XYYJs.png)

[назад](#Шпаргалка-по-GraphQL)

### **Security**

- Закрыть схему для Production окружения
- Контролировать уровень открытости схемы

[назад](#Шпаргалка-по-GraphQL)

### **Meta Fields**

![alt text](https://i.imgur.com/BAr0Pig.png)

typename - указывает тип запрашиваемой сущности

[назад](#Шпаргалка-по-GraphQL)

### **Работа с авторизацией**

- Если данных нет то для этого поля сервер возвращает null
- Если данные недоступные для неавторизованных пользователей сервер всегда возвращает null
- GraphQL не декларирует генерацию ошибок в случае недоступности данных

Решение через стороний сервер, например Apollo:

- Наличие единого GraphQL интерфейса
- Удобство при работе с авторизацией

```typescript
```

[назад](#Шпаргалка-по-GraphQL)

### **Работа с пагинациями**

- GraphQL не декларирует работу с пагинацией
- Популярные варианты:

  - ```typescript
    // Добавление аргументов
    friends(first:2 offset:2)
    // Добавление аргументов с индентифиатором сущности
    friends(first:2 after:$friendId)
    // Добавление аргументов с использованием курсора
    friends(first:2 after:$friendCursor)
    ```

- Примеры

![alt text](https://i.imgur.com/T9auCZv.png)
![alt text](https://i.imgur.com/qbOxBzT.png)

[назад](#Шпаргалка-по-GraphQL)
