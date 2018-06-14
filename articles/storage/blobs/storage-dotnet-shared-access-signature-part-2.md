---
title: Vytváření a používání sdíleného přístupového podpisu (SAS) s Azure Blob storage | Microsoft Docs
description: Tento kurz ukazuje, jak vytvořit sdílené přístupové podpisy pro použití s úložištěm Blob a jak využívat v klientských aplikacích.
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 491e0b3c-76d4-4149-9a80-bbbd683b1f3e
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: 9dde12acde748c48b56f9f96ee772fca49954358
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873209"
---
# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Sdílené přístupové podpisy, část 2: Vytvoření a použití SAS s úložištěm Blob

[Část 1](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) tohoto kurzu prozkoumali sdílené přístupové podpisy (SAS) a vysvětlení osvědčené postupy pro jejich použití. Část 2 ukazuje, jak vygenerovat a pak použít sdílené přístupové podpisy úložiště objektů Blob. Příklady jsou napsané v jazyce C# a použít knihovnu klienta služby Azure Storage pro .NET. Příklady v tomto kurzu:

* Vygenerovat sdílený přístupový podpis do kontejneru
* Vygenerovat sdílený přístupový podpis na objekt blob
* Vytvoření zásady uložené přístupu ke správě podpisů v kontejneru na prostředky
* Testovací sdílené přístupové podpisy v aplikaci klienta

## <a name="about-this-tutorial"></a>O tomto kurzu
V tomto kurzu vytvoříme dvě konzolové aplikace, které ukazují, vytvoření a použití sdílených přístupových podpisů pro kontejnery a objekty BLOB:

**Aplikaci 1**: aplikace pro správu. Vygeneruje sdílený přístupový podpis kontejneru a objekt blob. Přístupový klíč účtu úložiště zahrnuje ve zdrojovém kódu.

