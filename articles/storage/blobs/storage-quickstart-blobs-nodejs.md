---
title: 'Úvodní příručka: Knihovna úložiště objektů Blob Azure v12 – JavaScript'
description: V tomto rychlém startu se dozvíte, jak pomocí knihovny klienta úložiště objektů Blob Azure verze 12 pro JavaScript vytvořit kontejner a objekt blob v úložišti objektů blob (objekt). Dále se dozvíte, jak stáhnout objekt blob do místního počítače a jak zobrazit seznam všech objektů blob, které jsou v kontejneru.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: e68e91d90846ab77b994b53be7a84a9dd8bc5a25
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241035"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>Úvodní příručka: Správa objektů BLOB pomocí sady JavaScript v12 SDK v souboru Node.js

V tomto rychlém startu se naučíte spravovat objekty BLOB pomocí souboru Node.js. Objekty BLOB jsou objekty, které mohou obsahovat velké množství textových nebo binárních dat, včetně obrázků, dokumentů, datových proudů médií a archivních dat. Nahrajete, stáhnete a zobrazíte objekty BLOB a vytvoříte a odstraníte kontejnery.

[Referenční dokumentace](/javascript/api/@azure/storage-blob) | rozhraní[KNIHOVNA zdrojový kód](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [Balíček (Správce balíčků uzlů)](https://www.npmjs.com/package/@azure/storage-blob) | [Ukázky](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Účet služby Azure Storage. [Vytvořte účet úložiště](../common/storage-account-create.md).
- [Soubor Node.js](https://nodejs.org/en/download/).

> [!NOTE]
> Chcete-li začít s předchozí verzí sady SDK, přečtěte [si úvodní příručku: Správa objektů BLOB pomocí sady JavaScript v10 SDK v souboru Node.js](storage-quickstart-blobs-nodejs-legacy.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnou úložiště objektů Blob Azure v12 pro JavaScript.

### <a name="create-the-project"></a>Vytvoření projektu

Vytvořte aplikaci JavaScript s názvem *blob-quickstart-v12*.

1. V okně konzoly (například cmd, PowerShell nebo Bash) vytvořte nový adresář pro projekt.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Přepněte do nově vytvořeného *adresáře blob-quickstart-v12.*

    ```console
    cd blob-quickstart-v12
    ```

1. Vytvořte nový textový soubor s názvem *package.json*. Tento soubor definuje projekt Node.js. Uložte tento soubor do adresáře *blob-quickstart-v12.* Zde je obsah souboru:

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
    
    Můžete dát své vlastní jméno `author` do pole, pokud chcete.
   
### <a name="install-the-package"></a>Instalace balíčku

Zatímco stále v adresáři *blob-quickstart-v12,* nainstalujte klientskou knihovnu úložiště `npm install` Objektů blob Azure pro balíček JavaScript pomocí příkazu. Tento příkaz přečte soubor *package.json* a nainstaluje klientskou knihovnu úložiště objektů Blob Azure v12 pro balíček JavaScript a všechny knihovny, na kterých závisí.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Nastavení architektury aplikace

Z adresáře projektu:

1. Otevření jiného nového textového souboru v editoru kódu
1. Přidání `require` volání pro načtení modulů Azure a Node.js
1. Vytvoření struktury programu, včetně základního zpracování výjimek

    Zde je kód:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Uložte nový soubor jako *objekt blob-quickstart-v12.js* v adresáři *blob-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektový model

Úložiště objektů blob Azure je optimalizované pro ukládání obrovského množství nestrukturovaných dat. Jde o data, která nevyhovují konkrétnímu datovému modelu nebo definici, například textová nebo binární data. Úložiště objektů blob nabízí tři typy prostředků:

* Účet úložiště
* Kontejner v účtu úložiště
* Objekt blob v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury úložiště objektů blob](./media/storage-blobs-introduction/blob1.png)

K interakci s těmito prostředky použijte následující třídy JavaScriptu:

* [BlobServiceClient:](/javascript/api/@azure/storage-blob/blobserviceclient) `BlobServiceClient` Třída umožňuje manipulovat s prostředky úložiště Azure a kontejnery objektů blob.
* [ContainerClient:](/javascript/api/@azure/storage-blob/containerclient) `ContainerClient` Třída umožňuje manipulovat s kontejnery azure storage a jejich objekty BLOB.
* [Objekt BlobClient:](/javascript/api/@azure/storage-blob/blobclient)Třída `BlobClient` umožňuje manipulovat s objekty BLOB úložiště Azure.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provést následující akce v klientské knihovně úložiště objektů Blob Azure pro JavaScript:

* [Získání připojovacího řetězce](#get-the-connection-string)
* [Vytvoření kontejneru](#create-a-container)
* [Nahrání objektů BLOB do kontejneru](#upload-blobs-to-a-container)
* [Zobrazí seznam objektů blob v kontejneru](#list-the-blobs-in-a-container)
* [Stáhnout objekty blob](#download-blobs)
* [Odstranění kontejneru](#delete-a-container)

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Níže uvedený kód načte připojovací řetězec pro účet úložiště z proměnné prostředí vytvořené v části [Konfigurovat připojovací řetězec úložiště.](#configure-your-storage-connection-string)

Přidejte tento `main` kód do funkce:

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

Rozhodněte o názvu nového kontejneru. Níže uvedený kód připojí hodnotu UUID k názvu kontejneru, aby bylo zajištěno, že je jedinečný.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Vytvořte instanci třídy [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) voláním metody [fromConnectionString.](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) Potom zavolejte metodu [getContainerClient,](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) abyste získali odkaz na kontejner. Nakonec volání [vytvořit](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) skutečně vytvořit kontejner ve vašem účtu úložiště.

Přidejte tento kód na `main` konec funkce:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = await blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>Nahrání objektů BLOB do kontejneru

Následující fragment kódu:

1. Vytvoří textový řetězec pro nahrání do objektu blob.
1. Získá odkaz na Objekt [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) voláním metody [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) na [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) z [oddílu Vytvořit kontejner.](#create-a-container)
1. Nahraje data textového řetězce do objektu blob voláním metody [upload.](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-)

Přidejte tento kód na `main` konec funkce:

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

### <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Seznam objektů BLOB v kontejneru voláním [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) metoda. V tomto případě byl do kontejneru přidán pouze jeden objekt blob, takže operace výpisu vrátí pouze jeden objekt blob.

Přidejte tento kód na `main` konec funkce:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Stáhnout objekty blob

Stáhněte si dříve vytvořený objekt blob voláním metody [download.](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) Ukázkový kód obsahuje pomocnou `streamToString`funkci s názvem , která se používá ke čtení datového proudu Node.js do řetězce.

Přidejte tento kód na `main` konec funkce:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Přidejte tuto *after* pomocnou `main` funkci za funkci:

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

Následující kód vyčistí prostředky, které aplikace vytvořila odebráním celého kontejneru pomocí metody [delete.](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) Můžete také odstranit místní soubory, pokud chcete.

Přidejte tento kód na `main` konec funkce:

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>Spuštění kódu

Tato aplikace vytvoří textový řetězec a nahraje ho do úložiště objektů Blob. V příkladu je pak uvedenobjekty blob v kontejneru, stáhne objekt blob a zobrazí stažená data.

Z výzvy konzoly přejděte do *blob-quickstart-v12.py* adresáře obsahujícího `node` blob-quickstart-v12.py soubor a spusťte následující příkaz pro spuštění aplikace.

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

Projděte si kód v ladicím programu a zkontrolujte [portál Azure](https://portal.azure.com) v průběhu celého procesu. Zkontrolujte, zda je kontejner vytvářen. Objekt blob můžete otevřít uvnitř kontejneru a zobrazit obsah.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili nahrávat, stahovat a seznamovat objekty BLOB pomocí JavaScriptu.

Kurzy, ukázky, rychlé starty a další dokumentaci najdete na adrese:

> [!div class="nextstepaction"]
> [Dokumentace k Azure pro JavaScript](/azure/javascript/)

* Další informace najdete v tématu [klientská knihovna úložiště objektů Blob Azure pro JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob).
* Pokud chcete zobrazit ukázkové aplikace úložiště objektů blob, pokračujte na [ukázce klienta úložiště objektů Blob Azure v12 JavaScriptukázkové ukázky](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples).
