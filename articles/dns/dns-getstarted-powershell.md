---
title: Začínáme s DNS Azure pomocí PowerShellu | Dokumentace Microsoftu
description: Naučíte se vytvořit zónu a záznam DNS v DNS Azure. Pomocí tohoto podrobného průvodce můžete vytvořit a spravovat první zónu a záznam DNS pomocí PowerShellu.
services: dns
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: kumud
ms.openlocfilehash: 050111f4a5e8459e89d049ccb879b5079ff68527
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-dns-using-powershell"></a>Začínáme s DNS Azure pomocí PowerShellu

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

Tento článek vás provede kroky k vytvoření první zóny a záznamu DNS pomocí Azure PowerShellu. Tyto kroky můžete provést také pomocí webu Azure Portal nebo Azure CLI pro různé platformy. Azure DNS také podporuje vytváření privátních domén. Podrobné pokyny k vytvoření první privátní zóny DNS a záznamu najdete v tématu [Začínáme s privátními zónami DNS Azure pomocí PowerShellu](private-dns-getstarted-powershell.md).

K hostování záznamů DNS v určité doméně se používá zóna DNS. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny záznamy DNS pro vaši doménu se pak vytvoří v této zóně DNS. Nakonec, pokud chcete zónu DNS publikovat na internetu, bude potřeba nakonfigurovat pro doménu názvové servery. Jednotlivé kroky jsou popsány níže.

Tyto pokyny předpokládají, že již máte nainstalované prostředí Azure PowerShell a jste k němu přihlášeni. Nápovědu získáte v tématu [Správa zón DNS pomocí PowerShellu](dns-operations-dnszones.md).

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením zóny DNS se vytvoří skupina prostředků, která bude obsahovat zónu DNS. Následuje ukázka příkazu.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

Zóna DNS se vytvoří pomocí rutiny `New-AzureRmDnsZone`. Následující příklad vytvoří zónu DNS s názvem *contoso.com* ve skupině prostředků s názvem *MyResourceGroup*. Nahraďte hodnoty vlastními a použijte tento příklad k vytvoření zóny DNS.

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
```
Azure DNS teď podporuje také privátní zóny DNS (aktuálně ve verzi Public Preview).  Další informace o privátních zónách DNS najdete v tématu [Použití DNS Azure pro privátní domény](private-dns-overview.md). Příklad vytvoření privátní zóny DNS najdete v tématu [Začínáme s privátními zónami Azure DNS pomocí PowerShellu](./private-dns-getstarted-powershell.md).

## <a name="create-a-dns-record"></a>Vytvoření záznamu DNS

Sady záznamů vytvoříte pomocí rutiny `New-AzureRmDnsRecordSet`. Následující příklad vytvoří záznam s relativním názvem „www“ v zóně DNS „contoso.com“ ve skupině prostředků „MyResourceGroup“. Plně kvalifikovaný název sady záznamů je „www.contoso.com“. Typ záznamu je A, IP adresa je 1.2.3.4 a hodnota TTL je 3 600 sekund.

```powershell
New-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

Informace o dalších typech záznamů, sadách záznamů s více než jedním záznamem a úpravě existujících záznamů najdete v tématu [Správa záznamů a sad záznamů DNS pomocí Azure PowerShellu](dns-operations-recordsets.md). 


## <a name="view-records"></a>Zobrazení záznamů

K výpisu záznamů DNS ve vaší zóně použijte:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```


## <a name="update-name-servers"></a>Aktualizace názvových serverů

Jakmile budete spokojeni se správným nastavením zóny a záznamů DNS, bude potřeba nakonfigurovat váš název domény tak, aby používal názvové servery DNS Azure. Tím umožníte ostatním uživatelům na internetu najít vaše záznamy DNS.

Názvové servery vaší zóny můžete zobrazit rutinou `Get-AzureRmDnsZone`:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-b40d-0996b97ed101
Tags                  : {}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
NumberOfRecordSets    : 3
MaxNumberOfRecordSets : 5000
```

Tyto názvové servery by měly být nakonfigurované u registrátora názvu domény (u kterého jste zakoupili název domény). Registrátor vám nabídne možnost nastavit názvové servery pro doménu. Další informace najdete v tématu [Delegování domény do DNS Azure](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Odstranění všech prostředků

Pokud chcete odstranit všechny prostředky vytvořené v rámci tohoto článku, proveďte následující krok:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

Další informace o DNS Azure najdete v tématu [Přehled DNS Azure](dns-overview.md).

Další informace o správě zón DNS v DNS Azure najdete v tématu [Správa zón DNS v DNS Azure pomocí PowerShellu](dns-operations-dnszones.md).

Další informace o správě záznamů DNS v DNS Azure najdete v tématu [Správa záznamů a sad záznamů DNS v DNS Azure pomocí PowerShellu](dns-operations-recordsets.md).

