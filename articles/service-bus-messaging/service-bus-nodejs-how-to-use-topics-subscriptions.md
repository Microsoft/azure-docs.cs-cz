---
title: Jak používat témata a předplatné Azure Service Bus s Node.js | Microsoft Docs
description: Naučte se používat témata a odběry Service Bus v Azure z aplikace Node.js.
services: service-bus-messaging
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: d3a7ebd135f705a6a3ea91feb4e037a9ed6d0c79
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641301"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs"></a>Postup použití služby Service Bus témata a odběry s Node.js

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Tato příručka popisuje, jak používat témata Service Bus a odběry z aplikací Node.js. Pokryté scénáře zahrnují:

- Vytváření témat a odběrů 
- Vytváření filtrů odběrů 
- Odeslání zprávy do tématu 
- Přijímání zpráv z odběru
- Odstranění témat a odběrů 

Další informace o tématech a odběrech najdete v tématu [další kroky](#next-steps) části.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js
Vytvoření prázdné aplikace Node.js. Pokyny pro vytvoření aplikace Node.js, naleznete v části [vytvoření a nasazení aplikace Node.js na webovou stránku Azure], [Node.js Cloudová služba] [ Node.js Cloud Service] pomocí systému Windows Prostředí PowerShell nebo na webovém serveru pomocí služby WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití služby Service Bus
K použití služby Service Bus, stáhněte si balíček Node.js Azure. Tento balíček obsahuje sadu knihoven, které komunikují s služby REST pro Service Bus.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Získat balíček pomocí uzlu balíček správce (NPM)
1. Otevřete rozhraní příkazového řádku, jako **prostředí PowerShell** (Windows), **Terminálové** (Mac), nebo **Bash** (Unix).
2. Přejděte do složky, které jste vytvořili ukázkové aplikaci.
3. Typ **npm nainstalujte azure** v okně příkazového řádku, což by měla vést ke následující výstup:

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
3. Můžete ručně spustit **ls** příkazu ověřte, že **uzlu\_moduly** složka byla vytvořena. V této složce najít **azure** balíček, který obsahuje knihovny, je třeba získat přístup témat sběrnice Service Bus.

### <a name="import-the-module"></a>Naimportujte modul
Pomocí programu Poznámkový blok nebo jiném textovém editoru, přidejte následující do horní části **server.js** souboru aplikace:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Nastavení připojení služby Service Bus
Azure modul čte proměnnou prostředí `AZURE_SERVICEBUS_CONNECTION_STRING` pro připojovací řetězec, který jste získali z předchozích kroků, "získat přihlašovací údaje." Pokud není nastavena tato proměnná prostředí, musíte zadat informace o účtu při volání metody `createServiceBusService`.

Příklad nastavení proměnných prostředí pro cloudové služby Azure, naleznete v části [Node.js Cloudová služba se úložiště][Node.js Cloud Service with Storage].



## <a name="create-a-topic"></a>Vytvoření tématu
**ServiceBusService** objektu umožňuje pracovat s tématy. Následující kód vytvoří **ServiceBusService** objektu. Přidejte do horní části **server.js** souboru po příkazu k importu modulu azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Když zavoláte `createTopicIfNotExists` na **ServiceBusService** objektu zadané téma se vrátí (pokud existuje), nebo se vytvoří nové téma se zadaným názvem. Následující kód používá `createTopicIfNotExists` vytvořit nebo připojit k tématu s názvem `MyTopic`:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

`createServiceBusService` Metoda také podporuje další možnosti, které vám umožní přepsat výchozí nastavení téma například zpráva hodnota time to live nebo téma maximální velikost. 

Následující příklad nastaví téma maximální velikost 5 GB s časem TTL jedna minuta:

```javascript
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Filtry
Volitelné filtrování operace lze použít pro operace provedené pomocí **ServiceBusService**. Filtrování operací může zahrnovat protokolování, automatickým opakovaným pokusem o atd. Filtry jsou objekty, které implementovat metodu s podpisem:

```javascript
function handle (requestOptions, next)
```

Po provedení předzpracování na žádost o možnostech, volá metodu `next`a předá zpětné volání podpisem následující:

```javascript
function (returnObject, finalCallback, next)
```

V této zpětného volání a po zpracování `returnObject` (odpověď z požadavku na serveru), zpětné volání musí buď pokračovat zpracování ostatní filtry vyvolat další (pokud existuje) nebo vyvolat `finalCallback` na konec volání služby.

Dva filtry, které implementují logiku opakovaných pokusů, které jsou součástí sady Azure SDK pro Node.js, **ExponentialRetryPolicyFilter** a **LinearRetryPolicyFilter**. Vytvoří následující **ServiceBusService** objekt, který používá **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Vytvořte odběr
Odběry témat taky jsou vytvořeny pomocí **ServiceBusService** objektu. Odběry mají názvy a můžou mít volitelné filtry, které omezují výběr zpráv doručených do virtuální fronty odběru.

> [!NOTE]
> Odběry jsou trvalé, dokud buď jejich nebo tématu jsou spojeny s, se odstraní. Pokud vaše aplikace obsahuje logiku pro vytvoření odběru, by měl nejprve zkontrolujte, jestli existuje odběr pomocí `getSubscription` metoda.
>
>

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Vytvoření odběru s výchozím filtrem (MatchAll).
**MatchAll** filtr je výchozí filtr používají při vytváření odběru. Když použijete filtr **MatchAll**, všechny zprávy publikované do tématu se umístí do virtuální fronty odběru. Následující příklad vytvoří odběr s názvem AllMessages a používá výchozí **MatchAll** filtru.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Vytvoření odběru s filtry
Můžete taky vytvořit filtry, které umožňují, že jste do oboru, který zprávy odeslané do tématu by měla objeví v konkrétním odběru tématu.

Nejflexibilnější filtr, který odběry podporují je **SqlFilter**, který implementuje je podmnožinou SQL92. Filtry SQL pracují s vlastnostmi zpráv publikované do tématu. Další podrobnosti o výrazy, které lze použít s filtrem SQL, projděte si [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] syntaxe.

Filtry lze přidat na předplatné s použitím `createRule` metodu **ServiceBusService** objektu. Tato metoda umožňuje přidat nové filtry k existujícímu předplatnému.

> [!NOTE]
> Protože výchozí filtr je automaticky použita pro všechny nové odběry, je nutno nejprve odstranit výchozí filtr nebo **MatchAll** přepíše ostatní filtry, můžete určit. Výchozí pravidla můžete odebrat pomocí `deleteRule` metodu **ServiceBusService** objektu.
>
>

Následující příklad vytvoří odběr s názvem `HighMessages` s **SqlFilter** který vybere jen zprávy, které mají vlastní `messagenumber` vlastnost větší než 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'HighMessages',
            'HighMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Podobně platí, tento příklad vytvoří odběr s názvem `LowMessages` s **SqlFilter** který vybere jen zprávy, které mají `messagenumber` vlastnost menší než nebo rovna na 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'LowMessages',
            'LowMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Když je nyní odeslána zpráva `MyTopic`, bude doručena příjemci `AllMessages` odběr tématu a selektivně příjemcům přihlásit k odběru `HighMessages` a `LowMessages` odběry témat (v závislosti na obsah zprávy).

## <a name="how-to-send-messages-to-a-topic"></a>Odesílání zpráv do tématu
K odeslání zprávy do tématu Service Bus, musí vaše aplikace používat `sendTopicMessage` metodu **ServiceBusService** objektu.
Zprávy odeslané do témat Service Bus jsou **BrokeredMessage** objekty.
**BrokeredMessage** objekty mají sadu standardních vlastností (jako například `Label` a `TimeToLive`), slovník používaný pro udržení vlastních vlastností specifické pro aplikaci a obsah řetězec dat. Aplikace může tělo zprávy nastavit předáním řetězcové hodnoty `sendTopicMessage` a potřebné standardní vlastnosti vyplní výchozí hodnoty.

Následující příklad ukazuje, jak odeslat pět zkušebních zpráv do `MyTopic`. `messagenumber` Hodnotu vlastnosti každé zprávy se liší na iteraci smyčky (to určuje, které odběry ji přijmou):

```javascript
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Neexistuje žádné omezení počtu zpráv držených v tématu, ale je omezena na celková velikost zpráv držených v tématu. Velikost tématu se definuje při vytvoření, maximální limit je 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Příjem zpráv z odběru
Přijímání zpráv z odběru pomocí `receiveSubscriptionMessage` metodu **ServiceBusService** objektu. Ve výchozím nastavení jsou odstraněny zprávy z odběru jako jejich přečtení. Však můžete nastavit volitelný parametr `isPeekLock` k **true** číst (funkce Náhled) a uzamčení zpráva bez odstranění z předplatného.

Výchozí chování pro čtení a odstranění zprávy v rámci operace příjmu je nejjednodušší model a funguje nejlépe pro scénáře, ve kterých aplikace může tolerovat selhání se zpráva nezpracuje. Zjistit, toto chování, vezměte v úvahu scénář, ve kterém spotřebitel vyšle požadavek na přijetí a pak dojde k chybě před zpracováním ho. Vzhledem k tomu, že Service Bus se už ale zprávu označila jako spotřebovávanou, pak když se aplikace restartuje a začne znovu přijímat zprávy, ji budou neuskutečnily zprávu, která se spotřebovala před havárii.

Pokud `isPeekLock` parametr je nastaven na **true**, receive stane dvoufázová operaci, která vám umožní podporuje aplikace, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu využívat, uzamkne ji proti spotřebování jinými odběrateli příjem ho a vrátí ji do aplikace.
Když aplikace zprávu zpracuje (nebo ji bezpečně uloží pro pozdější zpracování), tím potvrdí dokončení druhé fáze přijetí volání **deleteMessage** metoda a předá zprávu odstranit jako parametr. **DeleteMessage** metoda označí zprávu jako spotřebovanou a odstraní ji z odběru.

Následující příklad ukazuje, jak můžete obdržet zprávy a zpracování pomocí `receiveSubscriptionMessage`. V příkladu nejprve obdrží a odstraní zprávu z předplatného 'LowMessages' a potom pomocí předplatného 'HighMessages' přijme zprávu o `isPeekLock` nastaven na hodnotu true. Pak odstraní zprávu pomocí `deleteMessage`:

```javascript
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        })
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud přijímající aplikace nedokáže zpracovat zprávu z nějakého důvodu, pak může zavolat `unlockMessage` metodu **ServiceBusService** objektu. Tato metoda způsobí, že Service Bus zprávu odemkne v odběru a zpřístupní ji pro přijetí. V tomto případě stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

Je také vypršení časového limitu zpráva uzamčená v odběru. Pokud aplikace se nepodaří zpracovat zprávu, než vyprší časový limit uzamčení (například pokud aplikace spadne), Service Bus zprávu automaticky odemkne a ji zpřístupní k přijetí.

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než `deleteMessage` metoda je volána, zpráva se víckrát do aplikace odešle znovu. Toto chování se často říká *alespoň jedno zpracování*. To znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může být stejná zpráva víckrát. Pokud scénář nemůže tolerovat zpracování duplicitní, měli přidat logiku do vaší aplikace pro zpracování víckrát doručené zprávy. Můžete použít **MessageId** vlastnosti zprávy, která zůstává konstantní mezi pokusy o doručení.

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů
Témata a odběry jsou trvalé a musí být explicitně odstranit buď prostřednictvím [portál Azure] [ Azure portal] nebo prostřednictvím kódu programu.
Následující příklad ukazuje, jak odstranit téma s názvem `MyTopic`:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Pokud se odstraní téma, odstraní se i všechny odběry registrované k tomuto tématu. Odběry se taky dají odstranit samostatně. Následující příklad ukazuje, jak odstranit odběr s názvem `HighMessages` z `MyTopic` tématu:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy témat sběrnice Service Bus, postupujte podle následujících odkazech na další informace.

* V tématu [fronty, témata a odběry][Queues, topics, and subscriptions].
* Reference k rozhraní API pro [SqlFilter][SqlFilter]
* Přejděte [Azure SDK pro uzel] [ Azure SDK for Node] úložišti na Githubu.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Vytvoření a nasazení aplikace Node.js na webovou stránku Azure]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

