---
title: Zabezpečené protokolem TLS pro klienta Azure Storage | Microsoft Docs
description: Informace o povolení TLS 1.2 v klientovi Azure Storage.
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
ms.openlocfilehash: 5c21df2b3bdeee6ac7c3956fe1cafa4f947dd6dd
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035801"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Zabezpečené protokolem TLS pro klienta Azure Storage

Pokud budete potřebovat k auditování vašim službám pomocí Azure Storage podle nejnovější dodržování předpisů a požadavky na zabezpečení SSL 1.0, 2.0, 3.0 a TLS 1.0 jsou rozpoznán jako nevyhovující komunikační protokoly.

Chcete-li být zranitelné v důsledku byly zjištěny SSL 1.0, 2.0 a 3.0. Mít bylo zakázáno v dokumentu RFC. Protokol TLS 1.0 stane nezabezpečené nezabezpečené bloku šifer (CBC šifrování DES a RC2 CBC) a šifrovací datového proudu (RC4). Rady PCI také navrhované migrace na vyšších verzí protokolu TLS. Další podrobnosti najdete v tématu [zabezpečení TLS (Transport Layer)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

Úložiště Azure od 2015 zastavil SSL 3.0 a používá TLS 1.2 na veřejné koncové body HTTPs, ale TLS 1.0 a protokol TLS 1.1 jsou stále podporovány pro zpětnou kompatibilitu.

Aby se zajistilo, zabezpečení a dodržování připojení do služby Azure Storage, musíte povolit TLS 1.2 na straně klienta před odesláním požadavky na provoz služby Azure Storage.

## <a name="enable-tls-12-in-net-client"></a>Povolení protokolu TLS 1.2 ve klient .NET

Pro klienta pro vyjednávání protokolu TLS 1.2, operačního systému a verzi rozhraní .NET Framework obě musí podporovat protokol TLS 1.2. Více informací najdete v [podpora protokolu TLS 1.2](https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls#support-for-tls-12).

Následující příklad ukazuje, jak povolit TLS 1.2 ve vašeho klienta rozhraní .NET.

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

## <a name="enable-tls-12-in-powershell-client"></a>Povolení protokolu TLS 1.2 ve klienta PowerShell

Následující příklad ukazuje, jak povolit TLS 1.2 ve vašeho klienta PowerShell.

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

Můžete použít aplikaci Fiddler k ověření, pokud se ve skutečnosti používá TLS 1.2. Otevřete aplikaci Fiddler zahájíte zachytávající síťový přenos klienta potom spusťte výše ukázka. Pak TLS verze najdete v připojení, které vzorku.

Na následujícím snímku obrazovky je ukázka pro ověření.

![snímek obrazovky ověřování TLS verze v aplikaci Fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Další informace najdete v tématech

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
