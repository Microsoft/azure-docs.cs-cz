---
title: Správa veřejného přístupu pro čtení pro kontejnery a objekty BLOB
titleSuffix: Azure Storage
description: Zjistěte, jak zpřístupnit kontejnery a objekty BLOB pro anonymní přístup a jak k nim programově přistupovat.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 4d9a54c220861b19d67b07998e609ee72897446a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255480"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob

V úložišti objektů blob v Azure můžete u kontejneru povolit anonymní veřejný přístup pro čtení. Pokud to uděláte, můžete u těchto prostředků udělit přístup jen pro čtení bez sdílení klíče k účtu a bez vyžadování sdíleného přístupového podpisu (SAS).

Veřejný přístup pro čtení je nejvhodnější pro scénáře, kde chcete, aby určité objekty BLOB byly vždy k dispozici pro anonymní přístup pro čtení. Pro podrobnější ovládací prvek můžete vytvořit sdílený přístupový podpis. Sdílené přístupové podpisy umožňují poskytovat omezený přístup pomocí různých oprávnění po určité časové období. Další informace o vytváření sdílených přístupových podpisů najdete [v tématu Používání sdílených přístupových podpisů (SAS) ve službě Azure Storage](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Udělení oprávnění anonymním uživatelům ke kontejnerům a objektům BLOB

Ve výchozím nastavení může ke kontejneru a ke všem v něm obsaženým objektům blob získat přístup jen uživatel, kterému byla udělena příslušná oprávnění. Pokud chcete u kontejneru a u jeho objektů blob udělit přístup pro čtení anonymním uživatelům, můžete u něho nastavit veřejnou úroveň přístupu. Když u kontejneru nastavíte veřejný přístup, můžou anonymní uživatelé číst objekty blob, aniž by bylo třeba jejich žádost autorizovat.

Kontejner můžete nakonfigurovat s následujícími oprávněními:

- **Žádný veřejný přístup pro čtení:** Ke kontejneru a jeho objektům BLOB má přístup pouze vlastník účtu úložiště. Toto je výchozí nastavení pro všechny nové kontejnery.
- **Veřejný přístup pro čtení pouze pro objekty BLOB:** Objekty BLOB v kontejneru lze číst pomocí anonymní požadavek, ale data kontejneru není k dispozici. Anonymní klienti nelze vytvořit výčet objektů BLOB v rámci kontejneru.
- **Veřejný přístup pro čtení pro kontejner a jeho objekty BLOB:** Všechna data kontejneru a objektu blob lze číst pomocí anonymního požadavku. Klienti mohou vytvořit výčet objektů BLOB v rámci kontejneru anonymní mj.

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Nastavení úrovně veřejného přístupu kontejneru na webu Azure Portal

Na [webu Azure Portal](https://portal.azure.com)můžete aktualizovat úroveň veřejného přístupu pro jeden nebo více kontejnerů:

1. Přejděte na přehled svého účtu úložiště na webu Azure Portal.
1. V části **Blob service** on the menu blade vyberte **Blobs**.
1. Vyberte kontejnery, pro které chcete nastavit úroveň veřejného přístupu.
1. Pomocí tlačítka **Změnit úroveň přístupu** můžete zobrazit nastavení veřejného přístupu.
1. V rozevíracím souboru **Úroveň veřejného přístupu** vyberte požadovanou úroveň veřejného přístupu a klepnutím na tlačítko OK použijte změnu u vybraných kontejnerů.

Následující snímek obrazovky ukazuje, jak změnit úroveň veřejného přístupu pro vybrané kontejnery.

![Snímek obrazovky znázorňující nastavení úrovně veřejného přístupu na portálu](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Úroveň veřejného přístupu pro jednotlivé objekty blob nelze změnit. Úroveň veřejného přístupu je nastavena pouze na úrovni kontejneru.

### <a name="set-container-public-access-level-with-net"></a>Nastavení úrovně veřejného přístupu kontejneru pomocí rozhraní .NET

Chcete-li nastavit oprávnění pro kontejner pomocí klientské knihovny Azure Storage pro rozhraní .NET, nejprve načtěte existující oprávnění kontejneru voláním jedné z následujících metod:

- [GetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

Dále nastavte vlastnost **PublicAccess** na objekt [blobContainerPermissions,](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) který je vrácen metodou **GetPermissions.**

Nakonec zavolejte jednu z následujících metod k aktualizaci oprávnění kontejneru:

- [SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

Následující příklad nastaví oprávnění kontejneru na úplný veřejný přístup pro čtení. Chcete-li nastavit oprávnění pro veřejný přístup ke čtení pouze pro objekty BLOB, nastavte vlastnost **PublicAccess** na **objekt BlobContainerPublicAccessType.Blob**. Chcete-li odebrat všechna oprávnění pro anonymní uživatele, nastavte vlastnost **na BlobContainerPublicAccessType.Off**.

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Anonymní přístup ke kontejnerům a objektům BLOB

Klient, který přistupuje k kontejnerům a objektům BLOB anonymně, může používat konstruktory, které nevyžadují pověření. Následující příklady ukazují několik různých způsobů, jak odkazovat na kontejnery a objekty BLOB anonymně.

### <a name="create-an-anonymous-client-object"></a>Vytvoření anonymního objektu klienta

Můžete vytvořit nový objekt klienta služby pro anonymní přístup poskytnutím koncového bodu úložiště objektů blob pro účet. Musíte však také znát název kontejneru v tomto účtu, který je k dispozici pro anonymní přístup.

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

Pokud máte adresu URL kontejneru, který je anonymně k dispozici, můžete ji použít k přímému odkazu na kontejner.

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

### <a name="reference-a-blob-anonymously"></a>Anonymní odkaz na objekt blob

Pokud máte adresu URL objektu blob, který je k dispozici pro anonymní přístup, můžete odkazovat na objekt blob přímo pomocí této adresy URL:

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
- [Udělit omezený přístup k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](../common/storage-sas-overview.md)
- [Rozhraní REST API služby Blob Service](/rest/api/storageservices/blob-service-rest-api)
