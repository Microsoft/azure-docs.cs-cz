---
title: 'Ověřování koncových uživatelů: Python s Azure Data Lake Storage Gen1 pomocí Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak dosáhnout ověřování koncových uživatelů pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory s Pythonem
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c69f6c1f587285c5c52280c4c49008764d5b20d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265594"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Ověřování koncových uživatelů pomocí Azure Data Lake Storage Gen1 pomocí Pythonu
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-end-user-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

V tomto článku se dozvíte, jak pomocí sady Python SDK provést ověřování koncových uživatelů pomocí Azure Data Lake Storage Gen1. Ověřování koncovým uživatelem lze dále rozdělit do dvou kategorií:

* Ověřování koncových uživatelů bez vícefaktorového ověřování
* Ověřování koncových uživatelů s vícefaktorovým ověřováním

Obě tyto možnosti jsou popsány v tomto článku. Ověřování mezi službami pomocí modulu Gen1 úložiště datového jezera pomocí Pythonu najdete v [tématu Ověřování mezi službami pomocí aplikace Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Požadavky

* **Python**. Python si můžete stáhnout [tady](https://www.python.org/downloads/). Tento článek používá Python verze 3.6.2.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvořte nativní aplikaci služby Azure Active Directory**. Kroky ověřování koncových uživatelů musíte provést [pomocí služby Data Lake Storage Gen1 pomocí služby Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalace modulů

Chcete-li pracovat s Datovým lakem Storage Gen1 pomocí Pythonu, musíte nainstalovat tři moduly.

* Modul `azure-mgmt-resource`, který zahrnuje moduly Azure pro Active Directory atd.
* Modul, `azure-mgmt-datalake-store` který zahrnuje operace správy účtu Azure Data Lake Storage Gen1. Další informace o tomto modulu najdete v [tématu Azure Data Lake Storage Gen1 Management odkaz na modul](/python/api/azure-mgmt-datalake-store/).
* Modul, `azure-datalake-store` který zahrnuje operace souborového systému Azure Data Lake Storage Gen1. Další informace o tomto modulu naleznete v [tématu azure-datalake-store Filesystem reference](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/).

Pomocí následujících příkazů tyto moduly nainstalujte.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

1. V ide dle vašeho výběru vytvořte novou aplikaci Pythonu, například **mysample.py**.

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

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Ověřování koncových uživatelů s vícefaktorovým ověřováním

### <a name="for-account-management"></a>Pro správu účtů

Pomocí následujícího fragmentu se ověřujete pomocí služby Azure AD pro operace správy účtů na účtu Data Lake Storage Gen1. Následující fragment kódu můžete použít k ověřování vaší aplikace pomocí vícefaktorového ověřování. Zadejte níže uvedené hodnoty pro existující **nativní** aplikaci Azure AD.

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

### <a name="for-filesystem-operations"></a>Pro operace souborového systému

Použijte k ověření pomocí Azure AD pro operace souborového systému na účtu Data Lake Storage Gen1. Následující fragment kódu můžete použít k ověřování vaší aplikace pomocí vícefaktorového ověřování. Zadejte níže uvedené hodnoty pro existující **nativní** aplikaci Azure AD.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Ověřování koncových uživatelů bez vícefaktorového ověřování

Tohle je zastaralé. Další informace najdete [v tématu Ověřování Azure pomocí pythonu SDK](/azure/python/python-sdk-azure-authenticate).
   
## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli, jak pomocí ověřování koncových uživatelů k ověření pomocí Azure Data Lake Storage Gen1 pomocí Pythonu. Teď se můžete podívat na následující články, které mluví o tom, jak používat Python pro práci s Azure Data Lake Storage Gen1.

* [Operace správy účtů v zařízení Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-get-started-python.md)
* [Datové operace na Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-data-operations-python.md)

