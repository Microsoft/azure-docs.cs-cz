---
title: Spravovat veřejný přístup pro čtení kontejnerů a objektů BLOB
titleSuffix: Azure Storage
description: Naučte se, jak zpřístupnit kontejnery a objekty blob pro anonymní přístup a jak k nim přistupovat prostřednictvím kódu programu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 4d9a54c220861b19d67b07998e609ee72897446a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360912"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob

Můžete povolit anonymní veřejný přístup pro čtení kontejneru a jeho objektů BLOB v úložišti objektů BLOB v Azure. Díky tomu můžete k těmto prostředkům udělit přístup jen pro čtení bez sdílení klíče účtu a bez vyžadování sdíleného přístupového podpisu (SAS).

Veřejný přístup pro čtení je nejvhodnější pro scénáře, ve kterých mají být některé objekty blob vždy dostupné pro anonymní přístup pro čtení. Pro detailní kontrolu můžete vytvořit sdílený přístupový podpis. Sdílené přístupové podpisy umožňují poskytovat omezený přístup pomocí různých oprávnění pro konkrétní časové období. Další informace o vytváření sdílených přístupových podpisů najdete [v tématu použití sdílených přístupových podpisů (SAS) v Azure Storage](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Udělení anonymních oprávnění uživatelům k kontejnerům a objektům blob

Ve výchozím nastavení může být k kontejneru a jakýmkoli objektům blob v něm přístup jen uživatel, kterému byla udělena příslušná oprávnění. Pokud chcete anonymním uživatelům udělit přístup pro čtení ke kontejneru a jeho objektům blob, můžete nastavit úroveň veřejného přístupu kontejneru. Když udělíte veřejný přístup ke kontejneru, můžou anonymní uživatelé číst objekty BLOB v rámci veřejně přístupného kontejneru, aniž by museli žádost autorizovat.

Můžete nakonfigurovat kontejner s následujícími oprávněními:

- **Žádný veřejný přístup pro čtení:** K kontejneru a jeho objektům blob může mít přistup jenom vlastník účtu úložiště. Toto je výchozí nastavení pro všechny nové kontejnery.
- **Veřejný přístup pro čtení jenom pro objekty BLOB:** Objekty BLOB v kontejneru lze číst anonymním požadavkem, ale data kontejneru nejsou k dispozici. Anonymní klienti nemohou vytvořit výčet objektů BLOB v rámci kontejneru.
- **Veřejný přístup pro čtení kontejneru a jeho objektů BLOB:** Všechna data kontejneru a objektů BLOB lze číst anonymním požadavkem. Klienti mohou vytvořit výčet objektů BLOB v kontejneru anonymním požadavkem, ale nemohou vytvořit výčet kontejnerů v rámci účtu úložiště.

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Nastavit úroveň veřejného přístupu kontejneru v Azure Portal

Z [Azure Portal](https://portal.azure.com)můžete úroveň veřejného přístupu aktualizovat na jeden nebo více kontejnerů:

1. V Azure Portal přejděte na přehled svého účtu úložiště.
1. V části **BLOB Service** v okně nabídky vyberte **objekty blob**.
1. Vyberte kontejnery, pro které chcete nastavit úroveň veřejného přístupu.
1. Pomocí tlačítka **změnit úroveň přístupu** můžete zobrazit nastavení veřejného přístupu.
1. Vyberte požadovanou úroveň veřejného přístupu v rozevíracím seznamu **úroveň veřejného přístupu** a kliknutím na tlačítko OK tuto změnu použijte u vybraných kontejnerů.

Následující snímek obrazovky ukazuje, jak změnit úroveň veřejného přístupu pro vybrané kontejnery.

![Snímek obrazovky ukazující, jak nastavit úroveň veřejného přístupu na portálu](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Úroveň veřejného přístupu pro jednotlivý objekt BLOB se nedá změnit. Úroveň veřejného přístupu je nastavena pouze na úrovni kontejneru.

### <a name="set-container-public-access-level-with-net"></a>Nastavení úrovně veřejného přístupu ke kontejneru pomocí .NET

Chcete-li nastavit oprávnění pro kontejner pomocí klientské knihovny Azure Storage pro .NET, nejprve Načtěte existující oprávnění kontejneru voláním jedné z následujících metod:

- [GetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

Dále nastavte vlastnost **PublicAccess** objektu [BlobContainerPermissions](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) , který je vrácen metodou **GetPermissions** .

Nakonec zavolejte jednu z následujících metod pro aktualizaci oprávnění kontejneru:

- [SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

Následující příklad nastaví oprávnění kontejneru na úplný veřejný přístup pro čtení. Pokud chcete nastavit oprávnění pro veřejný přístup pro čtení jenom pro objekty blob, nastavte vlastnost **PublicAccess** na **BlobContainerPublicAccessType. blob**. Chcete-li odebrat všechna oprávnění pro anonymní uživatele, nastavte vlastnost na hodnotu **BlobContainerPublicAccessType. off**.

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Anonymní přístup k kontejnerům a objektům blob

Klient, který přistupuje k kontejnerům a objektům blob anonymně, může používat konstruktory, které nevyžadují přihlašovací údaje. V následujících příkladech je znázorněno několik různých způsobů, jak odkazovat na kontejnery a objekty blob anonymně.

### <a name="create-an-anonymous-client-object"></a>Vytvoření objektu anonymního klienta

Můžete vytvořit nový objekt klienta služby pro anonymní přístup tím, že zadáte koncový bod úložiště objektů BLOB pro tento účet. Musíte ale taky znát název kontejneru v tomto účtu, který je dostupný pro anonymní přístup.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>Anonymní odkaz na kontejner

Pokud máte adresu URL kontejneru, který je anonymně dostupný, můžete jej použít k přímému odkazování kontejneru.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>Anonymní odkazování na objekt BLOB

Pokud máte adresu URL objektu blob, který je k dispozici pro anonymní přístup, můžete odkazovat na objekt BLOB přímo pomocí této adresy URL:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

## <a name="next-steps"></a>Další kroky

- [Autorizace přístupu k Azure Storage](../common/storage-auth.md)
- [Udělení omezeného přístupu k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](../common/storage-sas-overview.md)
- [REST API služby BLOB Service](/rest/api/storageservices/blob-service-rest-api)
