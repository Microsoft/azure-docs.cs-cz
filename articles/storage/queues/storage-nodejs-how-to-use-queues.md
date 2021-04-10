---
title: Jak používat Azure Queue Storage z Node.js-Azure Storage
description: Naučte se používat Azure Queue Storage k vytváření a odstraňování front. Naučte se vkládat, získávat a odstraňovat zprávy pomocí Node.js.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 12/21/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 161000f2860a1153424a628a2b303f3717f828da
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275936"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>Jak používat Azure Queue Storage z Node.js

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Přehled

V této příručce se dozvíte, jak provádět běžné scénáře pomocí Azure Queue Storage. Ukázky se napíší pomocí rozhraní Node.js API. Mezi zahrnuté scénáře patří vkládání, prohlížení, získávání a odstraňování zpráv fronty. Také se naučíte vytvářet a odstraňovat fronty.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js

Pokud chcete vytvořit prázdnou Node.js aplikaci, přečtěte si téma [Vytvoření webové aplikace v Node.js v Azure App Service](../../app-service/quickstart-nodejs.md), [sestavení a nasazení Node.js aplikace do Azure Cloud Services](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) pomocí PowerShellu nebo [Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Konfigurace aplikace pro přístup k úložišti

[Klientská knihovna Azure Storage pro jazyk JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript) obsahuje sadu praktických knihoven, které komunikují se službami REST služby Storage.

<!-- docutune:ignore Terminal -->

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>K získání balíčku použijte Správce balíčků Node (npm).

1. Použijte rozhraní příkazového řádku, jako je PowerShell (Windows), terminál (Mac) nebo bash (UNIX), přejděte do složky, ve které jste vytvořili ukázkovou aplikaci.

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

1. `npm install @azure/storage-queue`Do příkazového řádku zadejte.

1. Ověřte, že se `node_modules` vytvořila složka. V této složce najdete `@azure/storage-queue` balíček, který obsahuje klientskou knihovnu, kterou potřebujete pro přístup k úložišti.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

1. `npm install azure-storage`Do příkazového řádku zadejte.

1. Ověřte, že se `node_modules` vytvořila složka. V této složce najdete `azure-storage` balíček obsahující knihovny, které potřebujete pro přístup k úložišti.

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

Následující kód získá hodnotu proměnné prostředí s názvem `AZURE_STORAGE_CONNECTION_STRING` a použije ji k vytvoření [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient) objektu. Tento objekt se pak použije k vytvoření [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient) objektu, který vám umožní pracovat s konkrétní frontou.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

Pokud fronta již existuje, je vyvolána výjimka.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Modul Azure přečte proměnné prostředí `AZURE_STORAGE_ACCOUNT` a `AZURE_STORAGE_ACCESS_KEY` nebo `AZURE_STORAGE_CONNECTION_STRING` pro informace požadované pro připojení k vašemu Azure Storage účtu. Pokud tyto proměnné prostředí nejsou nastaveny, je nutné při volání zadat informace o účtu `createQueueService` .

Následující kód vytvoří `QueueService` objekt, který vám umožní pracovat s frontami.

```javascript
var queueSvc = azure.createQueueService();
```

Voláním `createQueueIfNotExists` metody vytvořte novou frontu se zadaným názvem nebo vraťte frontu, pokud již existuje.

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

Chcete-li přidat zprávu do fronty, zavolejte [`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) metodu.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Chcete-li vložit zprávu do fronty, zavolejte `createMessage` metodu pro vytvoření nové zprávy a přidejte ji do fronty.

```javascript
queueSvc.createMessage('myqueue', "Hello, World", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>Jak prohlížet další zprávu

Můžete prohlížet zprávy ve frontě bez jejich odebrání z fronty voláním `peekMessages` metody.

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

Ve výchozím nastavení se [`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) v jedné zprávě prohlédne. Následující příklad si prohlédne prvních pět zpráv ve frontě. Pokud jsou viditelné méně než pět zpráv, vrátí se pouze zobrazené zprávy.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Ve výchozím nastavení se `peekMessages` v jedné zprávě prohlédne.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result`Obsahuje zprávu.

---

Volání `peekMessages` v případě, že ve frontě nejsou žádné zprávy, nevrátí chybu. Nevrátí se ale žádné zprávy.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postup změny obsahu zprávy ve frontě

Následující příklad aktualizuje text zprávy.

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

Změňte obsah zprávy na místě ve frontě tak, že zavoláte [`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-) .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Změňte obsah zprávy na místě ve frontě tak, že zavoláte `updateMessage` .

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

Chcete-li získat zprávu, zavolejte [`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) metodu. Toto volání zpřístupňuje zprávy ve frontě, takže je nemohou zpracovat žádní další klienti. Jakmile aplikace zpracuje zprávu, zavolejte [`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) k jejímu odstranění z fronty.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

Ve výchozím nastavení je zpráva po dobu 30 sekund skrytá. Po 30 sekundách je uvidí ostatní klienti. Můžete zadat jinou hodnotu nastavením [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) při volání `receiveMessages` .

Volání `receiveMessages` v případě, že ve frontě nejsou žádné zprávy, nevrátí chybu. Nebudou ale vráceny žádné zprávy.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Chcete-li získat zprávu, zavolejte `getMessages` metodu. Toto volání zpřístupňuje zprávy ve frontě, takže je nemohou zpracovat žádní další klienti. Jakmile aplikace zpracuje zprávu, zavolejte `deleteMessage` k jejímu odstranění z fronty.

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

Ve výchozím nastavení je zpráva po dobu 30 sekund skrytá. Po 30 sekundách je uvidí ostatní klienti. Pomocí with můžete zadat jinou hodnotu `options.visibilityTimeout` `getMessages` .

`getMessages`V případě, že ve frontě nejsou žádné zprávy, nevrátí chybu. Nebudou ale vráceny žádné zprávy.

---

## <a name="additional-options-for-dequeuing-messages"></a>Další možnosti pro vyřazování zpráv do fronty

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

Existují dva způsoby, jak lze přizpůsobit načtení zprávy z fronty:

- [`options.numberOfMessages`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages): Načíst dávku zpráv (až do 32).
- [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout): Nastavte delší nebo kratší časový limit neviditelnosti.

Následující příklad používá `receiveMessages` metodu k získání pěti zpráv v jednom volání. Pak každou zprávu zpracuje pomocí `for` smyčky. U všech zpráv vrácených touto metodou nastaví také časový limit neviditelnosti na pět minut.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Existují dva způsoby, jak lze přizpůsobit načtení zprávy z fronty:

- `options.numOfMessages`: Načíst dávku zpráv (až do 32).
- `options.visibilityTimeout`: Nastavte delší nebo kratší časový limit neviditelnosti.

Následující příklad používá `getMessages` metodu k získání 15 zpráv v jednom volání. Pak každou zprávu zpracuje pomocí `for` smyčky. U všech zpráv vrácených touto metodou nastaví také časový limit neviditelnosti na pět minut.

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

[`getProperties`](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-)Metoda vrátí metadata o frontě, včetně přibližného počtu zpráv čekajících ve frontě.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

`getQueueMetadata`Metoda vrátí metadata o frontě, včetně přibližného počtu zpráv čekajících ve frontě.

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

Chcete-li načíst seznam front, zavolejte [`QueueServiceClient.listQueues`](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) . Chcete-li načíst seznam filtrovaný konkrétní předponou, nastavte [možnost Options. prefix](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) na `listQueues` .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Chcete-li načíst seznam front, použijte `listQueuesSegmented` . Chcete-li načíst seznam filtrovaný pomocí konkrétní předpony, použijte `listQueuesSegmentedWithPrefix` .

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Pokud nelze vrátit všechny fronty, předejte `result.continuationToken` jako první parametr `listQueuesSegmented` nebo druhý parametr `listQueuesSegmentedWithPrefix` pro získání více výsledků.

---

## <a name="how-to-delete-a-queue"></a>Postup odstranění fronty

# <a name="javascript-v12"></a>[JavaScriptový V12](#tab/javascript)

Chcete-li odstranit frontu a všechny zprávy, které jsou v něm obsaženy, zavolejte [`DeleteQueue`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) metodu `QueueClient` objektu.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

Chcete-li vymazat všechny zprávy z fronty bez jejich odstranění, zavolejte [`ClearMessages`](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-) .

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Pokud chcete odstranit frontu a všechny zprávy, které jsou v ní obsažené, zavolejte `deleteQueue` metodu u objektu Queue.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Chcete-li vymazat všechny zprávy z fronty bez jejich odstranění, zavolejte `clearMessages` .

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili základy Queue Storage, postupujte podle těchto odkazů a získejte další informace o složitějších úlohách úložiště.

- Navštivte [Blog týmu Azure Storage](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog) , kde se dozvíte, co je nového.
- Navštivte [Azure Storage klientskou knihovnu pro úložiště JavaScriptu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#Azure-storage-client-library-for-JavaScript) na GitHubu.
