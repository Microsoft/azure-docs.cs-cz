---
title: Analýza závislostí v azure migrate server assessment
description: Popisuje, jak použít analýzu závislostí pro hodnocení pomocí Azure Migrate Server Assessment.
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: f96496b66d6bcfd397fb0a7303d3dbfb4fd6f6b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455634"
---
# <a name="dependency-analysis"></a>Analýza závislostí

Tento článek popisuje analýzu závislostí v Azure Migrate:Server Assessment.

## <a name="overview"></a>Přehled

Analýza závislostí vám pomůže identifikovat závislosti mezi místními počítači, které chcete posoudit a migrovat do Azure. 

- V Azure Migrate:Server Assessment shromažďujete počítače do skupiny a pak vyhodnoťte skupinu. Analýza závislostí pomáhá seskupit počítače přesněji, s vysokou jistotou pro posouzení.
- Analýza závislostí umožňuje identifikovat počítače, které je třeba migrovat společně. Můžete určit, zda jsou stroje používány nebo zda je lze vyřadit z provozu namísto migrace.
- Analýza závislostí pomáhá zajistit, že nic nezůstane pozadu, a vyhnout se výpadkům překvapení během migrace.
- Analýza je užitečná zejména v případě, že si nejste jistí, jestli jsou počítače součástí nasazení aplikace, které chcete migrovat do Azure.
- [Projděte si](common-questions-discovery-assessment.md#what-is-dependency-visualization) běžné otázky týkající se analýzy závislostí.

Existují dvě možnosti nasazení analýzy závislostí.

- **Agent-** Analýza závislostí založená na agentovi vyžaduje, aby agenti byli nainstalováni v každém místním počítači, který chcete analyzovat.
- **Agentless**: S analýzou bez agentů není nutné instalovat agenty na počítačích, které chcete křížově kontrolovat. Tato možnost je momentálně ve verzi Preview a je dostupná jenom pro virtuální měna VMware.

> [!NOTE]
> Analýza závislostí není ve službě Azure Government dostupná.

## <a name="agentless-analysis"></a>Analýza bez agentů

Analýza závislostí bez agenta funguje tak, že zachytí data připojení TCP z počítačů, pro které je povolena. V počítačích, které chcete analyzovat, nejsou nainstalováni žádní agenti.

### <a name="collected-data"></a>Shromážděná data

Po spuštění zjišťování závislostí zařízení dotazování dat z počítačů každých pět minut shromažďovat data. Tato data se shromažďují z hostovaných virtuálních stránek prostřednictvím serveru vCenter Server pomocí vSphere API. Shromážděná data se zpracovávají na zařízení Azure Migrate, aby se odvodily informace o identitě, a odesílají se do Azure Migrate každých šest hodin.

Dotazování shromažďuje tato data ze počítačů: 
- Název procesů, které mají aktivní připojení.
- Název aplikace, která spouštějí procesy s aktivními připojeními.
- Cílový port na aktivních připojeních.

## <a name="agent-based-analysis"></a>Analýza založená na agentech

Pro analýzu založenou na agentovi používá server Assessment [řešení Map služeb](../azure-monitor/insights/service-map.md) v Azure Monitoru k povolení vizualizace a analýzy závislostí. [Agent microsoft monitoring agent/Log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent) a agent [závislostí](../azure-monitor/platform/agents-overview.md#dependency-agent), musí být nainstalovány v každém počítači, který chcete analyzovat.

### <a name="collected-data"></a>Shromážděná data

Pro vizualizaci založenou na agentovi jsou shromažďována následující data:

- Název zdrojového počítače, proces, název aplikace.
- Název serveru cílového počítače, proces, název aplikace a port.
- Počet připojení, latence a informace o přenosu dat jsou shromažďovány a k dispozici pro dotazy Log Analytics. 


## <a name="compare-agentless-and-agent-based"></a>Porovnání bez agenta a na základě agenta

Rozdíly mezi vizualizací bez agenta a vizualizací na základě agenta jsou shrnuty v tabulce.

**Požadavek** | **Bez agenta** | **Na základě agenta**
--- | --- | ---
Podpora | Tato možnost je momentálně ve verzi Preview a je dostupná jenom pro virtuální měna VMware. [Zkontrolujte](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) podporované operační systémy. | V obecné dostupnosti (GA).
Agent | Není třeba instalovat agenty na počítačích, které chcete křížově kontrolovat. | Agenti, kteří mají být nainstalováni v každém místním počítači, který chcete analyzovat: [Agent monitorování společnosti Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)a Agent [závislostí](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Log Analytics | Není vyžadováno. | Azure Migrate používá řešení [map služeb](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) v [protokolech Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) u analýzy závislostí. 
Jak to funguje | Zachytí data připojení TCP na počítačích, které jsou povoleny pro vizualizaci závislostí. Po zjištění shromažďuje data v intervalech pěti minut. | Agenti mapy služeb nainstalovaní v počítači shromažďují data o procesech TCP a příchozích a odchozích připojeních pro každý proces.
Data | Název zdrojového počítače, proces, název aplikace.<br/><br/> Název serveru cílového počítače, proces, název aplikace a port. | Název zdrojového počítače, proces, název aplikace.<br/><br/> Název serveru cílového počítače, proces, název aplikace a port.<br/><br/> Počet připojení, latence a informace o přenosu dat jsou shromažďovány a k dispozici pro dotazy Log Analytics. 
Vizualizace | Mapu závislostí jednoho serveru lze zobrazit po dobu jedné hodiny až 30 dnů. | Mapa závislostí jednoho serveru.<br/><br/> Mapu lze zobrazit pouze za hodinu.<br/><br/> Mapa závislostí skupiny serverů.<br/><br/> Přidejte a odeberte servery ve skupině ze zobrazení mapy.
Export dat | Nelze aktuálně stáhnout v tabulkovém formátu. | Data mohou být dotazováni pomocí Analýzy protokolů.



## <a name="next-steps"></a>Další kroky
- Projděte si požadavky na nastavení analýzy založené na agentech pro [virtuální zařízení VMware](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements), [fyzické servery](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)a [virtuální zařízení Hyper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- [Projděte si](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) požadavky na analýzu virtuálních měn VMware bez agentů.
- [Nastavení](how-to-create-group-machine-dependencies.md) vizualizace závislostí založených na agentovi
- [Vyzkoušejte](how-to-create-group-machine-dependencies-agentless.md) vizualizaci závislostí bez agenta pro virtuální zařízení VMware.
- Projděte si [běžné otázky](common-questions-discovery-assessment.md#what-is-dependency-visualization) týkající se vizualizace závislostí.


