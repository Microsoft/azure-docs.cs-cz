---
title: Jak používat témata služby Service Bus pomocí PHP | Dokumentace Microsoftu
description: Zjistěte, jak používat témata služby Service Bus pomocí PHP v Azure.
services: service-bus-messaging
documentationcenter: php
author: spelluru
manager: timlt
editor: ''
ms.assetid: faaa4bbd-f6ef-42ff-aca7-fc4353976449
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/06/2017
ms.author: spelluru
ms.openlocfilehash: 9901b485b97ecde2de889796fc682db3ee30c544
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43701391"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Jak používat témata a odběry Service Bus pomocí PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

V tomto článku se dozvíte, jak používat témata a odběry Service Bus. Ukázky jsou napsané v PHP a použití [sady Azure SDK pro jazyk PHP](../php-download-sdk.md). Mezi popsané scénáře patří **vytváření témat a odběrů**, **vytváření filtrů odběrů**, **odesílání zpráv do tématu**, **přijetí zprávy z odběru**, a **odstranění témat a odběrů**.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-php-application"></a>Vytvoření aplikace PHP
Jediným požadavkem pro vytvoření aplikace v jazyce PHP, který přistupuje k službě objektů Blob v Azure má odkazovat na třídy v [sady Azure SDK pro jazyk PHP](../php-download-sdk.md) z vašeho kódu. Nástroje pro vývoj můžete použít k vytvoření aplikace nebo Poznámkový blok.

