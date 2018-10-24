---
title: 'Ověřování služba služba: Python s Azure Data Lake Storage Gen1 pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak dokončit ověřování služba služba s Azure Data Lake Storage Gen1 pomocí Azure Active Directory pomocí Pythonu
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
ms.openlocfilehash: a51eb8f560d598f86caf57b7b24737b0a3574b68
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957209"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Ověřování služba služba s Azure Data Lake Storage Gen1 pomocí Pythonu
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-service-to-service-authenticate-java.md)
> * [Pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

V tomto článku najdete informace o tom, jak provádět ověřování služba služba s Azure Data Lake Storage Gen1 pomocí sady Python SDK. Ověřování koncového uživatele s Data Lake Storage Gen1 pomocí Pythonu, přečtěte si téma [ověřování koncového uživatele s Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Požadavky

* **Python**. Python si můžete stáhnout [tady](https://www.python.org/downloads/). Tento článek používá Python verze 3.6.2.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvoření aplikace Azure Active Directory "Web"**. Je nutné dokončit kroky v [ověřování služba služba Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalace modulů

Pro práci s Data Lake Storage Gen1 pomocí Pythonu, je nutné nainstalovat tři moduly.

* Modul `azure-mgmt-resource`, který zahrnuje moduly Azure pro Active Directory atd.
* `azure-mgmt-datalake-store` Modul, který zahrnuje operace správy účtů Data Lake Storage Gen1. Další informace o tomto modulu najdete v tématu [odkazu na modul Azure Data Lake Storage Gen1 Management](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* `azure-datalake-store` Modul, který zahrnuje operace systému souborů Data Lake Storage Gen1. Další informace o tomto modulu najdete v tématu [odkazu na modul azure-datalake-store Filesystem](http://azure-datalake-store.readthedocs.io/en/latest/).

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

Použijte tento fragment kódu k ověření ve službě Azure AD pro operace správy účtů ve službě Data Lake Storage Gen1, jako je například vytvoření účtu Data Lake Storage Gen1, Data Lake Storage Gen1 odstranění účtu atd. Následující fragment kódu můžete použít k neinteraktivnímu ověřování vaší aplikace pomocí tajného klíče klienta pro aplikaci nebo instančního objektu služby existující Azure AD "Webovou aplikaci" aplikace.

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    RESOURCE = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Ověřování služba-služba s tajným klíčem klienta pro operace se systémem souborů

Použít následující fragment kódu pro ověřování pomocí Azure AD pro operace systému souborů v Data Lake Storage Gen1 například vytváření složek, nahrávání souborů atd. Následující fragment kódu lze použít k neinteraktivnímu ověřování vaší aplikace pomocí tajného klíče klienta pro aplikaci nebo instančního objektu. Použijte tento fragment kódu se stávající aplikací Azure AD Webová aplikace.

    tenant = '<TENANT>'
    RESOURCE = 'https://datalake.azure.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    adlCreds = lib.auth(tenant_id = tenant,
                    client_secret = client_secret,
                    client_id = client_id,
                    resource = RESOURCE)

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

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat k ověření pomocí Data Lake Storage Gen1 ověřování služba služba pomocí Pythonu. Teď můžete prohlédnout v následujících článcích, které mluvit o tom, jak používat Python pro práci s Data Lake Storage Gen1.

* [Operace správy účtů v Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-get-started-python.md)
* [Operace s daty v Data Lake Storage Gen1 pomocí Pythonu](data-lake-store-data-operations-python.md)


