---
title: 'Ověřování koncového uživatele: Python s Data Lake Store pomocí Azure Active Directory | Microsoft Docs'
description: Zjistěte, jak zajistit ověření koncového uživatele s Data Lake Store pomocí Azure Active Directory s Pythonem
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: a93881c7a504279a1c48ba30d8d07e253f4b865b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="end-user-authentication-with-data-lake-store-using-python"></a>Ověření koncových uživatelů s Data Lake Store pomocí jazyka Python
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-end-user-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

V tomto článku se dozvíte o tom, jak pomocí Python SDK provádět ověřování koncových uživatelů s Azure Data Lake Store. Ověřování koncového uživatele můžete dále rozdělit do dvou kategorií:

* Ověřování koncového uživatele bez služby Multi-Factor authentication
* Koncový uživatel ověřování službou Multi-Factor authentication

Obě tyto možnosti jsou popsané v tomto článku. Service-to-service ověřování s Data Lake Store pomocí jazyka Python, najdete v části [Service-to-service ověřování s Data Lake Store pomocí Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Požadavky

* **Python**. Python si můžete stáhnout [tady](https://www.python.org/downloads/). Tento článek používá Python verze 3.6.2.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvoření aplikace Azure Active Directory "Nativní"**. Musí mít dokončili postup v [ověřování koncového uživatele s Data Lake Store pomocí Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

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

1. V prostředí IDE podle vaší volby, vytvořte novou aplikaci Python, například **mysample.py**.

2. Přidáním následujícího fragmentu kódu importujte požadované moduly.

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. Uložte změny v souboru mysample.py.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Koncový uživatel ověřování službou Multi-Factor authentication

### <a name="for-account-management"></a>Pro správu účtu

Následující fragment kódu se používají k ověření s Azure AD pro operace správy účtů v účtu Data Lake Store. Následující fragment kódu můžete použít k ověřování vaší aplikace pomocí vícefaktorového ověřování. Zadejte hodnoty níže existující Azure AD **nativní** aplikace.

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'
    
    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)

### <a name="for-filesystem-operations"></a>Pro operace systému souborů

Použijte k ověření s Azure AD pro operace systému souborů v účtu Data Lake Store. Následující fragment kódu můžete použít k ověřování vaší aplikace pomocí vícefaktorového ověřování. Zadejte hodnoty níže existující Azure AD **nativní** aplikace.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Ověřování koncového uživatele bez služby Multi-Factor authentication

Toto je zastaralé. Další informace najdete v tématu [ověřování Azure pomocí sady SDK pro Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python#mgmt-auth-token).
   
## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat ověřování koncových uživatelů k ověřování s Azure Data Lake Store pomocí jazyka Python. Teď můžete prohlédnout v následujících článcích, které mluvit o tom, jak používat jazyk Python pro práci s Azure Data Lake Store.

* [Operace správy účtů v Data Lake Store pomocí jazyka Python](data-lake-store-get-started-python.md)
* [Operace dat v Data Lake Store pomocí jazyka Python](data-lake-store-data-operations-python.md)

