---
title: 'Python: Operace správy účtů v Azure Data Lake Storage Gen1 | Dokumenty společnosti Microsoft'
description: Zjistěte, jak pomocí sady Python SDK pracovat s operacemi správy účtu Azure Data Lake Storage Gen1.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c71136ecb57fac460514b5f4815ba19cc22d86cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76290610"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operace správy účtů v Azure Data Lake Storage Gen1 pomocí Pythonu
> [!div class="op_single_selector"]
> * [Sada SDK rozhraní .NET](data-lake-store-get-started-net-sdk.md)
> * [ROZHRANÍ API PRO ODPOČINEK](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Zjistěte, jak pomocí sady Python SDK pro Azure Data Lake Storage Gen1 provádět základní operace správy účtů, jako je vytvoření účtu Data Lake Storage Gen1, seznam účtů Data Lake Storage Gen1 atd. Pokyny k provádění operací souborového systému v zařízení Data Lake Storage Gen1 pomocí Pythonu najdete v tématu [Operace souborového systému v zařízení Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Požadavky

* **Python**. Python si můžete stáhnout [tady](https://www.python.org/downloads/). Tento článek používá Python verze 3.6.2.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skupina prostředků Azure**. Pokyny najdete v tématu [Vytvoření skupiny prostředků Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

## <a name="install-the-modules"></a>Instalace modulů

Chcete-li pracovat s Datovým lakem Storage Gen1 pomocí Pythonu, musíte nainstalovat tři moduly.

* Modul `azure-mgmt-resource`, který zahrnuje moduly Azure pro Active Directory atd.
* Modul, `azure-mgmt-datalake-store` který zahrnuje operace správy účtu Azure Data Lake Storage Gen1. Další informace o tomto modulu najdete v [tématu Azure Data Lake Storage Gen1 Management odkaz na modul](/python/api/azure-mgmt-datalake-store/).
* Modul, `azure-datalake-store` který zahrnuje operace souborového systému Azure Data Lake Storage Gen1. Další informace o tomto modulu naleznete v [tématu azure-datalake-store filesystem reference](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/).

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

## <a name="authentication"></a>Ověřování

V této části popíšeme různé způsoby, jak provádět ověření pomocí Azure AD. Dostupné jsou následující možnosti:

* Ověření pro vaši aplikaci koncovým uživatelem najdete v [tématu Ověřování koncových uživatelů pomocí data lake storage gen1 pomocí Pythonu](data-lake-store-end-user-authenticate-python.md).
* Ověřování mezi službami pro vaši aplikaci najdete v [tématu Ověřování mezi službami pomocí data lake storage gen1 pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Vytvoření účtu Gen1 úložiště klienta a datového jezera

Následující fragment fragmentu nejprve vytvoří klienta účtu Data Lake Storage Gen1. Používá objekt klienta k vytvoření účtu Data Lake Storage Gen1. Nakonec vytvoří objekt klienta systému souborů.

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

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Seznam účtů Data Lake Storage Gen1

    ## List the existing Data Lake Storage Gen1 accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-storage-gen1-account"></a>Odstranění účtu Data Lake Storage Gen1

    ## Delete an existing Data Lake Storage Gen1 account
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Další kroky
* [Operace souborového systému na Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Viz také

* [azure-datalake-store Python (Souborový systém) odkaz](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core)
* [Aplikace s velkým objemem dat s otevřeným zdrojovým kódem kompatibilní s Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