> [!NOTE]
> Instalace PHP je také nutné [OpenSSL rozšíření](http://php.net/openssl) nainstalovaný a povolený.
> 
> 

Tento článek popisuje, jak používat funkce služby, které může být volána v rámci aplikace v jazyce PHP místně nebo v kódu v rámci webové role Azure, role pracovního procesu nebo webu.

## <a name="get-the-azure-client-libraries"></a>Získání knihoven Azure klienta
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití služby Service Bus
Použití rozhraní API služby Service Bus:

1. Odkazovat na soubor pomocí automatického zavaděče [require_once] [ require-once] příkazu.
2. Odkazovat na všechny třídy, které můžete použít.

Následující příklad ukazuje, jak zahrnout automatického zavaděče souboru a odkaz **ServiceBusService** třídy.

> [!NOTE]
> V tomto příkladu (a další příklady v tomto článku) se předpokládá, že jste si nainstalovali klientské knihovny PHP pro Azure prostřednictvím autora. Pokud jste nainstalovali na knihovny ručně nebo jako balíček hrušky, musí odkazovat **WindowsAzure.php** souboru automatického zavaděče.
> 
> 

```php
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

V následujících příkladech `require_once` příkaz je vždy zobrazen, ale jsou odkazovány pouze třídy nezbytné ke spuštění příkladu.

## <a name="set-up-a-service-bus-connection"></a>Nastavit připojení služby Service Bus
Chcete-li vytvořit instanci služby Service Bus klient musíte nejprve mít platný připojovací řetězec v následujícím formátu:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Kde `Endpoint` je obvykle ve formátu `https://[yourNamespace].servicebus.windows.net`.

K vytvoření libovolného klienta služby Azure, je nutné použít `ServicesBuilder` třídy. Můžete:

* Předejte řetězec připojení přímo k němu.
* Použití **CloudConfigurationManager (CCM)** ke kontrole více externích zdrojů pro připojovací řetězec:
  * Ve výchozím nastavení obsahuje podporu pro jeden externí zdroj – proměnné prostředí.
  * Nové zdroje můžete přidat rozšířením třídy `ConnectionStringSource`.

Ve zde uvedených příkladech se připojovací řetězec předává přímo.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Vytvoření tématu
Můžete provádět operace správy témat sběrnice Service Bus přes `ServiceBusRestProxy` třídy. A `ServiceBusRestProxy` objekt je vytvořen prostřednictvím `ServicesBuilder::createServiceBusService` výrobní metoda s odpovídající připojovací řetězec, který zapouzdřuje token oprávněními k její správě.

Následující příklad ukazuje, jak vytvořit instanci `ServiceBusRestProxy` a volat `ServiceBusRestProxy->createTopic` chcete vytvořit téma s názvem `mytopic` v rámci `MySBNamespace` obor názvů:

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
> Můžete použít `listTopics` metodu na `ServiceBusRestProxy` objekty ke kontrole, jestli téma se zadaným názvem již existuje v rámci oboru názvů služby.
> 
> 

## <a name="create-a-subscription"></a>Vytvoření odběru
Odběry témat taky jsou vytvořeny pomocí `ServiceBusRestProxy->createSubscription` metody. Odběry mají názvy a můžou mít volitelné filtry, které omezují výběr zpráv odesílaných do virtuální fronty odběru.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Vytvoření odběru s výchozím filtrem (MatchAll).
Filtr **MatchAll** je výchozí filtr, který se použije v případě, že při vytváření nového odběru nezadáte žádný filtr. Když **MatchAll** se používá filtr, všechny zprávy publikované do tématu se umístí do virtuální fronty odběru. Následující příklad vytvoří odběr s názvem "mysubscription" a používá výchozí **MatchAll** filtru.

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
Můžete taky vytvořit filtry, které vám umožní zprávy odeslané do tématu zobrazit v konkrétním odběru tématu. Nejflexibilnější filtr předplatných je [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter), která implementuje podmnožinu SQL92. Filtry SQL pracují s vlastnostmi zpráv publikované do tématu. Další informace o SqlFilters najdete v tématu [SqlFilter.SqlExpression vlastnost][sqlfilter].

> [!NOTE]
> Každé pravidlo v rámci předplatného zpracovává příchozí zprávy nezávisle na sobě, přidání jejich výsledek zprávy do odběru. Kromě toho každé nové předplatné má výchozí **pravidlo** objektu s filtrem, který přidá všechny zprávy z tématu k předplatnému. Pokud chcete přijímat pouze zprávy odpovídající filtr, musíte odebrat výchozí pravidlo. Výchozí pravidla můžete odebrat pomocí `ServiceBusRestProxy->deleteRule` metody.
> 
> 

Následující příklad vytvoří odběr s názvem `HighMessages` s **SqlFilter** , který vybere jen zprávy, které mají vlastní `MessageNumber` vlastnost větší než 3. Zobrazit [odesílání zpráv do tématu](#send-messages-to-a-topic) informace o přidání vlastních vlastností do zprávy.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Upozorňujeme, že tento kód vyžaduje použití další obor názvů: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Obdobně následující příklad vytvoří odběr s názvem `LowMessages` s `SqlFilter` , který vybere jen zprávy, které mají `MessageNumber` vlastnost menší než nebo rovna na 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Teď, když je odeslána zpráva `mytopic` tématu, vždy doručení příjemcům `mysubscription` předplatného a selektivně příjemcům `HighMessages` a `LowMessages` předplatných (v závislosti na obsah zprávy).

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu
K odeslání zprávy do tématu služby Service Bus, vaše aplikace volání `ServiceBusRestProxy->sendTopicMessage` metody. Následující kód ukazuje, jak odeslat zprávu `mytopic` dříve vytvořených v rámci tématu `MySBNamespace` obor názvů služby.

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

Zprávy odeslané do témat Service Bus jsou instance [BrokeredMessage] [ BrokeredMessage] třídy. [BrokeredMessage] [ BrokeredMessage] objekty mají sadu standardních vlastností a metod, jakož i vlastnosti, které můžete použít pro udržení vlastních vlastností specifické pro aplikaci. Následující příklad ukazuje, jak odeslat 5 zkušební zprávy `mytopic` tématu vytvořili dříve. `setProperty` Metoda se používá k přidání vlastních vlastností (`MessageNumber`) pro každou zprávu. Všimněte si, že `MessageNumber` hodnota vlastnosti se liší u každé zprávy (tuto hodnotu můžete použít k určení, které odběry přijmou, jak je znázorněno [vytvoření odběru](#create-a-subscription) části):

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

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených v tématu není omezený, ale celková velikost zpráv držených v tématu omezená je. Tato velikost tématu horní omezení je 5 GB. Další informace o kvótách najdete v tématu [kvótách služby Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Příjem zpráv z odběru
Nejlepší způsob, jak příjem zpráv z odběru je k použití `ServiceBusRestProxy->receiveSubscriptionMessage` metody. Může být přijaty zprávy ve dvou různých režimech: [ *ReceiveAndDelete* a *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). Výchozí hodnota je **PeekLock**.

Při použití režimu [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) je přijetí jednorázová operace – tzn. když Service Bus přijme požadavek na čtení zprávy v odběru, označí zprávu jako spotřebovávanou a vrátí ji do aplikace. [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) * režimu je nejjednodušší model a funguje nejlépe pro scénáře, ve kterých aplikace může tolerovat možnost, zprávy v případě selhání. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že Service Bus se už ale zprávu označila jako spotřebovávanou, pak když aplikace znovu spustí a začne znovu přijímat zprávy, ji budou pravděpodobně nenalezlo zprávu, která se spotřebovala před pádem vynechá.

Ve výchozím [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) režim přijetí zprávy stane dvoufázového operaci, která umožňuje podporuje aplikace, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Poté, co aplikace dokončí zpracování zprávy (nebo spolehlivě uloží pro pozdější zpracování), dokončení druhé fáze přijetí předáním přijatou zprávu do `ServiceBusRestProxy->deleteMessage`. Když Service Bus uvidí `deleteMessage` volání, označí zprávu jako spotřebovávanou a odebere ji z fronty.

Následující příklad ukazuje, jak pro příjem a zpracování zpráv pomocí [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) režimu (výchozím režimu). 

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
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud přijímající aplikace nedokáže zpracovat zprávu z nějakého důvodu, pak může volat `unlockMessage` metoda na přijatou zprávu (místo `deleteMessage` metoda). To způsobí, že Service Bus zprávu odemkne ve frontě a zpřístupní ji pro další přijetí, buďto stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

K dispozici je také vypršení časového limitu přidružené zpráva uzamčená ve frontě, a pokud aplikace zprávu nezpracuje zámku vyprší časový limit (například pokud aplikace spadne), pak služby Service Bus zprávu automaticky odemkne a nastavte ji k dispozici pro další přijetí.

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než `deleteMessage` požadavku a pak je víckrát do aplikace při restartování. To se často nazývá *nejméně jednou* zpracování; to znamená, že každá zpráva se zpracuje alespoň jednou ale v některých situacích může doručit víckrát. Pokud scénář nemůže tolerovat zpracování, pak vývojáři aplikace by měl přidat další logiku pro aplikace pro zpracování víckrát doručené zprávy. To se často opírá `getMessageId` metoda zprávy, která zůstává konstantní pokusu o doručení.

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů
Pokud chcete odstranit, tématu nebo předplatného, použijte `ServiceBusRestProxy->deleteTopic` nebo `ServiceBusRestProxy->deleteSubscripton` metody, v uvedeném pořadí. Všimněte si, že se odstraní téma se odstraní také všechny odběry, které jsou registrovány pro příslušné téma.

Následující příklad ukazuje, jak odstranit téma s názvem `mytopic` a její odběry registrované.

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

S použitím `deleteSubscription` metodu, můžete nezávisle na sobě odstranění předplatného:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## <a name="next-steps"></a>Další postup
Teď, když jste se seznámili se základy front Service Bus, najdete v článku [fronty, témata a odběry] [ Queues, topics, and subscriptions] Další informace.

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter
[require-once]: http://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
