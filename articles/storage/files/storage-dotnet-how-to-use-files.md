---
title: Vývoj pro Soubory Azure pomocí .NET | Dokumentace Microsoftu
description: Zjistěte, jak vyvíjet aplikace a služby .NET, které používají Soubory Azure k ukládání dat souborů.
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tamram
ms.assetid: 6a889ee1-1e60-46ec-a592-ae854f9fb8b6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 11/22/2017
ms.author: renash
ms.openlocfilehash: 95f890ccbe03fc734b54ac8c5edee2ec7b56d9c6
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737625"
---
# <a name="develop-for-azure-files-with-net"></a>Vývoj pro Soubory Azure pomocí .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Tento kurz ukazuje základy používání .NET k vývoji aplikací a služeb, které používají [Soubory Azure](storage-files-introduction.md) k ukládání dat souborů. Tento kurz vytvoří jednoduchou konzolovou aplikaci pro provádění základních akcí s technologií .NET a službou Soubory Azure:

* Získání obsahu souboru
* Nastavte kvótu (maximální velikost) pro sdílenou složku.
* Vytvořte sdílený přístupový podpis (klíč SAS) pro soubor, který používá zásady sdíleného přístupu definované ve sdílené složce.
* Zkopírujte soubor do jiného souboru ve stejném účtu úložiště.
* Zkopírujte soubor do objektu blob ve stejném účtu úložiště.
* Použijte Azure Storage Metrics pro řešení potíží.

Další informace o službě Soubory Azure najdete v tématu [Seznámení se Soubory Azure](storage-files-introduction.md).

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Vysvětlení rozhraní API .NET

Soubory Azure poskytuje dva přístupy ke klientským aplikacím: protokol SMB (Server Message Block) a REST. V rámci .NET se tyto přístupy abstrahují prostřednictvím rozhraní `System.IO` a `WindowsAzure.Storage` API.

