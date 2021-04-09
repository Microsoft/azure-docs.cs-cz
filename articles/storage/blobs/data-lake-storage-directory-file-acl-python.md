---
title: Použití Pythonu ke správě dat v Azure Data Lake Storage Gen2
description: Pomocí Pythonu můžete spravovat adresáře a soubory v účtech úložiště, které mají povolený hierarchický obor názvů.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: a143c0aa19241b532cabff95fe6bf85679e4007c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652288"
---
# <a name="use-python-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Použití Pythonu ke správě adresářů a souborů v Azure Data Lake Storage Gen2

V tomto článku se dozvíte, jak pomocí Pythonu vytvářet a spravovat adresáře a soubory v účtech úložiště, které mají hierarchický obor názvů.

Další informace o tom, jak získat, nastavit a aktualizovat seznamy řízení přístupu (ACL) adresářů a souborů, najdete v tématu [použití Pythonu ke správě seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-acl-python.md).

[Balíček (index balíčku Pythonu)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  Reference k rozhraní [API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  Mapování Gen1 na [Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

- Účet úložiště s povoleným hierarchickým oborem názvů. Pokud ho chcete vytvořit, postupujte podle [těchto](create-data-lake-storage-account.md) pokynů.

## <a name="set-up-your-project"></a>Nastavení projektu

Nainstalujte klientskou knihovnu Azure Data Lake Storage pro Python pomocí [PIP](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Přidejte tyto příkazy pro import do horní části souboru kódu.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Připojit k účtu

Pokud chcete používat fragmenty kódu v tomto článku, budete muset vytvořit instanci **DataLakeServiceClient** , která představuje účet úložiště. 

### <a name="connect-by-using-an-account-key"></a>Připojení pomocí klíče účtu

Toto je nejjednodušší způsob, jak se připojit k účtu. 

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí klíče účtu.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::

- Nahraďte `storage_account_name` hodnotu zástupného symbolu názvem vašeho účtu úložiště.

- Nahraďte `storage_account_key` hodnotu zástupného symbolu vaším klíčem pro přístup k účtu úložiště.

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Připojení pomocí Azure Active Directory (Azure AD)

K ověření vaší aplikace v Azure AD můžete použít [klientskou knihovnu Azure identity pro Python](https://pypi.org/project/azure-identity/) .

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí ID klienta, tajného klíče klienta a ID tenanta.  Pokud chcete získat tyto hodnoty, přečtěte si téma [získání tokenu z Azure AD pro autorizaci žádostí z klientské aplikace](../common/storage-auth-aad-app.md).

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Další příklady najdete v dokumentaci ke [klientské knihovně Azure identity pro Python](https://pypi.org/project/azure-identity/) .

## <a name="create-a-container"></a>Vytvoření kontejneru

Kontejner funguje jako systém souborů pro vaše soubory. Můžete jej vytvořit voláním metody **FileSystemDataLakeServiceClient.create_file_system** .

Tento příklad vytvoří kontejner s názvem `my-file-system` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na adresář voláním metody **FileSystemClient.create_directory** .

Tento příklad přidá adresář s názvem `my-directory` do kontejneru. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Přejmenování nebo přesunutí adresáře

Přejmenujte nebo přesuňte adresář voláním metody **DataLakeDirectoryClient.rename_directory** . Předejte cestu k požadovanému adresáři do parametru. 

Tento příklad přejmenuje podadresář na název `my-subdirectory-renamed` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_RenameDirectory":::

## <a name="delete-a-directory"></a>Odstranění adresáře

Odstraňte adresář voláním metody **DataLakeDirectoryClient.delete_directory** .

Tento příklad odstraní adresář s názvem `my-directory` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře

Nejprve vytvořte odkaz na soubor v cílovém adresáři vytvořením instance třídy **DataLakeFileClient** . Nahrajte soubor voláním metody **DataLakeFileClient.append_data** . Nezapomeňte dokončit nahrávání voláním metody **DataLakeFileClient.flush_data** .

Tento příklad nahraje textový soubor do adresáře s názvem `my-directory` .   

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFile":::

> [!TIP]
> Pokud je velikost souboru velká, váš kód bude muset provést více volání metody **DataLakeFileClient.append_data** . Místo toho zvažte použití metody **DataLakeFileClient.upload_data** . Tímto způsobem můžete nahrát celý soubor v jednom volání. 

## <a name="upload-a-large-file-to-a-directory"></a>Nahrání velkého souboru do adresáře

Použijte metodu **DataLakeFileClient.upload_data** pro nahrání velkých souborů, aniž by bylo nutné provádět více volání metody **DataLakeFileClient.append_data** .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Stažení z adresáře 

Otevřete místní soubor pro zápis. Pak vytvořte instanci **DataLakeFileClient** , která představuje soubor, který chcete stáhnout. Zavolejte **DataLakeFileClient.read_file** pro čtení bajtů ze souboru a pak zapište tyto bajty do místního souboru. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DownloadFromDirectory":::

## <a name="list-directory-contents"></a>Výpis obsahu adresáře

Výpis obsahu adresáře voláním metody **FileSystemClient.get_paths** a následným vytvořením výčtu výsledků.

Tento příklad vytiskne cestu každého podadresáře a souboru, který se nachází v adresáři s názvem `my-directory` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Viz také

- [Referenční dokumentace k rozhraní API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Balíček (index balíčku Pythonu)](https://pypi.org/project/azure-storage-file-datalake/)
- [ukázky](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Mapování Gen1 na Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-python/issues)