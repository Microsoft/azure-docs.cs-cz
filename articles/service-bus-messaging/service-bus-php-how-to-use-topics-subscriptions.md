---
title: Jak používat témata Service Bus s PHP | Microsoft Docs
description: Naučte se používat témata Service Bus s PHP v Azure.
services: service-bus-messaging
documentationcenter: php
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: faaa4bbd-f6ef-42ff-aca7-fc4353976449
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/06/2017
ms.author: sethm
ms.openlocfilehash: 4763b172375668213372e6f4f8fc87431c430e53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23868596"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Jak používat témata a odběry Service Bus s PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Tento článek ukazuje, jak používat témata a odběry Service Bus. Ukázky jsou napsané v PHP a použití [Azure SDK pro jazyk PHP](../php-download-sdk.md). Pokryté scénáře zahrnují **vytváření témat a odběrů**, **vytváření filtrů odběrů**, **odesílání zpráv do tématu**, **přijímání zpráv z odběru**, a **odstranění témat a odběrů**.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-php-application"></a>Vytvoření aplikace PHP
Jediný požadavek pro vytvoření aplikace PHP, který přistupuje k službě Azure Blob je referenční třídy v [Azure SDK pro jazyk PHP](../php-download-sdk.md) z vašeho kódu. Všechny nástroje pro vývoj vám pomůže vytvořit aplikaci nebo program Poznámkový blok.

