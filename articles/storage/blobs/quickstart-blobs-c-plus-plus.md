---
title: 'Rychlý Start: knihovna Azure Blob Storage V12-C++'
description: V tomto rychlém startu se dozvíte, jak pomocí klientské knihovny Azure Blob Storage verze 12 pro C++ vytvořit kontejner a objekt BLOB v úložišti objektů BLOB (objekt). Dále se dozvíte, jak stáhnout objekt blob do místního počítače a jak zobrazit seznam všech objektů blob, které jsou v kontejneru.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/21/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 246d2677c784daee4cc6b083cff04b6993ab3818
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006485"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-c"></a>Rychlý Start: V12 klientské knihovny Azure Blob Storage pro C++

Začínáme s klientskou knihovnou Azure Blob Storage V12 pro C++. Azure Blob Storage je řešení úložiště objektů od Microsoftu pro Cloud. Postupujte podle kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy. Blob Storage je optimalizovaná pro ukládání obrovských objemů nestrukturovaných dat.

Pomocí V12 klientské knihovny Azure Blob Storage pro C++:

- Vytvoření kontejneru
- Nahrání objektu blob do Azure Storage
- Výpis všech objektů BLOB v kontejneru
- Stažení objektu blob do místního počítače
- Odstranění kontejneru

Prostředky:

- [Referenční dokumentace k rozhraní API](https://azure.github.io/azure-sdk-for-cpp/storage.html)
- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage)
- [ukázky](../common/storage-samples-c-plus-plus.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Požadavky

- [Předplatné Azure](https://azure.microsoft.com/free/)
- [Účet úložiště Azure](../common/storage-account-create.md)
- [Kompilátor C++](https://azure.github.io/azure-sdk/cpp_implementation.html#supported-platforms)
- [CMake](https://cmake.org/)
- [Správce balíčků Vcpkg-C a C++](https://github.com/microsoft/vcpkg/blob/master/docs/index.md)
- [LibCurl](https://curl.haxx.se/libcurl/)
- [LibXML2](http://www.xmlsoft.org/)

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s V12 klientské knihovny Azure Blob Storage pro C++.

### <a name="install-the-packages"></a>Nainstalovat balíčky

Pokud jste to ještě neudělali, nainstalujte balíčky LibCurl a LibXML2 pomocí `vcpkg install` příkazu.

```console
vcpkg.exe install libxml2:x64-windows curl:x64-windows
```

Pokud chcete získat a sestavit [sadu Azure SDK pro C++](https://github.com/Azure/azure-sdk-for-cpp/), postupujte podle pokynů na GitHubu.

### <a name="create-the-project"></a>Vytvoření projektu

V aplikaci Visual Studio vytvořte novou konzolovou aplikaci C++ pro Windows s názvem *BlobQuickstartV12*.

:::image type="content" source="./media/quickstart-blobs-c-plus-plus/vs-create-project.jpg" alt-text="Dialog sady Visual Studio pro konfiguraci nové konzolové aplikace Windows C++":::

Do projektu přidejte následující knihovny:

- libcurl. lib
- libxml2. lib
- symetrickými. lib
- Verze souboru crypt32. lib
- WS2_32. lib
- Azure-Core. lib
- Azure-Storage-Common. lib
- Azure-Storage-BLOBs. lib

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektový model

Azure Blob Storage je optimalizovaná pro ukládání obrovských objemů nestrukturovaných dat. Nestrukturovaná data jsou data, která nevyhovují konkrétnímu datovému modelu nebo definici, jako jsou textová nebo binární data. Blob Storage nabízí tři typy prostředků:

- Účet úložiště
- Kontejner v účtu úložiště
- Objekt BLOB v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury Blob Storage](./media/storage-blobs-introduction/blob1.png)

Pro interakci s těmito prostředky použijte následující třídy jazyka C++:

- [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_service_client.html): `BlobServiceClient` Třída umožňuje manipulovat s Azure Storage prostředky a kontejnery objektů BLOB.
- [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html): `BlobContainerClient` Třída umožňuje manipulovat s kontejnery Azure Storage a jejich objekty blob.
- [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html): `BlobClient` Třída umožňuje manipulovat s objekty blob Azure Storage. Je základní třídou pro všechny specializované třídy objektů BLOB.
- [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html): `BlockBlobClient` Třída umožňuje manipulovat s objekty blob bloku Azure Storage.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny Azure Blob Storage pro C++:

- [Přidat soubory k zahrnutí](#add-include-files)
- [Získání připojovacího řetězce](#get-the-connection-string)
- [Vytvoření kontejneru](#create-a-container)
- [Nahrání objektů blob do kontejneru](#upload-blobs-to-a-container)
- [Seznam objektů blob v kontejneru](#list-the-blobs-in-a-container)
- [Stáhnout objekty blob](#download-blobs)
- [Odstranění kontejneru](#delete-a-container)

### <a name="add-include-files"></a>Přidat soubory k zahrnutí

Z adresáře projektu:

1. Otevřete soubor řešení *BlobQuickstartV12. sln* v aplikaci Visual Studio.
1. V aplikaci Visual Studio otevřete zdrojový soubor *BlobQuickstartV12. cpp.*
1. Odebrat veškerý kód uvnitř `main` , který byl automaticky vygenerován
1. Přidat `#include` příkazy

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_Includes":::

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Následující kód načte připojovací řetězec pro váš účet úložiště z proměnné prostředí vytvořené v [konfiguraci připojovacího řetězce úložiště](#configure-your-storage-connection-string).

Přidat tento kód dovnitř `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Vytvoření kontejneru

Vytvořte instanci třídy [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html) voláním funkce [CreateFromConnectionString](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ad103d1e3a7ce7c53a82da887d12ce6fe) . Pak voláním [Create](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#a22a1e001068a4ec52bb1b6bd8b52c047) vytvořte skutečný kontejner v účtu úložiště.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Přidejte tento kód na konec `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Nahrání objektů blob do kontejneru

Následující fragment kódu:

1. Deklaruje řetězec, který obsahuje Hello Azure!.
1. Načte odkaz na objekt [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html) voláním [GetBlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#acd8c68e3f37268fde0010dd478ff048f) na kontejneru z oddílu [vytvoření kontejneru](#create-a-container) .
1. Nahraje řetězec do objektu BLOB voláním funkce [UploadFrom](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html#af93af7e37f8806e39481596ef253f93d) . Tato funkce vytvoří objekt blob, pokud ještě neexistuje, nebo ho aktualizuje, pokud má.

Přidejte tento kód na konec `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_UploadBlob":::

### <a name="list-the-blobs-in-a-container"></a>Seznam objektů blob v kontejneru

Vypíše objekty BLOB v kontejneru voláním funkce [ListBlobsFlatSegment](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ac7712bc46909dc061d6bf554b496550c) . Do kontejneru byl přidán pouze jeden objekt blob, takže operace vrátí pouze tento objekt BLOB.

Přidejte tento kód na konec `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Stáhnout objekty blob

Získá vlastnosti nahraného objektu BLOB. Pak Deklarujte a změňte velikost nového `std::string` objektu pomocí vlastností nahraného objektu BLOB. Stáhněte dříve vytvořený objekt blob do nového `std::string` objektu voláním funkce [DownloadTo](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#aa844f37a8c216f3cb0f27912b114c4d2) v základní třídě [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html) . Nakonec zobrazte data staženého objektu BLOB.

Přidejte tento kód na konec `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DownloadBlob":::

### <a name="delete-a-blob"></a>Odstranění objektu BLOB

Následující kód odstraní objekt BLOB z kontejneru Azure Blob Storage voláním funkce [BlobClient. Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#a621eabcc8d23893ca1eb106494198615) .

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteBlob":::

### <a name="delete-a-container"></a>Odstranění kontejneru

Následující kód vyčistí prostředky, které aplikace vytvořila, odstraněním celého kontejneru pomocí [BlobContainerClient. Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#aa6b1db52697ae92e9a1227e2e02a5178).

Přidejte tento kód na konec `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>Spuštění kódu

Tato aplikace vytvoří kontejner a nahraje textový soubor do Azure Blob Storage. Příklad následně vypíše objekty BLOB v kontejneru, stáhne soubor a zobrazí obsah souboru. Nakonec aplikace odstraní objekt BLOB a kontejner.

Výstup aplikace je podobný následujícímu příkladu:

```output
Azure Blob Storage v12 - C++ quickstart sample
Creating container: myblobcontainer
Uploading blob: blob.txt
Listing blobs...
Blob name: blob.txt
Downloaded blob contents: Hello Azure!
Deleting blob: blob.txt
Deleting container: myblobcontainer
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nahrávat, stahovat a vypisovat objekty BLOB pomocí C++. Zjistili jste také, jak vytvořit a odstranit kontejner Azure Blob Storage.

Chcete-li zobrazit ukázku Blob Storage jazyka C++, pokračujte:

> [!div class="nextstepaction"]
> [Ukázka Azure Blob Storage SDK V12 for C++](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-blobs/sample)