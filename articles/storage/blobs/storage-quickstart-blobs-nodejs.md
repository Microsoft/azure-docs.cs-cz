---
title: 'Rychlý Start: knihovna úložiště objektů BLOB v Azure V12-JavaScript'
description: V tomto rychlém startu se dozvíte, jak pomocí klientské knihovny Azure Blob Storage verze 12 pro JavaScript vytvořit kontejner a objekt BLOB v úložišti objektů BLOB (objekt). Dále se dozvíte, jak stáhnout objekt blob do místního počítače a jak zobrazit seznam všech objektů blob, které jsou v kontejneru.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 0883fd1546ac3b55ec4211e5048569556858de29
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179914"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>Rychlý Start: Správa objektů BLOB pomocí sady JavaScript V12 SDK v Node.js

V tomto rychlém startu se naučíte spravovat objekty BLOB pomocí Node.js. Objekty blob jsou objekty, které mohou obsahovat velké objemy textových nebo binárních dat, včetně obrázků, dokumentů, datových proudů médií a dat archivu. Můžete nahrávat, stahovat a vypisovat objekty BLOB a vytvářet a odstraňovat kontejnery.

Další prostředky:

* [Referenční dokumentace k rozhraní API](/javascript/api/@azure/storage-blob)
* [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob)
* [Balíček (Správce balíčků uzlů)](https://www.npmjs.com/package/@azure/storage-blob)
* [ukázky](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Účet služby Azure Storage. [Vytvořte účet úložiště](../common/storage-account-create.md).
- [Node.js](https://nodejs.org/en/download/).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnou služby Azure Blob Storage V12 pro JavaScript.

### <a name="create-the-project"></a>Vytvoření projektu

Vytvořte aplikaci JavaScriptu s názvem *BLOB-Start-V12*.

1. V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro projekt.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Přepněte do nově vytvořeného adresáře pro *rychlý Start objektů BLOB-V12* .

    ```console
    cd blob-quickstart-v12
    ```

1. Vytvořte nový textový soubor s názvem *package.jsv*. Tento soubor definuje projekt Node.js. Uložte tento soubor v adresáři *objektů BLOB-rychlý Start-V12* . Tady je obsah souboru:

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Pokud chcete, můžete do pole umístit vlastní název `author` .

### <a name="install-the-package"></a>Instalace balíčku

Ještě pořád v adresáři *objektů BLOB – rychlý Start – V12* nainstalujte knihovnu klienta Azure Blob Storage pro balíček JavaScriptu pomocí `npm install` příkazu. Tento příkaz přečte *package.jsv* souboru a nainstaluje balíček klientské knihovny Azure Blob Storage V12 for JavaScript a všechny knihovny, na kterých závisí.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Z adresáře projektu:

1. Otevřít další nový textový soubor v editoru kódu
1. Přidání `require` volání pro načtení modulů Azure a Node.js
1. Vytvoření struktury pro program, včetně základního zpracování výjimek

    Zde je kód:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const { v1: uuidv1} = require('uuid');

    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }

    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Uložte nový soubor jako *blob-quickstart-v12.js* v adresáři *objektů BLOB-rychlý Start-V12* .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektový model

Úložiště objektů BLOB v Azure je optimalizované pro ukládání obrovských objemů nestrukturovaných dat. Jde o data, která nevyhovují konkrétnímu datovému modelu nebo definici, například textová nebo binární data. Úložiště objektů BLOB nabízí tři typy prostředků:

* Účet úložiště
* Kontejner v účtu úložiště
* Objekt BLOB v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury služby Blob Storage](./media/storage-blobs-introduction/blob1.png)

Pro interakci s těmito prostředky použijte následující třídy JavaScriptu:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): `BlobServiceClient` Třída umožňuje manipulovat s Azure Storage prostředky a kontejnery objektů BLOB.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): `ContainerClient` Třída umožňuje manipulovat s kontejnery Azure Storage a jejich objekty blob.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient): `BlobClient` Třída umožňuje manipulovat s objekty blob Azure Storage.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provést následující akce s klientskou knihovnou služby Azure Blob Storage pro JavaScript:

* [Získání připojovacího řetězce](#get-the-connection-string)
* [Vytvoření kontejneru](#create-a-container)
* [Nahrání objektů blob do kontejneru](#upload-blobs-to-a-container)
* [Seznam objektů blob v kontejneru](#list-the-blobs-in-a-container)
* [Stáhnout objekty blob](#download-blobs)
* [Odstranění kontejneru](#delete-a-container)

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Následující kód načte připojovací řetězec pro účet úložiště z proměnné prostředí vytvořené v oddílu [konfigurace vašeho připojovacího řetězce úložiště](#configure-your-storage-connection-string) .

Přidejte tento kód do `main` funkce:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-container"></a>Vytvoření kontejneru

Určete název nového kontejneru. Následující kód připojí hodnotu UUID k názvu kontejneru, aby bylo zajištěno, že je jedinečný.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Vytvořte instanci třídy [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) voláním metody [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) . Pak zavolejte metodu [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) a získejte odkaz na kontejner. Nakonec voláním metody [Create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) vytvořte kontejner ve svém účtu úložiště.

Přidejte tento kód na konec `main` funkce:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>Nahrání objektů blob do kontejneru

Následující fragment kódu:

1. Vytvoří textový řetězec, který se má nahrát do objektu BLOB.
1. Získá odkaz na objekt [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) voláním metody [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) na [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) v části [vytvoření kontejneru](#create-a-container) .
1. Nahraje data řetězce textu do objektu BLOB voláním metody [Upload](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) .

Přidejte tento kód na konec `main` funkce:

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
```

### <a name="list-the-blobs-in-a-container"></a>Seznam objektů blob v kontejneru

Seznam objektů BLOB v kontejneru zavoláním metody [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) . V tomto případě byl do kontejneru přidán pouze jeden objekt blob, takže operace výpisu vrátí pouze jeden objekt BLOB.

Přidejte tento kód na konec `main` funkce:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Stáhnout objekty blob

Stáhněte dřív vytvořený objekt BLOB voláním metody [Download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) . Vzorový kód obsahuje pomocnou funkci nazvanou `streamToString` , která se používá ke čtení Node.jsho datového proudu, který je čitelný do řetězce.

Přidejte tento kód na konec `main` funkce:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Tuto pomocnou funkci  přidejte za `main` funkci:

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>Odstranění kontejneru

Následující kód vyčistí prostředky, které aplikace vytvořila, odebráním celého kontejneru pomocí metody [Delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) . Pokud chcete, můžete také odstranit místní soubory.

Přidejte tento kód na konec `main` funkce:

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>Spuštění kódu

Tato aplikace vytvoří textový řetězec a nahraje ho do úložiště objektů BLOB. Příklad následně vypíše objekty BLOB v kontejneru, stáhne objekt BLOB a zobrazí stažená data.

V příkazovém řádku konzoly přejděte do adresáře obsahujícího soubor *blob-quickstart-v12.js* a pak `node` Spusťte následující příkaz, který aplikaci spustí.

```console
node blob-quickstart-v12.js
```

Výstup aplikace je podobný následujícímu příkladu:

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

Projděte si kód v ladicím programu a ověřte [Azure Portal](https://portal.azure.com) v průběhu celého procesu. Zkontrolujte, zda se kontejner vytváří. Objekt blob můžete otevřít uvnitř kontejneru a zobrazit jeho obsah.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nahrávat, stahovat a vypisovat objekty BLOB pomocí JavaScriptu.

Výukové programy, ukázky, rychlé starty a další dokumentaci najdete na webu:

> [!div class="nextstepaction"]
> [Azure pro středisko pro vývojáře JavaScriptu](/azure/developer/javascript/)

* Informace o tom, jak nasadit webovou aplikaci, která využívá úložiště objektů BLOB v Azure, najdete v tématu [kurz: nahrání obrazových dat v cloudu pomocí Azure Storage](./storage-upload-process-images.md?preserve-view=true&tabs=javascript)
* Ukázková aplikace služby Blob Storage najdete v [ukázkách V12 JavaScriptu v klientské knihovně pro úložiště objektů BLOB v Azure](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples).
* Další informace najdete v tématu [Klientská knihovna pro úložiště objektů BLOB v Azure pro JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob).
