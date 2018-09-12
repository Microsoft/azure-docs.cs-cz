---
title: 'Python: Operace systému souborů v Azure Data Lake Storage Gen1 | Dokumentace Microsoftu'
description: Zjistěte, jak pomocí sady Python SDK pracovat systémem souborů Data Lake Storage Gen1.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 33abaf7488579a501dc7e2d0b63645726b86c28b
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44390718"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operace systému souborů v Azure Data Lake Storage Gen1 pomocí Pythonu
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

V tomto článku se dozvíte, jak k provádění operací systému souborů ve službě Azure Data Lake Storage Gen1 pomocí sady Python SDK. Pokyny, jak provádět operace správy účtů v Data Lake Storage Gen1 pomocí Pythonu najdete v tématu [operace správy účtů v Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Požadavky

* **Python**. Python si můžete stáhnout [tady](https://www.python.org/downloads/). Tento článek používá Python verze 3.6.2.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Účet Azure Data Lake Storage Gen1**. Postupujte podle pokynů na adrese [Začínáme s Azure Data Lake Storage Gen1 pomocí webu Azure portal](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>Instalace modulů

Pro práci s Data Lake Storage Gen1 pomocí Pythonu, je nutné nainstalovat tři moduly.

* Modul `azure-mgmt-resource`, který zahrnuje moduly Azure pro Active Directory atd.
* `azure-mgmt-datalake-store` Modul, který zahrnuje operace správy účtů Azure Data Lake Storage Gen1. Další informace o tomto modulu najdete v tématu [odkazu na modul azure-mgmt-datalake-store](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* `azure-datalake-store` Modul, který zahrnuje operace systému souborů Azure Data Lake Storage Gen1. Další informace o tomto modulu najdete v tématu [odkazu na modul azure-datalake-store systému souborů](http://azure-datalake-store.readthedocs.io/en/latest/).

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

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Uložte změny v souboru mysample.py.

## <a name="authentication"></a>Authentication

V této části popíšeme různé způsoby, jak provádět ověření pomocí Azure AD. Dostupné jsou následující možnosti:

* Své aplikaci ověřování koncového uživatele, najdete v části [ověřování koncového uživatele s Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-end-user-authenticate-python.md).
* Své aplikaci ověřování služba služba, naleznete v tématu [ověřování služba služba Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Vytvoření klienta systému souborů

Následující fragment kódu nejprve vytvoří klienta účtu Data Lake Storage Gen1. Objekt klienta použije k vytvoření účtu Data Lake Storage Gen1. Nakonec vytvoří objekt klienta systému souborů.

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

## <a name="next-steps"></a>Další postup
* [Operace správy účtů v Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Další informace najdete v tématech

* [Referenční informace k Azure Data Lake Storage Gen1 Pythonu (systém souborů)](http://azure-datalake-store.readthedocs.io/en/latest)
* [Aplikace typu Open zdroj velké objemy dat kompatibilní s Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
