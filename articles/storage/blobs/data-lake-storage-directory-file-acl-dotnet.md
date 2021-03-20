---
title: Použití rozhraní .NET ke správě dat v Azure Data Lake Storage Gen2
description: Pomocí klientské knihovny Azure Storage pro .NET můžete spravovat adresáře a soubory v účtech úložiště s povoleným hierarchickým oborem názvů.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 52e993a22a512a94c8b5b8b050205db0c4ce0b1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100650316"
---
# <a name="use-net-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Použití rozhraní .NET ke správě adresářů a souborů v Azure Data Lake Storage Gen2

V tomto článku se dozvíte, jak pomocí .NET vytvářet a spravovat adresáře a soubory v účtech úložiště, které mají hierarchický obor názvů.

Další informace o tom, jak získat, nastavit a aktualizovat seznamy řízení přístupu (ACL) adresářů a souborů, najdete v tématu [použití rozhraní .NET ke správě seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-acl-dotnet.md).

[Balíček (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  Reference k rozhraní [API](/dotnet/api/azure.storage.files.datalake)  |  Mapování Gen1 na [Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

- Účet úložiště s povoleným hierarchickým oborem názvů. Pokud ho chcete vytvořit, postupujte podle [těchto](create-data-lake-storage-account.md) pokynů.

## <a name="set-up-your-project"></a>Nastavení projektu

Začněte tím, že nainstalujete balíček NuGet pro [Azure. Storage. Files. datalake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) .

Další informace o tom, jak nainstalovat balíčky NuGet, najdete v tématu [instalace a Správa balíčků v aplikaci Visual Studio pomocí Správce balíčků NuGet](/nuget/consume-packages/install-use-packages-visual-studio).

Pak přidejte tyto příkazy using do horní části souboru kódu.

```csharp
using Azure;
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;

```

## <a name="connect-to-the-account"></a>Připojit k účtu

Pokud chcete používat fragmenty kódu v tomto článku, budete muset vytvořit instanci [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) , která představuje účet úložiště. 

### <a name="connect-by-using-an-account-key"></a>Připojení pomocí klíče účtu

Toto je nejjednodušší způsob, jak se připojit k účtu. 

Tento příklad vytvoří instanci [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) pomocí klíče účtu.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Připojení pomocí Azure Active Directory (Azure AD)

K ověření vaší aplikace v Azure AD můžete použít [klientskou knihovnu Azure identity pro .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

Tento příklad vytvoří instanci [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) pomocí ID klienta, tajného klíče klienta a ID tenanta.  Pokud chcete získat tyto hodnoty, přečtěte si téma [získání tokenu z Azure AD pro autorizaci žádostí z klientské aplikace](../common/storage-auth-aad-app.md).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Další příklady najdete v dokumentaci ke [klientské knihovně Azure identity pro .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

## <a name="create-a-container"></a>Vytvoření kontejneru

Kontejner funguje jako systém souborů pro vaše soubory. Můžete jej vytvořit zavoláním metody [DataLakeServiceClient. CreateFileSystem](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) .

Tento příklad vytvoří kontejner s názvem `my-file-system` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na adresář voláním metody [DataLakeFileSystemClient. CreateDirectoryAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) .

Tento příklad přidá adresář s názvem `my-directory` do kontejneru a následně přidá podadresář s názvem `my-subdirectory` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Přejmenování nebo přesunutí adresáře

Přejmenujte nebo přesuňte adresář voláním metody [DataLakeDirectoryClient. RenameAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) . Předejte cestu k požadovanému adresáři do parametru. 

Tento příklad přejmenuje podadresář na název `my-subdirectory-renamed` .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_RenameDirectory":::

Tento příklad přesune adresář s názvem `my-subdirectory-renamed` do podadresáře adresáře s názvem `my-directory-2` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Odstranění adresáře

Odstraňte adresář voláním metody [DataLakeDirectoryClient. Delete](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) .

Tento příklad odstraní adresář s názvem `my-directory` .  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře

Nejprve vytvořte odkaz na soubor v cílovém adresáři vytvořením instance třídy [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) . Nahrajte soubor voláním metody [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) . Ujistěte se, že jste dokončí nahrávání voláním metody [DataLakeFileClient. FlushAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) .

Tento příklad nahraje textový soubor do adresáře s názvem `my-directory` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFile":::

> [!TIP]
> Pokud je velikost souboru velká, váš kód bude muset provést více volání do [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Místo toho zvažte použití metody [DataLakeFileClient. UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) . Tímto způsobem můžete nahrát celý soubor v jednom volání.
>
> Příklad najdete v další části.

## <a name="upload-a-large-file-to-a-directory"></a>Nahrání velkého souboru do adresáře

Pro nahrání velkých souborů použijte metodu [DataLakeFileClient. UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) , aniž byste museli provádět více volání metody [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Stažení z adresáře 

Nejprve vytvořte instanci [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) , která představuje soubor, který chcete stáhnout. Použijte metodu [DataLakeFileClient. ReadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync)  a analyzujte návratovou hodnotu pro získání objektu [datového proudu](/dotnet/api/system.io.stream) . K uložení bajtů z datového proudu do souboru použijte libovolné rozhraní API pro zpracování souborů .NET. 

V tomto příkladu se k uložení bajtů do souboru používá [BinaryReader](/dotnet/api/system.io.binaryreader) a [FileStream](/dotnet/api/system.io.filestream) . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DownloadBinaryFromDirectory":::

## <a name="list-directory-contents"></a>Výpis obsahu adresáře

Výpis obsahu adresáře voláním metody [FileSystemClient. GetPathsAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) a následným vytvořením výčtu výsledků.

Tento příklad vytiskne názvy jednotlivých souborů, které jsou umístěny v adresáři s názvem `my-directory` .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Viz také

- [Referenční dokumentace k rozhraní API](/dotnet/api/azure.storage.files.datalake)
- [Balíček (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [ukázky](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Mapování Gen1 na Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Poskytnout zpětnou vazbu](https://github.com/Azure/azure-sdk-for-net/issues)