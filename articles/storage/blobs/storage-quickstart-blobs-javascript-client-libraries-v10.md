---
title: Rychlý start – vytvoření objektu blob ve službě Azure Storage pomocí JavaScriptu a HTML v prohlížeči
description: Zjistěte, jak pomocí instance třídy BlobService nahrávat, vypisovat a odstraňovat objekty blob s využitím JavaScriptu na stránce HTML.
services: storage
keywords: storage, javascript, html
author: KarlErickson
ms.custom: mvc
ms.service: storage
ms.author: karler
ms.reviewer: seguler
ms.date: 05/20/2019
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: a971f2b4b63b3fd35777d1d890da8451b84bb086
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544048"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-upload-list-and-delete-blobs-using-azure-storage-v10-sdk-for-javascripthtml-in-the-browser"></a>Rychlý start: Nahrávání, vypisování a odstraňování objektů BLOB pomocí služby Azure Storage v10 SDK pro JavaScript a HTML v prohlížeči

V tomto rychlém startu se dozvíte, použijte [V10 SDK úložiště Azure pro jazyk JavaScript – objekt Blob](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob#readme) knihovny správu objektů blob z kódu jazyka JavaScript spuštěného zcela v prohlížeči. Níže použitý přístup ukazuje, jak používat požadovaná bezpečnostní opatření pro zajištění chráněného přístupu k účtu úložiště objektů blob.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Tyto klientské knihovny Azure Storage pro JavaScript nebudou fungovat přímo ze systému souborů a musí se obsluhovat z webového serveru. Toto téma používá [Node.js](https://nodejs.org) spustit základní server. Pokud nechcete nainstalovat uzlu, můžete použít jakýkoliv jiný způsob spuštění místní webový server.

Ke sledování postupu v ladění, budete potřebovat [Visual Studio Code](https://code.visualstudio.com) a buď [ladicího programu pro Chrome](vscode:extension/msjsdiag.debugger-for-chrome) nebo [ladicího programu pro Microsoft Edge](vscode:extension/msjsdiag.debugger-for-edge) rozšíření.

## <a name="setting-up-storage-account-cors-rules"></a>Nastavení pravidel CORS pro účet úložiště

Webové aplikace můžete získat přístup k úložišti objektů blob z klienta, musíte nakonfigurovat svůj účet povolit [sdílení prostředků různého původu](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services), neboli CORS.

Vraťte se na web Azure Portal a vyberte svůj účet úložiště. Chcete-li definovat nové pravidlo CORS, přejděte na **nastavení** a klikněte na **CORS** odkaz. Pak kliknutím na tlačítko **Přidat** otevřete okno **Přidat pravidlo CORS**. Pro účely tohoto rychlého startu vytvoříte otevřené pravidlo CORS:

![Nastavení CORS pro účet služby Azure Blob Storage](media/storage-quickstart-blobs-javascript-client-libraries/azure-blob-storage-cors-settings.png)

Následující tabulka obsahuje popis jednotlivých nastavení CORS a vysvětlení hodnot použitých k definici pravidla.

|Nastavení  |Hodnota  | Popis |
|---------|---------|---------|
| Povolené zdroje | * | Přijímá čárkami oddělený seznam domén nastavených jako přijatelné zdroje. Nastavením této hodnoty na `*` se povolí přístup k účtu úložiště ze všech domén. |
| Povolené příkazy     | delete, get, head, merge, post, options a put | Uvádí povolené příkazy HTTP, které je možné spouštět proti účtu úložiště. Pro účely tohoto rychlého startu vyberte všechny dostupné možnosti. |
| Povolené hlavičky | * | Definuje seznam hlaviček požadavku (včetně hlaviček s předponou), které účet úložiště povoluje. Nastavením této hodnoty na `*` se povolí přístup všem hlavičkám. |
| Zveřejněné hlavičky | * | Uvádí hlavičky odpovědi, které účet povoluje. Nastavením této hodnoty na `*` se účtu povolí odesílání jakýchkoli hlaviček.  |
| Maximální stáří (sekundy) | 86400 | Maximální doba, po kterou prohlížeč uchovává předběžné požadavky OPTIONS v mezipaměti. Hodnota *86400* povolí uchování v mezipaměti po celý den. |

> [!IMPORTANT]
> Ujistěte se, že všechna nastavení, které se používají v produkčním prostředí vystavit minimální potřebnou úroveň přístupu k účtu úložiště zajistit zabezpečený přístup. Zde popsaná nastavení CORS jsou vhodná pro účely rychlého startu, protože definují mírné zásady zabezpečení. Tato nastavení se však nedoporučují pro kontext skutečného světa.

Dále pomocí služby Azure Cloud Shell vytvoříte token zabezpečení.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Vytvořit sdílený přístupový podpis

Sdílený přístupový podpis (SAS) se používá v kódu spuštěném v prohlížeči k autorizaci požadavků na úložiště objektů blob. Díky použití SAS může klient autorizovat přístup k prostředkům úložiště, aniž by měl přístupový klíč nebo připojovací řetězec účtu. Další informace o SAS najdete v tématu [Použití sdílených přístupových podpisů (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md).

Můžete vytvořit SAS pomocí rozhraní příkazového řádku Azure prostřednictvím Azure cloud shell nebo pomocí webu Azure portal nebo Průzkumníka služby Azure Storage. Následující tabulka popisuje parametry, je potřeba zadat hodnoty pro generovat SAS pomocí rozhraní příkazového řádku.

| Parametr      |Popis  | Zástupný symbol |
|----------------|-------------|-------------|
| *expiry*       | Datum vypršení platnosti přístupového tokenu ve formátu RRRR-MM-DD. Pro účely tohoto rychlého startu zadejte zítřejší datum. | *FUTURE_DATE* |
| *account-name* | Název účtu úložiště. Použijte název, který jste si poznamenali v dřívějším kroku. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | Klíč účtu úložiště. Použijte klíč, který jste si poznamenali v dřívějším kroku. | *YOUR_STORAGE_ACCOUNT_KEY* |

Pomocí následujícího příkazu rozhraní příkazového řádku, skutečné hodnoty pro každý zástupný text, generovat SAS, který vám pomůže v kódu jazyka JavaScript.

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
| *oprávnění*    | racwdl  | Tento SAS povoluje funkce *read* (čtení), *append* (připojení), *create* (vytvoření), *write* (zápis), *delete* (odstranění) a *list* (výpis). |
| *resource-types* | sco     | Tento SAS ovlivňuje prostředky *service* (služba), *container* (kontejner) a *object* (objekt). |
| *services*       | b       | Tento SAS ovlivňuje službu *Blob* Service. |

Teď, když je SAS vygenerovaný, zkopírujte návratovou hodnotu a uložte ho někam pro použití v dalším kroku. Pokud jste vygenerovali váš SAS pomocí jiné metody než rozhraní příkazového řádku Azure, budete muset odebrat počáteční `?` Pokud je k dispozici. Tento znak je adresa URL oddělovač, který je již součástí adresy URL šablony dále v tomto tématu Použití sdíleného přístupového podpisu.

> [!IMPORTANT]
> V produkčním prostředí vždy předávejte tokeny SAS pomocí SSL. Kromě toho by se tokeny SAS měly generovat na serveru a odesílat na stránku HTML, která je předá zpět do služby Azure Blob Storage. Jedním z přístupů, které můžete zvážit, je generování tokenů SAS pomocí funkce bez serveru. Azure Portal obsahuje šablony funkcí, které zahrnují možnost generovat SAS pomocí funkce JavaScriptu.

## <a name="implement-the-html-page"></a>Implementace stránky HTML

V této části vytvoříte základní webové stránky a nakonfigurovat VS Code pro spuštění a ladění na stránce. Předtím, než můžete spustit, ale budete muset spustit místní webový server a poskytovat na stránce, když váš prohlížeč požádá pomocí Node.js. V dalším kroku přidáte kód jazyka JavaScript volat různé úložiště objektů blob rozhraní API a zobrazit výsledky na stránce. Můžete také zobrazit výsledky těchto volání v [webu Azure portal](https://portal.azure.com), [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer)a [rozšíření Azure Storage](vscode:extension/ms-azuretools.vscode-azurestorage) pro VS Code.

### <a name="set-up-the-web-application"></a>Nastavení webové aplikace

Nejprve vytvořte novou složku s názvem *azure-blobs-javascript* a otevřete ho v nástroji VS Code. Pak vytvořte nový soubor v nástroji VS Code, přidejte následující kód HTML a uložte ho jako *index.html* v *azure-blobs-javascript* složky.

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

### <a name="configure-the-debugger"></a>Nastavte ladící program

Chcete-li nastavit rozšíření ladicího programu v nástroji VS Code, vyberte **ladit > Přidat konfiguraci...** a pak vyberte **Chrome** nebo **hrany**podle toho, která rozšíření jste nainstalovali v oddílu požadavky dříve. Tato akce vytvoří *launch.json* souborů a otevře v editoru.

V dalším kroku změnit *launch.json* souboru tak, aby `url` hodnota zahrnuje `/index.html` jak je znázorněno:

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

Tato konfigurace říká VS Code webového prohlížeče spustit a adresu URL, která je načíst.

### <a name="launch-the-web-server"></a>Spustit webový server

Chcete-li spustit místní webový server Node.js, vyberte **zobrazení > terminálu** otevřete okno konzoly v nástroji VS Code, zadejte následující příkaz.

```console
npx http-server
```

Tento příkaz nainstaluje *http server* balíček a spustit na server, takže k dispozici prostřednictvím výchozí adresy URL včetně je uvedeno v předchozím kroku do aktuální složky.

### <a name="start-debugging"></a>Spustit ladění

Ke spuštění *index.html* v prohlížeči pomocí ladicího programu VS Code připojený, vyberte **ladit > Spustit ladění** nebo stiskněte klávesu F5 v nástroji VS Code.

Uživatelské rozhraní zobrazí ještě nic neumí, ale přidáte kód jazyka JavaScript v následující části k provádění každé funkce zobrazené. Potom můžete nastavit zarážky a interakci s ladicí program, když byla pozastavena na váš kód.

Když provedete změny u *index.html*, je potřeba znovu načíst stránku pro zobrazení změn v prohlížeči. V nástroji VS Code, můžete také vybrat **ladit > restartujte ladění** nebo stiskněte kombinaci kláves CTRL + SHIFT + F5.

### <a name="add-the-blob-storage-client-library"></a>Přidat klientskou knihovnu pro úložiště objektů blob

Povolit volání do úložiště objektů blob rozhraní API, nejprve [stáhnout sady SDK služby Azure Storage pro JavaScript – Klientská knihovna pro objekt Blob](https://aka.ms/downloadazurestoragejsblob), extrahujte obsah souboru zip a umístěte *azure storage.blob.js* soubor *azure-blobs-javascript* složky.

V dalším kroku vložte následující kód HTML do *index.html* po `</body>` uzavírací značka, přičemž nahraďte zástupný symbol komentáře.

```html
<script src="azure-storage.blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Tento kód přidá odkaz na soubor skriptu a místo pro kód jazyka JavaScript. Pro účely tohoto rychlého startu používáme *azure storage.blob.js* soubor skriptu tak, aby lze jej otevřít v nástroji VS Code, přečtěte si jeho obsah a nastavit zarážky. V produkčním prostředí byste měli použít další compact *azure storage.blob.min.js* soubor, který je k dispozici také v souboru zip.

Můžete zjistit více o jednotlivých funkcích úložiště objektů blob v [referenční dokumentaci](https://docs.microsoft.com/javascript/api/%40azure/storage-blob/index). Všimněte si, že některé funkce v sadě Windows SDK jsou pouze k dispozici v Node.js a k dispozici pouze v prohlížeči.

Kód v *azure storage.blob.js* exportuje globální proměnnou s názvem `azblob`, které budete používat v kódu jazyka JavaScript pro přístup k úložišti objektů blob rozhraní API.

### <a name="add-the-initial-javascript-code"></a>Přidat počáteční kód jazyka JavaScript

V dalším kroku vložte následující kód do `<script>` element je znázorněno v předchozí blok kódu nahraďte zástupný symbol komentáře.

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

Tento kód vytvoří pole pro každý prvek HTML, který bude používat následující kód a implementuje `reportStatus` funkce k zobrazení výstupu.

V následujících částech přidáte každý nový blok kódu jazyka JavaScript za předchozí blok.

### <a name="add-your-storage-account-info"></a>Přidejte informace o účtu úložiště

Dále přidejte kód pro přístup k účtu úložiště, zástupné texty nahraďte názvem vašeho účtu a SAS vygenerovaný v předchozím kroku.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

Tento kód používá k vytvoření přidružení zabezpečení a informace o účtu [ContainerURL](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL) instance, což je užitečné pro vytváření a manipulaci se kontejner úložiště.

### <a name="create-and-delete-a-storage-container"></a>Vytvořit a odstranit kontejner úložiště.

Dál přidejte kód k vytvoření a odstranění kontejneru úložiště po stisknutí klávesy na odpovídající tlačítko.

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

Tento kód volá objekt ContainerURL [vytvořit](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL#create-aborter--icontainercreateoptions-) a [odstranit](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL#delete-aborter--icontainerdeletemethodoptions-) funkce bez použití [Aborter](https://docs.microsoft.com/javascript/api/@azure/storage-blob/aborter) instance. Pro zjednodušení pro tento rychlý start, tento kód předpokládá, že váš účet úložiště se vytvořil a je povolená. V produkčním kódu používejte instanci Aborter k přidání funkcí časového limitu.

### <a name="list-blobs"></a>Výpis objektů blob

Dál přidejte kód pro vypsání obsahu kontejneru úložiště objektů po stisknutí klávesy **seznam souborů** tlačítko.

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

Tento kód volá [ContainerURL.listBlobFlatSegment](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL#listblobflatsegment-aborter--string--icontainerlistblobssegmentoptions-) funkce ve smyčce zajistit, že jsou načteny všechny segmenty. Pro každý segment cyklickému seznam objektů blob položek obsahuje a aktualizuje **soubory** seznamu.

### <a name="upload-blobs"></a>Nahrání objektů BLOB

Dál přidejte kód k nahrání souborů do kontejneru úložiště po stisknutí klávesy **vyberte a nahrajte soubory** tlačítko.

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

Tento kód se připojí **vyberte a nahrajte soubory** tlačítko skrytého `file-input` elementu. Tímto způsobem, tlačítko `click` událost se aktivuje vstupního souboru `click` událostí a zobrazí se okno pro výběr souboru. Po vybrání soubory a zavřete dialogové okno, `input` dojde k události a `uploadFiles` funkce je volána. Tato funkce volá pouze prohlížeče [uploadBrowserDataToBlockBlob](https://docs.microsoft.com/javascript/api/@azure/storage-blob/#uploadbrowserdatatoblockblob-aborter--blob---arraybuffer---arraybufferview--blockbloburl--iuploadtoblockbloboptions-) funkce pro každý soubor, který jste vybrali. Každé volání vrátí příslib, která se přidá do seznamu tak, aby se mohou všechny ní použít operátor await najednou, způsobí soubory k nahrání paralelně.

### <a name="delete-blobs"></a>Odstranění objektů blob

Dál přidejte kód k odstranění souborů z kontejneru úložiště. po stisknutí klávesy **odstranit vybrané soubory** tlačítko.

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

Tento kód volá [BlobURL.delete](https://docs.microsoft.com/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) funkce k odebrání každého souboru vybraného v seznamu. Poté zavolá `listFiles` funkce uvedené starší aktualizovat obsah **soubory** seznamu.

### <a name="run-and-test-the-web-application"></a>Spuštění a testování webové aplikace

V tomto okamžiku můžete spustit stránky a experimentování, chcete-li získat představu o tom, objektů blob úložiště funguje. Pokud dojde k nějaké chybě (například při pokusu o seznam souborů předtím, než vytvoříte kontejner), **stav** podokně se zobrazí chybová zpráva přijatá. Můžete také nastavit zarážky v kódu jazyka JavaScript ke kontrole hodnot vrácených rozhraní API služby storage.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v rámci tohoto rychlého startu, přejděte na [webu Azure portal](https://portal.azure.com) a odstraňte skupinu prostředků, které jste vytvořili v části předpoklady.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili jednoduchý web, přistupuje k úložišti objektů blob z založené na prohlížeči JavaScript. Informace o tom, jak můžete hostovat webem jako takovým v úložišti objektů blob, pokračujte k následujícímu kurzu:

> [!div class="nextstepaction"]
> [Hostování statického webu ve službě Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)
