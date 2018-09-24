---
title: Rychlý start Azure – Vytvoření objektu blob v úložišti objektů pomocí Node.js | Microsoft Docs
description: V tomto rychlém startu vytvoříte v úložišti objektů (blob) účet úložiště a kontejner. Pak použijete klientskou knihovnu pro úložiště pro Node.js k nahrání objektu blob do služby Azure Storage, stažení objektu blob a výpisu objektů blob v kontejneru.
services: storage
author: craigshoemaker
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: cshoe
ms.openlocfilehash: b1cb7d327d8bfd9a7c6fe9d466445c50620f8b45
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46976874"
---
# <a name="quickstart-upload-download-and-list-blobs-using-nodejs"></a>Rychlý start: Nahrávání, stahování a výpis objektů blob pomocí Node.js

V tomto rychlém startu zjistíte, jak pomocí Node.js nahrávat, stahovat a vypisovat objekty blob bloku v kontejneru s použitím úložiště objektů blob v Azure.

K dokončení tohoto rychlého startu potřebujete [předplatné Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

[Ukázková aplikace](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) v tomto rychlém startu je jednoduchá konzolová aplikace Node.js. Začněte tak, že do svého počítače naklonujete úložiště pomocí následujícího příkazu:

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
Teď, když jsou závislosti nainstalované, můžete ukázku spustit předáním příkazů do skriptu. Například kontejner objektů blob můžete vytvořit spuštěním následujícího příkazu:

```bash
node index.js --command createContainer
```

Mezi dostupné příkazy patří:

| Příkaz | Popis |
|---------|---------|
|*createContainer* | Vytvoří kontejner *test-container* (příkaz proběhne úspěšně i v případě, že kontejner již existuje). |
|*upload*          | Nahraje soubor *example.txt* do kontejneru *test-container*. |
|*download*        | Stáhne obsah objektu blob *example* do souboru *example.downloaded.txt*. |
|*odstranění*          | Odstraní objekt blob *example*. |
|*list*            | Vypíše do konzoly obsah kontejneru *test-container*. |


## <a name="understanding-the-sample-code"></a>Vysvětlení vzorového kódu
Tento vzorový kód k vytvoření rozhraní pro systém souborů a příkazový řádek používá několik modulů. 

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
const path = require('path');
const args = require('yargs').argv;
const storage = require('azure-storage');
```

Účelem těchto modulů je následující: 

- Modul *dotenv* načte proměnné prostředí definované v souboru *.env* do aktuálního kontextu spuštění.
- Modul *path* se vyžaduje k určení absolutní cesty k souboru, který se má nahrát do úložiště objektů blob.
- Modul *yargs* zveřejňuje jednoduché rozhraní pro přístup k argumentům příkazového řádku.
- Modul *azure-storage* je modul sady [SDK služby Azure Storage](https://docs.microsoft.com/javascript/api/azure-storage) pro Node.js.

Dále se inicializuje sada proměnných:

```javascript
const blobService = storage.createBlobService();
const containerName = 'test-container';
const sourceFilePath = path.resolve('./example.txt');
const blobName = path.basename(sourceFilePath, path.extname(sourceFilePath));
```

Tyto proměnné se nastaví na následující hodnoty:

- Proměnná *blobService* se nastaví na novou instanci služby Azure Blob service.
- Proměnná *containerName* se nastaví na název kontejneru.
- Proměnná *sourceFilePath* se nastaví na absolutní cestu k souboru, který se má nahrát.
- Proměnná *blobName* se vytvoří z názvu souboru odebráním přípony.

V následující implementaci jsou všechny funkce *blobService* zabalené do funkce *Promise* (Příslib), která umožňuje přístup k funkci *async* a operátoru *await* JavaScriptu pro zjednodušení zpětného volání rozhraní [API služby Azure Storage](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest). Po návratu úspěšné odpovědi pro všechny funkce se příslib přeloží s použitím relevantních dat společně se zprávou specifickou pro danou akci.

### <a name="create-a-blob-container"></a>Vytvoření kontejneru objektů blob

Funkce *createContainer* zavolá metodu [createContainerIfNotExists](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createcontainerifnotexists) a nastaví pro objekt blob odpovídající úroveň přístupu.

```javascript
const createContainer = () => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

Druhý parametr (*options*) pro metodu **createContainerIfNotExists** přijímá hodnotu [publicAccessLevel](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createcontainerifnotexists). Hodnota *blob* pro *publicAccessLevel* udává, že jsou data konkrétního objektu blob vystavená veřejnosti. Toto nastavení je v kontrastu s úrovní přístupu *container*, která přiděluje možnost vypisovat obsah kontejneru.

Použití metody **createContainerIfNotExists** umožňuje aplikaci několikrát spustit příkaz *createContainer* bez vracení chyb, pokud kontejner již existuje. V produkčním prostředí často voláte metodu **createContainerIfNotExists** pouze jednou, protože se stejný kontejner používá v rámci celé aplikace. V těchto případech můžete vytvořit kontejner předem prostřednictvím portálu nebo Azure CLI.

### <a name="upload-a-blob-to-the-container"></a>Nahrání objektu blob do kontejneru

Funkce *upload* používá funkci [createBlockBlobFromLocalFile](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromlocalfile) k nahrání a zapsání nebo přepsání souboru ze systému souborů do úložiště objektů blob. 

```javascript
const upload = () => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromLocalFile(containerName, blobName, sourceFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Upload of '${blobName}' complete` });
            }
        });
    });
};
```
V kontextu ukázkové aplikace se nahraje soubor *example.txt* do objektu blob *example* v kontejneru *test-container*. Mezi další dostupné přístupy k nahrávání obsahu do objektů blob patří práce s [textem](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromtext) a [datovými proudy](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromstream).

Pokud chcete ověřit nahrání souboru do úložiště objektů blob, můžete pomocí [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) zobrazit data ve vašem účtu.

### <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Funkce *list* zavolá metodu [listBlobsSegmented](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#listblobssegmented), která vrátí seznam metadat objektů blob v kontejneru. 

```javascript
const list = () => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Items in container '${containerName}':`, data: data });
            }
        });
    });
};
```