**Aplikace 2**: klientské aplikace. Přístupy kontejnerů a objektů blob prostředky použití sdílených přístupových podpisů vytvoření první aplikace. Používá sdílené přístupové podpisy k přístupu kontejneru a prostředkům blob – provede *není* zahrnují přístupový klíč účtu úložiště.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>Část 1: Vytvořte konzolovou aplikaci pro generování podpisů sdíleného přístupu
Nejprve je třeba splnit Klientská knihovna pro úložiště Azure pro .NET nainstalované. Můžete nainstalovat [balíček NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "balíček NuGet") obsahující nejaktuálnější sestavení pro knihovny klienta. Toto je doporučená metoda pro zajištění, že máte nejnovější opravy. Klientská knihovna můžete také stáhnout jako součást nejnovější verzi [Azure SDK for .NET](https://azure.microsoft.com/downloads/).

V sadě Visual Studio vytvořte novou konzolovou aplikaci systému Windows s názvem **GenerateSharedAccessSignatures**. Přidejte odkazy na [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) a [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) pomocí jedné z následujících postupů:

* Použití [Správce balíčků NuGet](https://docs.nuget.org/consume/installing-nuget) v sadě Visual Studio. Vyberte **projektu** > **spravovat balíčky NuGet**, vyhledejte online každý balíček (Microsoft.WindowsAzure.ConfigurationManager a WindowsAzure.Storage) a instalovat je.
* Můžete taky najít tyto sestavení v instalaci sady Azure SDK a přidejte odkazy na tyto:
  * Microsoft.WindowsAzure.Configuration.dll
  * Microsoft.WindowsAzure.Storage.dll

Na začátku souboru Program.cs přidejte následující **pomocí** direktivy:

```csharp
using System.IO;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

Upravte soubor app.config tak, aby obsahoval nastavení konfigurace s připojovacím řetězcem, který odkazuje na účtu úložiště. Váš soubor app.config by měl vypadat podobně jako k tomuto:

```xml
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
  </appSettings>
</configuration>
```

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>Vygenerovat sdílený přístupový podpis identifikátor URI pro kontejner
Přidáme způsob, jak vygenerovat sdílený přístupový podpis na nový kontejner. V takovém případě podpis není přidružený k zásadě uložené přístup, takže představuje v identifikátoru URI informace o jeho čas vypršení platnosti a oprávnění, která uděluje.

Nejprve přidejte kód, který **Main()** metoda ověření přístupu k účtu úložiště a vytvořit nový kontejner:

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Insert calls to the methods created below here...

    //Require user input before closing the console window.
    Console.ReadLine();
}
```

V dalším kroku přidejte metodu, která generuje sdílený přístupový podpis kontejneru a vrátí podpis URI:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
    //Set the expiry time and permissions for the container.
    //In this case no start time is specified, so the shared access signature becomes valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

Přidejte následující řádky na konci **Main()** metoda před voláním **Console.ReadLine()**, aby volal **GetContainerSasUri()** a zápis podpis URI v okně konzoly:

```csharp
//Generate a SAS URI for the container, without a stored access policy.
Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
Console.WriteLine();
```

Zkompilování a spuštění výstup sdílený přístupový podpis identifikátor URI pro nový kontejner. Identifikátor URI bude podobný následujícímu:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D
```

Jakmile spustíte kód, bude sdílený přístupový podpis, který jste vytvořili pro kontejner platný dobu následujících 24 hodin. Podpis uděluje oprávnění klienta k seznamu objektů BLOB v kontejneru a vytvářet nové objekty BLOB do kontejneru.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Vygenerovat sdílený přístupový podpis identifikátor URI pro objekt blob
Dále napište nám podobný kód k vytvoření nového objektu blob v kontejneru a vygenerovat sdílený přístupový podpis pro ni. Tento sdílený přístupový podpis není přidružený k zásadě uložené přístup tak, aby zahrnovala čas spuštění, čas vypršení platnosti a informace o oprávněních v identifikátoru URI.

Přidejte novou metodu, která vytvoří nový objekt blob a zapíše text, pak vygeneruje sdílený přístupový podpis a vrátí podpis URI:

```csharp
static string GetBlobSasUri(CloudBlobContainer container)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature (SAS).";
    blob.UploadText(blobContent);

    //Set the expiry time and permissions for the blob.
    //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
    //The shared access signature will be valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

V dolní části **Main()** metoda, přidejte následující řádky k volání **GetBlobSasUri()**, před voláním **Console.ReadLine()**, a zápis sdílený přístupový podpis URI v okně konzoly:

```csharp
//Generate a SAS URI for a blob within the container, without a stored access policy.
Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
Console.WriteLine();
```

Zkompilování a spuštění výstup sdílený přístupový podpis identifikátor URI pro tento nový objekt blob. Identifikátor URI bude podobný následujícímu:

```
https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D
```

### <a name="create-a-stored-access-policy-on-the-container"></a>Vytvoření zásady přístupu uložené na kontejneru
Nyní Pojďme vytvořit zásadu přístupu uložené na kontejneru, který bude definování omezení pro všechny sdílené přístupové podpisy, které jsou k ní přidružena.

V předchozích příkladech jsme určený čas spuštění (implicitně nebo explicitně), čas vypršení platnosti a oprávnění na sdílený přístupový podpis URI sám sebe. V následujících příkladech jsme specifikovat v zásadách přístupu uložené, nikoli na sdílený přístupový podpis. Díky tomu nám změnit bez opětovného vydání sdílený přístupový podpis těchto omezení.

Je možné, že jeden nebo více omezení u sdílený přístupový podpis a zbytek na zásadách uložené přístupu. Však je můžete jenom zadat čas zahájení, čas vypršení platnosti a oprávnění v jednom místě nebo dalších. Například nelze nastavit oprávnění na sdílený přístupový podpis a také zadejte je v zásadách přístupu uložené.

Při přidání zásad uložené přístup do kontejneru, musíte získat oprávnění existující kontejneru, přidat nové zásady přístupu a potom nastavte kontejneru oprávnění.

Přidejte novou metodu, která vytvoří novou zásadu uložené přístup do kontejneru a vrací název zásady:

```csharp
static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
    string policyName)
{
    //Get the container's existing permissions.
    BlobContainerPermissions permissions = container.GetPermissions();

    //Create a new shared access policy and define its constraints.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    //Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    container.SetPermissions(permissions);
}
```

V dolní části **Main()** metoda před voláním **Console.ReadLine()**, přidejte následující řádky první zrušte všechny existující zásady přístupu a pak zavolají **CreateSharedAccessPolicy()** metoda:

```csharp
//Clear any existing access policies on container.
BlobContainerPermissions perms = container.GetPermissions();
perms.SharedAccessPolicies.Clear();
container.SetPermissions(perms);

//Create a new access policy on the container, which may be optionally used to provide constraints for
//shared access signatures on the container and the blob.
string sharedAccessPolicyName = "tutorialpolicy";
CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
```

Když zrušíte zaškrtnutí zásady přístupu do kontejneru, musíte nejprve získat kontejneru existující oprávnění, poté zrušte oprávnění a znovu nastavit oprávnění.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Vygenerovat sdílený přístupový podpis URI na kontejneru, který používá zásady přístupu
V dalším kroku vytvoříme jiný sdílený přístupový podpis kontejneru, který jsme vytvořili výše, ale tentokrát uložené přístup zásadu, kterou jsme vytvořili v předchozím příkladu jsme přidružit podpis.

Přidání nové metody pro generování jiný sdílený přístupový podpis kontejneru:

```csharp
static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Generate the shared access signature on the container. In this case, all of the constraints for the
    //shared access signature are specified on the stored access policy.
    string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

V dolní části **Main()** metoda před voláním **Console.ReadLine()**, přidejte následující řádky k volání **GetContainerSasUriWithPolicy** metoda:

```csharp
//Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Vygenerovat sdílený přístupový podpis URI u objektu Blob, který používá zásady přístupu
Nakonec přidáme podobné metody vytvoření jiný objekt blob a vygenerovat sdílený přístupový podpis, který má přidružené k zásadě uložené přístup.

Přidání nové metody pro vytvoření objektu blob a vygenerovat sdílený přístupový podpis:

```csharp
static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature. " +
    "A stored access policy defines the constraints for the signature.";
    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    ms.Position = 0;
    using (ms)
    {
        blob.UploadFromStream(ms);
    }

    //Generate the shared access signature on the blob.
    string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

V dolní části **Main()** metoda před voláním **Console.ReadLine()**, přidejte následující řádky k volání **GetBlobSasUriWithPolicy** metoda:

```csharp
//Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

**Main()** metoda by měl nyní vypadat jako v celé jeho šíři. Spusťte ji k zápisu sdílený přístupový podpis identifikátory URI v okně konzoly pak zkopírujte a vložte je do textového souboru pro použití v druhé části tohoto kurzu.

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    Console.ReadLine();
}
```

Při spuštění GenerateSharedAccessSignatures konzolové aplikace, zobrazí se výstup podobný následujícímu. Jedná se o sdílené přístupové podpisy, které můžete použít v rámci 2 kurzu.

```
Container SAS URI: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=pFlEZD%2F6sJTNLxD%2FQ26Hh85j%2FzYPxZav6mP1KJwnvJE%3D&se=2017-05-16T16%3A16%3A47Z&sp=wl

Blob SAS URI: https://storagesample.blob.core.windows.net/sascontainer/sasblob.txt?sv=2016-05-31&sr=b&sig=%2FiBWAZbXESzCMvRcm7JwJBK0gT0BtPSWEq4pRwmlBRI%3D&st=2017-05-15T16%3A11%3A48Z&se=2017-05-16T16%3A16%3A48Z&sp=rw

Container SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&si=tutorialpolicy&sig=aMb6rKDvvpfiGVsZI2rCmyUra6ZPpq%2BZ%2FLyTgAeec%2Bk%3D

Blob SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer/sasblobpolicy.txt?sv=2016-05-31&sr=b&si=tutorialpolicy&sig=%2FkTWkT23SS45%2FoF4bK2mqXkN%2BPKs%2FyHuzkfQ4GFoZVU%3D
```

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>Část 2: Vytvořte konzolovou aplikaci otestovat sdílených přístupových podpisů
K testování sdílené přístupové podpisy vytvořili v předchozích příkladech, vytvoříme druhý konzolovou aplikaci, která používá podpisů k provádění operací na kontejneru a na objekt blob.

> [!NOTE]
> Pokud víc než 24 hodin předané vzhledem k tomu, že jste dokončili první část kurzu, podpisy, které jste vygenerovali již nebude platný. V takovém případě byste měli spustit kód v první aplikaci konzoly vygenerovat novou sdílené přístupové podpisy pro použití v druhé části tohoto kurzu.
>

V sadě Visual Studio vytvořte novou konzolovou aplikaci systému Windows s názvem **ConsumeSharedAccessSignatures**. Přidejte odkazy na [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) a [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/), protože jste provedli dříve.

Na začátku souboru Program.cs přidejte následující **pomocí** direktivy:

```csharp
using System.IO;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

V textu **Main()** metoda, přidejte následující konstanty řetězec, změna jejich hodnoty na sdílené přístupové podpisy generované v části 1 kurzu.

```csharp
static void Main(string[] args)
{
    const string containerSAS = "<your container SAS>";
    const string blobSAS = "<your blob SAS>";
    const string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    const string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
}
```

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Přidání metody pokusit kontejneru operace pomocí sdíleného přístupového podpisu
Potom přidáme metodu, která se testuje některé operace kontejneru pomocí sdíleného přístupového podpisu kontejneru. Sdílený přístupový podpis se používá k vrácení odkaz na kontejner, ověřování přístupu ke kontejneru podle podpis samostatně.

Do souboru Program.cs přidejte následující metodu:

```csharp
static void UseContainerSAS(string sas)
{
    //Try performing container operations with the SAS provided.

    //Return a reference to the container using the SAS URI.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

    //Create a list to store blob URIs returned by a listing operation on the container.
    List<ICloudBlob> blobList = new List<ICloudBlob>();

    //Write operation: write a new blob to the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
        string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        blob.UploadText(blobContent);

        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //List operation: List the blobs in the container.
    try
    {
        foreach (ICloudBlob blob in container.ListBlobs())
        {
            blobList.Add(blob);
        }
        Console.WriteLine("List operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("List operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Get a reference to one of the blobs in the container and read it.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        MemoryStream msRead = new MemoryStream();
        msRead.Position = 0;
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            Console.WriteLine(msRead.Length);
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    Console.WriteLine();

    //Delete operation: Delete a blob in the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Aktualizace **Main()** metoda k volání **UseContainerSAS()** s oběma sdílené přístupové podpisy, které jste vytvořili v kontejneru:

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    Console.ReadLine();
}
```

### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Přidání metody pokusit operace objektů blob pomocí sdíleného přístupového podpisu
Nakonec přidáme metodu, která se testuje některé operace objektů blob pomocí sdíleného přístupového podpisu u objektu blob. V tomto případě používáme konstruktoru **CloudBlockBlob(String)** a předejte sdílený přístupový podpis vrací odkaz na objekt blob. Další ověřování není vyžadováno; je založena na podpis samostatně.

Do souboru Program.cs přidejte následující metodu:

```csharp
static void UseBlobSAS(string sas)
{
    //Try performing blob operations using the SAS provided.

    //Return a reference to the blob using the SAS URI.
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

    //Write operation: Write a new blob to the container.
    try
    {
        string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
        MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        msWrite.Position = 0;
        using (msWrite)
        {
            blob.UploadFromStream(msWrite);
        }
        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Read the contents of the blob.
    try
    {
        MemoryStream msRead = new MemoryStream();
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            msRead.Position = 0;
            using (StreamReader reader = new StreamReader(msRead, true))
            {
                string line;
                while ((line = reader.ReadLine()) != null)
                {
                    Console.WriteLine(line);
                }
            }
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Delete operation: Delete the blob.
    try
    {
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Aktualizace **Main()** metoda k volání **UseBlobSAS()** s oběma sdílené přístupové podpisy, které jste vytvořili na objekt blob:

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
    UseBlobSAS(blobSAS);
    UseBlobSAS(blobSASWithAccessPolicy);

    Console.ReadLine();
}
```

Spusťte konzolovou aplikaci a sledovat výstup zobrazíte operací, které jsou povoleny pro které podpisy. Výstup v okně konzoly bude vypadat nějak takto:

```
Write operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

List operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

Read operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

Delete operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

...
```

## <a name="next-steps"></a>Další kroky

* [Sdílené přístupové podpisy, část 1: Vysvětlení modelu SAS](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Správa anonymního přístupu pro čtení ke kontejnerům a objektům BLOB](storage-manage-access-to-resources.md)
* [Delegování přístupu k pomocí sdíleného přístupového podpisu (REST API)](http://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Představení tabulky a fronty SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
