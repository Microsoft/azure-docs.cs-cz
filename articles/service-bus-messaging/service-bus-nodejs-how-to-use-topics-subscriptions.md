---
title: Použití Azure Service Bus témat s balíčkem Azure/Service-Bus Node.js
description: Naučte se používat Service Bus témata a odběry v Azure z Node.js aplikace pomocí balíčku Azure/Service-Bus.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 08/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: 219132fc8a0e618cdf2561947ae3904a9e2cb310
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91300747"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azure-sb-package"></a>Rychlý Start: jak používat Service Bus témata a předplatná s Node.js a balíčkem Azure-Sb
V tomto kurzu se naučíte vytvářet Node.js aplikace pro odesílání zpráv do Service Bus tématu a příjem zpráv z Service Bus předplatného pomocí balíčku [Azure-SB](https://www.npmjs.com/package/azure-sb) . Ukázky jsou napsané v JavaScriptu a používají Node.js [modul Azure](https://www.npmjs.com/package/azure) , který interně používá `azure-sb` balíček.

> [!IMPORTANT]
> Balíček [Azure-SB](https://www.npmjs.com/package/azure-sb) používá [Service Bus rozhraní REST runtime API](/rest/api/servicebus/service-bus-runtime-rest). Pomocí nového [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) balíčku, který používá rychlejší [protokol AMQP 1,0](service-bus-amqp-overview.md), můžete získat rychlejší prostředí. 
> 
> Další informace o novém balíčku najdete v tématu [Jak používat Service Bus témata a odběry s Node.js a @azure/service-bus balíčkem](./service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md). Další informace o použití balíčku [Azure](https://www.npmjs.com/package/azure) najdete v tématu o tom, jak pokračovat v čtení.

Zde uvedené scénáře zahrnují:

- Vytváření témat a předplatných 
- Vytváření filtrů předplatného 
- Odesílání zpráv do tématu 
- Příjem zpráv z předplatného
- Odstraňování témat a předplatných 

Další informace o tématech a předplatných najdete v části [Další kroky](#next-steps) .

## <a name="prerequisites"></a>Předpoklady
- Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat výhody pro [předplatitele sady Visual Studio nebo MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Postupujte podle kroků v [rychlém startu: použijte Azure Portal k vytvoření Service Bus tématu a odběry tématu](service-bus-quickstart-topics-subscriptions-portal.md) k vytvoření **oboru názvů** Service Bus a získání **připojovacího řetězce**.

    > [!NOTE]
    > V tomto rychlém startu vytvoříte **téma** a **předplatné** k tématu pomocí **Node.js** . 

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js
Vytvořte prázdnou Node.jsovou aplikaci. Pokyny k vytvoření Node.js aplikace najdete v tématu [vytvoření a nasazení Node.js aplikace na web Azure], [Node.js cloudové služby][Node.js Cloud Service] pomocí prostředí Windows PowerShell nebo webu s WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití Service Bus
Pokud chcete použít Service Bus, Stáhněte si balíček Azure Node.js. Tento balíček obsahuje sadu knihoven, které komunikují se službou Service Bus REST.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>K získání balíčku použijte Správce balíčků Node (NPM).
1. Otevřete rozhraní příkazového řádku, jako je **PowerShell** (Windows), **terminál** (Mac) nebo **bash** (UNIX).
2. Přejděte do složky, ve které jste vytvořili ukázkovou aplikaci.
3. Do příkazového okna zadejte **npm instalování Azure** , které by mělo mít za následek následující výstup:

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
3. Můžete ručně spustit příkaz **ls** a ověřit tak, že se vytvořila složka ** \_ modulů uzlů** . V této složce najděte balíček **Azure** obsahující knihovny, které potřebujete pro přístup k Service Bus tématům.

### <a name="import-the-module"></a>Importovat modul
Pomocí poznámkového bloku nebo jiného textového editoru přidejte na začátek souboru **server.js** aplikace následující:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Nastavení Service Busho připojení
Modul Azure přečte proměnnou prostředí `AZURE_SERVICEBUS_CONNECTION_STRING` pro připojovací řetězec, který jste získali jako součást [požadavků](#prerequisites). Pokud potřebujete pokyny k opětovnému získání připojovacího řetězce, přečtěte si téma [získání připojovacího řetězce](service-bus-quickstart-topics-subscriptions-portal.md#get-the-connection-string). Pokud tato proměnná prostředí není nastavena, je nutné při volání zadat informace o účtu `createServiceBusService` .

Příklad nastavení proměnných prostředí pro cloudovou službu Azure najdete v tématu [nastavení proměnných prostředí](../container-instances/container-instances-environment-variables.md#azure-cli-example).



## <a name="create-a-topic"></a>Vytvoření tématu
Objekt **ServiceBusService** vám umožní pracovat s tématy. Následující kód vytvoří objekt **ServiceBusService** . Po příkazu pro import modulu Azure ho přidejte poblíž horní části **server.js** souboru:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Pokud zavoláte `createTopicIfNotExists` objekt **ServiceBusService** , zadané téma se vrátí (pokud existuje), nebo se vytvoří nové téma se zadaným názvem. Následující kód používá `createTopicIfNotExists` k vytvoření nebo připojení k tématu s názvem `MyTopic` :

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

`createTopicIfNotExists`Metoda také podporuje další možnosti, které umožňují přepsat výchozí nastavení tématu, jako je například doba zprávy na hodnotu Live nebo maximální velikost tématu. 

Následující příklad nastaví maximální velikost tématu na 5 GB s časovým limitem na 1 minutu:

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
Volitelné operace filtrování lze použít na operace prováděné pomocí **ServiceBusService**. Operace filtrování můžou zahrnovat protokolování, automatické opakování atd. Filtry jsou objekty, které implementují metodu s podpisem:

```javascript
function handle (requestOptions, next)
```

Po provedení předběžného zpracování v možnostech žádosti metoda volá `next` a předá zpětné volání s následujícím podpisem:

```javascript
function (returnObject, finalCallback, next)
```

V tomto zpětném volání a po zpracování `returnObject` (odpověď z požadavku na server) musí zpětné volání vyvolat Next (pokud existuje) pro pokračování v zpracování jiných filtrů nebo vyvolání `finalCallback` pro ukončení volání služby.

Sada Azure SDK pro Node.js obsahuje dva filtry, které implementují logiku opakování: **ExponentialRetryPolicyFilter** a **LinearRetryPolicyFilter**. Následující kód vytvoří objekt **ServiceBusService** , který používá **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Vytvoření předplatných
Odběry témat jsou také vytvořeny s objektem **ServiceBusService** . Odběry jsou pojmenovány a mohou mít volitelný filtr, který omezuje sadu zpráv dodaných do virtuální fronty odběru.

> [!NOTE]
> Ve výchozím nastavení jsou odběry trvalé, dokud je neodstraní ani v tématu, ke kterému jsou přidruženy. Pokud vaše aplikace obsahuje logiku pro vytvoření předplatného, měli byste nejdřív ověřit, jestli předplatné existuje, pomocí `getSubscription` metody.
>
> Odběry můžete nechat automaticky odstranit nastavením [vlastnosti AutoDeleteOnIdle](/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Vytvoření odběru s výchozím filtrem (MatchAll).
Filtr **MatchAll** je výchozí filtr, který se používá při vytváření předplatného. Když použijete filtr **MatchAll**, všechny zprávy publikované do tématu se umístí do virtuální fronty odběru. Následující příklad vytvoří předplatné s názvem AllMessages a použije výchozí filtr **MatchAll** .

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Vytvoření odběru s filtry
Můžete také vytvořit filtry, které vám umožní určit rozsah zpráv odesílaných do tématu, které se mají zobrazit v rámci konkrétního předplatného tématu.

Nejpružnější typ filtru podporovaný předplatnými je **SqlFilter**, které implementuje podmnožinu SQL92. Filtry SQL pracují s vlastnostmi zpráv publikované do tématu. Další podrobnosti o výrazech, které lze použít s filtrem SQL, najdete v syntaxi [SqlFilter. syntaxi][SqlFilter.SqlExpression] .

Filtry lze přidat do předplatného pomocí `createRule` metody objektu **ServiceBusService** . Tato metoda umožňuje přidat nové filtry do stávajícího předplatného.

> [!NOTE]
> Vzhledem k tomu, že výchozí filtr je automaticky použit pro všechna nová předplatná, musíte nejprve odebrat výchozí filtr, jinak bude **MatchAll** přepsat všechny další filtry, které můžete zadat. Výchozí pravidlo můžete odebrat pomocí `deleteRule` metody objektu **ServiceBusService** .
>
>

Následující příklad vytvoří odběr s názvem `HighMessages` **SqlFilter** , který vybere pouze zprávy, které mají vlastní `messagenumber` vlastnost větší než 3:

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

Podobně následující příklad vytvoří odběr s názvem `LowMessages` **SqlFilter** , který vybere pouze zprávy, které mají `messagenumber` vlastnost menší nebo rovna 3:

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

Když se zpráva pošle do `MyTopic` , doručí se příjemcům, kteří se přihlásili k odběru `AllMessages` tématu, a selektivně se doručí na příjemce, kteří se přihlásili k odběru `HighMessages` a `LowMessages` předplatným tématům a (v závislosti na obsahu zprávy).

## <a name="how-to-send-messages-to-a-topic"></a>Postup odesílání zpráv do tématu
Chcete-li odeslat zprávu do Service Bus téma, musí vaše aplikace používat `sendTopicMessage` metodu objektu **ServiceBusService** .
Zprávy odeslané do Service Bus témata jsou **BrokeredMessage** objekty.
Objekty **BrokeredMessage** mají sadu standardních vlastností (například `Label` a `TimeToLive` ), slovník, který slouží k uložení vlastních vlastností specifických pro aplikaci a tělo dat řetězce. Aplikace může nastavit tělo zprávy předáním řetězcové hodnoty do `sendTopicMessage` a všechny požadované standardní vlastnosti jsou vyplněny výchozími hodnotami.

Následující příklad ukazuje, jak odeslat pět testovacích zpráv do `MyTopic` . `messagenumber`Hodnota vlastnosti každé zprávy se liší v iteraci smyčky (Tato vlastnost určuje, které odběry dostanou):

```javascript
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (var i = 0; i < 5; i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených v tématu není nijak omezený, ale celková velikost zpráv držených v tématu je omezena. Velikost tématu se definuje při vytvoření, maximální limit je 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Přijímání zpráv z předplatného
Zprávy jsou přijímány z předplatného pomocí `receiveSubscriptionMessage` metody v objektu **ServiceBusService** . Ve výchozím nastavení se zprávy z předplatného odstraňují při jejich čtení. Můžete ale nastavit volitelný parametr `isPeekLock` na **hodnotu true** pro čtení (Náhled) a uzamknout zprávu, aniž byste ji museli odstranit z předplatného.

Výchozím chováním při čtení a odstraňování zprávy jako součást operace Receive je nejjednodušší model a funguje nejlépe ve scénářích, ve kterých aplikace může tolerovat nezpracovávající zprávu, pokud dojde k selhání. Pro pochopení tohoto chování Vezměte v úvahu situaci, ve které spotřebitel vydá žádost o přijetí, a poté dojde k chybě před jejím zpracováním. Vzhledem k tomu, že Service Bus označila zprávu jako spotřebovaná, pak když se aplikace znovu spustí a začne znovu přijímat zprávy, vynechala zprávu, která byla spotřebována před selháním.

Pokud `isPeekLock` je parametr nastaven na **hodnotu true**, obdrží se operace se dvěma fázemi, což umožňuje podporovat aplikace, které nemůžou tolerovat zmeškané zprávy. Když Service Bus obdrží žádost, najde další zprávu, která se má použít, zamkne ji, aby zabránila tomu, aby ji ostatní příjemci přijímala a vrátila ji do aplikace.
Poté, co aplikace zpracuje zprávu (nebo ji spolehlivě ukládá pro účely budoucího zpracování), dokončí druhou fázi procesu Receive voláním metody **deleteMessage** a předá zprávu, aby ji bylo možné odstranit jako parametr. Metoda **deleteMessage** označí zprávu jako spotřebou a odebere ji z předplatného.

Následující příklad ukazuje, jak lze přijímat a zpracovávat zprávy pomocí `receiveSubscriptionMessage` . Příklad nejprve přijme a odstraní zprávu z předplatného "LowMessages" a poté obdrží zprávu z předplatného "HighMessages" pomocí příkazu `isPeekLock` set na hodnotu true. Pak tuto zprávu odstraní pomocí `deleteMessage` :

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
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce z nějakého důvodu nemůže zprávu zpracovat, může zavolat `unlockMessage` metodu na objekt **ServiceBusService** . Tato metoda způsobí, že Service Bus odemkne zprávu v rámci předplatného a zpřístupní ji pro přijetí znovu. V této instanci buď stejnou spotřebou aplikace, nebo jinou spotřebou aplikace.

K uzamčené zprávě v rámci předplatného je také přiřazen časový limit. Pokud aplikace nedokáže zpracovat zprávu před vypršením časového limitu zámku (například v případě, že aplikace selže), Service Bus automaticky odemkne zprávu a zpřístupní ji, aby byla k dispozici.

V případě, že dojde k chybě aplikace po zpracování zprávy, ale před `deleteMessage` zavoláním metody, bude zpráva doručena do aplikace při restartu. Toto chování se často nazývá *alespoň po zpracování*. To znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích se může stejná zpráva znovu doručovat. Pokud scénář nemůže tolerovat duplicitní zpracování, pak byste měli do aplikace přidat logiku pro zpracování duplicitního doručování zpráv. Můžete použít vlastnost **MessageID** zprávy, která zůstává v rámci pokusů o doručení konstantní.

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů
Témata a odběry jsou trvalé, pokud není nastavena [vlastnost AutoDeleteOnIdle](/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle) a je nutné ji explicitně odstranit buď pomocí [Azure Portal][Azure portal] nebo programově.
Následující příklad ukazuje, jak odstranit téma s názvem `MyTopic` :

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Pokud se odstraní téma, odstraní se i všechny odběry registrované k tomuto tématu. Odběry se taky dají odstranit samostatně. Následující příklad ukazuje, jak odstranit předplatné s názvem `HighMessages` z `MyTopic` tématu:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

> [!NOTE]
> Prostředky Service Bus můžete spravovat pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer umožňuje uživatelům připojit se k oboru názvů Service Bus a snadno spravovat entity zasílání zpráv. Tento nástroj poskytuje pokročilé funkce, jako jsou funkce importu a exportu, nebo možnost testovat témata, fronty, odběry, služby Relay, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Teď, když jste se seznámili se základy Service Bus témata, získáte další informace na následujících odkazech.

* Viz [fronty, témata a odběry][Queues, topics, and subscriptions].
* Reference pro API pro [SqlFilter][SqlFilter].
* Navštivte [Azure SDK pro úložiště uzlů][Azure SDK for Node] na GitHubu.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /javascript/api/@azure/arm-servicebus/sqlfilter?view=azure-node-latest
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Create and deploy a Node.js application to Azure App Service]: ../app-service/quickstart-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

