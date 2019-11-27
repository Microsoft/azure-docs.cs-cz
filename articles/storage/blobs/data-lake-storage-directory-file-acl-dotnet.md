---
title: Použití Azure .NET pro soubory & seznamů ACL v Azure Data Lake Storage Gen2 (Preview)
description: Pomocí klientské knihovny Azure Storage můžete spravovat adresáře a seznamy řízení přístupu (ACL) souborů a adresářů v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: fb69e0b797243a3403e8899d3f4ef23a9be9451d
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534280"
---
# <a name="use-net-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Použití rozhraní .NET pro soubory & seznamů ACL v Azure Data Lake Storage Gen2 (Preview)

V tomto článku se dozvíte, jak pomocí .NET vytvářet a spravovat adresáře, soubory a oprávnění v účtech úložiště, které mají povolený hierarchický obor názvů (HNS). 

> [!IMPORTANT]
> Balíček NuGet [Azure. Storage. Files. datalake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6) , který je vybraný v tomto článku, je momentálně ve verzi Public Preview.

[Package (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6) | [Samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [API](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Storage.Files.DataLake/12.0.0-preview.6/api/index.html) | [Gen1 na mapování Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [poskytnutí zpětné vazby](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](data-lake-storage-quickstart-create-account.md) pokynů.

## <a name="set-up-your-project"></a>Nastavení projektu

Začněte tím, že nainstalujete balíček NuGet pro [Azure. Storage. Files. datalake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6) .

Další informace o tom, jak nainstalovat balíčky NuGet, najdete v tématu [instalace a Správa balíčků v aplikaci Visual Studio pomocí Správce balíčků NuGet](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio).

Pak přidejte tyto příkazy using do horní části souboru kódu.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Připojit k účtu

Pokud chcete používat fragmenty kódu v tomto článku, budete muset vytvořit instanci **DataLakeServiceClient** , která představuje účet úložiště. Nejjednodušší způsob, jak ho získat, je použít klíč účtu. 

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí klíče účtu.

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

## <a name="create-a-file-system"></a>Vytvoření systému souborů

Systém souborů funguje jako kontejner pro vaše soubory. Můžete jej vytvořit zavoláním metody **FileSystemClient. CreateFileSystemAsync** .

Tento příklad vytvoří systém souborů s názvem `my-file-system`. 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na adresář voláním metody **FileSystemClient. CreateDirectoryAsync** .

Tento příklad přidá do systému souborů adresář s názvem `my-directory` a poté přidá podadresáři s názvem `my-subdirectory`. 

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

Přejmenujte nebo přesuňte adresář voláním metody **DirectoryClient. RenameAsync** . Předejte cestu k požadovanému adresáři do parametru. 

Tento příklad přejmenuje podadresář na název `my-subdirectory-renamed`.

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

Tento příklad přesune adresář s názvem `my-subdirectory-renamed` do podadresáře adresáře s názvem `my-directory-2`. 

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

Odstraňte adresář voláním metody **DirectoryClient. Delete** .

Tento příklad odstraní adresář s názvem `my-directory`.  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Správa seznamu ACL adresáře

Získání seznamu řízení přístupu (ACL) adresáře voláním metody **directoryClient. GetAccessControlAsync** a nastavením seznamu ACL voláním metody **directoryClient. SetAccessControl** .

Tento příklad získá a nastaví seznam řízení přístupu pro adresář s názvem `my-directory`. Řetězec `user::rwx,group::r-x,other::rw-` přidělí oprávnění ke čtení, zápisu a spouštění přiděleného uživateli, přidělí skupině pouze oprávnění číst a spouštět a poskytuje všem ostatním oprávnění ke čtení a zápisu.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.Acl);

    directoryClient.SetAccessControl("user::rwx,group::r-x,other::rw-");

}

```

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře

Nejprve vytvořte odkaz na soubor v cílovém adresáři vytvořením instance třídy **DataLakeFileClient** . Nahrajte soubor voláním metody **DataLakeFileClient. AppendAsync** . Ujistěte se, že jste dokončí nahrávání voláním metody **DataLakeFileClient. FlushAsync** .

Tento příklad nahraje textový soubor do adresáře s názvem `my-directory`.    

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

## <a name="manage-a-file-acl"></a>Správa seznamu ACL souboru

Získání seznamu řízení přístupu (ACL) souboru zavoláním metody **DataLakeFileClient. GetAccessControlAsync** a nastavením seznamu ACL voláním metody ' **klient. SetAccessControl** '.

Tento příklad získá a nastaví seznam řízení přístupu pro soubor s názvem `my-file.txt`. Řetězec `user::rwx,group::r-x,other::rw-` přidělí oprávnění ke čtení, zápisu a spouštění přiděleného uživateli, přidělí skupině pouze oprávnění číst a spouštět a poskytuje všem ostatním oprávnění ke čtení a zápisu.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.Acl);

    fileClient.SetAccessControl("user::rwx,group::r-x,other::rw-");
}
```

## <a name="download-from-a-directory"></a>Stažení z adresáře 

Nejprve vytvořte instanci **DataLakeFileClient** , která představuje soubor, který chcete stáhnout. Použijte metodu **. ReadAsync** a analyzujte návratovou hodnotu pro získání objektu [datového proudu](https://docs.microsoft.com/dotnet/api/system.io.stream) . K uložení bajtů z datového proudu do souboru použijte libovolné rozhraní API pro zpracování souborů .NET. 

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

Výpis obsahu adresáře voláním metody **FileSystemClient. ListPathsAsync** a následným vytvořením výčtu výsledků.

Tento příklad vytiskne názvy jednotlivých souborů, které jsou umístěny v adresáři s názvem `my-directory`.

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.ListPathsAsync("my-directory").GetAsyncEnumerator();

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

* [Referenční dokumentace k rozhraní API](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Storage.Files.DataLake/12.0.0-preview.6/api/index.html)
* [Balíček (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6)
* [Ukázky](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Mapování Gen1 na Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-net/issues)

