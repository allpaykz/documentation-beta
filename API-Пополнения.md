# Техническое описание API Пополнения

Сервисы предназначены для осуществления операций «Пополнение» через СЭД Allpay.

Перед чтением этой страницы желательно ознакомиться с [этой](https://github.com/allpaykz/documentation/wiki/%D0%9A%D0%B0%D0%BA%D0%B8%D0%B5-%D0%B2%D0%B8%D0%B4%D1%8B-%D0%B8%D0%BD%D1%82%D0%B5%D0%B3%D1%80%D0%B0%D1%86%D0%B8%D0%B9-%D1%83-%D0%BD%D0%B0%D1%81-%D0%B5%D1%81%D1%82%D1%8C)

**Примеры всех XML запросов в самом конце**

### СОДЕРЖАНИЕ

1. [СЦЕНАРИЙ ПОПОЛНЕНИЯ](#СЦЕНАРИЙ-ПОПОЛНЕНИЯ)
1. [ТЕХНИЧЕСКИЙ СЦЕНАРИЙ ПОПОЛНЕНИЯ](#ТЕХНИЧЕСКИЙ-СЦЕНАРИЙ-ПОПОЛНЕНИЯ)
1. [ОПИСАНИЕ ФОРМАТА СООБЩЕНИЙ](#ОПИСАНИЕ-ФОРМАТА-СООБЩЕНИЙ)
 2. [ОБЩИЕ ПОЛЯ](#ОБЩИЕ-ПОЛЯ)
 2. [ПОПОЛНЕНИЕ](#ПОПОЛНЕНИЕ)
2. [ИСПОЛЬЗОВАНИЕ ТЕСТОВОЙ СИСТЕМЫ ALLPAY](#ИСПОЛЬЗОВАНИЕ-ТЕСТВОЙ-СИСТЕМЫ-ALLPAY)
2. [ПРИМЕРЫ ДЕМО РЕАЛИЗАЦИЙ](#ПРИМЕРЫ-РЕАЛИЗАЦИЙ)
 3. [НА ЯЗЫКЕ JAVA](#РЕАЛИЗАЦИЯ-НА-ЯЗЫКЕ-java)
1. [ПРИМЕРЫ ЗАПРОСОВ ОТВЕТОВ](ПРИМЕРЫ-ЗАПРОСОВ-ОТВЕТОВ)

### СЦЕНАРИЙ ПОПОЛНЕНИЯ

1. Клиент просит кассира Пополнить деньги на кошелёк allpay
2. Клиент передает наличные и логин кошелька allpay Кассиру
2. Кассир формирует запрос на Пополнение кошелька, используя логин Клиента. Кассир отправляет запрос на сервер allpay
3. Кассир получает уведомление об успешной транзакции
4. Клиент получает пуш уведомление об успешной транзакции

### ТЕХНИЧЕСКИЙ СЦЕНАРИЙ ПОПОЛНЕНИЯ

1. Формируем запрос `createCashInTransaction`
2. Метод создает транзакцию, блокирует деньги агента. Статус транзакции становится PENDING (ОЖИДАНИЕ).
3. Формируем запрос `completeTransaction`, номер транзакции берём из ответа от `createCashOutTransaction`
4. Получаем ответ, заблокированные деньги списываются. Статус транзакции становится COMPLETE (ЗАВЕРШЕН).

В случае проблем или ошибок после шага 2 или 3 транзакцию можно отменить используя метод `declineTransaction`.

### ОПИСАНИЕ ФОРМАТА СООБЩЕНИЙ

#### ОБЩИЕ ПОЛЯ

`language` — язык текстов. От него зависит на каком языке будут приходить тексты ошибок, статусов или других текстовых описаний.

`userName` — логин пользователя в системе Allpay. 

`amount` — сумма денег указанная в KZT

`commission` — Содержит комиссию на транзакцию

`transactionInfo` — Содержит информацию о транзакции:
 * `GUID` — уникальный идентификатор транзакции на стороне пользователя API
 * `amount` — сумма в KZT
 * `transactionId` — идентификатор транзакции
 * `transactionStatus` — статус транзакции
 * `commission` — комиссия на транзакцию

`onlineTransactionResponseHeader` — общее поле для всех ответов на запросы. Содержит:
 * `userMessage` — поле информативного характера. Обычно это сообщения, которое можно показать пользователю. Содержит описание ошибок, статусов и т.д.
 * `developerMessage` — поле информативного характера. Содержит техническое описание и коды ошибок.
 * `status` — Статус запроса. Описание статусов будет приведено ниже.
 * `timestamp` — дата ответа
 * `token` - токен

`transactionResponse` — содержит информацию о транзакции.
 * `header` — составное поле типа onlineTransactionResponseHeader
 * `transactionInfo` — составное поле типа transactionInfo

`onlineTransactionRequestHeader` — общее поле для всех запросов. Содержит:
 * `lang` — определяет язык. Поле типа language, описанного выше.
 * `timestamp` — дата отправки запроса.
 * `requester` — логин отправителя в системе Allpay.
 
#### ПОПОЛНЕНИЕ
 
##### 1.1 Проверка пользователя запрос(checkUserAndValidateCashIn)

Пример запроса:

Входящие параметры:

userName - логин пользователя которого нужно пополнить

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns="http://www.allpay.kz/mfs/soap/CashInTransactionManagement/1.1">
   <soapenv:Header/>
   <soapenv:Body>
      <ns:checkUserAndValidateCashIn>
         <!--Optional:-->
         <CheckUserRequest>
            <header>
               <!--Optional:-->
               <lang>ru</lang>
               <timestamp>?</timestamp>
               <requester>?</requester>
               <!--Optional:-->
               <token>?</token>
            </header>
            <userName>?</userName>
         </CheckUserRequest>
      </ns:checkUserAndValidateCashIn>
   </soapenv:Body>
</soapenv:Envelope>
```

##### 1.2 Проверка пользователя ответ(checkUserAndValidateCashIn)

Блок basicUserInfo - опционален. Если он отстутсвует - значит такого пользователя нет. Запрашивать можно только клиентов. Блок basicUserInfo содержит
identified - является ли пользователь идентифицированным
IIN - ИИН - Индивидуальный идентификационный номер
firstName - имя
lastName - фамилия
patronymic - отчество


```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns="http://www.allpay.kz/mfs/soap/CashInTransactionManagement/1.1">
   <soapenv:Header/>
   <soapenv:Body>
      <ns:checkUserAndValidateCashInResponse>
         <!--Optional:-->
         <return>
            <header>
               <userMessage>?</userMessage>
               <developerMessage>?</developerMessage>
               <status>?</status>
               <timestamp>?</timestamp>
            </header>
            <!--Optional:-->
            <basicUserInfo>
               <identified>?</identified>
               <!--Optional:-->
               <IIN>?</IIN>
               <!--Optional:-->
               <firstName>?</firstName>
               <!--Optional:-->
               <lastName>?</lastName>
               <!--Optional:-->
               <patronymic>?</patronymic>
            </basicUserInfo>
         </return>
      </ns:checkUserAndValidateCashInResponse>
   </soapenv:Body>
</soapenv:Envelope>
```

##### 2.1 Проведение пополнения запрос(createCashInPayment)

Входящие параметры:

- GUID - (он же RRN) номер транзакции (должен быть уникален в разрезе мерчанта. Иными словами rrn + requester - у нас уникальны);
- amount - количество на которое необходимо пополнить
- toUserName - клиент чей счет необходимо пополнить

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns="http://www.allpay.kz/mfs/soap/CashInTransactionManagement/1.1">
   <soapenv:Header/>
   <soapenv:Body>
      <ns:createCashInPayment>
         <!--Optional:-->
         <CashInRequest>
            <header>
               <!--Optional:-->
               <lang>ru</lang>
               <timestamp>?</timestamp>
               <requester>?</requester>
               <!--Optional:-->
               <token>?</token>
            </header>
            <toUserName>?</toUserName>
            <amount>?</amount>
            <GUID>?</GUID>
         </CashInRequest>
      </ns:createCashInPayment>
   </soapenv:Body>
</soapenv:Envelope>
```


##### 2.2 Проведение пополнения ответ(createCashInPayment)

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns="http://www.allpay.kz/mfs/soap/CashInTransactionManagement/1.1">
   <soapenv:Header/>
   <soapenv:Body>
      <ns:createCashInPaymentResponse>
         <!--Optional:-->
         <return>
            <header>
               <userMessage>?</userMessage>
               <developerMessage>?</developerMessage>
               <status>?</status>
               <timestamp>?</timestamp>
            </header>
            <terminalPaymentTransactionStatus>?</terminalPaymentTransactionStatus>
            <!--Optional:-->
            <reason>?</reason>
         </return>
      </ns:createCashInPaymentResponse>
   </soapenv:Body>
</soapenv:Envelope>
```

### ИСПОЛЬЗОВАНИЕ ТЕСТОВОЙ СИСТЕМЫ ALLPAY

Для тестирования сервисов можно подключится к тестовой инфраструктуре ПС Allpay. Запрос на получение доступа к тестовому серверу отправляйте на почту techsupport@allpay.kz

### ПРИМЕРЫ РЕАЛИЗАЦИЙ

Исходный код демо проектов находится [здесь](https://github.com/allpaykz/allpay-public/tree/master/allpay-public-soap)

##### РЕАЛИЗАЦИЯ НА ЯЗЫКЕ JAVA

Проект находится [здесь](https://github.com/allpaykz/allpay-public/tree/master/allpay-public-soap)

### ПРИМЕРЫ ЗАПРОСОВ ОТВЕТОВ
Скоро будет