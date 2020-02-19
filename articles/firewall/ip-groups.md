---
title: Skupiny IP adres v Azure Firewall
description: Skupiny IP adres umožňují seskupovat a spravovat IP adresy pro pravidla Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 74e5a427d62d5249ffe6b0426b62a3577e43462f
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444482"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>Skupiny IP adres (Preview) v Azure Firewall

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Skupiny IP adres umožňují seskupit a spravovat IP adresy pro Azure Firewall pravidla následujícími způsoby:

- Jako zdrojová adresa v pravidlech DNAT
- Jako zdrojová nebo cílová adresa v pravidlech sítě
- Jako zdrojová adresa v pravidlech aplikací


Skupina IP adres může mít jednu IP adresu, několik IP adres nebo jeden nebo víc rozsahů IP adres.

Skupiny IP adres se dají znovu použít v Azure Firewall DNAT, síti a pravidel aplikací pro několik bran firewall v rámci oblastí a předplatných v Azure. Názvy skupin musí být jedinečné. Skupinu IP adres můžete nakonfigurovat v Azure Portal, Azure CLI nebo REST API. K dispozici je Ukázková šablona, která vám umožní začít.

## <a name="sample-format"></a>Vzorový formát

Následující příklady formátu IPv4 adres jsou platné pro použití ve skupinách IP:

- Jedna adresa: 10.0.0.0
- Zápis CIDR: 10.1.0.0/32
- Rozsah adres: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Vytvoření skupiny IP adres

Skupina IP adres se dá vytvořit pomocí Azure Portal, Azure CLI nebo REST API. Další informace najdete v tématu [Vytvoření skupiny IP adres (Preview)](create-ip-group.md).

## <a name="browse-ip-groups"></a>Procházet skupiny IP adres
1. Na panelu hledání Azure Portal zadejte **skupiny IP adres** a vyberte je. Můžete zobrazit seznam skupin IP adres, nebo můžete vybrat **Přidat** a vytvořit novou skupinu IP adres.
2. Vyberte skupinu IP adres a otevřete stránku Přehled. Můžete upravit, přidat nebo odstranit IP adresy nebo skupiny IP adres.

   ![Přehled skupin IP adres](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Správa skupiny IP adres

Všechny IP adresy můžete zobrazit ve skupině IP adres a podle pravidel nebo prostředků, které jsou k němu přidružené. Pokud chcete odstranit skupinu IP adres, musíte nejdřív oddělit skupinu IP od prostředku, který ji používá.

1. Pokud chcete zobrazit nebo upravit IP adresy, v části **Nastavení** v levém podokně vyberte **IP adresy** .
2. Pokud chcete přidat jednu nebo více IP adres, vyberte **Přidat IP adresy**. Tím se otevře stránka pro odeslání **nebo procházení** , nebo můžete adresu zadat ručně.
3.  Výběr tří teček ( **...** ) na pravé straně pro úpravu nebo odstranění IP adres. Chcete-li upravit nebo odstranit více IP adres, zaškrtněte políčka a v horní části vyberte možnost **Upravit** nebo **Odstranit** .
4. Nakonec může soubor exportovat ve formátu souboru CSV.

> [!NOTE]
> Pokud odstraníte všechny IP adresy ve skupině IP adres, když se stále používá v pravidle, toto pravidlo se přeskočí.


## <a name="use-an-ip-group"></a>Použití skupiny IP adres

Při vytváření Azure Firewallch DNAT, aplikací nebo síťových pravidel teď můžete vybrat možnost **Skupina IP** adres jako **typ zdroje** nebo **cílový typ** pro IP adresu (y).

> [!NOTE]
> Skupiny IP adres nejsou v zásadách brány firewall podporovány. V současné době se podporuje jenom s tradičními pravidly brány firewall.

![Skupiny IP adres v bráně firewall](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Dostupnost v oblastech

Skupiny IP adres jsou aktuálně k dispozici v následujících oblastech:

- USA – západ
- USA – západ 2
- USA – východ
- USA – východ 2
- USA – střed
- USA – středosever
- USA – středozápad
- USA – středojih
- Kanada – střed
- Severní Evropa
- Západní Evropa
- Francie – střed
- Velká Británie – jih
- Austrálie – východ
- Austrálie – střed
- Austrálie – jihovýchod

## <a name="related-azure-powershell-cmdlets"></a>Související rutiny Azure PowerShell

K vytváření a správě skupin IP adres se dají použít následující rutiny Azure PowerShell:

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Remove-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [New-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [New-AzFirewallNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nasadit a nakonfigurovat Azure firewall](tutorial-firewall-deploy-portal.md).