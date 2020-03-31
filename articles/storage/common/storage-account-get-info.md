---
title: Získání typu účtu úložiště a názvu skladové položky s rozhraním .NET
titleSuffix: Azure Storage
description: Zjistěte, jak získat typ účtu úložiště Azure a název sku pomocí knihovny klienta .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 1495ed55c24a0f94b911d60d1db0f32940ea134a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137054"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Získání typu účtu úložiště a názvu skladové položky s rozhraním .NET

Tento článek ukazuje, jak získat typ účtu úložiště Azure a název sku pro objekt blob pomocí [knihovny klienta Azure Storage pro .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

Informace o účtu jsou k dispozici ve verzích služeb počínaje verzí 2018-03-28.

## <a name="about-account-type-and-sku-name"></a>Typ účtu a název skladové položky

**Typ účtu**: Platné `BlobStorage` `BlockBlobStorage`typy `FileStorage` `Storage`účtů `StorageV2`zahrnují , , , a . [Přehled účtu úložiště Azure](storage-account-overview.md) obsahuje další informace, včetně popisů různých účtů úložiště.

**Název skladové položky**: `Premium_LRS` `Premium_ZRS`Platné `Standard_GRS` `Standard_GZRS`názvy skladových položk zahrnují , , `Standard_LRS`, , `Standard_RAGRS` `Standard_RAGZRS`, a `Standard_ZRS`. Názvy skladových míst rozlišují malá a velká písmena a jsou řetězcová pole ve [třídě SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet).

## <a name="retrieve-account-information"></a>Načíst informace o účtu

Chcete-li získat typ účtu úložiště a název skladové položky přidružené k objektu blob, zavolejte metodu [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) nebo [GetAccountPropertiesAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet)

Následující příklad kódu načte a zobrazí vlastnosti účtu jen pro čtení.

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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si o dalších operacích, které můžete provádět na účtu úložiště prostřednictvím [portálu Azure a](https://portal.azure.com) rozhraní AZURE REST API.

- [Operace Získat informace o účtu (REST)](/rest/api/storageservices/get-account-information)
