---
title: Vývoj pro soubory Azure pomocí Pythonu | Microsoft Docs
description: Naučte se vyvíjet aplikace a služby v Pythonu, které používají soubory Azure k ukládání dat souborů.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/08/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: 11c31b9ce3c5a8d8fba18d8e7c46ac38b0559aec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856309"
---
# <a name="develop-for-azure-files-with-python"></a>Vývoj pro Soubory Azure pomocí Pythonu

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Naučte se základy používání Pythonu k vývoji aplikací nebo služeb, které používají soubory Azure k ukládání dat souborů. Vytvořte jednoduchou konzolovou aplikaci a Naučte se provádět základní akce se soubory Python a Azure:

- Vytvoření sdílených složek Azure
- Odstraňování adresářů
- Zobrazení výčtu souborů a adresářů ve sdílené složce Azure
- Nahrání, stažení a odstranění souboru
- Vytváření záloh sdílených složek pomocí snímků

> [!NOTE]
> Vzhledem k tomu, že je možné získat přístup k souborům Azure přes protokol SMB, je možné psát jednoduché aplikace, které přistupují ke sdílené složce Azure pomocí standardních tříd a funkcí Pythonu v/v. Tento článek popisuje, jak psát aplikace, které používají sadu Azure Files Storage Python SDK, která používá [REST API souborů Azure](/rest/api/storageservices/file-service-rest-api) ke komunikaci se soubory Azure.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Stažení a instalace sady Azure Storage SDK pro Python

> [!NOTE]
> Pokud provádíte upgrade ze sady Azure Storage SDK pro Python verze 0,36 nebo starší, odinstalujte starší sadu SDK pomocí nástroje `pip uninstall azure-storage` před instalací nejnovějšího balíčku.

# <a name="python-v12"></a>[Python V12](#tab/python)

[Klientská knihovna Azure File Storage V12. x pro Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share) vyžaduje Python 2,7 nebo 3,5.

# <a name="python-v2"></a>[Python v2](#tab/python2)

