---
title: "Jak používat Azure Blob storage (úložiště objektů) z Python | Microsoft Docs"
description: "Ukládejte nestrukturovaná data v cloudu pomocí Azure Blob Storage (úložiště objektů)."
services: storage
documentationcenter: python
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 0348e360-b24d-41fa-bb12-b8f18990d8bc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 2/24/2017
ms.author: tamram
ms.openlocfilehash: b94a0f95454f9243ef09ce37a62466bca4003a91
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-use-azure-blob-storage-from-python"></a>Jak používat Azure Blob storage z Pythonu
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Přehled
Úložiště objektů blob v Azure je služba, která ukládá nestrukturovaná data v cloudu jako objekty nebo objekty blob. Do Blob storage se dá ukládat jakýkoli druh textu nebo binárních dat, jako je dokument, soubor médií nebo instalátor aplikace. Blob storage se také nazývá úložiště objektů.

Tento článek vám ukáže, jak provádět běžné scénáře s využitím úložiště objektů Blob. Ukázky jsou napsané v Pythonu a použití [Microsoft Azure SDK úložiště pro jazyk Python]. Pokryté scénáře zahrnují odesílání, výpis, stahování a odstraňování objektů BLOB.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Stáhněte a nainstalujte úložiště Azure SDK pro Python

Azure SDK úložiště pro Python vyžaduje Python 2.7 3.3, 3.4, 3.5 nebo 3.6 a je rozdělena na 4 různých balíčcích: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` a `azure-storage-queue`. V tomto kurzu budeme používat `azure-storage-blob` balíčku.
 
### <a name="install-via-pypi"></a>Nainstalovat prostřednictvím úložiště PyPi

Chcete-li nainstalovat prostřednictvím indexu balíčků Pythonu (úložiště PyPI), zadejte:

```bash
pip install azure-storage-blob
```


> [!NOTE]
> Pokud upgradujete ze sady SDK úložiště Azure pro jazyk Python 0.36 nebo starší verze, bude nejprve musíte odinstalovat pomocí `pip uninstall azure-storage` jako už sady SDK úložiště pro jazyk Python v vydáváme jeden balíček.
> 
> 

Metody alternativní instalace, najdete v článku [sada SDK úložiště Azure pro jazyk Python na Githubu](https://github.com/Azure/azure-storage-python/).

## <a name="create-a-container"></a>Vytvoření kontejneru
Na základě typu objektu blob, které chcete použít, vytvořte **BlockBlobService**, **AppendBlobService**, nebo **PageBlobService** objektu. Následující kód používá **BlockBlobService** objektu. Přidejte následující v horní části všech soubor Python, ve kterém chcete prostřednictvím kódu programu přístup k úložišti objektů Blob bloku Azure.

```python
from azure.storage.blob import BlockBlobService
```

Následující kód vytvoří **BlockBlobService** pomocí klíč účet a název účtu úložiště.  Nahraďte název účtu a klíč 'stránku Můj účet' a 'mykey.

```python
block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')
```

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

V následujícím příkladu kódu, můžete použít **BlockBlobService** objekt, který chcete vytvořit kontejner, pokud neexistuje.

```python
block_blob_service.create_container('mycontainer')
```

Ve výchozím nastavení je nový kontejner privátní, proto přístupový klíč k úložišti musí určit (stejně jako dříve) ke stažení z tohoto kontejneru objektů BLOB. Pokud chcete zpřístupnit objektů BLOB v kontejneru pro všechny uživatele, můžete vytvořit kontejner a předat úroveň veřejný přístup pomocí následujícího kódu.

```python
from azure.storage.blob import PublicAccess
block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)
```

Alternativně můžete upravit kontejner po vytvoření ho pomocí následujícího kódu.

```python
block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)
```

Po této změně kdokoli na Internetu může vidět objekty BLOB ve veřejném kontejneru, ale pouze můžete upravit nebo odstranit.

## <a name="upload-a-blob-into-a-container"></a>Nahrání objektu blob do kontejneru
Chcete-li vytvořit objekt blob bloku a odesílat data, použijte **vytvořit\_objektů blob\_z\_cesta**, **vytvořit\_objektů blob\_z\_datového proudu**, **vytvořit\_blob\_z\_bajtů** nebo **vytvořit\_blob\_z\_text** metody. Jsou nejdůležitější metody, které provádějí potřebné rozdělování, když velikost dat přesáhne 64 MB.

**vytvořit\_objektů blob\_z\_cesta** odešle obsah souboru ze zadané cesty a **vytvořit\_blob\_z\_datového proudu**odešle obsah z již otevřeného souboru/stream. **vytvořit\_blob\_z\_bajtů** odešle pole bajtů, a **vytvořit\_blob\_z\_text** odešle zadaný textové hodnoty pomocí zadaného kódování (výchozí hodnota je UTF-8).

V následujícím příkladu se uloží obsah **sunset.png** soubor do **myblockblob** objektů blob.

```python
from azure.storage.blob import ContentSettings
block_blob_service.create_blob_from_path(
    'mycontainer',
    'myblockblob',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png')
            )
