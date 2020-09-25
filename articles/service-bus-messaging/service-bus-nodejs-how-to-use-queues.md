---
title: Použití Azure Service Busch front v Node.js pomocí balíčku Azure-Sb
description: Naučte se vytvářet aplikace Node.js pro posílání zpráv a přijímání zpráv z fronty Azure Service Bus pomocí balíčku Azure-Sb.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: 0e94d21bd6550a7d62ef3e7dec302c53f9851a4a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326316"
---
# <a name="quickstart-use-service-bus-queues-in-azure-with-nodejs-and-the-azure-sb-package"></a>Rychlý Start: použití Service Busch front v Azure s využitím Node.js a balíčku Azure-Sb
V tomto kurzu se naučíte vytvářet Node.js aplikace pro posílání zpráv a příjem zpráv z fronty Azure Service Bus pomocí balíčku [Azure-SB](https://www.npmjs.com/package/azure-sb) . Ukázky jsou napsané v JavaScriptu a používají Node.js [modul Azure](https://www.npmjs.com/package/azure) , který interně používá balíček Azure-Sb.

> [!IMPORTANT]
> Balíček [Azure-SB](https://www.npmjs.com/package/azure-sb) používá [Service Bus rozhraní REST runtime API](/rest/api/servicebus/service-bus-runtime-rest). Můžete získat rychlejší práci pomocí nového, [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) který používá rychlejší [protokol AMQP 1,0](service-bus-amqp-overview.md). 
> 
> Další informace o novém balíčku najdete v tématu [použití Service Bus front s Node.js a @azure/service-bus balíčkem](./service-bus-nodejs-how-to-use-queues-new-package.md). Další informace o použití balíčku [Azure](https://www.npmjs.com/package/azure) najdete v tématu o tom, jak pokračovat v čtení.

## <a name="prerequisites"></a>Požadavky
- Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat výhody pro [předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Pokud nemáte frontu, ve které byste mohli pracovat, postupujte podle kroků v tématu [použití Azure Portal k vytvoření fronty Service Bus](service-bus-quickstart-portal.md) .
    1. Přečtěte si rychlý **přehled** Service Busch **front**. 
    2. Vytvořte **obor názvů**Service Bus. 
    3. Získá **připojovací řetězec**. 

        > [!NOTE]
        > V tomto kurzu vytvoříte **frontu** v oboru názvů Service Bus pomocí Node.js. 
 

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js
Vytvořte prázdnou Node.jsovou aplikaci. Pokyny k vytvoření Node.js aplikace najdete v tématu [Vytvoření a nasazení Node.js aplikace na web Azure][Create and deploy a Node.js application to an Azure Website]nebo [Node.js cloudovou službu][Node.js Cloud Service] pomocí Windows PowerShellu.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití Service Bus
Pokud chcete použít Azure Service Bus, Stáhněte a použijte balíček Azure Node.js. Tento balíček obsahuje sadu knihoven, které komunikují se službou Service Bus REST.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>K získání balíčku použijte Správce balíčků Node (NPM).
1. Pomocí příkazového okna **Windows PowerShellu pro Node.js** přejděte do složky **c: \\ node \\ sbqueues \\ WebRole1** , ve které jste vytvořili ukázkovou aplikaci.
2. Do příkazového okna zadejte **npm Install Azure** , které by měly mít za následek výstup podobný následujícímu příkladu:

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
3. Můžete ručně spustit příkaz **ls** a ověřit, že se vytvořila složka **node_modules**. V této složce najděte balíček **Azure** obsahující knihovny, které potřebujete pro přístup k Service Bus frontám.

### <a name="import-the-module"></a>Importovat modul
Pomocí poznámkového bloku nebo jiného textového editoru přidejte na začátek souboru **server.js** aplikace následující:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Nastavení Azure Service Busho připojení
Modul Azure přečte proměnnou prostředí `AZURE_SERVICEBUS_CONNECTION_STRING` a získá informace potřebné pro připojení k Service Bus. Pokud tato proměnná prostředí není nastavena, je nutné při volání zadat informace o účtu `createServiceBusService` .

Příklad nastavení proměnných prostředí v [Azure Portal][Azure portal] webu Azure najdete v tématu [Node.js webové aplikace s úložištěm][Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Vytvoření fronty
Objekt **ServiceBusService** vám umožní pracovat s frontami Service Bus. Následující kód vytvoří objekt **ServiceBusService** . Po příkazu pro import modulu Azure ho přidejte poblíž horní části **server.js** souboru:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Zavoláním `createQueueIfNotExists` objektu **ServiceBusService** se vrátí zadaná fronta (pokud existuje), nebo se vytvoří nová fronta se zadaným názvem. Následující kód používá `createQueueIfNotExists` k vytvoření nebo připojení k frontě s názvem `myqueue` :

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

`createServiceBusService`Metoda také podporuje další možnosti, které umožňují přepsat výchozí nastavení fronty, jako je například doba zprávy na hodnotu Live nebo maximální velikost fronty. Následující příklad nastaví maximální velikost fronty na 5 GB a hodnotu TTL (Time to Live) 1 minute:

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
Volitelné operace filtrování lze použít na operace prováděné pomocí **ServiceBusService**. Operace filtrování můžou zahrnovat protokolování, automatické opakování atd. Filtry jsou objekty, které implementují metodu s podpisem:

```javascript
function handle (requestOptions, next)
```

Po provedení předběžného zpracování na možnostech žádosti musí metoda volat `next` , předání zpětného volání s následujícím podpisem:

```javascript
function (returnObject, finalCallback, next)
```

V tomto zpětném volání a po zpracování `returnObject` (odpověď z požadavku na server) musí zpětné volání vyvolat, `next` Pokud existuje, aby pokračoval v zpracování jiných filtrů, nebo vyvolat `finalCallback` , což ukončí vyvolání služby.

K dispozici jsou dva filtry, které implementují logiku opakování, do sady Azure SDK pro Node.js `ExponentialRetryPolicyFilter` a `LinearRetryPolicyFilter` . Následující kód vytvoří `ServiceBusService` objekt, který používá `ExponentialRetryPolicyFilter` :

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty
Chcete-li odeslat zprávu do fronty Service Bus, vaše aplikace zavolá `sendQueueMessage` metodu na objekt **ServiceBusService** . Zprávy odeslané do (a přijaté z) Service Bus fronty jsou objekty **BrokeredMessage** a mají sadu standardních vlastností (například **Label** a **TimeToLive**), slovník, který se používá k uložení vlastních vlastností specifických pro aplikaci a tělo libovolných aplikačních dat. Aplikace může nastavit tělo zprávy předáním řetězce jako zprávy. Všechny požadované standardní vlastnosti se naplní výchozími hodnotami.

Následující příklad ukazuje, jak odeslat zkušební zprávu do fronty s názvem `myqueue` pomocí `sendQueueMessage` :

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

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených ve frontě není nijak omezený, ale celková velikost zpráv držených ve frontě je příliš velká. Velikost fronty se definuje při vytvoření, maximální limit je 5 GB. Další informace o kvótách najdete v tématu [Service Bus kvóty][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Přijímání zpráv z fronty
Zprávy jsou přijímány z fronty pomocí `receiveQueueMessage` metody v objektu **ServiceBusService** . Ve výchozím nastavení se zprávy při čtení z fronty odstraňují. Můžete si však přečíst (prohlížet) a uzamknout zprávu bez jejich odstranění z fronty nastavením volitelného parametru `isPeekLock` na **hodnotu true**.

Výchozím chováním při čtení a odstraňování zprávy jako součást operace Receive je nejjednodušší model a funguje nejlépe ve scénářích, ve kterých aplikace může tolerovat nezpracovávání zprávy, když dojde k selhání. Pro pochopení tohoto chování Vezměte v úvahu situaci, ve které spotřebitel vydá žádost o přijetí, a poté dojde k chybě před jejím zpracováním. Vzhledem k tomu, že Service Bus bude zprávu označovat jako spotřebovaná, a když se aplikace znovu spustí a začne znovu přijímat zprávy, vynechá se zpráva, která byla spotřebována před chybou.

Pokud `isPeekLock` je parametr nastaven na **hodnotu true**, obdrží se operace se dvěma fázemi, což umožňuje podporovat aplikace, které neumožňují tolerovat chybějící zprávy. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Poté, co aplikace dokončí zpracování zprávy (nebo je uloží spolehlivě pro budoucí zpracování), dokončí druhou fázi procesu Receive voláním `deleteMessage` metody a zadáním zprávy, která má být odstraněna jako parametr. `deleteMessage`Metoda označí zprávu jako spotřebou a odebere ji z fronty.

Následující příklad ukazuje, jak přijímat a zpracovávat zprávy pomocí `receiveQueueMessage` . Příklad nejprve přijme a odstraní zprávu a potom obdrží zprávu s použitím `isPeekLock` hodnoty nastavenou na **hodnotu true**a potom zprávu odstraní pomocí `deleteMessage` :

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
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce z nějakého důvodu nemůže zprávu zpracovat, může zavolat `unlockMessage` metodu na objekt **ServiceBusService** . způsobí Service Bus odemčení zprávy ve frontě a její zpřístupnění pro opětovné přijetí, a to buď pomocí stejné aplikace, nebo jiné náročné aplikace.

Je také časový limit přidružený ke zprávě uzamčený ve frontě a pokud aplikace nedokáže zpracovat zprávu před vypršením časového limitu zámku (například pokud dojde k selhání aplikace), Service Bus bude tuto zprávu automaticky odemknout a zpřístupní ji tak, aby byla k dispozici.

V případě, že dojde k chybě aplikace po zpracování zprávy, ale před `deleteMessage` zavoláním metody, bude zpráva doručena do aplikace při restartu. Tento přístup se často označuje *aspoň jednou při zpracování*, to znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může být stejná zpráva znovu dodána. Pokud scénář nemůže tolerovat duplicitní zpracování, vývojáři aplikací by měli do své aplikace přidat další logiku pro zpracování duplicitního doručování zpráv. Často se dosahuje pomocí vlastnosti **MessageID** zprávy, která zůstane v rámci pokusů o doručení konstantní.

> [!NOTE]
> Prostředky Service Bus můžete spravovat pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer umožňuje uživatelům připojit se k oboru názvů Service Bus a snadno spravovat entity zasílání zpráv. Tento nástroj poskytuje pokročilé funkce, jako jsou funkce importu a exportu, nebo možnost testovat témata, fronty, odběry, služby Relay, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Další informace o frontách najdete v následujících zdrojích informací.

* [Fronty, témata a odběry][Queues, topics, and subscriptions]
* [Azure SDK pro úložiště uzlů][Azure SDK for Node] na GitHubu
* [Středisko pro vývojáře Node.js](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/quickstart-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md