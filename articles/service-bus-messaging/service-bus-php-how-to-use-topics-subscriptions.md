---
title: Jak používat témata Azure Service Bus s PHP
description: V tomto kurzu se dozvíte, jak používat témata azure service bus a předplatná z aplikace PHP.
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: faaa4bbd-f6ef-42ff-aca7-fc4353976449
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 92f25f4bdac4942478c93f717c81eadd2c2f5b4a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760670"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Úvodní příručka: Jak používat témata a předplatná služby Service Bus s PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Tento článek ukazuje, jak používat témata service bus a odběry. Ukázky jsou zapsány v PHP a použít [Azure SDK pro PHP](https://github.com/Azure/azure-sdk-for-php). Zahrnuté scénáře zahrnují:

- Vytváření témat a předplatných 
- Vytváření filtrů předplatného 
- Odesílání zpráv k tématu 
- Příjem zpráv z předplatného
- Odstranění témat a předplatných

## <a name="prerequisites"></a>Požadavky
1. Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat [výhody pro předplatitele sady Visual Studio nebo MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Postupujte podle kroků na [úvodním panelu: Pomocí portálu Azure vytvořte téma služby Service Bus a odběry tématu](service-bus-quickstart-topics-subscriptions-portal.md) k vytvoření **oboru názvů** Service Bus a získání **připojovacího řetězce**.

    > [!NOTE]
    > V tomto rychlém startu vytvoříte **téma** a **předplatné** tohoto tématu pomocí **PHP.** 

## <a name="create-a-php-application"></a>Vytvoření aplikace PHP
Jediným požadavkem pro vytvoření aplikace PHP, která přistupuje ke službě Azure Blob, je odkazovat na třídy v [azure sdk pro PHP](https://github.com/Azure/azure-sdk-for-php) z vašeho kódu. K vytvoření aplikace nebo poznámkového bloku můžete použít libovolné vývojové nástroje.

> [!NOTE]
> Instalace PHP musí mít také nainstalované a povolené [rozšíření OpenSSL.](https://php.net/openssl)
> 
> 

Tento článek popisuje, jak používat funkce služby, které lze volat v rámci aplikace PHP místně nebo v kódu spuštěném v rámci webové role Azure, role pracovního procesu nebo webu.

## <a name="get-the-azure-client-libraries"></a>Získání klientských knihoven Azure

### <a name="install-via-composer"></a>Instalace přes Composer
1. Vytvořte soubor s názvem **composer.json** v kořenovém adresáři projektu a přidejte do něj následující kód:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Stáhněte si **[composer.phar][composer-phar]** v kořenovém adresáři projektu.
3. Otevření příkazového řádku a spuštění následujícího příkazu v kořenovém adresáři projektu
   
    ```
    php composer.phar install
    ```

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití služby Service Bus
Použití api sběrnice:

1. Odkaz na soubor automatického zavaděče pomocí [příkazu require_once.][require-once]
2. Odkaz na všechny třídy, které můžete použít.

Následující příklad ukazuje, jak zahrnout soubor automatického zavaděče a odkazovat na třídu **ServiceBusService.**

> [!NOTE]
> Tento příklad (a další příklady v tomto článku) předpokládá, že jste nainstalovali klientské knihovny PHP pro Azure prostřednictvím Composer. Pokud jste knihovny nainstalovali ručně nebo jako balíček PEAR, musíte odkazovat na soubor automatického zavaděče **WindowsAzure.php.**
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

V následujících příkladech `require_once` je příkaz vždy zobrazen, ale jsou odkazovány pouze třídy nezbytné pro provedení příkladu.

## <a name="set-up-a-service-bus-connection"></a>Nastavení připojení služby Service Bus
Chcete-li vytvořit instanci klienta služby Service Bus, musíte mít nejprve platný připojovací řetězec v tomto formátu:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Kde `Endpoint` je obvykle formátu `https://[yourNamespace].servicebus.windows.net`.

Chcete-li vytvořit libovolného klienta `ServicesBuilder` služby Azure, musíte použít třídu. Můžete:

* Předajte mu připojovací řetězec přímo.
* Pomocí **cloudconfigurationmanageru (CCM)** zkontrolujte více externích zdrojů pro připojovací řetězec:
  * Ve výchozím nastavení je dodáván s podporou jednoho externího zdroje - proměnných prostředí.
  * Nové zdroje můžete přidat rozšířením třídy `ConnectionStringSource`.

Ve zde uvedených příkladech se připojovací řetězec předává přímo.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Vytvoření tématu
Operace správy pro témata service bus `ServiceBusRestProxy` můžete provádět prostřednictvím třídy. Objekt `ServiceBusRestProxy` je vytvořen pomocí `ServicesBuilder::createServiceBusService` metody factory s příslušným připojovacím řetězcem, který zapouzdřuje oprávnění tokenu ke správě.

Následující příklad ukazuje, jak vytvořit `ServiceBusRestProxy` konkretizovat a volat `ServiceBusRestProxy->createTopic` k vytvoření tématu pojmenovaného `mytopic` `MySBNamespace` v oboru názvů:

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
> Metodu `listTopics` u `ServiceBusRestProxy` objektů můžete použít ke kontrole, zda téma se zadaným názvem již v oboru názvů služby existuje.
> 
> 

## <a name="create-a-subscription"></a>Vytvoření odběru
Téma odběry jsou také `ServiceBusRestProxy->createSubscription` vytvořeny s metodou. Odběry mají názvy a můžou mít volitelné filtry, které omezují výběr zpráv odesílaných do virtuální fronty odběru.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Vytvoření odběru s výchozím filtrem (MatchAll).
Pokud není při vytvoření nového předplatného zadán žádný filtr, použije se filtr **MatchAll** (výchozí). Při použití filtru **MatchAll** jsou všechny zprávy publikované do tématu umístěny do virtuální fronty předplatného. Následující příklad vytvoří odběr `mysubscription` s názvem a používá výchozí **MatchAll** filtr.

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
Můžete taky vytvořit filtry, které vám umožní zprávy odeslané do tématu zobrazit v konkrétním odběru tématu. Nejflexibilnějším typem filtru podporovaného odběry je [sqlfilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), který implementuje podmnožinu SQL92. Filtry SQL pracují s vlastnostmi zpráv publikované do tématu. Další informace o filtrech SqlFilters naleznete v tématu [SqlFilter.SqlExpression Property][sqlfilter].

> [!NOTE]
> Každé pravidlo o předplatném zpracovává příchozí zprávy nezávisle a přidává jejich výsledné zprávy do předplatného. Kromě toho každé nové předplatné má výchozí **rule** objekt s filtrem, který přidá všechny zprávy z tématu do předplatného. Chcete-li přijímat pouze zprávy odpovídající filtru, je nutné odebrat výchozí pravidlo. Výchozí pravidlo můžete odebrat `ServiceBusRestProxy->deleteRule` pomocí metody.
> 
> 

Následující příklad vytvoří odběr `HighMessages` s názvem **SqlFilter,** který vybere `MessageNumber` pouze zprávy, které mají vlastní vlastnost větší než 3. Informace o přidávání vlastních vlastností do zpráv najdete [v tématu Odesílání zpráv tématu.](#send-messages-to-a-topic)

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Tento kód vyžaduje použití dalšího oboru `WindowsAzure\ServiceBus\Models\SubscriptionInfo`názvů: .

Podobně následující příklad vytvoří odběr `LowMessages` s `SqlFilter` názvem, který vybere pouze `MessageNumber` zprávy, které mají vlastnost menší nebo rovno 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Nyní, když je zpráva `mytopic` odeslána na téma, je vždy doručena příjemcům přihlášeni k `mysubscription` odběru a `HighMessages` `LowMessages` selektivně doručena příjemcům, kteří se přihlásili k odběru a odběry (v závislosti na obsahu zprávy).

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu
Chcete-li odeslat zprávu do tématu `ServiceBusRestProxy->sendTopicMessage` service bus, aplikace volá metodu. Následující kód ukazuje, jak odeslat `mytopic` zprávu na `MySBNamespace` téma dříve vytvořené v oboru názvů služby.

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

Zprávy odeslané do témat Service Bus jsou instance třídy [BrokeredMessage][BrokeredMessage]. [BrokeredMessage][BrokeredMessage] objekty mají sadu standardnívlastnosti a metody, stejně jako vlastnosti, které lze použít k uložení vlastní vlastnosti specifické pro aplikaci. Následující příklad ukazuje, jak odeslat `mytopic` pět testovacích zpráv na téma dříve vytvořené. Metoda `setProperty` se používá k přidání`MessageNumber`vlastní vlastnosti ( ) ke každé zprávě. Hodnota `MessageNumber` vlastnosti se liší u každé zprávy (tuto hodnotu můžete použít k určení, která odběry ji obdrží, jak je znázorněno v části [Vytvořit předplatné):](#create-a-subscription)

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

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených v tématu není omezený, ale celková velikost zpráv držených v tématu omezená je. Tento horní limit velikosti tématu je 5 GB. Další informace o kvótách naleznete v [tématu Kvóty služby Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Příjem zpráv z předplatného
Nejlepší způsob, jak přijímat zprávy z `ServiceBusRestProxy->receiveSubscriptionMessage` předplatného je použít metodu. Zprávy mohou být přijímány ve dvou různých režimech: [ *ReceiveAndDelete* a *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). Výchozí hodnota je **PeekLock**.

Při použití režimu [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) je přijetí jednorázová operace – tzn. když Service Bus přijme požadavek na čtení zprávy v odběru, označí zprávu jako spotřebovávanou a vrátí ji do aplikace. [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) * režim je nejjednodušší model a funguje nejlépe pro scénáře, ve kterých aplikace může tolerovat není zpracování zprávy, když dojde k selhání. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že service bus označil zprávu jako spotřebované, pak při restartování aplikace a začne spotřebovávat zprávy znovu, má zmeškané zprávy, která byla spotřebována před selhání.

Ve výchozím režimu [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) se příjem zprávy stane dvoufázovou operací, která umožňuje podporovat aplikace, které nemohou tolerovat chybějící zprávy. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Po dokončení aplikace zpracování zprávy (nebo ukládá spolehlivě pro budoucí zpracování), dokončí druhou fázi procesu `ServiceBusRestProxy->deleteMessage`příjmu předáním přijaté zprávy . Když service bus `deleteMessage` zobrazí volání, označí zprávu jako spotřebované a odebere ji z fronty.

Následující příklad ukazuje, jak přijímat a zpracovávat zprávu pomocí režimu [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) (výchozí režim). 

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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Postup: zpracování selhání aplikace a nečitelných zpráv
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce není schopen zpracovat zprávu z nějakého `unlockMessage` důvodu, pak může volat `deleteMessage` metodu na přijaté zprávy (namísto metody). Způsobí, že service bus odemknout zprávu ve frontě a zpřístupnit ji k přijetí znovu, a to buď stejnou náročnou aplikací nebo jinou náročnou aplikací.

K dispozici je také časový limit přidružený ke zprávě zamčené ve frontě a pokud aplikace nezpracuje zprávu před vypršením časového limitu uzamčení (například pokud dojde k chybě aplikace), pak service bus zprávu automaticky odemkne a provede ji k dispozici k tomu, aby byly znovu přijaty.

V případě, že aplikace dojde k chybě `deleteMessage` po zpracování zprávy, ale před odesláním požadavku, pak je zpráva znovu doručena do aplikace při restartování. Tento typ zpracování se často nazývá *alespoň jednou* zpracování; to znamená, že každá zpráva je zpracována alespoň jednou, ale v určitých situacích může být stejná zpráva znovu doručena. Pokud scénář nemůže tolerovat duplicitní zpracování, vývojáři aplikací by měli přidat další logiku aplikacím pro zpracování duplicitního doručení zprávy. Často je dosaženo `getMessageId` pomocí metody zprávy, která zůstává konstantní napříč pokusy o doručení.

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů
Chcete-li odstranit téma nebo `ServiceBusRestProxy->deleteTopic` předplatné, `ServiceBusRestProxy->deleteSubscripton` použijte nebo metody, resp. Pokud se odstraní téma, odstraní se i všechny odběry registrované k tomuto tématu.

Následující příklad ukazuje, jak odstranit `mytopic` téma s názvem a jeho registrované odběry.

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

Pomocí této `deleteSubscription` metody můžete odstranit předplatné nezávisle:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

> [!NOTE]
> Prostředky služby Service Bus můžete spravovat pomocí [aplikace Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Průzkumník služby Service Bus umožňuje uživatelům připojit se k oboru názvů service bus a snadno spravovat entity zasílání zpráv. Nástroj poskytuje pokročilé funkce, jako je funkce importu a exportu nebo možnost testovat téma, fronty, předplatná, přenosové služby, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Další informace naleznete [v tématu Fronty, témata a odběry][Queues, topics, and subscriptions].

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
