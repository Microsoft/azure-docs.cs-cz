---
title: "Rychlý start Azure – Nahrávání, stahování a výpis objektů blob v Azure Storage pomocí Node.js | Microsoft Docs"
description: "V tomto rychlém startu vytvoříte účet úložiště a kontejner. Pak použijete klientskou knihovnu pro úložiště pro Node.js k nahrání objektu blob do služby Azure Storage, stažení objektu blob a výpisu objektů blob v kontejneru."
services: storage
author: craigshoemaker
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: cshoe
ms.openlocfilehash: ad0d4a2242aef99e0307f732175e0c50010580ce
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-nodejs"></a>Rychlý start: Nahrávání, stahování a výpis objektů blob pomocí Node.js

V tomto rychlém startu zjistíte, jak pomocí Node.js nahrávat, stahovat a vypisovat objekty blob bloku v kontejneru v úložišti objektů blob v Azure.

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* Instalovat [Node.js](https://nodejs.org/en/)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

[Ukázková aplikace](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) použitá v tomto rychlém startu je základní aplikace Node.js. 

Pomocí [gitu](https://git-scm.com/) stáhněte kopii aplikace do svého vývojového prostředí.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Tento příkaz naklonuje úložiště do vaší místní složky gitu. Pokud chcete aplikaci otevřít, vyhledejte složku storage-blobs-node-quickstart, otevřete ji a dvakrát klikněte na soubor index.js.

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

V aplikaci je potřeba zadat připojovací řetězec pro váš účet úložiště. Otevřete soubor `index.js` a vyhledejte proměnnou `connectionString`. Nahraďte její hodnotu úplnou hodnotou připojovacího řetězce, který jste si uložili z webu Azure Portal. Váš připojovací řetězec úložiště by měl vypadat nějak takto:

```javascript
// Create a blob client for interacting with the blob service from connection string
// How to create a storage connection string - http://msdn.microsoft.com/library/azure/ee758697.aspx
var connectionString = '<Your connection string here>';
var blobService = storage.createBlobService(connectionString);
```

## <a name="install-required-packages"></a>Instalace požadovaných balíčků

Spuštěním `npm install` v adresáři aplikace nainstalujte požadované balíčky uvedené v souboru `package.json`.

```javascript
npm install
```

## <a name="run-the-sample"></a>Spuštění ukázky

Tato ukázka vytvoří testovací soubor ve složce Dokumenty, nahraje ho do úložiště objektů blob, vypíše objekty blob v kontejneru a pak soubor stáhne s novým názvem, abyste mohli porovnat starý a nový soubor.

Spusťte ukázku zadáním `node index.js`. Systém Windows zobrazí následující výstup.  Při použití Linuxu se zobrazí podobný výstup s odpovídajícími cestami k souborům.

```
Azure Storage Node.js Client Library Blobs Quick Start

1. Creating a container with public access: quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d

2. Creating a file in ~/Documents folder to test the upload and download

   Local File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

3. Uploading BlockBlob: quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

   Uploaded Blob URL: https://mystorageaccount.blob.core.windows.net/quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d/quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

4. Listing blobs in container

   - quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt (type: BlockBlob)


5. Downloading blob

   Downloaded File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d_DOWNLOADED.txt

Sample finished running. When you hit <ENTER> key, the temporary files will be deleted and the sample application will exit.
```

Než budete pokračovat, zkontrolujte, jestli složka Dokumenty obsahuje příslušné dva soubory. Můžete je otevřít a podívat se, že jsou identické.

K zobrazení souborů v úložišti objektů blob můžete použít také nástroj, jako je [Průzkumník služby Azure Storage](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Průzkumník služby Azure Storage je bezplatný nástroj pro více platforem, který umožňuje přístup k informacím o účtu úložiště.

Po ověření souborů stiskněte libovolnou klávesu a dokončete ukázku a odstraňte testovací soubory. Když teď víte, co ukázka dělá, otevřete soubor index.js a prohlédněte si kód. 

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

Dále si projdeme vzorový kód, abyste pochopili, jak funguje.

### <a name="get-references-to-the-storage-objects"></a>Získání odkazů na objekty úložiště

První věc, kterou je potřeba udělat, je vytvořit odkaz na objekt **BlobService** sloužící k přístupu k úložišti objektů blob a jeho správě. Tyto objekty se vzájemně využívají a každý z nich je využívaný dalším objektem v seznamu.

* Vytvořte instanci objektu [BlobService](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService__ctor), která odkazuje na službu Blob ve vašem účtu úložiště.

* Vytvořte nový kontejner a pak nastavte oprávnění ke kontejneru tak, aby objekty blob byly veřejné a přístupné přes pouhou adresu URL. Název kontejneru začíná na **quickstartcontainer-**.

Tento příklad používá [createContainerIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists), protože chceme při každém spuštění ukázky vytvořit nový kontejner. V produkčním prostředí, kde používáte stejný kontejner v celé aplikaci, je lepší volat createContainerIfNotExists pouze jednou. Případně můžete kontejner vytvořit předem, abyste ho nemuseli vytvářet v kódu.

```javascript
// Create a container for organizing blobs within the storage account.
console.log('1. Creating a Container with Public Access:', blockBlobContainerName, '\n');
blobService.createContainerIfNotExists(blockBlobContainerName, { 'publicAccessLevel': 'blob' }, function (error) {
    if (error) return callback(error);
```

### <a name="upload-blobs-to-the-container"></a>Nahrání objektů blob do kontejneru

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Objekty blob bloku se používají nejčastěji. Jsou ideální k ukládání textových a binárních dat, což je důvodem jejich použití v tomto rychlém startu.

Soubor nahrajete do objektu blob pomocí metody [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile). Tato operace vytvoří objekt blob, pokud ještě neexistuje, nebo ho přepíše, pokud už existuje.

Vzorový kód vytvoří místní soubor, který se použije k nahrání a stažení. Soubor k nahrání uloží do proměnné **localPath** a název objektu blob do proměnné **localFileToUpload**. Následující příklad nahraje soubor do kontejneru, jehož název začíná na **quickstartcontainer-**.

```javascript
console.log('2. Creating a file in ~/Documents folder to test the upload and download\n');
console.log('   Local File:', LOCAL_FILE_PATH, '\n');
fs.writeFileSync(LOCAL_FILE_PATH, 'Greetings from Microsoft!');

console.log('3. Uploading BlockBlob:', BLOCK_BLOB_NAME, '\n');
blobService.createBlockBlobFromLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, LOCAL_FILE_PATH, function (error) {
handleError(error);
console.log('   Uploaded Blob URL:', blobService.getUrl(CONTAINER_NAME, BLOCK_BLOB_NAME), '\n');
```

V případě úložiště objektů blob můžete k nahrání použít několik metod. Například pokud máte paměťový proud, můžete místo [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile) použít metodu [createBlockBlobFromStream](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromStream).

### <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Aplikace dále získá seznam souborů v kontejneru pomocí [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented). Následující kód načte seznam objektů blob, pak je ve smyčce projde a zobrazí identifikátory URI nalezených objektů blob. Soubor můžete zobrazit zkopírováním identifikátoru URI z příkazového okna a jeho vložením do prohlížeče.

Pokud máte v kontejneru 5 000 nebo méně objektů blob, všechny názvy objektů blob se načtou jedním zavoláním [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented). Pokud máte v kontejneru více než 5 000 objektů blob, služba načítá seznam ve skupinách po 5 000, dokud se nenačtou názvy všech objektů blob. Takže při prvním volání rozhraní API se vrátí názvy prvních 5 000 objektů blob a token pro pokračování. Při druhém volání zadáte token a služba načte další sadu názvů objektů blob a tak dále, dokud token pro pokračování nemá hodnotu null, která indikuje, že se už načetly všechny názvy objektů blob.

```javascript
console.log('4. Listing blobs in container\n');
blobService.listBlobsSegmented(CONTAINER_NAME, null, function (error, data) {
    handleError(error);

    for (var i = 0; i < data.entries.length; i++) {
    console.log(util.format('   - %s (type: %s)'), data.entries[i].name, data.entries[i].blobType);
    }
    console.log('\n');
```

### <a name="download-blobs"></a>Stáhnout objekty blob

Stáhněte objekty blob na místní disk pomocí [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_getBlobToLocalFile).

Následující kód stáhne objekt blob nahraný v předchozí části a k názvu objektu blob přidá příponu „_DOWNLOADED“, takže na místním disku uvidíte oba soubory. 

```javascript
console.log('5. Downloading blob\n');
blobService.getBlobToLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, DOWNLOADED_FILE_PATH, function (error) {
handleError(error);
console.log('   Downloaded File:', DOWNLOADED_FILE_PATH, '\n');
```

### <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete objekty blob nahrané v rámci tohoto rychlého startu, můžete celý kontejner odstranit pomocí [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) a [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteContainerIfExists). Odstraňte také vytvořené soubory, pokud je už nepotřebujete. Aplikace se o to postará při ukončení stisknutím klávesy Enter.

```javascript
console.log('6. Deleting block Blob\n');
    blobService.deleteBlobIfExists(CONTAINER_NAME, BLOCK_BLOB_NAME, function (error) {
        handleError(error);

    console.log('7. Deleting container\n');
    blobService.deleteContainerIfExists(CONTAINER_NAME, function (error) {
        handleError(error);
            
        fs.unlinkSync(LOCAL_FILE_PATH);
        fs.unlinkSync(DOWNLOADED_FILE_PATH);
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak přenášet soubory mezi místním diskem a úložištěm objektů blob v Azure pomocí Node.js. Další informace o práci s úložištěm objektů blob najdete v postupech pro úložiště objektů blob.

> [!div class="nextstepaction"]
> [Operace s úložištěm objektů blob – postupy](storage-nodejs-how-to-use-blob-storage.md)

Referenční informace k Node.js pro službu Azure Storage najdete v [balíčku azure-storage](https://docs.microsoft.com/javascript/api/azure-storage/?view=azure-node-latest).