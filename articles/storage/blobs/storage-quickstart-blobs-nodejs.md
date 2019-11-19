---
title: 'Rychlý Start: knihovna úložiště objektů BLOB v Azure V12-JavaScript'
description: V tomto rychlém startu se dozvíte, jak pomocí klientské knihovny Azure Blob Storage verze 12 pro JavaScript vytvořit kontejner a objekt BLOB v úložišti objektů BLOB (objekt). Dále se dozvíte, jak stáhnout objekt blob do místního počítače a jak zobrazit seznam všech objektů blob, které jsou v kontejneru.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/19/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 23cd292162a4c16c4b2b5b1f297d23eb2682013f
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168283"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-javascript"></a>Rychlý Start: Klientská knihovna pro úložiště objektů BLOB v Azure V12 pro JavaScript

Začněte s klientskou knihovnou V12 Azure Blob Storage pro JavaScript. Azure Blob Storage je řešení úložiště objektů Microsoftu pro cloud. Postupujte podle kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy. Úložiště objektů blob je optimalizované pro ukládání velkých objemů nestrukturovaných dat.

> [!NOTE]
> Informace o tom, jak začít s předchozí verzí sady SDK, najdete v tématu [rychlý Start: Klientská knihovna pro Azure Blob Storage pro JavaScript](storage-quickstart-blobs-nodejs-v10.md).

Použijte klientskou knihovnu služby Azure Blob Storage V12 pro JavaScript:

* Vytvoření kontejneru
* Nahrání objektu blob do Azure Storage
* Výpis všech objektů BLOB v kontejneru
* Stažení objektu blob do místního počítače
* Odstranění kontejneru

[Referenční dokumentace k rozhraní API](/javascript/api/@azure/storage-blob) |  | balíčku [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) [(Správce balíčků Node)](https://www.npmjs.com/package/@azure/storage-blob/v/12.0.0) | [ukázky](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Účet úložiště Azure – [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Aktuální [Node. js](https://nodejs.org/en/download/) pro váš operační systém.

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

1. Vytvořte nový textový soubor s názvem *Package. JSON*. Tento soubor definuje projekt Node. js. Uložte tento soubor v adresáři *objektů BLOB-rychlý Start-V12* . Tady je obsah souboru:

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
    
    Pokud chcete, můžete do pole `author` umístit vlastní název.
   
### <a name="install-the-package"></a>Instalace balíčku

Ještě pořád v adresáři *objektů BLOB – rychlý Start – V12* nainstalujte knihovnu klienta Azure Blob Storage pro balíček JavaScriptu pomocí příkazu `npm install`. Tento příkaz přečte soubor *Package. JSON* a nainstaluje balíček klientské knihovny V12 pro úložiště objektů BLOB v Azure pro JavaScript a všechny knihovny, na kterých závisí.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Z adresáře projektu:

1. Otevřít další nový textový soubor v editoru kódu
1. Přidání volání `require` pro načtení modulů Azure a Node. js
1. Vytvoření struktury pro program, včetně velmi základního zpracování výjimek

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

1. Uložte nový soubor jako *BLOB-Quickstart-V12. js* v adresáři *objektů BLOB-rychlý Start-V12* .

### <a name="copy-your-credentials-from-the-azure-portal"></a>Zkopírování přihlašovacích údajů z webu Azure Portal

Když ukázková aplikace odešle požadavek na Azure Storage, musí být ověřena. K autorizaci žádosti přidejte do aplikace přihlašovací údaje účtu úložiště jako připojovací řetězec. Přihlašovací údaje účtu úložiště zobrazíte pomocí následujícího postupu:

1. Přihlásit se na [Azure Portal](https://portal.azure.com).
2. Vyhledejte svůj účet úložiště.
3. V části **Nastavení** v přehledu účtu úložiště vyberte **Přístupové klíče**. Tady se zobrazí přístupové klíče vašeho účtu a úplný připojovací řetězec pro jednotlivé klíče.
4. V části **key1** vyhledejte hodnotu **Připojovací řetězec** a výběrem tlačítka **Kopírovat** zkopírujte připojovací řetězec. V dalším kroku přidáte hodnotu připojovacího řetězce do proměnné prostředí.

    ![Snímek obrazovky ukazující zkopírování připojovacího řetězce z webu Azure Portal](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

Po zkopírování připojovacího řetězce ho zapište do nové proměnné prostředí na místním počítači, na kterém aplikaci spouštíte. Proměnnou prostředí nastavíte tak, že otevřete okno konzoly a budete postupovat podle pokynů pro váš operační systém. Nahraďte `<yourconnectionstring>` skutečným připojovacím řetězcem.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Po přidání proměnné prostředí v systému Windows je nutné spustit novou instanci příkazového okna.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Restartovat programy

Po přidání proměnné prostředí restartujte všechny spuštěné programy, které budou muset číst proměnnou prostředí. Před pokračováním například restartujte vývojové prostředí nebo editor.

## <a name="object-model"></a>Objektový model

Úložiště objektů BLOB v Azure je optimalizované pro ukládání obrovských objemů nestrukturovaných dat. Nestrukturovaná data jsou data, která nevyhovují konkrétnímu datovému modelu nebo definici, jako jsou textová nebo binární data. Úložiště objektů BLOB nabízí tři typy prostředků:

* Účet úložiště
* Kontejner v účtu úložiště
* Objekt BLOB v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury služby Blob Storage](./media/storage-blob-introduction/blob1.png)

Pro interakci s těmito prostředky použijte následující třídy JavaScriptu:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): třída `BlobServiceClient` umožňuje manipulovat s prostředky Azure Storage a kontejnery objektů BLOB.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): třída `ContainerClient` umožňuje manipulovat s kontejnery Azure Storage a jejich objekty blob.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient): třída `BlobClient` umožňuje manipulovat s objekty blob Azure Storage.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provést následující akce s klientskou knihovnou služby Azure Blob Storage pro JavaScript:

