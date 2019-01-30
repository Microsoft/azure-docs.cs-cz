---
title: Jaká je adresa IP adresy 168.63.129.16? | Dokumenty Microsoft
description: Další informace o IP adresy 168.63.129.16 a jak funguje s vašimi prostředky.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2019
ms.author: genli
ms.openlocfilehash: e018cbf0c71a9acf76e60f38aff1aa1ba8a81516
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55229312"
---
# <a name="what-is-ip-address-1686312916"></a>Jaká je adresa IP adresy 168.63.129.16?

IP adresy 168.63.129.16 je virtuální veřejnou IP adresu, která slouží k usnadnění komunikační kanál, který prostředky platformy Azure. Zákazníci mohou definovat libovolný adresní prostor pro jejich privátní virtuální síť v Azure. Proto musí být prostředky platformy Azure zobrazen jako jedinečnou veřejnou IP adresu. Tato virtuální veřejná IP adresa umožňuje následující věci:

- Povolí agenta virtuálního počítače ke komunikaci s platformou Azure, který signalizuje, že je ve stavu "Připraveno".
- Umožňuje komunikaci s virtuálním serverem DNS k překladu názvů filtrované do prostředky (například virtuální počítač), které nemají vlastní server DNS. Toto filtrování zajišťuje, že zákazníci lze vyřešit pouze názvy hostitelů prostředků.
- Umožňuje sond stavu z nástroje pro vyrovnávání zatížení k určování stavu virtuálních počítačů v sadě s vyrovnáváním zatížení.
- Umožňuje zprávy prezenčního signálu agenta hosta pro PaaS role.

## <a name="scope-of-ip-address-1686312916"></a>Rozsah IP adresy 168.63.129.16

Virtuální veřejné IP adresy 168.63.129.16 se používá ve všech oblastech a všech národních cloudech. Tato speciální veřejná IP adresa se nezmění. Je povoleno podle výchozího pravidla skupiny zabezpečení sítě. Doporučujeme povolit tuto IP adresu v rámci zásad žádné místní brány firewall. Komunikace mezi tento speciální IP adresu a prostředky je bezpečné, protože pouze interní platformy Azure mají možnost zprávy z této IP adresy. Pokud tato adresa je blokovaný, může dojít k neočekávanému chování v různých scénářích.

Kromě toho traffics z veřejné virtuální IP adresy 168.63.129.16 na koncový bod nakonfigurovaný [sondy stavu nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md) by neměly být zahrnuté jako provoz útoku. Ve scénáři mimo virtuální síť sondy stavu pochází z privátní IP adresy.


## <a name="next-steps"></a>Další postup

- [Skupiny zabezpečení](security-overview.md)
- [Vytvoření, změna nebo odstranění skupiny zabezpečení sítě](manage-network-security-group.md)
