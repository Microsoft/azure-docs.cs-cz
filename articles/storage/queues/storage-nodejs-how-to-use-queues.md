---
title: Použití Azure Queue Storage z Node.js-Azure Storage
description: Naučte se používat Služba front Azure k vytváření a odstraňování front a vkládání, získávání a odstraňování zpráv. Ukázky napsané v Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: seo-javascript-september2019, devx-track-javascript
ms.openlocfilehash: 950d85f391504095ceaaa10996145ca832b6db19
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87432734"
---
# <a name="use-azure-queue-service-to-create-and-delete-queues-from-nodejs"></a>Použití služby Azure Queue Service k vytváření a odstraňování front z Node.js
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Přehled
V této příručce se dozvíte, jak provádět běžné scénáře pomocí Služba front Microsoft Azure. Ukázky se napíší pomocí rozhraní Node.js API. Mezi zahrnuté scénáře patří **vkládání**, **prohlížení**, **získávání**a **odstraňování** zpráv fronty a **vytváření a odstraňování front**.

> [!IMPORTANT]
> Tento článek odkazuje na starší verzi Azure Storage klientské knihovny pro JavaScript. Pokud chcete začít používat nejnovější verzi, přečtěte si [rychlý Start: Klientská knihovna Azure Queue Storage pro JavaScript.](storage-quickstart-queues-nodejs.md)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js
Vytvořte prázdnou Node.jsovou aplikaci. Pokyny k vytvoření Node.js aplikace najdete v tématu [Vytvoření webové aplikace v Node.js v Azure App Service](../../app-service/app-service-web-get-started-nodejs.md), [sestavení a nasazení Node.js aplikace do cloudové služby Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) pomocí Windows PowerShellu nebo [Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Konfigurace aplikace pro přístup k úložišti
Pokud chcete používat službu Azure Storage, potřebujete sadu Azure Storage SDK pro Node.js, která zahrnuje sadu praktických knihoven, které komunikují se službou REST (Storage).

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>K získání balíčku použijte Správce balíčků Node (NPM).
1. Použijte rozhraní příkazového řádku, například **PowerShell** (Windows,) **Terminal** (Mac,) nebo **bash** (UNIX), přejděte do složky, ve které jste vytvořili ukázkovou aplikaci.
2. Do příkazového okna zadejte **npm install azure-storage**. Výstup příkazu je podobný následujícímu příkladu.
 
    ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    ```

3. Můžete ručně spustit příkaz **ls** a ověřit tak, že se vytvořila složka ** \_ modulů uzlů** . Uvnitř této složky najdete balíček **azure-storage** obsahující knihovny, které potřebujete pro přístup k úložišti.

### <a name="import-the-package"></a>Import balíčku
Pomocí poznámkového bloku nebo jiného textového editoru přidejte do horní části **server.js** souboru aplikace, ve které chcete úložiště použít, následující:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Nastavení Azure Storageho připojení
Modul Azure přečte proměnnou prostředí \_ účet úložiště Azure \_ a přístupový klíč úložiště \_ Azure \_ \_ nebo \_ \_ připojovací řetězec úložiště Azure \_ pro informace požadované pro připojení k vašemu účtu úložiště Azure. Pokud tyto proměnné prostředí nejsou nastaveny, při volání **createQueueService**je nutné zadat informace o účtu.

## <a name="how-to-create-a-queue"></a>Postupy: vytvoření fronty
Následující kód vytvoří objekt **QueueService** , který vám umožní pracovat s frontami.

```javascript
var queueSvc = azure.createQueueService();
```

Použijte metodu **createQueueIfNotExists** , která vrátí zadanou frontu, pokud již existuje, nebo vytvoří novou frontu se zadaným názvem, pokud ještě neexistuje.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Pokud je fronta vytvořená, `result.created` má hodnotu true. Pokud fronta existuje, `result.created` je false.

### <a name="filters"></a>Filtry
Volitelné operace filtrování lze použít na operace prováděné pomocí **QueueService**. Operace filtrování můžou zahrnovat protokolování, automatické opakování atd. Filtry jsou objekty, které implementují metodu s podpisem:

```javascript
function handle (requestOptions, next)
```

Po provedení předběžného zpracování v možnostech žádosti musí metoda volat "Další" předání zpětného volání s následujícím podpisem:

```javascript
function (returnObject, finalCallback, next)
```

V tomto zpětném volání a po zpracování returnObject (odpověď z požadavku na server) musí zpětné volání vyvolat další, pokud existuje, aby pokračovalo v zpracování jiných filtrů, nebo jednoduše vyvolat finalCallback, aby se ukončilo vyvolání služby.

Sada Azure SDK pro Node.js obsahuje dva filtry, které implementují logiku opakování: **ExponentialRetryPolicyFilter** a **LinearRetryPolicyFilter**. Následující vytvoří objekt **QueueService** , který používá **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Postupy: vložení zprávy do fronty
Chcete-li vložit zprávu do fronty, použijte metodu **createMessage** k vytvoření nové zprávy a přidejte ji do fronty.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Postupy: prohlížení další zprávy
Můžete prohlížet zprávu v přední části fronty bez jejího odebrání z fronty voláním metody **peekMessages** . Ve výchozím nastavení **peekMessages** prohlédne jednu zprávu.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result`Obsahuje zprávu.

> [!NOTE]
> V **případě, že ve** frontě nejsou žádné zprávy, nevrátí se žádná zpráva, ale nevrátí se žádné zprávy.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Postupy: vyřazení další zprávy z fronty
Zpracování zprávy je proces se dvěma fázemi:

1. Vyřadí zprávu do fronty.
2. Odstraňte zprávu.

Chcete-li vyřadit zprávu do fronty, použijte **GetMessages**. Tím se zprávy ve frontě nezobrazují, takže je nemůžou zpracovat žádní další klienti. Jakmile aplikace zpracuje zprávu, zavolá **deleteMessage** , aby ji odstranila z fronty. Následující příklad získá zprávu a pak ji odstraní:

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

> [!NOTE]
> Ve výchozím nastavení je zpráva skrytá jenom po dobu 30 sekund, po jejímž uplynutí se budou zobrazovat ostatním klientům. Pomocí `options.visibilityTimeout` se **GetMessages**můžete zadat jinou hodnotu.
> 
> [!NOTE]
> Pokud ve frontě nejsou žádné zprávy, nevrátí se k chybě **žádná zpráva,** ale nebudou se vracet žádné zprávy.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postupy: Změna obsahu zprávy ve frontě
Můžete změnit obsah zprávy na místě ve frontě pomocí **updateMessage**. Následující příklad aktualizuje text zprávy:

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Postupy: Další možnosti pro vyřazování zpráv z fronty
Existují dva způsoby, jak lze přizpůsobit načtení zprávy z fronty:

* `options.numOfMessages`-Načíst dávku zpráv (až 32.)
* `options.visibilityTimeout`– Nastavte delší nebo kratší časový limit pro neviditelnost.

Následující příklad používá metodu **GetMessages** k získání 15 zpráv v jednom volání. Potom zpracuje každou zprávu pomocí smyčky for. U všech zpráv vrácených touto metodou nastaví také časový limit neviditelnosti na pět minut.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in result){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

## <a name="how-to-get-the-queue-length"></a>Postupy: získání délky fronty
**GetQueueMetadata** vrátí metadata o frontě, včetně přibližného počtu zpráv čekajících ve frontě.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Postupy: výpis front
Chcete-li načíst seznam front, použijte **listQueuesSegmented**. Chcete-li načíst seznam filtrovaný podle konkrétní předpony, použijte **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Pokud nelze vrátit všechny fronty, `result.continuationToken` lze použít jako první parametr **listQueuesSegmented** nebo druhý parametr **listQueuesSegmentedWithPrefix** pro získání dalších výsledků.

## <a name="how-to-delete-a-queue"></a>Postupy: odstranění fronty
Pokud chcete odstranit frontu a všechny zprávy, které jsou v ní obsažené, zavolejte metodu **deleteQueue** u objektu Queue.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Pokud chcete vymazat všechny zprávy z fronty bez jejich odstranění, použijte **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Postupy: práce se sdílenými přístupovými podpisy
Sdílené přístupové podpisy (SAS) představují zabezpečený způsob, jak zajistit podrobný přístup k frontám bez zadání názvu nebo klíčů účtu úložiště. SAS se často používá k zajištění omezeného přístupu k vašim frontám, jako je například umožnění odesílání zpráv do mobilní aplikace.

Důvěryhodná aplikace, jako je cloudová služba, generuje SAS pomocí **GenerateSharedAccessSignature** **QueueService**a poskytuje ji pro nedůvěryhodnou nebo částečně důvěryhodnou aplikaci. Například mobilní aplikace. SAS se generuje pomocí zásady, která popisuje počáteční a koncové datum platnosti SAS a také úroveň přístupu udělenou držiteli SAS.

Následující příklad vygeneruje nové zásady sdíleného přístupu, které umožní držiteli SAS přidat zprávy do fronty a vyprší 100 minut od okamžiku jejího vytvoření.

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};

