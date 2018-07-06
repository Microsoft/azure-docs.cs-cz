---
title: Povolení zabezpečeného protokolu TLS pro klienta služby Azure Storage | Dokumentace Microsoftu
description: Informace o povolení protokolu TLS 1.2 v klientovi služby Azure Storage.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/25/2018
ms.author: fryu
ms.openlocfilehash: 6c313b6015a8a6dcc4ca5befb5fef70b047d0410
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866521"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Povolení zabezpečeného TLS pro klienta Azure Storage

Když budete potřebovat k auditování služby pomocí služby Azure Storage na základě nejnovější dodržování předpisů a požadavky na zabezpečení, SSL 1.0, 2.0, 3.0 a TLS 1.0, jsou rozpoznány jako nedodržující předpisy komunikační protokoly.

Bylo zjištěno SSL 1.0, 2.0 a 3.0 ohrožen. Mít bylo zakázáno v dokumentu RFC. Protokol TLS 1.0 stane nezabezpečené nezabezpečené blokových šifrách (CBC šifrování DES a RC2 CBC) a Stream šifrování (RC4). Rady PCI také navrhované přechodu na vyšší verze protokolu TLS. Další podrobnosti najdete v tématu [zabezpečení TLS (Transport Layer)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

Úložiště Azure od 2015 byla zastavena SSL 3.0 a používá TLS 1.2 na veřejné koncové body HTTPs, ale protokol TLS 1.0 a TLS 1.1 jsou stále podporovány z důvodu zpětné kompatibility.

Aby bylo možné zajistit zabezpečení a dodržování připojení ke službě Azure Storage, je potřeba povolit TLS 1.2 na straně klienta před odesláním žádosti o provoz služby Azure Storage.

## <a name="enable-tls-12-in-net-client"></a>Protokol TLS 1.2 v .NET klienta

Pro klienta pro vyjednávání protokolu TLS 1.2, operační systém a verzi rozhraní .NET Framework obě musí podporovat protokol TLS 1.2. Další podrobnosti najdete v [podpora protokolu TLS 1.2](https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls#support-for-tls-12).

Následující příklad ukazuje, jak v klientovi .NET protokol TLS 1.2.

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

## <a name="enable-tls-12-in-powershell-client"></a>Protokol TLS 1.2 v klientovi prostředí PowerShell

Následující příklad ukazuje, jak protokol TLS 1.2 v klientovi prostředí PowerShell.

```powershell

# Enable TLS 1.2 before connecting to Azure Storage
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

$resourceGroup = "{YourResourceGropuName}"
$storageAccountName = "{YourStorageAccountNme}"
$prefix = "foo"

# Connect to Azure Storage
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccountName
$ctx = $storageAccount.Context
$listOfContainers = Get-AzureStorageContainer -Context $ctx -Prefix $prefix
$listOfContainers

```

## <a name="verify-tls-12-connection"></a>Ověření připojení protokolu TLS 1.2

Použití aplikace Fiddler k ověření, pokud se ve skutečnosti používá TLS 1.2. Otevřete Fiddler spustit zachytávání síťového provozu klienta poté spuštěním nad vzorku. Pak verze TLS najdete v připojení, které vzorku.

Na následujícím snímku obrazovky je příklad pro ověření.

![snímek obrazovky ověřování TLS verze ve Fiddleru](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Další informace najdete v tématech

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [Povolení protokolu TLS v klientskou sadou Java](https://www.java.com/en/configure_crypto.html)
