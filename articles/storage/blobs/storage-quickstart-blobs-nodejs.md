---
title: Jak vytvořit objekt blob ve službě Azure Storage pomocí klientské knihovny pro Node.js v2
description: V tomto rychlém startu vytvoříte v úložišti objektů (blob) účet úložiště a kontejner. Potom použijte klientskou knihovnu pro úložiště Azure pro Node.js v2 k nahrání objektu blob do služby Azure Storage, stažení objektu blob a výpisu objektů BLOB v kontejneru.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: tamram
ms.openlocfilehash: 182315c705360d254c3bf342cd9c64ffafa0c021
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750070"
---
# <a name="how-to-upload-download-and-list-blobs-using-the-client-library-for-nodejs-v2"></a>Postup nahrání, stažení a výpis objektů BLOB pomocí klientské knihovny pro Node.js v2

V této příručce s postupy a zjistěte, jak používat klientskou knihovnu pro Node.js v2 k nahrávání, stahování a výpis objektů BLOB pomocí služby Azure Blob storage.

> [!TIP]
> Nejnovější verzi klientské knihovny Azure Storage pro Node.js je v10. Společnost Microsoft doporučuje, že používáte nejnovější verzi klientské knihovny, pokud je to možné. Chcete-li začít používat v10, přečtěte si téma [rychlý start: Nahrání, stažení, seznamu a odstraňování objektů BLOB pomocí klientské knihovny Azure Storage pro JavaScript v10 (preview)](storage-quickstart-blobs-nodejs-v10.md).

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Vytvořit účet úložiště Azure [webu Azure portal](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Nápovědu k vytvoření účtu najdete v tématu [Vytvoření účtu úložiště](../common/storage-quickstart-create-account.md).

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

[Ukázkovou aplikaci](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) je jednoduchou konzolovou aplikaci Node.js. Začněte tak, že do svého počítače naklonujete úložiště pomocí následujícího příkazu:

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Pokud chcete aplikaci otevřít, vyhledejte složku *storage-blobs-node-quickstart* a otevřete ji ve svém oblíbeném prostředí pro úpravy kódu.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

Před spuštěním aplikace je potřeba zadat připojovací řetězec pro váš účet úložiště. Ukázkové úložiště obsahuje soubor *.env.example*. Tento soubor můžete přejmenovat odebráním přípony *.example*, čímž získáte soubor *.env*. V souboru *.env* přidejte hodnotu svého připojovacího řetězce za klíč *AZURE_STORAGE_CONNECTION_STRING*.

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

Výstup skriptu se bude podobat tomuto:

```bash
Containers:
 - container-one
 - container-two
Container "demo" is created
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme.md
Blob downloaded blob content: "hello Blob SDK"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Pokud používáte nový účet úložiště pro účely tohoto příkladu a pak nezobrazí žádné názvy kontejnerů, které jsou uvedené pod popiskem "*kontejnery*".

## <a name="understanding-the-code"></a>Vysvětlení kódu
První výraz slouží načtení hodnot do proměnných prostředí.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
```

Modul *dotenv* načte proměnné prostředí při místním spuštění aplikace za účelem ladění. Hodnoty jsou definovány v souboru s názvem *.env* a načteny do aktuálního kontextu spuštění. V produkčních kontextech tyto hodnoty poskytuje konfigurace serveru a to je důvodem, proč se tento kód spouští, pouze pokud neběží v produkčním kontextu.

```javascript
const path = require('path');
const storage = require('azure-storage');
```

Účelem těchto modulů je následující: 

Načtení souboru s názvem *.env* do aktuálního kontextu spuštění
- Modul *path* se vyžaduje k určení absolutní cesty k souboru, který se má nahrát do úložiště objektů blob.
- *služby Azure storage* je [Klientská knihovna pro úložiště Azure](https://docs.microsoft.com/javascript/api/azure-storage) modul pro Node.js

Dále se proměnná **blobService** inicializuje jako nová instance služby Azure Blob.

```javascript
const blobService = storage.createBlobService();
```

V následující implementaci jsou všechny funkce *blobService* zabalené do funkce *Promise* (Příslib), která umožňuje přístup k funkci *async* a operátoru *await* JavaScriptu pro zjednodušení zpětného volání rozhraní [API služby Azure Storage](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest). Po návratu úspěšné odpovědi pro všechny funkce se příslib přeloží s použitím relevantních dat společně se zprávou specifickou pro danou akci.

### <a name="list-containers"></a>Výpis kontejnerů

Funkce *listContainers* zavolá metodu [listContainersSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest), která vrátí kolekci kontejnerů ve skupině.

```javascript
const listContainers = async () => {
    return new Promise((resolve, reject) => {
        blobService.listContainersSegmented(null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} containers`, containers: data.entries });
            }
        });
    });
};
```

Velikost skupin je možné konfigurovat prostřednictvím možnosti [ListContainersOptions](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.listcontaineroptions?view=azure-node-latest). Zavoláním funkce *listContainersSegmented* se vrátí metadata objektů blob jako pole instancí [ContainerResult](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.containerresult?view=azure-node-latest). Výsledky se vrací v dávkách (segmentech) v přírůstcích po 5 000. Pokud je v kontejneru více než 5 000 objektů blob, budou výsledky obsahovat hodnotu *continuationToken* (token pro pokračování). Pokud chcete vypsat další segmenty z kontejneru objektů blob, můžete token pro pokračování předat zpět do metody *listContainersSegment* jako druhý argument.

### <a name="create-a-container"></a>Vytvoření kontejneru

Funkce *createContainer* zavolá metodu [createContainerIfNotExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) a nastaví pro objekt blob odpovídající úroveň přístupu.

```javascript
const createContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

