---
title: Azure Data Lake Storage Gen2 Python SDK pro soubory & Seznamy ACL
description: Pomocí správy adresářů a seznamů řízení přístupu k souborům a adresářům (ACL) v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: a00713df2cdda626a76cc648826f7e56df214232
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668714"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Použití Pythonu ke správě adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2

Tento článek ukazuje, jak pomocí Pythonu vytvářet a spravovat adresáře, soubory a oprávnění v účtech úložiště, které mají povolený hierarchický obor názvů (HNS). 

[Balíček (Python Package Package Index)](https://pypi.org/project/azure-storage-file-datalake/) | [Ukázky](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [ROZHRANÍ API odkaz](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0/azure.storage.filedatalake.html) | [Gen1 na Gen2 mapování](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [poskytnout zpětnou vazbu](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Účet úložiště, který má povolen hierarchický obor názvů (HNS). [Chcete-li](data-lake-storage-quickstart-create-account.md) jej vytvořit, postupujte podle těchto pokynů.

## <a name="set-up-your-project"></a>Nastavení projektu

Nainstalujte klientskou knihovnu Azure Data Lake Storage pro Python pomocí [pipu](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake --pre
```

Přidejte tyto příkazy importu do horní části souboru kódu.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Připojení k účtu

Chcete-li použít výstřižky v tomto článku, budete muset vytvořit instanci **DataLakeServiceClient,** která představuje účet úložiště. 

### <a name="connect-by-using-an-account-key"></a>Připojení pomocí klíče účtu

Toto je nejjednodušší způsob připojení k účtu. 

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí klíče účtu.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- `storage_account_name` Nahraďte zástupnou hodnotu názvem svého účtu úložiště.

- `storage_account_key` Nahraďte zástupnou hodnotu klíčem pro přístup k účtu úložiště.

### <a name="connect-by-using-azure-active-directory-ad"></a>Připojení pomocí služby Azure Active Directory (AD)

Klientská [knihovna identity Azure pro Python](https://pypi.org/project/azure-identity/) můžete použít k ověření vaší aplikace pomocí Azure AD.

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí ID klienta, tajný klíč klienta a ID klienta.  Pokud chcete získat tyto hodnoty, najdete v článku [Získání tokenu z Azure AD pro autorizaci požadavků z klientské aplikace](../common/storage-auth-aad-app.md).

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Další příklady najdete v [knihovně klienta identity Azure pro](https://pypi.org/project/azure-identity/) dokumentaci pythonu.

## <a name="create-a-file-system"></a>Vytvoření systému souborů

Systém souborů funguje jako kontejner pro vaše soubory. Můžete vytvořit pomocí metody **FileSystemDataLakeServiceClient.create_file_system.**

Tento příklad vytvoří systém `my-file-system`souborů s názvem .

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na adresář voláním metody **FileSystemClient.create_directory.**

Tento příklad přidá `my-directory` adresář s názvem do systému souborů. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Přejmenování nebo přesunutí adresáře

Přejmenujte nebo přesuňte adresář voláním metody **DataLakeDirectoryClient.rename_directory.** Předá cestu požadovaného adresáře parametr. 

Tento příklad přejmenuje podadresář na `my-subdirectory-renamed`název .

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>Odstranění adresáře

Odstraňte adresář voláním metody **DataLakeDirectoryClient.delete_directory.**

Tento příklad odstraní adresář `my-directory`s názvem .  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>Správa oprávnění adresáře

Získejte seznam řízení přístupu (ACL) adresáře voláním metody **DataLakeDirectoryClient.get_access_control** a nastavte seznam ACL voláním metody **DataLakeDirectoryClient.set_access_control.**

> [!NOTE]
> Pokud vaše aplikace autorizuje přístup pomocí Služby Azure Active Directory (Azure AD), ujistěte se, že objekt zabezpečení, který vaše aplikace používá k autorizaci přístupu, byl přiřazen [roli vlastníka dat objektu blob úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Další informace o použití oprávnění seznamu ACL a jejich efektech najdete [v tématu Řízení přístupu v azure datovém úložišti.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

Tento příklad získá a nastaví seznam `my-directory`ACL adresáře s názvem . Řetězec `rwxr-xrw-` poskytuje vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, poskytuje vlastnící skupině pouze oprávnění ke čtení a spouštění a uděluje všem ostatním oprávnění ke čtení a zápisu.

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře 

Nejprve vytvořte odkaz na soubor v cílovém adresáři vytvořením instance třídy **DataLakeFileClient.** Nahrajte soubor voláním metody **DataLakeFileClient.append_data.** Nezapomeňte dokončit nahrávání voláním **Metody DataLakeFileClient.flush_data.**

Tento příklad odešle textový soubor `my-directory`do adresáře s názvem .   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

> [!TIP]
> Pokud je velikost souboru velká, bude váš kód muset provést více volání **metody DataLakeFileClient.append_data.** Zvažte použití metody **DataLakeFileClient.upload_data.** Tímto způsobem můžete nahrát celý soubor v jednom hovoru. 

## <a name="upload-a-large-file-to-a-directory"></a>Nahrání velkého souboru do adresáře

Pomocí metody **DataLakeFileClient.upload_data** nahrát velké soubory bez nutnosti více volání **DataLakeFileClient.append_data** metoda.

```python
def upload_file_to_directory_bulk():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.upload_data(file_contents, overwrite=True)

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>Správa oprávnění k souborům

Získejte seznam řízení přístupu (ACL) souboru voláním metody **DataLakeFileClient.get_access_control** a nastavte seznam ACL voláním metody **DataLakeFileClient.set_access_control.**

> [!NOTE]
> Pokud vaše aplikace autorizuje přístup pomocí Služby Azure Active Directory (Azure AD), ujistěte se, že objekt zabezpečení, který vaše aplikace používá k autorizaci přístupu, byl přiřazen [roli vlastníka dat objektu blob úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Další informace o použití oprávnění seznamu ACL a jejich efektech najdete [v tématu Řízení přístupu v azure datovém úložišti.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

Tento příklad získá a nastaví aCL souboru s názvem `my-file.txt`. Řetězec `rwxr-xrw-` poskytuje vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, poskytuje vlastnící skupině pouze oprávnění ke čtení a spouštění a uděluje všem ostatním oprávnění ke čtení a zápisu.

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## <a name="download-from-a-directory"></a>Stažení z adresáře 

Otevřete místní soubor pro psaní. Potom vytvořte instanci **DataLakeFileClient,** která představuje soubor, který chcete stáhnout. Volání **DataLakeFileClient.read_file** číst bajty ze souboru a pak zapsat tyto bajty do místního souboru. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        download = file_client.download_file()

        downloaded_bytes = download.readall()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>Výpis obsahu adresáře

Seznam obsahu adresáře voláním metody **FileSystemClient.get_paths** a následným výčetem výsledků.

V tomto příkladu vytiskne cestu ke každému podadresáři a souboru, který je umístěn v adresáři s názvem `my-directory`.

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="see-also"></a>Viz také

* [Referenční dokumentace k rozhraní API](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [Balíček (index balíčků Pythonu)](https://pypi.org/project/azure-storage-file-datalake/)
* [Ukázky](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Mapování Gen1 až Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Poskytnout zpětnou vazbu](https://github.com/Azure/azure-sdk-for-python/issues)
