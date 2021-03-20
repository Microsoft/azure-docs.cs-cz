---
title: 'Rychlý Start: knihovna pro Azure Blob Storage V12 – Python'
description: V tomto rychlém startu se dozvíte, jak pomocí klientské knihovny Azure Blob Storage verze 12 pro Python vytvořit kontejner a objekt BLOB v úložišti objektů BLOB (objekt). Dále se dozvíte, jak stáhnout objekt blob do místního počítače a jak zobrazit seznam všech objektů blob, které jsou v kontejneru.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/28/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: e315f0f4f7bfff03a659de430e6fe182037f1b8a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99096402"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Rychlý Start: Správa objektů BLOB pomocí sady Python V12 SDK

V tomto rychlém startu se naučíte spravovat objekty BLOB pomocí Pythonu. Objekty blob jsou objekty, které mohou obsahovat velké objemy textových nebo binárních dat, včetně obrázků, dokumentů, datových proudů médií a dat archivu. Můžete nahrávat, stahovat a vypisovat objekty BLOB a vytvářet a odstraňovat kontejnery.

Další zdroje informací:

* [Referenční dokumentace k rozhraní API](/python/api/azure-storage-blob)
* [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
* [Balíček (index balíčku Pythonu)](https://pypi.org/project/azure-storage-blob/)
* [ukázky](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Účet služby Azure Storage. [Vytvořte účet úložiště](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2,7, 3,5 nebo novější.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnou Azure Blob Storage V12 for Python.

### <a name="create-the-project"></a>Vytvoření projektu

Vytvořte aplikaci v Pythonu s názvem *BLOB-Start-V12*.

1. V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro projekt.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Přepněte do nově vytvořeného adresáře pro *rychlý Start objektů BLOB-V12* .

    ```console
    cd blob-quickstart-v12
    ```

1. V adresáři *objektů BLOB pro rychlý Start – V12* vytvořte další adresář s názvem *data*. V tomto adresáři se budou vytvářet a ukládat datové soubory objektů BLOB.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instalace balíčku

Ještě pořád v adresáři aplikace nainstalujte knihovnu klienta Azure Blob Storage pro balíček python pomocí `pip install` příkazu.

```console
pip install azure-storage-blob
```

Tento příkaz nainstaluje knihovnu klienta Azure Blob Storage pro balíček Pythonu a všechny knihovny, na kterých závisí. V tomto případě je to jenom knihovna Azure Core pro Python.

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Z adresáře projektu:

1. Otevřít nový textový soubor v editoru kódu
1. Přidat `import` příkazy
1. Vytvoření struktury pro program, včetně základního zpracování výjimek

    Zde je kód:

    :::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/app_framework.py":::

1. Uložte nový soubor jako *BLOB-Quickstart-V12.py* do adresáře *objektů BLOB-rychlý Start-V12* .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektový model

Azure Blob Storage je optimalizovaná pro ukládání obrovských objemů nestrukturovaných dat. Nestrukturovaná data jsou data, která nevyhovují konkrétnímu datovému modelu nebo definici, jako jsou textová nebo binární data. Úložiště objektů BLOB nabízí tři typy prostředků:

* Účet úložiště
* Kontejner v účtu úložiště
* Objekt BLOB v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury služby Blob Storage](./media/storage-blobs-introduction/blob1.png)

Pro interakci s těmito prostředky použijte následující třídy Pythonu:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): `BlobServiceClient` Třída umožňuje manipulovat s Azure Storage prostředky a kontejnery objektů BLOB.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): `ContainerClient` Třída umožňuje manipulovat s kontejnery Azure Storage a jejich objekty blob.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): `BlobClient` Třída umožňuje manipulovat s objekty blob Azure Storage.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny Azure Blob Storage pro Python:

