---
title: Povolení zabezpečeného tls s rozhraním .NET
titleSuffix: Azure Storage
description: Zjistěte, jak povolit TLS 1.2 pomocí knihovny klienta .NET pro Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 81c9a8fe9513f1f8fc65ad64b34f0fb04383569b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371798"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Povolení zabezpečeného TLS pro klienta Azure Storage

Zabezpečení transportní vrstvy (TLS) a SSL (Secure Sockets Layer) jsou kryptografické protokoly, které poskytují zabezpečení komunikace v počítačové síti. SSL 1.0, 2.0 a 3.0 byly shledány zranitelnými. RFC je zakázala. TLS 1.0 se stává nezabezpečený pro použití nezabezpečené blokové šifry (DES CBC a RC2 CBC) a streamové šifry (RC4). Rada PCI také navrhla přechod na vyšší verze TLS. Další podrobnosti naleznete v [tématu Zabezpečení transportní vrstvy (TLS).](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)

Azure Storage zastavil SSL 3.0 od roku 2015 a používá TLS 1.2 na veřejné koncové body HTTPs, ale TLS 1.0 a TLS 1.1 jsou stále podporovány pro zpětnou kompatibilitu.

Chcete-li zajistit zabezpečené a kompatibilní připojení k Úložišti Azure, musíte před odesláním požadavků na provoz služby Azure Storage povolit tls 1.2 nebo novější verzi na straně klienta.

## <a name="enable-tls-12-in-net-client"></a>Povolení protokolu TLS 1.2 v klientovi .NET

Aby klient mohl vyjednat TLS 1.2, operační systém i verze rozhraní .NET Framework potřebují podporovat TLS 1.2. Další podrobnosti naleznete v [části Podpora tls 1.2](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).

Následující ukázka ukazuje, jak povolit TLS 1.2 ve vašem klientovi .NET.

```csharp

    static void EnableTls12()
    {
        // Enable TLS 1.2 before connecting to Azure Storage
        System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

        // Connect to Azure Storage
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName={yourstorageaccount};AccountKey={yourstorageaccountkey};EndpointSuffix=core.windows.net");
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        CloudBlobContainer container = blobClient.GetContainerReference("foo");
        container.CreateIfNotExists();
    }

```

## <a name="enable-tls-12-in-powershell-client"></a>Povolení protokolu TLS 1.2 v klientovi PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)] 

Následující ukázka ukazuje, jak povolit TLS 1.2 v klientovi PowerShell.

```powershell
# Enable TLS 1.2 before connecting to Azure Storage
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

$resourceGroup = "{YourResourceGroupName}"
$storageAccountName = "{YourStorageAccountNme}"
$prefix = "foo"

# Connect to Azure Storage
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccountName
$ctx = $storageAccount.Context
$listOfContainers = Get-AzStorageContainer -Context $ctx -Prefix $prefix
$listOfContainers
```

## <a name="verify-tls-12-connection"></a>Ověření připojení TLS 1.2

Fiddler můžete použít k ověření, zda tls 1.2 se používá ve skutečnosti. Otevřete Fiddler a začněte zachycovat síťový provoz klienta a poté se spustí nad ukázkou. Pak můžete najít verzi TLS v připojení, které ukázka provede.

Následující snímek obrazovky je ukázkou pro ověření.

![snímek obrazovky s ověřením verze TLS v Fiddleru](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Viz také

* [Protokol TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [Shoda PCI na TLS](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [Povolení TLS v klientovi Java](https://www.java.com/en/configure_crypto.html)
