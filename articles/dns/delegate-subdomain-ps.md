---
title: Delegování subdomény Azure DNS pomocí Azure Powershellu
description: Naučíte se delegovat subdoménu Azure DNS pomocí Azure Powershellu.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: victorh
ms.openlocfilehash: 40b2a4d98e6269d9740856ba44c1043af75ce1b8
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55896659"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Delegování subdomény Azure DNS pomocí Azure Powershellu

Prostředí Azure PowerShell můžete použít pro delegování subdoméně DNS. Například pokud jste vlastníkem domény contoso.com, můžete subdoménu delegovat volá *engineering* jiný, samostatný zóny, který budete moct spravovat samostatně na zónu contoso.com.

Pokud dáváte přednost, můžete subdoménu pomocí delegovat [webu Azure Portal](delegate-subdomain.md).

> [!NOTE]
> Contoso.com slouží jako příklad v tomto článku. Doménu contoso.com nahraďte vlastním názvem domény.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>Požadavky

Delegovat subdoménu Azure DNS, musí nejprve přenést svoji veřejnou doménu do Azure DNS. Zobrazit [delegování domény do Azure DNS](./dns-delegate-domain-azure-dns.md) pokyny ke konfiguraci vaší názvové servery pro delegování. Once your domain is delegated to your Azure DNS zone, you can delegate your subdomain.

## <a name="create-a-zone-for-your-subdomain"></a>Create a zone for your subdomain

Nejprve vytvořit zónu **engineering** subdomény.

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Mějte na paměti názvové servery

V dalším kroku mějte na paměti čtyři názvové servery pro technický subdomény.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Vytvořit záznam testu

Vytvoření **A** záznamů v zóně engineering pro účely testování.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>Vytvoření záznamů NS

V dalším kroku vytvoření záznamu názvového serveru (NS) pro **engineering** zóny v zóně contoso.com.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>Test delegování

Nslookup použijte k otestování delegování.

1. Otevřete okno Powershellu.
2. Na příkazovém řádku zadejte `nslookup www.engineering.contoso.com.`
3. Měli byste obdržet neautoritativní odpovědí, který zobrazuje adresu **10.10.10.10**.

## <a name="next-steps"></a>Další postup

Zjistěte, jak [konfigurace reverzních záznamů DNS pro služby hostované v Azure](dns-reverse-dns-for-azure-services.md).