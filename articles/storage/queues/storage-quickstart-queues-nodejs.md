---
title: 'Rychlý Start: knihovna úložiště Azure Queue V12-JavaScript'
description: Naučte se používat knihovnu V12 JavaScriptu pro Azure Queue k vytvoření fronty a přidání zpráv do fronty. V dalším kroku se dozvíte, jak číst a odstraňovat zprávy z fronty. Naučíte se také, jak odstranit frontu.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/13/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 1446b501b9d91c94c12c82755d0e31f361f8cd8f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783415"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>Rychlý Start: Klientská knihovna Azure Queue Storage V12 pro JavaScript

Začněte s klientskou knihovnou Azure Queue Storage verze 12 pro JavaScript. Azure Queue Storage je služba pro ukládání velkého počtu zpráv pro pozdější načtení a zpracování. Postupujte podle těchto kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy.

Použijte klientskou knihovnu Azure Queue Storage V12 pro JavaScript:

* Vytvoření fronty
* Přidání zpráv do fronty
* Prohlížet zprávy ve frontě
* Aktualizace zprávy ve frontě
* Přijímání zpráv z fronty
* Odstranění zpráv z fronty
* Odstranění fronty

Další prostředky:

* [Referenční dokumentace k rozhraní API](/javascript/api/@azure/storage-queue/)
* [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)
* [Balíček (Správce balíčků uzlů)](https://www.npmjs.com/package/@azure/storage-queue)
* [Ukázky](../common/storage-samples-javascript.md?toc=%252fazure%252fstorage%252fqueues%252ftoc.json#queue-samples)

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Účet úložiště Azure – [Vytvoření účtu úložiště](../common/storage-account-create.md)
* Aktuální [Node.js](https://nodejs.org/en/download/) pro váš operační systém.

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnou Azure Queue Storage V12 for JavaScript.

### <a name="create-the-project"></a>Vytvoření projektu

Vytvořte aplikaci Node.js s názvem *Queues-Started-V12* .

1. V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro projekt.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Přepněte na nově vytvořený adresář *s rychlým startem front-V12* .

    ```console
    cd queues-quickstart-v12
    ```

1. Vytvořte nový textový soubor s názvem *package.jsv* . Tento soubor definuje projekt Node.js. Uložte tento soubor v adresáři *Queues-Starter-V12* . Tady je obsah souboru:

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Pokud chcete, můžete do pole umístit vlastní název `author` .

### <a name="install-the-package"></a>Instalace balíčku

Ještě pořád v adresáři *fronty – rychlý Start-V12* nainstalujte knihovnu klienta Azure Queue Storage pro balíček JavaScriptu pomocí `npm install` příkazu.

```console
npm install
```

 Tento příkaz přečte *package.jsv* souboru a nainstaluje balíček klientské knihovny Azure Queue Storage V12 for JavaScript a všechny knihovny, na kterých závisí.

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Z adresáře projektu:

1. Otevřít další nový textový soubor v editoru kódu
1. Přidání `require` volání pro načtení modulů Azure a Node.js
1. Vytvoření struktury pro program, včetně velmi základního zpracování výjimek

    Zde je kód:

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue storage v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. Uložte nový soubor jako *queues-quickstart-v12.js* v adresáři *Queues-Started-V12* .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektový model

Azure Queue Storage je služba pro ukládání velkých objemů zpráv. Zpráva fronty může mít velikost až 64 KB. Fronta může obsahovat miliony zpráv až do celkového limitu kapacity účtu úložiště. Fronty se běžně používají k vytváření nevyřízených položek pro asynchronní zpracování. Queue Storage nabízí tři typy prostředků:

* Účet úložiště
* Fronta v účtu úložiště
* Zprávy ve frontě

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury služby Queue Storage](./media/storage-queues-introduction/queue1.png)

Pro interakci s těmito prostředky použijte následující třídy JavaScriptu:

* [QueueServiceClient](/javascript/api/@azure/storage-queue/queueserviceclient): `QueueServiceClient` umožňuje spravovat všechny fronty v účtu úložiště.
* [QueueClient](/javascript/api/@azure/storage-queue/queueclient): `QueueClient` Třída umožňuje spravovat a manipulovat s jednotlivou frontou a jejími zprávami.
* [QueueMessage](/javascript/api/@azure/storage-queue/queuemessage): `QueueMessage` Třída představuje jednotlivé objekty vracené při volání [receiveMessages](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) ve frontě.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provést následující akce s klientskou knihovnou Azure Queue Storage pro JavaScript:

* [Získání připojovacího řetězce](#get-the-connection-string)
* [Vytvoření fronty](#create-a-queue)
* [Přidání zpráv do fronty](#add-messages-to-a-queue)
* [Prohlížet zprávy ve frontě](#peek-at-messages-in-a-queue)
* [Aktualizace zprávy ve frontě](#update-a-message-in-a-queue)
* [Přijímání zpráv z fronty](#receive-messages-from-a-queue)
* [Odstranění zpráv z fronty](#delete-messages-from-a-queue)
* [Odstranění fronty](#delete-a-queue)

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Následující kód načte připojovací řetězec pro účet úložiště z proměnné prostředí vytvořené v oddílu [konfigurace vašeho připojovacího řetězce úložiště](#configure-your-storage-connection-string) .

Přidejte tento kód do `main` funkce:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>Vytvoření fronty

Určete název nové fronty. Následující kód připojí hodnotu UUID k názvu fronty, aby bylo zajištěno, že je jedinečný.

> [!IMPORTANT]
> Názvy front můžou obsahovat jenom malá písmena, číslice a spojovníky a musí začínat písmenem nebo číslicí. Před i za každým spojovníkem musí být jiný znak než spojovník. Název musí mít také délku 3 až 63 znaků. Další informace o pojmenovávání front najdete v tématu [pojmenování front a metadat](/rest/api/storageservices/naming-queues-and-metadata).

Vytvořte instanci třídy [QueueClient](/javascript/api/@azure/storage-queue/queueclient) . Pak zavolejte metodu [Create](/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) a vytvořte ve svém účtu úložiště frontu.

Přidejte tento kód na konec `main` funkce:

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>Přidání zpráv do fronty

Následující fragment kódu přidá zprávy do fronty voláním metody [SendMessage](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) . Také uloží [QueueMessage](/javascript/api/@azure/storage-queue/queuemessage) vrácenou z třetího `sendMessage` volání. Vráceno `sendMessageResponse` se používá k aktualizaci obsahu zprávy později v programu.

Přidejte tento kód na konec `main` funkce:

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>Prohlížet zprávy ve frontě

Podívejte se na zprávy ve frontě voláním metody [peekMessages](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) . `peekMessages`Metoda načítá jednu nebo více zpráv před frontou, ale nemění viditelnost zprávy.

Přidejte tento kód na konec `main` funkce:

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>Aktualizace zprávy ve frontě

Aktualizujte obsah zprávy voláním metody [updateMessage](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) . `updateMessage`Metoda může změnit časový limit viditelnosti zprávy a její obsah. Obsah zprávy musí být řetězec kódovaný v kódování UTF-8, který má velikost až 64 KB. Společně s novým obsahem, předejte `messageId` a `popReceipt` z odpovědi, která byla uložena dříve v kódu. `sendMessageResponse`Vlastnosti určují, která zpráva se má aktualizovat.

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>Přijímání zpráv z fronty

Stáhněte dříve přidané zprávy voláním metody [receiveMessages](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) .  V `numberOfMessages` poli předejte maximální počet zpráv, které mají být pro toto volání přijímány.

Přidejte tento kód na konec `main` funkce:

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>Odstranění zpráv z fronty

Po přijetí a zpracování odstraní zprávy z fronty. V tomto případě se zpracovává jenom zpráva v konzole nástroje.

Odstraňte zprávy voláním metody [deleteMessage](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) . Všechny zprávy, které se explicitně neodstraní, se později stanou viditelnými ve frontě, aby se mohly zpracovat další šance na jejich zpracování.

Přidejte tento kód na konec `main` funkce:

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>Odstranění fronty

Následující kód vyčistí prostředky, které aplikace vytvořila, odstraněním fronty pomocí metody [Delete](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) .

Přidejte tento kód na konec `main` funkce a soubor uložte:

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>Spuštění kódu

Tato aplikace vytvoří a přidá tři zprávy do fronty Azure. Kód vypíše zprávy ve frontě a pak je načte a odstraní před tím, než se nakonec odstraní fronta.

V okně konzoly přejděte do adresáře obsahujícího soubor *queues-quickstart-v12.js* a potom `node` Spusťte následující příkaz pro spuštění aplikace.

```console
node queues-quickstart-v12.js
```

Výstup aplikace je podobný následujícímu příkladu:

```output
Azure Queue storage v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

Projděte si kód v ladicím programu a ověřte [Azure Portal](https://portal.azure.com) v průběhu celého procesu. Zkontrolujte, jestli máte účet úložiště, abyste ověřili, že se zprávy ve frontě vytvářejí a odstraňují.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit frontu a přidat do ní zprávy pomocí kódu JavaScriptu. Pak jste se naučili prohlížet, načítat a odstraňovat zprávy. Nakonec jste zjistili, jak odstranit frontu zpráv.

Kurzy, ukázky, rychlé starty a další dokumentace najdete na webu:

> [!div class="nextstepaction"]
> [Dokumentace k Azure pro JavaScript](/azure/developer/javascript/)

* Další informace najdete v tématu [Klientská knihovna pro Azure Storage Queue pro JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue).
* Pokud chcete zobrazit více ukázkových aplikací Azure Queue Storage, pokračujte na [ukázky V12 JavaScriptu v klientské knihovně Azure Queue Storage](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples).