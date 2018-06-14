---
title: Nastavit a načíst vlastnosti objektu a metadata ve službě Azure Storage | Microsoft Docs
description: Ukládat vlastní metadata pro objekty v Azure Storage a nastavit a načíst vlastnosti systému.
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 036f9006-273e-400b-844b-3329045e9e1f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: a3eb598b2dabd4986c72b8814926eb0944707050
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873027"
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Nastavení a načtení vlastností a metadat

Objekty v vlastnosti podporu systému Azure Storage a metadata definovaná uživatelem, kromě dat, která obsahují. Tento článek popisuje správu vlastnosti systému a metadata definovaná uživatelem s [Klientská knihovna pro úložiště Azure pro .NET](https://www.nuget.org/packages/WindowsAzure.Storage/).

* **Vlastnosti systému**: vlastnosti systému existovat na všechny prostředky úložiště. Některé z nich můžou číst nebo nastavit, zatímco jiné jsou jen pro čtení. Některé vlastnosti systému skrytě, odpovídají určité standardní hlavičky protokolu HTTP. Klientská knihovna pro úložiště Azure udržuje to pro vás.

* **Metadata definovaná uživatelem**: uživatelem definované metadata jsou metadata, která jste zadali na daný prostředek ve formě dvojice název hodnota. Metadata můžete použít k uložení další hodnoty s prostředků úložiště. Tyto hodnoty další metadata jsou vlastní pouze pro účely a neovlivní chování prostředku.

Načítání hodnot vlastností a metadat pro úložiště prostředek je ve dvou krocích. Než si můžete přečíst tyto hodnoty, můžete musí explicitně načíst je voláním **FetchAttributesAsync** metoda.

> [!IMPORTANT]
> Hodnoty vlastnosti a metadat pro úložiště prostředků nejsou naplněny, pokud je volání jednoho z **FetchAttributesAsync** metody.
>
> Zobrazí se `400 Bad Request` Pokud všechny dvojice název/hodnota obsahovat jiné znaky než ASCII. Dvojice název/hodnota metadat jsou platné hlavičky protokolu HTTP a proto musí splňovat všechny omezení, kterými se řídí hlavičky protokolu HTTP. Proto se doporučuje používat kódování URL nebo pro názvy a hodnoty, který obsahuje jiné znaky než ASCII kódování Base64.
>

## <a name="setting-and-retrieving-properties"></a>Nastavení nebo načtení vlastností
Chcete-li k získávání hodnot vlastností, zavolejte **FetchAttributesAsync** metodu objektu blob nebo kontejneru k naplnění vlastností, přečtěte si hodnoty.

Nastavení vlastností pro objekt, určete vlastnost hodnotu a pak volání **SetProperties –** metoda.

Následující příklad kódu vytvoří kontejner a některé jeho vlastnosti hodnoty zapisuje do okna konzoly.

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
Metadata můžete zadat jako jeden nebo více dvojice název hodnota u objektů blob nebo kontejner prostředku. K nastavení metadat, přidat dvojice název hodnota k **Metadata** kolekce k prostředku, pak volání **SetMetadata** metody uložte hodnoty ke službě.

> [!NOTE]
> Zásady vytváření názvů pro identifikátory jazyka C# musí odpovídat názvu metadata.
>
>

Následující příklad kódu nastaví metadata do kontejneru. Jedna hodnota se nastavuje pomocí kolekce **přidat** metoda. Další hodnota je nastavena pomocí syntaxe implicitní klíč/hodnota. Obě jsou platné.

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

K načtení metadat, volání **FetchAttributes** metodu objektu blob nebo kontejneru k naplnění **Metadata** kolekce, pak číst hodnoty, jak je znázorněno v následujícím příkladu.

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

## <a name="next-steps"></a>Další kroky
* [Klientská knihovna pro Azure Storage pro .NET – referenční informace](/dotnet/api/?term=Microsoft.WindowsAzure.Storage)
* [Azure Klientská knihovna pro úložiště pro balíček NuGet pro rozhraní .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
