---
title: Jak používat fronty služby Service Bus v Node.js | Dokumentace Microsoftu
description: Zjistěte, jak používat fronty Service Bus v Azure z aplikace Node.js.
services: service-bus-messaging
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 7bc291c3a453b4dec5e6c47ab4a7f2e2ac2b9967
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38232775"
---
# <a name="how-to-use-service-bus-queues-with-nodejs"></a>Jak používat fronty služby Service Bus s využitím Node.js

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Tento článek popisuje, jak používat fronty Service Bus s využitím Node.js. Ukázky jsou napsané v jazyce JavaScript a používají modul Node.js Azure. Mezi popsané scénáře patří **vytváření front**, **odesílání a přijímání zpráv**, a **odstranění front**. Další informace o frontách najdete v článku [další kroky](#next-steps) oddílu.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js
Vytvoření prázdné aplikace v Node.js. Pokyny o tom, jak vytvořit aplikaci v Node.js najdete v tématu [vytvoření a nasazení aplikace Node.js na web Azure][Create and deploy a Node.js application to an Azure Website], nebo [cloudové služby pro Node.js] [ Node.js Cloud Service] pomocí Windows Powershellu.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití služby Service Bus
Pokud chcete používat Azure Service Bus, stáhnout a použít balíček Node.js Azure. Tento balíček obsahuje sadu knihoven, které komunikují se službami REST pro Service Bus.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Získat balíček pomocí Node Package Manager (NPM)
1. Použití **prostředí Windows PowerShell pro Node.js** příkazové okno Přejít na **c:\\uzel\\sbqueues\\WebRole1** složky, ve kterém jste vytvořili svou ukázku aplikace.
2. Typ **npm nainstalujte azure** v příkazovém okně, které by měl vést výstup podobný následujícímu:

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
3. Můžete ručně spustit příkaz **ls** a ověřit, že se vytvořila složka **node_modules**. V takové složce najít **azure** balíček, který obsahuje knihovny, budete potřebovat přístup k fronty služby Service Bus.

### <a name="import-the-module"></a>Import modulu
Pomocí poznámkového bloku nebo jiného textového editoru, přidejte následující k hornímu okraji **server.js** souboru aplikace:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Nastavit připojení k Azure Service Bus
Modul Azure načte proměnnou prostředí `AZURE_SERVICEBUS_CONNECTION_STRING` získat informace požadované pro připojení k Service Bus. Pokud tato proměnná prostředí není nastavená, je nutné zadat informace o účtu, při volání metody `createServiceBusService`.

Příklad nastavení proměnných prostředí [webu Azure portal] [ Azure portal] web Azure, najdete v části [webové aplikace v Node.js s úložištěm] [ Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Vytvoření fronty
**ServiceBusService** objektu umožňuje pracovat s frontami služby Service Bus. Následující kód vytvoří **ServiceBusService** objektu. Přidejte do horní části **server.js** soubor po příkazu k importu modulu Azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Voláním `createQueueIfNotExists` na **ServiceBusService** objekt do zadané fronty se vrátí (pokud existuje), nebo vytvořit novou frontu se zadaným názvem. Následující kód používá `createQueueIfNotExists` vytvořit nebo připojit do fronty s názvem `myqueue`:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

`createServiceBusService` Metoda také podporuje další možnosti, které vám umožní přepsat výchozí nastavení fronta jako je například čas zprávy do fronty za provozu nebo maximální velikost. Následující příklad nastaví na hodnotu 5 GB a čas live (TTL) hodnotu 1 minuta maximální velikost fronty:

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
Volitelné filtrování operace lze použít u operací provedených pomocí **ServiceBusService**. Filtrování operací může zahrnovat protokolování, automatickým opakovaným pokusem o atd. Filtry jsou objekty, které implementují metodu s podpisem:

```javascript
function handle (requestOptions, next)
```

Po provedení jeho předběžného zpracování na možnosti žádosti, musí volat metodu `next`, předávání zpětného volání s následující signaturou:

```javascript
function (returnObject, finalCallback, next)
```

V tomto zpětném volání a po zpracování `returnObject` (odpovědi z požadavku na server), zpětného volání musí buď volat `next` pokud existuje pokračovat ve zpracování další filtry, nebo jednoduše vyvolat `finalCallback`, které končí služby vyvolání.

Dva filtry, které implementují logiku opakování, které jsou součástí sady Azure SDK pro Node.js, `ExponentialRetryPolicyFilter` a `LinearRetryPolicyFilter`. Následující kód vytvoří `ServiceBusService` objekt, který se používá `ExponentialRetryPolicyFilter`:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty
Odeslat zprávu do fronty služby Service Bus, vaše aplikace volání `sendQueueMessage` metodu **ServiceBusService** objektu. Zprávy odeslané do (a přijatých z) služby Service Bus jsou fronty **BrokeredMessage** objektů a mají sadu standardních vlastností (jako například **popisek** a **TimeToLive**), slovník, který se používá k uložení vlastních vlastností aplikace a tělo libovolnými aplikačními daty. Aplikace může tělo zprávy nastavit předáním řetězce jako zpráva. Jakékoliv nezbytné vlastnosti standardní zaplnění s výchozími hodnotami.

Následující příklad ukazuje, jak odeslat zkušební zprávu do fronty s názvem `myqueue` pomocí `sendQueueMessage`:

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

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených ve frontě není omezený, ale celková velikost zpráv držených ve frontě omezená je. Velikost fronty se definuje při vytvoření, maximální limit je 5 GB. Další informace o kvótách najdete v tématu [kvótách služby Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Příjem zpráv z fronty
Přijme zprávy z fronty pomocí `receiveQueueMessage` metodu **ServiceBusService** objektu. Ve výchozím nastavení zprávy odstranění z fronty jako jsou načteny; však může číst (Náhled) a uzamčení zprávy bez odstranění z fronty tak, že nastavíte parametr volitelný `isPeekLock` k **true**.

Výchozí chování pro čtení a odstranění zprávy jako součást operace receive je nejjednodušší model a funguje nejlépe v situacích, ve kterých aplikace může tolerovat možnost, zprávy v případě selhání. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že Service Bus se už ale zprávu označila jako spotřebovávanou, pak když aplikace znovu spustí a začne znovu přijímat zprávy, ji budou pravděpodobně nenalezlo zprávu, která se spotřebovala před pádem vynechá.

Pokud `isPeekLock` parametr je nastaven na **true**, receive stane dvoufázového operaci, která umožňuje podporuje aplikace, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Poté, co aplikace dokončí zpracování zprávy (nebo spolehlivě uloží pro pozdější zpracování), dokončení druhé fáze přijetí voláním `deleteMessage` metoda a poskytující zprávu, která se má odstranit jako parametr. `deleteMessage` Metoda označí zprávu jako spotřebovávanou a odstraní ji z fronty.

Následující příklad ukazuje, jak pro příjem a zpracování zpráv s použitím `receiveQueueMessage`. V příkladu nejprve obdrží a odstraní zprávu a potom přijímá zprávy pomocí `isPeekLock` nastavena na **true**, odstraní zprávu pomocí `deleteMessage`:

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
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud přijímající aplikace nedokáže zpracovat zprávu z nějakého důvodu, pak může volat `unlockMessage` metodu **ServiceBusService** objektu. To způsobí, že služba Service Bus zprávu odemkne ve frontě a zpřístupní ji pro další přijetí, stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

K dispozici je také vypršení časového limitu přidružené zpráva uzamčená ve frontě, a pokud aplikace zprávu nezpracuje zámku vyprší časový limit (například pokud aplikace spadne), pak se Service Bus zprávu automaticky odemkne a nastavte ji k dispozici pro další přijetí.

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než `deleteMessage` metoda je volána, pak bude doručit víckrát do aplikace při restartování. To se často nazývá *zpracování nejméně jednou*, to znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může doručit víckrát. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. To se často opírá **MessageId** vlastnosti zprávy, která zůstanou konstantní pokusu o doručení.

## <a name="next-steps"></a>Další postup
Další informace o fronty, naleznete v následujících zdrojích.

* [Fronty, témata a odběry][Queues, topics, and subscriptions]
* [Azure SDK pro Node] [ Azure SDK for Node] úložišti na Githubu
* [Středisko pro vývojáře Node.js](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
