---
title: Předpona veřejné adresy IPv6 ve virtuální síti Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si o předponě veřejné adresy IPv6 ve virtuální síti Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 8254a7d86d5cadc2ddc03940f4ab2d08de74bd86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72965179"
---
# <a name="reserved-public-ipv6-address-prefix-preview"></a>Vyhrazená veřejná předpona adresy IPv6 (náhled)

V Azure jsou virtuální sítě (Virtuální sítě) (VNet) (Virtual) (Dual Stack) (IPv4+IPv6) a virtuální počítače ve výchozím nastavení zabezpečené, protože nemají připojení k Internetu. K vašim azure balancers balancers a virtuálním počítačům azure můžete snadno přidat připojení k Internetu iPv6 s veřejnými Adresami IPv6, které získáte z Azure.

Všechny veřejné IP adresy, které si rezervujete, jsou přidruženy k oblasti Azure podle vašeho výběru a k vašemu předplatnému Azure. Můžete přesunout vyhrazenou (statickou) veřejnou IP adresu IPv6 mezi libovolnými vykladači zatížení Azure nebo virtuálními počítači ve vašem předplatném. Veřejnou IP adresu IPv6 můžete zcela oddělit a bude se konat pro vaše použití, až budete připraveni.

> [!WARNING]
> Buďte opatrní, abyste omylem neodstranili veřejné IP adresy. Odstraněním veřejné IP adresy ji odeberete z předplatného a nebudete ji moct obnovit (ani s pomocí podpory Azure).

Kromě rezervace jednotlivých adres IPv6 můžete pro své použití rezervovat souvislé rozsahy adres Azure IPv6 (označované jako IP prefix).  Podobně jako u jednotlivých IP adres jsou vyhrazené předpony přidružené k oblasti Azure podle vašeho výběru a k vašemu předplatnému Azure. Rezervace předvídatelný, souvislý rozsah adres má mnoho použití. Můžete například výrazně zjednodušit *seznam povolených* IP aplikací vaší společnosti a vašich zákazníků, protože statické rozsahy IP adres lze snadno naprogramovat do místních bran firewall.  Jednotlivé veřejné IP adresy můžete vytvořit z předpony IP podle potřeby a když odstraníte tyto jednotlivé veřejné IP adresy, budou *vráceny* do vyhrazeného rozsahu, abyste je mohli později znovu použít. Všechny IP adresy v předponě IP jsou vyhrazeny pro vaše výhradní použití, dokud ji neodstraníte.

> [!Important]
> IPv6 pro virtuální síť Azure je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ipv6-prefix-sizes"></a>Velikosti předpon IPv6
K dispozici jsou následující velikosti předpony ip ve veřejné adrese:

-  Minimální velikost předpony IPv6: /127 = 2 adresy
-  Maximální velikost předpony IPv6: /124 = 16 adres

Velikost předpony je určena jako velikost masky CIDR (Classless Inter-Domain Routing). Například maska /128 představuje individuální adresu IPv6, protože adresy IPv6 se skládají ze 128 bitů.

## <a name="pricing"></a>Ceny
 
Náklady spojené s používáním veřejných IP adres Azure, jednotlivých IP adres a rozsahů IP adres, najdete v [tématu Ceny za veřejnou IP adresu](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="limitations"></a>Omezení
IPv6 je podporována na základní veřejné IP adresy pouze s "dynamické" přidělení, což znamená, že Adresa IPv6 se změní, pokud odstranit a znovu nasadit aplikace (virtuální počítače nebo vyrovnávání zatížení) v Azure. Standardní podpora veřejné IP adresy IPv6 je pouze statické (vyhrazené) přidělení, i když standardní interní vykladače zatížení mohou také podporovat dynamickou alokaci z podsítě, ke které jsou přiřazeny.  

Jako osvědčený postup doporučujeme použít standardní veřejné IP adresy a standardní vyrovnávání zatížení pro vaše aplikace IPv6.

## <a name="next-steps"></a>Další kroky
- Vyhradit [veřejnou předponu adresy IPv6](ipv6-reserve-public-ip-address-prefix.md).
- Další informace o [adresách IPv6](ipv6-overview.md).
- Přečtěte si, jak v Azure [vytvářet a používat veřejné IP adresy](virtual-network-public-ip-address.md) (IPv4 i IPv6).
