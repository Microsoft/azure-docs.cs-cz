---
title: 'Rychlý Start: V12 knihovny pro Azure Blob Storage – .NET'
description: V tomto rychlém startu se dozvíte, jak pomocí klientské knihovny Azure Blob Storage verze 12 pro .NET vytvořit kontejner a objekt BLOB v úložišti objektů BLOB (objekt). Dále se dozvíte, jak stáhnout objekt blob do místního počítače a jak zobrazit seznam všech objektů blob, které jsou v kontejneru.
author: twooley
ms.author: twooley
ms.date: 03/03/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: f913b33d0bea425a24d2fd336c9d065978606e82
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869252"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Rychlý Start: V12 klientské knihovny Azure Blob Storage pro .NET

Začínáme s klientskou knihovnou V12 pro Azure Blob Storage pro .NET Azure Blob Storage je řešení úložiště objektů od Microsoftu pro Cloud. Postupujte podle kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy. Služba Blob Storage je optimalizovaná pro ukládání velkých objemů nestrukturovaných dat.

Pomocí V12 klientské knihovny Azure Blob Storage pro .NET:

* Vytvoření kontejneru
* Nahrání objektu blob do Azure Storage
* Výpis všech objektů BLOB v kontejneru
* Stažení objektu blob do místního počítače
* Odstranění kontejneru

Další prostředky:

* [Referenční dokumentace k rozhraní API](/dotnet/api/azure.storage.blobs)
* [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Balíček (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [ukázky](../common/storage-samples-dotnet.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Účet úložiště Azure – [Vytvoření účtu úložiště](../common/storage-account-create.md)
* Aktuální [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) pro váš operační systém. Ujistěte se, že jste získali sadu SDK a ne modul runtime.

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s V12 klientské knihovny Azure Blob Storage pro .NET.

### <a name="create-the-project"></a>Vytvoření projektu

Vytvořte aplikaci .NET Core s názvem *BlobQuickstartV12*.

1. V okně konzoly (například cmd, PowerShell nebo bash) pomocí `dotnet new` příkazu vytvořte novou konzolovou aplikaci s názvem *BlobQuickstartV12*. Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *program. cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Přepněte do nově vytvořeného adresáře *BlobQuickstartV12* .

   ```console
   cd BlobQuickstartV12
   ```

1. Na straně adresáře *BlobQuickstartV12* vytvořte další adresář s názvem *data*. V tomto umístění se vytvoří a uloží datové soubory objektů BLOB.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instalace balíčku

I když v adresáři aplikace ještě pořád nainstalujete balíček klientské knihovny Azure Blob Storage pro .NET pomocí `dotnet add package` příkazu.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Z adresáře projektu:

1. V editoru otevřete soubor *program. cs* .
1. Odeberte `Console.WriteLine("Hello World!");` příkaz.
1. Přidat `using` direktivy.
1. Aktualizujte `Main` deklaraci metody tak, aby podporovala asynchronní.

    Zde je kód:

    :::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/app_framework.cs":::

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektový model

Azure Blob Storage je optimalizovaná pro ukládání obrovských objemů nestrukturovaných dat. Jde o data, která nevyhovují konkrétnímu datovému modelu nebo definici, například textová nebo binární data. Úložiště objektů BLOB nabízí tři typy prostředků:

* Účet úložiště
* Kontejner v účtu úložiště
* Objekt BLOB v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury služby Blob Storage](./media/storage-blobs-introduction/blob1.png)

Pro interakci s těmito prostředky použijte následující třídy .NET:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): `BlobServiceClient` Třída umožňuje manipulovat s Azure Storage prostředky a kontejnery objektů BLOB.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): `BlobContainerClient` Třída umožňuje manipulovat s kontejnery Azure Storage a jejich objekty blob.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): `BlobClient` Třída umožňuje manipulovat s objekty blob Azure Storage.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): `BlobDownloadInfo` Třída představuje vlastnosti a obsah vrácený stažením objektu BLOB.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provést následující s klientskou knihovnou Azure Blob Storage pro .NET:

