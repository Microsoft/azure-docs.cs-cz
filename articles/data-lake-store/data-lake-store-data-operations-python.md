---
title: 'Python: Operace souborového systému v úložišti Azure Data Lake Storage Gen1 | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak pomocí sady Python SDK pracovat se souborovým systémem Data Lake Storage Gen1.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 17b53c508f0c4c5ba8fa257fcdc692cdaa45d470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76294214"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operace souborového systému v Azure Data Lake Storage Gen1 pomocí Pythonu
> [!div class="op_single_selector"]
> * [Sada SDK rozhraní .NET](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [ROZHRANÍ API PRO ODPOČINEK](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

V tomto článku se dozvíte, jak pomocí pythonu SDK provádět operace souborového systému na Azure Data Lake Storage Gen1. Pokyny k provádění operací správy účtů v zařízení Data Lake Storage Gen1 pomocí Pythonu najdete [v tématu Operace správy účtů v zařízení Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Požadavky

* **Python**. Python si můžete stáhnout [tady](https://www.python.org/downloads/). Tento článek používá Python verze 3.6.2.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Účet Azure Data Lake Storage Gen1**. Postupujte podle pokynů na [webu Začínáme s Azure Data Lake Storage Gen1 pomocí portálu Azure](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>Instalace modulů

Chcete-li pracovat s Datovým lakem Storage Gen1 pomocí Pythonu, musíte nainstalovat tři moduly.

* Modul `azure-mgmt-resource`, který zahrnuje moduly Azure pro Active Directory atd.
* Modul, `azure-mgmt-datalake-store` který zahrnuje operace správy účtu Azure Data Lake Storage Gen1. Další informace o tomto modulu naleznete v [odkazu na modul azure-mgmt-datalake-store](/python/api/azure-mgmt-datalake-store/).
* Modul, `azure-datalake-store` který zahrnuje operace souborového systému Azure Data Lake Storage Gen1. Další informace o tomto modulu naleznete v [odkazu na modul souborového systému azure-datalake-store](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/).

Pomocí následujících příkazů tyto moduly nainstalujte.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

1. Pomocí integrovaného vývojového prostředí (IDE) podle vašeho výběru vytvořte novou aplikaci v Pythonu, například **mysample.py**.

2. Přidáním následujících řádků importujte požadované moduly.

   ```
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

## <a name="authentication"></a>Ověřování

V této části popíšeme různé způsoby, jak provádět ověření pomocí Azure AD. Dostupné jsou následující možnosti:

* Ověření pro vaši aplikaci koncovým uživatelem najdete v [tématu Ověřování koncových uživatelů pomocí data lake storage gen1 pomocí Pythonu](data-lake-store-end-user-authenticate-python.md).
* Ověřování mezi službami pro vaši aplikaci najdete v [tématu Ověřování mezi službami pomocí data lake storage gen1 pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Vytvoření klienta systému souborů

Následující fragment fragmentu nejprve vytvoří klienta účtu Data Lake Storage Gen1. Používá objekt klienta k vytvoření účtu Data Lake Storage Gen1. Nakonec vytvoří objekt klienta systému souborů.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)

## <a name="create-a-directory"></a>Vytvoření adresáře

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>Nahrání souboru


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>Stažení souboru

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>Odstranění adresáře

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="next-steps"></a>Další kroky
* [Operace správy účtů na Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Viz také

* [Azure Data Lake Storage Gen1 Python (souborový systém) Reference](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core)
* [Aplikace s velkým objemem dat s otevřeným zdrojovým kódem kompatibilní s Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