```

## <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru
K zobrazení seznamu objektů BLOB v kontejneru, použijte **seznamu\_objekty BLOB** metoda. Tato metoda vrátí generátor. Následující kód výstupy **název** z jednotlivých objektů blob v kontejneru ke konzole.

```python
generator = block_blob_service.list_blobs('mycontainer')
for blob in generator:
    print(blob.name)
```

## <a name="download-blobs"></a>Stáhnout objekty blob
Ke stahování dat z objektu blob, použijte **získat\_blob\_k\_cesta**, **získat\_blob\_k\_datového proudu**, **získat\_blob\_k\_bajtů**, nebo **získat\_blob\_k\_text**. Jsou nejdůležitější metody, které provádějí potřebné rozdělování, když velikost dat přesáhne 64 MB.

Následující příklad ukazuje, jak pomocí **získat\_blob\_k\_cesta** stáhnout obsah **myblockblob** objektů blob a jejich uložení **na více systémů sunset.png** souboru.

```python
block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')
```

## <a name="delete-a-blob"></a>Odstranění objektu blob
Nakonec se odstranit objekt blob, zavolejte **delete_blob**.

```python
block_blob_service.delete_blob('mycontainer', 'myblockblob')
```

## <a name="writing-to-an-append-blob"></a>Zápis do doplňovacího objektu blob
Doplňovací objekt blob je optimalizován pro operace připojení, například protokolování. Podobně jako objekt blob bloku se doplňovací objekt blob skládá z bloků, ale když chcete do doplňovacího objektu blob připojit nový blok, je připojen vždy na konec objektu blob. Existující blok v doplňovacím objektu blob se nedá aktualizovat ani odstranit. ID bloku pro doplňovací objekt blob nejsou vystavená, protože jsou určená pro objekt blob bloku.

Každý blok v doplňovacím objektu blob může mít různou velikost až do 4 MB, každý doplňovací objekt blob může obsahovat maximálně 50 000 bloků. Maximální velikost doplňovacího objektu blob je proto o něco větší než 195 GB (4 MB × 50 000 bloků).

Následující příklad vytvoří nový doplňovací objekt blob a připojí některá data pro simulaci jednoduché operace protokolování.

```python
from azure.storage.blob import AppendBlobService
append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

# The same containers can hold all types of blobs
append_blob_service.create_container('mycontainer')

# Append blobs must be created before they are appended to
append_blob_service.create_blob('mycontainer', 'myappendblob')
append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy používání Blob storage, podívejte se na následující odkazy a získejte další informace.

* [Středisko pro vývojáře programující v Pythonu](https://azure.microsoft.com/develop/python/)
* [REST API služby Azure Storage](http://msdn.microsoft.com/library/azure/dd179355)
* [Blog týmu Azure Storage]
* [Microsoft Azure SDK úložiště pro jazyk Python]

[Blog týmu Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure SDK úložiště pro jazyk Python]: https://github.com/Azure/azure-storage-python
