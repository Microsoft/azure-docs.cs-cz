---
title: 'Úvodní příručka: Úložiště objektů blob Azure pro JavaScript v10 v prohlížeči'
description: Naučte se nahrávat, seznamovat a odstraňovat objekty BLOB pomocí sady JavaScript v10 SDK na stránce HTML.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.author: mhopkins
ms.date: 01/24/2020
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: 5219af0ec343a6e7f87a07e4a7280ac5f4e85cd3
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619116"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-browser"></a>Úvodní příručka: Správa objektů BLOB pomocí sady JavaScript v10 SDK v prohlížeči

V tomto rychlém startu se naučíte spravovat objekty BLOB pomocí kódu JavaScriptu, který běží výhradně v prohlížeči. Objekty BLOB jsou objekty, které mohou obsahovat velké množství textových nebo binárních dat, včetně obrázků, dokumentů, datových proudů médií a archivních dat. K zajištění chráněného přístupu k účtu úložiště objektů blob budete používat požadovaná bezpečnostní opatření.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Účet služby Azure Storage. [Vytvořte účet úložiště](../common/storage-account-create.md).
- Místní webový server. Tento článek používá [Soubor Node.js](https://nodejs.org) k otevření základního serveru.
- [Visual Studio kód](https://code.visualstudio.com).
- Rozšíření VS Code pro ladění prohlížeče, například [Ladicí program pro Chrome](vscode:extension/msjsdiag.debugger-for-chrome) nebo [Debugger pro Microsoft Edge](vscode:extension/msjsdiag.debugger-for-edge).

## <a name="setting-up-storage-account-cors-rules"></a>Nastavení pravidel CORS pro účet úložiště

Než bude mít vaše webová aplikace přístup k úložišti objektů blob z klienta, je nutné nakonfigurovat účet tak, aby umožňoval [sdílení prostředků mezi zdroji](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)nebo CORS.

Vraťte se na web Azure Portal a vyberte svůj účet úložiště. Chcete-li definovat nové pravidlo CORS, přejděte do části **Nastavení** a klikněte na odkaz **CORS.** Pak kliknutím na tlačítko **Přidat** otevřete okno **Přidat pravidlo CORS**. Pro účely tohoto rychlého startu vytvoříte otevřené pravidlo CORS:

![Nastavení CORS pro účet služby Azure Blob Storage](media/storage-quickstart-blobs-javascript-client-libraries-v10/azure-blob-storage-cors-settings.png)

Následující tabulka obsahuje popis jednotlivých nastavení CORS a vysvětlení hodnot použitých k definici pravidla.

|Nastavení  |Hodnota  | Popis |
|---------|---------|---------|
| Povolené zdroje | * | Přijímá čárkami oddělený seznam domén nastavených jako přijatelné zdroje. Nastavením této hodnoty na `*` se povolí přístup k účtu úložiště ze všech domén. |
| Povolené metody     | delete, get, head, merge, post, options a put | Uvádí povolené příkazy HTTP, které je možné spouštět proti účtu úložiště. Pro účely tohoto rychlého startu vyberte všechny dostupné možnosti. |
| Povolené hlavičky | * | Definuje seznam hlaviček požadavku (včetně hlaviček s předponou), které účet úložiště povoluje. Nastavením této hodnoty na `*` se povolí přístup všem hlavičkám. |
| Zveřejněné hlavičky | * | Uvádí hlavičky odpovědi, které účet povoluje. Nastavením této hodnoty na `*` se účtu povolí odesílání jakýchkoli hlaviček.  |
| Maximální věk (sekundy) | 86400 | Maximální doba, po kterou prohlížeč uchovává předběžné požadavky OPTIONS v mezipaměti. Hodnota *86400* povolí uchování v mezipaměti po celý den. |

> [!IMPORTANT]
> Ujistěte se, že všechna nastavení, která používáte v produkčním prostředí, zveřejňují minimální množství přístupu potřebného k vašemu účtu úložiště k udržení zabezpečeného přístupu. Zde popsaná nastavení CORS jsou vhodná pro účely rychlého startu, protože definují mírné zásady zabezpečení. Tato nastavení se však nedoporučují pro kontext skutečného světa.

Dále pomocí služby Azure Cloud Shell vytvoříte token zabezpečení.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Vytvoření sdíleného přístupového podpisu

Sdílený přístupový podpis (SAS) se používá v kódu spuštěném v prohlížeči k autorizaci požadavků na úložiště objektů blob. Díky použití SAS může klient autorizovat přístup k prostředkům úložiště, aniž by měl přístupový klíč nebo připojovací řetězec účtu. Další informace o SAS najdete v tématu [Použití sdílených přístupových podpisů (SAS)](../common/storage-sas-overview.md).

SAS můžete vytvořit pomocí rozhraní příkazového příkazu Azure prostřednictvím cloudového prostředí Azure nebo pomocí portálu Azure nebo Průzkumníka úložiště Azure. Následující tabulka popisuje parametry, které je třeba zadat hodnoty pro generování SAS s rozhraním příkazového řádku.

| Parametr      |Popis  | Zástupný symbol |
|----------------|-------------|-------------|
| *Vypršení platnosti*       | Datum vypršení platnosti přístupového tokenu ve formátu RRRR-MM-DD. Pro účely tohoto rychlého startu zadejte zítřejší datum. | *FUTURE_DATE* |
| *název účtu* | Název účtu úložiště. Použijte název, který jste si poznamenali v dřívějším kroku. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | Klíč účtu úložiště. Použijte klíč, který jste si poznamenali v dřívějším kroku. | *YOUR_STORAGE_ACCOUNT_KEY* |

Pomocí následujícího příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu s aktuálními hodnotami pro každý zástupný symbol vygeneřijte SAS, který můžete použít v kódu JavaScriptu.

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
| *Oprávnění*    | racwdl  | Tento SAS povoluje funkce *read* (čtení), *append* (připojení), *create* (vytvoření), *write* (zápis), *delete* (odstranění) a *list* (výpis). |
| *resource-types* | sco     | Tento SAS ovlivňuje prostředky *service* (služba), *container* (kontejner) a *object* (objekt). |
| *Služby*       | b       | Tento SAS ovlivňuje službu *Blob* Service. |

Nyní, když je generována SAS, zkopírujte vrácenou hodnotu a uložte ji někde pro použití v nadcházejícím kroku. Pokud jste vygenerovali SAS pomocí jiné metody než vazby `?` příkazového příkazu Azure, budete muset odebrat počáteční, pokud je k dispozici. Tento znak je oddělovač adres URL, který je již k dispozici v šabloně adresy URL dále v tomto tématu, kde se používá SAS.

> [!IMPORTANT]
> V produkčním prostředí vždy předejte tokeny SAS pomocí TLS. Kromě toho by se tokeny SAS měly generovat na serveru a odesílat na stránku HTML, která je předá zpět do služby Azure Blob Storage. Jedním z přístupů, které můžete zvážit, je generování tokenů SAS pomocí funkce bez serveru. Azure Portal obsahuje šablony funkcí, které zahrnují možnost generovat SAS pomocí funkce JavaScriptu.

## <a name="implement-the-html-page"></a>Implementace stránky HTML

V této části vytvoříte základní webovou stránku a nakonfigurujete kód VS pro spuštění a ladění stránky. Před spuštěním však budete muset pomocí souboru Node.js spustit místní webový server a zobrazit stránku, když o to prohlížeč požádá. Dále přidáte kód JavaScriptu pro volání různých api úložiště objektů blob a zobrazíte výsledky na stránce. Výsledky těchto volání můžete také zobrazit na [webu Azure Portal](https://portal.azure.com), [Průzkumníku úložiště Azure](https://azure.microsoft.com/features/storage-explorer)a v rozšíření úložiště [Azure](vscode:extension/ms-azuretools.vscode-azurestorage) pro kód VS.

### <a name="set-up-the-web-application"></a>Nastavení webové aplikace

Nejprve vytvořte novou složku s názvem *azure-blobs-javascript* a otevřete ji v Kódu VS. Pak vytvořte nový soubor ve VS Code, přidejte následující HTML a uložte ho jako *index.html* ve složce *azure-blobs-javascript.*

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

Chcete-li nastavit rozšíření ladicího programu v kódu VS, vyberte **možnost Ladění > Přidat konfiguraci...**, a pak vyberte Možnost **Chrome** nebo **Edge**v závislosti na tom, které rozšíření jste nainstalovali v části Požadavky dříve. Tato akce vytvoří soubor *launch.json* a otevře jej v editoru.

Dále upravte soubor *launch.json* `url` tak, `/index.html` aby hodnota zahrnovala, jak je znázorněno:

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

Tato konfigurace sděluje VS Code, který prohlížeč má být spuštěn a která adresa URL se má načíst.

### <a name="launch-the-web-server"></a>Spuštění webového serveru

Chcete-li spustit místní webový server Node.js, vyberte **možnost Zobrazit > terminál,** chcete-li otevřít okno konzoly uvnitř kódu VS, a pak zadat následující příkaz.

```console
npx http-server
```

Tento příkaz nainstaluje balíček *http-server* a spustí server, čímž bude aktuální složka dostupná prostřednictvím výchozích adres URL, včetně té, která je uvedena v předchozím kroku.

### <a name="start-debugging"></a>Zahájit ladění

Chcete-li spustit *soubor index.html* v prohlížeči s připojeným ladicím programem kódu VS, vyberte **možnost Ladění > začít ladění** nebo stiskněte klávesu F5 v kódu VS.

Zobrazené ui zatím nic nedělá, ale v následující části přidáte kód JavaScriptu, abyste implementovali každou zobrazenou funkci. Potom můžete nastavit zarážky a pracovat s ladicím programem, když je pozastavena na váš kód.

Když provedete změny *v souboru index.html*, nezapomeňte znovu načíst stránku, abyste viděli změny v prohlížeči. V kódu VS můžete také vybrat **ladění > restartovat ladění** nebo stisknout kombinaci kláves CTRL + SHIFT + F5.

### <a name="add-the-blob-storage-client-library"></a>Přidání klientské knihovny úložiště objektů blob

Chcete-li povolit volání rozhraní API úložiště objektů blob, nejprve stáhněte sadu [Azure Storage SDK pro knihovnu klienta JavaScript – Blob](https://aka.ms/downloadazurestoragejsblob), extrahujte obsah souboru zip a umístěte soubor *azure-storage-blob.js* do složky *azure-blobs-javascript.*

Dále vložte následující HTML do *index.html* za `</body>` uzavírací značku a nahrazte zástupný komentář.

```html
<script src="azure-storage-blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Tento kód přidá odkaz na soubor skriptu a poskytuje místo pro vlastní kód JavaScriptu. Pro účely tohoto rychlého startu používáme soubor skriptu *azure-storage-blob.js,* abyste ho mohli otevřít v kódu VS, číst jeho obsah a nastavovat zarážky. V produkčním prostředí byste měli použít kompaktnější soubor *azure-storage.blob.min.js,* který je také k dispozici v souboru zip.

Další informace o jednotlivých funkcích úložiště objektů blob najdete v [referenční dokumentaci](https://docs.microsoft.com/javascript/api/%40azure/storage-blob/index). Všimněte si, že některé funkce v sadě SDK jsou k dispozici pouze v souboru Node.js nebo jsou k dispozici pouze v prohlížeči.

Kód v *azure-storage-blob.js* exportuje `azblob`globální proměnnou s názvem , kterou použijete v kódu JavaScriptu pro přístup k api úložiště objektů blob.

### <a name="add-the-initial-javascript-code"></a>Přidání počátečního kódu JavaScriptu

Dále vložte následující kód `<script>` do prvku zobrazeného v předchozím bloku kódu a nahrazte zástupný komentář.

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

Tento kód vytvoří pole pro každý element HTML, který `reportStatus` bude používat následující kód, a implementuje funkci pro zobrazení výstupu.

V následujících částech přidejte každý nový blok kódu JavaScript u předchozího bloku.

### <a name="add-your-storage-account-info"></a>Přidání informací o účtu úložiště

Dále přidejte kód pro přístup k účtu úložiště a nahraďte zástupné symboly názvem účtu a SAS, který jste vygenerovali v předchozím kroku.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

Tento kód používá informace o vašem účtu a SAS k vytvoření instance [ContainerURL,](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL) což je užitečné pro vytváření a manipulaci s kontejnerem úložiště.

### <a name="create-and-delete-a-storage-container"></a>Vytvoření a odstranění kontejneru úložiště

Dále přidejte kód pro vytvoření a odstranění kontejneru úložiště po stisknutí odpovídajícího tlačítka.

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

Tento kód volá ContainerURL [vytvořit](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) a [odstranit](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) funkce bez použití [Aborter](https://docs.microsoft.com/javascript/api/@azure/storage-blob/aborter) instance. Chcete-li, aby věci jednoduché pro tento rychlý start, tento kód předpokládá, že váš účet úložiště byl vytvořen a je povolen. V produkčním kódu použijte aborter instanci přidat funkci časového opození.

### <a name="list-blobs"></a>Výpis objektů blob

Dále přidejte kód do seznamu obsahu kontejneru úložiště po stisknutí tlačítka **Seznam souborů.**

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

Tento kód volá [containerURL.listBlobFlatSegment](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) funkce ve smyčce zajistit, že jsou načteny všechny segmenty. Pro každý segment se promítne přes seznam položek objektů blob, které obsahuje, a aktualizuje seznam **Soubory.**

### <a name="upload-blobs"></a>Nahrát objekty BLOB

Dále přidejte kód pro nahrání souborů do kontejneru úložiště, když stisknete tlačítko **Vybrat a nahrát soubory.**

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

Tento kód spojuje tlačítko **Vybrat a nahrát soubory** se skrytým `file-input` prvkem. Tímto způsobem událost `click` tlačítka spustí událost `click` vstupu souboru a zobrazí výběr souboru. Po výběru souborů a zavření `input` dialogového okna `uploadFiles` dojde k události a funkce je volána. Tato funkce volá funkci [uploadBrowserDataToBlockBlob](https://docs.microsoft.com/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) pouze pro prohlížeč pro každý vybraný soubor. Každé volání vrátí promise, který je přidán do seznamu tak, aby mohly být všechny očekávány najednou, což způsobí, že soubory nahrát paralelně.

### <a name="delete-blobs"></a>Odstranění objektů blob

Dále přidejte kód pro odstranění souborů z kontejneru úložiště, když stisknete tlačítko **Odstranit vybrané soubory.**

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

Tento kód volá funkci [BlobURL.delete](https://docs.microsoft.com/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) k odebrání každého souboru vybraného v seznamu. Potom volá `listFiles` dříve zobrazenou funkci, aby aktualizoval obsah seznamu **Soubory.**

### <a name="run-and-test-the-web-application"></a>Spuštění a testování webové aplikace

V tomto okamžiku můžete spustit stránku a experimentovat, abyste získali pocit, jak funguje úložiště objektů blob. Pokud dojde k chybám (například při pokusu o vypsat soubory před vytvořením kontejneru), zobrazí se v podokně **Stav** přijatá chybová zpráva. Můžete také nastavit zarážky v kódu Jazyka JavaScript a zkontrolovat hodnoty vrácené úložišti API.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené během tohoto rychlého startu, přejděte na [portál Azure](https://portal.azure.com) a odstraňte skupinu prostředků, kterou jste vytvořili v části Požadavky.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili jednoduchý web, který přistupuje k úložišti objektů blob z javascriptu založeného na prohlížeči. Chcete-li se dozvědět, jak můžete hostovat samotný web v úložišti objektů blob, pokračujte v následujícím kurzu:

> [!div class="nextstepaction"]
> [Hostování statického webu v úložišti objektů Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)
