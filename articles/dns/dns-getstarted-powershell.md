---
title: 'Rychlé zprovoznění: Vytvoření záznamu a zóny Azure DNS pomocí Azure PowerShellu'
description: Naučíte se vytvořit zónu a záznam DNS v DNS Azure. Pomocí tohoto podrobného postupu můžete vytvořit a spravovat první zónu a záznam DNS pomocí Azure PowerShellu.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 12/4/2018
ms.author: victorh
ms.openlocfilehash: 839c97ccccbc1ce2cf646afcd27894a190eda1b0
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000881"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>Rychlý start: Vytvoření Azure zóny a záznamu DNS pomocí Azure Powershellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

V tomto rychlém zprovoznění vytvoříte první záznam a zónu Azure DNS pomocí Azure PowerShellu. Tyto kroky můžete provést také pomocí webu [Azure Portal](dns-getstarted-portal.md) nebo [Azure CLI](dns-getstarted-cli.md). 

K hostování záznamů DNS v určité doméně se používá zóna DNS. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny záznamy DNS pro vaši doménu se pak vytvoří v této zóně DNS. Nakonec, pokud chcete zónu DNS publikovat na internetu, bude potřeba nakonfigurovat pro doménu názvové servery. Jednotlivé kroky jsou popsány níže.

Azure DNS také podporuje vytváření privátních domén. Podrobné pokyny k vytvoření první privátní zóny DNS a záznamu najdete v tématu [Začínáme s privátními zónami DNS Azure pomocí PowerShellu](private-dns-getstarted-powershell.md).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením zóny DNS vytvořte skupinu prostředků, která bude obsahovat zónu DNS.

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

Zóna DNS se vytvoří pomocí rutiny `New-AzDnsZone`. Následující příklad vytvoří zónu DNS s názvem *contoso.com* ve skupině prostředků s názvem *MyResourceGroup*. Nahraďte hodnoty vlastními a použijte tento příklad k vytvoření zóny DNS.

```powershell
New-AzDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>Vytvoření záznamu DNS

Sady záznamů vytvoříte pomocí rutiny `New-AzDnsRecordSet`. Následující příklad vytvoří záznam s relativním názvem „www“ v zóně DNS „contoso.com“ ve skupině prostředků „MyResourceGroup“. Plně kvalifikovaný název sady záznamů je „www.contoso.com“. Typ záznamu je A, IP adresa je 1.2.3.4 a hodnota TTL je 3 600 sekund.

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4")
```

## <a name="view-records"></a>Zobrazení záznamů

K výpisu záznamů DNS ve vaší zóně použijte:

```powershell
Get-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="update-name-servers"></a>Aktualizace názvových serverů

Jakmile budete spokojeni se správným nastavením zóny a záznamů DNS, bude potřeba nakonfigurovat váš název domény tak, aby používal názvové servery DNS Azure. Tím umožníte ostatním uživatelům na internetu najít vaše záznamy DNS.

Názvové servery vaší zóny můžete zobrazit rutinou `Get-AzDnsZone`:

```powershell
Get-AzDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-b40d-0996b97ed101
Tags                  : {}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
NumberOfRecordSets    : 3
MaxNumberOfRecordSets : 5000
```

Tyto názvové servery by měly být nakonfigurované u registrátora názvu domény (u kterého jste zakoupili název domény). Registrátor vám nabídne možnost nastavit názvové servery pro doménu. Další informace najdete v tématu [kurzu: Hostování domény v Azure DNS](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="delete-all-resources"></a>Odstranění všech prostředků

Pokud už nejsou potřeba, můžete všechny prostředky vytvořené v rámci tohoto rychlého startu odstranit odstraněním této skupiny prostředků:

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Další postup

Teď když jste vytvořili svoji první zónu a záznam DNS pomocí Azure Powershellu, můžete vytvořit záznamy pro webovou aplikaci ve vlastní doméně.

> [!div class="nextstepaction"]
> [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně](./dns-web-sites-custom-domain.md)

