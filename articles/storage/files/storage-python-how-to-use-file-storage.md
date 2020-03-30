---
title: Vývoj pro soubory Azure s Pythonem | Dokumenty společnosti Microsoft
description: Zjistěte, jak vyvíjet aplikace a služby Pythonu, které používají soubory Azure k ukládání dat souborů.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 139e3009722761172b7bbd57805a7f5b07e55fc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699384"
---
# <a name="develop-for-azure-files-with-python"></a>Vývoj pro Soubory Azure pomocí Pythonu
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Tento kurz předvede základy používání Pythonu k vývoji aplikací nebo služeb, které používají soubory Azure k ukládání dat souborů. V tomto kurzu vytvoříme jednoduchou konzolovou aplikaci a ukážeme, jak provádět základní akce s Pythonem a soubory Azure:

* Vytvoření sdílených složek Azure
* Vytvoření adresářů
* Vytvoření výčtu souborů a adresářů ve sdílené složce Azure
* Nahrání, stažení a odstranění souboru

> [!Note]  
> Vzhledem k tomu, že k souborům Azure lze přistupovat přes SMB, je možné zapisovat jednoduché aplikace, které přistupují ke sdílené složce Azure pomocí standardních tříd a funkcí vstupně-v. pythonu. Tento článek popisuje, jak psát aplikace, které používají Azure Storage Python SDK, který používá [rozhraní API Azure Files REST](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) pro mluvení do souborů Azure.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Stažení a instalace sady Azure Storage SDK pro Python

Sada [Azure Storage SDK pro Python](https://github.com/azure/azure-storage-python) vyžaduje Python 2.7, 3.3, 3.4, 3.5 nebo 3.6.
 
## <a name="install-via-pypi"></a>Instalace přes PyPi

Chcete-li nainstalovat prostřednictvím indexu balíčků Pythonu (PyPI), zadejte:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Pokud upgradujete z sady Azure Storage SDK pro Python verze 0.36 `pip uninstall azure-storage` nebo starší, odinstalujte starší sdk pomocí před instalací nejnovějšího balíčku.

Alternativní metody instalace najdete na webu [Azure Storage SDK pro Python na GitHubu](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Zobrazit ukázkovou aplikaci
f Pokud chcete zobrazit a spustit ukázkovou aplikaci, která ukazuje, jak používat Python se soubory Azure, přečtěte si další informace [o azure storage: Začínáme se soubory Azure v Pythonu](https://github.com/Azure-Samples/storage-file-python-getting-started). 

Chcete-li spustit ukázkovou aplikaci, `azure-storage-file` ujistěte se, že jste nainstalovali oba balíčky a. `azure-storage-common`

## <a name="set-up-your-application-to-use-azure-files"></a>Nastavení aplikace pro používání souborů Azure
Přidejte následující v horní části libovolného zdrojového souboru Pythonu, ve kterém chcete programově přistupovat k Úložišti Azure.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Nastavení připojení k souborům Azure 
Objekt `FileService` umožňuje pracovat se sdílenými složkami, adresáři a soubory. Následující kód vytvoří `FileService` objekt pomocí názvu účtu úložiště a klíče účtu. Nahraďte `<myaccount>` a `<mykey>` názvem a klíčem vašeho účtu.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
V následujícím příkladu kódu můžete `FileService` použít objekt k vytvoření sdílené složky, pokud neexistuje.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Vytvoření adresáře
Úložiště můžete také uspořádat umístěním souborů do podadresářů namísto toho, abyste je měli všechny v kořenovém adresáři. Azure Files umožňuje vytvořit tolik adresářů, kolik váš účet povolí. Níže uvedený kód vytvoří podadresář s názvem **sampledir** pod kořenovým adresářem.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Vytvoření výčtu souborů a adresářů ve sdílené složce Azure
Chcete-li vypsat soubory a adresáře ve sdílené složce, použijte metodu **seznamů\_adresářů\_a\_souborů.** Tato metoda vrací generátor. Následující kód výstupy **název** každého souboru a adresáře ve sdílené složce do konzoly.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Nahrání souboru 
Azure sdílení souborů obsahuje přinejmenším kořenový adresář, kde mohou být umístěny soubory. V této části se dozvíte, jak nahrát soubor z místního úložiště do kořenového adresáře sdílené složky.

Chcete-li vytvořit soubor a `create_file_from_path`odeslat data, použijte metodu , `create_file_from_stream` `create_file_from_bytes` nebo `create_file_from_text` metody. Jedná se o metody vysoké úrovně, které provádějí nezbytné bloků, pokud velikost dat překročí 64 MB.

`create_file_from_path`nahraje obsah souboru ze zadané cesty `create_file_from_stream` a nahraje obsah z již otevřeného souboru nebo datového proudu. `create_file_from_bytes`nahraje pole bajtů a `create_file_from_text` nahraje zadanou textovou hodnotu pomocí zadaného kódování (výchozí hodnota UTF-8).

Následující příklad nahraje obsah souboru **sunset.png** do souboru **myfile.**

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
Chcete-li stáhnout data `get_file_to_path`ze `get_file_to_stream` `get_file_to_bytes`souboru, použijte , , nebo `get_file_to_text`. Jedná se o metody vysoké úrovně, které provádějí nezbytné bloků, pokud velikost dat překročí 64 MB.

Následující příklad ukazuje `get_file_to_path` použití ke stažení obsahu souboru **myfile** a uložit jej do souboru **out-sunset.png.**

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Odstranění souboru
Nakonec soubor odstraníte, `delete_file`zavolejte .

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Vytvořit snímek sdílené složky
Můžete vytvořit kopii bodu v čase celé sdílené složky.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Vytvoření snímku sdílené složky s metadaty**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Seznam sdílených složek a snímků 
Můžete vypsat všechny snímky pro určitou sdílenou složku.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Procházet snímek sdílené složky
Můžete procházet obsah každého snímku sdílené složky načíst soubory a adresáře z tohoto okamžiku v čase.

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Získání souboru ze snímku sdílené složky
Můžete stáhnout soubor ze snímku sdílené složky pro váš scénář obnovení.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Odstranění snímku jedné sdílené složky  
Můžete odstranit snímek jedné sdílené složky.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Odstranit sdílenou složku, pokud existují snímky sdílené složky
Sdílená sdílená složku, která obsahuje snímky nelze odstranit, pokud všechny snímky jsou odstraněny jako první.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili, jak manipulovat se soubory Azure pomocí Pythonu, postupujte podle těchto odkazů, abyste se dozvěděli další informace.

* [Středisko pro vývojáře programující v Pythonu](https://azure.microsoft.com/develop/python/)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)
* [Sada Microsoft Azure Storage SDK pro Python](https://github.com/Azure/azure-storage-python)
