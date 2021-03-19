---
title: Anonymní přístup k veřejným kontejnerům a objektům blob pomocí .NET
titleSuffix: Azure Storage
description: K anonymnímu přístupu k veřejným kontejnerům a objektům blob použijte Azure Storage Klientská knihovna pro .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 2437c5b3272163b3931d7417c84e761c591aec85
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90088812"
---
# <a name="access-public-containers-and-blobs-anonymously-with-net"></a>Anonymní přístup k veřejným kontejnerům a objektům blob pomocí .NET

Azure Storage podporuje volitelné veřejné oprávnění ke čtení pro kontejnery a objekty blob. Klienti mají anonymní přístup k veřejným kontejnerům a objektům blob pomocí Azure Storage klientských knihoven a také pomocí dalších nástrojů a nástrojů, které podporují přístup k datům Azure Storage.

Tento článek ukazuje, jak přistupovat k veřejnému kontejneru nebo objektu BLOB z rozhraní .NET. Informace o konfiguraci anonymního přístupu pro čtení v kontejneru najdete v tématu [Konfigurace anonymního veřejného přístupu pro čtení pro kontejnery a objekty blob](anonymous-read-access-configure.md). Informace o tom, jak zabránit všem anonymním přístupům k účtu úložiště, najdete v tématu [zabránění anonymnímu veřejnému přístupu pro čtení kontejnerů a objektů BLOB](anonymous-read-access-prevent.md).

Klient, který přistupuje k kontejnerům a objektům blob anonymně, může používat konstruktory, které nevyžadují přihlašovací údaje. V následujících příkladech je znázorněno několik různých způsobů, jak odkazovat na kontejnery a objekty blob anonymně.

## <a name="create-an-anonymous-client-object"></a>Vytvoření objektu anonymního klienta

Můžete vytvořit nový objekt klienta služby pro anonymní přístup tím, že zadáte koncový bod úložiště objektů BLOB pro tento účet. Musíte ale taky znát název kontejneru v tomto účtu, který je dostupný pro anonymní přístup.

# <a name="net-v12-sdk"></a>[\.Sada SDK pro .NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# <a name="net-v11-sdk"></a>[\.Sada SDK pro .NET v11](#tab/dotnet11)

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

---

## <a name="reference-a-container-anonymously"></a>Anonymní odkaz na kontejner

Pokud máte adresu URL kontejneru, který je anonymně dostupný, můžete jej použít k přímému odkazování kontejneru.

# <a name="net-v12-sdk"></a>[\.Sada SDK pro .NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_ListBlobsAnonymously":::

# <a name="net-v11-sdk"></a>[\.Sada SDK pro .NET v11](#tab/dotnet11)

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

---

## <a name="reference-a-blob-anonymously"></a>Anonymní odkazování na objekt BLOB

Pokud máte adresu URL objektu blob, který je k dispozici pro anonymní přístup, můžete odkazovat na objekt BLOB přímo pomocí této adresy URL:

# <a name="net-v12-sdk"></a>[\.Sada SDK pro .NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_DownloadBlobAnonymously":::

# <a name="net-v11-sdk"></a>[\.Sada SDK pro .NET v11](#tab/dotnet11)

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

---

## <a name="next-steps"></a>Další kroky

- [Konfigurace anonymního veřejného přístupu pro čtení pro kontejnery a objekty blob](anonymous-read-access-configure.md)
- [Zabránit anonymnímu veřejnému přístupu pro čtení kontejnerů a objektů BLOB](anonymous-read-access-prevent.md)
- [Autorizace přístupu k Azure Storage](../common/storage-auth.md)
