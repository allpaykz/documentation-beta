# Техническое описание API операций Cash-In Cash-Out

Сервисы предназначены для осуществления операций «Снятие» и «Пополнение» через СЭД Allpay.

Перед чтением этой страницы желательно ознакомиться с [этой](https://github.com/allpaykz/documentation/wiki/%D0%9A%D0%B0%D0%BA%D0%B8%D0%B5-%D0%B2%D0%B8%D0%B4%D1%8B-%D0%B8%D0%BD%D1%82%D0%B5%D0%B3%D1%80%D0%B0%D1%86%D0%B8%D0%B9-%D1%83-%D0%BD%D0%B0%D1%81-%D0%B5%D1%81%D1%82%D1%8C)

**Примеры всех XML запросов в самом конце**

### СОДЕРЖАНИЕ

1. [WSDL ССЫЛКИ НА API]( WSDL,-ССЫЛКИ-НА-API-И-ДОКУМЕНТАЦИЯ)
1. [ПРИНЦИПЫ ВЗАИМОДЕЙСТВИЯ С ПС ALLPAY](#ПРИНЦИПЫ-ВЗАИМОДЕЙСТВИЯ-С-ПС-ALLPAY)
1. [ИСПОЛЬЗОВАНИЕ ТЕСТОВОЙ СИСТЕМЫ ALLPAY](ИСПОЛЬЗОВАНИЕ-ТЕСТОВОЙ-СИСТЕМЫ-ALLPAY)


### WSDL, ССЫЛКИ НА API И ДОКУМЕНТАЦИЯ
   1. [API пополнения и снятия](https://github.com/allpaykz/documentation/wiki/API-%D0%9F%D0%BE%D0%BF%D0%BE%D0%BB%D0%BD%D0%B5%D0%BD%D0%B8%D1%8F-%D0%B8-%D0%A1%D0%BD%D1%8F%D1%82%D0%B8%D1%8F)
   2. [API только пополнение](https://github.com/allpaykz/documentation/wiki/API-%D0%9F%D0%BE%D0%BF%D0%BE%D0%BB%D0%BD%D0%B5%D0%BD%D0%B8%D1%8F)

* Тестовая среда, полностью идентична продакшн среде.
  * [Агентское API Пополнение&Снятие](https://beta.allpay.kz/allpay-public-soap/transaction-management/v1.0?wsdl) **v1.0** (Устарело/Deprecated)
  * [Агентское API Пополнение&Снятие](https://beta.allpay.kz/allpay-public-soap/transaction-management/v1.1?wsdl) **v1.1**
  * [Агентское API только Пополнение](https://beta.allpay.kz/allpay-public-soap/cash-in-transaction-management/v1.1?wsdl) (подходит для терминалов)

* Продакшн среда
  * [Агентское API Пополнение&Снятие](https://mfs.allpay.kz/allpay-public-soap/transaction-management/v1.0?wsdl) **v1.0** (Устарело/Deprecated)
  * [Агентское API Пополнение&Снятие](https://mfs.allpay.kz/allpay-public-soap/transaction-management/v1.1?wsdl) **v1.1**
  * [Агентское API только Пополнение](https://mfs.allpay.kz/allpay-public-soap/cash-in-transaction-management/v1.1?wsdl) (подходит для терминалов)

* Демо проект, реализация наших API - http://beta.allpay.kz/allpay-public-soap-demo/

### ПРИНЦИПЫ ВЗАИМОДЕЙСТВИЯ С ПС ALLPAY

Для обмена данными используется формат XML, для всех видов передаваемых сообщений определена [соответствующая схема XSD](http://allpay.kz/xsd/1.0.0/).

VPN каналы и шлюзы не используются. Для безопасного обмена сообщениями все XML подписываются обеими сторонами. Подпись реализована по стандарту W3C описанными в `XSD` схеме [XmlDSig](https://www.w3.org/TR/xmldsig-core/).
Каждое сообщение должно проходить валидацию по соответствующей XSD схеме, все обязательные поля должны быть заполнены по формату.

Используемая кодировка – UTF-8.

В большинстве схем обмена данными присутствуют общие поля. Все общие поля описаны схемой [OnlineTransactionCommons.xsd](http://allpay.kz/xsd/1.0.0/OnlineTransactionCommons.xsd).
Далее приведено описание и назначения каждого из общих полей.


### ИСПОЛЬЗОВАНИЕ ТЕСТОВОЙ СИСТЕМЫ ALLPAY

Для тестирования сервисов можно подключится к тестовой инфраструктуре ПС Allpay. Запрос на получение доступа к тестовому серверу отправляйте на почту techsupport@allpay.kz
