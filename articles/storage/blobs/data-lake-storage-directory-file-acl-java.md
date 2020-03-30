---
title: Azure Data Lake Storage Gen2 Java SDK pro soubory & Seznamy ACL
description: Knihovny úložiště Azure pro Jazyk Java slouží ke správě adresářů a seznamů řízení přístupu k souborům a adresářům (ACL) v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 45870dd7d3035b6b49340fd6e8016794088e775a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061558"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Správa adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2 pomocí Javy

Tento článek ukazuje, jak pomocí jazyka Java vytvářet a spravovat adresáře, soubory a oprávnění v účtech úložiště, které mají povolený hierarchický obor názvů (HNS). 

[Balíček (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [Ukázky](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [ROZHRANÍ API odkaz](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html) | [Gen1 na Gen2 mapování](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Poskytnout zpětnou vazbu](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Účet úložiště, který má povolen hierarchický obor názvů (HNS). [Chcete-li](data-lake-storage-quickstart-create-account.md) jej vytvořit, postupujte podle těchto pokynů.

## <a name="set-up-your-project"></a>Nastavení projektu

Chcete-li začít, otevřete [tuto stránku](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) a najděte nejnovější verzi knihovny Java. Potom otevřete soubor *pom.xml* v textovém editoru. Přidejte prvek závislosti, který odkazuje na tuto verzi.

Pokud plánujete ověřit klientskou aplikaci pomocí služby Azure Active Directory (AD), přidejte závislost do klientské knihovny Azure Secret. Viz [Přidání balíčku tajné klientské knihovny do projektu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Dále přidejte tyto příkazy importu do souboru kódu.

```java
import com.azure.core.credential.TokenCredential;
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

## <a name="connect-to-the-account"></a>Připojení k účtu 

Chcete-li použít výstřižky v tomto článku, budete muset vytvořit instanci **DataLakeServiceClient,** která představuje účet úložiště. 

### <a name="connect-by-using-an-account-key"></a>Připojení pomocí klíče účtu

Toto je nejjednodušší způsob připojení k účtu. 

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

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Připojení pomocí Služby Azure Active Directory (Azure AD)

Klientská [knihovna identity Azure pro Jazyk Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) můžete použít k ověření vaší aplikace pomocí Azure AD.

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí ID klienta, tajný klíč klienta a ID klienta.  Pokud chcete získat tyto hodnoty, najdete v článku [Získání tokenu z Azure AD pro autorizaci požadavků z klientské aplikace](../common/storage-auth-aad-app.md).

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> Další příklady najdete v [knihovně klienta identity Azure pro](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) dokumentaci java.


## <a name="create-a-file-system"></a>Vytvoření systému souborů

Systém souborů funguje jako kontejner pro vaše soubory. Můžete vytvořit pomocí volání **Metody DataLakeServiceClient.createFileSystem.**

Tento příklad vytvoří systém `my-file-system`souborů s názvem . 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na adresář voláním metody **DataLakeFileSystemClient.createDirectory.**

Tento příklad přidá `my-directory` adresář s názvem do systému souborů `my-subdirectory`a potom přidá podadresář s názvem . 

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

Přejmenujte nebo přesuňte adresář voláním metody **DataLakeDirectoryClient.rename.** Předá cestu požadovaného adresáře parametr. 

Tento příklad přejmenuje podadresář na `my-subdirectory-renamed`název .

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-subdirectory-renamed");
}
```

Tento příklad přesune `my-subdirectory-renamed` adresář s názvem do podadresáře adresáře s názvem `my-directory-2`. 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Odstranění adresáře

Odstraňte adresář voláním metody **DataLakeDirectoryClient.deleteWithResponse.**

Tento příklad odstraní adresář `my-directory`s názvem .   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>Správa seznamu ACL adresáře

Tento příklad získá a potom nastaví `my-directory`seznam ACL adresáře s názvem . Tento příklad poskytuje vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, poskytuje vlastnící skupině pouze oprávnění ke čtení a spouštění a poskytuje všem ostatním přístup ke čtení.

> [!NOTE]
> Pokud vaše aplikace autorizuje přístup pomocí Služby Azure Active Directory (Azure AD), ujistěte se, že objekt zabezpečení, který vaše aplikace používá k autorizaci přístupu, byl přiřazen [roli vlastníka dat objektu blob úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Další informace o použití oprávnění seznamu ACL a jejich efektech najdete [v tématu Řízení přístupu v azure datovém úložišti.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);
  
    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);
  
    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);
  
    PathPermissions permissions = new PathPermissions();
  
    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře

Nejprve vytvořte odkaz na soubor v cílovém adresáři vytvořením instance třídy **DataLakeFileClient.** Nahrajte soubor voláním metody **DataLakeFileClient.append.** Nezapomeňte dokončit nahrávání voláním metody **DataLakeFileClient.FlushAsync.**

Tento příklad nahraje textový soubor `my-directory`do adresáře s názvem .'

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

> [!TIP]
> Pokud je velikost souboru velká, bude muset váš kód provést více volání metody **DataLakeFileClient.append.** Zvažte použití metody **DataLakeFileClient.uploadFromFile.** Tímto způsobem můžete nahrát celý soubor v jednom hovoru. 
>
> Příklad najdete v další části.

## <a name="upload-a-large-file-to-a-directory"></a>Nahrání velkého souboru do adresáře

Pomocí metody **DataLakeFileClient.uploadFromFile** můžete nahrát velké soubory, aniž byste museli provádět více volání metody **DataLakeFileClient.append.**

```java
static public void UploadFileBulk(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.getFileClient("uploaded-file.txt");

    fileClient.uploadFromFile("C:\\mytestfile.txt");

    }

```


## <a name="manage-a-file-acl"></a>Správa souboru ACL

Tento příklad získá a potom nastaví `upload-file.txt`acl souboru s názvem . Tento příklad poskytuje vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, poskytuje vlastnící skupině pouze oprávnění ke čtení a spouštění a poskytuje všem ostatním přístup ke čtení.

> [!NOTE]
> Pokud vaše aplikace autorizuje přístup pomocí Služby Azure Active Directory (Azure AD), ujistěte se, že objekt zabezpečení, který vaše aplikace používá k autorizaci přístupu, byl přiřazen [roli vlastníka dat objektu blob úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Další informace o použití oprávnění seznamu ACL a jejich efektech najdete [v tématu Řízení přístupu v azure datovém úložišti.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

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
           
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);

    PathPermissions permissions = new PathPermissions();

    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

## <a name="download-from-a-directory"></a>Stažení z adresáře

Nejprve vytvořte instanci **DataLakeFileClient,** která představuje soubor, který chcete stáhnout. Ke čtení souboru použijte metodu **DataLakeFileClient.read.** K uložení bajtů z datového proudu do souboru použijte libovolné rozhraní API pro zpracování souborů .NET. 

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

V tomto příkladu vytiskne názvy každého `my-directory`souboru, který je umístěn v adresáři s názvem .

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

## <a name="see-also"></a>Viz také

* [Referenční dokumentace k rozhraní API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html)
* [Balíček (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [ukázky](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Mapování Gen1 až Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Poskytnout zpětnou vazbu](https://github.com/Azure/azure-sdk-for-java/issues)