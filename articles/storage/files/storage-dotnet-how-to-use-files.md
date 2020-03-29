---
title: Vývoj pro Soubory Azure pomocí .NET | Dokumentace Microsoftu
description: Zjistěte, jak vyvíjet aplikace a služby .NET, které používají Soubory Azure k ukládání dat souborů.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4d8be13a75e276d5be6ec71141a13f95601869f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301433"
---
# <a name="develop-for-azure-files-with-net"></a>Vývoj pro Soubory Azure pomocí .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Tento kurz ukazuje základy používání .NET k vývoji aplikací a služeb, které používají [Soubory Azure](storage-files-introduction.md) k ukládání dat souborů. Tento kurz vytvoří jednoduchou konzolovou aplikaci pro provádění základních akcí s rozhraními .NET a Soubory Azure:

* Získejte obsah souboru.
* Nastavte maximální velikost nebo *kvótu* pro sdílenou složku.
* Vytvořte sdílený přístupový podpis (klíč SAS) pro soubor, který používá uložené zásady přístupu definované ve sdílené složce.
* Zkopírujte soubor do jiného souboru ve stejném účtu úložiště.
* Zkopírujte soubor do objektu blob ve stejném účtu úložiště.
* K řešení potíží použijte metriky úložiště Azure.

Další informace o Souborech Azure najdete v tématu [Co je soubory Azure?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Vysvětlení rozhraní API .NET

Soubory Azure poskytuje dva přístupy ke klientským aplikacím: protokol SMB (Server Message Block) a REST. V rámci `System.IO` .NET `WindowsAzure.Storage` a rozhraní API abstraktní tyto přístupy.

rozhraní API | Kdy je použít | Poznámky
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | Vaše aplikace: <ul><li>Potřebuje číst/zapisovat soubory pomocí Protokolu SMB.</li><li>Je spuštěná v zařízení, které má prostřednictvím portu 445 přístup k vašemu účtu služby Soubory Azure.</li><li>Nemusí spravovat žádná nastavení pro správu sdílené složky.</li></ul> | Vstupně-no sypimplementovaný vstupně-off implementovaných soubory Azure přes SMB je obecně stejný jako vstupně-v., s jakoukoli sdílenou síťovou složku nebo místní paměťové zařízení. Úvod k řadě funkcí v rozhraní .NET, včetně vstupně-videa souboru, naleznete v kurzu [konzolové aplikace.](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter)
[Soubor Microsoft.Azure.Storage.File](/dotnet/api/overview/azure/storage?view=azure-dotnet#version-11x) | Vaše aplikace: <ul><li>Nelze získat přístup k souborům Azure pomocí protokolu SMB na portu 445 z důvodu omezení brány firewall nebo isp</li><li>Vyžaduje funkce pro správu, jako je například možnost nastavit kvótu sdílené složky nebo vytvořit sdílený přístupový podpis.</li></ul> | Tento článek ukazuje použití `Microsoft.Azure.Storage.File` pro vstupně-va souboru pomocí REST namísto SMB a správu sdílené složky.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Vytvoření konzolové aplikace a získání sestavení

V sadě Visual Studio vytvořte novou konzolovou aplikaci pro Windows. Následující kroky ukazují, jak vytvořit konzolovou aplikaci v Sadě Visual Studio 2019. Kroky u ostatních verzí sady Visual Studio jsou podobné.

1. Spusťte Visual Studio a vyberte **Vytvořit nový projekt**.
1. V **okně Vytvořit nový projekt**zvolte Console App **(.NET Framework)** pro C#, a pak vyberte **Další**.
1. V **části Konfigurace nového projektu**zadejte název aplikace a vyberte **Vytvořit**.

Můžete přidat všechny příklady kódu v `Main()` tomto kurzu k metodě souboru konzolové `Program.cs` aplikace.

Klientskou knihovnu Azure Storage můžete použít v libovolném typu aplikace .NET. Mezi tyto typy patří cloudová služba Azure nebo webová aplikace a desktopové a mobilní aplikace. V této příručce použijeme konzolovou aplikaci kvůli zjednodušení.

## <a name="use-nuget-to-install-the-required-packages"></a>Použití balíčku NuGet k instalaci požadovaných balíčků

Chcete-li dokončit tento kurz, postupujte v těchto balíčcích v projektu:

* [Společná knihovna úložiště Microsoft Azure pro rozhraní .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
  
  Tento balíček poskytuje programový přístup ke společným prostředkům ve vašem účtu úložiště.
* [Knihovna objektů blob úložiště Microsoft Azure pro rozhraní .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)

  Tento balíček poskytuje programový přístup k prostředkům objektu blob ve vašem účtu úložiště.
* [Knihovna souborů úložiště Microsoft Azure pro rozhraní .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

  Tento balíček poskytuje programový přístup k prostředkům souborů ve vašem účtu úložiště.
* [Knihovna Správce konfigurace Microsoft Azure pro rozhraní .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)

  Tento balíček obsahuje třídu pro analýzu připojovacího řetězce v konfiguračním souboru, ať už je aplikace spuštěna.

K získání obou balíčků můžete použít balíček NuGet. Postupujte následovně:

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt a zvolte **Spravovat balíčky NuGet**.
1. Ve **Správci balíčků NuGet**vyberte **procházet**. Pak vyhledejte a zvolte **Microsoft.Azure.Storage.Blob**a pak vyberte **Instalovat**.

   Tento krok nainstaluje balíček a jeho závislosti.
1. Vyhledejte a nainstalujte tyto balíčky:

   * **Microsoft.Azure.Storage.Common**
   * **Soubor Microsoft.Azure.Storage.File**
   * **Microsoft.Azure.ConfigurationManager**

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Uložení přihlašovacích údajů účtu úložiště do souboru App.config

Dále uložte pověření do `App.config` souboru projektu. V **Průzkumníku řešení** `App.config` poklepejte na soubor a upravte jej tak, aby byl podobný následujícímu příkladu. Nahraďte `myaccount` název účtu `mykey` úložiště a klíčem účtu úložiště.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> Nejnovější verze emulátoru úložiště Azure nepodporuje Soubory Azure. Aby váš připojovací řetězec mohl pracovat se Soubory Azure, musí jako cíl mít účet služby Azure Storage v cloudu.

## <a name="add-using-directives"></a>Přidání direktiv using

V **Průzkumníku**řešení `Program.cs` otevřete soubor a přidejte následující direktivy pomocí direktiv do horní části souboru.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>Programový přístup ke sdílené složce

Dále přidejte následující obsah `Main()` do metody, za výše uvedený kód, načíst připojovací řetězec. Tento kód získá odkaz na soubor, který jsme vytvořili dříve a výstupy jeho obsah.

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

## <a name="set-the-maximum-size-for-a-file-share"></a>Nastavení maximální velikosti sdílené složky

Počínaje verzí 5.x klientské knihovny úložiště Azure můžete nastavit kvótu (maximální velikost) pro sdílenou složku. Můžete se taky podívat, kolik data je aktuálně uloženo ve sdílené složce.

Nastavení kvóty pro sdílenou složku omezuje celkovou velikost souborů uložených ve sdílené složce. Pokud celková velikost souborů ve sdílené složce překročí kvótu nastavenou pro sdílenou složku, klienti nemohou zvětšit velikost existujících souborů. Klienti nemohou vytvářet nové soubory, pokud tyto soubory nejsou prázdné.

Dole uvedený příklad ukazuje, jak můžete zkontrolovat aktuální využití sdílené složky a jak nastavit kvótu pro sdílenou složku.

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

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Vygenerování sdíleného přístupového podpisu pro soubor nebo sdílenou složku

Klientská knihovna pro úložiště Azure od verze 5.x umožňuje vygenerovat sdílený přístupový podpis (SAS) pro sdílenou složku nebo konkrétní soubor. Můžete také vytvořit uložené zásady přístupu ve sdílené složce pro správu sdílených přístupových podpisů. Doporučujeme vytvořit uložené zásady přístupu, protože umožňuje odvolat SAS, pokud dojde k ohrožení zabezpečení.

Následující příklad vytvoří uložené zásady přístupu ke sdílené položce. Příklad používá tuto zásadu k poskytnutí omezení pro SAS na soubor ve sdílené složce.

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

Další informace o vytváření a používání sdílených přístupových podpisů naleznete v [tématu Jak sdílený přístupový podpis funguje](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works).

## <a name="copy-files"></a>Kopírování souborů

Klientská knihovna pro úložiště Azure od verze 5.x umožňuje kopírovat soubor do jiného souboru, soubor do objektu nebo objekt blob do souboru. V dalších částech ukážeme, jak tyto operace kopírování provést programově.

AzCopy můžete také použít ke kopírování jednoho souboru do jiného nebo ke kopírování objektu blob do souboru nebo naopak. Viz [Začínáme s AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Pokud kopírujete objekt blob do souboru nebo soubor do objektu blob, musíte použít sdílený přístupový podpis (SAS) k ověření přístupu ze zdrojovému objektu, a to i když kopírujete v rámci jednoho účtu úložiště.
>

### <a name="copy-a-file-to-another-file"></a>Kopírování souboru do jiného souboru

V následujícím příkladu se zkopíruje soubor do jiného souboru ve stejné sdílené složce. Vzhledem k tomu, že tato operace kopírování kopíruje mezi soubory ve stejném účtu úložiště, můžete ke kopírování použít ověřování pomocí sdíleného klíče.

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

### <a name="copy-a-file-to-a-blob"></a>Kopírování souboru do objektu blob

V následujícím příkladu se vytvoří soubor a zkopíruje se do objektu blob ve stejném účtu úložiště. V příkladu se pro zdrojový soubor vytvoří SAS, který služba během operace kopírování použije k autorizaci přístupu ke zdrojovému souboru.

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

Stejným způsobem můžete kopírovat objekt blob do souboru. Pokud je zdrojovým objektem objekt blob, vytvořte SAS k autorizaci přístupu k tomuto objektu blob během operace kopírování.

## <a name="share-snapshots"></a>Sdílení snímků

Počínaje verzí 8.5 klientské knihovny úložiště Azure, můžete vytvořit snímek sdílené složky. Umožňuje také vypsat nebo procházet snímky sdílené složky a odstranit je. Snímky sdílené složky jsou jen pro čtení, proto u snímků sdílené složky nejsou povoleny žádné operace zápisu.

### <a name="create-share-snapshots"></a>Vytvoření snímků sdílené složky

Následující příklad vytvoří snímek sdílené složky.

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

### <a name="list-share-snapshots"></a>Výpis snímků sdílené složky

Následující příklad vypíše snímky příslušné sdílené složky.

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

### <a name="browse-files-and-directories-within-share-snapshots"></a>Procházení souborů a adresářů v rámci snímků sdílené složky

Následující příklad prochází soubory a adresáře v rámci snímků sdílené složky.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

### <a name="list-shares-and-share-snapshots-and-restore-file-shares-or-files-from-share-snapshots"></a>Výpis sdílených složek a snímků sdílené složky a obnovení sdílených složek nebo souborů ze snímků sdílené složky

Pořízení snímku sdílené složky umožňuje v budoucnu obnovit jednotlivé soubory nebo celou sdílenou složku.

Soubor můžete ze snímku sdílené složky obnovit zadáním dotazu na snímky sdílené složky nebo sdílenou složku. Potom můžete načíst soubor, který patří do určité houfky sdílené složky. Pomocí této verze můžete buď přímo číst a porovnávat, nebo obnovit.

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

### <a name="delete-share-snapshots"></a>Odstranění snímků sdílené složky

Následující příklad odstraní snímek sdílené složky.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## <a name="troubleshoot-azure-files-by-using-metrics"></a>Poradce při potížích se soubory Azure pomocí metrik<a name="troubleshooting-azure-files-using-metrics"></a>

Analýza úložiště Azure teď podporuje metriky pro Soubory Azure. S údaji z metriky můžete sledovat žádosti a diagnostikovat potíže.

Metriky pro Soubory Azure můžete povolit z [webu Azure Portal](https://portal.azure.com). Metriky můžete také povolit programově voláním operace Vlastnosti sady souborových služeb pomocí rozhraní REST API nebo jednoho z jeho analogů v klientské knihovně úložiště.

Následující příklad kódu ukazuje, jak můžete použít Klientskou knihovnu pro úložiště pro .NET k zapnutí metrik pro Soubory Azure.

Nejprve do `using` `Program.cs` souboru přidejte následující direktivy spolu s těmi, které jste přidali výše:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Přestože objekty BLOB Azure, tabulky Azure `ServiceProperties` a fronty `Microsoft.Azure.Storage.Shared.Protocol` Azure používají sdílený typ v `FileServiceProperties` oboru názvů, Azure Files používá svůj vlastní typ, typ v oboru `Microsoft.Azure.Storage.File.Protocol` názvů. Je však nutné odkazovat na oba obory názvů z vašeho kódu, ale pro následující kód ke kompilaci.

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

Pokud narazíte na nějaké problémy, můžete se odkazovat na [řešení problémů azure soubory v systému Windows](storage-troubleshoot-windows-file-connection-problems.md).

## <a name="next-steps"></a>Další kroky

Další informace o souborech Azure najdete v následujících zdrojích:

### <a name="conceptual-articles-and-videos"></a>Koncepční články a videa

* [Soubory Azure: hladký cloudový souborový systém SMB pro Windows a Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Použití služby Soubory Azure s Linuxem](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Podpora nástrojů pro úložiště File

* [Začínáme s nástrojem AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Řešení potíží se službou Azure Files ve Windows](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Odkaz

* [Rozhraní API služby Azure Storage pro .NET](/dotnet/api/overview/azure/storage)
* [Rozhraní REST API služby File Service](/rest/api/storageservices/File-Service-REST-API)

### <a name="blog-posts"></a>Příspěvky na blozích

* [Azure File Storage, teď obecně dostupné](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Uvnitř Azure File Storage](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Představujeme službu Microsoft Azure Files Service](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Nastavení trvalých připojení k Microsoft Azure Files](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
