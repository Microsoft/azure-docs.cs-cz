---
title: Použití front Azure Service Bus v souboru Node.js pomocí balíčku azure-sb
description: Zjistěte, jak vytvořit aplikace Node.js pro odesílání zpráv a přijímání zpráv z fronty Azure Service Bus pomocí balíčku azure-sb.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 7ee3939c1a1b450f2458267ab0b70e3924a4869b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330596"
---
# <a name="quickstart-use-service-bus-queues-in-azure-with-nodejs-and-the-azure-sb-package"></a>Úvodní příručka: Použití front service bus u Azure s node.js a balíčkem azure-sb
V tomto kurzu se dozvíte, jak vytvořit aplikace Node.js pro odesílání zpráv a přijímání zpráv z fronty Služby Azure Service Bus pomocí balíčku [azure-sb.](https://www.npmjs.com/package/azure-sb) Ukázky jsou zapsány v Jazyce JavaScript a použít [modul](https://www.npmjs.com/package/azure) Azure Node.js, který interně používá balíček azure-sb.

> [!IMPORTANT]
> Balíček [azure-sb](https://www.npmjs.com/package/azure-sb) používá [run-time API service bus REST](/rest/api/servicebus/service-bus-runtime-rest). Můžete získat rychlejší zkušenosti pomocí [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) nového, který používá rychlejší [Protokol AMQP 1.0](service-bus-amqp-overview.md). 
> 
> Další informace o novém balíčku najdete v [tématu Jak používat fronty @azure/service-bus service bus s Node.js a balíček](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-queues-new-package), jinak pokračovat ve čtení vidět, jak používat balíček [azure.](https://www.npmjs.com/package/azure)

## <a name="prerequisites"></a>Požadavky
- Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat [výhody předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Pokud nemáte frontu pro práci s, postupujte podle kroků na [portálu Azure k vytvoření](service-bus-quickstart-portal.md) článku fronty Service Bus k vytvoření fronty.
    1. Přečtěte si stručný **přehled** **front služby**Service Bus . 
    2. Vytvořte **obor názvů**service bus . 
    3. Získejte **připojovací řetězec**. 

        > [!NOTE]
        > Frontu v **queue** oboru názvů service bus vytvoříte pomocí souboru Node.js v tomto kurzu. 
 

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js
Vytvořte prázdnou aplikaci Node.js. Pokyny k vytvoření aplikace Node.js najdete v tématu [Vytvoření a nasazení aplikace Node.js na web Azure][Create and deploy a Node.js application to an Azure Website]nebo [Node.js Cloud Service][Node.js Cloud Service] pomocí prostředí Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití služby Service Bus
Pokud chcete používat Azure Service Bus, stáhněte a použijte balíček Node.js Azure. Tento balíček obsahuje sadu knihoven, které komunikují se službami SERVICE Bus REST.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>K získání balíčku použijte Správce balíčků uzlů (NPM).
1. Pomocí příkazového okna **Windows PowerShell pro Node.js** přejděte do složky **c:\\node\\sbqueues\\WebRole1,** ve které jste vytvořili ukázkovou aplikaci.
2. Zadejte **npm nainstalovat azure** v příkazovém okně, což by mělo mít za následek výstup podobný následujícímu příkladu:

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```
3. Můžete ručně spustit příkaz **ls** a ověřit, že se vytvořila složka **node_modules**. Uvnitř této složky najděte balíček **azure,** který obsahuje knihovny, které potřebujete pro přístup do front Service Bus.

### <a name="import-the-module"></a>Import modulu
Pomocí poznámkového bloku nebo jiného textového editoru přidejte do horní části souboru **server.js** aplikace následující:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Nastavení připojení azure service bus
Modul Azure přečte `AZURE_SERVICEBUS_CONNECTION_STRING` proměnnou prostředí k získání informací potřebných pro připojení k service bus. Pokud tato proměnná prostředí není nastavena, je `createServiceBusService`nutné zadat informace o účtu při volání .

Příklad nastavení proměnných prostředí na [webu Azure pro][Azure portal] web Azure najdete v tématu [Webová aplikace Node.js s úložištěm][Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Vytvoření fronty
Objekt **ServiceBusService** umožňuje pracovat s frontami služby Service Bus. Následující kód vytvoří objekt **ServiceBusService.** Po příkazu k importu modulu Azure jej přidejte v horní části souboru **server.js:**

```javascript
var serviceBusService = azure.createServiceBusService();
```

Voláním `createQueueIfNotExists` objektu **ServiceBusService** je vrácena zadaná fronta (pokud existuje) nebo je vytvořena nová fronta se zadaným názvem. Následující kód `createQueueIfNotExists` používá k vytvoření fronty `myqueue`nebo připojení k ní s názvem :

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

Metoda `createServiceBusService` také podporuje další možnosti, které umožňují přepsat výchozí nastavení fronty, jako je například čas zprávy na aktuální nebo maximální velikost fronty. Následující příklad nastaví maximální velikost fronty na 5 GB a hodnotu ttl času (TTL) 1 minutu:

```javascript
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Filtry
Volitelné filtrování operace lze použít na operace prováděné pomocí **ServiceBusService**. Filtrování operace mohou zahrnovat protokolování, automatické opakování, atd. Filtry jsou objekty, které implementují metodu s podpisem:

```javascript
function handle (requestOptions, next)
```

Po provedení jeho pre-zpracování na možnosti `next`požadavku, metoda musí volat , předání zpětného volání s následujícím podpisem:

```javascript
function (returnObject, finalCallback, next)
```

V tomto zpětném volání `returnObject` a po zpracování (odpověď z požadavku na server), zpětné volání musí buď `next` vyvolat, `finalCallback`pokud existuje pokračovat ve zpracování jiných filtrů, nebo vyvolat , který ukončí vyvolání služby.

Dva filtry, které implementují logiku opakování jsou součástí sady `ExponentialRetryPolicyFilter` `LinearRetryPolicyFilter`Azure SDK pro Node.js a . Následující kód vytvoří `ServiceBusService` objekt, `ExponentialRetryPolicyFilter`který používá :

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty
Chcete-li odeslat zprávu do fronty služby Service Bus, aplikace volá metodu `sendQueueMessage` na Objekt **ServiceBusService.** Zprávy odeslané (a přijaté z) fronty Service Bus jsou **BrokeredMessage** objekty a mají sadu standardních vlastností (například **Label** a **TimeToLive**), slovník, který se používá k uložení vlastní vlastnosti specifické pro aplikaci a tělo libovolných dat aplikace. Aplikace můžete nastavit text zprávy předáním řetězec jako zpráva. Všechny požadované standardní vlastnosti jsou naplněny výchozími hodnotami.

Následující příklad ukazuje, jak odeslat testovací zprávu `myqueue` `sendQueueMessage`do fronty s názvem pomocí :

```javascript
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv uchovávaných ve frontě není nijak omezen, ale je omezena celková velikost zpráv držených frontou. Velikost fronty se definuje při vytvoření, maximální limit je 5 GB. Další informace o kvótách naleznete v [tématu Kvóty služby Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Příjem zpráv z fronty
Zprávy jsou přijímány z `receiveQueueMessage` fronty pomocí metody na Objekt **ServiceBusService.** Ve výchozím nastavení jsou zprávy odstraněny z fronty při jejich čtení; můžete však číst (náhled) a uzamknout zprávu bez odstranění z `isPeekLock` fronty nastavením volitelného parametru na **hodnotu true**.

Výchozí chování čtení a odstranění zprávy jako součást operace příjmu je nejjednodušší model a funguje nejlépe pro scénáře, ve kterých aplikace může tolerovat není zpracování zprávy při selhání dojde. Chcete-li pochopit toto chování, zvažte scénář, ve kterém spotřebitel vydá požadavek na přijetí a pak dojde k chybě před jeho zpracováním. Vzhledem k tomu, že service bus bude mít označeny zprávy jako spotřebované, pak při restartování aplikace a začne spotřebovávat zprávy znovu, bude mít zmeškané zprávy, která byla spotřebována před selháním.

Pokud `isPeekLock` je parametr nastaven na **hodnotu true**, příjem se stane dvoustupňovou operací, která umožňuje podporovat aplikace, které nemohou tolerovat chybějící zprávy. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Po dokončení aplikace zpracování zprávy (nebo ukládá spolehlivě pro budoucí zpracování), dokončí druhou fázi `deleteMessage` procesu příjmu voláním metody a poskytnutím zprávy, která má být odstraněna jako parametr. Metoda `deleteMessage` označí zprávu jako spotřebované a odebere ji z fronty.

Následující příklad ukazuje, jak přijímat a `receiveQueueMessage`zpracovávat zprávy pomocí . Příklad nejprve obdrží a odstraní zprávu a potom obdrží `isPeekLock` zprávu pomocí nastavena na `deleteMessage` **hodnotu true**, pak odstraní zprávu pomocí :

```javascript
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce není z nějakého důvodu schopna zprávu `unlockMessage` zpracovat, může volat metodu v objektu **ServiceBusService.** způsobí, že service bus odemknout zprávu ve frontě a zpřístupnit ji k přijetí znovu, a to buď stejnou náročnou aplikací nebo jinou náročnou aplikací.

K dispozici je také časový limit přidružený ke zprávě zamčené ve frontě a pokud aplikace nezpracuje zprávu před vypršením časového limitu uzamčení (například pokud dojde k chybě aplikace), pak Service Bus zprávu automaticky odemkne a provede ji k dispozici k tomu, aby byly znovu přijaty.

V případě, že aplikace dojde k chybě `deleteMessage` po zpracování zprávy, ale před je volána metoda, pak zpráva bude znovu doručena do aplikace při restartování. Tento přístup se často nazývá *alespoň jednou zpracování*, to znamená, že každá zpráva bude zpracována alespoň jednou, ale v určitých situacích může být stejná zpráva znovu doručena. Pokud scénář nemůže tolerovat duplicitní zpracování, pak vývojáři aplikací by měli přidat další logiku do své aplikace pro zpracování duplicitní doručení zprávy. Často je dosaženo pomocí **MessageId** vlastnost zprávy, která zůstane konstantní napříč pokusy o doručení.

> [!NOTE]
> Prostředky služby Service Bus můžete spravovat pomocí [aplikace Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Průzkumník služby Service Bus umožňuje uživatelům připojit se k oboru názvů service bus a snadno spravovat entity zasílání zpráv. Nástroj poskytuje pokročilé funkce, jako je funkce importu a exportu nebo možnost testovat téma, fronty, předplatná, přenosové služby, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Další informace o frontách naleznete v následujících zdrojích.

* [Fronty, témata a odběry][Queues, topics, and subscriptions]
* [Azure SDK pro][Azure SDK for Node] úložiště uzlů na GitHubu
* [Středisko pro vývojáře Node.js](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
