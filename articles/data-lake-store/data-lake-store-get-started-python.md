---
title: Správa účtu Azure Data Lake Storage Gen1 pomocí Pythonu
description: Naučte se používat sadu Python SDK pro operace správy účtu Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-python
ms.openlocfilehash: 57414828394e7f241cd33b7d56190a3ce9a7eab2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87875996"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operace správy účtů na Azure Data Lake Storage Gen1 s využitím Pythonu
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Naučte se používat sadu Python SDK pro Azure Data Lake Storage Gen1 k provádění základních operací správy účtů, jako je například vytvoření účtu Data Lake Storage Gen1, výpis účtů Data Lake Storage Gen1 atd. Pokyny k provádění operací systému souborů v Data Lake Storage Gen1 pomocí Pythonu najdete v tématu [operace systému souborů na data Lake Storage Gen1 pomocí Pythonu](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Požadavky

* **Python**. Python si můžete stáhnout [tady](https://www.python.org/downloads/). Tento článek používá Python verze 3.6.2.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skupina prostředků Azure**. Pokyny najdete v tématu [Vytvoření skupiny prostředků Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

## <a name="install-the-modules"></a>Instalace modulů

Pokud chcete pracovat s Data Lake Storage Gen1 pomocí Pythonu, musíte nainstalovat tři moduly.

* Modul `azure-mgmt-resource`, který zahrnuje moduly Azure pro Active Directory atd.
* `azure-mgmt-datalake-store`Modul, který zahrnuje operace správy účtu Azure Data Lake Storage Gen1. Další informace o tomto modulu najdete v referenčních informacích k [modulu správy Azure Data Lake Storage Gen1](/python/api/azure-mgmt-datalake-store/).
* `azure-datalake-store`Modul, který zahrnuje operace Azure Data Lake Storage Gen1 systému souborů. Další informace o tomto modulu najdete v tématu [Reference k modulům systému souborů Azure-datalake-Store](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/).

Pomocí následujících příkazů tyto moduly nainstalujte.

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

1. Pomocí integrovaného vývojového prostředí (IDE) podle vašeho výběru vytvořte novou aplikaci v Pythonu, například **mysample.py**.

2. Přidáním následujícího fragmentu kódu importujte požadované moduly.

    ```python
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

* Pro ověřování koncového uživatele pro vaši aplikaci si přečtěte téma [ověřování koncových uživatelů s Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-end-user-authenticate-python.md).
* Pro ověřování služby pro vaši aplikaci, přečtěte si téma [ověřování služba-služba s Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Vytvořit klientský a Data Lake Storage Gen1 účet

Následující fragment kódu nejprve vytvoří klienta Data Lake Storage Gen1 účtu. Pomocí objektu klienta vytvoří účet Data Lake Storage Gen1. Nakonec vytvoří objekt klienta systému souborů.

```python
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
```

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Výpis účtů Data Lake Storage Gen1

```python
## List the existing Data Lake Storage Gen1 accounts
result_list_response = adlsAcctClient.account.list()
result_list = list(result_list_response)
for items in result_list:
    print(items)
```

## <a name="delete-the-data-lake-storage-gen1-account"></a>Odstranit účet Data Lake Storage Gen1

```python
## Delete an existing Data Lake Storage Gen1 account
adlsAcctClient.account.delete(adlsAccountName)
```
    

## <a name="next-steps"></a>Další kroky
* [Operace systému souborů na data Lake Storage Gen1 s využitím Pythonu](data-lake-store-data-operations-python.md)

## <a name="see-also"></a>Viz také

* [Reference k jazyku Python (systém souborů) pro Azure – datalake – Store](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core)
* [Open Source aplikace pro velké objemy dat kompatibilní s Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