* [Získání připojovacího řetězce](#get-the-connection-string)
* [Vytvoření kontejneru](#create-a-container)
* [Nahrání objektů blob do kontejneru](#upload-blobs-to-a-container)
* [Výpis objektů BLOB v kontejneru](#list-the-blobs-in-a-container)
* [Stáhnout objekty blob](#download-blobs)
* [Odstranění kontejneru](#delete-a-container)

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Následující kód načte připojovací řetězec pro účet úložiště z proměnné prostředí vytvořené v oddílu [konfigurace vašeho připojovacího řetězce úložiště](#configure-your-storage-connection-string) .

Přidejte tento kód do funkce `main`:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called CONNECT_STR. If the environment variable is
// created after the application is launched in a console or with Visual Studio,
// the shell or application needs to be closed and reloaded to take the
// environment variable into account.
const CONNECT_STR = process.env.CONNECT_STR;
```

### <a name="create-a-container"></a>Vytvoření kontejneru

Určete název nového kontejneru. Následující kód připojí hodnotu UUID k názvu kontejneru, aby bylo zajištěno, že je jedinečný.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Vytvořte instanci třídy [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) voláním metody [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) . Pak zavolejte metodu [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) a získejte odkaz na kontejner. Nakonec voláním metody [Create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) vytvořte kontejner ve svém účtu úložiště.

Přidejte tento kód na konec funkce `main`:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await BlobServiceClient.fromConnectionString(CONNECT_STR);

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

### <a name="upload-blobs-to-a-container"></a>Nahrání objektů blob do kontejneru

Následující fragment kódu:

1. Vytvoří textový řetězec, který se má nahrát do objektu BLOB.
1. Získá odkaz na objekt [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) voláním metody [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) na [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) v části [vytvoření kontejneru](#create-a-container) .
1. Nahraje data řetězce textu do objektu BLOB voláním metody [Upload](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) .

Přidejte tento kód na konec funkce `main`:

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

Seznam objektů BLOB v kontejneru zavoláním metody [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) . V tomto případě byl do kontejneru přidán pouze jeden objekt blob, takže operace výpisu vrátí pouze jeden objekt BLOB.

Přidejte tento kód na konec funkce `main`:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Stáhnout objekty blob

Stáhněte dřív vytvořený objekt BLOB voláním metody [Download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) . Vzorový kód obsahuje pomocnou funkci nazvanou `streamToString`, která se používá ke čtení datového proudu čitelného pro Node. js do řetězce.

Přidejte tento kód na konec funkce `main`:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Tuto pomocnou funkci přidejte *za* funkci `main`:

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

Přidejte tento kód na konec funkce `main`:

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>Spuštění kódu

Tato aplikace vytvoří textový řetězec a nahraje ho do úložiště objektů BLOB. Příklad následně vypíše objekty BLOB v kontejneru, stáhne objekt BLOB a zobrazí stažená data.

V příkazovém řádku konzoly přejděte do adresáře obsahujícího soubor *BLOB-Quickstart-V12.py* a spusťte aplikaci spuštěním následujícího příkazu `node`.

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

Projděte si kód v ladicím programu a ověřte Azure Portal v průběhu celého procesu. Zkontrolujte, zda se kontejner vytváří. Objekt blob můžete otevřít uvnitř kontejneru a zobrazit jeho obsah.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nahrávat, stahovat a vypisovat objekty BLOB pomocí JavaScriptu.

Pokud chcete zobrazit ukázkové aplikace služby Blob Storage, pokračujte:

> [!div class="nextstepaction"]
> [Ukázky V12 JavaScriptu pro Azure Blob Storage SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

* Další informace najdete v tématu [sada Azure SDK pro JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/README.md).
* Kurzy, ukázky, rychlé starty a další dokumentace najdete v [dokumentaci k sadě Azure SDK pro JavaScript](/azure/javascript/).
