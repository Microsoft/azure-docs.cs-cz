---
title: 'Rychlý Start: použití Service Bus front s PHP'
description: 'Rychlý Start: Naučte se používat fronty Service Bus v Azure. Ukázky kódu napsané v PHP.'
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
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: d576c269f4178c7543327c6b75f46f5487d7a205
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719183"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-php"></a>Rychlý Start: použití Service Bus front s PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

V tomto kurzu se naučíte vytvářet aplikace PHP pro posílání zpráv a příjem zpráv z Service Bus fronty. 

## <a name="prerequisites"></a>Požadavky
1. Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat výhody pro [předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Pokud nemáte frontu, ve které byste mohli pracovat, postupujte podle kroků v tématu [použití Azure Portal k vytvoření fronty Service Bus](service-bus-quickstart-portal.md) .
    1. Přečtěte si rychlý **přehled** Service Busch **front**. 
    2. Vytvořte **obor názvů**Service Bus. 
    3. Získá **připojovací řetězec**. 

        > [!NOTE]
        > V tomto kurzu vytvoříte **frontu** v oboru názvů Service Bus pomocí PHP. 
3. [Azure SDK pro PHP](https://github.com/Azure/azure-sdk-for-php)

## <a name="create-a-php-application"></a>Vytvoření aplikace PHP
Jediným požadavkem pro vytvoření aplikace PHP, která přistupuje k Azure Blob service, je odkazování tříd v [sadě Azure SDK pro php](https://github.com/Azure/azure-sdk-for-php) z vašeho kódu. Pomocí libovolného vývojového nástroje můžete vytvořit aplikaci nebo Poznámkový blok.

> [!NOTE]
> Vaše instalace PHP musí také mít nainstalované a povolené [rozšíření OpenSSL](https://php.net/openssl) .

V této příručce budete používat funkce služby, které lze volat v rámci aplikace PHP místně nebo v kódu spuštěném v rámci webové role Azure, role pracovního procesu nebo webu.

## <a name="get-the-azure-client-libraries"></a>Získání klientských knihoven Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití Service Bus
Pokud chcete použít rozhraní API fronty Service Bus, udělejte toto:

1. Odkazování na soubor automatického zavaděče pomocí příkazu [require_once][require_once]
2. Odkázat na libovolné třídy, které můžete použít.

Následující příklad ukazuje, jak zahrnout soubor automatického zavaděče a odkazovat na třídu `ServicesBuilder`.

> [!NOTE]
> V tomto příkladu (a dalších příkladech v tomto článku) se předpokládá, že máte nainstalované klientské knihovny PHP pro Azure přes skladatele. Pokud jste knihovny nainstalovali ručně nebo jako balíček pro HRUŠKy, musíte se odkázat na soubor automatického zavaděče **windowsazure. php** .
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

V níže uvedených příkladech bude příkaz `require_once` vždy zobrazen, ale odkazuje se na třídy, které jsou nezbytné pro spuštění příkladu.

## <a name="set-up-a-service-bus-connection"></a>Nastavení Service Busho připojení
Chcete-li vytvořit instanci klienta Service Bus, musíte nejprve mít platný připojovací řetězec v tomto formátu:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Kde `Endpoint` obvykle je `[yourNamespace].servicebus.windows.net`formátu.

Chcete-li vytvořit libovolného klienta služby Azure, je nutné použít třídu `ServicesBuilder`. Můžete:

* Předání připojovacího řetězce přímo do něj.
* Použijte **CloudConfigurationManager (ccm)** pro kontrolu několika externích zdrojů pro připojovací řetězec:
  * Ve výchozím nastavení se dodává s podporou pro jednu externí proměnnou zdrojového prostředí.
  * Nové zdroje můžete přidat rozšířením třídy `ConnectionStringSource`

Ve zde uvedených příkladech se připojovací řetězec předává přímo.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Vytvoření fronty
Pomocí `ServiceBusRestProxy` třídy můžete provádět operace správy pro fronty Service Bus. Objekt `ServiceBusRestProxy` je vytvořen pomocí metody `ServicesBuilder::createServiceBusService` Factory s příslušným připojovacím řetězcem, který zapouzdřuje oprávnění tokenu pro jeho správu.

Následující příklad ukazuje vytvoření instance `ServiceBusRestProxy` a volání `ServiceBusRestProxy->createQueue` k vytvoření fronty s názvem `myqueue` v rámci oboru názvů služby `MySBNamespace`:

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
> Metodu `listQueues` pro objekty `ServiceBusRestProxy` lze použít k ověření, zda fronta se zadaným názvem již v rámci oboru názvů existuje.
> 
> 

## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty
Chcete-li odeslat zprávu do fronty Service Bus, aplikace zavolá metodu `ServiceBusRestProxy->sendQueueMessage`. Následující kód ukazuje, jak odeslat zprávu do fronty `myqueue` dříve vytvořenou v rámci oboru názvů služby `MySBNamespace`.

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

Zprávy odeslané do (a přijaté z) Service Bus fronty jsou instance třídy [BrokeredMessage][BrokeredMessage] . Objekty [BrokeredMessage][BrokeredMessage] mají sadu standardních metod a vlastností, které se používají k uložení vlastních vlastností specifických pro aplikace a obsahu libovolných dat aplikace.

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených ve frontě není omezený, ale celková velikost zpráv držených ve frontě omezená je. Tento horní limit velikosti fronty je 5 GB.

## <a name="receive-messages-from-a-queue"></a>Přijímání zpráv z fronty

Nejlepším způsobem, jak přijímat zprávy z fronty, je použít metodu `ServiceBusRestProxy->receiveQueueMessage`. Zprávy mohou být přijímány ve dvou různých režimech: [*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) a [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). Výchozí hodnota je **PeekLock**.

Při použití režimu [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) je operace Receive jedinou operací; To znamená, že když Service Bus obdrží požadavek na čtení zprávy ve frontě, označí zprávu jako spotřebou a vrátí ji do aplikace. Režim [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) je nejjednodušší model a funguje nejlépe ve scénářích, kde aplikace může tolerovat možnost, že v případě selhání se zpráva nezpracuje. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že Service Bus bude označena jako spotřebovaná zpráva, pak když se aplikace znovu spustí a začne znovu spotřebovávat zprávy, vynechá se zpráva, která byla spotřebována před chybou.

Ve výchozím režimu [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) se příjem zprávy stane dvě operace fáze, která umožňuje podporovat aplikace, které nemůžou tolerovat chybějící zprávy. Když Service Bus obdrží požadavek, najde další zprávu, která se má spotřebovat, zamkne ji, aby zabránila ostatním uživatelům v jejich přijetí, a pak ji vrátí do aplikace. Poté, co aplikace dokončí zpracování zprávy (nebo je uloží spolehlivě pro budoucí zpracování), dokončí druhou fázi procesu přijímání tím, že se přijatá zpráva pošle `ServiceBusRestProxy->deleteMessage`. Když Service Bus uvidí volání `deleteMessage`, bude zprávu označovat jako spotřebou a odebrat ji z fronty.

Následující příklad ukazuje, jak přijmout a zpracovat zprávu pomocí režimu [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) (výchozí režim).

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

Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce z nějakého důvodu nemůže zprávu zpracovat, může volat metodu `unlockMessage` v přijaté zprávě (namísto metody `deleteMessage`). To způsobí, že Service Bus odemkne zprávu v rámci fronty a zpřístupní ji pro opětovné přijetí, a to buď pomocí stejné aplikace, nebo jiné náročné aplikace.

Je také časový limit přidružený ke zprávě uzamčený ve frontě a pokud aplikace nedokáže zpracovat zprávu před vypršením časového limitu zámku (například pokud dojde k selhání aplikace), Service Bus bude automaticky odemknout a nastavit k dispozici pro opětovné přijetí.

V případě, že dojde k chybě aplikace po zpracování zprávy, ale před vydáním žádosti o `deleteMessage`, bude zpráva doručena do aplikace při restartu. Tato metoda se často nazývá *alespoň po* zpracování; To znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může být stejná zpráva doručena znovu. Pokud scénář nemůže tolerovat duplicitní zpracování, doporučuje se přidat další logiku do aplikací pro zpracování duplicitního doručování zpráv. To se často dosahuje pomocí metody `getMessageId` zprávy, která zůstává při pokusůch o doručení konstantní.

> [!NOTE]
> Prostředky Service Bus můžete spravovat pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer umožňuje uživatelům připojit se k oboru názvů Service Bus a snadno spravovat entity zasílání zpráv. Tento nástroj poskytuje pokročilé funkce, jako jsou funkce importu a exportu, nebo možnost testovat témata, fronty, odběry, služby Relay, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy Service Busch front, najdete další informace v tématu [fronty, témata a předplatná][Queues, topics, and subscriptions] .

Další informace najdete také v centru pro [vývojáře PHP](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


