---
title: 'Rychlý Start: Klientská knihovna služby Azure Blob Storage v 2.1 pro Python'
description: V tomto rychlém startu vytvoříte v úložišti objektů (blob) účet úložiště a kontejner. Pak použijete klientskou knihovnu pro úložiště v 2.1 pro Python k nahrání objektu BLOB pro Azure Storage, stažení objektu BLOB a výpisu objektů BLOB v kontejneru.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 34205caf03d2f2d7255f75ea6203c5572c4c429b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95523296"
---
# <a name="quickstart-manage-blobs-with-python-v21-sdk"></a>Rychlý Start: Správa objektů BLOB pomocí Pythonu v 2.1 SDK

V tomto rychlém startu se naučíte spravovat objekty BLOB pomocí Pythonu. Objekty blob jsou objekty, které mohou obsahovat velké objemy textových nebo binárních dat, včetně obrázků, dokumentů, datových proudů médií a dat archivu. Můžete nahrávat, stahovat a vypisovat objekty BLOB a vytvářet a odstraňovat kontejnery.

> [!NOTE]
> V tomto rychlém startu se používá starší verze klientské knihovny pro úložiště objektů BLOB v Azure. Pokud chcete začít používat nejnovější verzi, přečtěte si téma [rychlý Start: Správa objektů BLOB pomocí Python V12 SDK](storage-quickstart-blobs-python.md).

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Účet služby Azure Storage. [Vytvořte účet úložiště](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/).
- [Sada SDK Azure Storage pro Python](https://github.com/Azure/azure-sdk-for-python)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

[Ukázková aplikace](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) v tomto rychlém startu je základní aplikace v Pythonu.  

Pomocí následujícího příkazu [Git](https://git-scm.com/) Stáhněte aplikaci do vývojového prostředí. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Pokud chcete zkontrolovat program Pythonu, otevřete soubor *example.py* v kořenovém adresáři úložiště.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

V aplikaci zadejte název účtu úložiště a klíč účtu pro vytvoření objektu `BlockBlobService`.

1. V Průzkumníku řešení vašeho integrovaného vývojového prostředí (IDE) otevřete soubor *example.py*.

1. `accountname`Hodnoty a nahraďte `accountkey` názvem svého účtu úložiště a klíčem:

    ```python
    block_blob_service = BlockBlobService(
        account_name='accountname', account_key='accountkey')
    ```

1. Uložte soubor a zavřete ho.

## <a name="run-the-sample"></a>Spuštění ukázky

Vzorový program vytvoří testovací soubor ve složce *dokumenty* , nahraje soubor do úložiště objektů blob, vypíše objekty BLOB v souboru a stáhne soubor s novým názvem.

1. Nainstalujte závislosti:

    ```console
    pip install azure-storage-blob==2.1.0
    ```

1. Přejít na ukázkovou aplikaci:

    ```console
    cd storage-blobs-python-quickstart
    ```

1. Spusťte ukázku:

    ```console
    python example.py
    ```

    Zobrazí se zprávy podobné následujícímu výstupu:
  
    ```output
    Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    List blobs in the container
             Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Downloading blob to     C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
    ```

1. Než budete pokračovat, přejděte do složky *dokumenty* a vyhledejte dva soubory.

    * *QuickStart_\<universally-unique-identifier\>*
    * *QuickStart_ \<universally-unique-identifier\> _DOWNLOADED*

1. Můžete je otevřít a podívat se, že jsou stejné.

    Můžete použít také nástroj podobný [Průzkumník služby Azure Storage](https://storageexplorer.com). Je dobrý pro zobrazení souborů v úložišti objektů BLOB. Průzkumník služby Azure Storage je bezplatný nástroj pro více platforem, který umožňuje přístup k informacím o účtu úložiště. 

1. Po prohledání souborů stiskněte libovolnou klávesu a dokončete ukázku a odstraňte testovací soubory.

## <a name="learn-about-the-sample-code"></a>Přečtěte si o vzorovém kódu

Když teď víte, co ukázka dělá, otevřete soubor *example.py* a Prohlédněte si kód.

### <a name="get-references-to-the-storage-objects"></a>Získání odkazů na objekty úložiště

V této části vytvoříte instance objektů, vytvoříte nový kontejner a pak nastavíte oprávnění ke kontejneru tak, aby objekty blob byly veřejné. Zavoláte kontejner `quickstartblobs` . 

```python
# Create the BlockBlockService that the system uses to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```

Nejprve vytvoříte odkazy na objekty sloužící k přístupu k úložišti objektů blob a jeho správě. Tyto objekty se vzájemně využívají a každý z nich je využívaný dalším objektem v seznamu.

* Vytvořte instanci objektu **BlockBlobService**, která odkazuje na službu Blob service ve vašem účtu úložiště. 

* Vytvořte instanci objektu **CloudBlobContainer**, která představuje kontejner, ke kterému přistupujete. Systém používá kontejnery k uspořádání objektů blob, jako jsou složky v počítači, které slouží k uspořádání souborů.

Jakmile budete mít CloudBlobContainer, vytvořte instanci objektu **CloudBlockBlob**, která odkazuje na konkrétní objekt blob, který vás zajímá. Tento objekt blob pak můžete podle potřeby nahrát, stáhnout nebo zkopírovat.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o názvech kontejnerů a objektů BLOB najdete v tématu [pojmenování a odkazování kontejnerů, objektů BLOB a metadat](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="upload-blobs-to-the-container"></a>Nahrání objektů blob do kontejneru

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Objekty blob bloku můžou mít velikost až 4,7 TB a můžou být čímkoli od tabulky aplikace Excel po velké videosoubory. Pokud chcete zapisovat do souboru a pak dál přidávat další informace, můžete použít doplňovací objekty blob k protokolování. Objekty blob stránky se primárně používají pro soubory virtuálního pevného disku (VHD), které jsou zpětnou infrastrukturou jako virtuální počítače služby (IaaS virtuální počítače). Objekty blob bloku se používají nejčastěji. Tento rychlý Start používá objekty blob bloku.

Pokud chcete nahrát soubor do objektu blob, získejte úplnou cestu k souboru spojením názvu adresáře a názvu souboru na místním disku. Pak můžete soubor nahrát do zadané cesty pomocí metody `create_blob_from_path`. 

Vzorový kód vytvoří místní soubor, který systém používá k nahrání a stažení, a uloží soubor, který systém nahraje jako *full_path_to_file* a název objektu blob jako *local_file_name*. Tento příklad nahraje soubor do vašeho kontejneru s názvem `quickstartblobs` :

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(
    container_name, local_file_name, full_path_to_file)
```

V případě úložiště objektů blob můžete k nahrání použít několik metod. Například pokud máte paměťový proud, můžete místo metody `create_blob_from_path` použít metodu `create_blob_from_stream`. 

### <a name="list-the-blobs-in-a-container"></a>Seznam objektů blob v kontejneru

Následující kód vytvoří `generator` pro `list_blobs` metodu. Kód projde seznam objektů BLOB v kontejneru a vytiskne jejich názvy do konzoly.

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Stažení objektů blob


Stáhněte objekty blob na místní disk pomocí `get_blob_to_path` metody.
Následující kód stáhne objekt blob, který jste nahráli dříve. Systém připojí *_DOWNLOADED* k názvu objektu blob, takže na místním disku uvidíte oba soubory.

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, local_file_name.replace(
   '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už nepotřebujete objekty blob nahrané v rámci tohoto rychlého startu, můžete celý kontejner odstranit pomocí metody `delete_container`. Pokud místo toho chcete odstranit jednotlivé soubory, použijte metodu `delete_blob`.

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="resources-for-developing-python-applications-with-blobs"></a>Zdroje informací pro vývoj aplikací v Pythonu s využitím objektů blob

Další informace o vývoji Pythonu s využitím služby Blob Storage najdete v těchto dalších zdrojích informací:

### <a name="binaries-and-source-code"></a>Binární soubory a zdrojový kód

- Prohlédněte, stáhněte a nainstalujte si [zdrojový kód klientské knihovny pro Python](https://github.com/Azure/azure-storage-python) pro službu Azure Storage na GitHubu.

### <a name="client-library-reference-and-samples"></a>Klientská knihovna – referenční informace a ukázky

- Další informace o klientské knihovně Pythonu najdete v tématu [knihovny Azure Storage pro Python](/python/api/overview/azure/storage).
- Prozkoumejte [ukázky pro úložiště objektů blob](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) napsané s využitím klientské knihovny pro Python.

## <a name="next-steps"></a>Další kroky
 
V tomto rychlém startu jste zjistili, jak přenášet soubory mezi místním diskem a úložištěm objektů blob v Azure pomocí Pythonu. 

Další informace o Průzkumník služby Storage a objektů BLOB najdete v tématu [Správa prostředků Azure Blob Storage pomocí Průzkumník služby Storage](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).