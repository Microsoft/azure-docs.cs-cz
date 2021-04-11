---
title: Získání typu účtu úložiště a názvu SKU pomocí .NET
titleSuffix: Azure Storage
description: Naučte se, jak získat Azure Storage typ účtu a název SKU pomocí klientské knihovny .NET.
services: storage
author: twooley
ms.author: twooley
ms.date: 11/12/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 26ce4a77e02e62a1a204529ce8652fbba3aab2c9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277143"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Získání typu účtu úložiště a názvu SKU pomocí .NET

Tento článek ukazuje, jak získat Azure Storage typ účtu a název SKU pro objekt BLOB pomocí [Azure Storage klientské knihovny pro .NET](/dotnet/api/overview/azure/storage).

## <a name="about-account-type-and-sku-name"></a>Informace o typu účtu a názvu SKU

**Typ účtu**: platné typy účtů zahrnují `BlobStorage` , `BlockBlobStorage` , `FileStorage` , `Storage` a `StorageV2` . [Přehled účtu Azure Storage](storage-account-overview.md) obsahuje další informace, včetně popisů různých účtů úložiště.

**Název SKU**: platné názvy SKU zahrnují `Premium_LRS` , `Premium_ZRS` , `Standard_GRS` , `Standard_GZRS` , `Standard_LRS` , `Standard_RAGRS` , `Standard_RAGZRS` a `Standard_ZRS` . V názvech SKU jsou rozlišována velká a malá písmena a jsou řetězcová pole ve [třídě SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname).

## <a name="retrieve-account-information"></a>Načíst informace o účtu

Následující příklad kódu načte a zobrazí vlastnosti účtu jen pro čtení.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

K získání typu účtu úložiště a názvu SKU přidruženého k objektu BLOB volejte metodu [GetAccountInfo](/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfo) nebo [GetAccountInfoAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfoasync) .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Account.cs" id="Snippet_GetAccountInfo":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

K získání typu účtu úložiště a názvu SKU přidruženého k objektu BLOB volejte metodu [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties) nebo [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync) .

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
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

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si o dalších operacích, které můžete provádět na účtu úložiště prostřednictvím [Azure Portal](https://portal.azure.com) a REST API Azure.

- [Operace získání informací o účtu (REST)](/rest/api/storageservices/get-account-information)
