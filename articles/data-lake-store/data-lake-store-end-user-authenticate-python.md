---
title: 'Ověřování koncového uživatele: Python s Azure Data Lake Storage Gen1 pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak dokončit ověřování koncového uživatele pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory s využitím Pythonu
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
ms.openlocfilehash: 1ba7dbd9436a15989564a806a7c8f586c01e5243
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128065"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Ověřování koncového uživatele pomocí Azure Data Lake Storage Gen1 pomocí Pythonu
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-end-user-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

V tomto článku najdete informace o tom, jak provádět ověřování koncového uživatele pomocí Azure Data Lake Storage Gen1 pomocí sady Python SDK. Ověřování koncového uživatele lze dále rozdělit do dvou kategorií:

* Ověřování koncového uživatele bez ověřování Multi-Factor Authentication
* Ověřování koncového uživatele pomocí služby Multi-Factor authentication

Obě tyto možnosti jsou popsány v tomto článku. Ověřování služba služba s Data Lake Storage Gen1 pomocí Pythonu, přečtěte si téma [ověřování služba služba Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Požadavky

* **Python**. Python si můžete stáhnout [tady](https://www.python.org/downloads/). Tento článek používá Python verze 3.6.2.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvoření aplikace Azure Active Directory "Nativní"**. Je nutné dokončit kroky v [ověřování koncového uživatele s Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalace modulů

Pro práci s Data Lake Storage Gen1 pomocí Pythonu, je nutné nainstalovat tři moduly.

* Modul `azure-mgmt-resource`, který zahrnuje moduly Azure pro Active Directory atd.
* `azure-mgmt-datalake-store` Modul, který zahrnuje operace správy účtů Azure Data Lake Storage Gen1. Další informace o tomto modulu najdete v tématu [odkazu na modul Azure Data Lake Storage Gen1 Management](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* `azure-datalake-store` Modul, který zahrnuje operace systému souborů Azure Data Lake Storage Gen1. Další informace o tomto modulu najdete v tématu [odkazu na modul azure-datalake-store Filesystem](http://azure-datalake-store.readthedocs.io/en/latest/).

Pomocí následujících příkazů tyto moduly nainstalujte.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

1. V integrovaném vývojovém prostředí podle vašeho výběru vytvořte novou aplikaci v Pythonu, například **mysample.py**.

2. Přidáním následujícího fragmentu kódu importujte požadované moduly.

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. Uložte změny v souboru mysample.py.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Ověřování koncového uživatele pomocí služby Multi-Factor authentication

### <a name="for-account-management"></a>Pro správu účtu

Použijte následující fragment kódu k ověření ve službě Azure AD pro operace správy účtů v účtu Data Lake Storage Gen1. Následující fragment kódu můžete použít k ověřování vaší aplikace pomocí vícefaktorového ověřování. Zadejte následující hodnoty pro existující služby Azure AD **nativní** aplikace.

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

Použijte k ověření ve službě Azure AD pro operace systému souborů v účtu Data Lake Storage Gen1. Následující fragment kódu můžete použít k ověřování vaší aplikace pomocí vícefaktorového ověřování. Zadejte následující hodnoty pro existující služby Azure AD **nativní** aplikace.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Ověřování koncového uživatele bez ověřování Multi-Factor Authentication

Toto je zastaralé. Další informace najdete v tématu [ověřování Azure pomocí sady Python SDK](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python#mgmt-auth-token).
   
## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat ověřování koncového uživatele k ověření pomocí Azure Data Lake Storage Gen1 pomocí Pythonu. Teď můžete prohlédnout v následujících článcích, které mluvit o tom, jak používat Python pro práci s Azure Data Lake Storage Gen1.

* [Operace správy účtů v Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-get-started-python.md)
* [Operace s daty v Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-data-operations-python.md)

