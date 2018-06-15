---
title: Vývoj pro Azure soubory s Pythonem | Microsoft Docs
description: Další informace jak vyvíjet aplikace Python a služby, které používají Azure souborů k ukládání dat souborů.
services: storage
documentationcenter: python
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/19/2017
ms.author: tamram
ms.openlocfilehash: 1102fd516b5497b4c482986b64fa7c96e9ccc54a
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738257"
---
# <a name="develop-for-azure-files-with-python"></a>Vývoj pro Azure soubory s Pythonem
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

V tomto kurzu se ukazují základy používání Python k vývoji aplikací nebo služeb, které používají Azure soubory k ukládání dat souborů. V tomto kurzu jsme vytvořit jednoduché konzolové aplikace a ukazují, jak provést základní operace s Python a Azure souborů:

* Vytvoření sdílené složky Azure
* Vytváření adresářů
* Vytvoření výčtu souborů a adresářů v sdílenou složku Azure
* Odesílání, stahování a odstranění souboru

> [!Note]  
> Protože soubory Azure může mít přístup přes protokol SMB, je možné psát jednoduché aplikace, které přístup k Azure souborové složce přes standardní Python vstupně-výstupních operací třídy a funkce. Tento článek popisuje, jak k psaní aplikací, které používají Azure Python SDK úložiště, který používá [REST API služby Azure soubory](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) ke komunikaci s Azure Files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Stáhněte a nainstalujte úložiště Azure SDK pro Python

Azure SDK úložiště pro Python vyžaduje Python 2.7 3.3, 3.4, 3.5 nebo 3.6 a je rozdělena na 4 různých balíčcích: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` a `azure-storage-queue`. V tomto kurzu budeme používat `azure-storage-file` balíčku.
 
## <a name="install-via-pypi"></a>Nainstalovat prostřednictvím úložiště PyPi

Chcete-li nainstalovat prostřednictvím indexu balíčků Pythonu (úložiště PyPI), zadejte:

```bash
pip install azure-storage-file
```


> [!NOTE]
> Pokud upgradujete ze sady SDK úložiště Azure pro jazyk Python 0.36 nebo starší verze, bude nejprve musíte odinstalovat pomocí `pip uninstall azure-storage` jako už sady SDK úložiště pro jazyk Python v vydáváme jeden balíček.
> 
> 

Metody alternativní instalace, najdete v článku [sada SDK úložiště Azure pro jazyk Python na Githubu](https://github.com/Azure/azure-storage-python/).

## <a name="set-up-your-application-to-use-azure-files"></a>Nastavení aplikace pomocí Azure Files
Přidejte následující v horní části všech Python zdrojový soubor, ve kterém chcete k programovému přístupu ke službě Azure Storage.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Nastavit připojení k Azure Files 
`FileService` Objekt vám umožňuje spolupracovat s sdílených složek, adresářů a souborů. Následující kód vytvoří `FileService` pomocí klíč účet a název účtu úložiště. Nahraďte `<myaccount>` a `<mykey>` s názvem účtu a klíč.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
V následujícím příkladu kódu, můžete použít `FileService` objekt, který chcete vytvořit sdílenou složku, pokud neexistuje.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Vytvoření adresáře
Úložiště můžete navíc uspořádat umístěním souborů v podadresářích místo nutnosti všechny z nich v kořenovém adresáři. Soubory Azure můžete vytvořit tolik adresáře, které umožní váš účet. Následující kód vytvoří adresář s názvem **sampledir** pod kořenovým adresářem.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Vytvoření výčtu souborů a adresářů v sdílenou složku Azure
K zobrazení seznamu souborů a adresářů ve sdílené složce, použijte **seznamu\_adresáře\_a\_soubory** metoda. Tato metoda vrací generátor. Následující kód výstupy **název** každého souboru a adresáře ve sdílené složce, do konzoly.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Nahrání souboru 
Sdílenou složku Azure obsahuje v každém, kořenový adresář, kde mohou být uloženy soubory. V této části se dozvíte jak nahrát soubor z místního úložiště do kořenového adresáře sdílenou složku.

Chcete-li vytvořit soubor a odesílat data, použijte `create_file_from_path`, `create_file_from_stream`, `create_file_from_bytes` nebo `create_file_from_text` metody. Jsou nejdůležitější metody, které provádějí potřebné rozdělování, když velikost dat přesáhne 64 MB.

`create_file_from_path` Odešle obsah souboru ze zadané cesty a `create_file_from_stream` odešle obsah z již otevřeného souboru/stream. `create_file_from_bytes` odešle pole bajtů, a `create_file_from_text` odešle zadanou textovou hodnotu pomocí zadaného kódování (výchozí hodnota je UTF-8).

V následujícím příkladu se uloží obsah **sunset.png** soubor do **myfile** souboru.

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
Chcete-li stáhnout data ze souboru, použijte `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes`, nebo `get_file_to_text`. Jsou nejdůležitější metody, které provádějí potřebné rozdělování, když velikost dat přesáhne 64 MB.

Následující příklad ukazuje, jak pomocí `get_file_to_path` stáhnout obsah **myfile** souboru a uložte ho do **na více systémů sunset.png** souboru.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Odstranění souboru
Nakonec odstranění souboru, zavolejte `delete_file`.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot-preview"></a>Vytvořte sdílenou složku snímku (preview)
Můžete vytvořit bod v době provedená kopie sdílené složky celý soubor.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Vytvoření sdílené složky snímku s metadaty**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Zobrazit seznam sdílených složek a snímky 
Můžete vytvořit seznam všechny snímky pro konkrétní sdílenou složku.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Procházet sdílenou složku snímku
Můžete procházet obsah jednotlivých sdílené složky snímků pro načtení souborů a adresářů z tohoto bodu v čase.

```python
directories_and_files = list(file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Získání souborů ze sdílené složky snímku
Soubor můžete stáhnout ze snímku sdílené složky pro váš scénář obnovení.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Odstranit snímek jednu sdílenou složku  
Odstraněním snímku jednu sdílenou složku.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Odstranit sdílenou složku, pokud existují snímky sdílené složky
Sdílené složky, která obsahuje snímky nelze odstranit, pokud jsou jako první smazány všechny snímky.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili jak pracovat se soubory Azure s Python, postupujte podle následujících odkazech na další informace.

* [Středisko pro vývojáře programující v Pythonu](/develop/python/)
* [REST API služby Azure Storage](http://msdn.microsoft.com/library/azure/dd179355)
* [Úložiště Microsoft Azure SDK pro jazyk Python](https://github.com/Azure/azure-storage-python)