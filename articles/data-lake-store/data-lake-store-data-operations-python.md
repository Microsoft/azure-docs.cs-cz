---
title: 'Python: Operace systému souborů v Azure Data Lake Store | Dokumentace Microsoftu'
description: Zjistěte, jak pomocí sady Python SDK pracovat se systémem souborů Data Lake Store.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 6e92eed5e4083c9b8063318b2a9139e20f53de1a
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-python"></a>Operace systému souborů v Azure Data Lake Store pomocí Pythonu
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

V tomto článku zjistíte, jak používat sadu Python SDK k provádění operací systému souborů v Azure Data Lake Store. Pokyny k provádění operací správy účtů ve službě Data Lake Store pomocí Pythonu najdete v tématu [Operace správy účtů ve službě Data Lake Store pomocí Pythonu](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Požadavky

* **Python**. Python si můžete stáhnout [tady](https://www.python.org/downloads/). Tento článek používá Python verze 3.6.2.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Účet Azure Data Lake Store**. Postupujte podle pokynů v tématu [Začínáme s Azure Data Lake Store s použitím webu Azure Portal](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>Instalace modulů

Abyste mohli pracovat se službou Data Lake Store pomocí Pythonu, je nutné nainstalovat tři moduly.

* Modul `azure-mgmt-resource`, který zahrnuje moduly Azure pro Active Directory atd.
* Modul `azure-mgmt-datalake-store`, který zahrnuje operace správy účtů Azure Data Lake Store. Další informace o tomto modulu najdete v [referenčních informacích k modulu Azure Data Lake Store Management](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* Modul `azure-datalake-store`, který zahrnuje operace systému souborů Azure Data Lake Store. Další informace o tomto modulu najdete v [referenčních informacích k modulu Azure Data Lake Store Filesystem](http://azure-datalake-store.readthedocs.io/en/latest/).

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

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
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

* Pokud chcete ve své aplikaci ověřování koncového uživatele, přečtěte si téma [Ověřování koncového uživatele pomocí služby Data Lake Store s použitím Pythonu](data-lake-store-end-user-authenticate-python.md).
* Pokud chcete ve své aplikaci ověřování služba-služba, přečtěte si téma [Ověřování služba-služba pomocí služby Data Lake Store s použitím Pythonu](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Vytvoření klienta systému souborů

Následující fragment kódu nejprve vytvoří klienta účtu Data Lake Store. Objekt klienta použije k vytvoření účtu Data Lake Store. Nakonec vytvoří objekt klienta systému souborů.

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
* [Operace správy účtů ve službě Data Lake Store pomocí Pythonu](data-lake-store-get-started-python.md)

## <a name="see-also"></a>Viz také
* [Referenční informace k Pythonu pro Azure Data Lake Store (správa účtů)](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
* [Referenční informace k Pythonu pro Azure Data Lake Store (systém souborů)](http://azure-datalake-store.readthedocs.io/en/latest)
* [Aplikace typu Open Source pro velké objemy dat kompatibilní s Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)
