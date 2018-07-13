---
title: 'Python: Operace správy účtů v Azure Data Lake Store | Dokumentace Microsoftu'
description: Zjistěte, jak pomocí sady Python SDK pracovat s operacemi správy účtů Data Lake Store.
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
ms.openlocfilehash: e5b04a4cfbf26011753715f02baea689ec3065b6
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009242"
---
# <a name="account-management-operations-on-azure-data-lake-store-using-python"></a>Operace správy účtů v Azure Data Lake Store pomocí Pythonu
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Zjistěte, jak pomocí sady Python SDK pro Azure Data Lake Store provádět základní operace správy účtů, jako je například vytvoření účtu Data Lake Store, výpis účtu Data Lake Store atd. Pokyny k provádění operací systému souborů ve službě Data Lake Store pomocí Pythonu najdete v tématu [Operace systému souborů ve službě Data Lake Store pomocí Pythonu](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Požadavky

* **Python**. Python si můžete stáhnout [tady](https://www.python.org/downloads/). Tento článek používá Python verze 3.6.2.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skupina prostředků Azure**. Pokyny najdete v tématu [Vytvoření skupiny prostředků Azure](../azure-resource-manager/resource-group-portal.md).

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

2. Přidáním následujícího fragmentu kódu importujte požadované moduly.

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

## <a name="create-client-and-data-lake-store-account"></a>Vytvoření klienta a účtu Data Lake Store

Následující fragment kódu nejprve vytvoří klienta účtu Data Lake Store. Objekt klienta použije k vytvoření účtu Data Lake Store. Nakonec vytvoří objekt klienta systému souborů.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'

    ## Create data lake store account management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    
## <a name="list-the-data-lake-store-accounts"></a>Výpis účtů Data Lake Store

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-store-account"></a>Odstranění účtů Data Lake Store

    ## Delete the existing Data Lake Store accounts
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Další postup
* [Operace systému souborů ve službě Data Lake Store pomocí Pythonu](data-lake-store-data-operations-python.md)

## <a name="see-also"></a>Další informace najdete v tématech

* [Referenční informace k Pythonu pro Azure Data Lake Store (systém souborů)](http://azure-datalake-store.readthedocs.io/en/latest)
* [Aplikace typu Open Source pro velké objemy dat kompatibilní s Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)
