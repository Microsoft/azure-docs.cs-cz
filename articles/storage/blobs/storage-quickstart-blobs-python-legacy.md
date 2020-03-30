---
title: 'Úvodní příručka: Klientská knihovna úložiště objektů blob Azure v2.1 pro Python'
description: V tomto rychlém startu vytvoříte v úložišti objektů (blob) účet úložiště a kontejner. Pak použijete knihovnu klienta úložiště v2.1 pro Python k nahrání objektu blob do Azure Storage, stažení objektu blob a seznam objektů BLOB v kontejneru.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: seo-python-october2019
ms.openlocfilehash: 4b0248604b6e9189d5275177a4960e4c352e8215
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76906440"
---
# <a name="quickstart-manage-blobs-with-python-v21-sdk"></a>Úvodní příručka: Správa objektů BLOB pomocí pythonu v2.1 SDK

V tomto rychlém startu se naučíte spravovat objekty BLOB pomocí Pythonu. Objekty BLOB jsou objekty, které mohou obsahovat velké množství textových nebo binárních dat, včetně obrázků, dokumentů, datových proudů médií a archivních dat. Nahrajete, stáhnete a zobrazíte objekty BLOB a vytvoříte a odstraníte kontejnery.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Účet služby Azure Storage. [Vytvořte účet úložiště](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/).
- [Azure Storage SDK pro Python](https://github.com/Azure/azure-sdk-for-python).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

[Ukázková aplikace](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) v tomto rychlém startu je základní aplikace v Pythonu.  

Pomocí následujícího příkazu [git](https://git-scm.com/) stáhněte aplikaci do vývojového prostředí. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Chcete-li zkontrolovat program Pythonu, otevřete *soubor example.py* v kořenovém adresáři úložiště.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

V aplikaci zadejte název účtu úložiště a klíč účtu pro vytvoření objektu `BlockBlobService`.

1. V Průzkumníku řešení vašeho integrovaného vývojového prostředí (IDE) otevřete soubor *example.py*.

1. `accountname` Nahraďte `accountkey` hodnoty a názvem a klíčem účtu úložiště:

    ```python
    block_blob_service = BlockBlobService(
        account_name='accountname', account_key='accountkey')
    ```

1. Uložte soubor a zavřete ho.

## <a name="run-the-sample"></a>Spuštění ukázky

Ukázkový program vytvoří testovací soubor ve složce *Dokumenty,* nahraje soubor do úložiště objektů Blob, zobrazí seznam objektů BLOB v souboru a stáhne soubor s novým názvem.

1. Nainstalujte závislosti:

    ```console
    pip install azure-storage-blob==2.1.0
    ```

1. Přejděte do ukázkové aplikace:

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

1. Než budete pokračovat, přejděte do složky *Dokumenty* a zkontrolujte, zda tyto dva soubory nenajdete.

    * *QuickStart_\<univerzálně jedinečný identifikátor\>*
    * *QuickStart_\<_DOWNLOADED s univerzálně jedinečným identifikátorem\>*

1. Můžete je otevřít a podívat se, že jsou stejné.

    Můžete také použít nástroj, jako je [Průzkumník úložiště Azure](https://storageexplorer.com). Je to dobré pro prohlížení souborů v úložišti objektů Blob. Azure Storage Explorer je bezplatný nástroj pro různé platformy, který vám umožní přístup k informacím o vašem účtu úložiště. 

1. Po prohlížení na soubory dokončete ukázku stisknutím libovolné klávesy a odstraňte testovací soubory.

## <a name="learn-about-the-sample-code"></a>Informace o ukázkovém kódu

Nyní, když víte, co ukázka dělá, otevřete *soubor example.py* a podívejte se na kód.

### <a name="get-references-to-the-storage-objects"></a>Získání odkazů na objekty úložiště

V této části vytvoříte instance objektů, vytvoříte nový kontejner a pak nastavíte oprávnění ke kontejneru tak, aby objekty blob byly veřejné. Zavoláte do kontejneru. `quickstartblobs` 

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

* Vytvořte instanci objektu **CloudBlobContainer**, která představuje kontejner, ke kterému přistupujete. Systém používá kontejnery k uspořádání objektů BLOB, jako používáte složky v počítači k uspořádání souborů.

Jakmile budete mít CloudBlobContainer, vytvořte instanci objektu **CloudBlockBlob**, která odkazuje na konkrétní objekt blob, který vás zajímá. Tento objekt blob pak můžete podle potřeby nahrát, stáhnout nebo zkopírovat.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o názvech kontejnerů a objektů blob naleznete v [tématu Pojmenování a odkazování kontejnerů, objektů Blob a metadat .](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)

### <a name="upload-blobs-to-the-container"></a>Nahrání objektů blob do kontejneru

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Objekty blob bloku můžou mít velikost až 4,7 TB a můžou být čímkoli od tabulky aplikace Excel po velké videosoubory. Při psaní dat můžete použít přidávací objekty BLOB, když chcete zapisovat do souboru, a pak pokračovat v přidávání dalších informací. Objekty BLOB stránky se používají především pro soubory virtuálního pevného disku (VHD), které zpět infrastrukturu jako virtuální počítače služby (Virtuální počítače IaaS). Objekty blob bloku se používají nejčastěji. Tento rychlý start používá objekty BLOB bloku.

Pokud chcete nahrát soubor do objektu blob, získejte úplnou cestu k souboru spojením názvu adresáře a názvu souboru na místním disku. Pak můžete soubor nahrát do zadané cesty pomocí metody `create_blob_from_path`. 

Ukázkový kód vytvoří místní soubor, který systém používá pro nahrávání a stahování, a ukládá soubor, který systém nahraje jako *full_path_to_file* a název objektu blob jako *local_file_name*. Tento příklad nahraje soubor do `quickstartblobs`kontejneru s názvem :

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

### <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Následující kód vytvoří `generator` for `list_blobs` the metoda. Kód prochází seznam objektů BLOB v kontejneru a vytiskne jejich názvy do konzoly.

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Stažení objektů blob


Pomocí metody stáhněte objekty `get_blob_to_path` BLOB na místní disk.
Následující kód stáhne objekt blob, který jste nahráli dříve. Systém připojí *_DOWNLOADED* k názvu objektu blob, takže můžete vidět oba soubory na místním disku.

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

Další informace o vývoji Pythonu s úložištěm objektů Blob najdete v těchto dalších zdrojích:

### <a name="binaries-and-source-code"></a>Binární soubory a zdrojový kód

- Prohlédněte, stáhněte a nainstalujte si [zdrojový kód klientské knihovny pro Python](https://github.com/Azure/azure-storage-python) pro službu Azure Storage na GitHubu.

### <a name="client-library-reference-and-samples"></a>Klientská knihovna – referenční informace a ukázky

- Další informace o klientské knihovně Pythonu najdete v [knihovnách Azure Storage pro Python](https://docs.microsoft.com/python/api/overview/azure/storage).
- Prozkoumejte [ukázky pro úložiště objektů blob](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) napsané s využitím klientské knihovny pro Python.

## <a name="next-steps"></a>Další kroky
 
V tomto rychlém startu jste zjistili, jak přenášet soubory mezi místním diskem a úložištěm objektů blob v Azure pomocí Pythonu. 

Další informace o Průzkumníku úložiště a objektech BLOB najdete v [tématu Správa prostředků úložiště objektů blob Azure pomocí Průzkumníka úložiště](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
