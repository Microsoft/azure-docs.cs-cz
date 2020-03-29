---
title: Azure Data Lake Storage Gen2 .NET SDK pro soubory & Seznamy AC
description: Klientská knihovna Azure Storage slouží ke správě adresářů a seznamů řízení přístupu k souborům a adresářům (ACL) v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: b83d0d2d765b60585832f1a3e7c610f05eac075c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061584"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Použití rozhraní .NET ke správě adresářů, souborů a seznamů ACL v azure data lake storage gen2

Tento článek ukazuje, jak pomocí rozhraní .NET vytvářet a spravovat adresáře, soubory a oprávnění v účtech úložiště, které mají povolený hierarchický obor názvů (HNS). 

[Balíček (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [Ukázky](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [ROZHRANÍ API odkaz](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake) | [Gen1 na Gen2 mapování](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [poskytnout zpětnou vazbu](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Účet úložiště, který má povolen hierarchický obor názvů (HNS). [Chcete-li](data-lake-storage-quickstart-create-account.md) jej vytvořit, postupujte podle těchto pokynů.

## <a name="set-up-your-project"></a>Nastavení projektu

Chcete-li začít, nainstalujte balíček [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet.

Další informace o instalaci balíčků NuGet naleznete [v tématu Instalace a správa balíčků v sadě Visual Studio pomocí Správce balíčků NuGet](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio).

Potom přidejte tyto příkazy pomocí do horní části souboru kódu.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Připojení k účtu

Chcete-li použít výstřižky v tomto článku, budete muset vytvořit instanci [DataLakeServiceClient,](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) která představuje účet úložiště. 

### <a name="connect-by-using-an-account-key"></a>Připojení pomocí klíče účtu

Toto je nejjednodušší způsob připojení k účtu. 

Tento příklad vytvoří instanci [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) pomocí klíče účtu.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

### <a name="connect-by-using-azure-active-directory-ad"></a>Připojení pomocí služby Azure Active Directory (AD)

Klientská [knihovna identity Azure pro rozhraní .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) můžete použít k ověření vaší aplikace pomocí Azure AD.

Tento příklad vytvoří instanci [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) pomocí ID klienta, tajný klíč klienta a ID klienta.  Pokud chcete získat tyto hodnoty, najdete v článku [Získání tokenu z Azure AD pro autorizaci požadavků z klientské aplikace](../common/storage-auth-aad-app.md).

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

```

> [!NOTE]
> Další příklady najdete v [knihovně klienta identity Azure pro dokumentaci .NET..](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)

## <a name="create-a-file-system"></a>Vytvoření systému souborů

Systém souborů funguje jako kontejner pro vaše soubory. Můžete vytvořit pomocí volání [Metody DataLakeServiceClient.CreateFileSystem.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync)

Tento příklad vytvoří systém `my-file-system`souborů s názvem . 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na adresář voláním metody [DataLakeFileSystemClient.CreateDirectoryAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync)

Tento příklad přidá `my-directory` adresář s názvem do systému souborů `my-subdirectory`a potom přidá podadresář s názvem . 

```cs
public async Task<DataLakeDirectoryClient> CreateDirectory
    (DataLakeServiceClient serviceClient, string fileSystemName)
{
    DataLakeFileSystemClient fileSystemClient =
        serviceClient.GetFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        await fileSystemClient.CreateDirectoryAsync("my-directory");

    return await directoryClient.CreateSubDirectoryAsync("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Přejmenování nebo přesunutí adresáře

Přejmenujte nebo přesuňte adresář voláním metody [DataLakeDirectoryClient.RenameAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) Předá cestu požadovaného adresáře parametr. 

Tento příklad přejmenuje podadresář na `my-subdirectory-renamed`název .

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

Tento příklad přesune `my-subdirectory-renamed` adresář s názvem do podadresáře adresáře s názvem `my-directory-2`. 

```cs
public async Task<DataLakeDirectoryClient> MoveDirectory
    (DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
            fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory-renamed");

    return await directoryClient.RenameAsync("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Odstranění adresáře

Odstraňte adresář voláním metody [DataLakeDirectoryClient.Delete.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete)

Tento příklad odstraní adresář `my-directory`s názvem .  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Správa seznamu ACL adresáře

Získejte seznam řízení přístupu (ACL) adresáře voláním metody [DataLakeDirectoryClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) a nastavte seznam ACL voláním metody [DataLakeDirectoryClient.SetAccessControlList.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist)

> [!NOTE]
> Pokud vaše aplikace autorizuje přístup pomocí Služby Azure Active Directory (Azure AD), ujistěte se, že objekt zabezpečení, který vaše aplikace používá k autorizaci přístupu, byl přiřazen [roli vlastníka dat objektu blob úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Další informace o použití oprávnění seznamu ACL a jejich efektech najdete [v tématu Řízení přístupu v azure datovém úložišti.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) 

Tento příklad získá a nastaví seznam `my-directory`ACL adresáře s názvem . Řetězec `user::rwx,group::r-x,other::rw-` poskytuje vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, poskytuje vlastnící skupině pouze oprávnění ke čtení a spouštění a uděluje všem ostatním oprávnění ke čtení a zápisu.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    directoryClient.SetAccessControlList(accessControlList);

}

```

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře

Nejprve vytvořte odkaz na soubor v cílovém adresáři vytvořením instance třídy [DataLakeFileClient.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) Nahrajte soubor voláním metody [DataLakeFileClient.AppendAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) Nezapomeňte dokončit nahrávání voláním metody [DataLakeFileClient.FlushAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync)

Tento příklad odešle textový soubor `my-directory`do adresáře s názvem .    

```cs
public async Task UploadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = await directoryClient.CreateFileAsync("uploaded-file.txt");

    FileStream fileStream = 
        File.OpenRead("C:\\file-to-upload.txt");

    long fileSize = fileStream.Length;

    await fileClient.AppendAsync(fileStream, offset: 0);

    await fileClient.FlushAsync(position: fileSize);

}
```

> [!TIP]
> Pokud je velikost souboru velká, bude muset váš kód provést více volání [DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Zvažte použití metody [DataLakeFileClient.UploadAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) Tímto způsobem můžete nahrát celý soubor v jednom hovoru. 
>
> Příklad najdete v další části.

## <a name="upload-a-large-file-to-a-directory"></a>Nahrání velkého souboru do adresáře

Pomocí metody [DataLakeFileClient.UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) nahrajte velké soubory, aniž byste museli provádět více volání metody [DataLakeFileClient.AppendAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)

```cs
public async Task UploadFileBulk(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.GetFileClient("uploaded-file.txt");

    FileStream fileStream =
        File.OpenRead("C:\\file-to-upload.txt");

    await fileClient.UploadAsync(fileStream);

}

```

## <a name="manage-a-file-acl"></a>Správa souboru ACL

Získejte seznam řízení přístupu (ACL) souboru voláním metody [DataLakeFileClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) a nastavte seznam ACL voláním metody [DataLakeFileClient.SetAccessControlList.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist)

> [!NOTE]
> Pokud vaše aplikace autorizuje přístup pomocí Služby Azure Active Directory (Azure AD), ujistěte se, že objekt zabezpečení, který vaše aplikace používá k autorizaci přístupu, byl přiřazen [roli vlastníka dat objektu blob úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Další informace o použití oprávnění seznamu ACL a jejich efektech najdete [v tématu Řízení přístupu v azure datovém úložišti.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) 

Tento příklad získá a nastaví aCL souboru s názvem `my-file.txt`. Řetězec `user::rwx,group::r-x,other::rw-` poskytuje vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, poskytuje vlastnící skupině pouze oprávnění ke čtení a spouštění a uděluje všem ostatním oprávnění ke čtení a zápisu.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    fileClient.SetAccessControlList(accessControlList);
}
```

## <a name="download-from-a-directory"></a>Stažení z adresáře 

Nejprve vytvořte instanci [DataLakeFileClient,](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) která představuje soubor, který chcete stáhnout. Použijte metodu [DataLakeFileClient.ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) a analyzujte vrácenou hodnotu k získání objektu [Stream.](https://docs.microsoft.com/dotnet/api/system.io.stream) K uložení bajtů z datového proudu do souboru použijte libovolné rozhraní API pro zpracování souborů .NET. 

Tento příklad používá [BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) a [FileStream](https://docs.microsoft.com/dotnet/api/system.io.filestream) pro uložení bajtů do souboru. 

```cs
public async Task DownloadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("my-image.png");

    Response<FileDownloadInfo> downloadResponse = await fileClient.ReadAsync();

    BinaryReader reader = new BinaryReader(downloadResponse.Value.Content);

    FileStream fileStream = 
        File.OpenWrite("C:\\my-image-downloaded.png");

    int bufferSize = 4096;

    byte[] buffer = new byte[bufferSize];

    int count;

    while ((count = reader.Read(buffer, 0, buffer.Length)) != 0)
    {
        fileStream.Write(buffer, 0, count);
    }

    await fileStream.FlushAsync();

    fileStream.Close();
}
```

## <a name="list-directory-contents"></a>Výpis obsahu adresáře

Seznam obsahu adresáře voláním metody [FileSystemClient.GetPathsAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) a následným výčetm výsledků.

V tomto příkladu vytiskne názvy každého `my-directory`souboru, který je umístěn v adresáři s názvem .

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.GetPathsAsync("my-directory").GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    PathItem item = enumerator.Current;

    while (item != null)
    {
        Console.WriteLine(item.Name);

        if (!await enumerator.MoveNextAsync())
        {
            break;
        }
                
        item = enumerator.Current;
    }

}
```

## <a name="see-also"></a>Viz také

* [Referenční dokumentace k rozhraní API](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)
* [Balíček (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [ukázky](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Mapování Gen1 až Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Poskytnout zpětnou vazbu](https://github.com/Azure/azure-sdk-for-net/issues)

