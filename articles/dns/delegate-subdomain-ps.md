---
title: Delegování subdomény-Azure PowerShell-Azure DNS
description: Pomocí této cesty výukového programu začněte s delegováním Azure DNS subdomény pomocí Azure PowerShell.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: 9b37d313aa5d8c2255b4e3be69831dfcb50238ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84712543"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Delegování Azure DNS subdomény pomocí Azure PowerShell

K delegování subdomény DNS můžete použít Azure PowerShell. Pokud například vlastníte doménu contoso.com, můžete poddoménu s názvem *inženýry* delegovat na jinou a samostatnou zónu, kterou můžete spravovat odděleně od zóny contoso.com.

Pokud budete chtít, můžete subdoménu delegovat pomocí webu [Azure Portal](delegate-subdomain.md).

> [!NOTE]
> Contoso.com se používá jako příklad v celém rámci tohoto článku. Doménu contoso.com nahraďte vlastním názvem domény.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li delegovat Azure DNS subdoménu, musíte nejprve delegovat veřejnou doménu na Azure DNS. Pokyny ke konfiguraci názvových serverů pro delegování najdete v tématu [delegování domény na Azure DNS](./dns-delegate-domain-azure-dns.md) . Jakmile je vaše doména delegovaná do vaší Azure DNS zóny, můžete svou subdoménu delegovat.

## <a name="create-a-zone-for-your-subdomain"></a>Vytvořit zónu pro subdoménu

Nejprve vytvořte zónu pro subdoménu **strojírenství** .

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Poznamenejte si názvové servery

Dále si poznamenejte čtyři názvové servery pro inženýrskou subdoménu.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Vytvořit záznam testu

Vytvořte záznam **A** v technické zóně, který se použije pro testování.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>Vytvoření záznamu NS

Dále vytvořte záznam názvového serveru (NS) pro **technickou** zónu v zóně contoso.com.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>Testování delegování

K otestování delegování použijte nástroj Nslookup.

1. Otevřete okno PowerShellu.
2. Na příkazovém řádku zadejte `nslookup www.engineering.contoso.com.`
3. Měli byste obdržet neautoritativní odpověď ukazující na adresu **10.10.10.10**.

## <a name="next-steps"></a>Další kroky

Naučte se [Konfigurovat reverzní DNS pro služby hostované v Azure](dns-reverse-dns-for-azure-services.md).