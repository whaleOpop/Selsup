## Обзор

`CrptApi` — это простой Java-клиент для взаимодействия с CRPT API. Он предоставляет функциональность для создания документов асинхронно, отправляя HTTP POST-запросы на CRPT. Класс использует `HttpClient` для отправки запросов и Gson для сериализации JSON.

## Возможности

- **Асинхронные HTTP-запросы**: Клиент API отправляет HTTP-запросы асинхронно, используя `CompletableFuture`.
- **Ограничение скорости запросов**: Клиент реализует базовый механизм ограничения скорости, ограничивающий количество запросов в указанный временной интервал.
- **Сериализация JSON**: Клиент использует библиотеку Gson для сериализации Java-объектов в формат JSON.
- **Обработка документов**: Клиент обрабатывает создание документов с использованием пользовательского класса `Document`, который включает различные атрибуты и цифровую подпись.

## Необходимые условия

- **Java 11 или выше**: Код использует `java.net.http.HttpClient`, который был введен в Java 11.
- **Библиотека Gson**: Убедитесь, что библиотека Gson добавлена в зависимости вашего проекта.

## Установка

Чтобы использовать этот класс в вашем проекте:

1. **Клонируйте или скачайте репозиторий**: Скопируйте файл `CrptApi.java` в ваш проект.
2. **Добавьте зависимости**: Убедитесь, что у вас добавлена библиотека Gson. Если вы используете Maven, добавьте следующее в ваш `pom.xml`:

   ```xml
   <dependency>
       <groupId>com.google.code.gson</groupId>
       <artifactId>gson</artifactId>
       <version>2.8.8</version>
   </dependency>
   ```

3. **Компиляция и запуск**: Скомпилируйте Java-файл и запустите его как часть вашего приложения.

## Использование

### Инициализация

Чтобы создать экземпляр `CrptApi`, укажите единицу времени и лимит запросов. Например:

```java
CrptApi api = new CrptApi(TimeUnit.MINUTES, 10);
```

Это создаст API-клиент, который позволяет до 10 запросов в минуту.

### Создание документа

Чтобы создать документ, создайте объект `Document` и заполните его необходимыми полями:

```java
Document document = new Document();
document.setDoc_id("12345");
document.setDoc_type("Example");
```

Вы также можете создать и установить объекты `Description` и `Product` при необходимости.

### Отправка запроса

Для отправки запроса асинхронно используйте метод `createDocumentAsync`, передав документ и его цифровую подпись:

```java
String signature = "Пример подписи";

try {
    api.createDocumentAsync(document, signature).join();
} catch (RuntimeException e) {
    e.printStackTrace(); // Обработка исключений
}
```

### Обработка ответа

Метод `createDocumentAsync` возвращает `CompletableFuture<String>`, который содержит тело ответа в случае успешного запроса. Если код статуса не равен 200, выбрасывается `RuntimeException`.

### Пример

Вот полный пример использования `CrptApi`:

```java
public static void main(String[] args) {
    CrptApi api = new CrptApi(TimeUnit.MINUTES, 10);

    Document document = new Document();
    document.setDoc_id("12345");
    document.setDoc_type("Example");

    String signature = "Пример подписи";

    try {
        api.createDocumentAsync(document, signature).join();
    } catch (RuntimeException e) {
        e.printStackTrace(); // или используйте логирование
    }
}
```

## Ограничение скорости

Конструктор `CrptApi` принимает `TimeUnit` и `requestLimit`, которые вместе определяют скорость, с которой могут быть выполнены запросы. Например, если лимит составляет 10 запросов в минуту, клиент гарантирует, что не будет отправлено более 10 запросов в течение любых 60 секунд.

## Обработка ошибок

Если сервер отвечает с кодом статуса, отличным от 200, выбрасывается `RuntimeException` с сообщением, указывающим на сбой. Рекомендуется правильно обрабатывать такие исключения в вашем приложении.
