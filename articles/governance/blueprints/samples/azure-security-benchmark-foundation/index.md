---
title: Přehled ukázkového plánu Azure Security benchmark Foundation
description: Přehled a architektura ukázkového plánu Azure Security benchmark Foundation
ms.date: 02/12/2020
ms.topic: sample
ms.openlocfilehash: 4bbc5b94ea9b977a2b71edbf15cf5a7aa0566974
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487106"
---
# <a name="overview-of-the-azure-security-benchmark-foundation-blueprint-sample"></a>Přehled ukázkového plánu Azure Security benchmark Foundation

Ukázka návrhového plánu Azure Security test Foundation poskytuje sadu vzorů základní infrastruktury, které vám pomůžou vytvořit zabezpečené a kompatibilní prostředí Azure. Podrobný plán vám pomůže nasadit cloudovou architekturu, která nabízí řešení scénářů, které mají požadavky na akreditaci nebo dodržování předpisů. Tato ukázka v rámci tohoto základu plánu je rozšířením podrobného plánu [ukázkových testů Azure Security test](../azure-security-benchmark/index.md). Nasadí a nakonfiguruje hranice sítě, monitorování a další prostředky, které jsou v souladu se zásadami a dalšími guardrails definovanými v [srovnávacím testu zabezpečení Azure](../../../../security/benchmarks/index.yml).

## <a name="architecture"></a>Architektura

Základní prostředí, které vytvořila Tato ukázka podrobného plánu, je založené na objektech architektury [modelu hvězdicové lokality](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
Podrobný plán nasadí virtuální síť centrální sítě, která obsahuje běžné a sdílené prostředky, služby a artefakty, jako je Azure bastionu, brána a brána firewall pro připojení, správu a podsítě rámečků pro připojení, které hostují další/volitelnou správu, údržbu, správu a infrastrukturu připojení. Jedna nebo více virtuálních sítí s paprskem je nasazeno do hostitelských úloh aplikací, jako jsou webové a databázové služby. Virtuální sítě paprsků jsou připojené k virtuální síti rozbočovače pomocí partnerského vztahu virtuálních sítí Azure pro bezproblémové a zabezpečené připojení. Další paprsky je možné přidat změnou přiřazení ukázkového podrobného plánu nebo ručním vytvořením virtuální sítě Azure a partnerským vztahem k virtuální síti rozbočovače. Všechna externí připojení k virtuálním sítím a podsítím paprsků jsou nakonfigurovaná tak, aby směrovala přes virtuální síť centrální sítě a, přes bránu firewall, bránu a odkazy na správu.

:::image type="content" source="../../media/azure-security-benchmark-foundation/architecture.png" alt-text="Diagram ukázkové architektury plánu Azure Security benchmark Foundation" border="false":::

Tento podrobný plán nasadí několik služeb Azure, které poskytují zabezpečený, monitorovaný podnik připravený na podnikové úrovni. Toto prostředí tvoří:

- [Azure monitor protokoly](../../../../azure-monitor/platform/data-platform-logs.md) a účet úložiště Azure pro zajištění, že se protokoly o prostředcích, protokolech aktivit, metrikách a přenosech v síti ukládají do centrálního umístění pro snadné dotazování, analýzy, archivaci a upozorňování.
- [Azure Security Center](../../../../security-center/security-center-introduction.md) (standardní verze) pro zajištění ochrany před hrozbami pro prostředky Azure.
- [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md) v centru, které podporují podsítě pro připojení zpátky do místní sítě, příchozího a odchozího zásobníku pro připojení k Internetu a volitelné podsítě pro nasazení dalších služeb správy nebo správy. Virtual Network v paprsku obsahuje podsítě pro hostování úloh aplikací. Další podsítě je možné vytvořit po nasazení podle potřeby pro podporu použitelných scénářů.
- [Azure firewall](../../../../firewall/overview.md) ke směrování veškerého odchozího internetového provozu a povolení příchozího internetového provozu přes pole s odkazem. (Výchozí pravidla brány firewall blokují veškerý příchozí a odchozí provoz Internetu a pravidla musí být po nasazení nakonfigurovaná.)
- [Skupiny zabezpečení sítě](../../../../virtual-network/network-security-group-how-it-works.md) (skupin zabezpečení sítě) přiřazené ke všem podsítím (s výjimkou podsítí vlastněných službou, jako je Azure bastionu, brána a Azure firewall) nakonfigurované k blokování veškerého příchozího a odchozího provozu internetu.
- [Skupiny zabezpečení aplikací](../../../../virtual-network/application-security-groups.md) umožňují seskupování virtuálních počítačů Azure použít společné zásady zabezpečení sítě.
- [Směrovací tabulky](../../../../virtual-network/manage-route-table.md) pro směrování veškerého odchozího internetového provozu z podsítí přes bránu firewall. (Pravidla Azure Firewall a NSG bude nutné po nasazení nakonfigurovat pro otevření připojení.)
- [Azure Network Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) k monitorování, diagnostice a zobrazování metrik prostředků ve službě Azure Virtual Network.
- [Azure DDoS Protection Standard](../../../../ddos-protection/ddos-protection-overview.md) pro ochranu prostředků Azure před útoky DDoS.
- [Azure bastionu](../../../../bastion/bastion-overview.md) k zajištění bezproblémového a zabezpečeného připojení k virtuálnímu počítači, který nevyžaduje veřejnou IP adresu, agenta nebo speciální klientský software.
- [Azure VPN Gateway](../../../../vpn-gateway/vpn-gateway-about-vpngateways.md) k povolení šifrovaného provozu mezi virtuální sítí Azure a místním umístěním přes veřejný Internet.

> [!NOTE] 
> Azure Security test Foundation pro úlohy stanoví základní architekturu. Diagram architektury výše obsahuje několik fiktivních prostředků k předvedení potenciálního použití podsítí. Pořád je potřeba nasazovat úlohy do této základní architektury.

## <a name="next-steps"></a>Další kroky

Zkontrolovali jste přehled a architekturu ukázkového plánu Azure Security benchmark Foundation.

> [!div class="nextstepaction"]
> [Plán Azure Security benchmark Foundation – postup nasazení](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
