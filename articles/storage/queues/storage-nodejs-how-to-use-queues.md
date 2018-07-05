---
title: Postup používání úložiště Queue z Node.js | Dokumentace Microsoftu
description: Zjistěte, jak pomocí služby front Azure můžete vytvářet a odstraňovat fronty a vložit, získání a odstranění zprávy. Ukázky napsané v Node.js.
services: storage
documentationcenter: nodejs
author: craigshoemaker
manager: jeconnoc
editor: tysonn
ms.assetid: a8a92db0-4333-43dd-a116-28b3147ea401
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: cshoe
ms.openlocfilehash: c19ac5f45504e3c19b0b300dbc3ea157861d05ed
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2018
ms.locfileid: "34653021"
---
# <a name="how-to-use-queue-storage-from-nodejs"></a>Používání úložiště Queue z Node.js
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Přehled
Tento průvodce vám ukáže, jak provádět běžné scénáře pomocí služby Microsoft Azure Queue. Ukázky jsou napsané pomocí rozhraní API Node.js. Mezi popsané scénáře patří **vkládání**, **prohlížení**, **získávání**, a **odstranění** fronty zpráv, stejně jako  **vytváření a odstraňování front**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace v Node.js
Vytvoření prázdné aplikace v Node.js. Pokyny k vytvoření aplikace Node.js najdete v tématu [vytvoření webové aplikace Node.js ve službě Azure App Service](../../app-service/app-service-web-get-started-nodejs.md), [sestavit a nasadit aplikaci Node.js do Azure Cloud Service](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) pomocí Windows Powershellu nebo [ Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Konfigurace aplikace pro přístup ke službě Storage
Použití služby Azure storage, budete potřebovat Azure Storage SDK pro Node.js, který obsahuje sadu knihoven pohodlí, které komunikují se službami REST úložiště.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Získat balíček pomocí Node Package Manager (NPM)
1. Pomocí rozhraní příkazového řádku, jako **PowerShell** (Windows), **terminálu** (Mac), nebo **Bash** (Unix), přejděte do složky, ve které jste vytvořili ukázkovou aplikaci.
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

3. Můžete ručně spustit **ls** příkazu ověřte, že **uzel\_moduly** složka byla vytvořena. Uvnitř této složky najdete balíček **azure-storage** obsahující knihovny, které potřebujete pro přístup k úložišti.

### <a name="import-the-package"></a>Import balíčku
Pomocí poznámkového bloku nebo jiného textového editoru, přidejte následující do horní části **server.js** souboru aplikace, kde máte v úmyslu používat úložiště:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Nastavit připojení k Azure Storage
Modul azure načte proměnné prostředí AZURE\_úložiště\_účet a AZURE\_úložiště\_přístup\_klíč nebo AZURE\_úložiště\_připojení\_ ŘETĚZEC informace požadované pro připojení k účtu služby Azure storage. Pokud nejsou nastavené tyto proměnné prostředí, musíte zadat informace o účtu při volání metody **createQueueService**.

## <a name="how-to-create-a-queue"></a>Postupy: Vytvoření fronty
Následující kód vytvoří **QueueService** objektu, který umožňuje pracovat s frontami.

```javascript
var queueSvc = azure.createQueueService();
```

Použití **createQueueIfNotExists** metoda, která vrátí zadanou frontu, pokud již existuje, nebo vytvoří novou frontu se zadaným názvem, pokud ještě neexistuje.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Pokud se vytvoří frontu, `result.created` má hodnotu true. Pokud existuje fronta `result.created` má hodnotu false.

### <a name="filters"></a>Filtry
Volitelné filtrování operace lze použít u operací provedených pomocí **QueueService**. Filtrování operací může zahrnovat protokolování, automatickým opakovaným pokusem o atd. Filtry jsou objekty, které implementují metodu s podpisem:

```javascript
function handle (requestOptions, next)
```

Po provedení předzpracování na možnosti žádosti, metoda musí volat "následující" předání zpětného volání s následující signaturou:

```javascript
function (returnObject, finalCallback, next)
```

V tomto zpětném volání a po zpracování returnObject (odpovědi z požadavku na server) je potřeba zpětné volání vyvolat další, pokud existuje pokračovat ve zpracování další filtry nebo jednoduše vyvolat finalCallback jinak skončit služby vyvolání.

Sada Azure SDK pro Node.js obsahuje dva filtry, které implementují logiku opakování: **ExponentialRetryPolicyFilter** a **LinearRetryPolicyFilter**. Vytvoří následující **QueueService** objekt, který se používá **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Postupy: Vložit zprávu do fronty
Chcete-li vložit zprávu do fronty, použijte **createMessage** metoda vytvořit novou zprávu a přidat do fronty.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Postupy: Zobrazení náhledu další zprávy
Můžete prohlížet zprávy ve frontě bez odebrání z fronty pomocí volání **peekMessages** metody. Ve výchozím nastavení **peekMessages** prohlédne do jedné zprávy.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result` Obsahuje zprávu.

> [!NOTE]
> Pomocí **peekMessages** při nejsou žádné zprávy ve frontě se vrátí chybu, ale nevrátí se žádné zprávy.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Postupy: Odstranění z fronty další zprávy
Zpracování zprávy je dvoustupňový proces:

1. Odstranit zprávu z fronty.
2. Odstranění zprávy.

Chcete-li odstranit zprávu z fronty, použijte **getMessages**. Díky tomu zprávy ve frontě, neviditelné tak, že žádní klienti mohou je zpracovávat. Po zpracování zprávy aplikace volat **deleteMessage** k jeho odstranění z fronty. Následující příklad získá zprávu a potom ji odstraní:

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
> Ve výchozím nastavení je zpráva skryté jen po dobu 30 sekund, po jejichž uplynutí je viditelné pro ostatní klienty. Můžete zadat jinou hodnotu s použitím `options.visibilityTimeout` s **getMessages**.
> 
> [!NOTE]
> Pomocí **getMessages** při nejsou žádné zprávy ve frontě se vrátí chybu, ale nevrátí se žádné zprávy.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postupy: Změna obsahu zpráv zařazených ve frontě
Můžete změnit obsah zprávu na místě ve frontě pomocí **updateMessage**. Následující příklad aktualizuje text zprávy:

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

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Postupy: Dalších možností pro vyřazování z fronty zpráv
Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby:

* `options.numOfMessages` -Načíst dávku zpráv (až 32.)
* `options.visibilityTimeout` -Nastavte časový limit neviditelnosti delší nebo kratší.

V následujícím příkladu **getMessages** metodu k získání 15 zpráv v jednom volání. Pak se každá zpráva zpracuje pomocí smyčky for. Také nastaví časový limit neviditelnosti 5 minut pro všechny zprávy vrácený touto metodou.

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

## <a name="how-to-get-the-queue-length"></a>Postupy: Získání délky fronty
**GetQueueMetadata** vrací metadata o frontě, včetně přibližný počet zpráv čekajících ve frontě.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Postupy: Seznam fronty
Pokud chcete načíst seznam front, použijte **listQueuesSegmented**. Chcete-li načíst seznam filtrovaný podle konkrétní předpony, použijte **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Pokud nelze vrátit všechny fronty, `result.continuationToken` může sloužit jako první parametr **listQueuesSegmented** nebo druhý parametr **listQueuesSegmentedWithPrefix** načíst více výsledků.

## <a name="how-to-delete-a-queue"></a>Postupy: Odstranění fronty
Chcete-li odstranit frontu se všemi zprávami, které v ní, zavolejte **deleteQueue** metodu na objekt fronty.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Chcete-li vymazat všechny zprávy z fronty, aniž by byl, použijte **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Postupy: práce s podpisy sdíleného přístupu
Sdílených přístupových podpisů (SAS) je zabezpečený způsob, jak poskytovat granulární přístup k frontám bez zadání názvu účtu úložiště nebo klíče. SAS se často používají k zajištění omezený přístup k vaší front, například povolení mobilní aplikace pro odesílání zpráv.

Důvěryhodné aplikace, jako je Cloudová služba vygeneruje SAS pomocí **generateSharedAccessSignature** z **QueueService**a poskytuje k nedůvěryhodné nebo částečně důvěryhodnou aplikaci. Například mobilní aplikace. SAS se generuje pomocí zásady, která popisuje počáteční a koncové datum platnosti SAS a také úroveň přístupu udělenou držiteli SAS.

Následující příklad vytvoří nové zásady sdíleného přístupu, které vám umožní držitel SAS a přidává zprávy do fronty a vyprší platnost 100 minut po času vytvoření.

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

Všimněte si, že informace o hostiteli musí být uvedeny také, že je vyžadován vlastník SAS pokusí získat přístup k frontě.

Klientské aplikace pak pomocí SAS s **QueueServiceWithSAS** o provedení operací k frontě. Následující příklad připojí do fronty a vytvoří zprávu.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Protože sdílený přístupový podpis vygeneroval s přidat přístup, pokud byl proveden pokus o čtení, aktualizace nebo odstranění zprávy, bude vrácena chyba.

### <a name="access-control-lists"></a>Seznamy řízení přístupu
K nastavení zásad přístupu pro SAS můžete použít také seznam řízení přístupu (ACL). To je užitečné, pokud chcete povolit více klientům přístup k frontě, ale poskytnout různá přístupová zásady pro každého klienta.

Seznam ACL se implementuje pomocí pole zásad přístupu, z nichž každá zásada má přidružené ID. Následující příklad definuje dvě zásady; jeden pro "uživatel1" a jeden pro "uživatel2":

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

Následující příklad získá aktuální seznam ACL pro **myqueue**, pak přidá nové zásady pomocí **setQueueAcl**. Tento přístup umožňuje:

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

Po nastavení seznamu ACL, si můžete vytvořit na základě ID pro zásady SAS. Následující příklad vytvoří nový SAS pro uživatele user2:

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy používání služby queue storage, použijte tyto odkazy na další informace o složitějších úlohách úložiště.

* Přejděte [Blog týmu Azure Storage][Azure Storage Team Blog].
* Přejděte [sadu SDK služby Azure Storage pro uzel] [ Azure Storage SDK for Node] úložišti na Githubu.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Vytvoření webové aplikace Node.js ve službě Azure App Service](../../app-service/app-service-web-get-started-nodejs.md)

[Vytvoření a nasazení aplikace Node.js do Azure Cloud Service](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