Rozhraní API | Kdy je použít | Poznámky
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | Vaše aplikace: <ul><li>Potřebuje číst/zapisovat soubory prostřednictvím SMB.</li><li>Je spuštěná v zařízení, které má prostřednictvím portu 445 přístup k vašemu účtu služby Soubory Azure.</li><li>Nemusí spravovat žádná nastavení pro správu sdílené složky.</li></ul> | Kódování vstupně-výstupních operací pro Soubory Azure přes SMB je obvykle stejné jako kódování vstupně-výstupních operací u libovolné síťové sdílené složky nebo místního úložné zařízení. Úvod k celé řadě funkcí v .NET, včetně vstupně-výstupních operací se soubory, najdete v [tomto kurzu](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter).
[WindowsAzure.Storage](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet#client-library) | Vaše aplikace: <ul><li>Nemá přístup ke službě Soubory Azure přes SMB na portu 445 kvůli bráně firewall nebo omezením poskytovatele internetových služeb.</li><li>Vyžaduje funkce pro správu, jako je například možnost nastavit kvótu sdílené složky nebo vytvořit sdílený přístupový podpis.</li></ul> | Tento článek ukazuje použití `WindowsAzure.Storage` pro vstupně-výstupní operace se soubory s využitím REST (místo SMB) a správy sdílené složky.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Vytvoření konzolové aplikace a získání sestavení
V sadě Visual Studio vytvořte novou konzolovou aplikaci pro Windows. Následující kroky ukazují, jak vytvořit konzolovou aplikaci v sadě Visual Studio 2017, ale kroky v jiných verzích sady Visual Studio se podobají.

1. Vyberte **Soubor**  >  **Nový**  >  **Projekt**.
2. Vyberte **Instalováno** > **Šablony** > **Visual C#** > **Klasická plocha Windows**.
3. Vyberte **Aplikace konzoly (.NET Framework)**.
4. Do pole **Název** zadejte název vaší aplikace.
5. Vyberte **OK**.

Všechny příklady kódu v tomto kurzu můžete přidat do metody `Main()` v souboru `Program.cs` vaší konzolové aplikace.

Můžete použít knihovnu klienta služby Azure Storage z libovolného typu aplikace .NET, včetně webové aplikace nebo cloudové služby Azure, desktopové nebo mobilní aplikace. V této příručce použijeme konzolovou aplikaci kvůli zjednodušení.

## <a name="use-nuget-to-install-the-required-packages"></a>Použití balíčku NuGet k instalaci požadovaných balíčků
Abyste mohli tento kurz dokončit, potřebujete ze svého projektu odkazovat na dva balíčky:

* [Microsoft Azure Storage Client Library for .NET](https://www.nuget.org/packages/WindowsAzure.Storage/): Tento balíček zajišťuje programový přístup k datovým prostředkům na účtu úložiště.
* [Microsoft Azure Configuration Manager library for .NET:](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) Tento balíček poskytuje třídu pro potřeby analýzy připojovacího řetězce v konfiguračním souboru bez ohledu na to, kde je aplikace spuštěná.

K získání obou balíčků můžete použít balíček NuGet. Postupujte následovně:

1. Klikněte v **Průzkumníku řešení** pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**.
2. Proveďte online hledání textu „WindowsAzure.Storage“ a kliknutím na **Instalovat** nainstalujte balíček knihovny klienta úložiště a jeho závislosti.
3. Proveďte online hledání textu „WindowsAzure.ConfigurationManager“ a kliknutím na **Instalovat** nainstalujete správce konfigurace Azure.

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Uložení přihlašovacích údajů pro účet úložiště do souboru app.config
Dál uložte svoje přihlašovací údaje do souboru app.config vašeho projektu. Upravte soubor app.config tak, aby vypadal podobně jako v následujícím příkladu, `myaccount` nahraďte názvem svého účtu úložiště a `mykey` nahraďte svým klíčem účtu úložiště.

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
V Průzkumníku řešení otevřete soubor `Program.cs` a na začátek souboru přidejte následující direktivy using.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.WindowsAzure.Storage; // Namespace for Storage Client Library
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.WindowsAzure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>Programový přístup ke sdílené složce
Dál přidejte následující kód k metodě `Main()` (po výše uvedeném kódu) pro získání připojovacího řetězce. Tento kód získá odkaz na soubor, který jsme vytvořili předtím, a vypíše obsah do okna konzoly.

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
Klientská knihovna Azure Storage od verze 5.x umožňuje nastavit kvótu (maximální velikost) sdílené složky v gigabajtech. Můžete se taky podívat, kolik data je aktuálně uloženo ve sdílené složce.

Pokud nastavíte kvótu sdílené složky, můžete omezit celkovou velikost souborů uložených ve sdílené složce. Pokud celková velikost souborů ve sdílené složce překročí kvótu nastavenou pro sdílenou složku, klienti nebudou moct zvyšovat velikost existujících souborů, s výjimkou situace, když je velikost souborů nulová.

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
    Microsoft.WindowsAzure.Storage.File.Protocol.ShareStats stats = share.GetStats();
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
Klientská knihovna pro úložiště Azure od verze 5.x umožňuje vygenerovat sdílený přístupový podpis (SAS) pro sdílenou složku nebo konkrétní soubor. Můžete taky vytvořit sdílené zásady přístupu pro sdílenou složku ke správě sdílených přístupových podpisů. Vytvoření sdílených zásad přístupu se doporučuje, protože se tím nabízí způsob odvolání SAS v případě narušení jeho integrity nebo důvěryhodnosti.

V následujícím příkladu se vytvoří sdílená zásada přístupu pro sdílenou složku a ta se pak použije k omezení pro SAS na souboru ve sdílené složce.

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

    // Create a new shared access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
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
    fileSas.UploadText("This write operation is authenticated via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

Další informace o vytváření a používání sdílených přístupových podpisů najdete v tématech [Použití sdílených přístupových podpisů (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) a [Vytvoření a použití SAS s objekty blob Azure](../blobs/storage-dotnet-shared-access-signature-part-2.md).

## <a name="copy-files"></a>Kopírování souborů
Klientská knihovna pro úložiště Azure od verze 5.x umožňuje kopírovat soubor do jiného souboru, soubor do objektu nebo objekt blob do souboru. V dalších kapitolách ukážeme, jak se operace kopírovaní dají provést programově.

Pomocí nástroje AzCopy taky můžete zkopírovat jeden soubor do jiného nebo zkopírovat objekt blob do souboru a obráceně. Viz téma [Přenos dat pomocí nástroje příkazového řádku AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Pokud kopírujete objekt blob do souboru nebo soubor do objektu blob, musíte použít sdílený přístupový klíč (SAS) k ověření zdroje objektu, a to i když kopírujete v rámci jednoho účtu úložiště.
> 
> 

**Kopírování souboru do jiného souboru** V následujícím příkladu se zkopíruje soubor do jiného souboru ve stejné sdílené složce. Protože tato operace kopírování kopíruje soubory ve stejném účtu úložiště, můžete pro kopírování použít ověření Sdíleným klíčem.

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

**Kopírování souboru do objektu blob** V následujícím příkladu se vytvoří soubor a zkopíruje se do objektu blob ve stejném účtu úložiště. V příkladu se vytvoří SAS pro zdrojový soubor a služba ho při kopírování použije k ověření přístupu ke zdrojovému souboru.

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
// to authenticate access to the source object, even if you are copying within the same
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

Stejným způsobem můžete kopírovat objekt blob do souboru. Pokud je zdrojovým objektem objekt blob, vytvořte SAS k ověření přístupu k tomuto objektu blob při kopírování.

## <a name="share-snapshots-preview"></a>Snímky sdílené složky (Preview)
Klientská knihovna Azure Storage od verze 8.5 umožňuje vytvořit snímek sdílené složky (Preview). Umožňuje také vypsat nebo procházet snímky sdílené složky a odstranit je. Snímky sdílené složky jsou jen pro čtení, proto u snímků sdílené složky nejsou povoleny žádné operace zápisu.

**Vytvoření snímků sdílené složky**

Následující příklad vytvoří snímek sdílené složky.

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```
**Výpis snímků sdílené složky**

Následující příklad vypíše snímky příslušné sdílené složky.

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

**Procházení souborů a adresářů v rámci snímků sdílené složky**

Následující příklad prochází soubory a adresáře v rámci snímků sdílené složky.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

**Výpis sdílených složek a snímků sdílené složky a obnovení sdílených složek nebo souborů ze snímků sdílené složky** 

Pořízení snímku sdílené složky umožňuje v budoucnu obnovit jednotlivé soubory nebo celou sdílenou složku. 

Soubor můžete ze snímku sdílené složky obnovit zadáním dotazu na snímky sdílené složky nebo sdílenou složku. Následně můžete načíst soubor, který patří do konkrétního snímku sdílené složky, a tuto verzi použít k přímému čtení a porovnání nebo obnovení.

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


**Odstranění snímků sdílené složky**

Následující příklad odstraní snímek sdílené složky.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## <a name="troubleshooting-azure-files-using-metrics"></a>Řešení potíží se Soubory Azure pomocí metrik
Analýza úložiště Azure teď podporuje metriky pro Soubory Azure. S údaji z metriky můžete sledovat žádosti a diagnostikovat potíže.

Metriky pro Soubory Azure můžete povolit na webu [Azure Portal](https://portal.azure.com). Metriky můžete povolit také programově zavoláním operace Set File Service Properties přes rozhraní REST API nebo některou z podobných operací v Klientské knihovně pro úložiště.

Následující příklad kódu ukazuje, jak můžete použít Klientskou knihovnu pro úložiště pro .NET k zapnutí metrik pro Soubory Azure.

Nejdříve do souboru `Program.cs` mimo těch, které jste přidali výše, přidejte následující direktivy `using`:

```csharp
using Microsoft.WindowsAzure.Storage.File.Protocol;
using Microsoft.WindowsAzure.Storage.Shared.Protocol;
```

Pamatujte, že zatímco objekty blob Azure, Tabulky Azure a Fronty Azure používají sdílený typ `ServiceProperties` v oboru názvů `Microsoft.WindowsAzure.Storage.Shared.Protocol`, služba Soubory Azure používá vlastní typ `FileServiceProperties` v oboru názvů `Microsoft.WindowsAzure.Storage.File.Protocol`. Aby se ale následující kód mohl zkompilovat, musí se z vašeho kódu odkazovat oba obory názvů.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.WindowsAzure.Storage.File.Protocol namespace.
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

Podrobné pokyny, jak postupovat při řešení problémů, najdete v článku [Azure Files Troubleshooting Article](storage-troubleshoot-windows-file-connection-problems.md) (Soubory Azure – řešení problémů).

## <a name="next-steps"></a>Další kroky
Další informace o službě Soubory Azure najdete na těchto odkazech.

### <a name="conceptual-articles-and-videos"></a>Koncepční články a videa
* [Soubory Azure: hladký cloudový souborový systém SMB pro Windows a Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Jak používat Soubory Azure s Linuxem](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Podpora nástrojů pro úložiště File
* [Použití nástroje AzCopy s Microsoft Azure Storage](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Použití Azure CLI s Azure Storage](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Řešení potíží se Soubory Azure](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Referenční informace
* [Klientská knihovna Storage pro .NET – referenční informace](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [REST API služby File – referenční informace](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### <a name="blog-posts"></a>Příspěvky na blozích
* [Služba Soubory Azure je teď obecně dostupná](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Uvnitř služby Soubory Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Představujeme službu Microsoft Azure File](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Nastavení trvalých připojení k Microsoft Azure Files](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)