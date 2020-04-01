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
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 096dbcb6b6a732bd21622658f6f30c5158a821c5
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420522"
---
# <a name="reserved-public-ipv6-address-prefix"></a>Předpona adresy IPv6 vyhrazená veřejná

V Azure jsou virtuální sítě (Virtuální sítě) (VNet) (Virtual) (Dual Stack) (IPv4+IPv6) a virtuální počítače ve výchozím nastavení zabezpečené, protože nemají připojení k Internetu. K vašim azure balancers balancers a virtuálním počítačům azure můžete snadno přidat připojení k Internetu iPv6 s veřejnými Adresami IPv6, které získáte z Azure.

Všechny veřejné IP adresy, které si rezervujete, jsou přidruženy k oblasti Azure podle vašeho výběru a k vašemu předplatnému Azure. Můžete přesunout vyhrazenou (statickou) veřejnou IP adresu IPv6 mezi libovolnými vykladači zatížení Azure nebo virtuálními počítači ve vašem předplatném. Veřejnou IP adresu IPv6 můžete zcela oddělit a bude se konat pro vaše použití, až budete připraveni.

> [!WARNING]
> Buďte opatrní, abyste omylem neodstranili veřejné IP adresy. Odstraněním veřejné IP adresy ji odeberete z předplatného a nebudete ji moct obnovit (ani s pomocí podpory Azure).

Kromě rezervace jednotlivých adres IPv6 můžete pro své použití rezervovat souvislé rozsahy adres Azure IPv6 (označované jako IP prefix).  Podobně jako u jednotlivých IP adres jsou vyhrazené předpony přidružené k oblasti Azure podle vašeho výběru a k vašemu předplatnému Azure. Rezervace předvídatelný, souvislý rozsah adres má mnoho použití. Můžete například výrazně zjednodušit *seznam povolených* IP aplikací vaší společnosti a vašich zákazníků, protože statické rozsahy IP adres lze snadno naprogramovat do místních bran firewall.  Jednotlivé veřejné IP adresy můžete vytvořit z předpony IP podle potřeby a když odstraníte tyto jednotlivé veřejné IP adresy, budou *vráceny* do vyhrazeného rozsahu, abyste je mohli později znovu použít. Všechny IP adresy v předponě IP jsou vyhrazeny pro vaše výhradní použití, dokud ji neodstraníte.



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
