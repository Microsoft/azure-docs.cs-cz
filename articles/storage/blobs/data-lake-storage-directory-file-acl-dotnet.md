---
title: Azure Data Lake Storage Gen2 .NET SDK pro soubory & seznamy ACL
description: Pomocí klientské knihovny Azure Storage můžete spravovat adresáře a seznamy řízení přístupu (ACL) souborů a adresářů v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
author: normesta
ms.service: storage
ms.date: 08/26/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 6533809be614210a88bfa605e24209d094bb679d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89019296"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Správa adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2 pomocí .NET

V tomto článku se dozvíte, jak pomocí .NET vytvářet a spravovat adresáře, soubory a oprávnění v účtech úložiště, které mají povolený hierarchický obor názvů (HNS). 

[Balíček (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  Reference k rozhraní [API](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)  |  Mapování Gen1 na [Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](data-lake-storage-quickstart-create-account.md) pokynů.

## <a name="set-up-your-project"></a>Nastavení projektu

Začněte tím, že nainstalujete balíček NuGet pro [Azure. Storage. Files. datalake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) .

Další informace o tom, jak nainstalovat balíčky NuGet, najdete v tématu [instalace a Správa balíčků v aplikaci Visual Studio pomocí Správce balíčků NuGet](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio).

Pak přidejte tyto příkazy using do horní části souboru kódu.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Připojit k účtu

Pokud chcete používat fragmenty kódu v tomto článku, budete muset vytvořit instanci [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) , která představuje účet úložiště. 

### <a name="connect-by-using-an-account-key"></a>Připojení pomocí klíče účtu

Toto je nejjednodušší způsob, jak se připojit k účtu. 

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Připojení pomocí Azure Active Directory (AD)

K ověření vaší aplikace v Azure AD můžete použít [klientskou knihovnu Azure identity pro .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

Tento příklad vytvoří instanci [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) pomocí ID klienta, tajného klíče klienta a ID tenanta.  Pokud chcete získat tyto hodnoty, přečtěte si téma [získání tokenu z Azure AD pro autorizaci žádostí z klientské aplikace](../common/storage-auth-aad-app.md).

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
> Další příklady najdete v dokumentaci ke [klientské knihovně Azure identity pro .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

## <a name="create-a-container"></a>Vytvoření kontejneru

Kontejner funguje jako systém souborů pro vaše soubory. Můžete jej vytvořit zavoláním metody [DataLakeServiceClient. CreateFileSystem](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) .

Tento příklad vytvoří kontejner s názvem `my-file-system` . 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na adresář voláním metody [DataLakeFileSystemClient. CreateDirectoryAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) .

Tento příklad přidá adresář s názvem `my-directory` do kontejneru a následně přidá podadresář s názvem `my-subdirectory` . 

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

Přejmenujte nebo přesuňte adresář voláním metody [DataLakeDirectoryClient. RenameAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) . Předejte cestu k požadovanému adresáři do parametru. 

Tento příklad přejmenuje podadresář na název `my-subdirectory-renamed` .

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

Tento příklad přesune adresář s názvem `my-subdirectory-renamed` do podadresáře adresáře s názvem `my-directory-2` . 

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

Odstraňte adresář voláním metody [DataLakeDirectoryClient. Delete](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) .

Tento příklad odstraní adresář s názvem `my-directory` .  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Správa seznamu ACL adresáře

Získání seznamu řízení přístupu (ACL) adresáře voláním metody [DataLakeDirectoryClient. GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) a nastavením seznamu ACL voláním metody [DataLakeDirectoryClient. SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

> [!NOTE]
> Pokud vaše aplikace autorizuje přístup pomocí Azure Active Directory (Azure AD), ujistěte se, že se k objektu zabezpečení, který vaše aplikace používá k autorizaci přístupu, přiřadila [role vlastníka dat objektu BLOB úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Pokud se chcete dozvědět víc o tom, jak se používají oprávnění seznamu ACL, a důsledky jejich změny, přečtěte si téma  [řízení přístupu v Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Tento příklad načte a nastaví seznam řízení přístupu k adresáři s názvem `my-directory` . Řetězec přiřadí `user::rwx,group::r-x,other::rw-` vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, dává vlastnící skupině pouze oprávnění číst a spouštět a poskytuje všem ostatním oprávnění ke čtení a zápisu.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    foreach (var item in directoryAccessControl.AccessControlList)
    {
        Console.WriteLine(item.ToString());
    }


    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    directoryClient.SetAccessControlList(accessControlList);

}

```

Můžete také získat a nastavit seznam ACL kořenového adresáře kontejneru. Chcete-li získat kořenový adresář, předejte prázdný řetězec ( `""` ) do metody [DataLakeFileSystemClient. GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) .

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře

Nejprve vytvořte odkaz na soubor v cílovém adresáři vytvořením instance třídy [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) . Nahrajte soubor voláním metody [DataLakeFileClient. AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) . Ujistěte se, že jste dokončí nahrávání voláním metody [DataLakeFileClient. FlushAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) .

Tento příklad nahraje textový soubor do adresáře s názvem `my-directory` .    

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
> Pokud je velikost souboru velká, váš kód bude muset provést více volání do [DataLakeFileClient. AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Místo toho zvažte použití metody [DataLakeFileClient. UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) . Tímto způsobem můžete nahrát celý soubor v jednom volání. 
>
> Příklad najdete v další části.

## <a name="upload-a-large-file-to-a-directory"></a>Nahrání velkého souboru do adresáře

Pro nahrání velkých souborů použijte metodu [DataLakeFileClient. UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) , aniž byste museli provádět více volání metody [DataLakeFileClient. AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) .

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

## <a name="manage-a-file-acl"></a>Správa seznamu ACL souboru

Získání seznamu řízení přístupu (ACL) souboru zavoláním metody [DataLakeFileClient. GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) a nastavením seznamu ACL voláním metody [DataLakeFileClient. SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) .

> [!NOTE]
> Pokud vaše aplikace autorizuje přístup pomocí Azure Active Directory (Azure AD), ujistěte se, že se k objektu zabezpečení, který vaše aplikace používá k autorizaci přístupu, přiřadila [role vlastníka dat objektu BLOB úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Pokud se chcete dozvědět víc o tom, jak se používají oprávnění seznamu ACL, a důsledky jejich změny, přečtěte si téma  [řízení přístupu v Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Tento příklad získá a nastaví seznam řízení přístupu k souboru s názvem `my-file.txt` . Řetězec přiřadí `user::rwx,group::r-x,other::rw-` vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, dává vlastnící skupině pouze oprávnění číst a spouštět a poskytuje všem ostatním oprávnění ke čtení a zápisu.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    foreach (var item in FileAccessControl.AccessControlList)
    {
        Console.WriteLine(item.ToString());
    }

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    fileClient.SetAccessControlList(accessControlList);
}
```

## <a name="download-from-a-directory"></a>Stažení z adresáře 

Nejprve vytvořte instanci [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) , která představuje soubor, který chcete stáhnout. Použijte metodu [DataLakeFileClient. ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync)  a analyzujte návratovou hodnotu pro získání objektu [datového proudu](https://docs.microsoft.com/dotnet/api/system.io.stream) . K uložení bajtů z datového proudu do souboru použijte libovolné rozhraní API pro zpracování souborů .NET. 

V tomto příkladu se k uložení bajtů do souboru používá [BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) a [FileStream](https://docs.microsoft.com/dotnet/api/system.io.filestream) . 

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

Výpis obsahu adresáře voláním metody [FileSystemClient. GetPathsAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) a následným vytvořením výčtu výsledků.

Tento příklad vytiskne názvy jednotlivých souborů, které jsou umístěny v adresáři s názvem `my-directory` .

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

## <a name="set-an-acl-recursively-preview"></a>Rekurzivní nastavení seznamu ACL (Preview)

Seznamy ACL můžete přidat, aktualizovat a odebrat rekurzivně na existujících podřízených položkách nadřazeného adresáře bez nutnosti provádět tyto změny jednotlivě pro každou podřízenou položku. Další informace najdete v tématu [rekurzivní nastavení seznamů řízení přístupu (ACL) pro Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Viz také

* [Referenční dokumentace k rozhraní API](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)
* [Balíček (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [ukázky](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Mapování Gen1 na Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-net/issues)

