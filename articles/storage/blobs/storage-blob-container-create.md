---
title: Vytvoření nebo odstranění kontejneru objektů blob s rozhraním .NET – Azure Storage
description: Zjistěte, jak vytvořit nebo odstranit kontejner objektů blob v účtu Azure Storage pomocí knihovny klienta .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c95ed6dde3c00c0688ccfd58565fd112427c8899
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135934"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Vytvoření nebo odstranění kontejneru ve službě Azure Storage pomocí rozhraní .NET

Objekty BLOB ve službě Azure Storage jsou uspořádané do kontejnerů. Před nahráním objektu blob je nutné nejprve vytvořit kontejner. Tento článek ukazuje, jak vytvořit a odstranit kontejnery s [klientskou knihovnou Azure Storage pro .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="name-a-container"></a>Pojmenování kontejneru

Název kontejneru musí být platný název DNS, protože je součástí jedinečného identifikátoru URI, který slouží k adresování kontejneru nebo jeho objektů BLOB. Při pojmenování kontejneru postupujte podle těchto pravidel:

- Názvy kontejnerů mohou mít velikost 3 až 63 znaků.
- Názvy kontejnerů musí začínat písmenem nebo číslem a mohou obsahovat pouze malá písmena, čísla a znak pomlčky (-).
- Dva nebo více po sobě jdoucích pomlčka znaky nejsou povoleny v názvech kontejnerů.

Identifikátor URI pro kontejner je v tomto formátu:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Vytvoření kontejneru

Chcete-li vytvořit kontejner, zavolejte jednu z následujících metod:

- [Vytvořit](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

**Create** a **CreateAsync** metody vyvolat výjimku, pokud kontejner se stejným názvem již existuje.

**Metody CreateIfNotExists** a **CreateIfNotExistsAsync** vrátí logickou hodnotu označující, zda byl kontejner vytvořen. Pokud kontejner se stejným názvem již existuje, pak tyto metody vrátí **False** označují, že nebyl vytvořen nový kontejner.

Kontejnery jsou vytvořeny okamžitě pod účet úložiště. Není možné vnořit jeden kontejner pod druhou.

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

Kořenový kontejner slouží jako výchozí kontejner pro váš účet úložiště. Každý účet úložiště může mít jeden kořenový kontejner, který musí být pojmenován *$root.*. Kořenový kontejner je nutné explicitně vytvořit nebo odstranit.

Můžete odkazovat na objekt blob uložený v kořenovém kontejneru bez zahrnutí názvu kořenového kontejneru. Kořenový kontejner umožňuje odkazovat na objekt blob na nejvyšší úrovni hierarchie účtů úložiště. Můžete například odkazovat na objekt blob, který se nachází v kořenovém kontejneru následujícím způsobem:

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

- [Odstranit](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [OdstranitAsynchronizace](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [OdstranitIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [OdstranitIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

**Delete** a **DeleteAsync** metody vyvolat výjimku, pokud kontejner neexistuje.

**DeleteIfExists** a **DeleteIfExistsAsync** metody vrátí logickou hodnotu označující, zda byl odstraněn kontejner. Pokud zadaný kontejner neexistuje, pak tyto metody vrátí **False** označují, že kontejner nebyl odstraněn.

Po odstranění kontejneru nelze vytvořit kontejner se stejným názvem po dobu nejméně 30 sekund a případně déle. Při odstraňování kontejneru se pokus o vytvoření kontejneru se stejným názvem nezdaří s kódem chyby HTTP 409 (Conflict). Všechny ostatní operace na kontejneru nebo objekty BLOB, které obsahuje se nezdaří s kódem chyby HTTP 404 (nebyl nalezen) při odstraňování kontejneru.

Následující příklad odstraní zadaný kontejner a zpracovává výjimku, pokud kontejner neexistuje:

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

Následující příklad ukazuje, jak odstranit všechny kontejnery, které začínají zadanou předponou. Příklad přeruší zapůjčení, pokud existuje zapůjčení na kontejneru.

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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Viz také

- [Vytvořit operaci kontejneru](/rest/api/storageservices/create-container)
- [Operace Delete Container](/rest/api/storageservices/delete-container)
