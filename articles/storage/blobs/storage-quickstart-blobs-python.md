---
title: 'Rychlý Start: knihovna úložiště objektů BLOB v Azure V12 – Python'
description: V tomto rychlém startu se dozvíte, jak pomocí klientské knihovny Azure Blob Storage verze 12 pro Python vytvořit kontejner a objekt BLOB v úložišti objektů BLOB (objekt). Dále se dozvíte, jak stáhnout objekt blob do místního počítače a jak zobrazit seznam všech objektů blob, které jsou v kontejneru.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: d589215cf79154bcc8aead1d6695bd4cf870fc0a
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423968"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-python"></a>Rychlý Start: Klientská knihovna pro úložiště objektů BLOB v Azure V12 pro Python

Začněte s klientskou knihovnou V12 Azure Blob Storage pro Python. Azure Blob Storage je řešení úložiště objektů Microsoftu pro cloud. Postupujte podle kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy. Úložiště objektů blob je optimalizované pro ukládání velkých objemů nestrukturovaných dat.

> [!NOTE]
> Informace o tom, jak začít s předchozí verzí sady SDK, najdete v tématu [rychlý Start: Klientská knihovna pro Azure Blob Storage pro Python](storage-quickstart-blobs-python-legacy.md).

Použijte klientskou knihovnu služby Azure Blob Storage k těmto akcím:

* Vytvoření kontejneru
* Nahrání objektu blob do Azure Storage
* Výpis všech objektů BLOB v kontejneru
* Stažení objektu blob do místního počítače
* Odstranění kontejneru

[Referenční dokumentace k rozhraní API](/python/api/azure-storage-blob) |  | balíčku [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob) [(Python Package index)](https://pypi.org/project/azure-storage-blob/) | [ukázky](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Účet úložiště Azure – [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Python](https://www.python.org/downloads/) pro váš operační systém – 2,7, 3,5 nebo novější

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnou služby Azure Blob Storage V12 for Python.

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

1. V adresáři *objektů BLOB pro rychlý Start – V12* vytvořte další adresář s názvem *data*. V tomto umístění se vytvoří a uloží datové soubory objektů BLOB.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instalace balíčku

Ještě pořád v adresáři aplikace nainstalujte klientskou knihovnu pro úložiště objektů BLOB v Azure pro balíček Pythonu pomocí příkazu `pip install`.

```console
pip install azure-storage-blob
```

Tento příkaz nainstaluje klientskou knihovnu pro úložiště objektů BLOB v Azure pro balíček Pythonu a všechny knihovny, na kterých závisí. V tomto případě je to jenom knihovna Azure Core pro Python.

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Z adresáře projektu:

1. Otevřít nový textový soubor v editoru kódu
1. Přidat `import` příkazy
1. Vytvoření struktury pro program, včetně velmi základního zpracování výjimek

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

1. Uložte nový soubor jako *BLOB-Quickstart-V12.py* do adresáře *objektů BLOB-rychlý Start-V12* .

[!INCLUDE [storage-quickstart-connection-string-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektový model

Úložiště objektů BLOB v Azure je optimalizované pro ukládání obrovských objemů nestrukturovaných dat. Nestrukturovaná data jsou data, která nevyhovují konkrétnímu datovému modelu nebo definici, jako jsou textová nebo binární data. Úložiště objektů BLOB nabízí tři typy prostředků:

* Účet úložiště
* Kontejner v účtu úložiště
* Objekt BLOB v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury služby Blob Storage](./media/storage-blob-introduction/blob1.png)

Pro interakci s těmito prostředky použijte následující třídy Pythonu:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): třída `BlobServiceClient` umožňuje manipulovat s prostředky Azure Storage a kontejnery objektů BLOB.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): třída `ContainerClient` umožňuje manipulovat s kontejnery Azure Storage a jejich objekty blob.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): třída `BlobClient` umožňuje manipulovat s objekty blob Azure Storage.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provést následující s klientskou knihovnou služby Azure Blob Storage pro Python:

