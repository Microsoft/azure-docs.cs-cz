---
title: Vývoj pro soubory Azure pomocí Pythonu | Dokumentace Microsoftu
description: Informace o vývoji aplikací v Pythonu a služby, které používají soubory Azure k ukládání dat souborů.
services: storage
author: wmgries
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 09/19/2017
ms.author: tamram
ms.component: files
ms.openlocfilehash: a376e4f5d8d74731f30da47a696656e79ae55ac7
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245677"
---
# <a name="develop-for-azure-files-with-python"></a>Vývoj pro soubory Azure pomocí Pythonu
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Tento kurz vám ukáže základy používání Python k vývoji aplikací a služeb, které používají soubory Azure k ukládání dat souborů. V tomto kurzu jsme se vytvořit jednoduchou konzolovou aplikaci a ukazují, jak provádět základní akce s Python a službou soubory Azure:

* Vytvoření sdílené složky Azure
* Vytvoření adresáře
* Zobrazení výčtu soubory a adresáře ve sdílené složky Azure
* Nahrání, stažení a odstranění souboru

> [!Note]  
> Protože soubory Azure přístupná přes protokol SMB, je možné psát jednoduché aplikace, které přistupují k sdílenou složku Azure pomocí standardních vstupně-výstupních operací Python třídy a funkce. Tento článek popisuje, jak psát aplikace, které používají Azure Python SDK úložiště, které používá [REST API služby soubory Azure](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) ke komunikaci s Azure Files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Stáhněte a nainstalujte sadu SDK služby Azure Storage pro Python

Azure Storage SDK pro Python vyžaduje Python 2.7, 3.3, 3.4, 3.5 a 3.6 a je k dispozici ve 4 různých balíčcích: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` a `azure-storage-queue`. V tomto kurzu budeme používat `azure-storage-file` balíčku.
 
## <a name="install-via-pypi"></a>Instalace přes PyPi

Pokud chcete nainstalovat prostřednictvím indexu balíčků Pythonu (PyPI), zadejte:

```bash
pip install azure-storage-file
```


> [!NOTE]
> Pokud provádíte upgrade z Azure Storage SDK pro Python verze 0.36 nebo starší, budete nejdřív muset, odinstalujte ji pomocí `pip uninstall azure-storage` jako už vydáváme Storage SDK pro Python v jediném balíčku.
> 
> 

Metody alternativní instalace, najdete [Azure Storage SDK pro Python na Githubu](https://github.com/Azure/azure-storage-python/).

## <a name="set-up-your-application-to-use-azure-files"></a>Nastavení aplikace používat soubory Azure
Přidejte následující v horní části libovolného Python zdrojového souboru, ve kterém chcete programovému přístupu ke službě Azure Storage.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Nastavit připojení k Azure Files 
`FileService` Objekt vám umožní pracovat s sdílených složek, adresářů a souborů. Následující kód vytvoří `FileService` pomocí úložiště účtu název a klíč účtu. Nahraďte `<myaccount>` a `<mykey>` názvem a klíčem vašeho účtu.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
V následujícím příkladu kódu, můžete použít `FileService` objektu, který chcete vytvořit sdílenou složku, pokud neexistuje.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Vytvoření adresáře
Úložiště můžete také uspořádat tak, že vložíte souborů v podadresářích namísto toho, aby všechny z nich v kořenovém adresáři. Služba soubory Azure umožňuje vytvářet tolik adresáře, které umožní váš účet. Následující kód vytvoří podadresář s názvem **sampledir** pod kořenovým adresářem.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Zobrazení výčtu soubory a adresáře ve sdílené složky Azure
K zobrazení seznamu soubory a adresáře ve sdílené složce, použijte **seznamu\_adresáře\_a\_soubory** metody. Tato metoda vrací generátor. Následující kód výstupů **název** každého souboru a adresáře ve sdílené složce, do konzoly.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Nahrání souboru 
Sdílené složky Azure obsahuje přinejmenším, kořenový adresář, kde mohou být soubory umístěny. V této části se dozvíte, jak nahrát soubor z místního úložiště na kořenovém adresáři sdílené složky.

Chcete-li vytvořit soubor a nahrát data, použijte `create_file_from_path`, `create_file_from_stream`, `create_file_from_bytes` nebo `create_file_from_text` metody. Jsou to základní metody, které provádějí nezbytné bloků, když velikost dat je větší než 64 MB.

`create_file_from_path` Odešle obsah souboru v zadané cestě, a `create_file_from_stream` odešle obsah z již otevřeného souboru/stream. `create_file_from_bytes` pole bajtů, nahraje a `create_file_from_text` odešle zadanou textovou hodnotu pomocí zadaného kódování (výchozí nastavení: UTF-8).

Následující příklad nahraje obsah **sunset.png** soubor do **myfile** souboru.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None, # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>Stažení souboru
Chcete-li stáhnout data ze souboru, použijte `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes`, nebo `get_file_to_text`. Jsou to základní metody, které provádějí nezbytné bloků, když velikost dat je větší než 64 MB.

Následující příklad ukazuje použití `get_file_to_path` stáhnout obsah **myfile** soubor a uložte ho do **mimo sunset.png** souboru.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Odstranění souboru
Nakonec odstranění souboru, zavolejte `delete_file`.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot-preview"></a>Vytvořit snímek sdílené složky (preview)
Vytvořit bod v době provedená kopie celou sdílenou.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Vytvoření snímku sdílené složky s metadaty**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Zobrazit seznam sdílených složek a snímků 
Můžete vytvořit seznam všechny snímky pro konkrétní sdílenou složku.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Procházet snímku sdílené složky
Můžete procházet obsah jednotlivých snímků sdílené složky k načtení souborů a adresářů od tohoto okamžiku v čase.

```python
directories_and_files = list(file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Získání souboru ze snímku sdílené složky
Stáhnout soubor ze snímku sdílené složky pro váš scénář obnovení.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Odstranění snímků jedné sdílené složky  
Snímek jedné sdílené složky můžete odstranit.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Odstranit sdílenou složku, pokud existují snímky sdílené složky
Sdílenou složku, která obsahuje snímky nejde odstranit, dokud se neodstraní všechny snímky.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Další postup
Teď, když jsme zjistili, jak pracovat se soubory Azure pomocí Pythonu, použijte tyto odkazy na další informace.

* [Středisko pro vývojáře programující v Pythonu](https://azure.microsoft.com/develop/python/)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK pro Python](https://github.com/Azure/azure-storage-python)