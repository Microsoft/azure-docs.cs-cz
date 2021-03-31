---
title: Skupiny IP adres v Azure Firewall
description: Skupiny IP adres umožňují seskupovat a spravovat IP adresy pro pravidla Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: victorh
ms.openlocfilehash: 907ed0774db0e07a61a3ad568e6c451ea5d37b44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102501732"
---
# <a name="ip-groups-in-azure-firewall"></a>Skupiny IP adres v Azure Firewall

Skupiny IP adres umožňují seskupit a spravovat IP adresy pro Azure Firewall pravidla následujícími způsoby:

- Jako zdrojová adresa v pravidlech DNAT
- Jako zdrojová nebo cílová adresa v pravidlech sítě
- Jako zdrojová adresa v pravidlech aplikací


Skupina IP adres může mít jednu IP adresu, několik IP adres nebo jeden nebo víc rozsahů IP adres.

Skupiny IP adres se dají znovu použít v Azure Firewall DNAT, síti a pravidel aplikací pro několik bran firewall v rámci oblastí a předplatných v Azure. Názvy skupin musí být jedinečné. Skupinu IP adres můžete nakonfigurovat v Azure Portal, Azure CLI nebo REST API. K dispozici je Ukázková šablona, která vám umožní začít.

> [!NOTE]
> Skupiny IP adres nejsou aktuálně k dispozici ve národních cloudových prostředích Azure.

## <a name="sample-format"></a>Formát ukázky

Následující příklady formátu IPv4 adres jsou platné pro použití ve skupinách IP:

- Jedna adresa: 10.0.0.0
- Zápis CIDR: 10.1.0.0/32
- Rozsah adres: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Vytvoření skupiny IP adres

Skupina IP adres se dá vytvořit pomocí Azure Portal, Azure CLI nebo REST API. Další informace najdete v tématu [Vytvoření skupiny IP adres](create-ip-group.md).

## <a name="browse-ip-groups"></a>Procházet skupiny IP adres
1. Na panelu hledání Azure Portal zadejte **skupiny IP adres** a vyberte je. Můžete zobrazit seznam skupin IP adres, nebo můžete vybrat **Přidat** a vytvořit novou skupinu IP adres.
2. Vyberte skupinu IP adres a otevřete stránku Přehled. Můžete upravit, přidat nebo odstranit IP adresy nebo skupiny IP adres.

   ![Přehled skupin IP adres](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Správa skupiny IP adres

Všechny IP adresy můžete zobrazit ve skupině IP adres a podle pravidel nebo prostředků, které jsou k němu přidružené. Pokud chcete odstranit skupinu IP adres, musíte nejdřív oddělit skupinu IP od prostředku, který ji používá.

1. Pokud chcete zobrazit nebo upravit IP adresy, v části **Nastavení** v levém podokně vyberte **IP adresy** .
2. Pokud chcete přidat jednu nebo více IP adres, vyberte **Přidat IP adresy**. Tím se otevře stránka pro odeslání **nebo procházení** , nebo můžete adresu zadat ručně.
3.    Výběr tří teček (**...**) na pravé straně pro úpravu nebo odstranění IP adres. Chcete-li upravit nebo odstranit více IP adres, zaškrtněte políčka a v horní části vyberte možnost **Upravit** nebo **Odstranit** .
4. Nakonec může soubor exportovat ve formátu souboru CSV.

> [!NOTE]
> Pokud odstraníte všechny IP adresy ve skupině IP adres, když se stále používá v pravidle, toto pravidlo se přeskočí.


## <a name="use-an-ip-group"></a>Použití skupiny IP adres

Při vytváření Azure Firewallch DNAT, aplikací nebo síťových pravidel teď můžete vybrat možnost **Skupina IP** adres jako **typ zdroje** nebo **cílový typ** pro IP adresu (y).

![Skupiny IP adres v bráně firewall](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Dostupnost v oblastech

Skupiny IP adres jsou dostupné ve všech oblastech veřejného cloudu.

## <a name="ip-address-limits"></a>Omezení IP adres

Můžete mít maximálně 100 skupin IP adres na bránu firewall s maximálním počtem 5000 individuálních IP adres nebo předpon IP adres na každou skupinu IP adres.

## <a name="related-azure-powershell-cmdlets"></a>Související rutiny Azure PowerShell

K vytváření a správě skupin IP adres se dají použít následující rutiny Azure PowerShell:

- [New-AzIpGroup](/powershell/module/az.network/new-azipgroup)
- [Remove-AzIPGroup](/powershell/module/az.network/remove-azipgroup)
- [Get-AzIpGroup](/powershell/module/az.network/get-azipgroup)
- [Set-AzIpGroup](/powershell/module/az.network/set-azipgroup)
- [New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
- [New-AzFirewallApplicationRule](/powershell/module/az.network/new-azfirewallapplicationrule)
- [New-AzFirewallNatRule](/powershell/module/az.network/new-azfirewallnatrule)

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nasadit a nakonfigurovat Azure firewall](tutorial-firewall-deploy-portal.md).