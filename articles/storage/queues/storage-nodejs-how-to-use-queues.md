---
title: "Používání úložiště Queue z Node.js | Microsoft Docs"
description: "Naučte se používat službu front Azure k vytváření a odstraňování front a vložit, získání a odstranění zprávy. Ukázky napsané v Node.js."
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
ms.openlocfilehash: 2565f56324a070368c499a62ab54bb98830d8c20
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="how-to-use-queue-storage-from-nodejs"></a>Používání úložiště Queue z Node.js
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Přehled
Tento průvodce vám ukáže, jak provádět běžné scénáře pomocí služby Microsoft Azure Queue. Ukázky jsou zapsány pomocí rozhraní API Node.js. Pokryté scénáře zahrnují **vkládání**, **prohlížení**, **získávání**, a **odstraňování** fronty zpráv, a také **vytváření a odstraňování front**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js
Vytvoření prázdné aplikace Node.js. Pokyny k vytvoření aplikace Node.js najdete v tématu [vytvoření webové aplikace Node.js ve službě Azure App Service](../../app-service/app-service-web-get-started-nodejs.md), [sestavení a nasazení aplikace Node.js ve službě Azure Cloud Service](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) pomocí prostředí Windows PowerShell nebo [ Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Konfigurace aplikace pro přístup k úložišti
Pokud chcete používat úložiště Azure, musíte sady SDK úložiště Azure pro platformu Node.js, která obsahuje sadu knihoven pohodlí, které komunikují s služby REST úložiště.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Získat balíček pomocí uzlu balíček správce (NPM)
1. Pomocí rozhraní příkazového řádku, jako například **prostředí PowerShell** (Windows) **Terminálové** (Mac), nebo **Bash** (Unix), přejděte do složky, které jste vytvořili ukázkové aplikaci.
2. Typ **npm nainstalujte azure-storage** v příkazovém okně. Výstup z tohoto příkazu se podobně jako v následujícím příkladu.
 
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

3. Můžete ručně spustit **ls** příkazu ověřte, že **uzlu\_moduly** složka byla vytvořena. Uvnitř této složky najdete **azure-storage** balíček, který obsahuje knihovny, je třeba získat přístup k úložišti.

### <a name="import-the-package"></a>Import balíčku
Pomocí programu Poznámkový blok nebo jiném textovém editoru, přidejte následující horní **server.js** souboru aplikace, které máte v úmyslu používat úložiště:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Nastavit připojení k úložišti Azure
Modul azure, bude číst proměnné prostředí AZURE\_úložiště\_účet a AZURE\_úložiště\_přístup\_klíč nebo AZURE\_úložiště\_připojení\_řetězec informace požadované pro připojení k účtu úložiště Azure. Pokud nejsou nastavené těchto proměnných prostředí, musíte zadat informace o účtu při volání metody **createQueueService**.

Příklad nastavení proměnných prostředí [portálu Azure](https://portal.azure.com) web Azure, najdete v části [webové aplikace Node.js pomocí služby Azure Table](../../cosmos-db/table-storage-cloud-service-nodejs.md).

## <a name="how-to-create-a-queue"></a>Postupy: Vytvoření fronty
Následující kód vytvoří **QueueService** objekt, který umožňuje pracovat s fronty.

```javascript
var queueSvc = azure.createQueueService();
```

Použití **createQueueIfNotExists** metoda, která vrátí zadanou frontu, pokud již existuje nebo vytvoří novou frontu se zadaným názvem, pokud ještě neexistuje.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Pokud je vytvářena fronta, `result.created` hodnotu true. Pokud existuje fronty, `result.created` je false.

### <a name="filters"></a>Filtry
Volitelné filtrování operace lze použít pro operace provedené pomocí **QueueService**. Filtrování operací může zahrnovat protokolování, automatickým opakovaným pokusem o atd. Filtry jsou objekty, které implementovat metodu s podpisem:

```javascript
function handle (requestOptions, next)
```

Až to předzpracování na žádost o možnostech, potřebuje volat "Další" předání zpětné volání podpisem následující metody:

```javascript
function (returnObject, finalCallback, next)
```

V této zpětného volání a po zpracování returnObject (odpovědi požadavek na server) zpětné volání je potřeba buď vyvolat další, pokud existuje pokračovat ve zpracovávání ostatní filtry nebo jednoduše vyvolat finalCallback jinak skončili volání služby.

Dva filtry, které implementují logiku opakovaných pokusů, které jsou součástí sady Azure SDK pro Node.js, **ExponentialRetryPolicyFilter** a **LinearRetryPolicyFilter**. Vytvoří následující **QueueService** objekt, který používá **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Postupy: Vložit zprávu do fronty
Chcete-li vložit zprávu do fronty, použijte **createMessage** metodu pro vytvoření nové zprávy a přidejte ji do fronty.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Postupy: Zobrazení náhledu další zprávy
Můžete prohlížet zprávy ve frontě bez odebere ji z fronty voláním **peekMessages** metoda. Ve výchozím nastavení **peekMessages** prohlédne do jedné zprávy.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result` Obsahuje zprávu.

> [!NOTE]
> Pomocí **peekMessages** Pokud nejsou žádné zprávy ve frontě nebude vrátí chybu, ale žádné zprávy budou vráceny.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Postupy: Dequeue – další zprávy
Zpracování zprávy je dvoustupňový proces:

1. Dequeue – zpráva.
2. Tuto zprávu odstraníte.

Chcete-li dequeue – zprávu, použijte **getMessages**. Díky tomu zprávy neviditelná ve frontě, takže je může zpracovat žádné další klienty. Po zpracování zprávy aplikace volání **deleteMessage** odstranění z fronty. Následující příklad získá zprávu, a pak ji odstraní:

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
> Ve výchozím nastavení je zprávu pouze skryto 30 sekund, po kterých je viditelná pro ostatní klienty. Můžete zadat jinou hodnotu pomocí `options.visibilityTimeout` s **getMessages**.
> 
> [!NOTE]
> Pomocí **getMessages** Pokud nejsou žádné zprávy ve frontě nebude vrátí chybu, ale žádné zprávy budou vráceny.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postupy: Změna obsahu zpráv zařazených ve frontě
Můžete změnit obsah zprávy přímo do fronty pomocí **updateMessage**. Následující příklad aktualizací text zprávy:

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

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Postupy: Další možnosti pro vyřazení zprávy
Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby:

* `options.numOfMessages` -Načíst dávku zpráv (až 32.)
* `options.visibilityTimeout` -Nastavte časový limit neviditelnosti delší nebo kratší.

Následující příklad používá **getMessages** metoda získat 15 zpráv v jednom volání. Pak se každá zpráva zpracuje pomocí pro smyčky. Také nastaví časový limit neviditelnosti 5 minut pro všechny zprávy vrácená touto metodou.

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
Chcete-li načíst seznam front, použijte **listQueuesSegmented**. Chcete-li načíst seznam filtrovat podle konkrétní předpony, použijte **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Pokud nelze vrátit všechny fronty, `result.continuationToken` lze použít jako první parametr **listQueuesSegmented** nebo druhý parametr **listQueuesSegmentedWithPrefix** k načtení více výsledků.

## <a name="how-to-delete-a-queue"></a>Postupy: Odstranění fronty
Chcete-li odstranit frontu se všemi zprávami, které v ní, zavolejte **deleteQueue** metoda pro objekt fronty.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Pokud chcete vymazat všechny zprávy z fronty bez odstranění, použijte **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Postupy: práce s podpisy sdíleného přístupu
Podpisy sdíleného přístupu (SAS) jsou zabezpečené způsob, jak poskytnout podrobné přístup k frontám bez zadání názvu účtu úložiště nebo klíče. SAS se často používá k poskytování omezený přístup k vaší front, například povolení mobilní aplikace k odeslání zprávy.

Důvěryhodné aplikace, jako je Cloudová služba vygeneruje SAS pomocí **generateSharedAccessSignature** z **QueueService**a poskytuje k nedůvěryhodné nebo částečně důvěryhodné aplikace. Například mobilní aplikace. SAS je generována pomocí zásad, která popisuje počátečním a koncovým datem, během které SAS je platný, a také úroveň přístupu k majiteli SAS.

Následující příklad vytvoří nové zásady sdíleného přístupu, který vám umožní držitele SAS přidat zprávy do fronty a vyprší platnost 100 minut od okamžiku, kdy je vytvořena.

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

Všimněte si, že informace o hostiteli je třeba zadat také, jako je povinný, když se pokusí přistupovat ke frontě držitele SAS.

Klientská aplikace pak používá SAS s **QueueServiceWithSAS** k provedení operace u fronty. Následující příklad se připojí do fronty a vytvoří zprávu.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Vzhledem k tomu, že SAS se vygeneroval s přidat přístup, pokud byly proveden pokus o čtení, aktualizaci nebo odstranění zprávy, bude vrácena chyba.

### <a name="access-control-lists"></a>Seznamy řízení přístupu
Seznam řízení přístupu (ACL) můžete také nastavit zásady přístupu pro SAS. To je užitečné, pokud chcete povolit více klientům přistupovat ke frontě, ale poskytnutí zásad, jiný přístup pro každého klienta.

Seznam ACL je implementovaná pomocí pole zásady přístupu s ID spojené s každou zásadu. V následujícím příkladu definuje dvě zásady; jeden pro "uživatel1" a jeden pro, uživatel2":

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

Následující příklad načte aktuální seznam ACL pro **Moje_fronta**, pak přidá nové zásady pomocí **setQueueAcl**. Tento přístup umožňuje:

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

Jakmile je nastavená seznamu ACL, potom můžete vytvořit SAS na základě ID pro zásadu. Následující příklad vytvoří nový SAS pro, uživatel2":

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy používání služby queue storage, postupujte podle následujících odkazech na další informace o složitějších úlohách úložiště.

* Přejděte [Blog týmu Azure Storage][Azure Storage Team Blog].
* Přejděte [sada SDK úložiště Azure pro uzel] [ Azure Storage SDK for Node] úložišti na Githubu.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Vytvoření webové aplikace Node.js ve službě Azure App Service](../../app-service/app-service-web-get-started-nodejs.md)

[Vytvoření a nasazení aplikace Node.js do Azure Cloud Service](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
