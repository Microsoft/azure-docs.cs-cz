---
title: Rychlý start Azure – Vytvoření objektu blob v úložišti objektů pomocí Pythonu | Microsoft Docs
description: V tomto rychlém startu vytvoříte v úložišti objektů (blob) účet úložiště a kontejner. Pak použijete klientskou knihovnu pro úložiště pro Python k nahrání objektu blob do služby Azure Storage, stažení objektu blob a výpisu objektů blob v kontejneru.
services: storage
author: craigshoemaker
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: cshoe
ms.openlocfilehash: a1994f00715e8b3fb8a207de85ca5e2a1fdc3131
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399151"
---
# <a name="quickstart-upload-download-and-list-blobs-using-python"></a>Rychlý start: Nahrávání, stahování a výpis objektů blob pomocí Pythonu

V tomto rychlém startu zjistíte, jak pomocí Pythonu nahrávat, stahovat a vypisovat objekty blob bloku v kontejneru v úložišti objektů blob v Azure. 

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba: 
* Nainstalovat [Python](https://www.python.org/downloads/).
* Stáhnout a nainstalovat [sadu SDK služby Azure Storage pro Python](https://github.com/Azure/azure-sdk-for-python). 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace
[Ukázková aplikace](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) použitá v tomto rychlém startu je základní aplikace v Pythonu.  

Pomocí [gitu](https://git-scm.com/) stáhněte kopii aplikace do svého vývojového prostředí. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Tento příkaz naklonuje úložiště do vaší místní složky gitu. Pokud chcete otevřít program v Pythonu, vyhledejte složku storage-blobs-python-quickstart a soubor example.py.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště
V aplikaci je potřeba zadat název účtu úložiště a klíč úložiště pro vytvoření objektu `BlockBlobService`. V Průzkumníku řešení vašeho integrovaného vývojového prostředí (IDE) otevřete soubor `example.py`. Hodnoty **accountname** a **accountkey** nahraďte názvem a klíčem vašeho účtu. 

```python 
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
```

## <a name="run-the-sample"></a>Spuštění ukázky
Tato ukázka vytvoří testovací soubor ve složce Dokumenty. Ukázkový program nahraje testovací soubor do úložiště objektů blob, vypíše objekty blob v kontejneru a stáhne soubor s novým názvem. 

Nejprve nainstalujte závislosti spuštěním příkazu `pip install`:

    pip install azure-storage

Potom spusťte ukázku. Následující výstup je příkladem výstupu vráceného po spuštění aplikace:
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Když budete pokračovat stisknutím libovolné klávesy, ukázkový program odstraní kontejner úložiště i soubory. Než budete pokračovat, zkontrolujte, jestli složka Dokumenty obsahuje příslušné dva soubory. Můžete je otevřít a podívat se, že jsou identické.

K zobrazení souborů v úložišti objektů blob můžete použít také nástroj, jako je [Průzkumník služby Azure Storage](http://storageexplorer.com). Průzkumník služby Azure Storage je bezplatný nástroj pro více platforem, který umožňuje přístup k informacím o účtu úložiště. 

Po ověření souborů stiskněte libovolnou klávesu a dokončete ukázku a odstraňte testovací soubory. Když teď víte, co ukázka dělá, otevřete soubor example.py a prohlédněte si kód. 

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

Dále si projdeme vzorový kód, abyste pochopili, jak funguje.

### <a name="get-references-to-the-storage-objects"></a>Získání odkazů na objekty úložiště
První věc, kterou je potřeba udělat, je vytvořit odkazy na objekty sloužící k přístupu k úložišti objektů blob a jeho správě. Tyto objekty se vzájemně využívají a každý z nich je využívaný dalším objektem v seznamu.

* Vytvořte instanci objektu **BlockBlobService**, která odkazuje na službu Blob service ve vašem účtu úložiště. 

* Vytvořte instanci objektu **CloudBlobContainer**, která představuje kontejner, ke kterému přistupujete. Kontejnery slouží k uspořádání objektů blob podobně jako složky na počítači k uspořádání souborů.

Jakmile budete mít CloudBlobContainer, můžete vytvořit instanci objektu **CloudBlockBlob**, která odkazuje na konkrétní objekt blob, který vás zajímá, a provádět operace, jako jsou nahrávání, stahování a kopírování.

> [!IMPORTANT]
> Názvy kontejnerů musí obsahovat jen malá písmena. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

V této části vytvoříte instance objektů, vytvoříte nový kontejner a pak nastavíte oprávnění ke kontejneru tak, aby objekty blob byly veřejné. Kontejner má název **quickstartblobs**. 

```python 
# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
 
# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
block_blob_service.create_container(container_name) 

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(container_name, public_access=PublicAccess.Container)
```
### <a name="upload-blobs-to-the-container"></a>Nahrání objektů blob do kontejneru

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Nejčastěji používané jsou objekty blob bloku, které se používají také v tomto rychlém startu.  

Pokud chcete nahrát soubor do objektu blob, získejte úplnou cestu k souboru spojením názvu adresáře a názvu souboru na místním disku. Pak můžete soubor nahrát do zadané cesty pomocí metody **create\_blob\_from\_path**. 

Vzorový kód vytvoří místní soubor, který se použije k nahrání a stažení. Soubor k nahrání uloží do proměnné **full\_path\_to\_file** a název objektu blob do proměnné **local\_file\_name**. Následující příklad nahraje soubor do kontejneru **quickstartblobs**.

```python
# Create a file in Documents to test the upload and download.
local_path=os.path.expanduser("~\Documents")
local_file_name ="QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file =os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name
block_blob_service.create_blob_from_path(container_name, local_file_name, full_path_to_file)
```

V případě úložiště objektů blob můžete k nahrání použít několik metod. Například pokud máte paměťový proud, můžete místo **create\_blob\_from\_path** použít metodu **create\_blob\_from\_stream**. 

Objekty blob bloku můžou mít velikost až 4,7 TB a můžou být čímkoli od tabulky aplikace Excel po velké videosoubory. Objekty blob stránky se používají hlavně pro soubory VHD využívané virtuálními počítači IaaS. Doplňovací objekty blob se používají k protokolování, například když chcete zapisovat do souboru a pak přidávat další informace. Většina objektů blob ukládaných do úložiště jsou typu blok.

### <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Získejte seznam souborů v kontejneru pomocí metody **list_blobs**. Tato metoda vrací generátor. Následující kód načte seznam objektů blob, pak je ve smyčce projde a zobrazí názvy nalezených objektů blob v kontejneru.  

```python
# List the blobs in the container
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Stažení objektů blob

Stáhněte objekty blob na místní disk pomocí metody **get\_blob\_to\_path**. Následující kód stáhne objekt blob nahraný v předchozí části. K názvu objektu blob se přidá přípona „_DOWNLOADED“, takže na místním disku uvidíte oba soubory. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name ,'.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už nepotřebujete objekty blob nahrané v rámci tohoto rychlého startu, můžete celý kontejner odstranit pomocí metody **delete\_container**. Pokud už nepotřebujete vytvořené soubory, pomocí metody **delete\_blob** je odstraníte.

```python
# Clean up resources. This includes the container and the temp files
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```
## <a name="resources-for-developing-python-applications-with-blobs"></a>Zdroje informací pro vývoj aplikací v Pythonu s využitím objektů blob

Prohlédněte si tyto další zdroje informací o vývoji v Pythonu s využitím úložiště objektů blob:

### <a name="binaries-and-source-code"></a>Binární soubory a zdrojový kód

- Prohlédněte, stáhněte a nainstalujte si [zdrojový kód klientské knihovny pro Python](https://github.com/Azure/azure-storage-python) pro službu Azure Storage na GitHubu.

### <a name="client-library-reference-and-samples"></a>Klientská knihovna – referenční informace a ukázky

- Další informace o klientské knihovně pro Python najdete v [referenčních informacích k rozhraní Python API](https://docs.microsoft.com/python/api/overview/azure/storage).
- Prozkoumejte [ukázky pro úložiště objektů blob](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) napsané s využitím klientské knihovny pro Python.

## <a name="next-steps"></a>Další kroky
 
V tomto rychlém startu jste zjistili, jak přenášet soubory mezi místním diskem a úložištěm objektů blob v Azure pomocí Pythonu. Další informace o práci s úložištěm objektů blob najdete v postupech pro úložiště objektů blob.

> [!div class="nextstepaction"]
> [Operace s úložištěm objektů blob – postupy](./storage-python-how-to-use-blob-storage.md)
 
Další informace o Průzkumníku služby Storage a objektech blob najdete v tématu [Správa prostředků úložiště objektů blob v Azure pomocí Průzkumníka služby Storage](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
