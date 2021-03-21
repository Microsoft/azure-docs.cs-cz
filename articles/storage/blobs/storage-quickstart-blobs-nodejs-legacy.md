---
title: 'Rychlý Start: Klientská knihovna pro úložiště objektů BLOB v Azure v10 za účelem pro JavaScript'
description: Vytváření, odesílání a odstraňování objektů BLOB a kontejnerů v Node.js pomocí Azure Storage klientské knihovny v10 za účelem pro JavaScript
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/19/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 2371c789b9e4a9fc70f4207fd8a634e419c97912
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98599446"
---
# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-nodejs"></a>Rychlý Start: Správa objektů BLOB pomocí sady JavaScript v10 za účelem SDK v Node.js

V tomto rychlém startu se naučíte spravovat objekty BLOB pomocí Node.js. Objekty blob jsou objekty, které mohou obsahovat velké objemy textových nebo binárních dat, včetně obrázků, dokumentů, datových proudů médií a dat archivu. Můžete nahrávat, stahovat, vypisovat a odstraňovat objekty BLOB a vy budete spravovat kontejnery.

> [!NOTE]
> V tomto rychlém startu se používá starší verze klientské knihovny pro úložiště objektů BLOB v Azure. Pokud chcete začít používat nejnovější verzi, přečtěte si téma [rychlý Start: Správa objektů BLOB pomocí sady JavaScript V12 SDK v Node.js](storage-quickstart-blobs-nodejs.md).

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Účet služby Azure Storage. [Vytvořte účet úložiště](../common/storage-account-create.md).
- [Node.js](https://nodejs.org/en/download/).

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

[Ukázková aplikace](https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git) v tomto rychlém startu je jednoduchá konzolová aplikace Node.js. Začněte tak, že do svého počítače naklonujete úložiště pomocí následujícího příkazu:

```bash
git clone https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git
```

Jako další změňte složky pro aplikaci:

```bash
cd azure-storage-js-v10-quickstart
```

Teď tuto složku otevřete ve svém oblíbeném prostředí pro úpravu kódu.

## <a name="configure-your-storage-credentials"></a>Konfigurace přihlašovacích údajů k úložišti

Před spuštěním aplikace musíte zadat zabezpečovací přihlašovací údaje pro účet úložiště. Ukázkové úložiště obsahuje soubor *.env.example*. Přejmenujte tento soubor odebráním přípony *.example*, čímž získáte soubor s názvem *.env*. Do souboru *.env* přidejte za klíče *AZURE_STORAGE_ACCOUNT_NAME* a *AZURE_STORAGE_ACCOUNT_ACCESS_KEY* název účtu a hodnoty přístupových klíčů.

## <a name="install-required-packages"></a>Instalace požadovaných balíčků

Spuštěním příkazu *npm install* v adresáři aplikace nainstalujte pro aplikaci požadované balíčky.

```bash
npm install
```

## <a name="run-the-sample"></a>Spuštění ukázky

Teď, když jsou závislosti nainstalované, můžete ukázku spustit následujícím příkazem:

```bash
npm start
```

Výstup z aplikace se bude podobat následujícímu příkladu:

```bash
Container "demo" is created
Containers:
 - container-one
 - container-two
 - demo
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme-stream.md
 - readme.md
Blob downloaded blob content: "hello!"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Pokud pro tento rychlý Start používáte nový účet úložiště, může se zobrazit jenom *ukázkový* kontejner uvedený pod popiskem *kontejnery:*.

## <a name="understanding-the-code"></a>Vysvětlení kódu

Ukázka začíná importem určitého počtu tříd a funkcí z oboru názvů úložiště objektů blob v Azure. Jednotlivé importované položky jsou vysvětleny na místě, kde se v této ukázce používají.

```javascript
const {
    Aborter,
    BlobURL,
    BlockBlobURL,
    ContainerURL,
    ServiceURL,
    SharedKeyCredential,
    StorageURL,
    uploadStreamToBlockBlob
} = require('@azure/storage-blob');
```

Přihlašovací údaje se načítají z proměnných prostředí na základě příslušného kontextu.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
```

Modul *dotenv* načte proměnné prostředí při místním spuštění aplikace za účelem ladění. Hodnoty jsou definovány v souboru s názvem *.env* a načteny do aktuálního kontextu spuštění. V produkčním prostředí poskytuje tyto hodnoty konfigurace serveru. Z tohoto důvodu se tento kód spustí jen tehdy, když skript *neběží* v „produkčním“ prostředí.

Další blok modulů je naimportovaný kvůli tomu, aby rozhraní mohlo pracovat se systémem souborů.

```javascript
const fs = require('fs');
const path = require('path');
```

Účel těchto modulů je následující: 

- *fs* je nativní modul Node.js sloužící k práci se systémem souborů.

- *path* se vyžaduje k určení absolutní cesty k souboru, která se používá k nahrání souboru do úložiště objektů blob.

Hodnoty proměnných prostředí se načítají a ukládají do konstant.

```javascript
const STORAGE_ACCOUNT_NAME = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const ACCOUNT_ACCESS_KEY = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
```
Další sada konstant pomáhá odhalit záměr výpočtů velikosti souborů během operací nahrávání.

```javascript
const ONE_MEGABYTE = 1024 * 1024;
const FOUR_MEGABYTES = 4 * ONE_MEGABYTE;
```

Žádosti vytvořené rozhraním API je možné nastavit tak, aby po uplynutí daného intervalu vypršel časový limit. Třída [Aborter](/javascript/api/%40azure/storage-blob/aborter?view=azure-node-legacy&preserve-view=true) se stará o vypršení časového limitu žádostí a následující konstanta slouží k definování časových limitů použitých v této ukázce.

```javascript
const ONE_MINUTE = 60 * 1000;
```

### <a name="calling-code"></a>Volání kódu

Pro podporu syntaxe *async/await* JavaScriptu se veškerý volající kód zabalí do funkce s názvem *execute*. Pak *je* volána a zpracována jako příslib.

```javascript
async function execute() {
    // commands... 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```

Veškerý následující kód se spustí uvnitř funkce execute, kde je umístěn komentář `// commands...`.

Nejprve se relevantní proměnné deklarují pro přiřazení názvů, ukázkového obsahu a pro odkázání na místní soubor, který se má nahrát do úložiště objektů blob.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello!";
const localFilePath = "./readme.md";
```

Pomocí přihlašovacích údajů k účtu se vytvoří kanál, který zodpovídá za odesílání žádostí do rozhraní REST API. Kanály jsou bezpečné pro přístup z více vláken a určují logiku pro zásady opakování, protokolování, pravidla deserializace odpovědí HTTP a další.

```javascript
const credentials = new SharedKeyCredential(STORAGE_ACCOUNT_NAME, ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(credentials);
const serviceURL = new ServiceURL(`https://${STORAGE_ACCOUNT_NAME}.blob.core.windows.net`, pipeline);
```

V tomto bloku kódu se používají následující třídy:

- Třída [SharedKeyCredential](/javascript/api/%40azure/storage-blob/sharedkeycredential?view=azure-node-legacy&preserve-view=true) má na starosti zabalení přihlašovacích údajů k účtu úložiště a jejich poskytnutí kanálu žádosti.

- Třída [StorageURL](/javascript/api/%40azure/storage-blob/storageurl?view=azure-node-legacy&preserve-view=true) zajišťuje vytvoření nového kanálu.

- Třída [ServiceURL](/javascript/api/%40azure/storage-blob/serviceurl?view=azure-node-legacy&preserve-view=true) sestavuje adresu URL použitou v rozhraní REST API. Instance této třídy vám umožňují provádět akce, jako je výpis kontejnerů, a poskytují kontextové informace pro generování adres URL kontejnerů.

Instance *ServiceURL* se používá s instancemi [ContainerURL](/javascript/api/%40azure/storage-blob/containerurl?view=azure-node-legacy&preserve-view=true) a [BlockBlobURL](/javascript/api/%40azure/storage-blob/blockbloburl?view=azure-node-legacy&preserve-view=true) ke správě kontejnerů a objektů blob v účtu úložiště.

```javascript
const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, blobName);
```

Proměnné *containerURL* a *blockBlobURL* se v této ukázce opakovaně používají k práci s účtem úložiště. 

V tomto okamžiku kontejner neexistuje v účtu úložiště. Instance *ContainerURL* představuje adresu URL, se kterou můžete pracovat. Pomocí této instance můžete kontejner vytvořit a odstranit. Umístění tohoto kontejneru odpovídá například tomuto:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo
```

Proměnná *blockBlobURL* slouží ke správě jednotlivých objektů blob a umožňuje nahrát, stáhnout a odstranit obsah objektu blob. Zde uvedená adresa URL se podobá následujícímu umístění:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo/quickstart.txt
```

Stejně jako kontejner tento objekt blob bloku zatím neexistuje. Proměnná *blockBlobURL* se později používá k vytvoření objektu blob nahráním obsahu.

### <a name="using-the-aborter-class"></a>Použití třídy Aborter

Žádosti vytvořené rozhraním API je možné nastavit tak, aby po uplynutí daného intervalu vypršel časový limit. Třída *Abort* zodpovídá za správu, jak vypršel časový limit požadavků. Následující kód vytvoří kontext, ve kterém je sada požadavků 30 minut spuštěna.

```javascript
const aborter = Aborter.timeout(30 * ONE_MINUTE);
```

Prvky Aborter vám poskytují kontrolu nad žádostmi, protože umožňují:

- Určit množství času uděleného pro dávku žádostí
- Určit, jak dlouho se jednotlivá žádost má v dávce provádět
- Zrušit žádosti
- Zastavit vypršení časového limitu najednou u všech žádostí pomocí statického člena *Aborter.none*

### <a name="create-a-container"></a>Vytvoření kontejneru

K vytvoření kontejneru se používá metoda *create* třídy *ContainerURL*.

```javascript
await containerURL.create(aborter);
console.log(`Container: "${containerName}" is created`);
```

Protože je název kontejneru definovaný při volání *ContainerURL.fromServiceURL(adresa URL služby, název kontejneru)*, stačí k vytvoření kontejneru zavolat metodu *create*.

### <a name="show-container-names"></a>Zobrazení názvů kontejnerů

V účtech může být uložený ohromný počet kontejnerů. Následující kód ukazuje, jak vypsat kontejnery segmentovaným způsobem, který umožňuje cyklicky procházet velké množství kontejnerů. Funkce *showContainerNames* se předává instancím *ServiceURL* a *Aborter*.

```javascript
console.log("Containers:");
await showContainerNames(serviceURL, aborter);
```

Funkce *showContainerNames* používá metodu *listContainersSegment* k vyžádání dávky názvů kontejnerů z účtu úložiště.

```javascript
async function showContainerNames(aborter, serviceURL) {
    let marker = undefined;

    do {
        const listContainersResponse = await serviceURL.listContainersSegment(aborter, marker);
        marker = listContainersResponse.nextMarker;
        for(let container of listContainersResponse.containerItems) {
            console.log(` - ${ container.name }`);
        }
    } while (marker);
}
```

Při vrácení odpovědi se pomocí iterace *containerItems* vypíše název do konzoly. 

### <a name="upload-text"></a>Nahrání textu

K nahrání textu do objektu blob se používá metoda *upload*.

```javascript
await blockBlobURL.upload(aborter, content, content.length);
console.log(`Blob "${blobName}" is uploaded`);
```

Zde se této metodě předává text a jeho délka.

### <a name="upload-a-local-file"></a>Nahrání místního souboru

K nahrání místního souboru do kontejneru potřebujete adresu URL kontejneru a cestu k souboru.

```javascript
await uploadLocalFile(aborter, containerURL, localFilePath);
console.log(`Local file "${localFilePath}" is uploaded`);
```

Funkce *uploadLocalFile* volá funkci *uploadFileToBlockBlob*, která jako argumenty přebírá cestu k souboru a instanci cíle objektu blob bloku.

```javascript
async function uploadLocalFile(aborter, containerURL, filePath) {

    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath);
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    return await uploadFileToBlockBlob(aborter, filePath, blockBlobURL);
}
```

### <a name="upload-a-stream"></a>Nahrání streamu

Nahrávání streamů je také podporované. Tato ukázka otevře místní soubor jako stream, který předá metodě pro nahrání.

```javascript
await uploadStream(containerURL, localFilePath, aborter);
console.log(`Local file "${localFilePath}" is uploaded as a stream`);
```

Funkce *uploadStream* volá *uploadStreamToBlockBlob* pro nahrání streamu do kontejneru úložiště.

```javascript
async function uploadStream(aborter, containerURL, filePath) {
    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath).replace('.md', '-stream.md');
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    const stream = fs.createReadStream(filePath, {
      highWaterMark: FOUR_MEGABYTES,
    });

    const uploadOptions = {
        bufferSize: FOUR_MEGABYTES,
        maxBuffers: 5,
    };

    return await uploadStreamToBlockBlob(
                    aborter, 
                    stream, 
                    blockBlobURL, 
                    uploadOptions.bufferSize, 
                    uploadOptions.maxBuffers);
}
```

Během nahrávání alokuje *uploadStreamToBlockBlob* vyrovnávací paměti pro uložení dat streamu do mezipaměti, pokud bude potřeba opakování. Hodnota *maxBuffers* určuje maximální počet použitých vyrovnávacích pamětí, protože každá vyrovnávací paměť vytváří samostatnou žádost o nahrání. V ideálním případě se větší počet vyrovnávacích pamětí projeví ve vyšší rychlosti, ale za cenu většího využití paměti. Při dostatečně vysokém počtu vyrovnávacích paměti se rychlost nahrávání ustálí a kritický bod se přesune z klienta na síť nebo disk.

### <a name="show-blob-names"></a>Zobrazení názvů objektů blob

Tak jako mohou účty obsahovat množství kontejnerů, může každý kontejner potenciálně obsahovat obrovské množství objektů blob. Přístup k jednotlivým objektům blob v kontejneru je dostupný přes instanci třídy *ContainerURL*.

```javascript
console.log(`Blobs in "${containerName}" container:`);
await showBlobNames(aborter, containerURL);
```

Funkce *showBlobNames* volá *listBlobFlatSegment* k vyžádání dávky objektů blob z kontejneru.

```javascript
async function showBlobNames(aborter, containerURL) {
    let marker = undefined;

    do {
        const listBlobsResponse = await containerURL.listBlobFlatSegment(Aborter.none, marker);
        marker = listBlobsResponse.nextMarker;
        for (const blob of listBlobsResponse.segment.blobItems) {
            console.log(` - ${ blob.name }`);
        }
    } while (marker);
}
```

### <a name="download-a-blob"></a>Stažení objektu blob

Po vytvoření objektu blob můžete jeho obsah stáhnout pomocí metody *download*.

```javascript
const downloadResponse = await blockBlobURL.download(aborter, 0);
const downloadedContent = await streamToString(downloadResponse.readableStreamBody);
console.log(`Downloaded blob content: "${downloadedContent}"`);
```

Odpověď se vrátí v podobě streamu. V tomto příkladu je datový proud převeden na řetězec pomocí následující pomocné funkce *streamToString* .

```javascript
// A helper method used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", data => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
}
```

### <a name="delete-a-blob"></a>Odstranění objektu blob

Metoda *delete* z instance *BlockBlobURL* odstraní objekt blob z kontejneru.

```javascript
await blockBlobURL.delete(aborter)
console.log(`Block blob "${blobName}" is deleted`);
```

### <a name="delete-a-container"></a>Odstranění kontejneru

Metoda *delete* z instance *ContainerURL* odstraní kontejner z účtu úložiště.

```javascript
await containerURL.delete(aborter);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Veškerá data zapsaná do účtu úložiště se automaticky odstraní na konci vzorku kódu. 

## <a name="next-steps"></a>Další kroky

Tento rychlý start ukazuje, jak spravovat objekty blob a kontejnery v úložišti objektů blob v Azure pomocí Node.js. Další informace o práci s touto sadou SDK najdete v úložišti GitHubu.

> [!div class="nextstepaction"]
> [Azure Storage v10 za účelem SDK pro úložiště JavaScriptu](https://github.com/Azure/azure-storage-js) 
>  [Reference k rozhraní API pro Azure Storage JavaScript](/javascript/api/overview/azure/storage-overview)
