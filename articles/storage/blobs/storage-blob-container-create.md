---
title: Vytvoření nebo odstranění kontejneru objektů BLOB pomocí Azure Storage .NET
description: Naučte se, jak vytvořit nebo odstranit kontejner objektů BLOB v účtu Azure Storage pomocí klientské knihovny .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: fbeb6dc425709d26768a6185828b900a37c88fb6
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235038"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Vytvoření nebo odstranění kontejneru v Azure Storage pomocí .NET

Objekty BLOB v Azure Storage jsou uspořádány do kontejnerů. Než budete moct nahrát objekt blob, musíte nejdřív vytvořit kontejner. Tento článek ukazuje, jak vytvořit a odstranit kontejnery pomocí [klientské knihovny Azure Storage pro .NET](/dotnet/api/overview/azure/storage/client).

## <a name="name-a-container"></a>Pojmenování kontejneru

Název kontejneru musí být platný název DNS, protože se jedná o součást jedinečného identifikátoru URI, který se používá k adresování kontejneru nebo objektů BLOB. Při pojmenování kontejneru postupujte podle těchto pravidel:

- Názvy kontejnerů můžou mít délku 3 až 63 znaků.
- Názvy kontejnerů musí začínat písmenem nebo číslicí a může obsahovat jenom malá písmena, číslice a spojovník (-).
- V názvech kontejnerů nejsou povoleny dva nebo více po sobě jdoucích spojovníkových znaků.

Identifikátor URI pro kontejner je v tomto formátu:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Vytvoření kontejneru

Chcete-li vytvořit kontejner, zavolejte jednu z následujících metod:

- [Vytvoření](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [createIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

Metody **Create** a **CreateAsync** vyvolávají výjimku, pokud kontejner se stejným názvem již existuje.

Metody **CreateIfNotExists** a **CreateIfNotExistsAsync** vrací logickou hodnotu, která označuje, zda byl kontejner vytvořen. Pokud už kontejner se stejným názvem existuje, vrátí tyto metody **hodnotu false** , aby označoval, že se nový kontejner nevytvořil.

Kontejnery se vytvoří hned pod účtem úložiště. Není možné vnořit jeden kontejner pod jiným.

Následující příklad vytvoří kontejner asynchronně:

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```

## <a name="create-the-root-container"></a>Vytvoření kořenového kontejneru

Kořenový kontejner slouží jako výchozí kontejner pro váš účet úložiště. Každý účet úložiště může mít jeden kořenový kontejner, který musí být pojmenovaný *$root.* . Kořenový kontejner musíte explicitně vytvořit nebo odstranit.

Můžete odkazovat na objekt BLOB uložený v kořenovém kontejneru bez zahrnutí názvu kořenového kontejneru. Kořenový kontejner umožňuje odkazovat na objekt blob na nejvyšší úrovni hierarchie účtu úložiště. Například můžete odkazovat na objekt blob, který se nachází v kořenovém kontejneru následujícím způsobem:

`https://myaccount.blob.core.windows.net/default.html`

Následující příklad vytvoří kořenový kontejner synchronně:

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

## <a name="delete-a-container"></a>Odstranění kontejneru

Chcete-li odstranit kontejner v rozhraní .NET, použijte jednu z následujících metod:

- [Odstranění](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

Metody **Delete** a **DeleteAsync** vyvolávají výjimku, pokud kontejner neexistuje.

Metody **DeleteIfNotExists** a **DeleteIfNotExistsAsync** vrací logickou hodnotu, která označuje, zda byl kontejner odstraněn. Pokud zadaný kontejner neexistuje, vrátí tyto metody **hodnotu false** , aby označoval, že se kontejner neodstranil.

Po odstranění kontejneru nemůžete vytvořit kontejner se stejným názvem aspoň na 30 sekund a pravděpodobně déle. Při odstraňování kontejneru se pokus o vytvoření kontejneru se stejným názvem nezdaří s kódem chyby HTTP 409 (konflikt). Všechny ostatní operace na kontejneru nebo v objektech blob, které obsahuje, se nezdaří s kódem chyby HTTP 404 (Nenalezeno) při odstraňování kontejneru.

Následující příklad odstraní zadaný kontejner a zpracuje výjimku, pokud kontejner neexistuje:

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Následující příklad ukazuje, jak odstranit všechny kontejnery, které začínají zadanou předponou. V příkladu dojde k přerušení zapůjčení, pokud existuje existující zapůjčení kontejneru.

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            if (container.Properties.LeaseState == LeaseState.Leased)
            {
                await container.BreakLeaseAsync(null);
            }

            await container.DeleteAsync();
        }

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources](../../../includes/storage-blob-dotnet-resources.md)]

## <a name="see-also"></a>Viz také:

- [Vytvořit operaci kontejneru](/rest/api/storageservices/create-container)
- [Operace Delete Container](/rest/api/storageservices/delete-container)
