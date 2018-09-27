---
title: Jak používat Azure Service Bus, témat a odběrů s Node.js | Dokumentace Microsoftu
description: Zjistěte, jak používat témata a odběry Service Bus v Azure z aplikace Node.js.
services: service-bus-messaging
documentationcenter: nodejs
author: spelluru
manager: timlt
editor: ''
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/10/2018
ms.author: spelluru
ms.openlocfilehash: f13e46b310f4f9048b38ab50ce0241d1b2b3161b
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395691"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs"></a>Postup použití služby Service Bus témata a odběry s využitím Node.js

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Tato příručka popisuje, jak používat témata služby Service Bus a odběrů v aplikacích Node.js. Mezi popsané scénáře patří:

- Vytváření témat a odběrů 
- Vytváření filtrů odběrů 
- Odesílání zpráv do tématu 
- Příjem zpráv z odběru
- Odstranění témat a odběrů 

Další informace o tématech a odběrech najdete v tématu [další kroky](#next-steps) oddílu.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js
Vytvoření prázdné aplikace v Node.js. Pokyny týkající se vytvoření aplikace Node.js najdete v tématu [vytvoření a nasazení aplikace Node.js na web Azure], [cloudové služby pro Node.js] [ Node.js Cloud Service] pomocí Windows Prostředí PowerShell, nebo webu pomocí Webmatrixu.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití služby Service Bus
Použití služby Service Bus, stáhněte si balíček Node.js Azure. Tento balíček obsahuje sadu knihoven, které komunikují se službami REST pro Service Bus.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Získat balíček pomocí Node Package Manager (NPM)
1. Otevřete rozhraní příkazového řádku, jako **PowerShell** (Windows), **terminálu** (Mac), nebo **Bash** (Unix).
2. Přejděte do složky, ve které jste vytvořili ukázkovou aplikaci.
3. Typ **npm nainstalujte azure** v příkazovém okně, které by měl vést následující výstup:

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
3. Můžete ručně spustit **ls** příkazu ověřte, že **uzel\_moduly** složka byla vytvořena. V takové složce najít **azure** balíček, který obsahuje knihovny, budete potřebovat přístup k témat sběrnice Service Bus.

### <a name="import-the-module"></a>Import modulu
Pomocí poznámkového bloku nebo jiného textového editoru, přidejte následující k hornímu okraji **server.js** souboru aplikace:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Nastavit připojení služby Service Bus
Modul Azure načte proměnnou prostředí `AZURE_SERVICEBUS_CONNECTION_STRING` pro připojovací řetězec, který jste získali z předchozích kroků, "získání přihlašovacích údajů." Pokud tato proměnná prostředí není nastavená, je nutné zadat informace o účtu, při volání metody `createServiceBusService`.

Příklad nastavení proměnných prostředí pro cloudové služby Azure najdete v tématu [Cloudová služba Node.js s využitím úložiště][Node.js Cloud Service with Storage].



## <a name="create-a-topic"></a>Vytvoření tématu
**ServiceBusService** objektu umožňuje pracovat s tématy. Následující kód vytvoří **ServiceBusService** objektu. Přidejte do horní části **server.js** soubor po příkazu k importu modulu azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Při volání `createTopicIfNotExists` na **ServiceBusService** objektu zadaného tématu se vrátí (pokud existuje), nebo vytvořit nové téma se zadaným názvem. Následující kód používá `createTopicIfNotExists` vytvořit nebo připojit k tématu s názvem `MyTopic`:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

`createTopicIfNotExists` Metoda také podporuje další možnosti, které vám umožní přepsat výchozí nastavení téma například time to live zprávy nebo téma maximální velikost. 

Následující příklad nastaví maximální počet témat velikosti 5 GB s časem TTL délce jedné minuty:

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
Volitelné filtrování operace lze použít u operací provedených pomocí **ServiceBusService**. Filtrování operací může zahrnovat protokolování, automatickým opakovaným pokusem o atd. Filtry jsou objekty, které implementují metodu s podpisem:

```javascript
function handle (requestOptions, next)
```

Po provedení předzpracování na možnosti žádosti, volá metodu `next`a předá zpětného volání s následující signaturou:

```javascript
function (returnObject, finalCallback, next)
```

V tomto zpětném volání a po zpracování `returnObject` (odpovědi z požadavku na server), zpětného volání musí vyvolat další (pokud existuje) Chcete-li pokračovat ve zpracování další filtry, nebo vyvolat `finalCallback` k ukončení volání služby.

Sada Azure SDK pro Node.js obsahuje dva filtry, které implementují logiku opakování: **ExponentialRetryPolicyFilter** a **LinearRetryPolicyFilter**. Vytvoří následující **ServiceBusService** objekt, který se používá **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Vytvořit odběry
Odběry témat taky jsou vytvořeny pomocí **ServiceBusService** objektu. Odběry mají názvy a můžou mít volitelné filtry, který omezuje sadu doručování zpráv do virtuální fronty odběru.

> [!NOTE]
> Předplatná jsou až do buď trvalé nebo téma, které jsou přidružené, se odstraní. Pokud vaše aplikace obsahuje logiku pro vytvoření odběru, měli by nejdřív zkontrolovat, pokud existuje odběr s použitím `getSubscription` metody.
>
>

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Vytvoření odběru s výchozím filtrem (MatchAll).
**MatchAll** filtr je výchozí filtr použitý při přihlášení k odběru. Když použijete filtr **MatchAll**, všechny zprávy publikované do tématu se umístí do virtuální fronty odběru. Následující příklad vytvoří odběr s názvem AllMessages a používá výchozí **MatchAll** filtru.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Vytvoření odběru s filtry
Můžete taky vytvořit filtry, které umožňují že jste do oboru, které zprávy odeslané do tématu měla zobrazit v konkrétním odběru tématu.

Nejflexibilnější filtr předplatných je **SqlFilter**, která implementuje podmnožinu SQL92. Filtry SQL pracují s vlastnostmi zpráv publikované do tématu. Další informace o výrazech, které lze použít s filtrem SQL, přečtěte si [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] syntaxe.

Filtry lze přidat na předplatné s použitím `createRule` metodu **ServiceBusService** objektu. Tato metoda umožňuje přidat nové filtry k existujícímu předplatnému.

> [!NOTE]
> Protože výchozí filtr automaticky platí pro všechna nová předplatná, musíte nejdřív odebrat výchozí filtr nebo **MatchAll** přepíše ostatní filtry, můžete zadat. Výchozí pravidla můžete odebrat pomocí `deleteRule` metodu **ServiceBusService** objektu.
>
>

Následující příklad vytvoří odběr s názvem `HighMessages` s **SqlFilter** , který vybere jen zprávy, které mají vlastní `messagenumber` vlastnost větší než 3:

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

Obdobně následující příklad vytvoří odběr s názvem `LowMessages` s **SqlFilter** , který vybere jen zprávy, které mají `messagenumber` vlastnost menší než nebo rovna na 3:

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

Když je teď odeslána zpráva `MyTopic`, se doručí do příjemcům `AllMessages` odběru tématu a selektivně příjemcům `HighMessages` a `LowMessages` odběry tématu (v závislosti na obsah zprávy).

## <a name="how-to-send-messages-to-a-topic"></a>Postup odesílání zpráv do tématu
Odeslat zprávu do tématu služby Service Bus, vaše aplikace musí používat `sendTopicMessage` metodu **ServiceBusService** objektu.
Zprávy odeslané do témat Service Bus jsou **BrokeredMessage** objekty.
**BrokeredMessage** objekty mají sadu standardních vlastností (jako například `Label` a `TimeToLive`), slovník používaný pro udržení vlastních vlastností specifické pro aplikace a tělo s daty řetězec. Aplikace může tělo zprávy nastavit předáním řetězcové hodnoty `sendTopicMessage` a veškeré požadované standardní vlastnosti jsou vyplněn výchozí hodnoty.

Následující příklad ukazuje, jak odeslat pět zkušebních zpráv do `MyTopic`. `messagenumber` Hodnota vlastnosti každé zprávy se liší v iteraci smyčky (to určuje, které odběry ji přijmou):

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

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Neexistuje žádné omezení na počet zpráv držených v tématu, ale celková velikost zpráv držených v tématu, je omezený. Velikost tématu se definuje při vytvoření, maximální limit je 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Příjem zpráv z odběru
Přijme zprávy z odběru pomocí `receiveSubscriptionMessage` metodu **ServiceBusService** objektu. Ve výchozím nastavení zprávy odstraněny z předplatného, jako jsou načteny. Však lze nastavit volitelný parametr `isPeekLock` k **true** číst (Náhled) a uzamčení zprávy bez odstranění z předplatného.

Výchozí chování pro čtení a odstranění zprávy jako součást operace receive je nejjednodušší model a funguje nejlépe v situacích, ve kterých aplikace může tolerovat možnost, zprávy v případě selhání. Informace o tom toto chování, vezměte v úvahu scénář, ve kterém spotřebitel požadavek na přijetí a poté dojde k chybě před její zpracování. Vzhledem k tomu, že Service Bus označil zprávu jako spotřebovávanou, pak když aplikace znovu spustí a začne znovu přijímat zprávy, byl vynechán zprávu, která se spotřebovala před pádem vynechá.

Pokud `isPeekLock` parametr je nastaven na **true**, receive stane dvoufázová operaci, která umožňuje to podporuje aplikace, které nemůžou tolerovat chybějících zpráv. Když Service Bus přijme požadavek, najde zprávu, chcete-li využívají, uzamkne ji ostatní uživatelé z dešifrujete proti a vrátí ji do aplikace.
Poté, co aplikace zpracovává zprávu (nebo spolehlivě uloží pro pozdější zpracování), dokončení druhé fáze přijetí voláním **deleteMessage** metoda a předá zpráva, kterou chcete odstranit jako parametr. **DeleteMessage** metoda označí zprávu jako spotřebovanou a odstraní ji z odběru.

Následující příklad ukazuje, jak lze přijímat zprávy a zpracovaná pomocí `receiveSubscriptionMessage`. V příkladu nejprve obdrží a odstraní zprávu z předplatného "LowMessages" a pak přijme zprávu z předplatného "HighMessages" pomocí `isPeekLock` nastavenou na hodnotu true. Poté odstraní zprávu pomocí `deleteMessage`:

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
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud přijímající aplikace nedokáže zpracovat zprávu z nějakého důvodu, pak může volat `unlockMessage` metodu **ServiceBusService** objektu. Tato metoda způsobí, že Service Bus zprávu odemkne v odběru a zpřístupní ji pro přijetí. V takovém případě stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

Je také vypršení časového limitu zpráva uzamčená v odběru. Pokud se aplikaci nepodaří zprávu zpracovat, než vyprší časový limit zámku (například pokud aplikace spadne), Service Bus zprávu automaticky odemkne a ji zpřístupní k přijetí.

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než `deleteMessage` metoda je volána, zprávu je víckrát do aplikace při restartování. Toto chování se často nazývá *alespoň jedno zpracování*. To znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může doručit víckrát. Pokud scénář nemůže tolerovat zpracování, měli byste do vaší aplikace pro zpracování víckrát doručené zprávy přidat logiku. Můžete použít **MessageId** vlastnosti zprávy, která zůstává konstantní pokusu o doručení.

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů
Témata a odběry jsou trvalé a musí být explicitně odstranit prostřednictvím [webu Azure portal] [ Azure portal] nebo prostřednictvím kódu programu.
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
Teď, když jste se naučili základy témat sběrnice Service Bus, použijte tyto odkazy na další informace.

* Zobrazit [fronty, témata a odběry][Queues, topics, and subscriptions].
* Reference k rozhraní API pro [SqlFilter][SqlFilter]
* Přejděte [sady Azure SDK for Node] [ Azure SDK for Node] úložišti na Githubu.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Vytvoření a nasazení aplikace Node.js na web Azure]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