Zavoláním metody *listBlobsSegmented* se vrátí metadata objektů blob jako pole instancí [BlobResult](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.blobresult?view=azure-node-latest). Výsledky se vrací v dávkách (segmentech) v přírůstcích po 5 000. Pokud je v kontejneru více než 5 000 objektů blob, budou výsledky obsahovat hodnotu **continuationToken** (token pro pokračování). Pokud chcete vypsat další segmenty z kontejneru objektů blob, můžete token pro pokračování předat zpět do metody **listBlobSegmented** jako druhý argument.

### <a name="download-a-blob-from-the-container"></a>Stažení objektu blob z kontejneru

Funkce *download* používá metodu [getBlobToLocalFile](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#getblobtolocalfile) ke stažení obsahu objektu blob do zadané absolutní cesty.

```javascript
const download = () => {
    const dowloadFilePath = sourceFilePath.replace('.txt', '.downloaded.txt');
    return new Promise((resolve, reject) => {
        blobService.getBlobToLocalFile(containerName, blobName, dowloadFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Download of '${blobName}' complete` });
            }
        });
    });
};
```
Zde uvedená implementace změní cestu ke zdrojovému souboru tak, že k názvu souboru připojí *.downloaded.txt*. V reálných kontextech můžete umístění i název souboru změnit při výběru cíle stahování.

### <a name="delete-blobs-in-the-container"></a>Odstranění objektů blob v kontejneru

Funkce *deleteBlock* (v příkazu konzoly má alias *delete*) volá funkci [deleteBlobIfExists](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#deleteblobifexists). Jak již název napovídá, tato funkce nevrací chybu, pokud je objekt blob již odstraněný.

```javascript
const deleteBlock = () => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="upload-and-list"></a>Nahrání a výpis

