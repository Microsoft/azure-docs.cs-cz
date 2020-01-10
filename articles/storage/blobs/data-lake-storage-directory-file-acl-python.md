---
title: Azure Data Lake Storage Gen2 Python SDK for Files & seznamy řízení přístupu (Preview)
description: Pomocí Pythonu spravujte adresáře a seznamy řízení přístupu k souborům a adresářům (ACL) v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: dbfee5d897d54d68ea6471f0001793a905c95e62
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75835060"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Použití Pythonu ke správě adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2 (Preview)

V tomto článku se dozvíte, jak pomocí Pythonu vytvářet a spravovat adresáře, soubory a oprávnění v účtech úložiště, které mají povolený hierarchický obor názvů (HNS). 

> [!IMPORTANT]
> Klientská knihovna Azure Data Lake Storage pro Python je aktuálně ve verzi Public Preview.

[Package (index balíčku Python)](https://pypi.org/project/azure-storage-file-datalake/) | [Samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [API Reference](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html) | [Gen1 to Gen2 Mapping](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](data-lake-storage-quickstart-create-account.md) pokynů.

## <a name="set-up-your-project"></a>Nastavení projektu

Nainstalujte klientskou knihovnu Azure Data Lake Storage pro Python pomocí [PIP](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake --pre
```

Přidejte tyto příkazy pro import do horní části souboru kódu.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
```

## <a name="connect-to-the-account"></a>Připojit k účtu

Pokud chcete používat fragmenty kódu v tomto článku, budete muset vytvořit instanci **DataLakeServiceClient** , která představuje účet úložiště. Nejjednodušší způsob, jak ho získat, je použít klíč účtu. 

V tomto příkladu se pomocí klíče účtu vytvoří instance **DataLakeServiceClient** , která představuje účet úložiště. 

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Nahraďte hodnotu zástupného symbolu `storage_account_name` názvem svého účtu úložiště.

- Nahraďte hodnotu zástupného symbolu `storage-account-key` vaším klíčem pro přístup k účtu úložiště.

## <a name="create-a-file-system"></a>Vytvoření systému souborů

Systém souborů funguje jako kontejner pro vaše soubory. Můžete jej vytvořit zavoláním metody **FileSystemDataLakeServiceClient. create_file_system** .

Tento příklad vytvoří systém souborů s názvem `my-file-system`.

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na adresář voláním metody **FileSystemClient. create_directory** .

Tento příklad přidá adresář s názvem `my-directory` do systému souborů. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Přejmenování nebo přesunutí adresáře

Přejmenujte nebo přesuňte adresář voláním metody **DataLakeDirectoryClient. rename_directory** . Předejte cestu k požadovanému adresáři do parametru. 

Tento příklad přejmenuje podadresář na název `my-subdirectory-renamed`.

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

Odstraňte adresář voláním metody **DataLakeDirectoryClient. delete_directory** .

Tento příklad odstraní adresář s názvem `my-directory`.  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>Spravovat oprávnění adresáře

Získejte seznam řízení přístupu (ACL) adresáře voláním metody **DataLakeDirectoryClient. get_access_control** a nastavte seznam řízení přístupu voláním metody **DataLakeDirectoryClient. set_access_control** .

> [!NOTE]
> Pokud vaše aplikace autorizuje přístup pomocí Azure Active Directory (Azure AD), ujistěte se, že se k objektu zabezpečení, který vaše aplikace používá k autorizaci přístupu, přiřadila [role vlastníka dat objektu BLOB úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Pokud se chcete dozvědět víc o tom, jak se používají oprávnění seznamu ACL, a důsledky jejich změny, přečtěte si téma [řízení přístupu v Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

Tento příklad získá a nastaví seznam řízení přístupu pro adresář s názvem `my-directory`. Řetězec `rwxr-xrw-` přidělí oprávnění ke čtení, zápisu a spouštění přiděleného uživateli, přidělí skupině pouze oprávnění číst a spouštět a poskytuje všem ostatním oprávnění ke čtení a zápisu.

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

Nejprve vytvořte odkaz na soubor v cílovém adresáři vytvořením instance třídy **DataLakeFileClient** . Nahrajte soubor voláním metody **DataLakeFileClient. append_data** . Ujistěte se, že jste dokončí nahrávání voláním metody **DataLakeFileClient. flush_data** .

Tento příklad nahraje textový soubor do adresáře s názvem `my-directory`.   

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

## <a name="manage-file-permissions"></a>Správa oprávnění k souborům

Získání seznamu řízení přístupu (ACL) souboru voláním metody **DataLakeFileClient. get_access_control** a nastavením seznamu ACL voláním metody **DataLakeFileClient. set_access_control** .

> [!NOTE]
> Pokud vaše aplikace autorizuje přístup pomocí Azure Active Directory (Azure AD), ujistěte se, že se k objektu zabezpečení, který vaše aplikace používá k autorizaci přístupu, přiřadila [role vlastníka dat objektu BLOB úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Pokud se chcete dozvědět víc o tom, jak se používají oprávnění seznamu ACL, a důsledky jejich změny, přečtěte si téma [řízení přístupu v Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

Tento příklad získá a nastaví seznam řízení přístupu pro soubor s názvem `my-file.txt`. Řetězec `rwxr-xrw-` přidělí oprávnění ke čtení, zápisu a spouštění přiděleného uživateli, přidělí skupině pouze oprávnění číst a spouštět a poskytuje všem ostatním oprávnění ke čtení a zápisu.

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

Otevřete místní soubor pro zápis. Pak vytvořte instanci **DataLakeFileClient** , která představuje soubor, který chcete stáhnout. Zavolejte **DataLakeFileClient. read_file** pro čtení bajtů ze souboru a pak zapište tyto bajty do místního souboru. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        downloaded_bytes = file_client.read_file()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>Výpis obsahu adresáře

Výpis obsahu adresáře voláním metody **FileSystemClient. get_paths** a následným vytvořením výčtu výsledků.

Tento příklad vytiskne cestu každého podadresáře a souboru, který je umístěn v adresáři s názvem `my-directory`.

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

## <a name="see-also"></a>Další informace najdete v tématech

* [Referenční dokumentace k rozhraní API](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [Balíček (index balíčku Pythonu)](https://pypi.org/project/azure-storage-file-datalake/)
* [Ukázky](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Mapování Gen1 na Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-python/issues)
