---
title: Delegování subdomény – Azure PowerShell – Azure DNS
description: S tímto studijním programem můžete začít delegovat subdoménu Azure DNS pomocí Azure PowerShellu.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: 7e019afaae98422b8d5a3c8fa7a5f79e26c6a149
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937709"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Delegování subdomény Azure DNS pomocí Azure PowerShellu

Azure PowerShell můžete použít k delegování subdomény DNS. Pokud například vlastníte contoso.com domény, můžete delegovat subdoménu nazvanou *inženýrství* do jiné samostatné zóny, kterou můžete spravovat odděleně od zóny contoso.com.

Pokud chcete, můžete delegovat subdoménu pomocí [portálu Azure Portal](delegate-subdomain.md).

> [!NOTE]
> Contoso.com se používá jako příklad v celém tomto článku. Doménu contoso.com nahraďte vlastním názvem domény.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li delegovat subdoménu Azure DNS, musíte nejprve delegovat svou veřejnou doménu na Azure DNS. Pokyny, jak nakonfigurovat názvové servery pro delegování, najdete v tématu [Delegování domény na Azure DNS.](./dns-delegate-domain-azure-dns.md) Jakmile je vaše doména delegována do zóny Azure DNS, můžete delegovat svou subdoménu.

## <a name="create-a-zone-for-your-subdomain"></a>Vytvoření zóny pro subdoménu

Nejprve vytvořte zónu pro **inženýrskou** subdoménu.

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Poznamenejte si názvové servery

Dále si všimněte čtyř názvových serverů pro inženýrskou subdoménu.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Vytvoření testovacího záznamu

Vytvořte záznam **A** v technické zóně, který se použije pro testování.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>Vytvoření záznamu NS

Dále vytvořte záznam názvového serveru (NS) pro **inženýrskou** zónu v zóně contoso.com.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>Otestujte delegování

Použijte nslookup k testování delegování.

1. Otevřete okno PowerShellu.
2. Na příkazovém řádku zadejte`nslookup www.engineering.contoso.com.`
3. Měli byste obdržet neautoritativní odpověď s adresou **10.10.10.10**.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [nakonfigurovat reverzní DNS pro služby hostované v Azure](dns-reverse-dns-for-azure-services.md).