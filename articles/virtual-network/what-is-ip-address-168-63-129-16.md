---
title: Co je IP adresa 168.63.129.16? | Dokumentace Microsoftu
description: Přečtěte si o IP adrese 168.63.129.16 a o tom, jak funguje s vašimi zdroji.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 287f881fb17dd84357f540ee562e21c66c11ab95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77114359"
---
# <a name="what-is-ip-address-1686312916"></a>Co je IP adresa 168.63.129.16?

IP adresa 168.63.129.16 je virtuální veřejná IP adresa, která se používá k usnadnění komunikačního kanálu k prostředkům platformy Azure. Zákazníci můžou definovat libovolný adresní prostor pro svou privátní virtuální síť v Azure. Prostředky platformy Azure proto musí být prezentovány jako jedinečná veřejná IP adresa. Tato virtuální veřejná IP adresa usnadňuje následující věci:

- Umožňuje agentovi virtuálního počítače komunikovat s platformou Azure, aby signalizoval, že je ve stavu "Připraveno".
- Umožňuje komunikaci s virtuálním serverem DNS poskytovat filtrované překlad názvů prostředkům (například virtuálnímu počítači), které nemají vlastní server DNS. Toto filtrování zajišťuje, že zákazníci mohou vyřešit pouze názvy hostitelů svých prostředků.
- Umožňuje [sondy stavu z nástrojpro vyrovnávání zatížení Azure](../load-balancer/load-balancer-custom-probe-overview.md) k určení stavu virtuálních počítačích.
- Umožňuje virtuálnímu počítači získat dynamickou IP adresu ze služby DHCP v Azure.
- Povolí prezenční signál agenta hosta pro roli PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Rozsah IP adresy 168.63.129.16

Veřejná IP adresa 168.63.129.16 se používá ve všech regionech a ve všech národních cloudech. Tato speciální veřejná IP adresa je vlastněna společností Microsoft a nezmění se. Doporučujeme povolit tuto IP adresu v libovolné místní (v zásadách brány firewall virtuálního počítače) (směr odchozí). Komunikace mezi touto speciální IP adresou a prostředky je bezpečná, protože pouze interní platforma Azure může zdroj zprávy z této IP adresy. Pokud je tato adresa blokována, může dojít k neočekávanému chování v různých scénářích. 168.63.129.16 je [virtuální IP adresa hostitelského uzlu](../virtual-network/security-overview.md#azure-platform-considerations) a jako taková nepodléhá uživatelem definovaným tramám.

- Agent virtuálního zařízení vyžaduje odchozí komunikaci přes porty 80, 443, 32526 s WireServer (168.63.129.16). Ty by měly být otevřené v místní bráně firewall na virtuálním počítači. Komunikace na těchto portech s 168.63.129.16 nepodléhá nakonfigurovaným skupinám zabezpečení sítě.
- 168.63.129.16 může poskytovat služby DNS virtuálnímu virtuálnímu virtuálnímu ms. Pokud to není žádoucí, tento provoz může být blokován v místní bráně firewall na virtuálním počítači. Ve výchozím nastavení se komunikace DNS nevztahuje na nakonfigurované skupiny zabezpečení sítě, pokud není konkrétně cílené využití značky služby [AzurePlatformDNS.](../virtual-network/service-tags-overview.md#available-service-tags)
- Když je virtuální měnový tým součástí back-endového fondu vyrovnávání zatížení, měla by být povolena komunikace [sondy stavu](../load-balancer/load-balancer-custom-probe-overview.md) z 168.63.129.16. Výchozí konfigurace skupiny zabezpečení sítě má pravidlo, které umožňuje tuto komunikaci. Toto pravidlo využívá značku služby [AzureLoadBalancer.](../virtual-network/service-tags-overview.md#available-service-tags) V případě potřeby může být tento provoz blokován konfigurací skupiny zabezpečení sítě, ale to bude mít za následek sondy, které se nezdaří.

Ve scénáři bez virtuální sítě (Classic) je sonda stavu získávána z privátní IP adresy a 168.63.129.16 se nepoužívá.

## <a name="next-steps"></a>Další kroky

- [Skupiny zabezpečení](security-overview.md)
- [Vytvoření, změna nebo odstranění skupiny zabezpečení sítě](manage-network-security-group.md)