Jednou z výhod používání příslibů je možnost řetězení příkazů. Funkce **uploadAndList** ukazuje, jak snadné je vypsat obsah objektu blob přímo po nahrání souboru.

```javascript
const uploadAndList = () => {
    return _module.upload().then(_module.list);
};
```

### <a name="calling-code"></a>Volání kódu

Za účelem zveřejnění implementovaných funkcí do příkazového řádku se jednotlivé funkce mapují jako literály objektu.

```javascript
const _module = {
    "createContainer": createContainer,
    "upload": upload,
    "download": download,
    "delete": deleteBlock,
    "list": list,
    "uploadAndList": uploadAndList
};
```

Když teď máte objekt *_module*, všechny příkazy jsou dostupné z příkazového řádku.

```javascript
const commandExists = () => exists = !!_module[args.command];
```

Pokud zadaný příkaz neexistuje, do konzoly se vypíší vlastnosti objektu *_module* sloužící jako text nápovědy pro uživatele. 

Funkce *executeCommand* je *asynchronní* funkce, která volá zadaný příkaz s použitím operátoru *await* a protokoluje všechny zprávy pro data do konzoly.

```javascript
const executeCommand = async () => {
    const response = await _module[args.command]();

    console.log(response.message);

    if (response.data) {
        response.data.entries.forEach(entry => {
            console.log('Name:', entry.name, ' Type:', entry.blobType)
        });
    }
};
```

Nakonec prováděný kód nejprve zavoláním funkce *commandExists* ověří předání známého příkazu do skriptu. Pokud je vybraný existující příkaz, pak se příkaz spustí a případné chyby se zaprotokolují do konzoly.

```javascript
try {
    const cmd = args.command;

    console.log(`Executing '${cmd}'...`);

    if (commandExists()) {
        executeCommand();
    } else {
        console.log(`The '${cmd}' command does not exist. Try one of these:`);
        Object.keys(_module).forEach(key => console.log(` - ${key}`));
    }
} catch (e) {
    console.log(e);
}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud neplánujete používat data nebo účty vytvořené v rámci tohoto článku, můžete je odstranit, abyste se vyhnuli nežádoucímu účtování. K odstranění objektů blob a kontejnerů můžete použít metody [deleteBlobIfExists](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#deleteblobifexists) a [deleteContainerIfExists](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#deletecontainerifexists). [Prostřednictvím portálu](../common/storage-create-storage-account.md) můžete odstranit také účet úložiště.

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Zdroje informací pro vývoj aplikací Node.js s využitím objektů blob

Prohlédněte si tyto další zdroje informací o vývoji v Node.js s využitím úložiště objektů blob:

### <a name="binaries-and-source-code"></a>Binární soubory a zdrojový kód

- Prohlédněte si a nainstalujte [zdrojový kód klientské knihovny pro Node.js](https://github.com/Azure/azure-storage-node) pro službu Azure Storage na GitHubu.

### <a name="client-library-reference-and-samples"></a>Klientská knihovna – referenční informace a ukázky

- Další informace o klientské knihovně pro Node.js najdete v [referenčních informacích k rozhraní Node.js API](https://docs.microsoft.com/javascript/api/overview/azure/storage).
- Prozkoumejte [ukázky pro úložiště objektů blob](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob) napsané s využitím klientské knihovny pro Node.js.

## <a name="next-steps"></a>Další kroky

Tyto rychlé starty ukazují, jak odeslat soubor mezi místním diskem a úložištěm objektů blob v Azure pomocí Node.js. Další informace o práci s úložištěm objektů blob najdete v postupech pro úložiště objektů blob.

> [!div class="nextstepaction"]
> [Operace s úložištěm objektů blob – postupy](storage-nodejs-how-to-use-blob-storage.md)

Referenční informace k Node.js pro službu Azure Storage najdete v [balíčku azure-storage](https://docs.microsoft.com/javascript/api/azure-storage).
