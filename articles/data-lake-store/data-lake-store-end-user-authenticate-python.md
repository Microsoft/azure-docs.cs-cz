---
title: Ověřování koncových uživatelů – Python s Data Lake Storage Gen1 – Azure
description: Naučte se, jak dosáhnout ověřování koncovými uživateli pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory pomocí Pythonu.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: c3262a2802da8dbd50fc05f7aa2fea71623c9b42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87872187"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Ověřování koncových uživatelů pomocí Azure Data Lake Storage Gen1 s využitím Pythonu
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-end-user-authenticate-python.md)
> * [Použití REST API](data-lake-store-end-user-authenticate-rest-api.md)
>
>

V tomto článku se dozvíte, jak pomocí sady Python SDK provádět ověřování koncových uživatelů pomocí Azure Data Lake Storage Gen1. Ověřování koncových uživatelů lze dále rozdělit do dvou kategorií:

* Ověřování koncových uživatelů bez vícefaktorového ověřování
* Ověřování koncových uživatelů pomocí Multi-Factor Authentication

Obě tyto možnosti jsou popsány v tomto článku. Informace o ověřování služby-služba pomocí Data Lake Storage Gen1 pomocí Pythonu najdete v tématu [ověřování služby-služba pomocí Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Požadavky

* **Python**. Python si můžete stáhnout [tady](https://www.python.org/downloads/). Tento článek používá Python verze 3.6.2.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvořte Azure Active Directory "nativní" aplikaci**. Musíte dokončit kroky v části [ověřování koncového uživatele s Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

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

1. V integrovaném vývojovém prostředí vytvořte novou aplikaci v Pythonu, například **MySample.py**.

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

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Ověřování koncových uživatelů pomocí Multi-Factor Authentication

### <a name="for-account-management"></a>Pro správu účtů

Pomocí následujícího fragmentu kódu se pomocí služby Azure AD prokáže operace správy účtů na účtu Data Lake Storage Gen1. Následující fragment kódu můžete použít k ověřování vaší aplikace pomocí vícefaktorového ověřování. Zadejte níže uvedené hodnoty pro existující **nativní** aplikaci Azure AD.

```python
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
```

### <a name="for-filesystem-operations"></a>Pro operace systému souborů

Toto použijte k ověření pomocí služby Azure AD pro operace systému souborů na účtu Data Lake Storage Gen1. Následující fragment kódu můžete použít k ověřování vaší aplikace pomocí vícefaktorového ověřování. Zadejte níže uvedené hodnoty pro existující **nativní** aplikaci Azure AD.

```console
adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')
```

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Ověřování koncových uživatelů bez vícefaktorového ověřování

Tato je zastaralá. Další informace najdete v tématu [ověřování Azure pomocí Python SDK](/azure/python/python-sdk-azure-authenticate).

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak používat ověřování koncovými uživateli k ověřování pomocí Azure Data Lake Storage Gen1 pomocí Pythonu. Nyní se můžete podívat na následující články, které vás seznámí s postupem použití Pythonu pro práci s Azure Data Lake Storage Gen1.

* [Operace správy účtů na Data Lake Storage Gen1 s využitím Pythonu](data-lake-store-get-started-python.md)
* [Operace s daty při Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-data-operations-python.md)