* [Získání připojovacího řetězce](#get-the-connection-string)
* [Vytvoření kontejneru](#create-a-container)
* [Nahrání objektů blob do kontejneru](#upload-blobs-to-a-container)
* [Seznam objektů blob v kontejneru](#list-the-blobs-in-a-container)
* [Stáhnout objekty blob](#download-blobs)
* [Odstranění kontejneru](#delete-a-container)

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Následující kód načte připojovací řetězec pro účet úložiště z proměnné prostředí vytvořené v oddílu [konfigurace vašeho připojovacího řetězce úložiště](#configure-your-storage-connection-string) .

Přidejte tento kód do `Main` metody:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Vytvoření kontejneru

Určete název nového kontejneru. Následující kód připojí hodnotu identifikátoru GUID k názvu kontejneru, aby bylo zajištěno, že je jedinečný.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Vytvořte instanci třídy [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) . Pak zavolejte metodu [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) a vytvořte kontejner v účtu úložiště.

Přidejte tento kód na konec `Main` metody:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Nahrání objektů blob do kontejneru

Následující fragment kódu:

1. Vytvoří textový soubor v místním *datovém* adresáři.
1. Získá odkaz na objekt [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) voláním metody [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) na kontejneru z oddílu [vytvoření kontejneru](#create-a-container) .
1. Nahraje místní textový soubor do objektu BLOB voláním metody [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync#Azure_Storage_Blobs_BlobClient_UploadAsync_System_IO_Stream_System_Boolean_System_Threading_CancellationToken_) . Tato metoda vytvoří objekt blob, pokud ještě neexistuje, a přepíše ho, pokud už existoval.

Přidejte tento kód na konec `Main` metody:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Seznam objektů blob v kontejneru

Seznam objektů BLOB v kontejneru zavoláním metody [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) . V tomto případě byl do kontejneru přidán pouze jeden objekt blob, takže operace výpisu vrátí pouze jeden objekt BLOB.

Přidejte tento kód na konec `Main` metody:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Stáhnout objekty blob

Stáhněte dřív vytvořený objekt BLOB voláním metody [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync) . Vzorový kód přidá příponu "stažený" do názvu souboru, aby bylo možné zobrazit oba soubory v místním systému souborů.

Přidejte tento kód na konec `Main` metody:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Odstranění kontejneru

Následující kód vyčistí prostředky, které aplikace vytvořila, odstraněním celého kontejneru pomocí [DeleteAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteasync). Odstraní také místní soubory vytvořené aplikací.

Aplikace pozastaví vstup uživatele voláním `Console.ReadLine` před odstraněním objektu blob, kontejneru a místních souborů. Tato možnost je vhodná pro ověření, že prostředky byly ve skutečnosti vytvořeny správně, než byly odstraněny.

Přidejte tento kód na konec `Main` metody:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>Spuštění kódu

Tato aplikace vytvoří testovací soubor ve složce místních *dat* a nahraje ho do úložiště objektů BLOB. Příklad zobrazí seznam objektů BLOB v kontejneru a stáhne soubor s novým názvem, abyste mohli porovnat staré a nové soubory.

Přejděte do adresáře aplikace a pak Sestavte a spusťte aplikaci.

```console
dotnet build
```

```console
dotnet run
```

Výstup aplikace je podobný následujícímu příkladu:

```output
Azure Blob Storage v12 - .NET quickstart sample

Uploading to Blob storage as blob:
         https://mystorageacct.blob.core.windows.net/quickstartblobs60c70d78-8d93-43ae-954d-8322058cfd64/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Listing blobs...
        quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Downloading blob to
        ./data/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31DOWNLOADED.txt

Press any key to begin clean up
Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Než zahájíte proces vyčištění, vyhledejte tyto dva soubory ve složce *dat* . Můžete je otevřít a podívat se, že jsou identické.

Po ověření souborů stiskněte klávesu **ENTER** , aby se odstranily soubory testu, a dokončete ukázku.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nahrávat, stahovat a vypisovat objekty blob pomocí .NET.

Pokud chcete zobrazit ukázkové aplikace služby Blob Storage, pokračujte:

> [!div class="nextstepaction"]
> [Ukázky V12 .NET pro Azure Blob Storage SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* Kurzy, ukázky, rychlé starty a další dokumentace najdete na webu [Azure pro vývojáře na platformě .NET a .NET Core](/dotnet/azure/).
* Další informace o .NET Core najdete v tématu [Začínáme s .NET během 10 minut](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/intro).