* [Získání připojovacího řetězce](#get-the-connection-string)
* [Vytvoření kontejneru](#create-a-container)
* [Nahrání objektů blob do kontejneru](#upload-blobs-to-a-container)
* [Seznam objektů blob v kontejneru](#list-the-blobs-in-a-container)
* [Stáhnout objekty blob](#download-blobs)
* [Odstranění kontejneru](#delete-a-container)

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Následující kód načte připojovací řetězec účtu úložiště z proměnné prostředí vytvořené v oddílu [konfigurace vašeho připojovacího řetězce úložiště](#configure-your-storage-connection-string) .

Přidejte tento kód do `try` bloku:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Vytvoření kontejneru

Určete název nového kontejneru. Následující kód připojí hodnotu UUID k názvu kontejneru, aby bylo zajištěno, že je jedinečný.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Vytvořte instanci třídy [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) voláním metody [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) . Pak zavolejte metodu [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) , aby se kontejner ve vašem účtu úložiště skutečně vytvořil.

Přidejte tento kód na konec `try` bloku:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Nahrání objektů blob do kontejneru

Následující fragment kódu:

1. Vytvoří místní adresář pro ukládání datových souborů.
1. Vytvoří textový soubor v místním adresáři.
1. Získá odkaz na objekt [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) voláním metody [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) v [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) z oddílu [vytvoření kontejneru](#create-a-container) .
1. Nahraje místní textový soubor do objektu BLOB voláním metody [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) .

Přidejte tento kód na konec `try` bloku:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Seznam objektů blob v kontejneru

Vypíše objekty BLOB v kontejneru voláním metody [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) . V tomto případě byl do kontejneru přidán pouze jeden objekt blob, takže operace výpisu vrátí pouze jeden objekt BLOB.

Přidejte tento kód na konec `try` bloku:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Stáhnout objekty blob

Stáhněte dřív vytvořený objekt BLOB voláním metody [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) . Vzorový kód přidá příponu "DOWNLOAD" do názvu souboru, aby bylo možné zobrazit oba soubory v místním systému souborů.

Přidejte tento kód na konec `try` bloku:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Odstranění kontejneru

Následující kód vyčistí prostředky, které aplikace vytvořila, odebráním celého kontejneru pomocí metody [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) . Pokud chcete, můžete také odstranit místní soubory.

Aplikace pozastaví vstup uživatele voláním `input()` před odstraněním objektu blob, kontejneru a místních souborů. Před odstraněním ověřte, že se prostředky správně vytvořily.

Přidejte tento kód na konec `try` bloku:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CleanUp":::

## <a name="run-the-code"></a>Spuštění kódu

Tato aplikace vytvoří testovací soubor v místní složce a nahraje ho do služby Azure Blob Storage. Příklad zobrazí seznam objektů BLOB v kontejneru a stáhne soubor s novým názvem. Můžete porovnat staré a nové soubory.

Přejděte do adresáře obsahujícího soubor *BLOB-Quickstart-V12.py* a `python` spuštěním následujícího příkazu spusťte aplikaci.

```console
python blob-quickstart-v12.py
```

Výstup aplikace je podobný následujícímu příkladu:

```output
Azure Blob Storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Než zahájíte proces čištění, vyhledejte ve složce *data* tyto dva soubory. Můžete je otevřít a sledovat, že jsou identické.

Po ověření souborů stiskněte klávesu **ENTER** , aby se odstranily soubory testu, a dokončete ukázku.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nahrávat, stahovat a vypisovat objekty BLOB pomocí Pythonu.

Pokud chcete zobrazit ukázkové aplikace služby Blob Storage, pokračujte:

> [!div class="nextstepaction"]
> [Ukázky pro Azure Blob Storage SDK V12 Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Další informace najdete v tématu [klientské knihovny Azure Storage pro Python](/azure/developer/python/sdk/storage/overview).
* Výukové programy, ukázky, rychlé starty a další dokumentaci najdete v [Azure pro vývojáře v Pythonu](/azure/python/).
