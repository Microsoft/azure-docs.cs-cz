---
title: 'Úvodní příručka: Knihovna úložiště objektů blob Azure v12 – Python'
description: V tomto rychlém startu se dozvíte, jak pomocí knihovny klienta úložiště objektů Blob Azure verze 12 pro Python vytvořit kontejner a objekt blob v úložišti objektů blob (objekt). Dále se dozvíte, jak stáhnout objekt blob do místního počítače a jak zobrazit seznam všech objektů blob, které jsou v kontejneru.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 8daf7380e859cd2f9b5890c716f7b7d95e6c3fe4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061334"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Úvodní příručka: Správa objektů BLOB pomocí pythonu v12 SDK

V tomto rychlém startu se naučíte spravovat objekty BLOB pomocí Pythonu. Objekty BLOB jsou objekty, které mohou obsahovat velké množství textových nebo binárních dat, včetně obrázků, dokumentů, datových proudů médií a archivních dat. Nahrajete, stáhnete a zobrazíte objekty BLOB a vytvoříte a odstraníte kontejnery.

[Referenční dokumentace](/python/api/azure-storage-blob) | rozhraní[API Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob) | [knihovny](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples) [(Python Package Index)](https://pypi.org/project/azure-storage-blob/) | 

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Účet služby Azure Storage. [Vytvořte účet úložiště](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2.7, 3.5 nebo vyšší.

> [!NOTE]
> Pokud chcete začít s předchozí verzí sady SDK, přečtěte si [úvodní příručku: Správa objektů BLOB pomocí pythonu 2.1 SDK](storage-quickstart-blobs-python-legacy.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnou úložiště objektů Blob Azure v12 pro Python.

### <a name="create-the-project"></a>Vytvoření projektu

Vytvořte aplikaci Pythonu s názvem *blob-quickstart-v12*.

1. V okně konzoly (například cmd, PowerShell nebo Bash) vytvořte nový adresář pro projekt.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Přepněte do nově vytvořeného *adresáře blob-quickstart-v12.*

    ```console
    cd blob-quickstart-v12
    ```

1. Na straně *adresáře blob-quickstart-v12* vytvořte jiný adresář s názvem *data*. Toto je místo, kde budou vytvořeny a uloženy datové soubory objektů blob.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instalace balíčku

Zatímco ještě v adresáři aplikace, nainstalujte klientskou knihovnu `pip install` úložiště Azure Blob pro balíček Python pomocí příkazu.

```console
pip install azure-storage-blob
```

Tento příkaz nainstaluje klientskou knihovnu úložiště objektů Blob Azure pro balíček Pythonu a všechny knihovny, na kterých závisí. V tomto případě je to jen základní knihovna Azure pro Python.

### <a name="set-up-the-app-framework"></a>Nastavení architektury aplikace

Z adresáře projektu:

1. Otevření nového textového souboru v editoru kódu
1. Přidat `import` příkazy
1. Vytvoření struktury programu, včetně základního zpracování výjimek

    Zde je kód:

    ```python
    import os, uuid
    from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

    try:
        print("Azure Blob storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

1. Uložte nový soubor jako *blob-quickstart-v12.py* v adresáři *blob-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektový model

Úložiště objektů blob Azure je optimalizované pro ukládání obrovského množství nestrukturovaných dat. Jde o data, která nevyhovují konkrétnímu datovému modelu nebo definici, například textová nebo binární data. Úložiště objektů blob nabízí tři typy prostředků:

* Účet úložiště
* Kontejner v účtu úložiště
* Objekt blob v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury úložiště objektů blob](./media/storage-blobs-introduction/blob1.png)

K interakci s těmito prostředky použijte následující třídy Pythonu:

* [BlobServiceClient:](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) `BlobServiceClient` Třída umožňuje manipulovat s prostředky úložiště Azure a kontejnery objektů blob.
* [ContainerClient:](/python/api/azure-storage-blob/azure.storage.blob.containerclient) `ContainerClient` Třída umožňuje manipulovat s kontejnery azure storage a jejich objekty BLOB.
* [Objekt BlobClient:](/python/api/azure-storage-blob/azure.storage.blob.blobclient)Třída `BlobClient` umožňuje manipulovat s objekty BLOB úložiště Azure.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provést následující akce v klientské knihovně úložiště Azure Blob pro Python:

* [Získání připojovacího řetězce](#get-the-connection-string)
* [Vytvoření kontejneru](#create-a-container)
* [Nahrání objektů BLOB do kontejneru](#upload-blobs-to-a-container)
* [Zobrazí seznam objektů blob v kontejneru](#list-the-blobs-in-a-container)
* [Stáhnout objekty blob](#download-blobs)
* [Odstranění kontejneru](#delete-a-container)

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Níže uvedený kód načte připojovací řetězec pro účet úložiště z proměnné prostředí vytvořené v části [Konfigurovat připojovací řetězec úložiště.](#configure-your-storage-connection-string)

Přidejte tento `try` kód do bloku:

```python
# Retrieve the connection string for use with the application. The storage
# connection string is stored in an environment variable on the machine
# running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-container"></a>Vytvoření kontejneru

Rozhodněte o názvu nového kontejneru. Níže uvedený kód připojí hodnotu UUID k názvu kontejneru, aby bylo zajištěno, že je jedinečný.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Vytvořte instanci třídy [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) voláním metody [from_connection_string.](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) Potom zavolejte [metodu create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) skutečně vytvořit kontejner ve vašem účtu úložiště.

Přidejte tento kód na `try` konec bloku:

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>Nahrání objektů BLOB do kontejneru

Následující fragment kódu:

1. Vytvoří textový soubor v místním adresáři.
1. Získá odkaz na objekt [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) voláním [metody get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) na [blobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) z [oddílu Vytvořit kontejner.](#create-a-container)
1. Odešle místní textový soubor do objektu blob voláním [metody upload_blob.](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-)

Přidejte tento kód na `try` konec bloku:

```python
# Create a file in local data directory to upload and download
local_path = "./data"
local_file_name = "quickstart" + str(uuid.uuid4()) + ".txt"
upload_file_path = os.path.join(local_path, local_file_name)

# Write text to the file
file = open(upload_file_path, 'w')
file.write("Hello, World!")
file.close()

# Create a blob client using the local file name as the name for the blob
blob_client = blob_service_client.get_blob_client(container=container_name, blob=local_file_name)

print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)

# Upload the created file
with open(upload_file_path, "rb") as data:
    blob_client.upload_blob(data)
```

### <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Seznam objektů BLOB v kontejneru voláním [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) metody. V tomto případě byl do kontejneru přidán pouze jeden objekt blob, takže operace výpisu vrátí pouze jeden objekt blob.

Přidejte tento kód na `try` konec bloku:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Stáhnout objekty blob

Stáhněte si dříve vytvořený objekt blob voláním [metody download_blob.](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) Ukázkový kód přidá k názvu souboru příponu "DOWNLOAD", takže oba soubory můžete zobrazit v místním systému souborů.

Přidejte tento kód na `try` konec bloku:

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in the data directory
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>Odstranění kontejneru

Následující kód vyčistí prostředky, které aplikace vytvořila odebráním celého kontejneru pomocí [metody delete_container.](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) Můžete také odstranit místní soubory, pokud chcete.

Aplikace pozastaví vstup uživatele `input()` voláním před odstraněním objektu blob, kontejneru a místních souborů. Toto je dobrá šance ověřit, že prostředky byly vytvořeny správně, před jejich odstraněním.

Přidejte tento kód na `try` konec bloku:

```python
# Clean up
print("\nPress the Enter key to begin clean up")
input()

print("Deleting blob container...")
container_client.delete_container()

print("Deleting the local source and downloaded files...")
os.remove(upload_file_path)
os.remove(download_file_path)

print("Done")
```

## <a name="run-the-code"></a>Spuštění kódu

Tato aplikace vytvoří testovací soubor v místní složce a nahraje jej do úložiště objektů Blob. V příkladu jsou uvedeny objekty BLOB v kontejneru a stáhne soubor s novým názvem, abyste mohli porovnat staré a nové soubory.

Přejděte do adresáře obsahujícího *blob-quickstart-v12.py* soubor `python` a spusťte následující příkaz pro spuštění aplikace.

```console
python blob-quickstart-v12.py
```

Výstup aplikace je podobný následujícímu příkladu:

```output
Azure Blob storage v12 - Python quickstart sample

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

Než začnete proces čištění, zkontrolujte, zda ve složce *dat* neobsahuje dva soubory. Můžete je otevřít a podívat se, že jsou identické.

Po ověření souborů odstraňte testovací soubory stisknutím klávesy **Enter** a dokončete ukázku.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak nahrát, stáhnout a seznam objektů BLOB pomocí Pythonu.

Pokud chcete zobrazit ukázkové aplikace úložiště objektů Blob, pokračujte v:

> [!div class="nextstepaction"]
> [Ukázky Pythonu úložiště objektů blob Azure](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Další informace najdete v [tématu Azure SDK pro Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/README.md).
* Kurzy, ukázky, rychlé starty a další dokumentaci najdete na webu [Azure for Python Developers](/azure/python/).
