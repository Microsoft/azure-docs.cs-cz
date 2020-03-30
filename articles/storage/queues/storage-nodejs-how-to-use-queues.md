---
title: Použití úložiště fronty Azure z souboru Node.js – Azure Storage
description: Zjistěte, jak pomocí služby Azure Queue vytvářet a odstraňovat fronty a vkládat, získat a odstraňovat zprávy. Ukázky napsané v Souboru Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 7abcad03678131668700f5d2c64b9c971081cb89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060940"
---
# <a name="use-azure-queue-service-to-create-and-delete-queues-from-nodejs"></a>Vytvoření a odstranění front z souboru Node.js pomocí služby Front Service Azure
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Přehled
Tato příručka ukazuje, jak provádět běžné scénáře pomocí služby Microsoft Azure Queue. Ukázky jsou zapsány pomocí rozhraní NODE.js API. Zahrnuté scénáře zahrnují **vkládání**, **prohlížení**, **získávání**a **odstranění** zpráv fronty, stejně jako vytváření a **mazání front**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js
Vytvořte prázdnou aplikaci Node.js. Pokyny k vytvoření aplikace Node.js najdete [v tématu Vytvoření webové aplikace Node.js ve službě Azure App Service](../../app-service/app-service-web-get-started-nodejs.md), sestavení a nasazení aplikace [Node.js do cloudové služby Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) pomocí prostředí Windows PowerShell nebo visual studio [code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Konfigurace aplikace pro přístup k úložišti
Chcete-li používat úložiště Azure, potřebujete sadu Azure Storage SDK pro Node.js, která zahrnuje sadu knihoven pohodlí, které komunikují se službami REST úložiště.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>K získání balíčku použijte Správce balíčků uzlů (NPM).
1. Použijte rozhraní příkazového řádku, jako je **PowerShell** (Windows),) **Terminál** (Mac) nebo **Bash** (Unix), přejděte do složky, do které jste vytvořili ukázkovou aplikaci.
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

3. Příkaz **LS** můžete spustit ručně a ověřit, zda byla vytvořena složka **modulů\_uzlů.** Uvnitř této složky najdete balíček **azure-storage** obsahující knihovny, které potřebujete pro přístup k úložišti.

### <a name="import-the-package"></a>Import balíčku
Pomocí poznámkového bloku nebo jiného textového editoru přidejte do horní části soubor **server.js** aplikace, ve které chcete použít úložiště:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Nastavení připojení úložiště Azure
Modul Azure bude číst proměnné\_prostředí\_AZURE\_STORAGE\_\_ACCOUNT a\_AZURE\_\_STORAGE ACCESS KEY nebo AZURE STORAGE CONNECTION STRING pro informace potřebné pro připojení k účtu úložiště Azure. Pokud tyto proměnné prostředí nejsou nastaveny, je nutné zadat informace o účtu při volání **createQueueService**.

## <a name="how-to-create-a-queue"></a>Postup: Vytvoření fronty
Následující kód vytvoří objekt **QueueService,** který umožňuje pracovat s frontami.

```javascript
var queueSvc = azure.createQueueService();
```

Použijte metodu **createQueueIfNotExists,** která vrátí zadanou frontu, pokud již existuje, nebo vytvoří novou frontu se zadaným názvem, pokud již neexistuje.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Pokud je fronta `result.created` vytvořena, je true. Pokud fronta existuje, `result.created` je false.

### <a name="filters"></a>Filtry
Volitelné filtrování operace lze použít na operace prováděné pomocí **QueueService**. Filtrování operace mohou zahrnovat protokolování, automatické opakování, atd. Filtry jsou objekty, které implementují metodu s podpisem:

```javascript
function handle (requestOptions, next)
```

Po provedení jeho předběžné zpracování na možnosti požadavku, metoda musí volat "další" předání zpětného volání s následujícím podpisem:

```javascript
function (returnObject, finalCallback, next)
```

V tomto zpětném volání a po zpracování returnObject (odpověď z požadavku na server), zpětné volání musí buď vyvolat další, pokud existuje pokračovat ve zpracování jiných filtrů nebo jednoduše vyvolat finalCallback jinak ukončit službu Vyvolání.

Sada Azure SDK pro Node.js obsahuje dva filtry, které implementují logiku opakování: **ExponentialRetryPolicyFilter** a **LinearRetryPolicyFilter**. Následující vytvoří Objekt **QueueService,** který používá **ExponencialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Postup: Vložení zprávy do fronty
Chcete-li vložit zprávu do fronty, použijte metodu **createMessage** k vytvoření nové zprávy a její přidání do fronty.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Postup: Náhled na další zprávu
Můžete nahlédnout na zprávu v přední části fronty bez odebrání z fronty voláním **peekMessages** metoda. Ve výchozím nastavení **peekMessages** pohlédne na jednu zprávu.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

Obsahuje `result` zprávu.

> [!NOTE]
> Použití **peekMessages** pokud nejsou žádné zprávy ve frontě nevrátí chybu, ale žádné zprávy budou vráceny.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Postup: Vyřazení další zprávy ze zařazení do fronty
Zpracování zprávy je dvoufázový proces:

1. Dequeue zprávy.
2. Odstraňte zprávu.

