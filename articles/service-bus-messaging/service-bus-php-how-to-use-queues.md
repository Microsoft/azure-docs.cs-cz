---
title: Jak používat fronty Service Bus s PHP | Microsoft Docs
description: Naučte se používat fronty Service Bus v Azure. Ukázky kódu jsou vytvořeny v jazyce PHP.
services: service-bus-messaging
documentationcenter: php
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: e29c829b-44c5-4350-8f2e-39e0c380a9f2
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 3514812f7f087582035dad5d9a4d620652aa4da9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23868323"
---
# <a name="how-to-use-service-bus-queues-with-php"></a>Jak používat fronty Service Bus s PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Tento průvodce vám ukáže, jak používat fronty Service Bus. Ukázky jsou napsané v PHP a použití [Azure SDK pro jazyk PHP](../php-download-sdk.md). Pokryté scénáře zahrnují **vytváření front**, **odesílání a přijímání zpráv**, a **odstraňování front**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-php-application"></a>Vytvoření aplikace PHP
Jediný požadavek pro vytvoření aplikace PHP, který přistupuje k službě Azure Blob je odkazující na třídy v [Azure SDK pro jazyk PHP](../php-download-sdk.md) z vašeho kódu. Všechny nástroje pro vývoj vám pomůže vytvořit aplikaci nebo program Poznámkový blok.

