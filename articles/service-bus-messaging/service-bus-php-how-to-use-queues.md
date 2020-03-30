---
title: Jak používat fronty Azure Service Bus s PHP
description: V tomto kurzu se dozvíte, jak vytvořit aplikace PHP pro odesílání zpráv a přijímání zpráv z fronty service bus.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: fcb735d81cac587c75a133ad582f2a839551dcfa
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760687"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-php"></a>Úvodní příručka: Jak používat fronty service bus s PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

V tomto kurzu se dozvíte, jak vytvořit aplikace PHP pro odesílání zpráv a přijímání zpráv z fronty service bus. 

## <a name="prerequisites"></a>Požadavky
1. Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat [výhody předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Pokud nemáte frontu pro práci s, postupujte podle kroků na [portálu Azure k vytvoření](service-bus-quickstart-portal.md) článku fronty Service Bus k vytvoření fronty.
    1. Přečtěte si stručný **přehled** **front služby**Service Bus . 
    2. Vytvořte **obor názvů**service bus . 
    3. Získejte **připojovací řetězec**. 

        > [!NOTE]
        > Frontu v **queue** oboru názvů service bus vytvoříte pomocí PHP v tomto kurzu. 
3. [Azure SDK pro PHP](https://github.com/Azure/azure-sdk-for-php)

## <a name="create-a-php-application"></a>Vytvoření aplikace PHP
Jediným požadavkem pro vytvoření aplikace PHP, která přistupuje ke službě Azure Blob, je odkazování na třídy v [Azure SDK pro PHP](https://github.com/Azure/azure-sdk-for-php) z vašeho kódu. K vytvoření aplikace nebo poznámkového bloku můžete použít libovolné vývojové nástroje.

> [!NOTE]
> Instalace PHP musí mít také nainstalované a povolené [rozšíření OpenSSL.](https://php.net/openssl)

V této příručce budete používat funkce služby, které lze volat z aplikace PHP místně nebo v kódu spuštěném v rámci webové role Azure, role pracovního procesu nebo webu.

## <a name="get-the-azure-client-libraries"></a>Získání klientských knihoven Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití služby Service Bus
Chcete-li použít api fronty služby Service Bus, postupujte takto:

1. Odkaz na soubor automatického zavaděče pomocí [příkazu require_once.][require_once]
2. Odkaz na všechny třídy, které můžete použít.

Následující příklad ukazuje, jak zahrnout soubor automatického `ServicesBuilder` zavaděče a odkazovat na třídu.

> [!NOTE]
> Tento příklad (a další příklady v tomto článku) předpokládá, že jste nainstalovali klientské knihovny PHP pro Azure prostřednictvím Composer. Pokud jste knihovny nainstalovali ručně nebo jako balíček PEAR, musíte odkazovat na soubor automatického zavaděče **WindowsAzure.php.**
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

V níže uvedených `require_once` příkladech bude příkaz vždy zobrazen, ale budou odkazovány pouze třídy nezbytné pro provedení příkladu.

## <a name="set-up-a-service-bus-connection"></a>Nastavení připojení služby Service Bus
Chcete-li vytvořit instanci klienta služby Service Bus, musíte mít nejprve platný připojovací řetězec v tomto formátu:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Kde `Endpoint` je obvykle formátu `[yourNamespace].servicebus.windows.net`.

Chcete-li vytvořit libovolného klienta `ServicesBuilder` služby Azure, musíte použít třídu. Můžete:

* Předajte mu připojovací řetězec přímo.
* Pomocí **cloudconfigurationmanageru (CCM)** zkontrolujte více externích zdrojů pro připojovací řetězec:
  * Ve výchozím nastavení je dodáván s podporou jednoho externího zdroje - environmentálních proměnných
  * Nové zdroje můžete přidat rozšířením třídy `ConnectionStringSource`

Ve zde uvedených příkladech se připojovací řetězec předává přímo.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Vytvoření fronty
Můžete provádět operace správy pro fronty `ServiceBusRestProxy` service bus prostřednictvím třídy. Objekt `ServiceBusRestProxy` je vytvořen pomocí `ServicesBuilder::createServiceBusService` metody factory s příslušným připojovacím řetězcem, který zapouzdřuje oprávnění tokenu ke správě.

Následující příklad ukazuje, jak vytvořit `ServiceBusRestProxy` instanci a `ServiceBusRestProxy->createQueue` volání `myqueue` k `MySBNamespace` vytvoření fronty pojmenované v oboru názvů služby:

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
> Metodu `listQueues` u `ServiceBusRestProxy` objektů můžete použít ke kontrole, zda fronta se zadaným názvem již v oboru názvů existuje.
> 
> 

## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty
Chcete-li odeslat zprávu do fronty služby Service Bus, aplikace volá metodu. `ServiceBusRestProxy->sendQueueMessage` Následující kód ukazuje, jak odeslat `myqueue` zprávu do `MySBNamespace` fronty dříve vytvořené v rámci oboru názvů služby.

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

Zprávy odeslané do (a přijaté z) fronty service bus jsou instance [BrokeredMessage][BrokeredMessage] třídy. [BrokeredMessage][BrokeredMessage] objekty mají sadu standardních metod a vlastností, které se používají k uložení vlastní vlastnosti specifické pro aplikaci a tělo libovolných dat aplikace.

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených ve frontě není omezený, ale celková velikost zpráv držených ve frontě omezená je. Tento horní limit velikosti fronty je 5 GB.

## <a name="receive-messages-from-a-queue"></a>Příjem zpráv z fronty

Nejlepší způsob, jak přijímat zprávy z `ServiceBusRestProxy->receiveQueueMessage` fronty, je použít metodu. Zprávy mohou být přijímány ve dvou různých režimech: [*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) a [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). Výchozí hodnota je **PeekLock**.

Při použití [receiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) režimu receive, receive je operace s jedním výstřelem; to znamená, že když service bus obdrží požadavek na čtení zprávy ve frontě, označí zprávu jako spotřebované a vrátí ji do aplikace. Režim [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) je nejjednodušší model a funguje nejlépe ve scénářích, kde aplikace může tolerovat možnost, že v případě selhání se zpráva nezpracuje. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že service bus bude mít označeny zprávy jako spotřebované, pak při restartování aplikace a začne spotřebovávat zprávy znovu, bude mít zmeškané zprávy, která byla spotřebována před selhání.

Ve výchozím režimu [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) se příjem zprávy stane dvoufázovou operací, která umožňuje podporovat aplikace, které nemohou tolerovat chybějící zprávy. Když service bus obdrží požadavek, najde další zprávu, která má být spotřebována, uzamkne ji zabránit ostatním spotřebitelům v přijetí a vrátí ji do aplikace. Po dokončení aplikace zpracování zprávy (nebo ukládá spolehlivě pro budoucí zpracování), dokončí druhou fázi procesu `ServiceBusRestProxy->deleteMessage`příjmu předáním přijaté zprávy . Když service bus `deleteMessage` zobrazí volání, označí zprávu jako spotřebované a odebere ji z fronty.

Následující příklad ukazuje, jak přijímat a zpracovávat zprávu pomocí režimu [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) (výchozí režim).

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

Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce není schopen zpracovat zprávu z nějakého `unlockMessage` důvodu, pak může volat `deleteMessage` metodu na přijaté zprávy (namísto metody). To způsobí, že service bus odemknout zprávu ve frontě a zpřístupnit pro přijetí znovu, a to buď stejnou náročnou aplikací nebo jinou náročnou aplikací.

K dispozici je také časový limit přidružený ke zprávě zamčené ve frontě a pokud aplikace nezpracuje zprávu před vypršením časového limitu uzamčení (například pokud dojde k chybě aplikace), pak Service Bus zprávu automaticky odemkne a provede ji k dispozici k tomu, aby byly znovu přijaty.

V případě, že aplikace dojde k chybě `deleteMessage` po zpracování zprávy, ale před odesláním požadavku, bude zpráva znovu doručena do aplikace při restartování. To se často nazývá *alespoň jednou* zpracování; to znamená, že každá zpráva je zpracována alespoň jednou, ale v určitých situacích může být stejná zpráva znovu doručena. Pokud scénář nemůže tolerovat duplicitní zpracování, doporučuje se přidat další logiku do aplikací pro zpracování duplikátního doručení zprávy. Toho je často `getMessageId` dosaženo pomocí metody zprávy, která zůstává konstantní napříč pokusy o doručení.

> [!NOTE]
> Prostředky služby Service Bus můžete spravovat pomocí [aplikace Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Průzkumník služby Service Bus umožňuje uživatelům připojit se k oboru názvů service bus a snadno spravovat entity zasílání zpráv. Nástroj poskytuje pokročilé funkce, jako je funkce importu a exportu nebo možnost testovat téma, fronty, předplatná, přenosové služby, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy front service bus, najdete [v tématu Fronty, témata a odběry][Queues, topics, and subscriptions] další informace.

Pro více informací navštivte také [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