[Sada SDK Azure Storage pro Python](https://github.com/azure/azure-storage-python) vyžaduje Python 2,7, 3,3, 3,4, 3,5 nebo 3,6.

---

## <a name="install-via-pypi"></a>Instalace prostřednictvím PyPI

K instalaci prostřednictvím indexu balíčku Pythonu (PyPI) zadejte:

# <a name="python-v12"></a>[Python V12](#tab/python)

```console
pip install azure-storage-file-share
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-file
```

### <a name="view-the-sample-application"></a>Zobrazit ukázkovou aplikaci

Pokud chcete zobrazit a spustit ukázkovou aplikaci, která ukazuje, jak používat Python se soubory Azure, přečtěte si téma [Azure Storage: Začínáme se soubory Azure v Pythonu](https://github.com/Azure-Samples/storage-file-python-getting-started).

Pokud chcete spustit ukázkovou aplikaci, ujistěte se, že jste nainstalovali `azure-storage-file` `azure-storage-common` balíčky a.

---

## <a name="set-up-your-application-to-use-azure-files"></a>Nastavení aplikace pro používání souborů Azure

Přidejte následující v horní části zdrojového souboru Pythonu, aby se použily fragmenty kódu v tomto článku.

# <a name="python-v12"></a>[Python V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_Imports":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
from azure.storage.file import FileService
```

---

## <a name="set-up-a-connection-to-azure-files"></a>Nastavení připojení k souborům Azure

# <a name="python-v12"></a>[Python V12](#tab/python)

[ShareServiceClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareserviceclient) umožňuje pracovat se sdílenými složkami, adresáři a soubory. Následující kód vytvoří `ShareServiceClient` objekt pomocí připojovacího řetězce účtu úložiště.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateShareServiceClient":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

`FileService`Objekt umožňuje pracovat se sdílenými složkami, adresáři a soubory. Následující kód vytvoří `FileService` objekt pomocí názvu účtu úložiště a klíče účtu. Nahraďte `<myaccount>` a `<mykey>` názvem a klíčem vašeho účtu.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

---

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure

# <a name="python-v12"></a>[Python V12](#tab/python)

Následující příklad kódu používá objekt [ShareClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient) k vytvoření sdílené složky, pokud neexistuje.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateFileShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Následující příklad kódu používá `FileService` objekt pro vytvoření sdílené složky, pokud neexistuje.

```python
file_service.create_share('myshare')
```

---

## <a name="create-a-directory"></a>Vytvoření adresáře

Úložiště můžete uspořádat tak, že umístíte soubory do podadresářů místo jejich použití v kořenovém adresáři.

# <a name="python-v12"></a>[Python V12](#tab/python)

Následující metoda vytvoří adresář v kořenovém adresáři zadané sdílené složky pomocí objektu [ShareDirectoryClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharedirectoryclient) .

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateDirectory":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Následující kód vytvoří podadresář s názvem *sampledir* v kořenovém adresáři.

```python
file_service.create_directory('myshare', 'sampledir')
```

---

## <a name="upload-a-file"></a>Nahrání souboru

V této části se dozvíte, jak nahrát soubor z místního úložiště do Azure File Storage.

# <a name="python-v12"></a>[Python V12](#tab/python)

Následující metoda nahraje obsah zadaného souboru do zadaného adresáře v zadané sdílené složce Azure.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_UploadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Sdílená složka Azure obsahuje alespoň kořenový adresář, kde se můžou umístit soubory. Chcete-li vytvořit soubor a odeslat data, použijte `create_file_from_path` metody,, `create_file_from_stream` `create_file_from_bytes` nebo `create_file_from_text` . Jsou to metody vysoké úrovně, které provádějí nezbytné datové bloky, když velikost dat překročí 64 MB.

`create_file_from_path` nahraje obsah souboru ze zadané cesty a `create_file_from_stream` nahraje obsah z již otevřeného souboru nebo datového proudu. `create_file_from_bytes` nahraje pole bajtů a `create_file_from_text` nahraje zadanou textovou hodnotu pomocí zadaného kódování (standardně UTF-8).

Následující příklad nahraje obsah *sunset.png* souboru do souboru **MyFile** .

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this file in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Zobrazení výčtu souborů a adresářů ve sdílené složce Azure

# <a name="python-v12"></a>[Python V12](#tab/python)

Chcete-li zobrazit seznam souborů a adresářů v podadresáři, použijte metodu [list_directories_and_files](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#list-directories-and-files-directory-name-none--name-starts-with-none--marker-none----kwargs-) . Tato metoda vrací automatické stránkování iterě. Následující kód vypíše **název** každého souboru a podadresáře v zadaném adresáři do konzoly.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListFilesAndDirs":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Chcete-li zobrazit seznam souborů a adresářů ve sdílené složce, použijte metodu **Výpis \_ adresářů \_ a \_ souborů** . Tato metoda vrací generátor. Následující kód vypíše **název** každého souboru a adresáře ve sdílené složce do konzoly.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

---

## <a name="download-a-file"></a>Stažení souboru

# <a name="python-v12"></a>[Python V12](#tab/python)

Chcete-li stáhnout data ze souboru, použijte [download_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#download-file-offset-none--length-none----kwargs-).

Následující příklad ukazuje použití nástroje `download_file` k získání obsahu zadaného souboru a jeho místnímu uložení se **staženými** zprávami, které se připojí k názvu souboru.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Chcete-li stáhnout data ze souboru, použijte `get_file_to_path` ,, `get_file_to_stream` `get_file_to_bytes` nebo `get_file_to_text` . Jsou to metody vysoké úrovně, které provádějí nezbytné datové bloky, když velikost dat překročí 64 MB.

Následující příklad ukazuje použití `get_file_to_path` ke stažení obsahu souboru **MyFile** a jeho uložení do souboru *out-sunset.png* .

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

---

## <a name="create-a-share-snapshot"></a>Vytvoření snímku sdílené složky

Můžete vytvořit bod v čase kopie celé sdílené složky.

# <a name="python-v12"></a>[Python V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Vytvoření snímku sdílené složky s metadaty**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

---

## <a name="list-shares-and-snapshots"></a>Vypsat sdílené složky a snímky

Můžete vypsat všechny snímky pro konkrétní sdílenou složku.

# <a name="python-v12"></a>[Python V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListSharesAndSnapshots":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

---

## <a name="browse-share-snapshot"></a>Procházet snímek sdílené složky

Můžete procházet každý snímek sdílené složky a načíst soubory a adresáře z daného bodu v čase.

# <a name="python-v12"></a>[Python V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_BrowseSnapshotDir":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

---

## <a name="get-file-from-share-snapshot"></a>Získat soubor ze snímku sdílené složky

Soubor si můžete stáhnout ze snímku sdílené složky. To vám umožní obnovit předchozí verzi souboru.

# <a name="python-v12"></a>[Python V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadSnapshotFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

---

## <a name="delete-a-single-share-snapshot"></a>Odstranit jeden snímek sdílené složky
Můžete odstranit jeden snímek sdílené složky.

# <a name="python-v12"></a>[Python V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

---

## <a name="delete-a-file"></a>Odstranění souboru

# <a name="python-v12"></a>[Python V12](#tab/python)

Chcete-li odstranit soubor, zavolejte [delete_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#delete-file---kwargs-).

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Chcete-li odstranit soubor, zavolejte `delete_file` .

```python
file_service.delete_file('myshare', None, 'myfile')
```

---

## <a name="delete-share-when-share-snapshots-exist"></a>Odstranit sdílenou složku, když existují snímky sdílené složky

# <a name="python-v12"></a>[Python V12](#tab/python)

Chcete-li odstranit sdílenou složku obsahující snímky, zavolejte [delete_share](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#delete-share-delete-snapshots-false----kwargs-) s `delete_snapshots=True` .

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Sdílenou složku obsahující snímky nelze odstranit, pokud nebyly nejprve odstraněny všechny snímky.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

---

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak manipulovat se soubory Azure pomocí Pythonu, získáte další informace pomocí těchto odkazů.

- [Středisko pro vývojáře programující v Pythonu](/azure/developer/python/)
- [REST API služby Azure Storage](/rest/api/azure/)
- [Sada SDK Microsoft Azure Storage pro Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
