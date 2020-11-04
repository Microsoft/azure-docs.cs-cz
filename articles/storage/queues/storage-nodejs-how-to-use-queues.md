---
title: Jak používat službu Azure Queue Storage z Node.js Azure Storage
description: Naučte se používat Azure Služba front k vytváření a odstraňování front. Naučte se vkládat, získávat a odstraňovat zprávy pomocí Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: c5a9fb1a179164d24c84213762ee7e2332a1aa25
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93345937"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>Jak používat službu Azure Queue Storage z Node.js

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Přehled

V této příručce se dozvíte, jak provádět běžné scénáře pomocí Služba front Microsoft Azure. Ukázky se napíší pomocí rozhraní Node.js API. Mezi zahrnuté scénáře patří vkládání, prohlížení, získávání a odstraňování zpráv fronty. Také se naučíte vytvářet a odstraňovat fronty.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js

Pokud chcete vytvořit prázdnou Node.js aplikaci, přečtěte si téma [Vytvoření webové aplikace v Node.js v Azure App Service][Create a Node.js web app in Azure App Service], [sestavení a nasazení Node.js aplikace do cloudové služby Azure][Build and deploy a Node.js application to an Azure Cloud Service] pomocí Windows PowerShellu nebo [Visual Studio Code][Visual Studio Code].

## <a name="configure-your-application-to-access-storage"></a>Konfigurace aplikace pro přístup k úložišti

[Klientská knihovna Azure Storage pro jazyk JavaScript][Azure Storage client library for JavaScript] obsahuje sadu praktických knihoven, které komunikují se službami REST služby Storage.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>K získání balíčku použijte Správce balíčků Node (NPM).

1. Použijte rozhraní příkazového řádku, jako je PowerShell (Windows), terminál (Mac) nebo bash (UNIX), přejděte do složky, ve které jste vytvořili ukázkovou aplikaci.

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

1. Do příkazového okna zadejte **npm Install \@ Azure/Storage-Queue** .

1. Ověřte, že se vytvořila složka **\_ modulů uzlů** . V této složce najdete balíček **\@ Azure/Storage – Queue** , který obsahuje klientskou knihovnu, kterou potřebujete pro přístup k úložišti.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

1. Do příkazového okna zadejte **npm install azure-storage**.

1. Ověřte, že se vytvořila složka **\_ modulů uzlů** . V této složce najdete balíček **Azure-Storage** obsahující knihovny, které potřebujete pro přístup k úložišti.

---

### <a name="import-the-package"></a>Import balíčku

Pomocí editoru kódu přidejte následující kód do horní části souboru JavaScriptu, kde máte v úmyslu používat fronty.

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ImportStatements":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

```javascript
var azure = require('azure-storage');
```

---

## <a name="how-to-create-a-queue"></a>Jak vytvořit frontu

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

Následující kód získá hodnotu proměnné prostředí s názvem `AZURE_STORAGE_CONNECTION_STRING` a použije ji k vytvoření objektu [QueueServiceClient](/javascript/api/@azure/storage-queue/queueserviceclient) . Objekt **QueueServiceClient** se pak použije k vytvoření objektu [QueueClient](/javascript/api/@azure/storage-queue/queueclient) . Objekt **QueueClient** vám umožní pracovat s konkrétní frontou.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

Pokud fronta již existuje, je vyvolána výjimka.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Modul Azure přečte proměnné prostředí `AZURE_STORAGE_ACCOUNT` a `AZURE_STORAGE_ACCESS_KEY` nebo `AZURE_STORAGE_CONNECTION_STRING` pro informace požadované pro připojení k účtu úložiště Azure. Pokud tyto proměnné prostředí nejsou nastaveny, je nutné při volání **createQueueService** zadat informace o účtu.

Následující kód vytvoří objekt **QueueService** , který vám umožní pracovat s frontami.

```javascript
var queueSvc = azure.createQueueService();
```

Voláním metody **createQueueIfNotExists** vytvořte novou frontu se zadaným názvem nebo vraťte frontu, pokud již existuje.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Pokud je fronta vytvořená, `result.created` má hodnotu true. Pokud fronta existuje, `result.created` je false.

---

## <a name="how-to-insert-a-message-into-a-queue"></a>Vložení zprávy do fronty

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

