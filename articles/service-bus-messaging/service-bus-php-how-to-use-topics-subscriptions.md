---
title: Jak používat Azure Service Bus témata s PHP
description: V tomto kurzu se naučíte používat Azure Service Bus témata a odběry z aplikace PHP.
ms.devlang: PHP
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 706f523fdfb3c710bb16b048cfc68ce98875adb1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88066198"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Rychlý Start: jak používat Service Bus témata a odběry pomocí PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

V tomto článku se dozvíte, jak používat Service Bus témata a odběry. Ukázky jsou napsané v PHP a používají [sadu Azure SDK pro php](https://github.com/Azure/azure-sdk-for-php). Mezi zahrnuté scénáře patří:

- Vytváření témat a předplatných 
- Vytváření filtrů předplatného 
- Odesílání zpráv do tématu 
- Příjem zpráv z předplatného
- Odstraňování témat a předplatných

## <a name="prerequisites"></a>Předpoklady
1. Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat výhody pro [předplatitele sady Visual Studio nebo MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Postupujte podle kroků v [rychlém startu: použijte Azure Portal k vytvoření Service Bus tématu a odběry tématu](service-bus-quickstart-topics-subscriptions-portal.md) k vytvoření **oboru názvů** Service Bus a získání **připojovacího řetězce**.

    > [!NOTE]
    > V tomto rychlém startu vytvoříte **téma** a **předplatné** k tématu pomocí **php** . 

## <a name="create-a-php-application"></a>Vytvoření aplikace PHP
Jediným požadavkem pro vytvoření aplikace PHP, která přistupuje k Blob service Azure, je odkazování na třídy v [sadě Azure SDK pro php](https://github.com/Azure/azure-sdk-for-php) z vašeho kódu. Pomocí libovolného vývojového nástroje můžete vytvořit aplikaci nebo Poznámkový blok.

> [!NOTE]
> Vaše instalace PHP musí také mít nainstalované a povolené [rozšíření OpenSSL](https://php.net/openssl) .
> 
> 

Tento článek popisuje, jak používat funkce služby, které lze volat v rámci aplikace PHP místně, nebo v kódu spuštěném v rámci webové role Azure, role pracovního procesu nebo webu.

## <a name="get-the-azure-client-libraries"></a>Získání klientských knihoven Azure

### <a name="install-via-composer"></a>Instalace prostřednictvím skladatele
1. V kořenovém adresáři projektu vytvořte soubor s názvem **composer.js** a přidejte do něj následující kód:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. V kořenovém adresáři projektu stáhněte **[skladatele. phar] [skladatele-phar]** .
3. Otevřete příkazový řádek a v kořenovém adresáři projektu spusťte následující příkaz.
   
    ```
    php composer.phar install
    ```

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití Service Bus
Použití rozhraní Service Bus API:

1. Odkazování na soubor automatického zavaděče pomocí příkazu [require_once][require-once] .
2. Odkázat na libovolné třídy, které můžete použít.

Následující příklad ukazuje, jak zahrnout soubor automatického zavaděče a odkazovat na třídu **ServiceBusService** .

> [!NOTE]
> V tomto příkladu (a dalších příkladech v tomto článku) se předpokládá, že máte nainstalované klientské knihovny PHP pro Azure přes skladatele. Pokud jste knihovny nainstalovali ručně nebo jako balíček pro HRUŠKy, musíte se odkázat na soubor automatického zavaděče **windowsazure. php** .
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

V následujících příkladech `require_once` je příkaz vždy zobrazen, ale jsou odkazovány pouze třídy, které jsou nezbytné pro spuštění příkladu.

## <a name="set-up-a-service-bus-connection"></a>Nastavení Service Busho připojení
Chcete-li vytvořit instanci klienta Service Bus, musíte nejprve mít platný připojovací řetězec v tomto formátu:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Kde `Endpoint` je obvykle formát `https://[yourNamespace].servicebus.windows.net` .

Chcete-li vytvořit libovolného klienta služby Azure, musíte použít `ServicesBuilder` třídu. Další možnosti:

* Předání připojovacího řetězce přímo do něj.
* Použijte **CloudConfigurationManager (ccm)** pro kontrolu několika externích zdrojů pro připojovací řetězec:
  * Ve výchozím nastavení se dodává s podporou pro jednu externí proměnnou zdrojového prostředí.
  * Nové zdroje můžete přidat rozšířením třídy `ConnectionStringSource`.

Ve zde uvedených příkladech se připojovací řetězec předává přímo.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Vytvoření tématu
Můžete provádět operace správy pro Service Bus témata prostřednictvím `ServiceBusRestProxy` třídy. `ServiceBusRestProxy`Objekt je vytvořen pomocí `ServicesBuilder::createServiceBusService` metody Factory s příslušným připojovacím řetězcem, který zapouzdřuje oprávnění tokenu pro jeho správu.

Následující příklad ukazuje, jak vytvořit instanci `ServiceBusRestProxy` a volání `ServiceBusRestProxy->createTopic` pro vytvoření tématu s názvem `mytopic` v rámci `MySBNamespace` oboru názvů:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Můžete použít `listTopics` metodu pro `ServiceBusRestProxy` objekty a ověřit, zda téma se zadaným názvem již existuje v oboru názvů služby.
> 
> 

## <a name="create-a-subscription"></a>Vytvoření odběru
Odběry témat jsou také vytvořeny s `ServiceBusRestProxy->createSubscription` metodou. Odběry mají názvy a můžou mít volitelné filtry, které omezují výběr zpráv odesílaných do virtuální fronty odběru.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Vytvoření odběru s výchozím filtrem (MatchAll).
Pokud při vytvoření nového předplatného není zadaný žádný filtr, použije se filtr **MatchAll** (výchozí). Když se použije filtr **MatchAll** , všechny zprávy publikované v tématu se umístí do virtuální fronty odběru. Následující příklad vytvoří odběr s názvem `mysubscription` a použije výchozí filtr **MatchAll** .

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

### <a name="create-subscriptions-with-filters"></a>Vytvoření odběru s filtry
Můžete taky vytvořit filtry, které vám umožní zprávy odeslané do tématu zobrazit v konkrétním odběru tématu. Nejpružnější typ filtru podporovaný předplatnými je [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), které implementuje podmnožinu SQL92. Filtry SQL pracují s vlastnostmi zpráv publikované do tématu. Další informace o SqlFilters naleznete v tématu [vlastnost SqlFilter. syntaxi][sqlfilter].

> [!NOTE]
> Každé pravidlo v předplatném zpracovává příchozí zprávy nezávisle a přidává jejich výsledné zprávy do předplatného. Kromě toho má každé nové předplatné výchozí objekt **Rule** s filtrem, který přidává všechny zprávy z tématu do předplatného. Chcete-li dostávat pouze zprávy, které odpovídají vašemu filtru, je nutné odebrat výchozí pravidlo. Výchozí pravidlo můžete odebrat pomocí `ServiceBusRestProxy->deleteRule` metody.
> 
> 

Následující příklad vytvoří odběr s názvem `HighMessages` **SqlFilter** , který vybere pouze zprávy, které mají vlastní `MessageNumber` vlastnost větší než 3. Informace o přidávání vlastních vlastností do zpráv naleznete v tématu [odeslání zpráv do tématu](#send-messages-to-a-topic) .

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Tento kód vyžaduje použití dalšího oboru názvů: `WindowsAzure\ServiceBus\Models\SubscriptionInfo` .

Podobně následující příklad vytvoří odběr s názvem `LowMessages` s `SqlFilter` tím, že vybere pouze zprávy, které mají `MessageNumber` vlastnost menší nebo rovna 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Když se teď zpráva pošle do `mytopic` tématu, je vždycky Doručená příjemcům, kteří se přihlásili k `mysubscription` předplatnému, a selektivně doručovat příjemcům `HighMessages` a `LowMessages` předplatným (v závislosti na obsahu zprávy).

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu
Chcete-li odeslat zprávu do Service Bus téma, vaše aplikace volá `ServiceBusRestProxy->sendTopicMessage` metodu. Následující kód ukazuje, jak odeslat zprávu do tématu, které bylo `mytopic` dříve vytvořeno v rámci `MySBNamespace` oboru názvů služby.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Zprávy odeslané do témat Service Bus jsou instance třídy [BrokeredMessage][BrokeredMessage]. Objekty [BrokeredMessage][BrokeredMessage] mají sadu standardních vlastností a metod, jakož i vlastnosti, které lze použít pro uložení vlastních vlastností specifických pro aplikace. Následující příklad ukazuje, jak odeslat pět testovacích zpráv do `mytopic` dříve vytvořeného tématu. `setProperty`Metoda se používá k přidání vlastní vlastnosti ( `MessageNumber` ) ke každé zprávě. `MessageNumber`Hodnota vlastnosti se u každé zprávy liší (tuto hodnotu můžete použít k určení, které odběry obdrží, jak je znázorněno v části [vytvoření předplatného](#create-a-subscription) ):

```php
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);

    // Set custom property.
    $message->setProperty("MessageNumber", $i);

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených v tématu není omezený, ale celková velikost zpráv držených v tématu omezená je. Tento horní limit velikosti tématu je 5 GB. Další informace o kvótách najdete v tématu [Service Bus kvóty][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Přijímání zpráv z předplatného
Nejlepším způsobem, jak přijímat zprávy z předplatného, je použití `ServiceBusRestProxy->receiveSubscriptionMessage` metody. Zprávy mohou být přijímány ve dvou různých režimech: [ *ReceiveAndDelete* a *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode). Výchozí hodnota je **PeekLock**.

Při použití režimu [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) je přijetí jednorázová operace – tzn. když Service Bus přijme požadavek na čtení zprávy v odběru, označí zprávu jako spotřebovávanou a vrátí ji do aplikace. [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) * režim je nejjednodušší model a funguje nejlépe ve scénářích, ve kterých aplikace může tolerovat nezpracovávání zprávy, když dojde k selhání. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že Service Bus označila zprávu jako spotřebovaná, pak když se aplikace znovu spustí a začne znovu přijímat zprávy, vynechala zprávu, která byla spotřebována před selháním.

Ve výchozím režimu [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) se příjem zprávy stane dvě operace fáze, která umožňuje podporovat aplikace, které nemůžou tolerovat chybějící zprávy. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Poté, co aplikace dokončí zpracování zprávy (nebo je uloží spolehlivě pro budoucí zpracování), dokončí druhou fázi procesu příjmu tím, že se přijatá zpráva pošle do `ServiceBusRestProxy->deleteMessage` . Když Service Bus uvidí `deleteMessage` volání, označí zprávu jako spotřebou a odebere ji z fronty.

Následující příklad ukazuje, jak přijmout a zpracovat zprávu pomocí režimu [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) (výchozí režim). 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Postupy: zpracování chyb aplikace a nečitelných zpráv
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce z nějakého důvodu nemůže zprávu zpracovat, může volat `unlockMessage` metodu na přijaté zprávě (místo `deleteMessage` metody). Způsobuje Service Bus odemčení zprávy ve frontě a její zpřístupnění pro opětovné přijetí, a to buď pomocí stejné aplikace, nebo jiné náročné aplikace.

Je také časový limit přidružený ke zprávě uzamčený ve frontě a pokud aplikace nedokáže zpracovat zprávu před vypršením časového limitu zámku (například pokud dojde k selhání aplikace), Service Bus automaticky odemkne zprávu a zpřístupní ji, aby byla k dispozici.

V případě, že aplikace po zpracování zprávy dojde k chybě, ale před `deleteMessage` vydáním žádosti, je zpráva po restartování znovu doručena do aplikace. Tento typ zpracování se často nazývá *alespoň po* zpracování; To znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může být stejná zpráva doručena znovu. Pokud scénář nemůže tolerovat duplicitní zpracování, vývojáři aplikací by měli přidat další logiku do aplikací pro zpracování duplicitního doručování zpráv. Často se dosahuje pomocí `getMessageId` metody zprávy, která zůstává konstantní během pokusů o doručení.

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů
Chcete-li odstranit téma nebo odběr, použijte `ServiceBusRestProxy->deleteTopic` metody nebo v `ServiceBusRestProxy->deleteSubscripton` uvedeném pořadí. Pokud se odstraní téma, odstraní se i všechny odběry registrované k tomuto tématu.

Následující příklad ukazuje, jak odstranit téma s názvem `mytopic` a jeho registrovaná předplatná.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Pomocí `deleteSubscription` metody můžete odběr odstranit nezávisle:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

> [!NOTE]
> Prostředky Service Bus můžete spravovat pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer umožňuje uživatelům připojit se k oboru názvů Service Bus a snadno spravovat entity zasílání zpráv. Tento nástroj poskytuje pokročilé funkce, jako jsou funkce importu a exportu, nebo možnost testovat témata, fronty, odběry, služby Relay, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Další informace najdete v tématu [fronty, témata a předplatná][Queues, topics, and subscriptions].

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md