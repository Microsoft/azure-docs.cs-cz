---
title: Konfigurace protokolu TLS (Transport Layer Security) pro klientskou aplikaci
titleSuffix: Azure Storage
description: Nakonfigurujte klientskou aplikaci pro komunikaci s Azure Storage s použitím minimální verze protokolu TLS (Transport Layer Security).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 37b8c79df5b208feea185292fa09c323b64fa27d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89001803"
---
# <a name="configure-transport-layer-security-tls-for-a-client-application"></a>Konfigurace protokolu TLS (Transport Layer Security) pro klientskou aplikaci

Pro účely zabezpečení může účet Azure Storage vyžadovat, aby klienti při odesílání požadavků používali minimální verzi protokolu TLS (Transport Layer Security). Pokud klient používá verzi TLS, která je nižší než minimální požadovaná verze, volání Azure Storage se nezdaří. Pokud třeba účet úložiště vyžaduje protokol TLS 1,2, požadavek odeslaný klientem, který používá protokol TLS 1,1, se nezdaří.

Tento článek popisuje, jak nakonfigurovat klientskou aplikaci, aby používala konkrétní verzi TLS. Informace o tom, jak nakonfigurovat minimální požadovanou verzi TLS pro účet Azure Storage, najdete v tématu [Konfigurace minimální požadované verze protokolu TLS (Transport Layer Security) pro účet úložiště](transport-layer-security-configure-minimum-version.md).

## <a name="configure-the-client-tls-version"></a>Konfigurace verze TLS klienta

Aby klient mohl odeslat žádost s určitou verzí TLS, musí operační systém tuto verzi podporovat.

Následující příklady ukazují, jak nastavit verzi TLS klienta na 1,2 z PowerShellu nebo .NET. .NET Framework používané klientem musí podporovat protokol TLS 1,2. Další informace najdete v tématu [Podpora TLS 1,2](/dotnet/framework/network-programming/tls#support-for-tls-12).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Následující příklad ukazuje, jak povolit TLS 1,2 v klientovi PowerShellu:

```powershell
# Set the TLS version used by the PowerShell client to TLS 1.2.
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

# Create a new container.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context
New-AzStorageContainer -Name "sample-container" -Context $ctx
```

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Následující příklad ukazuje, jak povolit TLS 1,2 v klientovi .NET pomocí verze 12 Azure Storage klientské knihovny:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Networking.cs" id="Snippet_ConfigureTls12":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Následující příklad ukazuje, jak povolit TLS 1,2 v klientovi .NET pomocí verze 11 Azure Storage klientské knihovny:

```csharp
static void EnableTls12()
{
    // Enable TLS 1.2 before connecting to Azure Storage
    System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

    // Add your connection string here.
    string connectionString = "";

    // Connect to Azure Storage and create a new container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
}
```

---

## <a name="verify-the-tls-version-used-by-a-client"></a>Ověření verze TLS používané klientem

Chcete-li ověřit, zda klient použil určenou verzi TLS k odeslání žádosti, můžete použít [Fiddler](https://www.telerik.com/fiddler) nebo podobný nástroj. Otevřete Fiddler a spusťte zachytávání provozu klientské sítě a potom spusťte jeden z příkladů v předchozí části. Podívejte se na trasování Fiddler a potvrďte, že se pro odeslání žádosti použila správná verze TLS, jak je znázorněno na následujícím obrázku.

:::image type="content" source="media/transport-layer-security-configure-client-version/fiddler-trace-tls-version.png" alt-text="Snímek obrazovky zobrazující Fiddler trasování, které indikuje verzi protokolu TLS použitou na vyžádání":::

## <a name="next-steps"></a>Další kroky

- [Konfigurace minimální požadované verze protokolu TLS (Transport Layer Security) pro účet úložiště](transport-layer-security-configure-minimum-version.md)
- [Doporučení zabezpečení pro úložiště objektů BLOB](../blobs/security-recommendations.md)
