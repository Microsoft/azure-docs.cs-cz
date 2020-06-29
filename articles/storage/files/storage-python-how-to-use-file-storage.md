---
title: Vývoj pro soubory Azure pomocí Pythonu | Microsoft Docs
description: Naučte se vyvíjet aplikace a služby v Pythonu, které používají soubory Azure k ukládání dat souborů.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.custom: tracking-python
ms.openlocfilehash: c612ddc324144a818dd738b440660d473ec3a1bd
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2020
ms.locfileid: "85509638"
---
# <a name="develop-for-azure-files-with-python"></a>Vývoj pro Soubory Azure pomocí Pythonu
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

V tomto kurzu se dozvíte základy použití Pythonu k vývoji aplikací nebo služeb, které používají soubory Azure k ukládání dat souborů. V tomto kurzu vytvoříme jednoduchou konzolovou aplikaci a ukážeme, jak provádět základní akce s Pythonem a soubory Azure:

* Vytvoření sdílených složek Azure
* Vytváření adresářů
* Zobrazení výčtu souborů a adresářů ve sdílené složce Azure
* Nahrání, stažení a odstranění souboru

> [!Note]  
> Vzhledem k tomu, že je možné získat přístup k souborům Azure přes protokol SMB, je možné psát jednoduché aplikace, které přistupují ke sdílené složce Azure pomocí standardních tříd a funkcí Pythonu v/v. Tento článek popisuje, jak psát aplikace, které používají sadu SDK Azure Storage Python, která používá [REST API souborů Azure](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) ke komunikaci se soubory Azure.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Stažení a instalace sady Azure Storage SDK pro Python

[Sada SDK Azure Storage pro Python](https://github.com/azure/azure-storage-python) vyžaduje Python 2,7, 3,3, 3,4, 3,5 nebo 3,6.
 
## <a name="install-via-pypi"></a>Instalace prostřednictvím PyPi

K instalaci prostřednictvím indexu balíčku Pythonu (PyPI) zadejte:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Pokud provádíte upgrade ze sady Azure Storage SDK pro Python verze 0,36 nebo starší, odinstalujte starší sadu SDK pomocí nástroje `pip uninstall azure-storage` před instalací nejnovějšího balíčku.

Alternativní metody instalace najdete [v sadě Azure Storage SDK for Python na GitHubu](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Zobrazit ukázkovou aplikaci
Pokud chcete zobrazit a spustit ukázkovou aplikaci, která ukazuje, jak používat Python se soubory Azure, přečtěte si téma [Azure Storage: Začínáme se soubory Azure v Pythonu](https://github.com/Azure-Samples/storage-file-python-getting-started). 

Chcete-li spustit ukázkovou aplikaci, ujistěte se, že jste `azure-storage-file` nainstalovali `azure-storage-common` balíčky a.

## <a name="set-up-your-application-to-use-azure-files"></a>Nastavení aplikace pro používání souborů Azure
V horní části libovolného zdrojového souboru Pythonu, ve kterém chcete programově přistupovat k Azure Storage, přidejte následující.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Nastavení připojení k souborům Azure 
`FileService`Objekt vám umožní pracovat se sdílenými složkami, adresáři a soubory. Následující kód vytvoří `FileService` objekt pomocí názvu účtu úložiště a klíče účtu. Nahraďte `<myaccount>` a `<mykey>` názvem a klíčem vašeho účtu.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
V následujícím příkladu kódu můžete `FileService` vytvořit sdílenou složku pomocí objektu, pokud neexistuje.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Vytvoření adresáře
Úložiště můžete také uspořádat tak, že umístíte soubory do podadresářů, aniž byste je museli umístit do kořenového adresáře. Soubory Azure vám umožní vytvořit tolik adresářů, kolik bude váš účet umožňovat. Následující kód vytvoří podadresář s názvem **sampledir** v kořenovém adresáři.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Zobrazení výčtu souborů a adresářů ve sdílené složce Azure
Chcete-li zobrazit seznam souborů a adresářů ve sdílené složce, použijte metodu **Výpis \_ adresářů \_ a \_ souborů** . Tato metoda vrací generátor. Následující kód vypíše **název** každého souboru a adresáře ve sdílené složce do konzoly.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Nahrání souboru 
Sdílená složka Azure obsahuje minimálně kořenový adresář, kde se můžou nacházet soubory. V této části se dozvíte, jak nahrát soubor z místního úložiště do kořenového adresáře sdílené složky.

Chcete-li vytvořit soubor a odeslat data, použijte `create_file_from_path` `create_file_from_stream` metody, `create_file_from_bytes` nebo `create_file_from_text` . Jsou to metody vysoké úrovně, které provádějí nezbytné datové bloky, pokud velikost dat překročí 64 MB.

`create_file_from_path`nahraje obsah souboru ze zadané cesty a `create_file_from_stream` nahraje obsah z již otevřeného souboru nebo datového proudu. `create_file_from_bytes`nahraje pole bajtů a `create_file_from_text` nahraje zadanou textovou hodnotu pomocí zadaného kódování (standardně UTF-8).

Následující příklad nahraje obsah **sunset.png** souboru do souboru **MyFile** .

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>Stažení souboru
Chcete-li stáhnout data ze souboru, použijte `get_file_to_path` ,, `get_file_to_stream` `get_file_to_bytes` nebo `get_file_to_text` . Jsou to metody vysoké úrovně, které provádějí nezbytné datové bloky, pokud velikost dat překročí 64 MB.

Následující příklad ukazuje použití `get_file_to_path` ke stažení obsahu souboru **MyFile** a jeho uložení do souboru **out-sunset.png** .

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Odstranění souboru
Nakonec pro odstranění souboru volejte `delete_file` .

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Vytvořit snímek sdílené složky
Můžete vytvořit bod v čase kopie celé sdílené složky.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Vytvoření snímku sdílené složky s metadaty**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Vypsat sdílené složky a snímky 
Můžete vypsat všechny snímky pro konkrétní sdílenou složku.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Procházet snímek sdílené složky
Můžete procházet obsah každého snímku sdílené složky a načíst soubory a adresáře z daného bodu v čase.

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Získat soubor ze snímku sdílené složky
Soubor můžete stáhnout ze snímku sdílené složky pro váš scénář obnovení.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Odstranit jeden snímek sdílené složky  
Můžete odstranit jeden snímek sdílené složky.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Odstranit sdílenou složku, když existují snímky sdílené složky
Sdílenou složku obsahující snímky nelze odstranit, pokud nebyly nejprve odstraněny všechny snímky.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili, jak manipulovat se soubory Azure pomocí Pythonu, získáte další informace pomocí těchto odkazů.

* [Středisko pro vývojáře programující v Pythonu](https://azure.microsoft.com/develop/python/)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)
* [Sada SDK Microsoft Azure Storage pro Python](https://github.com/Azure/azure-storage-python)