Chcete-li zprávu odřadit do fronty, použijte **službu getMessages**. Díky zprávy neviditelné ve frontě, takže žádné jiné klienty mohou zpracovat. Jakmile vaše aplikace zpracovala zprávu, volání **deleteMessage** odstranit z fronty. Následující příklad zobrazí zprávu a odstraní ji:

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
> Ve výchozím nastavení je zpráva skryta pouze po dobu 30 sekund, po které je viditelná pro ostatní klienty. Můžete zadat jinou hodnotu pomocí `options.visibilityTimeout` **getMessages**.
> 
> [!NOTE]
> Použití **getMessages** pokud nejsou žádné zprávy ve frontě nevrátí chybu, ale žádné zprávy budou vráceny.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postup: Změna obsahu zprávy ve frontě
Obsah zprávy ve frontě můžete změnit pomocí **funkce updateMessage**. Následující příklad aktualizuje text zprávy:

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

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Postup: Další možnosti pro dequeuing zpráv
Načítání zpráv z fronty lze přizpůsobit dvěma způsoby:

* `options.numOfMessages`- Načíst dávku zpráv (až 32.)
* `options.visibilityTimeout`- Nastavte delší nebo kratší časový limit neviditelnosti.

Následující příklad používá **getMessages** metoda získat 15 zpráv v jednom volání. Pak zpracovává každou zprávu pomocí smyčky for. Také nastaví časový limit neviditelnosti na pět minut pro všechny zprávy vrácené touto metodou.

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

## <a name="how-to-get-the-queue-length"></a>Postup: Získání délky fronty
**GetQueueMetadata** vrátí metadata o frontě, včetně přibližného počtu zpráv čekajících ve frontě.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Postup: Seznam front
Chcete-li načíst seznam front, použijte **listQueuesSegmented**. Chcete-li načíst seznam filtrovaný určitou předponou, použijte **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Pokud nelze vrátit všechny `result.continuationToken` fronty, lze použít jako první parametr **listQueuesSegmented** nebo druhý parametr **listQueuesSegmentedWithPrefix** pro načtení dalších výsledků.

## <a name="how-to-delete-a-queue"></a>Postup: Odstranění fronty
Chcete-li odstranit frontu a všechny zprávy v ní obsažené, zavolejte metodu **deleteQueue** na objektu fronty.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Chcete-li vymazat všechny zprávy z fronty bez odstranění, použijte **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Postup: Práce se sdílenými přístupovými podpisy
Sdílené přístupové podpisy (SAS) představují bezpečný způsob, jak poskytnout podrobný přístup k frontám bez poskytnutí názvu účtu úložiště nebo klíčů. SAS se často používají k poskytování omezeného přístupu k frontám, jako je například povolení mobilní aplikace odesílat zprávy.

Důvěryhodná aplikace, jako je například cloudová služba, generuje SAS pomocí **generateSharedAccessSignature** **služby QueueService**a poskytuje ji nedůvěryhodné nebo částečně důvěryhodné aplikaci. Například mobilní aplikace. SAS se generuje pomocí zásady, která popisuje počáteční a koncové datum platnosti SAS a také úroveň přístupu udělenou držiteli SAS.

Následující příklad generuje nové zásady sdíleného přístupu, které umožní držiteli SAS přidávat zprávy do fronty a vyprší 100 minut po jeho vytvoření.

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

Všimněte si, že informace o hostiteli musí být poskytnuty také, jak je požadováno, když držitel SAS pokusí o přístup do fronty.

Klientská aplikace pak používá SAS s **QueueServiceWithSAS** k provádění operací proti frontě. Následující příklad se připojí k frontě a vytvoří zprávu.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Vzhledem k tomu, že SAS byla generována s přístupem přidat, pokud došlo k pokusu o čtení, aktualizaci nebo odstranění zpráv, bude vrácena chyba.

### <a name="access-control-lists"></a>Seznamy řízení přístupu
K nastavení zásad přístupu pro SAS můžete použít také seznam řízení přístupu (ACL). To je užitečné, pokud chcete povolit více klientům přístup do fronty, ale poskytují různé zásady přístupu pro každého klienta.

Seznam ACL se implementuje pomocí pole zásad přístupu, z nichž každá zásada má přidružené ID. Následující příklad definuje dvě zásady; jeden pro 'user1' a jeden pro 'user2':

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

Následující příklad získá aktuální acl pro **myqueue**, pak přidá nové zásady pomocí **setQueueAcl**. Tento přístup umožňuje:

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

Po nastavení acl, pak můžete vytvořit SAS na základě ID pro zásady. Následující příklad vytvoří nový SAS pro uživatele user2:

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy úložiště front, postupujte podle těchto odkazů a získejte další informace o složitějších úlohách úložiště.

* Navštivte [Blog týmu Azure Storage][Azure Storage Team Blog].
* Navštivte úložiště [Azure Storage SDK pro][Azure Storage SDK for Node] úložiště uzlů na GitHubu.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Vytvoření webové aplikace Node.js ve službě Azure App Service](../../app-service/app-service-web-get-started-nodejs.md)

[Sestavení a nasazení aplikace Node.js ve službě Azure Cloud Service](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
