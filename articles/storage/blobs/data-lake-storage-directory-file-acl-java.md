---
title: Azure Data Lake Storage Gen2 Java SDK for Files & seznamy řízení přístupu (Preview)
description: Pomocí knihoven Azure Storage pro jazyk Java můžete spravovat adresáře a seznamy řízení přístupu (ACL) souborů a adresářů v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 575f3ed1a692087cc99881798f7f0ca74c5d3a66
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024445"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Správa adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2 pomocí jazyka Java (Preview)

V tomto článku se dozvíte, jak pomocí jazyka Java vytvářet a spravovat adresáře, soubory a oprávnění v účtech úložiště s povoleným hierarchickým oborem názvů (HNS). 

> [!IMPORTANT]
> Knihovna Java, která je doporučena v tomto článku, je aktuálně ve verzi Public Preview.

[Package (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.0-preview.6/index.html) | [Gen1 na mapování Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [poskytnutí zpětné vazby](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](data-lake-storage-quickstart-create-account.md) pokynů.

## <a name="set-up-your-project"></a>Nastavení projektu

Začněte tím, že otevřete [tuto stránku](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) a získáte nejnovější verzi knihovny Java. Pak otevřete soubor *pom. XML* v textovém editoru. Přidejte element závislosti, který odkazuje na tuto verzi.

Dále přidejte tyto příkazy import do souboru kódu.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.RolePermissions;
```

## <a name="connect-to-the-account"></a>Připojit k účtu 

Pokud chcete používat fragmenty kódu v tomto článku, budete muset vytvořit instanci **DataLakeServiceClient** , která představuje účet úložiště. Nejjednodušší způsob, jak ho získat, je použít klíč účtu. 

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí klíče účtu.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      

```
## <a name="create-a-file-system"></a>Vytvoření systému souborů

Systém souborů funguje jako kontejner pro vaše soubory. Můžete jej vytvořit zavoláním metody **DataLakeServiceClient. createFileSystem** .

Tento příklad vytvoří systém souborů s názvem `my-file-system`. 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na adresář voláním metody **DataLakeFileSystemClient. createDirectory** .

Tento příklad přidá do systému souborů adresář s názvem `my-directory` a poté přidá podadresáři s názvem `my-subdirectory`. 

```java
static public DataLakeDirectoryClient CreateDirectory
(DataLakeServiceClient serviceClient, String fileSystemName){
    
    DataLakeFileSystemClient fileSystemClient =
    serviceClient.getFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.createDirectory("my-directory");

    return directoryClient.createSubDirectory("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Přejmenování nebo přesunutí adresáře

Přejmenujte nebo přesuňte adresář voláním metody **DataLakeDirectoryClient. rename** . Předejte cestu k požadovanému adresáři do parametru. 

Tento příklad přejmenuje podadresář na název `my-subdirectory-renamed`.

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename("my-directory/my-subdirectory-renamed");
}
```

Tento příklad přesune adresář s názvem `my-subdirectory-renamed` do podadresáře adresáře s názvem `my-directory-2`. 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Odstranění adresáře

Odstraňte adresář voláním metody **DataLakeDirectoryClient. deleteWithResponse** .

Tento příklad odstraní adresář s názvem `my-directory`.   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>Správa seznamu ACL adresáře

Tento příklad načte a potom nastaví seznam řízení přístupu pro adresář s názvem `my-directory`. Tento příklad uděluje vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, dává vlastnící skupině pouze oprávnění číst a spouštět a poskytuje všem ostatním přístup pro čtení.

> [!NOTE]
> Pokud vaše aplikace autorizuje přístup pomocí Azure Active Directory (Azure AD), ujistěte se, že se k objektu zabezpečení, který vaše aplikace používá k autorizaci přístupu, přiřadila [role vlastníka dat objektu BLOB úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Pokud se chcete dozvědět víc o tom, jak se používají oprávnění seznamu ACL, a důsledky jejich změny, přečtěte si téma [řízení přístupu v Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    PathPermissions permissions = new PathPermissions()

      .group(new RolePermissions().execute(true).read(true))
      .owner(new RolePermissions().execute(true).read(true).write(true))
      .other(new RolePermissions().read(true));

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře

Nejprve vytvořte odkaz na soubor v cílovém adresáři vytvořením instance třídy **DataLakeFileClient** . Nahrajte soubor voláním metody **DataLakeFileClient. Append** . Ujistěte se, že jste dokončí nahrávání voláním metody **DataLakeFileClient. FlushAsync** .

Tento příklad nahraje textový soubor do adresáře s názvem `my-directory`.

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new FileInputStream(file);

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

## <a name="manage-a-file-acl"></a>Správa seznamu ACL souboru

Tento příklad načte a potom nastaví seznam řízení přístupu pro soubor s názvem `upload-file.txt`. Tento příklad uděluje vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, dává vlastnící skupině pouze oprávnění číst a spouštět a poskytuje všem ostatním přístup pro čtení.

> [!NOTE]
> Pokud vaše aplikace autorizuje přístup pomocí Azure Active Directory (Azure AD), ujistěte se, že se k objektu zabezpečení, který vaše aplikace používá k autorizaci přístupu, přiřadila [role vlastníka dat objektu BLOB úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Pokud se chcete dozvědět víc o tom, jak se používají oprávnění seznamu ACL, a důsledky jejich změny, přečtěte si téma [řízení přístupu v Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```java
static public void ManageFileACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    PathAccessControl fileAccessControl =
        fileClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = fileAccessControl.getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
           
    PathPermissions permissions = new PathPermissions()

        .group(new RolePermissions().execute(true).read(true))
        .owner(new RolePermissions().execute(true).read(true).write(true))
        .other(new RolePermissions().read(false));

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

## <a name="download-from-a-directory"></a>Stažení z adresáře

Nejprve vytvořte instanci **DataLakeFileClient** , která představuje soubor, který chcete stáhnout. K načtení souboru použijte metodu **DataLakeFileClient. Read** . K uložení bajtů z datového proudu do souboru použijte libovolné rozhraní API pro zpracování souborů .NET. 

Ujistěte se, že jste stahování dokončili voláním metody **DataLakeFileClient. Flush** .

```java
static public void DownloadFile(DataLakeFileSystemClient fileSystemClient)
    throws FileNotFoundException, java.io.IOException{

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    File file = new File("C:\\downloadedFile.txt");

    OutputStream targetStream = new FileOutputStream(file);

    fileClient.read(targetStream);

    targetStream.close();
      
}

```

## <a name="list-directory-contents"></a>Výpis obsahu adresáře

Tento příklad vytiskne názvy jednotlivých souborů, které jsou umístěny v adresáři s názvem `my-directory`.

```java
static public void ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient){
        
    ListPathsOptions options = new ListPathsOptions();
    options.setPath("my-directory");
     
    PagedIterable<PathItem> pagedIterable = 
    fileSystemClient.listPaths(options, null);

    java.util.Iterator<PathItem> iterator = pagedIterable.iterator();
       
    PathItem item = iterator.next();

    while (item != null)
    {
        System.out.println(item.getName());


        if (!iterator.hasNext())
        {
            break;
        }
            
        item = iterator.next();
    }

}
```

## <a name="see-also"></a>Další informace najdete v tématech

* [Referenční dokumentace k rozhraní API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.0-preview.6/index.html)
* [Balíček (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake/12.0.0-preview.6/jar)
* [Ukázky](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Mapování Gen1 na Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-java/issues)