Chcete-li přidat zprávu do fronty, zavolejte metodu [SendMessage](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Chcete-li vložit zprávu do fronty, zavolejte metodu **createMessage** a vytvořte novou zprávu a přidejte ji do fronty.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>Jak prohlížet další zprávu

Můžete prohlížet zprávy ve frontě bez jejich odebrání z fronty voláním metody **peekMessages** .

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

Ve výchozím nastavení [peekMessages](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) prohlédne jednu zprávu. Následující příklad si prohlédne prvních pět zpráv ve frontě. Pokud jsou viditelné méně než pět zpráv, vrátí se pouze zobrazené zprávy.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Ve výchozím nastavení **peekMessages** prohlédne jednu zprávu.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result`Obsahuje zprávu.

---

Volání **peekMessages** v případě, že ve frontě nejsou žádné zprávy, nevrátí chybu. Nevrátí se ale žádné zprávy.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postup změny obsahu zprávy ve frontě

Následující příklad aktualizuje text zprávy.

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

Změňte obsah zprávy na místě ve frontě tak, že zavoláte [updateMessage](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-).

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Změňte obsah zprávy na místě ve frontě tak, že zavoláte **updateMessage**.

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

---

## <a name="how-to-dequeue-a-message"></a>Jak odřadit zprávu do fronty

Vyřazení zprávy do fronty je proces se dvěma fázemi:

1. Získejte zprávu.

1. Odstraňte zprávu.

Následující příklad získá zprávu a pak ji odstraní.

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

Chcete-li získat zprávu, zavolejte metodu [receiveMessages](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) . Toto volání zpřístupňuje zprávy ve frontě, takže je nemohou zpracovat žádní další klienti. Jakmile aplikace zpracuje zprávu, zavolá [deleteMessage](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) , aby ji odstranila z fronty.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

Ve výchozím nastavení je zpráva po dobu 30 sekund skrytá. Po 30 sekundách je uvidí ostatní klienti. Můžete určit jinou hodnotu nastavením [Options. visibilityTimeout](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) při volání **receiveMessages**.

Volání **receiveMessages** v případě, že ve frontě nejsou žádné zprávy, nevrátí chybu. Nebudou ale vráceny žádné zprávy.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Chcete-li získat zprávu, zavolejte metodu **GetMessages** . Toto volání zpřístupňuje zprávy ve frontě, takže je nemohou zpracovat žádní další klienti. Jakmile aplikace zpracuje zprávu, zavolá **deleteMessage** , aby ji odstranila z fronty.

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

Ve výchozím nastavení je zpráva po dobu 30 sekund skrytá. Po 30 sekundách je uvidí ostatní klienti. Pomocí `options.visibilityTimeout` se **GetMessages** můžete zadat jinou hodnotu.

Když ve frontě nejsou žádné zprávy, nevrátí se žádná chybová **zpráva** . Nebudou ale vráceny žádné zprávy.

---

## <a name="additional-options-for-dequeuing-messages"></a>Další možnosti pro vyřazování zpráv do fronty

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

Existují dva způsoby, jak lze přizpůsobit načtení zprávy z fronty:

- [Options. numberOfMessages](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages) – načtení dávky zpráv (až do 32.)
- [Options. visibilityTimeout](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) – nastaví delší nebo kratší časový limit neviditelnosti.

Následující příklad používá metodu **receiveMessages** k získání pěti zpráv v jednom volání. Pak každou zprávu zpracuje pomocí `for` smyčky. U všech zpráv vrácených touto metodou nastaví také časový limit neviditelnosti na pět minut.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Existují dva způsoby, jak lze přizpůsobit načtení zprávy z fronty:

- `options.numOfMessages` -Načíst dávku zpráv (až 32.)
- `options.visibilityTimeout` – Nastavte delší nebo kratší časový limit pro neviditelnost.

Následující příklad používá metodu **GetMessages** k získání 15 zpráv v jednom volání. Pak každou zprávu zpracuje pomocí `for` smyčky. U všech zpráv vrácených touto metodou nastaví také časový limit neviditelnosti na pět minut.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in results){
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

---

## <a name="how-to-get-the-queue-length"></a>Jak získat délku fronty

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

Metoda [GetProperties](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-) vrátí metadata o frontě, včetně přibližného počtu zpráv čekajících ve frontě.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Metoda **getQueueMetadata** vrací metadata o frontě, včetně přibližného počtu zpráv čekajících ve frontě.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

---

## <a name="how-to-list-queues"></a>Výpis front

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

Chcete-li načíst seznam front, zavolejte [QueueServiceClient. listQueues](). Chcete-li načíst seznam filtrovaný konkrétní předponou, nastavte [Možnosti. prefix](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) ve volání **listQueues**.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Chcete-li načíst seznam front, použijte **listQueuesSegmented**. Chcete-li načíst seznam filtrovaný podle konkrétní předpony, použijte **listQueuesSegmentedWithPrefix**.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Pokud nelze vrátit všechny fronty, předejte `result.continuationToken` jako první parametr **listQueuesSegmented** nebo druhý parametr **listQueuesSegmentedWithPrefix** pro získání dalších výsledků.

---

## <a name="how-to-delete-a-queue"></a>Postup odstranění fronty

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

Pokud chcete odstranit frontu a všechny zprávy, které jsou v ní obsažené, zavolejte metodu [deleteQueue](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) pro objekt **QueueClient** .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

Chcete-li vymazat všechny zprávy z fronty bez jejich odstranění, zavolejte na [clearMessages](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-).

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Pokud chcete odstranit frontu a všechny zprávy, které jsou v ní obsažené, zavolejte metodu **deleteQueue** u objektu Queue.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Chcete-li vymazat všechny zprávy z fronty bez jejich odstranění, zavolejte na **clearMessages**.

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili základní informace o službě Queue Storage, získáte další informace o složitějších úlohách úložiště pomocí těchto odkazů.

- Navštivte [Blog týmu Azure Storage][Azure Storage Team Blog] , kde se dozvíte, co je nového.
- Navštivte [Azure Storage klientskou knihovnu pro úložiště JavaScriptu][Azure Storage client library for JavaScript] na GitHubu.

[Azure Storage client library for JavaScript]: https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript
[Azure Storage Team Blog]: https://techcommunity.microsoft.com/t5/azure-storage/bg-p/AzureStorageBlog
[Build and deploy a Node.js application to an Azure Cloud Service]: ../../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Create a Node.js web app in Azure App Service]: ../../app-service/quickstart-nodejs.md
[Visual Studio Code]: https://code.visualstudio.com/docs/nodejs/nodejs-tutorial