> [!NOTE]
> Instalace PHP musí mít také [OpenSSL rozšíření](http://php.net/openssl) nainstalované a povolené.
> 
> 

V této příručce bude používat funkce služby, které lze volat z v rámci aplikace PHP místně nebo v kódu běžící v rámci webové role Azure, role pracovního procesu nebo webu.

## <a name="get-the-azure-client-libraries"></a>Získání klienta Azure knihovny
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití služby Service Bus
Pokud chcete používat fronty Service Bus rozhraní API, postupujte takto:

1. Reference souboru pomocí automatického zavaděče [require_once] [ require_once] příkaz.
2. Referenční všechny třídy, které můžete použít.

Následující příklad ukazuje, jak se zahrnuje automatického zavaděče souboru a odkaz `ServicesBuilder` třídy.

> [!NOTE]
> Tento příklad (a další příklady v tomto článku) předpokládá, že jste nainstalovali PHP klientské knihovny pro Azure prostřednictvím autora. Pokud jste nainstalovali v knihovnách ručně nebo jako balíček HRUŠKAMI, musí odkazovat **WindowsAzure.php** automatického zavaděče souboru.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

V následujících příkladech `require_once` příkaz vždy se zobrazí, ale jenom ty třídy potřebné pro tento příklad provést odkazují.

## <a name="set-up-a-service-bus-connection"></a>Nastavení připojení služby Service Bus
K vytvoření instance služby Service Bus klient, že máte platný připojovací řetězec v tomto formátu:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Kde `Endpoint` je obvykle ve formátu `[yourNamespace].servicebus.windows.net`.

Vytvoření libovolného klienta služby Azure, je nutné použít `ServicesBuilder` třídy. Můžete:

* Připojovací řetězec přímo jí předejte.
* Použití **CloudConfigurationManager (CCM)** zkontrolujte několik externích zdrojů pro připojovací řetězec:
  * Ve výchozím nastavení je teď obsahuje podporu pro jeden externí zdroj – proměnné prostředí
  * Můžete přidat nové zdroje tím, že rozšíří `ConnectionStringSource` – třída

Příklady podle zde uvedeného je předaná přímo připojovací řetězec.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Vytvoření fronty
Můžete provádět operace správy front služby Service Bus přes `ServiceBusRestProxy` třídy. A `ServiceBusRestProxy` objektu je vytvořený pomocí `ServicesBuilder::createServiceBusService` metoda factory řetězcem odpovídající připojení, který zapouzdřuje tokenu oprávněními k její správě.

Následující příklad ukazuje, jak vytvořit instanci `ServiceBusRestProxy` a volání `ServiceBusRestProxy->createQueue` vytvořit frontu s názvem `myqueue` v rámci `MySBNamespace` oboru názvů služby:

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
> Můžete použít `listQueues` metodu `ServiceBusRestProxy` objekty, které chcete zkontrolovat, zda fronta se zadaným názvem již existuje v rámci oboru názvů.
> 
> 

## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty
K odeslání zprávy do fronty Service Bus, vaše aplikace volání `ServiceBusRestProxy->sendQueueMessage` metoda. Následující kód ukazuje, jak odeslat zprávu `myqueue` fronty vytvořili v rámci `MySBNamespace` oboru názvů služby.

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

Zprávy odeslané do (a přijaté z) sběrnice fronty jsou instance [BrokeredMessage] [ BrokeredMessage] třídy. [BrokeredMessage] [ BrokeredMessage] objekty mají sadu standardních metod a vlastností, které jsou používané pro udržení vlastních vlastností aplikace a tělo s libovolnými aplikačními daty.

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených ve frontě není omezený, ale celková velikost zpráv držených ve frontě omezená je. Toto omezení velikost fronty je 5 GB.

## <a name="receive-messages-from-a-queue"></a>Příjem zpráv z fronty

Nejlepší způsob, jak přijmout zprávy z fronty je použití `ServiceBusRestProxy->receiveQueueMessage` metoda. Můžete obdržet zprávy ve dvou různých režimech: [ *ReceiveAndDelete* ](/dotnet/api/microsoft.servicebus.messaging.receivemode.receiveanddelete) a [ *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode.peeklock). Výchozí hodnota je **PeekLock**.

Při použití [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode.receiveanddelete) režimu přijímat je jednorázová operace; to znamená, když Service Bus přijme požadavek čtení zprávy ve frontě, označí zprávu jako spotřebovávanou a vrátí ji do aplikace. Režim [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode.receiveanddelete) je nejjednodušší model a funguje nejlépe ve scénářích, kde aplikace může tolerovat možnost, že v případě selhání se zpráva nezpracuje. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že Service Bus se už ale zprávu označila jako spotřebovávanou, pak když se aplikace restartuje a začne znovu přijímat zprávy, ji budou neuskutečnily zprávu, která se spotřebovala před havárii.

Ve výchozím [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode.peeklock) režimu, přijímání zprávy se změní na dvě fáze operaci, která umožňuje podporuje aplikace, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, který se má používat, uzamkne ji proti spotřebování jinými odběrateli příjem ho a vrátí ji do aplikace. Když aplikace dokončí zpracování zprávy (nebo ji bezpečně uloží pro pozdější zpracování), tím potvrdí dokončení druhé fáze přijetí předávání přijaté zprávy do `ServiceBusRestProxy->deleteMessage`. Když Service Bus uvidí `deleteMessage` volání, která se bude označí zprávu jako spotřebovávanou a odebrat ji z fronty.

Následující příklad ukazuje, jak přijímat a zpracovávat zprávu pomocí [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode.peeklock) režimu (výchozím režimu).

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

Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud přijímající aplikace nedokáže zpracovat zprávu z nějakého důvodu, pak může zavolat `unlockMessage` metoda na přijatou zprávu (místo `deleteMessage` metoda). To způsobí, že Service Bus zprávu odemkne ve frontě a zpřístupní ji pro další přijetí, stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

Je také vypršení časového limitu přidružené zpráva uzamčená ve frontě, a pokud se nepodaří aplikace zprávu nezpracuje zámku vyprší časový limit (například pokud aplikace spadne), pak se Service Bus zprávu automaticky odemkne a zpřístupní ji pro další přijetí.

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než `deleteMessage` požadavku a potom zpráva bude vysláním do aplikace odešle znovu. To se často označuje jako *nejméně jednou* zpracování; to znamená, že každá zpráva se zpracuje alespoň jednou ale v některých situacích může být stejná zpráva víckrát. Pokud scénář nemůže tolerovat zpracování duplicitní, se doporučuje následným přidáním další logiku pro aplikace pro zpracování víckrát doručené zprávy. To se často opírá `getMessageId` metoda zprávy, která zůstává konstantní mezi pokusy o doručení.

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy front Service Bus, najdete v části [fronty, témata a odběry] [ Queues, topics, and subscriptions] Další informace.

Další informace najdete také naleznete [středisku pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once