var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
var host = queueSvc.host;
```

Všimněte si, že informace o hostiteli musí být k dispozici také v případě, že se vyžaduje, když se držitel SAS pokusí o přístup ke frontě.

Klientská aplikace pak pomocí SAS s **QueueServiceWithSAS** provede operace s frontou. Následující příklad se připojí ke frontě a vytvoří zprávu.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Vzhledem k tomu, že se SAS vygeneroval s oprávněním přidat přístup, pokud došlo k pokusu o čtení, aktualizaci nebo odstranění zpráv, vrátí se chyba.

### <a name="access-control-lists"></a>Seznamy řízení přístupu
K nastavení zásad přístupu pro SAS můžete použít také seznam řízení přístupu (ACL). To je užitečné, pokud chcete, aby přístup k frontě umožňovalo více klientů, ale poskytovaly pro každého klienta různé zásady přístupu.

Seznam ACL se implementuje pomocí pole zásad přístupu, z nichž každá zásada má přidružené ID. Následující příklad definuje dvě zásady. jednu pro ' uživatel1 ' a jednu pro ' uživatel2 ':

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

Následující příklad načte aktuální seznam ACL pro **MyQueue**a pak přidá nové zásady pomocí **setQueueAcl**. Tento přístup umožňuje:

```javascript
var extend = require('extend');
queueSvc.getQueueAcl('myqueue', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Po nastavení seznamu ACL můžete vytvořit SAS na základě ID zásady. Následující příklad vytvoří nový SAS pro uživatele user2:

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základní informace o službě Queue Storage, získáte další informace o složitějších úlohách úložiště pomocí těchto odkazů.

* Navštivte [Blog týmu Azure Storage][Azure Storage Team Blog].
* Navštivte [Azure Storage SDK pro úložiště uzlů][Azure Storage SDK for Node] na GitHubu.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Vytvoření webové aplikace Node.js ve službě Azure App Service](../../app-service/app-service-web-get-started-nodejs.md)

[Sestavení a nasazení aplikace Node.js ve službě Azure Cloud Service](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
