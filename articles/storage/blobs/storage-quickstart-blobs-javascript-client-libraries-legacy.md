---
title: 'Rychlý Start: Azure Blob Storage pro JavaScript v10 za účelem v prohlížeči'
description: Naučte se nahrávat, vypisovat a odstraňovat objekty BLOB pomocí JavaScriptu v10 za účelem SDK na stránce HTML.
services: storage
author: mhopkins-msft
ms.custom: mvc, devx-track-js
ms.service: storage
ms.author: mhopkins
ms.date: 07/24/2020
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: 134408704d54479fbbe0dfb5094f2920fa2e74be
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91336186"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-browser"></a>Rychlý Start: Správa objektů BLOB pomocí sady JavaScript v10 za účelem SDK v prohlížeči

V tomto rychlém startu se naučíte spravovat objekty BLOB pomocí kódu JavaScriptu, který běží zcela v prohlížeči. Objekty blob jsou objekty, které mohou obsahovat velké objemy textových nebo binárních dat, včetně obrázků, dokumentů, datových proudů médií a dat archivu. Pro zajištění chráněného přístupu k účtu úložiště objektů BLOB budete používat požadovaná bezpečnostní opatření.

> [!NOTE]
> V tomto rychlém startu se používá starší verze klientské knihovny pro úložiště objektů BLOB v Azure. Pokud chcete začít používat nejnovější verzi, přečtěte si téma [rychlý Start: Správa objektů BLOB pomocí JavaScriptu V12 SDK v prohlížeči](quickstart-blobs-javascript-browser.md).

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Účet služby Azure Storage. [Vytvořte účet úložiště](../common/storage-account-create.md).
- Místní webový server. Tento článek používá [Node.js](https://nodejs.org) k otevření základního serveru.
- [Visual Studio Code](https://code.visualstudio.com).
- Rozšíření VS Code pro ladění prohlížeče, jako je například [ladicí program pro Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome) nebo [ladicí program pro Microsoft Edge](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-edge).

## <a name="setting-up-storage-account-cors-rules"></a>Nastavení pravidel CORS pro účet úložiště

Předtím, než může vaše webová aplikace získat přístup k úložišti objektů BLOB z klienta, musíte nakonfigurovat účet, aby povoloval [sdílení prostředků mezi zdroji](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)nebo CORS.

Vraťte se na web Azure Portal a vyberte svůj účet úložiště. Pokud chcete definovat nové pravidlo CORS, přejděte do části **Nastavení** a klikněte na odkaz **CORS** . Pak kliknutím na tlačítko **Přidat** otevřete okno **Přidat pravidlo CORS**. Pro účely tohoto rychlého startu vytvoříte otevřené pravidlo CORS:

![Nastavení CORS pro účet služby Azure Blob Storage](media/storage-quickstart-blobs-javascript-client-libraries-v10/azure-blob-storage-cors-settings.png)

Následující tabulka obsahuje popis jednotlivých nastavení CORS a vysvětlení hodnot použitých k definici pravidla.

|Nastavení  |Hodnota  | Popis |
|---------|---------|---------|
| Povolené zdroje | * | Přijímá čárkami oddělený seznam domén nastavených jako přijatelné zdroje. Nastavením této hodnoty na `*` se povolí přístup k účtu úložiště ze všech domén. |
| Povolené metody     | delete, get, head, merge, post, options a put | Uvádí povolené příkazy HTTP, které je možné spouštět proti účtu úložiště. Pro účely tohoto rychlého startu vyberte všechny dostupné možnosti. |
| Povolené hlavičky | * | Definuje seznam hlaviček požadavku (včetně hlaviček s předponou), které účet úložiště povoluje. Nastavením této hodnoty na `*` se povolí přístup všem hlavičkám. |
| Zveřejněné hlavičky | * | Uvádí hlavičky odpovědi, které účet povoluje. Nastavením této hodnoty na `*` se účtu povolí odesílání jakýchkoli hlaviček.  |
| Maximální stáří (sekundy) | 86400 | Maximální doba, po kterou prohlížeč uchovává předběžné požadavky OPTIONS v mezipaměti. Hodnota *86400* povolí uchování v mezipaměti po celý den. |

> [!IMPORTANT]
> Zajistěte, aby všechna nastavení, která používáte v produkčním prostředí, zveřejnila minimální přístup k účtu úložiště, který je nezbytný k udržení zabezpečeného přístupu. Zde popsaná nastavení CORS jsou vhodná pro účely rychlého startu, protože definují mírné zásady zabezpečení. Tato nastavení se však nedoporučují pro kontext skutečného světa.

Dále pomocí služby Azure Cloud Shell vytvoříte token zabezpečení.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Vytvoření sdíleného přístupového podpisu

Sdílený přístupový podpis (SAS) se používá v kódu spuštěném v prohlížeči k autorizaci požadavků na úložiště objektů blob. Díky použití SAS může klient autorizovat přístup k prostředkům úložiště, aniž by měl přístupový klíč nebo připojovací řetězec účtu. Další informace o SAS najdete v tématu [Použití sdílených přístupových podpisů (SAS)](../common/storage-sas-overview.md).

SAS můžete vytvořit pomocí Azure CLI prostřednictvím služby Azure Cloud Shell nebo pomocí Azure Portal nebo Průzkumník služby Azure Storage. Následující tabulka popisuje parametry, které potřebujete k zadání hodnot pro vygenerování SAS pomocí rozhraní příkazového řádku.

| Parametr      |Popis  | Zástupný symbol |
|----------------|-------------|-------------|
| *vypršení platnosti*       | Datum vypršení platnosti přístupového tokenu ve formátu RRRR-MM-DD. Pro účely tohoto rychlého startu zadejte zítřejší datum. | *FUTURE_DATE* |
| *název účtu* | Název účtu úložiště. Použijte název, který jste si poznamenali v dřívějším kroku. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | Klíč účtu úložiště. Použijte klíč, který jste si poznamenali v dřívějším kroku. | *YOUR_STORAGE_ACCOUNT_KEY* |

Pomocí následujícího příkazu rozhraní příkazového řádku se skutečnými hodnotami pro jednotlivé zástupné znaky vygenerujte SAS, které můžete použít v kódu JavaScriptu.

```azurecli-interactive
az storage account generate-sas \
  --permissions racwdl \
  --resource-types sco \
  --services b \
  --expiry FUTURE_DATE \
  --account-name YOUR_STORAGE_ACCOUNT_NAME \
  --account-key YOUR_STORAGE_ACCOUNT_KEY
```

Několik hodnot uvedených za jednotlivými parametry je možná trochu nesrozumitelných. Tyto hodnoty parametrů představují první písmeno odpovídajícího oprávnění. Následující tabulka vysvětluje, odkud hodnoty pochází:

| Parametr        | Hodnota   | Popis  |
|------------------|---------|---------|
| *nastaven*    | racwdl  | Tento SAS povoluje funkce *read* (čtení), *append* (připojení), *create* (vytvoření), *write* (zápis), *delete* (odstranění) a *list* (výpis). |
| *resource-types* | sco     | Tento SAS ovlivňuje prostředky *service* (služba), *container* (kontejner) a *object* (objekt). |
| *orgány*       | b       | Tento SAS ovlivňuje službu *Blob* Service. |

Teď, když se SAS vygeneruje, zkopírujte vrácenou hodnotu a uložte ji někam pro použití v nadcházejícím kroku. Pokud jste vygenerovali SAS pomocí jiné metody než Azure CLI, bude nutné odebrat počáteční, `?` Pokud je k dispozici. Tento znak je oddělovač adres URL, který je již poskytnutý v šabloně adresy URL dále v tomto tématu, kde se používá SAS.

> [!IMPORTANT]
> V produkčním prostředí vždy předejte tokeny SAS pomocí protokolu TLS. Kromě toho by se tokeny SAS měly generovat na serveru a odesílat na stránku HTML, která je předá zpět do služby Azure Blob Storage. Jedním z přístupů, které můžete zvážit, je generování tokenů SAS pomocí funkce bez serveru. Azure Portal obsahuje šablony funkcí, které zahrnují možnost generovat SAS pomocí funkce JavaScriptu.

## <a name="implement-the-html-page"></a>Implementace stránky HTML

V této části vytvoříte základní webovou stránku a nakonfigurujete VS Code pro spuštění a ladění stránky. Než budete moct spustit, budete muset použít Node.js ke spuštění místního webového serveru a obsloužit stránku, když si ji prohlížeč požádá. V dalším kroku přidáte kód JavaScriptu pro volání různých rozhraní API služby Blob Storage a výsledky zobrazíte na stránce. Výsledky těchto volání můžete zobrazit také v [Azure Portal](https://portal.azure.com), [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer)a [rozšíření Azure Storage](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) pro vs Code.

### <a name="set-up-the-web-application"></a>Nastavení webové aplikace

Nejdřív vytvořte novou složku s názvem *Azure-BLOBs-JavaScript* a otevřete ji v vs Code. Pak v VS Code vytvořte nový soubor, přidejte následující kód HTML a uložte ho jako *index.html* do složky *Azure-BLOBs-JavaScript* .

```html
<!DOCTYPE html>
<html>

<body>
    <button id="create-container-button">Create container</button>
    <button id="delete-container-button">Delete container</button>
    <button id="select-button">Select and upload files</button>
    <input type="file" id="file-input" multiple style="display: none;" />
    <button id="list-button">List files</button>
    <button id="delete-button">Delete selected files</button>
    <p><b>Status:</b></p>
    <p id="status" style="height:160px; width: 593px; overflow: scroll;" />
    <p><b>Files:</b></p>
    <select id="file-list" multiple style="height:222px; width: 593px; overflow: scroll;" />
</body>

<!-- You'll add code here later in this quickstart. -->

</html>
```

### <a name="configure-the-debugger"></a>Konfigurace ladicího programu

Chcete-li nastavit rozšíření ladicího programu v VS Code, vyberte možnost **ladění > Přidat konfiguraci...** pak vyberte možnost **Chrome** nebo **Edge**v závislosti na tom, které rozšíření jste dříve nainstalovali v části požadavky. Tato akce vytvoří *launch.js* v souboru a otevře se v editoru.

V dalším kroku upravte *launch.jsv* souboru tak, aby `url` obsahovala hodnotu, `/index.html` jak je znázorněno níže:

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:8080/index.html",
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```

Tato konfigurace oznamuje VS Code, který prohlížeč se má spustit, a adresu URL, která se má načíst.

### <a name="launch-the-web-server"></a>Spustit webový server

Pokud chcete spustit místní Node.js webový server, vyberte **zobrazit > terminálu** a otevřete tak okno konzoly v rámci vs Code a potom zadejte následující příkaz.

```console
npx http-server
```

Tento příkaz nainstaluje balíček *http-Server* a spustí server a zpřístupní tak aktuální složku prostřednictvím výchozích adres URL, včetně toho, který je uvedený v předchozím kroku.

### <a name="start-debugging"></a>Spustit ladění

Pokud chcete spustit *index.html* v prohlížeči pomocí připojeného ladicího programu vs Code, vyberte **ladit > spustit ladění** nebo stiskněte klávesu F5 v vs Code.

Zobrazené uživatelské rozhraní ještě nic nedělá, ale v následující části přidáte JavaScriptový kód, který implementuje jednotlivé zobrazené funkce. Pak můžete nastavit zarážky a interagovat s ladicím programem, když je pozastaven na vašem kódu.

Když provedete změny *index.html*, nezapomeňte znovu načíst stránku, abyste viděli změny v prohlížeči. V VS Code můžete také vybrat možnost **ladění > restartovat ladění** nebo stisknout kombinaci kláves CTRL + SHIFT + F5.

### <a name="add-the-blob-storage-client-library"></a>Přidání klientské knihovny pro úložiště objektů BLOB

Pokud chcete povolit volání rozhraní API služby Blob Storage, napřed [si stáhněte Azure Storage SDK pro JavaScriptový klientskou knihovnu objektů BLOB](https://aka.ms/downloadazurestoragejsblob), rozbalte obsah souboru zip a umístěte soubor *azure-storage-blob.js* do složky *Azure-BLOBs-JavaScript* .

Dále vložte následující kód HTML do *index.html* za `</body>` uzavírací značku a nahraďte zástupný komentář.

```html
<script src="azure-storage-blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Tento kód přidá odkaz na soubor skriptu a poskytne místo pro váš vlastní kód JavaScriptu. Pro účely tohoto rychlého startu používáme soubor skriptu *azure-storage-blob.js* , abyste ho mohli otevřít v vs Code, číst jeho obsah a nastavovat zarážky. V produkčním prostředí byste měli použít kompaktnější *azure-storage.blob.min.js* soubor, který je také k dispozici v souboru ZIP.

Další informace o jednotlivých funkcích služby Blob Storage najdete v [referenční dokumentaci](https://docs.microsoft.com/javascript/api/%40azure/storage-blob/index). Všimněte si, že některé funkce v sadě SDK jsou k dispozici pouze v Node.js nebo pouze k dispozici v prohlížeči.

Kód v *azure-storage-blob.js* exportuje globální proměnnou s názvem `azblob` , kterou použijete v kódu JavaScriptu pro přístup k rozhraním API služby Blob Storage.

### <a name="add-the-initial-javascript-code"></a>Přidání počátečního kódu JavaScriptu

Dále vložte následující kód do `<script>` prvku zobrazeného v předchozím bloku kódu a nahraďte zástupný komentář.

```javascript
const createContainerButton = document.getElementById("create-container-button");
const deleteContainerButton = document.getElementById("delete-container-button");
const selectButton = document.getElementById("select-button");
const fileInput = document.getElementById("file-input");
const listButton = document.getElementById("list-button");
const deleteButton = document.getElementById("delete-button");
const status = document.getElementById("status");
const fileList = document.getElementById("file-list");

const reportStatus = message => {
    status.innerHTML += `${message}<br/>`;
    status.scrollTop = status.scrollHeight;
}
```

Tento kód vytvoří pole pro každý prvek jazyka HTML, který bude použit v následujícím kódu, a implementuje `reportStatus` funkci pro zobrazení výstupu.

V následujících částech přidejte všechny nové bloky kódu JavaScriptu za předchozí blok.

### <a name="add-your-storage-account-info"></a>Přidání informací o účtu úložiště

Dále přidejte kód pro přístup k účtu úložiště, nahraďte zástupné symboly názvem vašeho účtu a SAS, které jste vygenerovali v předchozím kroku.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

Tento kód používá informace o účtu a SAS k vytvoření instance [ContainerURL](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL) , která je užitečná pro vytváření a manipulaci s kontejnerem úložiště.

### <a name="create-and-delete-a-storage-container"></a>Vytvoření a odstranění kontejneru úložiště

V dalším kroku přidejte kód pro vytvoření a odstranění kontejneru úložiště, když stisknete příslušné tlačítko.

```javascript
const createContainer = async () => {
    try {
        reportStatus(`Creating container "${containerName}"...`);
        await containerURL.create(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

const deleteContainer = async () => {
    try {
        reportStatus(`Deleting container "${containerName}"...`);
        await containerURL.delete(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

createContainerButton.addEventListener("click", createContainer);
deleteContainerButton.addEventListener("click", deleteContainer);
```

Tento kód volá funkce ContainerURL [Create](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) a [Delete](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) bez použití instance [Abort](https://docs.microsoft.com/javascript/api/@azure/storage-blob/aborter) . Tento kód předpokládá, že váš účet úložiště je vytvořený a je povolený, aby pro tento rychlý Start byly jednoduché věci. V produkčním kódu použijte k přidání funkce časového limitu instanci přerušit.

### <a name="list-blobs"></a>Výpis objektů blob

V dalším kroku přidejte kód pro výpis obsahu kontejneru úložiště při stisknutí tlačítka **seznam souborů** .

```javascript
const listFiles = async () => {
    fileList.size = 0;
    fileList.innerHTML = "";
    try {
        reportStatus("Retrieving file list...");
        let marker = undefined;
        do {
            const listBlobsResponse = await containerURL.listBlobFlatSegment(
                azblob.Aborter.none, marker);
            marker = listBlobsResponse.nextMarker;
            const items = listBlobsResponse.segment.blobItems;
            for (const blob of items) {
                fileList.size += 1;
                fileList.innerHTML += `<option>${blob.name}</option>`;
            }
        } while (marker);
        if (fileList.size > 0) {
            reportStatus("Done.");
        } else {
            reportStatus("The container does not contain any files.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

listButton.addEventListener("click", listFiles);
```

Tento kód zavolá funkci [ContainerURL. listBlobFlatSegment](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) ve smyčce, aby se zajistilo, že se načtou všechny segmenty. U každého segmentu se přeskočí přes seznam položek objektů blob, které obsahuje, a aktualizuje seznam **soubory** .

### <a name="upload-blobs"></a>Nahrát objekty blob

V dalším kroku přidejte kód pro nahrání souborů do kontejneru úložiště při stisknutí tlačítka pro **Výběr a nahrání souborů** .

```javascript
const uploadFiles = async () => {
    try {
        reportStatus("Uploading files...");
        const promises = [];
        for (const file of fileInput.files) {
            const blockBlobURL = azblob.BlockBlobURL.fromContainerURL(containerURL, file.name);
            promises.push(azblob.uploadBrowserDataToBlockBlob(
                azblob.Aborter.none, file, blockBlobURL));
        }
        await Promise.all(promises);
        reportStatus("Done.");
        listFiles();
    } catch (error) {
        reportStatus(error.body.message);
    }
}

selectButton.addEventListener("click", () => fileInput.click());
fileInput.addEventListener("change", uploadFiles);
```

Tento kód propojí tlačítko **Vybrat a odeslat soubory** do skrytého `file-input` prvku. Tímto způsobem `click` Událost tlačítka aktivuje událost vstupu souboru `click` a zobrazí nástroj pro výběr souboru. Po výběru souborů a zavření dialogového okna `input` dojde k události a `uploadFiles` zavolá se funkce. Tato funkce volá funkci [uploadBrowserDataToBlockBlob](https://docs.microsoft.com/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) pouze pro prohlížeč pro každý vybraný soubor. Každé volání vrátí příslib, který je přidán do seznamu tak, aby bylo možné všechny očekávat najednou, což způsobí, že se soubory budou nahrávat paralelně.

### <a name="delete-blobs"></a>Odstraňovat objekty blob

V dalším kroku přidejte kód pro odstranění souborů z kontejneru úložiště při stisknutí tlačítka **Odstranit vybrané soubory** .

```javascript
const deleteFiles = async () => {
    try {
        if (fileList.selectedOptions.length > 0) {
            reportStatus("Deleting files...");
            for (const option of fileList.selectedOptions) {
                const blobURL = azblob.BlobURL.fromContainerURL(containerURL, option.text);
                await blobURL.delete(azblob.Aborter.none);
            }
            reportStatus("Done.");
            listFiles();
        } else {
            reportStatus("No files selected.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

deleteButton.addEventListener("click", deleteFiles);
```

Tento kód zavolá funkci [BlobURL. Delete](https://docs.microsoft.com/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) pro odebrání každého souboru vybraného v seznamu. Pak zavolá `listFiles` funkci uvedenou výše a aktualizuje obsah seznamu **souborů** .

### <a name="run-and-test-the-web-application"></a>Spuštění a testování webové aplikace

V tuto chvíli můžete spustit stránku a experimentovat, abyste získali dojem o fungování služby Blob Storage. Pokud dojde k nějakým chybám (například při pokusu o výpis souborů před vytvořením kontejneru), zobrazí se v podokně **stav** zobrazená chybová zpráva. Můžete také nastavit zarážky v kódu JavaScriptu pro prohlížení hodnot vrácených rozhraními API úložiště.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v rámci tohoto rychlého startu, otevřete [Azure Portal](https://portal.azure.com) a odstraňte skupinu prostředků, kterou jste vytvořili v části požadavky.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili jednoduchý web, který přistupuje k úložišti objektů BLOB z JavaScriptu založeného na prohlížeči. Pokud se chcete dozvědět, jak můžete hostovat samotný web na úložišti objektů blob, pokračujte k následujícímu kurzu:

> [!div class="nextstepaction"]
> [Hostování statického webu na Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)
