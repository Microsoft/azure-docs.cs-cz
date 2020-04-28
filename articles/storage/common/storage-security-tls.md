---
title: Povolení zabezpečeného TLS pomocí .NET
titleSuffix: Azure Storage
description: Přečtěte si, jak povolit TLS 1,2 pomocí klientské knihovny .NET pro Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 81c9a8fe9513f1f8fc65ad64b34f0fb04383569b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "75371798"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Povolení zabezpečeného TLS pro klienta Azure Storage

Protokol TLS (Transport Layer Security) a SSL (Secure Sockets Layer) (SSL) jsou kryptografické protokoly, které poskytují zabezpečení komunikace v počítačové síti. Bylo zjištěno, že SSL 1,0, 2,0 a 3,0 budou zranitelné. Dokument RFC zakázal. Protokol TLS 1,0 se nezabezpečuje pro použití CBCu nezabezpečeného blokového šifrování (DES a RC2 CBC) a Stream šifry (RC4). Rada PCI také navrhla migraci na vyšší verze TLS. Další podrobnosti najdete v tématu [TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

Azure Storage zastavil protokol SSL 3,0 od 2015 a používá TLS 1,2 u veřejných koncových bodů HTTPs, ale TLS 1,0 a TLS 1,1 se pořád podporuje kvůli zpětné kompatibilitě.

Aby bylo zajištěno zabezpečené a kompatibilní připojení Azure Storage, je nutné povolit TLS 1,2 nebo novější verzi na straně klienta před odesláním žádostí o provozování služby Azure Storage.

## <a name="enable-tls-12-in-net-client"></a>Povolení protokolu TLS 1.2 v klientovi .NET

Aby mohl klient vyjednávat TLS 1,2, musí operační systém a .NET Framework verze podporovat protokol TLS 1,2. Další podrobnosti najdete v článku [Podpora TLS 1,2](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).

Následující příklad ukazuje, jak povolit TLS 1,2 ve vašem klientovi .NET.

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

Následující příklad ukazuje, jak povolit TLS 1,2 ve vašem klientovi PowerShellu.

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

## <a name="verify-tls-12-connection"></a>Ověření připojení TLS 1,2

Pomocí Fiddler můžete ověřit, jestli se skutečně používá TLS 1,2. Otevřete Fiddler a spusťte zachytávání provozu sítě klienta a potom spusťte výše uvedený vzorek. Pak můžete najít verzi TLS v připojení, které ukázka provede.

Následující snímek obrazovky je ukázka pro ověření.

![snímek obrazovky s ověřením verze TLS v Fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Viz také

* [Protokol TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [Dodržování předpisů PCI v TLS](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [Povolit TLS v klientovi Java](https://www.java.com/en/configure_crypto.html)