* [Získání připojovacího řetězce](#get-the-connection-string)
* [Vytvoření kontejneru](#create-a-container)
* [Nahrání objektů blob do kontejneru](#upload-blobs-to-a-container)
* [Výpis objektů BLOB v kontejneru](#list-the-blobs-in-a-container)
* [Stáhnout objekty blob](#download-blobs)
* [Odstranění kontejneru](#delete-a-container)

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Následující kód načte připojovací řetězec pro účet úložiště z proměnné prostředí vytvořené v oddílu [konfigurace vašeho připojovacího řetězce úložiště](#configure-your-storage-connection-string) .

Přidejte tento kód do bloku `try`:

```python
# Retrieve the connection string for use with the application. The storage
# connection string is stored in an environment variable on the machine
# running the application called CONNECT_STR. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('CONNECT_STR')
```

### <a name="create-a-container"></a>Vytvoření kontejneru

Určete název nového kontejneru. Následující kód připojí hodnotu UUID k názvu kontejneru, aby bylo zajištěno, že je jedinečný.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Vytvořte instanci třídy [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) voláním metody [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) . Pak zavolejte metodu [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) , aby se kontejner ve vašem účtu úložiště skutečně vytvořil.

Přidejte tento kód na konec `try` bloku:

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>Nahrání objektů blob do kontejneru

Následující fragment kódu:

1. Vytvoří textový soubor v místním adresáři.
1. Získá odkaz na objekt [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) voláním metody [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) v [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) z oddílu [vytvoření kontejneru](#create-a-container) .
1. Nahraje místní textový soubor do objektu BLOB voláním metody [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) .

Přidejte tento kód na konec `try` bloku:

```python
# Create a file in local Documents directory to upload and download
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

Vypíše objekty BLOB v kontejneru voláním metody [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) . V tomto případě byl do kontejneru přidán pouze jeden objekt blob, takže operace výpisu vrátí pouze jeden objekt BLOB.

Přidejte tento kód na konec `try` bloku:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Stáhnout objekty blob

Stáhněte dřív vytvořený objekt BLOB voláním metody [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) . Vzorový kód přidá příponu "DOWNLOAD" do názvu souboru, aby bylo možné zobrazit oba soubory v místním systému souborů.

Přidejte tento kód na konec `try` bloku:

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in Documents
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>Odstranění kontejneru

Následující kód vyčistí prostředky, které aplikace vytvořila, odebráním celého kontejneru pomocí metody [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) . Pokud chcete, můžete také odstranit místní soubory.

Aplikace pozastaví vstup uživatele voláním `input()` předtím, než odstraní objekt blob, kontejner a místní soubory. Tato možnost je vhodná pro ověření, že prostředky byly ve skutečnosti vytvořeny správně, než byly odstraněny.

Přidejte tento kód na konec `try` bloku:

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

Tato aplikace vytvoří testovací soubor v místní složce a nahraje ho do úložiště objektů BLOB. Příklad zobrazí seznam objektů BLOB v kontejneru a stáhne soubor s novým názvem, abyste mohli porovnat staré a nové soubory.

Přejděte do adresáře, který obsahuje soubor *BLOB-Quickstart-V12.py* , a spusťte následující příkaz `python` pro spuštění aplikace.

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

Než zahájíte proces vyčištění, vyhledejte tyto dva soubory ve složce *dokumenty* . Můžete je otevřít a podívat se, že jsou identické.

Po ověření souborů stiskněte klávesu **ENTER** , aby se odstranily soubory testu, a dokončete ukázku.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nahrávat, stahovat a vypisovat objekty BLOB pomocí Pythonu.

Pokud chcete zobrazit ukázkové aplikace služby Blob Storage, pokračujte:

> [!div class="nextstepaction"]
> [Ukázky V12 v Pythonu pro Azure Blob Storage SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Další informace najdete v tématu [sada Azure SDK for Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/README.md).
* Kurzy, ukázky, rychlé starty a další dokumentace najdete na webu [Azure pro vývojáře v Pythonu](/azure/python/).