> [!NOTE]
> Instalace PHP musí mít také [OpenSSL rozšíření](http://php.net/openssl) nainstalované a povolené.
> 
> 

Tento článek popisuje, jak používat funkce služby, které může být volána v rámci aplikace PHP místně nebo v kódu běžící v rámci webu, role pracovního procesu nebo webové role Azure.

## <a name="get-the-azure-client-libraries"></a>Získání klienta Azure knihovny
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití služby Service Bus
Použití API pro Service Bus:

1. Reference souboru pomocí automatického zavaděče [require_once] [ require-once] příkaz.
2. Referenční všechny třídy, které můžete použít.

Následující příklad ukazuje, jak se zahrnuje automatického zavaděče souboru a odkaz **ServiceBusService** třídy.

> [!NOTE]
> Tento příklad (a další příklady v tomto článku) předpokládá, že jste nainstalovali PHP klientské knihovny pro Azure prostřednictvím autora. Pokud jste nainstalovali v knihovnách ručně nebo jako balíček HRUŠKAMI, musí odkazovat **WindowsAzure.php** automatického zavaděče souboru.
> 
> 

```php
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

V následujících příkladech `require_once` údajů se vždy zobrazí, ale jenom ty třídy potřebné pro tento příklad provést odkazují.

## <a name="set-up-a-service-bus-connection"></a>Nastavení připojení služby Service Bus
K vytvoření instance služby Service Bus klient Nejdřív musíte mít platný připojovací řetězec v tomto formátu:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Kde `Endpoint` je obvykle ve formátu `https://[yourNamespace].servicebus.windows.net`.

Vytvoření libovolného klienta služby Azure, je nutné použít `ServicesBuilder` třídy. Můžete:

* Připojovací řetězec přímo jí předejte.
* Použití **CloudConfigurationManager (CCM)** zkontrolujte několik externích zdrojů pro připojovací řetězec:
  * Ve výchozím nastavení je teď obsahuje podporu pro jeden externí zdroj – proměnné prostředí.
  * Můžete přidat nové zdroje tím, že rozšíří `ConnectionStringSource` třídy.

Příklady podle zde uvedeného je předaná přímo připojovací řetězec.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Vytvoření tématu
Můžete provádět operace správy témat sběrnice Service Bus přes `ServiceBusRestProxy` třídy. A `ServiceBusRestProxy` objektu je vytvořený pomocí `ServicesBuilder::createServiceBusService` metoda factory řetězcem odpovídající připojení, který zapouzdřuje tokenu oprávněními k její správě.

Následující příklad ukazuje, jak vytvořit instanci `ServiceBusRestProxy` a volání `ServiceBusRestProxy->createTopic` vytvořit téma s názvem `mytopic` v rámci `MySBNamespace` obor názvů:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {        
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
> Můžete použít `listTopics` metodu `ServiceBusRestProxy` objekty, které chcete zkontrolovat, pokud téma se zadaným názvem již existuje v rámci oboru názvů služby.
> 
> 

## <a name="create-a-subscription"></a>Vytvoření odběru
Odběry témat taky jsou vytvořeny pomocí `ServiceBusRestProxy->createSubscription` metoda. Odběry mají názvy a můžou mít volitelné filtry, které omezují výběr zpráv odesílaných do virtuální fronty odběru.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Vytvoření odběru s výchozím filtrem (MatchAll).
Filtr **MatchAll** je výchozí filtr, který se použije v případě, že při vytváření nového odběru nezadáte žádný filtr. Když **MatchAll** filtr se používá, všechny zprávy publikované do tématu jsou umístěny do virtuální fronty odběru. Následující příklad vytvoří odběr s názvem 'mysubscription' a používá výchozí **MatchAll** filtru.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
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
Můžete taky vytvořit filtry, které vám umožní zprávy odeslané do tématu zobrazit v konkrétním odběru tématu. Nejflexibilnější filtr, který odběry podporují je [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter), který implementuje je podmnožinou SQL92. Filtry SQL pracují s vlastnostmi zpráv publikované do tématu. Další informace o SqlFilters najdete v tématu [SqlFilter.SqlExpression vlastnost][sqlfilter].

> [!NOTE]
> Každé pravidlo na předplatném zpracuje příchozí zprávy nezávisle, přidávání jejich výsledek zprávy k předplatnému. Kromě toho má každý nový odběr výchozí **pravidlo** objekt s filtr, který přidá všechny zprávy z tématu k předplatnému. Pokud chcete přijímat pouze zprávy odpovídající vašemu filtru, musíte odebrat výchozí pravidlo. Výchozí pravidla můžete odebrat pomocí `ServiceBusRestProxy->deleteRule` metoda.
> 
> 

Následující příklad vytvoří odběr s názvem `HighMessages` s **SqlFilter** který vybere jen zprávy, které mají vlastní `MessageNumber` vlastnost větší než 3. V tématu [odeslání zprávy do tématu](#send-messages-to-a-topic) informace o přidání vlastních vlastností do zprávy.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Všimněte si, že tento kód vyžaduje použití další oboru názvů: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Podobně platí, tento příklad vytvoří odběr s názvem `LowMessages` s `SqlFilter` který vybere jen zprávy, které mají `MessageNumber` vlastnost menší než nebo rovné 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Teď, když je odeslána zpráva `mytopic` tématu, vždy se dodá příjemci `mysubscription` předplatného a selektivně příjemcům přihlásit k odběru `HighMessages` a `LowMessages` odběry (v závislosti Při obsahu zprávy).

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu
K odeslání zprávy do tématu Service Bus, vaše aplikace volání `ServiceBusRestProxy->sendTopicMessage` metoda. Následující kód ukazuje, jak odeslat zprávu `mytopic` vytvořili v tématu `MySBNamespace` oboru názvů služby.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
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

Zprávy odeslané do témat Service Bus jsou instance [BrokeredMessage] [ BrokeredMessage] třídy. [BrokeredMessage] [ BrokeredMessage] objekty mají sadu standardních vlastností a metod, jakož i vlastnosti, které lze použít pro udržení vlastních vlastností specifické pro aplikaci. Následující příklad ukazuje, jak odeslat 5 zkušebních zpráv do `mytopic` tématu vytvořili. `setProperty` Metoda se používá k přidání vlastní vlastnosti (`MessageNumber`) pro každou zprávu. Všimněte si, že `MessageNumber` vlastnost hodnota se liší u každé zprávy (Tato hodnota slouží k určení, které odběry dostávat, jak je znázorněno v [vytvořit odběr](#create-a-subscription) část):

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

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených v tématu není omezený, ale celková velikost zpráv držených v tématu omezená je. Toto omezení velikost tématu je 5 GB. Další informace o kvótách najdete v tématu [Service Bus kvóty][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Příjem zpráv z odběru
Nejlepší způsob, jak přijmout zprávy z odběru je použití `ServiceBusRestProxy->receiveSubscriptionMessage` metoda. Můžete obdržet zprávy ve dvou různých režimech: [ *ReceiveAndDelete* a *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). Výchozí hodnota je **PeekLock**.

Při použití režimu [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) je přijetí jednorázová operace – tzn. když Service Bus přijme požadavek na čtení zprávy v odběru, označí zprávu jako spotřebovávanou a vrátí ji do aplikace. [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) * režimu je nejjednodušší model a funguje nejlépe ve scénářích, kde aplikace může tolerovat selhání se zpráva nezpracuje. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že Service Bus se už ale zprávu označila jako spotřebovávanou, pak když se aplikace restartuje a začne znovu přijímat zprávy, ji budou neuskutečnily zprávu, která se spotřebovala před havárii.

Ve výchozím [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) režimu, přijímání zprávy se změní na dvě fáze operaci, která umožňuje podporuje aplikace, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Když aplikace dokončí zpracování zprávy (nebo ji bezpečně uloží pro pozdější zpracování), tím potvrdí dokončení druhé fáze přijetí předávání přijaté zprávy do `ServiceBusRestProxy->deleteMessage`. Když Service Bus uvidí `deleteMessage` volání, označí zprávu jako spotřebovávanou a odebrat ji z fronty.

Následující příklad ukazuje, jak přijímat a zpracovávat zprávu pomocí [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) režimu (výchozím režimu). 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Postupy: zpracování pádů aplikace a nečitelných zpráv
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud přijímající aplikace nedokáže zpracovat zprávu z nějakého důvodu, pak může zavolat `unlockMessage` metoda na přijatou zprávu (místo `deleteMessage` metoda). To způsobí, že Service Bus zprávu odemkne ve frontě a zpřístupní ji pro další přijetí, buďto stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

Je také vypršení časového limitu přidružené zpráva uzamčená ve frontě, a pokud se nepodaří aplikace zprávu nezpracuje zámku vyprší časový limit (například pokud aplikace spadne), pak Service Bus zprávu automaticky odemkne a nastavit jej jako k dispozici pro další přijetí.

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než `deleteMessage` požadavku a potom zprávy je víckrát do aplikace odešle znovu. To se často označuje jako *nejméně jednou* zpracování; to znamená, že každá zpráva se zpracuje alespoň jednou ale v některých situacích může být stejná zpráva víckrát. Pokud scénář nemůže tolerovat zpracování duplicitní, pak vývojáři aplikace by měla přidat další logiku aplikace pro zpracování víckrát doručené zprávy. To se často opírá `getMessageId` metoda zprávy, která zůstává konstantní mezi pokusy o doručení.

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů
Chcete-li odstranit tématu nebo předplatného, použijte `ServiceBusRestProxy->deleteTopic` nebo `ServiceBusRestProxy->deleteSubscripton` metody, v uvedeném pořadí. Všimněte si, že se odstraní téma také odstraní všechny odběry registrované k tomuto tématu.

Následující příklad ukazuje, jak odstranit téma s názvem `mytopic` a jeho registrované odběry.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {        
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

Pomocí `deleteSubscription` metodu, můžete odstranit odběr nezávisle:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy front Service Bus, najdete v části [fronty, témata a odběry] [ Queues, topics, and subscriptions] Další informace.

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter
[require-once]: http://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
