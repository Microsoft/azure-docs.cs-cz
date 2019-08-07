---
title: Získání typu účtu úložiště a názvu SKU pomocí .NET-Azure Storage
description: Naučte se, jak získat Azure Storage typ účtu a název SKU pomocí klientské knihovny .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 21ecaae679ad4a5f21107ef53d3899cf03593c9d
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68829200"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Získání typu účtu úložiště a názvu SKU pomocí .NET

Tento článek ukazuje, jak získat Azure Storage typ účtu a název SKU pro objekt BLOB pomocí [Azure Storage klientské knihovny pro .NET](/dotnet/api/overview/azure/storage/client).

Informace o účtu jsou k dispozici pro verze služby od verze 2018-03-28.

## <a name="about-account-type-and-sku-name"></a>Informace o typu účtu a názvu SKU

**Typ účtu**: Platné typy účtů zahrnují `BlobStorage`, `BlockBlobStorage`, `FileStorage`, `Storage` a.`StorageV2` [Přehled účtu Azure Storage](storage-account-overview.md) obsahuje další informace, včetně popisů různých účtů úložiště.

**Název SKU**: Platné názvy SKU zahrnují `Premium_LRS`, `Premium_ZRS`, `Standard_GRS`, `Standard_GZRS`, `Standard_LRS`, ,`Standard_RAGRS` a`Standard_ZRS`. `Standard_RAGZRS` V názvech SKU jsou rozlišována velká a malá písmena a jsou řetězcová pole ve [třídě SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet).

## <a name="retrieve-account-information"></a>Načíst informace o účtu

K získání typu účtu úložiště a názvu SKU přidruženého k objektu BLOB volejte metodu [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) nebo [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet) .

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

[!INCLUDE [storage-blob-dotnet-resources](../../../includes/storage-blob-dotnet-resources.md)]

## <a name="next-steps"></a>Další postup

Přečtěte si o dalších operacích, které můžete provádět na účtu úložiště prostřednictvím [Azure Portal](https://portal.azure.com) a REST API Azure.

- [Správa účtu úložiště](storage-account-manage.md)
- [Upgrade účtu úložiště](storage-account-upgrade.md)
- [Operace získání informací o účtu (REST)](/rest/api/storageservices/get-account-information)
