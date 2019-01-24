---
title: Jak používat fronty služby Service Bus pomocí PHP | Dokumentace Microsoftu
description: Naučte se používat fronty Service Bus v Azure. Ukázky kódu napsané v jazyce PHP.
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e29c829b-44c5-4350-8f2e-39e0c380a9f2
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 09/10/2018
ms.author: aschhab
ms.openlocfilehash: c320e06881c73feb228b9d5f49243d7e1d321f52
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847557"
---
# <a name="how-to-use-service-bus-queues-with-php"></a>Jak používat fronty služby Service Bus pomocí PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Tato příručka ukazuje, jak používat fronty Service Bus. Ukázky jsou napsané v PHP a použití [sady Azure SDK pro jazyk PHP](../php-download-sdk.md). Mezi popsané scénáře patří **vytváření front**, **odesílání a přijímání zpráv**, a **odstranění front**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-php-application"></a>Vytvoření aplikace PHP
Jediným požadavkem pro vytvoření aplikace v jazyce PHP, který přistupuje k službě objektů Blob v Azure je odkazující na třídy v [sady Azure SDK pro jazyk PHP](../php-download-sdk.md) z vašeho kódu. Nástroje pro vývoj můžete použít k vytvoření aplikace nebo Poznámkový blok.

> [!NOTE]
> Instalace PHP je také nutné [OpenSSL rozšíření](http://php.net/openssl) nainstalovaný a povolený.
> 
> 

V této příručce se bude používat funkce služby, které může být volána z v rámci aplikace v jazyce PHP místně nebo v kódu v rámci webové role Azure, role pracovního procesu nebo webu.

## <a name="get-the-azure-client-libraries"></a>Získání knihoven Azure klienta
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití služby Service Bus
Pokud chcete používat fronty služby Service Bus rozhraní API, postupujte takto:

1. Odkazovat na soubor pomocí automatického zavaděče [require_once] [ require_once] příkazu.
2. Odkazovat na všechny třídy, které můžete použít.

Následující příklad ukazuje, jak zahrnout automatického zavaděče souboru a odkaz `ServicesBuilder` třídy.

> [!NOTE]
> V tomto příkladu (a další příklady v tomto článku) se předpokládá, že jste si nainstalovali klientské knihovny PHP pro Azure prostřednictvím autora. Pokud jste nainstalovali na knihovny ručně nebo jako balíček hrušky, musí odkazovat **WindowsAzure.php** souboru automatického zavaděče.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

V příkladech níže `require_once` příkazu se vždy nezobrazí, ale jsou odkazovány pouze třídy nezbytné ke spuštění příkladu.

## <a name="set-up-a-service-bus-connection"></a>Nastavit připojení služby Service Bus
Chcete-li vytvořit instanci služby Service Bus klienta, musíte nejprve mít platný připojovací řetězec v následujícím formátu:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Kde `Endpoint` je obvykle ve formátu `[yourNamespace].servicebus.windows.net`.

K vytvoření libovolného klienta služby Azure, je nutné použít `ServicesBuilder` třídy. Můžete:

* Předejte řetězec připojení přímo k němu.
* Použití **CloudConfigurationManager (CCM)** ke kontrole více externích zdrojů pro připojovací řetězec:
  * Ve výchozím nastavení obsahuje podporu pro jeden externí zdroj – proměnné prostředí
  * Můžete přidat nové zdroje tím, že rozšíří `ConnectionStringSource` třídy

Ve zde uvedených příkladech se připojovací řetězec předává přímo.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Vytvoření fronty
Můžete provádět operace správy front služby Service Bus přes `ServiceBusRestProxy` třídy. A `ServiceBusRestProxy` objekt je vytvořen prostřednictvím `ServicesBuilder::createServiceBusService` výrobní metoda s odpovídající připojovací řetězec, který zapouzdřuje token oprávněními k její správě.

Následující příklad ukazuje, jak vytvořit instanci `ServiceBusRestProxy` a volat `ServiceBusRestProxy->createQueue` můžete vytvořit frontu s názvem `myqueue` v rámci `MySBNamespace` obor názvů služby:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
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
> Můžete použít `listQueues` metodu na `ServiceBusRestProxy` objekty ke kontrole, jestli fronta se zadaným názvem již existuje v oboru názvů.
> 
> 

## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty
Odeslat zprávu do fronty služby Service Bus, vaše aplikace volání `ServiceBusRestProxy->sendQueueMessage` metody. Následující kód ukazuje, jak odeslat zprávu `myqueue` fronty dříve vytvořených v rámci `MySBNamespace` obor názvů služby.

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
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
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

Zprávy odeslané do (a přijatých z) služby Service Bus jsou instance front [BrokeredMessage] [ BrokeredMessage] třídy. [BrokeredMessage] [ BrokeredMessage] objekty mají sadu standardních metod a vlastností, které se používají k uložení vlastních vlastností aplikace a tělo libovolnými aplikačními daty.

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených ve frontě není omezený, ale celková velikost zpráv držených ve frontě omezená je. Tento horní limit velikosti fronty je 5 GB.

## <a name="receive-messages-from-a-queue"></a>Příjem zpráv z fronty

Nejlepší způsob, jak přijímá zprávy z fronty je použití `ServiceBusRestProxy->receiveQueueMessage` metoda. Může být přijaty zprávy ve dvou různých režimech: [*ReceiveAndDelete* ](/dotnet/api/microsoft.servicebus.messaging.receivemode) a [ *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). Výchozí hodnota je **PeekLock**.

Při použití [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) režimu přijímat je jednorázová operace; to znamená, když Service Bus přijme požadavek čtení zprávy ve frontě, označí zprávu jako spotřebovávanou a vrátí ji do aplikace. Režim [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) je nejjednodušší model a funguje nejlépe ve scénářích, kde aplikace může tolerovat možnost, že v případě selhání se zpráva nezpracuje. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že Service Bus se už ale zprávu označila jako spotřebovávanou, pak když aplikace znovu spustí a začne znovu přijímat zprávy, ji budou pravděpodobně nenalezlo zprávu, která se spotřebovala před pádem vynechá.

Ve výchozím [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) režim přijetí zprávy stane dvoufázového operaci, která umožňuje podporuje aplikace, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, který se má používat, uzamkne ji ostatní uživatelé z dešifrujete proti a vrátí ji do aplikace. Poté, co aplikace dokončí zpracování zprávy (nebo spolehlivě uloží pro pozdější zpracování), dokončení druhé fáze přijetí předáním přijatou zprávu do `ServiceBusRestProxy->deleteMessage`. Když Service Bus uvidí `deleteMessage` volání, bude označí zprávu jako spotřebovávanou a odebere ji z fronty.

Následující příklad ukazuje, jak pro příjem a zpracování zpráv pomocí [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) režimu (výchozím režimu).

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv

Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud přijímající aplikace nedokáže zpracovat zprávu z nějakého důvodu, pak může volat `unlockMessage` metoda na přijatou zprávu (místo `deleteMessage` metoda). To způsobí, že služba Service Bus zprávu odemkne ve frontě a zpřístupní ji pro další přijetí, stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

K dispozici je také vypršení časového limitu přidružené zpráva uzamčená ve frontě, a pokud aplikace zprávu nezpracuje zámku vyprší časový limit (například pokud aplikace spadne), pak se Service Bus zprávu automaticky odemkne a nastavte ji k dispozici pro další přijetí.

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než `deleteMessage` požadavku a potom bude doručit víckrát do aplikace při restartování. To se často nazývá *nejméně jednou* zpracování; to znamená, že každá zpráva se zpracuje alespoň jednou ale v některých situacích může doručit víckrát. Pokud scénář nemůže tolerovat zpracování, je doporučeno následným přidáním další logiku pro aplikace pro zpracování víckrát doručené zprávy. To se často opírá `getMessageId` metoda zprávy, která zůstává konstantní pokusu o doručení.

## <a name="next-steps"></a>Další postup
Teď, když jste se seznámili se základy front Service Bus, najdete v článku [fronty, témata a odběry] [ Queues, topics, and subscriptions] Další informace.

Další informace také najdete [středisko pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once


