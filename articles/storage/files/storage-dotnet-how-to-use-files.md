---
title: Vývoj pro Soubory Azure pomocí .NET | Dokumentace Microsoftu
description: Naučte se vyvíjet aplikace a služby .NET, které používají soubory Azure k ukládání dat.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-csharp
ms.openlocfilehash: e112060db4a44884d3094a939b03ff106ba72e65
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96492195"
---
# <a name="develop-for-azure-files-with-net"></a>Vývoj pro Soubory Azure pomocí .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Seznamte se se základy vývoje aplikací .NET, které používají [soubory Azure](storage-files-introduction.md) k ukládání dat. V tomto článku se dozvíte, jak vytvořit jednoduchou konzolovou aplikaci, která provede následující akce se soubory .NET a Azure:

- Získá obsah souboru.
- Nastavte maximální velikost nebo kvótu pro sdílenou složku.
- Vytvoření sdíleného přístupového podpisu (SAS) pro soubor.
- Zkopírujte soubor do jiného souboru ve stejném účtu úložiště.
- Zkopírujte soubor do objektu blob ve stejném účtu úložiště.
- Vytvoří snímek sdílené složky.
- Obnovte soubor ze snímku sdílené složky.
- Pro řešení potíží použijte Azure Storage metriky.

Další informace o službě soubory Azure najdete v tématu [co je Azure Files?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Vysvětlení rozhraní API .NET

Soubory Azure poskytuje dva přístupy ke klientským aplikacím: protokol SMB (Server Message Block) a REST. V rozhraní .NET rozhraní `System.IO` `Azure.Storage.Files.Shares` API a tyto přístupy abstraktní.

Rozhraní API | Kdy je použít | Poznámky
----|-------------|------
[System.IO](/dotnet/api/system.io) | Vaše aplikace: <ul><li>Musí číst/zapisovat soubory pomocí protokolu SMB.</li><li>Je spuštěná v zařízení, které má prostřednictvím portu 445 přístup k vašemu účtu služby Soubory Azure.</li><li>Nemusí spravovat žádná nastavení pro správu sdílené složky.</li></ul> | I/O souborů implementovaných pomocí služby Azure Files přes SMB je obvykle stejné jako u vstupu a výstupu pomocí libovolné síťové sdílené složky nebo místního úložného zařízení. Úvod do řady funkcí v rozhraní .NET, včetně vstupně-výstupních operací se soubory, najdete v kurzu [konzolové aplikace](/dotnet/csharp/tutorials/console-teleprompter) .
[Azure. Storage. Files. shares](/dotnet/api/azure.storage.files.shares) | Vaše aplikace: <ul><li>K souborům Azure nelze přistupovat pomocí protokolu SMB na portu 445 z důvodu omezení brány firewall nebo poskytovatele internetových služeb.</li><li>Vyžaduje funkce pro správu, jako je například možnost nastavit kvótu sdílené složky nebo vytvořit sdílený přístupový podpis.</li></ul> | Tento článek ukazuje použití `Azure.Storage.Files.Shares` pro vstupně-výstupní operace se soubory pomocí REST místo SMB a správy sdílené složky.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Vytvoření konzolové aplikace a získání sestavení

Knihovnu klienta soubory Azure můžete použít v jakémkoli typu aplikace .NET. Mezi tyto aplikace patří cloudové, webové, desktopové a mobilní aplikace Azure. V této příručce vytvoříme konzolovou aplikaci pro zjednodušení.

V sadě Visual Studio vytvořte novou konzolovou aplikaci pro Windows. Následující kroky ukazují, jak vytvořit konzolovou aplikaci v aplikaci Visual Studio 2019. Kroky u ostatních verzí sady Visual Studio jsou podobné.

1. Spusťte aplikaci Visual Studio a vyberte možnost **vytvořit nový projekt**.
1. V možnosti **vytvořit nový projekt** zvolte **Konzolová aplikace (.NET Framework)** pro C# a pak vyberte **Další**.
1. V části **Konfigurace nového projektu** zadejte název aplikace a vyberte **vytvořit**.

Do `Program` třídy v souboru *program. cs* přidejte všechny příklady kódu v tomto článku.

## <a name="use-nuget-to-install-the-required-packages"></a>Použití balíčku NuGet k instalaci požadovaných balíčků

Podívejte se na tyto balíčky v projektu:

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

- [Knihovna Azure Core pro .NET](https://www.nuget.org/packages/Azure.Core/): Tento balíček je implementací kanálu klienta Azure.
- [Azure Storage BLOB Klientská knihovna pro .NET](https://www.nuget.org/packages/Azure.Storage.Blobs/): Tento balíček poskytuje programový přístup k prostředkům BLOB v účtu úložiště.
- [Klientská knihovna pro soubory Azure Storage Files pro .NET](https://www.nuget.org/packages/Azure.Storage.Files.Shares/): Tento balíček poskytuje programový přístup k prostředkům souborů ve vašem účtu úložiště.
- [Systémová Configuration Manager knihovna pro .NET](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/): Tento balíček poskytuje třídu, která ukládá a načítá hodnoty v konfiguračním souboru.

K získání balíčků můžete použít NuGet. Postupujte takto:

1. V **Průzkumník řešení** klikněte pravým tlačítkem myši na projekt a vyberte možnost **Spravovat balíčky NuGet**.
1. V **nástroji Správce balíčků NuGet** vyberte **Procházet**. Pak vyhledejte a zvolte **Azure. Core** a pak vyberte **nainstalovat**.

   Tento krok nainstaluje balíček a jeho závislosti.

1. Vyhledejte a nainstalujte tyto balíčky:

   - **Azure. Storage. BLOBs**
   - **Azure. Storage. Files. shares**
   - **System.Configuration.ConfigurationManager**

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [Microsoft Azure Storage společnou knihovnu pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): Tento balíček poskytuje programový přístup k běžným prostředkům v účtu úložiště.
- [Microsoft Azure Storage knihovna objektů BLOB pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/): Tento balíček poskytuje programový přístup k prostředkům BLOB v účtu úložiště.
- [Microsoft Azure Storage knihovna souborů pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/): Tento balíček poskytuje programový přístup k prostředkům souborů ve vašem účtu úložiště.
- [Microsoft Azure Configuration Manager Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): Tento balíček poskytuje třídu pro analýzu připojovacího řetězce v konfiguračním souboru bez ohledu na to, kde je aplikace spuštěná.

K získání balíčků můžete použít NuGet. Postupujte takto:

1. V **Průzkumník řešení** klikněte pravým tlačítkem myši na projekt a vyberte možnost **Spravovat balíčky NuGet**.
1. V **nástroji Správce balíčků NuGet** vyberte **Procházet**. Pak vyhledejte a zvolte **Microsoft. Azure. Storage. blob** a pak vyberte **nainstalovat**.

   Tento krok nainstaluje balíček a jeho závislosti.
1. Vyhledejte a nainstalujte tyto balíčky:

   - **Microsoft. Azure. Storage. Common**
   - **Microsoft. Azure. Storage. File**
   - **Microsoft.Azure.ConfigurationManager**

---

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Uložení přihlašovacích údajů účtu úložiště do souboru App.config

Potom uložte své přihlašovací údaje do souboru *App.config* projektu. V **Průzkumník řešení** dvakrát klikněte `App.config` a upravte soubor tak, aby byl podobný následujícímu příkladu.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Nahraďte `myaccount` názvem svého účtu úložiště a `mykey` klíčem účtu úložiště.

:::code language="xml" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/app.config" highlight="5,6,7":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Nahraďte `myaccount` názvem svého účtu úložiště a `StorageAccountKeyEndingIn==` klíčem účtu úložiště.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString"
      value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
  </appSettings>
</configuration>
```

---

> [!NOTE]
> Emulátor úložiště Azurite v současné době nepodporuje soubory Azure. Aby váš připojovací řetězec mohl pracovat se soubory Azure, musí cílit na účet služby Azure Storage v cloudu.

## <a name="add-using-directives"></a>Přidání direktiv using

V **Průzkumník řešení** otevřete soubor *program. cs* a na začátek souboru přidejte následující direktivy using.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

---

## <a name="access-the-file-share-programmatically"></a>Programový přístup ke sdílené složce

Do souboru *program. cs* přidejte následující kód pro přístup ke sdílené složce programově.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Následující metoda vytvoří sdílení souborů, pokud ještě neexistuje. Metoda začíná vytvořením objektu [ShareClient](/dotnet/api/azure.storage.files.shares.shareclient) z připojovacího řetězce. Ukázka pak pokusí stáhnout soubor, který jsme vytvořili dříve. Zavolejte tuto metodu z `Main()` .

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShare":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Dále přidejte následující obsah do `Main()` metody po výše uvedeném kódu, aby bylo možné načíst připojovací řetězec. Tento kód získá odkaz na soubor, který jsme vytvořili dříve, a vytvoří výstup svého obsahu.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Výstup zobrazíte spuštěním konzolové aplikace.

---

## <a name="set-the-maximum-size-for-a-file-share"></a>Nastavení maximální velikosti sdílené složky

Od verze 5. x klientské knihovny souborů Azure můžete nastavit kvótu (maximální velikost) pro sdílenou složku. Můžete se taky podívat, kolik data je aktuálně uloženo ve sdílené složce.

Nastavení kvóty pro sdílenou složku omezí celkovou velikost souborů uložených ve sdílené složce. Pokud celková velikost souborů ve sdílené složce překročí kvótu, klienti nemůžou zvětšit velikost existujících souborů. Klienti také nemůžou vytvářet nové soubory, pokud tyto soubory nejsou prázdné.

Dole uvedený příklad ukazuje, jak můžete zkontrolovat aktuální využití sdílené složky a jak nastavit kvótu pro sdílenou složku.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_SetMaxShareSize":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

---

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Vygenerování sdíleného přístupového podpisu pro soubor nebo sdílenou složku

Od verze 5. x klientské knihovny souborů Azure můžete vygenerovat sdílený přístupový podpis (SAS) pro sdílenou složku nebo pro jednotlivé soubory.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Následující příklad metody vrátí SAS pro soubor v zadané sdílené složce.

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_GetFileSasUri":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Můžete také vytvořit uložené zásady přístupu pro sdílenou složku pro správu podpisů sdíleného přístupu. Doporučujeme vytvořit zásadu uloženého přístupu, protože umožňuje odvolat SAS, pokud dojde k ohrožení zabezpečení. Následující příklad vytvoří uloženou zásadu přístupu pro sdílenou složku. Tento příklad používá tuto zásadu k poskytnutí omezení pro SAS v souboru ve sdílené složce.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

---

Další informace o vytváření a používání sdílených přístupových podpisů najdete v tématu [Jak funguje sdílený přístupový podpis](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works).

## <a name="copy-files"></a>Kopírování souborů

Od verze 5. x klientské knihovny souborů Azure můžete zkopírovat soubor do jiného souboru, soubor do objektu BLOB nebo objekt blob do souboru.

AzCopy můžete použít také ke kopírování jednoho souboru do jiného nebo ke zkopírování objektu blob do souboru nebo jiným způsobem. Viz Začínáme [s AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Pokud kopírujete objekt blob do souboru nebo soubor do objektu blob, musíte použít sdílený přístupový podpis (SAS) k ověření přístupu ze zdrojovému objektu, a to i když kopírujete v rámci jednoho účtu úložiště.

### <a name="copy-a-file-to-another-file"></a>Kopírování souboru do jiného souboru

V následujícím příkladu se zkopíruje soubor do jiného souboru ve stejné sdílené složce. K provedení kopírování můžete použít [ověřování pomocí sdíleného klíče](/rest/api/storageservices/authorize-with-shared-key) , protože tato operace kopíruje soubory v rámci stejného účtu úložiště.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFile":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

---

### <a name="copy-a-file-to-a-blob"></a>Kopírování souboru do objektu blob

V následujícím příkladu se vytvoří soubor a zkopíruje se do objektu blob ve stejném účtu úložiště. V příkladu se pro zdrojový soubor vytvoří SAS, který služba během operace kopírování použije k autorizaci přístupu ke zdrojovému souboru.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFileToBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

---

Stejným způsobem můžete kopírovat objekt blob do souboru. Pokud je zdrojovým objektem objekt blob, vytvořte SAS k autorizaci přístupu k tomuto objektu blob během operace kopírování.

## <a name="share-snapshots"></a>Sdílet snímky

Od verze 8,5 klientské knihovny souborů Azure Files můžete vytvořit snímek sdílené složky. Umožňuje také vypsat nebo procházet snímky sdílené složky a odstranit je. Po vytvoření budou snímky sdílet jen pro čtení.

### <a name="create-share-snapshots"></a>Vytvoření snímků sdílené složky

Následující příklad vytvoří snímek sdílené složky.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShareSnapshot":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

---

### <a name="list-share-snapshots"></a>Výpis snímků sdílené složky

Následující příklad vypíše snímky pro sdílenou složku.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListShareSnapshots":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

---

### <a name="list-files-and-directories-within-share-snapshots"></a>Vypsání souborů a adresářů v rámci snímků sdílené složky

Následující příklad prochází soubory a adresáře v rámci snímků sdílené složky.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListSnapshotContents":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

---

### <a name="restore-file-shares-or-files-from-share-snapshots"></a>Obnovení sdílených složek nebo souborů ze snímků sdílené složky

Pořízení snímku sdílené složky umožňuje obnovení jednotlivých souborů nebo celé sdílené složky.

Soubor můžete ze snímku sdílené složky obnovit zadáním dotazu na snímky sdílené složky nebo sdílenou složku. Pak můžete načíst soubor, který patří do konkrétního snímku sdílené složky. Tuto verzi můžete použít k přímému čtení nebo obnovení souboru.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_RestoreFileFromSnapshot":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

---

### <a name="delete-share-snapshots"></a>Odstranění snímků sdílené složky

Následující příklad odstraní snímek sdílené složky.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_DeleteSnapshot":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

---

## <a name="troubleshoot-azure-files-by-using-metrics"></a>Řešení potíží se soubory Azure pomocí metrik<a name="troubleshooting-azure-files-using-metrics"></a>

Analýza úložiště Azure podporuje metriky pro soubory Azure. S údaji z metriky můžete sledovat žádosti a diagnostikovat potíže.

Metriky pro soubory Azure můžete povolit z [Azure Portal](https://portal.azure.com). Metriky můžete také povolit programově voláním operace [set File Service Properties](/rest/api/storageservices/set-file-service-properties) u REST API nebo některého z jeho analogie v klientské knihovně souborů Azure Files.

Následující příklad kódu ukazuje, jak pomocí klientské knihovny .NET povolit metriky pro soubory Azure.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UseMetrics":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Nejprve `using` do souboru *program. cs* přidejte následující direktivy spolu s těmi, které jste přidali výše:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

I když objekty blob Azure, tabulky Azure a fronty Azure používají sdílený `ServiceProperties` typ v `Microsoft.Azure.Storage.Shared.Protocol` oboru názvů, Azure Files používá vlastní typ, `FileServiceProperties` typ v `Microsoft.Azure.Storage.File.Protocol` oboru názvů. Pro zkompilování tohoto kódu je však nutné odkazovat oba obory názvů z kódu.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

---

Pokud narazíte na nějaké problémy, najdete informace [v tématu řešení potíží se soubory Azure v systému Windows](storage-troubleshoot-windows-file-connection-problems.md).

## <a name="next-steps"></a>Další kroky

Další informace o službě soubory Azure najdete v následujících zdrojích informací:

### <a name="conceptual-articles-and-videos"></a>Koncepční články a videa

- [Soubory Azure: hladký cloudový souborový systém SMB pro Windows a Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [Použití služby Azure Files s Linuxem](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Podpora nástrojů pro úložiště File

- [Začínáme s nástrojem AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
- [Řešení potíží se službou Azure Files ve Windows](./storage-troubleshoot-windows-file-connection-problems.md)

### <a name="reference"></a>Reference

- [Rozhraní API služby Azure Storage pro .NET](/dotnet/api/overview/azure/storage)
- [Rozhraní REST API služby File Service](/rest/api/storageservices/File-Service-REST-API)