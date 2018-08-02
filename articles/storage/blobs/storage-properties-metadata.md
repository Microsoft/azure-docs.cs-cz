---
title: Nastavení a načtení vlastností objektu a metadata ve službě Azure Storage | Dokumentace Microsoftu
description: Store vlastních metadat pro objekty ve službě Azure Storage a nastavení a načtení vlastností systému.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/16/2018
ms.author: tamram
ms.openlocfilehash: 2641e1653e14a7c101d95b02b8a5af71ceb9fdc6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398170"
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Nastavení a načtení vlastností a metadat

Objekty ve vlastnostech systému podpory služby Azure Storage a metadata definovaná uživatelem, kromě dat, které obsahují. Tento článek popisuje správu systémové vlastnosti a metadata definovaná uživatelem s [Klientská knihovna Azure Storage pro .NET](https://www.nuget.org/packages/WindowsAzure.Storage/).

* **Vlastnosti systému**: vlastnosti systému existují u jednotlivých prostředků úložiště. Některé z nich může být čtena nebo nastavena, zatímco jiné jsou jen pro čtení. Pod pokličkou některé vlastnosti systému odpovídají určitým standardními záhlavími HTTP. Klientské knihovny pro Azure Storage spravovat za vás tyto vlastnosti.

* **Metadata definovaná uživatelem**: metadata definovaná uživatelem se skládá z jednoho nebo více dvojice název hodnota, které zadáte pro prostředek služby Azure Storage. Metadata můžete použít k ukládání další hodnoty, s prostředkem. Hodnoty metadat jsou vlastní pouze pro účely a nemají vliv na chování prostředku.

Načítání hodnot vlastností a metadat pro prostředek úložiště je dvoustupňový proces. Předtím, než si můžete přečíst tyto hodnoty, musíte explicitně načíst jejich voláním **FetchAttributes** nebo **FetchAttributesAsync** metody. Výjimkou je, pokud jsou volání **Exists** nebo **ExistsAsync** metodu na prostředek. Při volání jedné z těchto metod, zavolá odpovídající služby Azure Storage **FetchAttributes** metoda na pozadí jako součást volání **Exists** metody.

> [!IMPORTANT]
> Pokud zjistíte, že hodnoty vlastnosti nebo metadata pro prostředek úložiště nebyly vyplněna, zkontrolujte, že váš kód volá **FetchAttributes** nebo **FetchAttributesAsync** metody.
>
> Dvojice název/hodnota metadata mohou obsahovat pouze znaky ASCII. Dvojice název/hodnota metadata jsou platná hlavičky protokolu HTTP a proto musí splňovat všechna omezení, kterými se řídí hlavičky protokolu HTTP. Doporučuje se, že používáte kódování URL nebo kódování Base64 pro názvy a hodnoty, který obsahuje jiné znaky než ASCII.
>

## <a name="setting-and-retrieving-properties"></a>Nastavení a načtení vlastností
Chcete-li načíst hodnoty vlastností, zavolejte **FetchAttributesAsync** metodu na objekt blob nebo kontejner k naplnění vlastnosti, pak si můžete přečíst hodnoty.

Můžete nastavit vlastnosti objektu, určete vlastnost hodnotu a potom voláním **SetProperties –** metody.

Následující příklad kódu vytvoří kontejner a pak vypíše některé z jeho hodnot vlastností do okna konzoly.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
await container.FetchAttributesAsync();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>Nastavení a načítání metadat
Metadata můžete zadat jako jeden nebo více dvojice název hodnota u objektu blob nebo kontejner prostředku. Chcete-li nastavit metadata, přidejte dvojice název hodnota, které budou **metadat** kolekce na prostředku, zavolejte **SetMetadata** nebo **SetMetadataAsync** metodu pro uložení hodnoty, které mají Služba.

> [!NOTE]
> Název metadata musí splňovat zásady vytváření názvů pro identifikátory jazyka C#.
>
>

Následující příklad kódu nastaví metadata pro kontejner. Jedna hodnota je nastavena pomocí kolekce **přidat** metody. Jiná hodnota se nastavuje pomocí syntaxe implicitní klíč/hodnota. Obě jsou platné.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    // Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    // Set the container's metadata.
    await container.SetMetadataAsync();
}
```

Chcete-li načíst metadata, zavolejte **FetchAttributes** nebo **FetchAttributesAsync** metodu na objekt blob nebo kontejner k naplnění **metadat** kolekce, pak si můžete přečíst hodnoty, jak je znázorněno v následujícím příkladu.

```csharp
public static async Task ListContainerMetadataAsync(CloudBlobContainer container)
{
    // Fetch container attributes in order to populate the container's properties and metadata.
    await container.FetchAttributesAsync();

    // Enumerate the container's metadata.
    Console.WriteLine("Container metadata:");
    foreach (var metadataItem in container.Metadata)
    {
        Console.WriteLine("\tKey: {0}", metadataItem.Key);
        Console.WriteLine("\tValue: {0}", metadataItem.Value);
    }
}
```

## <a name="next-steps"></a>Další postup
* [Klientská knihovna Azure Storage pro .NET – referenční informace](/dotnet/api/?term=Microsoft.WindowsAzure.Storage)
* [Klientská knihovna Azure Storage pro balíček NuGet pro rozhraní .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
