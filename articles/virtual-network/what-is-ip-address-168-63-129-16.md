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
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: acfd5230d1bd572ea5179651558e3f736a8570af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65833119"
---
# <a name="what-is-ip-address-1686312916"></a>Jaká je adresa IP adresy 168.63.129.16?

IP adresy 168.63.129.16 je virtuální veřejnou IP adresu, která slouží k usnadnění komunikační kanál, který prostředky platformy Azure. Zákazníci mohou definovat libovolný adresní prostor pro jejich privátní virtuální síť v Azure. Proto musí být prostředky platformy Azure zobrazen jako jedinečnou veřejnou IP adresu. Tato virtuální veřejná IP adresa umožňuje následující věci:

- Povolí agenta virtuálního počítače ke komunikaci s platformou Azure, který signalizuje, že je ve stavu "Připraveno".
- Umožňuje komunikaci s virtuálním serverem DNS k překladu názvů filtrované do prostředky (například virtuální počítač), které nemají vlastní server DNS. Toto filtrování zajišťuje, že zákazníci lze vyřešit pouze názvy hostitelů prostředků.
- Umožňuje [sondy stavu ze služby Azure load balancer](../load-balancer/load-balancer-custom-probe-overview.md) k určování stavu virtuálních počítačů.
- Umožňuje virtuálnímu počítači můžete získat dynamickou IP adresu ze služby DHCP v Azure.
- Umožňuje zprávy prezenčního signálu agenta hosta pro PaaS role.

## <a name="scope-of-ip-address-1686312916"></a>Rozsah IP adresy 168.63.129.16

Veřejné IP adresy 168.63.129.16 se používá ve všech oblastech a všech národních cloudech. Tato speciální veřejná IP adresa není ve vlastnictví společnosti Microsoft a nezmění. Je povoleno podle výchozího pravidla skupiny zabezpečení sítě. Doporučujeme povolit tuto IP adresu v rámci zásad žádné místní brány firewall. Komunikace mezi tento speciální IP adresu a prostředky je bezpečné, protože pouze interní platformy Azure mají možnost zprávy z této IP adresy. Pokud tato adresa je blokovaný, může dojít k neočekávanému chování v různých scénářích.

[Sondy stavu služby Azure Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md) pochází z této IP adresy. Pokud zablokujete tato IP adresa, vaše testy se nezdaří.

V případě mimo virtuální síť (Classic) v sondu stavu pochází z privátní IP adresy a adresy 168.63.129.16 nepoužívá.

## <a name="next-steps"></a>Další postup

- [Skupiny zabezpečení](security-overview.md)
- [Vytvoření, změna nebo odstranění skupiny zabezpečení sítě](manage-network-security-group.md)
