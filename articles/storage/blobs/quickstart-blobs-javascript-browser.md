---
title: 'Rychlý Start: knihovna úložiště objektů BLOB v Azure V12-JavaScript v prohlížeči'
description: V tomto rychlém startu se dozvíte, jak používat klientskou knihovnu Azure Blob Storage verze 12 pro JavaScript v prohlížeči. Vytvoříte kontejner a objekt v úložišti objektů BLOB. V dalším kroku se dozvíte, jak zobrazit seznam všech objektů BLOB v kontejneru. Nakonec se naučíte, jak odstranit objekty BLOB a odstranit kontejner.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: eebfa61632bc49d5df35c17ba2d2faca0382001c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336135"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-a-browser"></a>Rychlý Start: Správa objektů BLOB pomocí sady JavaScript V12 SDK v prohlížeči

Úložiště objektů BLOB v Azure je optimalizované pro ukládání velkých objemů nestrukturovaných dat. Objekty blob jsou objekty, které mohou obsahovat text nebo binární data, včetně obrázků, dokumentů, datových proudů médií a archivovaných dat. V tomto rychlém startu se naučíte spravovat objekty BLOB pomocí JavaScriptu v prohlížeči. Nahrajete a vypíšete objekty BLOB a vytvoříte a odstraníte kontejnery.

Další prostředky:

