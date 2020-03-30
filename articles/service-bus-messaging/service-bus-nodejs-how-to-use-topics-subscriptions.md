---
title: Použití témat Azure Service Bus s balíčkem Azure/service-bus Node.js
description: Zjistěte, jak používat témata a předplatná služby Service Bus v Azure z aplikace Node.js pomocí balíčku azure/service-bus.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 01/16/2020
ms.author: aschhab
ms.openlocfilehash: c85b63b4a56e74b0fef9a122ec995b4106496cbe
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330442"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azure-sb-package"></a>Úvodní příručka: Použití témat a předplatných služby Service Bus pomocí souboru Node.js a balíčku azure-sb
V tomto kurzu se dozvíte, jak vytvořit aplikace Node.js pro odesílání zpráv do tématu service bus a přijímat zprávy z předplatného service bus pomocí balíčku [azure-sb.](https://www.npmjs.com/package/azure-sb) Ukázky jsou zapsány v Jazyce JavaScript a použít [modul](https://www.npmjs.com/package/azure) `azure-sb` Azure Node.js, který interně používá balíček.

> [!IMPORTANT]
> Balíček [azure-sb](https://www.npmjs.com/package/azure-sb) používá [run-time API service bus REST](/rest/api/servicebus/service-bus-runtime-rest). Můžete získat rychlejší prostředí pomocí [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) nového balíčku, který používá rychlejší [protokol AMQP 1.0](service-bus-amqp-overview.md). 
> 
> Další informace o novém balíčku najdete v [tématu Jak používat témata a @azure/service-bus odběry služby Service Bus s Node.js a balíček](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-topics-subscriptions-new-package), jinak pokračovat ve čtení vidět, jak používat balíček [Azure.](https://www.npmjs.com/package/azure)

Scénáře, které jsou zde zahrnuty, zahrnují:

- Vytváření témat a předplatných 
- Vytváření filtrů předplatného 
- Odesílání zpráv k tématu 
- Příjem zpráv z předplatného
- Odstranění témat a předplatných 

Další informace o tématech a předplatných najdete v části [Další kroky.](#next-steps)

## <a name="prerequisites"></a>Požadavky
- Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat [výhody pro předplatitele sady Visual Studio nebo MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Postupujte podle kroků na [úvodním panelu: Pomocí portálu Azure vytvořte téma služby Service Bus a odběry tématu](service-bus-quickstart-topics-subscriptions-portal.md) k vytvoření **oboru názvů** Service Bus a získání **připojovacího řetězce**.

    > [!NOTE]
    > V tomto rychlém startu vytvoříte **téma** a **předplatné** tématu pomocí **souboru Node.js.** 

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js
Vytvořte prázdnou aplikaci Node.js. Pokyny k vytvoření aplikace Node.js najdete v [tématu Vytvoření a nasazení aplikace Node.js na web Azure], [Cloudovou službu Node.js][Node.js Cloud Service] pomocí prostředí Windows PowerShell nebo web ovou stránku s WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití služby Service Bus
Chcete-li použít service bus, stáhněte si balíček Azure Node.js. Tento balíček obsahuje sadu knihoven, které komunikují se službami SERVICE Bus REST.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>K získání balíčku použijte Správce balíčků uzlů (NPM).
1. Otevřete rozhraní příkazového řádku, například **PowerShell** (Windows), **Terminál** (Mac) nebo **Bash** (Unix).
2. Přejděte do složky, do které jste vytvořili ukázkovou aplikaci.
3. Zadejte **npm nainstalovat azure** v příkazovém okně, což by mělo mít za následek následující výstup:

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
3. Příkaz **LS** můžete spustit ručně a ověřit, zda byla vytvořena složka **modulů\_uzlů.** Uvnitř této složky najděte balíček **azure,** který obsahuje knihovny, které potřebujete pro přístup k tématům service bus.

### <a name="import-the-module"></a>Import modulu
Pomocí poznámkového bloku nebo jiného textového editoru přidejte do horní části souboru **server.js** aplikace následující:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Nastavení připojení služby Service Bus
Modul Azure přečte `AZURE_SERVICEBUS_CONNECTION_STRING` proměnnou prostředí pro připojovací řetězec, který jste získali z předchozího kroku "Získat přihlašovací údaje.". Pokud tato proměnná prostředí není nastavena, je `createServiceBusService`nutné zadat informace o účtu při volání .

Příklad nastavení proměnných prostředí pro cloudovou službu Azure najdete v tématu [Nastavení proměnných prostředí](../container-instances/container-instances-environment-variables.md#azure-cli-example).



## <a name="create-a-topic"></a>Vytvoření tématu
Objekt **ServiceBusService** umožňuje pracovat s tématy. Následující kód vytvoří objekt **ServiceBusService.** Přidejte jej v horní části souboru **server.js,** po příkazu k importu modulu azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Pokud voláte `createTopicIfNotExists` na **Objekt ServiceBusService,** zadané téma je vrácena (pokud existuje) nebo nové téma se zadaným názvem je vytvořen. Následující kód `createTopicIfNotExists` používá k vytvoření nebo `MyTopic`připojení k tématu s názvem :

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

Metoda `createTopicIfNotExists` také podporuje další možnosti, které umožňují přepsat výchozí nastavení tématu, jako je například čas zprávy na živé nebo maximální velikost tématu. 

Následující příklad nastaví maximální velikost tématu na 5 GB s časem žít jednu minutu:

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
Volitelné filtrování operace lze použít na operace prováděné pomocí **ServiceBusService**. Filtrování operace mohou zahrnovat protokolování, automatické opakování, atd. Filtry jsou objekty, které implementují metodu s podpisem:

```javascript
function handle (requestOptions, next)
```

Po provedení předběžného zpracování na možnosti `next`požadavku, metoda volá a předá zpětné volání s následujícím podpisem:

```javascript
function (returnObject, finalCallback, next)
```

V tomto zpětném volání `returnObject` a po zpracování (odpověď z požadavku na server), zpětné volání musí buď vyvolat další (pokud existuje) pokračovat ve zpracování jiných filtrů nebo vyvolat `finalCallback` ukončení vyvolání služby vyvolání.

Sada Azure SDK pro Node.js obsahuje dva filtry, které implementují logiku opakování: **ExponentialRetryPolicyFilter** a **LinearRetryPolicyFilter**. Následující kód vytvoří objekt **ServiceBusService,** který používá **exponencialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Vytvoření předplatných
Předplatná tématu jsou také vytvořena pomocí objektu **ServiceBusService.** Odběry jsou pojmenovány a může mít volitelný filtr, který omezuje sadu zpráv doručované do virtuální fronty předplatného.

> [!NOTE]
> Ve výchozím nastavení jsou odběry trvalé, dokud nejsou odstraněny buď oni, nebo téma, které jsou přidruženy. Pokud vaše aplikace obsahuje logiku pro vytvoření předplatného, měla by `getSubscription` nejprve zkontrolovat, zda předplatné existuje pomocí metody.
>
> Odběry můžete automaticky odstranit nastavením [vlastnosti AutoDeleteOnIdle](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Vytvoření odběru s výchozím filtrem (MatchAll).
Filtr **MatchAll** je výchozí filtr používaný při vytvoření předplatného. Když použijete filtr **MatchAll**, všechny zprávy publikované do tématu se umístí do virtuální fronty odběru. Následující příklad vytvoří odběr s názvem AllMessages a používá výchozí **MatchAll** filtr.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Vytvoření odběru s filtry
Můžete také vytvořit filtry, které umožňují obor, které zprávy odeslané na téma by se měly zobrazovat v rámci předplatného konkrétního tématu.

Nejflexibilnějším typem filtru podporovaného odběry je **sqlfilter**, který implementuje podmnožinu SQL92. Filtry SQL pracují s vlastnostmi zpráv publikované do tématu. Další podrobnosti o výrazech, které lze použít s filtrem SQL, zkontrolujte syntaxi [SqlFilter.SqlExpression.][SqlFilter.SqlExpression]

Filtry lze přidat do předplatného `createRule` pomocí metody **ServiceBusService** objektu. Tato metoda umožňuje přidat nové filtry do existujícího předplatného.

> [!NOTE]
> Vzhledem k tomu, že výchozí filtr je použit automaticky pro všechny nové odběry, musíte nejprve odebrat výchozí filtr nebo **MatchAll** přepíše všechny ostatní filtry, které zadáte. Výchozí pravidlo můžete odebrat `deleteRule` pomocí metody objektu **ServiceBusService.**
>
>

Následující příklad vytvoří odběr `HighMessages` s názvem **SqlFilter,** který vybere `messagenumber` pouze zprávy, které mají vlastní vlastnost větší než 3:

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

Podobně následující příklad vytvoří odběr `LowMessages` s názvem **sqlfilter,** který vybere `messagenumber` pouze zprávy, které mají vlastnost menší nebo rovno 3:

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

Když je zpráva nyní `MyTopic`odeslána , je doručena `AllMessages` příjemcům, kteří se přihlásili k odběru `HighMessages` `LowMessages` tématu, a selektivně doručena příjemcům, kteří se přihlásili k odběru a téma (v závislosti na obsahu zprávy).

## <a name="how-to-send-messages-to-a-topic"></a>Jak odesílat zprávy k tématu
Chcete-li odeslat zprávu do tématu služby Service Bus, musí aplikace použít `sendTopicMessage` metodu objektu **ServiceBusService.**
Zprávy odeslané do témat service bus jsou **BrokeredMessage** objekty.
**BrokeredMessage** objekty mají sadu standardnívlastnosti (například `Label` a `TimeToLive`), slovník, který se používá k uložení vlastní vlastnosti specifické pro aplikaci a tělo dat řetězce. Aplikace můžete nastavit text zprávy předáním hodnotu `sendTopicMessage` řetězce a všechny požadované standardní vlastnosti jsou naplněny výchozí hodnoty.

Následující příklad ukazuje, jak odeslat `MyTopic`pět testovacích zpráv do aplikace . Hodnota `messagenumber` vlastnosti každé zprávy se liší na iteraci smyčky (tato vlastnost určuje, které odběry přijímat):

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

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv uchovávaných v tématu není nijak omezen, ale je omezena celková velikost zpráv, které jsou v tématu v držení. Velikost tématu se definuje při vytvoření, maximální limit je 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Příjem zpráv z předplatného
Zprávy jsou přijímány z `receiveSubscriptionMessage` předplatného pomocí metody na **ServiceBusService** objektu. Ve výchozím nastavení jsou zprávy odstraněny z předplatného při jejich čtení. Můžete však nastavit volitelný `isPeekLock` parametr **true** číst (náhled) a zamknout zprávu bez odstranění z předplatného.

Výchozí chování čtení a odstranění zprávy jako součást operace příjmu je nejjednodušší model a funguje nejlépe pro scénáře, ve kterých aplikace může tolerovat není zpracování zprávy, pokud dojde k selhání. Chcete-li pochopit toto chování, zvažte scénář, ve kterém spotřebitel vydá požadavek na přijetí a pak dojde k chybě před jeho zpracováním. Vzhledem k tomu, že service bus označil zprávu jako spotřebované, pak při restartování aplikace a začne spotřebovávat zprávy znovu, má zmeškané zprávy, která byla spotřebována před selhání.

Pokud `isPeekLock` je parametr nastaven na **hodnotu true**, příjem se stane dvoustupňovou operací, která umožňuje podporovat aplikace, které nemohou tolerovat zmeškané zprávy. Když service bus obdrží požadavek, najde další zprávu spotřebovávat, uzamkne ji zabránit ostatním spotřebitelům v přijetí a vrátí ji do aplikace.
Poté, co aplikace zpracuje zprávu (nebo ji spolehlivě uloží pro budoucí zpracování), dokončí druhou fázi procesu příjmu voláním metody **deleteMessage** a předá zprávu, kterou chcete odstranit jako parametr. **DeleteMessage** Metoda označí zprávu jako spotřebované a odebere ji z předplatného.

Následující příklad ukazuje, jak mohou být zprávy `receiveSubscriptionMessage`přijímány a zpracovávány pomocí . Příklad nejprve obdrží a odstraní zprávu z předplatného "LowMessages" a potom obdrží zprávu z `isPeekLock` předplatného "HighMessages" pomocí nastavena na hodnotu true. Poté zprávu odstraní pomocí `deleteMessage`:

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
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce není z nějakého důvodu schopna zprávu `unlockMessage` zpracovat, může volat metodu v objektu **ServiceBusService.** Tato metoda způsobí, že Service Bus odemknout zprávu v rámci předplatného a zpřístupnit pro přijetí znovu. V tomto případě buď stejnou náročnou aplikací nebo jinou náročnou aplikací.

K dispozici je také časový čas přidružený ke zprávě zamčené v rámci předplatného. Pokud aplikace nepodaří zpracovat zprávu před vypršením časového limitu uzamčení (například pokud dojde k chybě aplikace), pak Service Bus automaticky odemkne zprávu a zpřístupní ji k přijetí znovu.

V případě, že aplikace dojde k chybě `deleteMessage` po zpracování zprávy, ale před voláním metody je zpráva znovu doručena do aplikace při restartování. Toto chování *se*často nazývá alespoň jednou zpracování . To znamená, že každá zpráva je zpracována alespoň jednou, ale v určitých situacích může být stejná zpráva znovu doručena. Pokud scénář nemůže tolerovat duplicitní zpracování, měli byste přidat logiku do aplikace pro zpracování duplikátní zprávy doručení. Můžete použít **MessageId** vlastnost zprávy, která zůstává konstantní napříč pokusy o doručení.

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů
Témata a odběry jsou trvalé, pokud není [nastavena vlastnost autoDeleteOnIdle](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle) a musí být explicitně odstraněna buď prostřednictvím [portálu Azure][Azure portal] nebo programově.
Následující příklad ukazuje, jak odstranit `MyTopic`téma s názvem :

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Pokud se odstraní téma, odstraní se i všechny odběry registrované k tomuto tématu. Odběry se taky dají odstranit samostatně. Následující příklad ukazuje, jak odstranit `HighMessages` předplatné `MyTopic` pojmenované z tématu:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

> [!NOTE]
> Prostředky služby Service Bus můžete spravovat pomocí [aplikace Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Průzkumník služby Service Bus umožňuje uživatelům připojit se k oboru názvů service bus a snadno spravovat entity zasílání zpráv. Nástroj poskytuje pokročilé funkce, jako je funkce importu a exportu nebo možnost testovat téma, fronty, předplatná, přenosové služby, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Nyní, když jste se naučili základy témat service bus, postupujte podle těchto odkazů se dozvíte více.

* Viz [Fronty, témata a odběry][Queues, topics, and subscriptions].
* Reference pro API pro [SqlFilter][SqlFilter].
* Navštivte [azure sdk pro][Azure SDK for Node] úložiště uzlů na GitHubu.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /javascript/api/@azure/arm-servicebus/sqlfilter?view=azure-node-latest
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Vytvoření a nasazení aplikace Node.js na web Azure]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

