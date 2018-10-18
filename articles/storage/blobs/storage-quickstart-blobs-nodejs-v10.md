---
title: Nahrání, stažení, vypsání a odstranění objektů blob pomocí sady Azure Storage v10 SDK pro JavaScript (verze Preview)
description: Vytvoření, nahrání a odstranění objektů blob a kontejnerů v Node.js pomocí služby Azure Storage
services: storage
author: craigshoemaker
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 09/19/2018
ms.author: cshoe
ms.openlocfilehash: 6e23e888a1c90e1c6c7eecf25491f048e9077f11
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857886"
---
# <a name="quickstart-upload-download-list-and-delete-blobs-using-azure-storage-v10-sdk-for-javascript-preview"></a>Rychlý start: Nahrání, stažení, vypsání a odstranění objektů blob pomocí sady Azure Storage v10 SDK pro JavaScript (verze Preview)

V tomto rychlém startu se dozvíte, jak sadu [Azure Storage v10 SDK pro JavaScript](https://github.com/Azure/azure-storage-js) v Node.js použít k nahrání, stažení, vypsání a odstranění objektů blob a správě kontejnerů.

K dokončení tohoto rychlého startu potřebujete [předplatné Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

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
Containers:
 - container-one
 - container-two
Container "demo" is created
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
Pokud pro tento rychlý start používáte nový účet úložiště, nemusí se názvy kontejnerů v části *Kontejnery* zobrazit.

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
    require('dotenv').load();
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
Žádosti tohoto rozhraní API lze nastavit tak, aby jim po daném intervalu vypršel časový limit. Třída [Aborter](/javascript/api/%40azure/storage-blob/aborter?view=azure-node-preview) se stará o vypršení časového limitu žádostí a následující konstanta slouží k definování časových limitů použitých v této ukázce.
```javascript
const ONE_MINUTE = 60 * 1000;
```
### <a name="calling-code"></a>Volání kódu

Pro podporu syntaxe *async/await* JavaScriptu se veškerý volající kód zabalí do funkce s názvem *execute*. Potom se zavolá funkce *execute* a zpracuje se jako příslib.

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

- Třída [SharedKeyCredential](/javascript/api/%40azure/storage-blob/sharedkeycredential?view=azure-node-preview) má na starosti zabalení přihlašovacích údajů k účtu úložiště a jejich poskytnutí kanálu žádosti.

- Třída [StorageURL](/javascript/api/%40azure/storage-blob/storageurl?view=azure-node-preview) zajišťuje vytvoření nového kanálu.

- Třída [ServiceURL](/javascript/api/%40azure/storage-blob/serviceurl?view=azure-node-preview) sestavuje adresu URL použitou v rozhraní REST API. Instance této třídy vám umožňují provádět akce, jako je výpis kontejnerů, a poskytují kontextové informace pro generování adres URL kontejnerů.

Instance *ServiceURL* se používá s instancemi [ContainerURL](/javascript/api/%40azure/storage-blob/containerurl?view=azure-node-preview) a [BlockBlobURL](/javascript/api/%40azure/storage-blob/blockbloburl?view=azure-node-preview) ke správě kontejnerů a objektů blob v účtu úložiště.

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

Žádosti tohoto rozhraní API lze nastavit tak, aby jim po daném intervalu vypršel časový limit. Třída *Aborter* obsluhuje vypršení časového limitu žádostí. Následující kód vytvoří kontext, ve kterém je sadě žádostí uděleno 30 minut k provedení.

```javascript
const aborter = Aborter.timeout(30 * ONE_MINUTE);
```
Prvky Aborter vám poskytují kontrolu nad žádostmi, protože umožňují:

- Určit množství času uděleného pro dávku žádostí
- Určit, jak dlouho se jednotlivá žádost má v dávce provádět
- Zrušit žádosti
- Zastavit vypršení časového limitu najednou u všech žádostí pomocí statického člena *Aborter.none*

### <a name="show-container-names"></a>Zobrazení názvů kontejnerů
V účtech může být uložený ohromný počet kontejnerů. Následující kód ukazuje, jak vypsat kontejnery segmentovaným způsobem, který umožňuje cyklicky procházet velké množství kontejnerů. Funkce *showContainerNames* se předává instancím *ServiceURL* a *Aborter*.

```javascript
console.log("Containers:");
await showContainerNames(serviceURL, aborter);
```
Funkce *showContainerNames* používá metodu *listContainersSegment* k vyžádání dávky názvů kontejnerů z účtu úložiště.
```javascript
async function showContainerNames(aborter, serviceURL) {

    let response;
    let marker;

    do {
        response = await serviceURL.listContainersSegment(aborter, marker);
        marker = response.marker;
        for(let container of response.containerItems) {
            console.log(` - ${ container.name }`);
        }
    } while (marker);
}
```
Při vrácení odpovědi se pomocí iterace *containerItems* vypíše název do konzoly. 

### <a name="create-a-container"></a>Vytvoření kontejneru

K vytvoření kontejneru se používá metoda *create* třídy *ContainerURL*.

```javascript
await containerURL.create(aborter);
console.log(`Container: "${containerName}" is created`);
```
Protože je název kontejneru definovaný při volání *ContainerURL.fromServiceURL(adresa URL služby, název kontejneru)*, stačí k vytvoření kontejneru zavolat metodu *create*.

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

    let response;
    let marker;

    do {
        response = await containerURL.listBlobFlatSegment(aborter);
        marker = response.marker;
        for(let blob of response.segment.blobItems) {
            console.log(` - ${ blob.name }`);
        }
    } while (marker);
}
```
### <a name="download-a-blob"></a>Stažení objektu blob
Po vytvoření objektu blob můžete jeho obsah stáhnout pomocí metody *download*.
```javascript
const downloadResponse = await blockBlobURL.download(aborter, 0);
const downloadedContent = downloadResponse.readableStreamBody.read(content.length).toString();
console.log(`Downloaded blob content: "${downloadedContent}"`);
```
Odpověď se vrátí v podobě streamu. V tomto příkladu je stream převedený na řetězec kvůli výpisu na konzole.
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
Veškerá data zapsaná do účtu úložiště se automaticky odstraní na konci této ukázky kódu. 

## <a name="next-steps"></a>Další kroky

Tento rychlý start ukazuje, jak spravovat objekty blob a kontejnery v úložišti objektů blob v Azure pomocí Node.js. Další informace o práci s touto sadou SDK najdete v úložišti GitHubu.

> [!div class="nextstepaction"]
> [Úložiště sady Azure Storage v10 SDK pro JavaScript](https://github.com/Azure/azure-storage-js)