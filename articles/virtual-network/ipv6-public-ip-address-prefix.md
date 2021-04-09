---
title: Předpona veřejných adres IPv6 ve službě Azure Virtual Network
titlesuffix: Azure Virtual Network
description: Přečtěte si o předponě veřejných adres IPv6 ve službě Azure Virtual Network.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 3e3d3c01ba1190871da39ce27de6a95952d09be1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99538202"
---
# <a name="reserved-public-ipv6-address-prefix"></a>Vyhrazená předpona veřejných adres IPv6

Ve výchozím nastavení jsou virtuální sítě (VNet) a virtuální počítače v Azure standardně zabezpečené, protože nemají žádné připojení k Internetu. Můžete snadno přidat připojení k Internetu s protokolem IPv6 k vašim nástrojům pro vyrovnávání zatížení Azure a virtuálním počítačům pomocí veřejných adres IPv6, které získáte z Azure.

Všechny veřejné IP adresy, které rezervujete, jsou přidružené k oblasti Azure podle vašeho výběru a s vaším předplatným Azure. Můžete přesunout rezervovanou (statickou) veřejnou IP adresu IPv6 mezi libovolnými nástroji pro vyrovnávání zatížení Azure nebo virtuálními počítači v rámci vašeho předplatného. Veřejnou IP adresu IPv6 můžete oddělit výhradně a bude se používat pro vaše použití, až budete připraveni.

> [!WARNING]
> Nechtěně neodstraňujte veřejné IP adresy pomocí opatrnosti. Odstraněním veřejné IP adresy se odebere z vašeho předplatného a nebudete je moct obnovit (ani s podporou Azure Support).

Kromě rezervace individuálních adres IPv6 můžete pro své použití rezervovat souvislé rozsahy adres IPv6 pro Azure (označované jako předpona IP adresy).  Podobně jako jednotlivé IP adresy jsou rezervované předpony přidružené k oblasti Azure podle vašeho výběru a s vaším předplatným Azure. Vyhrazení předvídatelného souvislého rozsahu adres má mnoho využití. Například můžete výrazně zjednodušit *filtrování* protokolu IP vašich aplikací hostovaných v Azure vaší společností a vašimi zákazníky, protože statické rozsahy IP adres je možné snadno programovat do místních bran firewall.  Podle potřeby můžete vytvořit jednotlivé veřejné IP adresy z předpony IP adres a při odstranění těchto jednotlivých veřejných IP adres, které se *vrátí* do rezervovaného rozsahu, abyste je mohli znovu použít později. Všechny IP adresy v rámci vaší předpony IP adres jsou vyhrazené pro vaše výhradní použití až do doby, kdy jste tuto předponu odstranili.



## <a name="ipv6-prefix-sizes"></a>Velikosti předpon IPv6
K dispozici jsou následující velikosti předpony veřejných IP adres:

-  Minimální velikost předpony IPv6:/127 = 2 adresy
-  Maximální velikost předpony IPv6:/124 = 16 adres

Velikost předpony je zadaná jako velikost masky CIDR (Inter-Domain Routing). Například maska/128 představuje konkrétní adresu IPv6, protože adresy IPv6 se skládají z 128 bitů.

## <a name="pricing"></a>Ceny
 
Náklady spojené s používáním veřejných IP adres Azure, individuálních IP adres a rozsahů IP adres najdete v tématu [ceny veřejných IP adres](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="limitations"></a>Omezení
Protokol IPv6 se podporuje jenom u základních veřejných IP adres, které jsou jenom v případě "dynamického" přidělení, což znamená, že se adresa IPv6 změní, když v Azure odstraníte a znovu nasadíte svoji aplikaci (nebo nástroje pro vyrovnávání zatížení). Standardní podpora veřejné IP adresy IPv6 je výhradně statická (rezervovaná) alokace, i když standardní interní nástroje pro vyrovnávání zatížení můžou podporovat dynamické přidělování i v rámci podsítě, ke které jsou přiřazené.  

Jako osvědčený postup doporučujeme, abyste pro aplikace IPv6 používali standardní veřejné IP adresy a standardní nástroje pro vyrovnávání zatížení.

## <a name="next-steps"></a>Další kroky
- Rezervujte veřejnou [předponu adresy IPv6](ipv6-reserve-public-ip-address-prefix.md).
- Přečtěte si další informace o [adresách IPv6](ipv6-overview.md).
- Přečtěte si, [jak vytvořit a používat veřejné IP adresy](virtual-network-public-ip-address.md) (IPv4 a IPv6) v Azure.