Druhý parametr (*options*) pro metodu **createContainerIfNotExists** přijímá hodnotu [publicAccessLevel](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest). Hodnota *blob* pro *publicAccessLevel* udává, že jsou data konkrétního objektu blob vystavená veřejnosti. Toto nastavení je v kontrastu s úrovní přístupu *container*, která přiděluje možnost vypisovat obsah kontejneru.

Použití metody **createContainerIfNotExists** umožňuje aplikaci několikrát spustit příkaz *createContainer* bez vracení chyb, pokud kontejner již existuje. V produkčním prostředí často voláte metodu **createContainerIfNotExists** pouze jednou, protože se stejný kontejner používá v rámci celé aplikace. V těchto případech můžete vytvořit kontejner předem prostřednictvím portálu nebo Azure CLI.

### <a name="upload-text"></a>Nahrání textu

Funkce *uploadString* zavolá metodu [createBlockBlobFromText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) a do kontejneru objektů blob zapíše libovolný řetězec (nebo ho přepíše).

```javascript
const uploadString = async (containerName, blobName, text) => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromText(containerName, blobName, text, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Text "${text}" is written to blob storage` });
            }
        });
    });
};
```
### <a name="upload-a-local-file"></a>Nahrání místního souboru

Funkce *uploadLocalFile* používá metodu [createBlockBlobFromLocalFile](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromlocalfile-string--string--string--errororresult-blobresult--) k nahrání a zapsání nebo přepsání souboru ze systému souborů do úložiště objektů blob. 

```javascript
const uploadLocalFile = async (containerName, filePath) => {
    return new Promise((resolve, reject) => {
        const fullPath = path.resolve(filePath);
        const blobName = path.basename(filePath);
        blobService.createBlockBlobFromLocalFile(containerName, blobName, fullPath, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Local file "${filePath}" is uploaded` });
            }
        });
    });
};
```
Mezi další dostupné přístupy k nahrávání obsahu do objektů blob patří práce s [textem](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest-string--string--string---buffer--errororresult-blobresult--) a [datovými proudy](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromstream-string--string--stream-readable--number--errororresult-blobresult--). Pokud chcete ověřit nahrání souboru do úložiště objektů blob, můžete pomocí [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) zobrazit data ve vašem účtu.

### <a name="list-the-blobs"></a>Výpis objektů blob

Funkce *listBlobs* zavolá metodu [listBlobsSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#listblobssegmented-string--continuationtoken--errororresult-listblobsresult--), která vrátí seznam metadat objektů blob v kontejneru. 

```javascript
const listBlobs = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} blobs in '${containerName}'`, blobs: data.entries });
            }
        });
    });
};
```

Zavoláním metody *listBlobsSegmented* se vrátí metadata objektů blob jako pole instancí [BlobResult](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.blobresult?view=azure-node-latest). Výsledky se vrací v dávkách (segmentech) v přírůstcích po 5 000. Pokud je v kontejneru více než 5 000 objektů blob, budou výsledky obsahovat hodnotu **continuationToken** (token pro pokračování). Pokud chcete vypsat další segmenty z kontejneru objektů blob, můžete token pro pokračování předat zpět do metody **listBlobSegmented** jako druhý argument.

### <a name="download-a-blob"></a>Stažení objektu blob

Funkce *download* používá metodu [getBlobToLocalFile](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) ke stažení obsahu objektu blob do zadané absolutní cesty k souboru.

```javascript
const downloadBlob = async (containerName, blobName) => {
    const dowloadFilePath = path.resolve('./' + blobName.replace('.txt', '.downloaded.txt'));
    return new Promise((resolve, reject) => {
        blobService.getBlobToText(containerName, blobName, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Blob downloaded "${data}"`, text: data });
            }
        });
    });
};
```
Implementace znázorněná v tomto příkladu změní zdroj a vrátí obsah objektu blob jako řetězec. Můžete také stáhnout objekt blob jako [stream](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) nebo přímo do [místního souboru](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest).

### <a name="delete-a-blob"></a>Odstranění objektu blob

Funkce *deleteBlob* zavolá funkci [deleteBlobIfExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#deleteblobifexists-string--string--errororresult-boolean--). Jak již název napovídá, tato funkce nevrací chybu, pokud je objekt blob již odstraněný.

```javascript
const deleteBlob = async (containerName, blobName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="delete-a-container"></a>Odstranění kontejneru

Kontejnery se odstraní zavoláním metody *deleteContainer* mimo službu objektu blob a předáním názvu kontejneru.

```javascript
const deleteContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteContainer(containerName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' deleted` });
            }
        });
    });
};
```

### <a name="calling-code"></a>Volání kódu

Aby bylo možné podporovat syntaxi *async/await* Javascriptu, zabalí se veškerý volající kód do funkce s názvem *execute*. Potom se zavolá funkce execute a zpracuje se jako příslib.

```javascript
async function execute() {
    // commands 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```
Veškerý následující kód se spustí uvnitř funkce execute, kde je umístěn komentář `// commands`.

Nejprve se relevantní proměnné deklarují pro přiřazení názvů, ukázkového obsahu a pro odkázání na místní soubor, který se má nahrát do úložiště objektů blob.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello Node SDK";
const localFilePath = "./readme.md";
let response;
```

Pro zobrazení seznamu kontejnerů v účtu úložiště se zavolá funkce listContainers a vrácený seznam kontejnerů se zaprotokoluje do okna výstupu.

```javascript
console.log("Containers:");
response = await listContainers();
response.containers.forEach((container) => console.log(` -  ${container.name}`));
```

Jakmile je seznam kontejnerů k dispozici, můžete pomocí metody *findIndex* pole zobrazit, zda kontejner, který chcete vytvořit, už existuje. Pokud kontejner neexistuje, vytvoří se.

```javascript
const containerDoesNotExist = response.containers.findIndex((container) => container.name === containerName) === -1;

if (containerDoesNotExist) {
    await createContainer(containerName);
    console.log(`Container "${containerName}" is created`);
}
```
Dále se řetězec a místní soubor nahrají do úložiště objektů blob.

```javascript
await uploadString(containerName, blobName, content);
console.log(`Blob "${blobName}" is uploaded`);

response = await uploadLocalFile(containerName, localFilePath);
console.log(response.message);
```
Proces zobrazení seznamu objektů blob je stejný jako zobrazení seznamu kontejnerů. Volání funkce *listBlobs* vrátí pole objektů blob v kontejneru, které se zaprotokolují do okna výstupu.

```javascript
console.log(`Blobs in "${containerName}" container:`);
response = await listBlobs(containerName);
response.blobs.forEach((blob) => console.log(` - ${blob.name}`));
```

Pokud chcete stáhnout objekt blob, odpověď se zachytí a použije pro přístup k hodnotě objektu blob. readableStreamBody z odpovědi se převede na řetězec a zaprotokoluje se do okna výstupu.

```javascript
response = await downloadBlob(containerName, blobName);
console.log(`Downloaded blob content: "${response.text}"`);
```

Nakonec se objekt blob i kontejner z účtu úložiště odstraní.

```javascript
await deleteBlob(containerName, blobName);
console.log(`Blob "${blobName}" is deleted`);

await deleteContainer(containerName);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Veškerá data zapsaná do účtu úložiště se automaticky odstraní na konci této ukázky kódu. 

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Zdroje informací pro vývoj aplikací Node.js s využitím objektů blob

Prohlédněte si tyto další zdroje informací o vývoji v Node.js s využitím úložiště objektů blob:

### <a name="binaries-and-source-code"></a>Binární soubory a zdrojový kód

- Prohlédněte si a nainstalujte [zdrojový kód klientské knihovny pro Node.js](https://github.com/Azure/azure-storage-node) pro službu Azure Storage na GitHubu.

### <a name="client-library-reference-and-samples"></a>Klientská knihovna – referenční informace a ukázky

- Další informace o klientské knihovně pro Node.js najdete v [referenčních informacích k rozhraní Node.js API](https://docs.microsoft.com/javascript/api/overview/azure/storage).
- Prozkoumejte [ukázky pro úložiště objektů blob](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob) napsané s využitím klientské knihovny pro Node.js.

## <a name="next-steps"></a>Další postup

Tento článek ukazuje, jak odeslat soubor mezi místním diskem a úložištěm objektů Blob v Azure pomocí Node.js. Další informace o práci s úložištěm objektů blob najdete v tématu věnovanému úložišti GitHub.

> [!div class="nextstepaction"]
> [Microsoft Azure Storage SDK pro Node.js a JavaScript pro prohlížeče](https://github.com/Azure/azure-storage-node)
