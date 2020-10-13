---
title: 'Ukázka podrobného plánu ISO 27001: Sdílené služby – přehled'
description: 'Přehled a architektura ukázky podrobného plánu ISO 27001: Sdílené služby Tento ukázkový podrobný plán pomáhá zákazníkům vyhodnotit konkrétní kontroly ISO 27001.'
ms.date: 07/13/2020
ms.topic: sample
ms.openlocfilehash: e02f34c424e3f68e67f2d0dc2f4541c57ce3882f
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950546"
---
# <a name="overview-of-the-iso-27001-shared-services-blueprint-sample"></a>Přehled ukázky podrobného plánu ISO 27001: Sdílené služby

Ukázka podrobného plánu ISO 27001: Sdílené služby poskytuje sadu kompatibilních vzorů infrastruktury a ochranné mantinely zásad, které pomáhají zajistit osvědčení ISO 27001. Tento podrobný plán pomáhá zákazníkům nasazovat cloudové architektury, které nabízejí řešení pro scénáře s požadavky na dodržování předpisů nebo akreditaci.

Ukázka podrobného plánu [úlohy ISO 27001 App Service Environment/SQL Database](../iso27001-ase-sql-workload/index.md) je rozšířením této ukázky.

## <a name="architecture"></a>Architektura

Ukázka podrobného plánu ISO 27001: Sdílené služby nasazuje v Azure základní infrastrukturu, kterou organizace mohou využít k hostování více úloh na základě přístupu VDC (virtuální datové centrum).
VDC je prověřená sada referenčních architektur, nástrojů pro automatizaci a modelů zapojení využívaných Microsoftem pro největší podnikové zákazníky. Ukázka podrobného plánu sdílených služeb je založená na plně nativním prostředí Azure VDC znázorněném níž.

:::image type="content" source="../../media/sample-iso27001-shared/iso27001-shared-services-blueprint-sample-design.png" alt-text="Návrhy ukázky podrobného plánu ISO 27001: Sdílené služby" border="false":::

Toto prostředí je tvořené několika službami Azure, které se využívají k zajištění zabezpečené a plně monitorované infrastruktury sdílených služeb na podnikové úrovni, která je založená na standardech ISO 27001. Toto prostředí tvoří:

- [Role Azure](../../../../role-based-access-control/overview.md) využívané k oddělení povinností od roviny kontrol Před nasazením libovolné infrastruktury jsou definované tři role:
  - Role NetOps má oprávnění spravovat síťové prostředí, včetně nastavení brány firewall, nastavení NSG, směrování a dalších síťových funkcí
  - Role SecOps má nezbytná oprávnění k nasazení a správě služby [Azure Security Center](../../../../security-center/security-center-introduction.md), definování definic [Azure Policy](../../../policy/overview.md) a další oprávnění související se zabezpečením
  - Role SysOps má nezbytná oprávnění k definování definic [Azure Policy](../../../policy/overview.md) v rámci předplatného, správě služby [Log Analytics](../../../../azure-monitor/overview.md) pro celé prostředí a další provozní práva
- Jako první služba Azure se nasadí [Log Analytics](../../../../azure-monitor/overview.md), aby se zajistilo, že se všechny akce a služby připojují k centrálnímu umístění, a to od okamžiku, kdy vaše zabezpečené nasazení spustíte.
- Virtuální síť podporující podsítě pro možnosti připojení zpátky k místnímu datovému centru, zásobník příchozího a odchozího přenosu dat pro internetové připojení a podsíť sdílených služeb využívající skupiny zabezpečení sítě a skupiny zabezpečení aplikace pro kompletní mikrosegmentaci obsahující:
  - Jumpbox nebo hostitele typu bašta (bastion host) používaného pro účely správy, který je přístupný jenom přes službu [Azure Firewall](../../../../firewall/overview.md) nasazenou v podsíti stacku příchozího přenosu dat
  - Dva virtuální počítače, na kterých běží Azure Active Directory Domain Services (Azure AD DS) a DNS, které jsou přístupné jenom přes tento jumpbox a které se dají nakonfigurovat jenom pro replikaci AD přes VPN nebo připojení [ExpressRoute](../../../../expressroute/expressroute-introduction.md) (nejsou nasazené v rámci podrobného plánu)
  - Použití [Azure Net Watcheru](../../../../network-watcher/network-watcher-monitoring-overview.md) a standardní ochrany před útoky DDoS
- Instance služby [Azure Key Vault](../../../../key-vault/general/overview.md), která slouží k hostování tajných kódů používaných pro virtuální počítače nasazené v prostředí sdílených služeb

Všechny tyto prvky dodržují prověřené postupy publikované v článku zaměřeném na [Centrum architektury Azure – referenční architektury](/azure/architecture/reference-architectures/).

> [!NOTE]
> Infrastruktura ISO 27001: Sdílené služby poskytuje základní architekturu pro úlohy.
> V rámci této základní architektury je ale pořád potřeba úlohy nasadit.

Další informace najdete v [dokumentaci k virtuálním datovým centrům](/azure/architecture/vdc/).

## <a name="next-steps"></a>Další kroky

Prošli jste si přehled a architekturu ukázky podrobného plánu ISO 27001: Sdílené služby.
Jako další si projděte následující články, ve kterých se dozvíte víc o mapování kontrol a o postupu nasazení této ukázky:

> [!div class="nextstepaction"]
> [Podrobný plán ISO 27001: Sdílené služby – mapování kontrol](./control-mapping.md)
> [Podrobný plán ISO 27001: Sdílené služby – kroky pro nasazení](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)