* [Referenční dokumentace k rozhraní API](/javascript/api/@azure/storage-blob)
* [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob)
* [Balíček (npm)](https://www.npmjs.com/package/@azure/storage-blob)
* [Ukázky](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Požadavky

* [Účet Azure s aktivním předplatným](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Účet Azure Storage](../common/storage-account-create.md)
* [Node.js](https://nodejs.org)
* [Microsoft Visual Studio Code](https://code.visualstudio.com)
* Rozšíření Visual Studio Code pro ladění prohlížeče, například:
    * [Ladicí program pro Microsoft Edge](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-edge)
    * [Ladicí program pro Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)
    * [Ladicí program pro Firefox](https://marketplace.visualstudio.com/items?itemName=firefox-devtools.vscode-firefox-debug)


[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="object-model"></a>Objektový model

Úložiště objektů BLOB nabízí tři typy prostředků:

* Účet úložiště
* Kontejner v účtu úložiště
* Objekt BLOB v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury služby Blob Storage](./media/storage-blobs-introduction/blob1.png)

V tomto rychlém startu použijete následující třídy JavaScriptu k interakci s těmito prostředky:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): `BlobServiceClient` Třída umožňuje manipulovat s Azure Storage prostředky a kontejnery objektů BLOB.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): `ContainerClient` Třída umožňuje manipulovat s kontejnery Azure Storage a jejich objekty blob.
* [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient): `BlockBlobClient` Třída umožňuje manipulovat s objekty blob Azure Storage.

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnou služby Azure Blob Storage V12 pro JavaScript.

### <a name="create-a-cors-rule"></a>Vytvoření pravidla CORS

Předtím, než může vaše webová aplikace získat přístup k úložišti objektů BLOB z klienta, musíte nakonfigurovat svůj účet, aby povoloval [sdílení prostředků mezi zdroji](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)nebo CORS.

V Azure Portal vyberte svůj účet úložiště. Pokud chcete definovat nové pravidlo CORS, přejděte do části **Nastavení** a vyberte **CORS**. Pro účely tohoto rychlého startu vytvoříte otevřené pravidlo CORS:

![Nastavení CORS pro účet služby Azure Blob Storage](media/quickstart-blobs-javascript-browser/azure-blob-storage-cors-settings.png)

Následující tabulka obsahuje popis jednotlivých nastavení CORS a vysvětlení hodnot použitých k definici pravidla.

|Nastavení  |Hodnota  | Popis |
|---------|---------|---------|
| **POVOLENÉ ZDROJE** | **\*** | Přijímá čárkami oddělený seznam domén nastavených jako přijatelné zdroje. Nastavením této hodnoty na `*` se povolí přístup k účtu úložiště ze všech domén. |
| **POVOLENÉ METODY** | **Odstranění**, **získání**, **záhlaví**, **sloučení**, **odeslání**, **Možnosti**a **vložení** | Uvádí povolené příkazy HTTP, které je možné spouštět proti účtu úložiště. Pro účely tohoto rychlého startu vyberte všechny dostupné možnosti. |
| **POVOLENÉ HLAVIČKY** | **\*** | Definuje seznam hlaviček požadavku (včetně hlaviček s předponou), které účet úložiště povoluje. Nastavením této hodnoty na `*` se povolí přístup všem hlavičkám. |
| **ZVEŘEJNĚNÉ HLAVIČKY** | **\*** | Uvádí hlavičky odpovědi, které účet povoluje. Nastavením této hodnoty na `*` se účtu povolí odesílání jakýchkoli hlaviček. |
| **MAXIMÁLNÍ STÁŘÍ** | **86400** | Maximální doba, po kterou prohlížeč ukládá do mezipaměti požadavek na předběžné možnosti v sekundách Hodnota *86400* povolí uchování v mezipaměti po celý den. |

Po vyplnění polí hodnotami z této tabulky klikněte na tlačítko **Uložit** .

> [!IMPORTANT]
> Zajistěte, aby všechna nastavení, která používáte v produkčním prostředí, zveřejnila minimální přístup k účtu úložiště, který je nezbytný k udržení zabezpečeného přístupu. Zde popsaná nastavení CORS jsou vhodná pro účely rychlého startu, protože definují mírné zásady zabezpečení. Tato nastavení se však nedoporučují pro kontext skutečného světa.

### <a name="create-a-shared-access-signature"></a>Vytvoření sdíleného přístupového podpisu

Sdílený přístupový podpis (SAS) se používá v kódu spuštěném v prohlížeči k autorizaci požadavků na úložiště objektů BLOB v Azure. Díky použití SAS může klient autorizovat přístup k prostředkům úložiště, aniž by měl přístupový klíč nebo připojovací řetězec účtu. Další informace o SAS najdete v tématu [Použití sdílených přístupových podpisů (SAS)](../common/storage-sas-overview.md).

Blob service adresu URL SAS můžete získat pomocí následujících kroků:

1. V Azure Portal vyberte svůj účet úložiště.
2. Přejděte do části **Nastavení** a vyberte **sdílený přístupový podpis**.
3. Posuňte se dolů a klikněte na tlačítko **Generovat SAS a připojovací řetězec** .
4. Posuňte se dolů a vyhledejte pole **Adresa URL BLOB Service SAS** .
5. Klikněte na tlačítko **Kopírovat do schránky** v pravém horním rohu pole **adresa URL SAS BLOB Service** .
6. Uložte zkopírovanou adresu URL někam pro použití v nadcházejícím kroku.

### <a name="add-the-azure-blob-storage-client-library"></a>Přidat klientskou knihovnu služby Azure Blob Storage

V místním počítači vytvořte novou složku s názvem *Azure-BLOBs-js-Browser* a otevřete ji v Visual Studio Code.

Vyberte **zobrazit > terminálu** a otevřete okno konzoly ve Visual Studio Code. V okně terminálu spusťte následující příkaz Správce balíčků Node.js (npm) a vytvořte [package.jsv](https://docs.npmjs.com/files/package.json) souboru.

```console
npm init -y
```

Sada Azure SDK se skládá z mnoha samostatných balíčků. Můžete si vybrat, které balíčky budete potřebovat na základě služeb, které hodláte použít. Spusťte následující `npm` příkaz v okně terminálu pro instalaci `@azure/storage-blob` balíčku.

```console
npm install --save @azure/storage-blob
```

#### <a name="bundle-the-azure-blob-storage-client-library"></a>Vytvoření balíčku klientské knihovny služby Azure Blob Storage

Pokud chcete používat knihovny Azure SDK na webu, převeďte kód tak, aby fungoval v prohlížeči. Provedete to pomocí nástroje, který se nazývá sada prostředků. Sdružování používá kód JavaScriptu napsaný pomocí konvencí [Node.js](https://nodejs.org) a převede ho do formátu srozumitelného pro prohlížeče. Tento článek rychlý Start používá nástroj [parcele](https://parceljs.org/) .

Nainstalujte parcelu spuštěním následujícího `npm` příkazu v okně terminálu:

```console
npm install -g parcel-bundler
```

V Visual Studio Code otevřete *package.jsna* soubor a přidejte `browserlist` mezi `license` `dependencies` položky a. Tím `browserlist` se cílí na nejnovější verzi tří oblíbených prohlížečů. Úplný *package.js* souboru by teď měl vypadat takto:

:::code language="json" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/package.json" highlight="12-16":::

Uložte *package.jsdo* souboru.

### <a name="import-the-azure-blob-storage-client-library"></a>Import klientské knihovny pro úložiště objektů BLOB v Azure

Pokud chcete použít knihovny Azure SDK v JavaScriptu, importujte `@azure/storage-blob` balíček. Vytvořte nový soubor v Visual Studio Code, který obsahuje následující kód JavaScriptu.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_ImportLibrary":::

Uložte soubor jako *index.js* v adresáři *Azure-BLOBs-js-Browser* .

### <a name="implement-the-html-page"></a>Implementace stránky HTML

Vytvořte nový soubor v Visual Studio Code a přidejte následující kód HTML.

:::code language="html" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.html":::

Uložte soubor jako *index.html* do složky *Azure-BLOBs-js-Browser* .

## <a name="code-examples"></a>Příklady kódu

Vzorový kód ukazuje, jak provádět následující úlohy pomocí klientské knihovny Azure Blob Storage pro JavaScript:

* [Deklarace polí pro prvky uživatelského rozhraní](#declare-fields-for-ui-elements)
* [Přidání informací o účtu úložiště](#add-your-storage-account-info)
* [Vytvoření objektů klienta](#create-client-objects)
* [Vytvoření a odstranění kontejneru úložiště](#create-and-delete-a-storage-container)
* [Výpis objektů blob](#list-blobs)
* [Nahrát objekty blob](#upload-blobs)
* [Odstraňovat objekty blob](#delete-blobs)

Kód spustíte po přidání všech fragmentů kódu do souboru *index.js* .

### <a name="declare-fields-for-ui-elements"></a>Deklarace polí pro prvky uživatelského rozhraní

Na konec souboru *index.js* přidejte následující kód.

:::code language="JavaScript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_DeclareVariables":::

Uložte soubor *index.js* .

Tento kód deklaruje pole pro každý prvek HTML a implementuje `reportStatus` funkci pro zobrazení výstupu.

V následujících částech přidejte všechny nové bloky kódu JavaScriptu za předchozí blok.

### <a name="add-your-storage-account-info"></a>Přidání informací o účtu úložiště

Přidejte kód pro přístup k účtu úložiště. Zástupný symbol nahraďte adresou URL Blob service SAS, kterou jste předtím vytvořili. Na konec souboru *index.js* přidejte následující kód.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_StorageAcctInfo":::

Uložte soubor *index.js* .

### <a name="create-client-objects"></a>Vytvoření objektů klienta

Vytvářejte objekty [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) a [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) pro interakci se službou Azure Blob Storage. Na konec souboru *index.js* přidejte následující kód.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_CreateClientObjects":::

Uložte soubor *index.js* .

### <a name="create-and-delete-a-storage-container"></a>Vytvoření a odstranění kontejneru úložiště

Vytvořte a odstraňte kontejner úložiště po kliknutí na příslušné tlačítko na webové stránce. Na konec souboru *index.js* přidejte následující kód.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_CreateDeleteContainer":::

Uložte soubor *index.js* .

### <a name="list-blobs"></a>Výpis objektů blob

Po kliknutí na tlačítko **Zobrazit soubory** vypíšete obsah kontejneru úložiště. Na konec souboru *index.js* přidejte následující kód.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_ListBlobs":::

Uložte soubor *index.js* .

Tento kód volá funkci [ContainerClient. listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) a poté pomocí iterátoru načte název každého vráceného [BlobItem](/javascript/api/@azure/storage-blob/blobitem) . Pro každý `BlobItem` se v seznamu **souborů** aktualizuje hodnota vlastnosti [název](/javascript/api/@azure/storage-blob/blobitem#name) .

### <a name="upload-blobs"></a>Nahrát objekty blob

Po kliknutí na tlačítko **Vybrat a nahrát soubory** nahrajte soubory do kontejneru úložiště. Na konec souboru *index.js* přidejte následující kód.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_UploadBlobs":::

Uložte soubor *index.js* .

Tento kód propojí tlačítko **Vybrat a odeslat soubory** do skrytého `file-input` prvku. Událost tlačítka `click` aktivuje událost vstupu souboru `click` a zobrazí nástroj pro výběr souboru. Po výběru souborů a zavření dialogového okna `input` dojde k události a `uploadFiles` zavolá se funkce. Tato funkce vytvoří objekt [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) a potom zavolá funkci [uploadBrowserData](/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) pouze pro prohlížeč pro každý vybraný soubor. Každé volání vrátí `Promise` . Každá `Promise` je přidána do seznamu tak, aby mohly být všechny očekávány společně, což způsobí, že se soubory budou nahrávat paralelně.

### <a name="delete-blobs"></a>Odstraňovat objekty blob

Pokud kliknete na tlačítko **Odstranit vybrané soubory** , odstraňte soubory z kontejneru úložiště. Na konec souboru *index.js* přidejte následující kód.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_DeleteBlobs":::

Uložte soubor *index.js* .

Tento kód zavolá funkci [ContainerClient. deleteBlob](/javascript/api/@azure/storage-blob/containerclient#deleteblob-string--blobdeleteoptions-) , která odebere všechny soubory vybrané v seznamu. Pak zavolá `listFiles` funkci uvedenou výše a aktualizuje obsah seznamu **souborů** .

## <a name="run-the-code"></a>Spuštění kódu

Chcete-li spustit kód v ladicím programu Visual Studio Code, nakonfigurujte *launch.jsv* souboru pro váš prohlížeč.

### <a name="configure-the-debugger"></a>Konfigurace ladicího programu

Nastavení rozšíření ladicího programu v Visual Studio Code:

1. Vyberte **spustit > Přidat konfiguraci** .
2. Vyberte **Edge**, **Chrome**nebo **Firefox**v závislosti na tom, jaké rozšíření jste předtím nainstalovali v části [požadavky](#prerequisites) .

Přidáním nové konfigurace se vytvoří *launch.js* v souboru a otevře se v editoru. Upravte *launch.jsv* souboru tak, aby `url` byla hodnota `http://localhost:1234/index.html` , jak je znázorněno zde:

:::code language="json" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/.vscode/launch.json" highlight="11":::

Po aktualizaci uložte *launch.jsdo* souboru. Tato konfigurace oznamuje Visual Studio Code, který prohlížeč se má otevřít a která adresa URL se načte.

### <a name="launch-the-web-server"></a>Spustit webový server

Chcete-li spustit místní vývojový webový server, vyberte možnost **zobrazit > terminálu** a otevřete okno konzoly v rámci Visual Studio Code a zadejte následující příkaz.

```console
parcel index.html
```

Parcela rozbalí váš kód a spustí místní vývojový server pro vaši stránku na adrese `http://localhost:1234/index.html` . Změny, které provedete v *index.js* , se automaticky sestaví a projeví na vývojovém serveru při každém uložení souboru.

Pokud se zobrazí zpráva oznamující, že se **nepodařilo použít nakonfigurovaný port 1234**, můžete port změnit spuštěním příkazu `parcel -p <port#> index.html` . V *launch.jsv* souboru aktualizujte port v cestě URL tak, aby odpovídal.

### <a name="start-debugging"></a>Spustit ladění

Spusťte stránku v ladicím programu a získejte dojem o fungování služby Blob Storage. Pokud dojde k nějakým chybám, zobrazí se v podokně **stav** na webové stránce chybová zpráva přijata.

Chcete-li otevřít *index.html* v prohlížeči pomocí připojeného ladicího programu Visual Studio Code, vyberte **Spustit > spustit ladění** nebo stiskněte klávesu F5 v Visual Studio Code.

### <a name="use-the-web-app"></a>Použití webové aplikace

V [Azure Portal](https://portal.azure.com)můžete pomocí následujících kroků ověřit výsledky volání rozhraní API.

#### <a name="step-1---create-a-container"></a>Krok 1 – vytvoření kontejneru

1. Ve webové aplikaci vyberte **vytvořit kontejner**. Stav označuje, že byl vytvořen kontejner.
2. Pokud chcete ověřit Azure Portal, vyberte svůj účet úložiště. V části **Blob service** vyberte **Kontejnery**. Ověřte, že se nový kontejner zobrazuje. (Možná budete muset vybrat **aktualizovat**.)

#### <a name="step-2---upload-a-blob-to-the-container"></a>Krok 2 – nahrání objektu blob do kontejneru

1. V místním počítači vytvořte a uložte testovací soubor, například *test.txt*.
2. Ve webové aplikaci klikněte na **Vybrat a nahrajte soubory**.
3. Vyhledejte soubor testu a pak vyberte **otevřít**. Stav označuje, že soubor byl nahrán a seznam souborů byl načten.
4. V Azure Portal vyberte název nového kontejneru, který jste vytvořili dříve. Ověřte, že se soubor testu zobrazuje.

#### <a name="step-3---delete-the-blob"></a>Krok 3 – odstranění objektu BLOB

1. Ve webové aplikaci vyberte v části **soubory**testovací soubor.
2. Vyberte možnost **Odstranit vybrané soubory**. Stav označuje, že se soubor odstranil a že kontejner neobsahuje žádné soubory.
3. V Azure Portal vyberte **aktualizovat**. Ověřte, že se **nenašly žádné objekty blob**.

#### <a name="step-4---delete-the-container"></a>Krok 4 – odstranění kontejneru

1. Ve webové aplikaci vyberte **Odstranit kontejner**. Stav označuje, že byl kontejner odstraněn.
2. V Azure Portal vyberte ** \<account-name\> | **Odkaz na kontejnery v levém horním rohu podokna portálu.
3. Vyberte **aktualizovat**. Nový kontejner zmizí.
4. Zavřete webovou aplikaci.

### <a name="clean-up-resources"></a>Vyčištění prostředků

V Visual Studio Code klikněte na konzolu **terminálu** a stisknutím kombinace kláves CTRL + C zastavte webový server.

Pokud chcete vyčistit prostředky vytvořené v rámci tohoto rychlého startu, otevřete [Azure Portal](https://portal.azure.com) a odstraňte skupinu prostředků, kterou jste vytvořili v části [požadavky](#prerequisites) .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nahrát, vypsat a odstranit objekty BLOB pomocí JavaScriptu. Zjistili jste také, jak vytvořit a odstranit kontejner úložiště objektů BLOB.

Výukové programy, ukázky, rychlé starty a další dokumentaci najdete na webu:

> [!div class="nextstepaction"]
> [Dokumentace k Azure pro JavaScript](/azure/developer/javascript/)

* Další informace najdete v tématu [Klientská knihovna pro úložiště objektů BLOB v Azure pro JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob).
* Ukázková aplikace služby Blob Storage najdete v [ukázkách V12 JavaScriptu v klientské knihovně pro úložiště objektů BLOB v Azure](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples).
