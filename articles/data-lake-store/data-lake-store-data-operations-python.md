---
title: 'Python: operace systému souborů v Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Naučte se používat sadu Python SDK pro práci s Data Lake Storage Gen1m systémem souborů.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-python
ms.openlocfilehash: 8ca455a802b180163579f67104a61f455dd54f94
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109217"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operace systému souborů při Azure Data Lake Storage Gen1 pomocí Pythonu
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

V tomto článku se naučíte, jak pomocí sady Python SDK provádět operace systému souborů na Azure Data Lake Storage Gen1. Pokyny k provádění operací správy účtů v Data Lake Storage Gen1 pomocí Pythonu najdete v tématu [operace správy účtů v Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Předpoklady

* **Python**. Python si můžete stáhnout [tady](https://www.python.org/downloads/). Tento článek používá Python verze 3.6.2.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Účet Azure Data Lake Storage Gen1**. Postupujte podle pokynů v tématu [Začínáme s Azure Data Lake Storage Gen1 pomocí Azure Portal](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>Instalace modulů

Pokud chcete pracovat s Data Lake Storage Gen1 pomocí Pythonu, musíte nainstalovat tři moduly.

* Modul `azure-mgmt-resource`, který zahrnuje moduly Azure pro Active Directory atd.
* `azure-mgmt-datalake-store`Modul, který zahrnuje operace správy účtu Azure Data Lake Storage Gen1. Další informace o tomto modulu najdete v referenčních informacích k [modulu Azure-Správa-datalake-Store](/python/api/azure-mgmt-datalake-store/).
* `azure-datalake-store`Modul, který zahrnuje operace Azure Data Lake Storage Gen1 systému souborů. Další informace o tomto modulu najdete v referenčních informacích o [systémových modulech v souboru Azure-datalake-Store](/python/api/azure-datalake-store/azure.datalake.store.core/).

Pomocí následujících příkazů tyto moduly nainstalujte.

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

1. Pomocí integrovaného vývojového prostředí (IDE) podle vašeho výběru vytvořte novou aplikaci v Pythonu, například **mysample.py**.

2. Přidáním následujících řádků importujte požadované moduly.

   ```python
   ## Use this only for Azure AD service-to-service authentication
   from azure.common.credentials import ServicePrincipalCredentials

   ## Use this only for Azure AD end-user authentication
   from azure.common.credentials import UserPassCredentials

   ## Use this only for Azure AD multi-factor authentication
   from msrestazure.azure_active_directory import AADTokenCredentials

   ## Required for Azure Data Lake Storage Gen1 account management
   from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
   from azure.mgmt.datalake.store.models import DataLakeStoreAccount

   ## Required for Azure Data Lake Storage Gen1 filesystem management
   from azure.datalake.store import core, lib, multithread

   ## Common Azure imports
   from azure.mgmt.resource.resources import ResourceManagementClient
   from azure.mgmt.resource.resources.models import ResourceGroup

   ## Use these as needed for your application
   import logging, getpass, pprint, uuid, time
   ```

3. Uložte změny v souboru mysample.py.

## <a name="authentication"></a>Authentication

V této části popíšeme různé způsoby, jak provádět ověření pomocí Azure AD. Dostupné jsou následující možnosti:

* Pro ověřování koncového uživatele pro vaši aplikaci si přečtěte téma [ověřování koncových uživatelů s Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-end-user-authenticate-python.md).
* Pro ověřování služby pro vaši aplikaci, přečtěte si téma [ověřování služba-služba s Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Vytvoření klienta systému souborů

Následující fragment kódu nejprve vytvoří klienta Data Lake Storage Gen1 účtu. Pomocí objektu klienta vytvoří účet Data Lake Storage Gen1. Nakonec vytvoří objekt klienta systému souborů.

```python
## Declare variables
subscriptionId = 'FILL-IN-HERE'
adlsAccountName = 'FILL-IN-HERE'

## Create a filesystem client object
adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)
```

## <a name="create-a-directory"></a>Vytvoření adresáře

```python
## Create a directory
adlsFileSystemClient.mkdir('/mysampledirectory')
```

## <a name="upload-a-file"></a>Nahrání souboru

```python
## Upload a file
multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)
```


## <a name="download-a-file"></a>Stažení souboru

```python
## Download a file
multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)
```

## <a name="delete-a-directory"></a>Odstranění adresáře

```python
## Delete a directory
adlsFileSystemClient.rm('/mysampledirectory', recursive=True)
```

## <a name="next-steps"></a>Další kroky
* [Operace správy účtů na data Lake Storage Gen1 s využitím Pythonu](data-lake-store-get-started-python.md)

## <a name="see-also"></a>Viz také

* [Reference k Azure Data Lake Storage Gen1 Pythonu (systém souborů)](/python/api/azure-datalake-store/azure.datalake.store.core)
* [Open Source aplikace pro velké objemy dat kompatibilní s Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)