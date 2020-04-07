---
title: Skupiny IP v azure firewall
description: Skupiny IP umožňují seskupit a spravovat IP adresy pro pravidla Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: victorh
ms.openlocfilehash: e0638cbccd5e3bc282dbdd7d3b5918e29081a12b
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757158"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>Skupiny IP (preview) v Azure Firewall

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Skupiny IP umožňují seskupit a spravovat IP adresy pro pravidla azure brány Azure následujícími způsoby:

- Jako zdrojová adresa v pravidlech DNAT
- Jako zdrojová nebo cílová adresa v síťových pravidlech
- Jako zdrojová adresa v pravidlech aplikace


Skupina IP může mít jednu IP adresu, více IP adres nebo jeden nebo více rozsahů IP adres.

Skupiny IP se můžou znovu použít v DNAT azure firewallu, v pravidlech sítě a aplikací pro více bran firewall napříč oblastmi a předplatnými v Azure. Názvy skupin musí být jedinečné. Skupinu IP můžete nakonfigurovat na webu Azure Portal, Azure CLI nebo rozhraní REST API. K dispozici je ukázková šablona, která vám pomůže začít.

## <a name="sample-format"></a>Ukázkový formát

Následující příklady formátu adresy IPv4 jsou platné pro použití ve skupinách IP:

- Jedna adresa: 10.0.0.0
- CIDR zápis: 10.1.0.0/32
- Rozsah adres: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Vytvoření skupiny IP

Skupinu IP lze vytvořit pomocí portálu Azure, rozhraní API Azure nebo rozhraní REST API. Další informace naleznete [v tématu Vytvoření skupiny IP (náhled)](create-ip-group.md).

## <a name="browse-ip-groups"></a>Procházet skupiny IP
1. Na panelu hledání na portálu Azure zadejte **IP skupiny** a vyberte ho. Můžete zobrazit seznam skupin IP nebo můžete vybrat **Přidat** a vytvořit novou skupinu IP.
2. Vyberte skupinu IP, chcete-li otevřít stránku s přehledem. Můžete upravovat, přidávat nebo odstraňovat adresy IP nebo skupiny IP.

   ![Přehled skupin IP](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Správa skupiny IP

Můžete zobrazit všechny IP adresy ve skupině IP a pravidla nebo prostředky, které jsou k ní přidruženy. Chcete-li odstranit skupinu IP, musíte nejprve oddělit skupinu IP od prostředku, který ji používá.

1. Chcete-li zobrazit nebo upravit adresy IP, vyberte v části **Nastavení** v levém podokně **možnost Adresy IP.**
2. Chcete-li přidat jednu nebo více adres IP, vyberte **přidat adresy IP**. Otevře se stránka **Přetažení nebo Procházet** pro nahrání nebo můžete adresu zadat ručně.
3.    Výběrem elipsy (**...**) vpravo upravit nebo odstranit IP adresy. Pokud chcete upravit nebo odstranit více IP adres, vyberte pole a nahoře vyberte **Upravit** nebo **Odstranit.**
4. Nakonec můžete exportovat soubor ve formátu csv souboru.

> [!NOTE]
> Pokud odstraníte všechny adresy IP ve skupině IP, zatímco je stále používána v pravidle, bude toto pravidlo přeskočeno.


## <a name="use-an-ip-group"></a>Použití skupiny IP

Skupinu **IP** teď můžete vybrat jako **typ zdroje** nebo **typ cíle** pro IP adresy při vytváření pravidel DNAT, aplikace nebo sítě azure firewall.

> [!NOTE]
> Skupiny IP nejsou v zásadách brány firewall podporovány. V současné době je podporován pouze s tradičními pravidly brány firewall.

![Skupiny IP v bráně firewall](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Dostupnost v oblastech

Skupiny IP jsou k dispozici ve všech oblastech veřejného cloudu.

## <a name="ip-address-limits"></a>Limity IP adres

U 50 skupin IP nebo méně můžete mít maximálně 5000 jednotlivých IP adres na každou instanci brány firewall. U 51 až 100 skupin IP můžete mít 500 jednotlivých IP adres pro každou instanci brány firewall.

### <a name="examples"></a>Příklady

#### <a name="example-1-supported"></a>Příklad 1: podporováno

|Skupiny IP adres  |# IP adresy  |Notace  |Pravidlo  |
|---------|---------|---------|---------|
|Skupina IPGroup1 |4 096     |10.0.0.0/20  |Pravidlo 1|
|Skupina IPGroup2     |3|196.0.0.0 - 196.0.0.2|Pravidlo 1|
|Skupina IP3     |1|1.2.3.4|Pravidlo 1|
|     |**Celkem 4100**|         |         |
|     |         |         |         |

#### <a name="example-2-supported"></a>Příklad 2: podporováno

|Skupiny IP adres  |# IP adresy  |Notace  |Pravidlo  |
|---------|---------|---------|---------|
|Skupina IPGroup1 |4 096     |10.0.0.0/20  |Pravidlo 1|
|Skupina IPGroup2     |4 096|11.0.0.0/20|Pravidlo 1|
|     |**Celkem 8192**|         |         |

#### <a name="example-3-not-supported"></a>Příklad 3: není podporováno

|Skupiny IP adres  |# IP adresy  |Notace  |Pravidlo  |
|---------|---------|---------|---------|
|Skupina IPGroup1 |8192     |10.0.0.0/20, 11.0.0.0/20  |Pravidlo 1|
|     |**Celkem 8192**|||

#### <a name="example-4-supported"></a>Příklad 4: podporováno

|Skupiny IP adres  |# IP adresy  |Notace  |Pravidlo  |
|---------|---------|---------|---------|
|Skupina IPGroup1 |4 096     |10.0.0.0/20  |Pravidlo 1|
|Skupina IPGroup2     |4 096|11.0.0.0/20|Pravidlo 2|
|     |**Celkem 8192**|         |         |


## <a name="related-azure-powershell-cmdlets"></a>Související rutiny prostředí Azure PowerShell

K vytváření a správě skupin IP lze použít následující rutiny prostředí Azure PowerShell:

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Odebrat-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [Nové pravidlo sítě AzFirewallNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [Nové pravidlo aplikace AzFirewallApplication](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [Nové pravidlo AzFirewallNat](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nasadit a nakonfigurovat bránu Azure Firewall](tutorial-firewall-deploy-portal.md).