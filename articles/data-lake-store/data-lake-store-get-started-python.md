---
title: 'Python: Operace správy účtů v Azure Data Lake Storage Gen1 | Dokumentace Microsoftu'
description: Zjistěte, jak pomocí sady Python SDK pracovat s Azure Data Lake Storage Gen1 operace správy účtů.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 0cc5b101e1afb6ea648963188887cf43b65a5afa
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124945"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operace správy účtů v Azure Data Lake Storage Gen1 pomocí Pythonu
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Zjistěte, jak pomocí sady Python SDK pro Azure Data Lake Storage Gen1 provádět základní účet operace správy, jako je například vytvoření účtu Data Lake Storage Gen1, Data Lake Storage Gen1 výpis účtů atd. Pokyny k provádění operací systému souborů ve službě Data Lake Storage Gen1 pomocí Pythonu najdete v tématu [operace systému souborů v Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Požadavky

* **Python**. Python si můžete stáhnout [tady](https://www.python.org/downloads/). Tento článek používá Python verze 3.6.2.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skupina prostředků Azure**. Pokyny najdete v tématu [Vytvoření skupiny prostředků Azure](../azure-resource-manager/resource-group-portal.md).

## <a name="install-the-modules"></a>Instalace modulů

Pro práci s Data Lake Storage Gen1 pomocí Pythonu, je nutné nainstalovat tři moduly.

* Modul `azure-mgmt-resource`, který zahrnuje moduly Azure pro Active Directory atd.
* `azure-mgmt-datalake-store` Modul, který zahrnuje operace správy účtů Azure Data Lake Storage Gen1. Další informace o tomto modulu najdete v tématu [odkazu na modul Azure Data Lake Storage Gen1 Management](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* `azure-datalake-store` Modul, který zahrnuje operace systému souborů Azure Data Lake Storage Gen1. Další informace o tomto modulu najdete v tématu [odkazu na modul azure-datalake-store filesystem](http://azure-datalake-store.readthedocs.io/en/latest/).

Pomocí následujících příkazů tyto moduly nainstalujte.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

1. Pomocí integrovaného vývojového prostředí (IDE) podle vašeho výběru vytvořte novou aplikaci v Pythonu, například **mysample.py**.

2. Přidáním následujícího fragmentu kódu importujte požadované moduly.

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
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

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Vytvoření klienta a účtu Data Lake Storage Gen1

Následující fragment kódu nejprve vytvoří klienta účtu Data Lake Storage Gen1. Objekt klienta použije k vytvoření účtu Data Lake Storage Gen1. Nakonec vytvoří objekt klienta systému souborů.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'

    ## Create Data Lake Storage Gen1 account management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

    ## Create a Data Lake Storage Gen1 account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Výpis účtů Data Lake Storage Gen1

    ## List the existing Data Lake Storage Gen1 accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-storage-gen1-account"></a>Odstranění účtu Data Lake Storage Gen1

    ## Delete an existing Data Lake Storage Gen1 account
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Další postup
* [Operace systému souborů v Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Další informace najdete v tématech

* [referenční informace k Azure-datalake-store Pythonu (systém souborů)](http://azure-datalake-store.readthedocs.io/en/latest)
* [Aplikace typu Open zdroj velké objemy dat kompatibilní s Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
