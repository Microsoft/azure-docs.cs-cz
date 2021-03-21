---
title: Python-Service-to-Service – ověřování – Data Lake Storage Gen1
description: Naučte se, jak dosáhnout ověřování služby-služba pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory pomocí Pythonu.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: fe987070438c689e899dee18edaf4f708555ab11
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92106752"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Ověřování služba-služba pomocí Azure Data Lake Storage Gen1 s využitím Pythonu
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-service-to-service-authenticate-java.md)
> * [Pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

V tomto článku se dozvíte, jak pomocí sady Python SDK provádět ověřování pomocí služby s Azure Data Lake Storage Gen1. Ověřování koncových uživatelů pomocí Data Lake Storage Gen1 pomocí Pythonu najdete v tématu [ověřování koncových uživatelů pomocí Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Předpoklady

* **Python**. Python si můžete stáhnout [tady](https://www.python.org/downloads/). Tento článek používá Python verze 3.6.2.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvořte Azure Active Directory aplikaci "Web"**. Musíte dokončit kroky v [ověřování služba-služba s Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalace modulů

Pokud chcete pracovat s Data Lake Storage Gen1 pomocí Pythonu, musíte nainstalovat tři moduly.

* Modul `azure-mgmt-resource`, který zahrnuje moduly Azure pro Active Directory atd.
* `azure-mgmt-datalake-store`Modul, který zahrnuje operace správy účtu Data Lake Storage Gen1. Další informace o tomto modulu najdete v referenčních informacích k [modulu správy Azure Data Lake Storage Gen1](/python/api/azure-mgmt-datalake-store/).
* `azure-datalake-store`Modul, který zahrnuje operace data Lake Storage Gen1 systému souborů. Další informace o tomto modulu najdete v tématu [Reference k modulům systému souborů Azure-datalake-Store](/python/api/azure-datalake-store/azure.datalake.store.core/).

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
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Uložte změny v souboru mysample.py.

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Ověřování služba-služba s tajným klíčem klienta pro správu účtu

Tento fragment kódu použijte k ověření pomocí Azure AD při operacích správy účtů na Data Lake Storage Gen1, jako je například vytvoření účtu Data Lake Storage Gen1, odstranění účtu Data Lake Storage Gen1 atd. Následující fragment kódu lze použít k neinteraktivnímu ověřování vaší aplikace pomocí tajného klíče klienta pro aplikaci nebo instanční objekt existující aplikace Web App služby Azure AD.

```python
authority_host_uri = 'https://login.microsoftonline.com'
tenant = '<TENANT>'
authority_uri = authority_host_uri + '/' + tenant
RESOURCE = 'https://management.core.windows.net/'
client_id = '<CLIENT_ID>'
client_secret = '<CLIENT_SECRET>'

context = adal.AuthenticationContext(authority_uri, api_version=None)
mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)
```

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Ověřování služba-služba s tajným klíčem klienta pro operace se systémem souborů

Pomocí následujícího fragmentu kódu se pomocí služby Azure AD pro operace systému souborů proveďte v Data Lake Storage Gen1, jako je například vytváření složek, nahrávání souborů atd. Následující fragment kódu lze použít k neinteraktivnímu ověřování vaší aplikace pomocí tajného klíče klienta pro aplikaci nebo instanční objekt. Použijte tento fragment kódu se stávající aplikací Azure AD Webová aplikace.

```python
tenant = '<TENANT>'
RESOURCE = 'https://datalake.azure.net/'
client_id = '<CLIENT_ID>'
client_secret = '<CLIENT_SECRET>'

adlCreds = lib.auth(tenant_id = tenant,
                client_secret = client_secret,
                client_id = client_id,
                resource = RESOURCE)
```

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Storage Gen1 such as create a Data Lake Storage Gen1 account, delete a Data Lake Storage Gen1 account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak používat ověřování služby pro službu k ověřování pomocí Data Lake Storage Gen1 pomocí Pythonu. Nyní se můžete podívat na následující články, které vás seznámí s postupem použití Pythonu pro práci s Data Lake Storage Gen1.

* [Operace správy účtů na Data Lake Storage Gen1 s využitím Pythonu](data-lake-store-get-started-python.md)
* [Operace s daty při Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-data-operations-python.md)