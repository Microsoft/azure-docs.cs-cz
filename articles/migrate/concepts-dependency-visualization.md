---
title: Analýza závislostí v Azure Migrate Server Assessment
description: Popisuje, jak používat analýzu závislostí pro posouzení pomocí Azure Migrateho posouzení serveru.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: b269322f5426a68b072452bc2f79531685be3742
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84447573"
---
# <a name="dependency-analysis"></a>Analýza závislostí

Tento článek popisuje analýzu závislostí v Azure Migrate: posouzení serveru.

## <a name="overview"></a>Přehled

Analýza závislostí vám pomůže identifikovat závislosti mezi místními počítači, které chcete vyhodnotit a migrovat do Azure. 

- V Azure Migrate: vyhodnocení serveru shromažďujete počítače do skupiny a pak ji vyhodnotí. Analýza závislostí vám pomůže přesněji seskupit počítače a zajistit vysokou spolehlivost pro posouzení.
- Analýza závislostí vám umožní identifikovat počítače, které se musí migrovat dohromady. Můžete určit, jestli se počítače používají, nebo jestli je možné je vyřadit z provozu místo migrace.
- Analýza závislostí pomáhá zajistit, že nic není v provozu, a během migrace se vyhnete nepřekročení výpadků.
- Analýza je užitečná hlavně v případě, že si nejste jistí, jestli jsou počítače součástí nasazení aplikace, které chcete migrovat do Azure.
- [Přečtěte si](common-questions-discovery-assessment.md#what-is-dependency-visualization) běžné otázky k analýze závislostí.

Pro nasazení analýzy závislostí existují dvě možnosti.

- **Založené na agentovi**: analýza závislostí založená na agentech vyžaduje instalaci agentů na každý místní počítač, který chcete analyzovat.
- Bez **agentů**: s analýzou bez agentů nemusíte instalovat agenty na počítače, které chcete křížově kontrolovat. Tato možnost je momentálně ve verzi Preview a je dostupná jenom pro virtuální počítače VMware.

> [!NOTE]
> Analýza závislostí na základě agenta není v Azure Government k dispozici. Můžete využít analýzu závislostí bez agentů.

## <a name="agentless-analysis"></a>Analýza bez agentů

Analýza závislostí bez agentů funguje tak, že zachytává data připojení TCP z počítačů, pro které je povolená. Na počítačích, které chcete analyzovat, nejsou nainstalované žádné agenty.

### <a name="collected-data"></a>Shromážděná data

Po zahájení zjišťování závislosti se zařízení během pěti minut dotazuje data na shromažďování dat. Tato data se shromažďují z virtuálních počítačů hosta prostřednictvím vCenter Server pomocí rozhraní API vSphere. Shromážděná data se zpracovávají na zařízení Azure Migrate, aby se odvodit informace o identitě a odesílaly se Azure Migrate každých 6 hodin.

Cyklické dotazování shromažďuje tato data z počítačů: 
- Názvy procesů, které mají aktivní připojení.
- Název aplikace, která spouští procesy, které mají aktivní připojení.
- Cílový port aktivních připojení.

## <a name="agent-based-analysis"></a>Analýza založená na agentovi

V případě analýzy založené na agentech používá posouzení serveru [Service map řešení](../azure-monitor/insights/service-map.md) v Azure monitor a umožňuje vizualizaci a analýzu závislostí. [Agent Microsoft Monitoring Agent/Log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent) a [Agent závislostí](../azure-monitor/platform/agents-overview.md#dependency-agent)musí být nainstalován na každém počítači, který chcete analyzovat.

### <a name="collected-data"></a>Shromážděná data

Pro analýzu založenou na agentech se shromažďují následující data:

- Název zdrojového počítačového serveru, proces, název aplikace
- Název cílového počítačového serveru, proces, název aplikace a port.
- Pro Log Analytics dotazy se shromažďují a k dispozici informace o počtu připojení, latenci a přenosu dat. 


## <a name="compare-agentless-and-agent-based"></a>Porovnání bez agentů a založené na agentovi

Rozdíly mezi vizualizacemi bez agentů a vizualizací na základě agentů jsou shrnuty v tabulce.

**Požadavek** | **Bez agenta** | **Založené na agentovi**
--- | --- | ---
Podpora | Tato možnost je momentálně ve verzi Preview a je dostupná jenom pro virtuální počítače VMware. [Zkontrolujte](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) podporované operační systémy. | Obecně dostupná (GA).
Agent | Není nutné instalovat agenty na počítačích, které chcete křížově kontrolovat. | Agenti, kteří se mají nainstalovat na každý místní počítač, který chcete analyzovat: [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)a [Agent závislostí](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Log Analytics | Nepožadováno. | Azure Migrate používá řešení [Service map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) v [protokolech Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) k analýze závislostí. 
Jak to funguje | Zachycuje data připojení TCP na počítačích, které jsou povoleny pro vizualizaci závislostí. Po zjištění se data shromáždí v intervalech po pěti minutách. | Agenti Service Map nainstalovaná na počítači shromažďují data o procesech TCP a příchozích a odchozích připojeních pro jednotlivé procesy.
Data | Název zdrojového počítačového serveru, proces, název aplikace<br/><br/> Název cílového počítačového serveru, proces, název aplikace a port. | Název zdrojového počítačového serveru, proces, název aplikace<br/><br/> Název cílového počítačového serveru, proces, název aplikace a port.<br/><br/> Pro Log Analytics dotazy se shromažďují a k dispozici informace o počtu připojení, latenci a přenosu dat. 
Vizualizace | Mapa závislostí jednoho serveru se dá zobrazit po dobu od 1 hodiny do 30 dnů. | Mapa závislostí pro jeden server.<br/><br/> Mapu lze zobrazit pouze za hodinu.<br/><br/> Mapa závislostí skupiny serverů.<br/><br/> Přidejte nebo odeberte servery ve skupině z zobrazení mapy.
Export dat | Posledních 30 dní data je možné stáhnout ve formátu CSV. | Data se dají dotazovat pomocí Log Analytics.



## <a name="next-steps"></a>Další kroky
- Přečtěte si požadavky pro nastavení analýzy založené na agentech pro [virtuální počítače VMware](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements), [fyzické servery](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)a [virtuální počítače Hyper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- [Projděte si](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) požadavky na analýzu bez agentů pro virtuální počítače VMware.
- [Nastavení](how-to-create-group-machine-dependencies.md) Vizualizace závislostí na základě agentů
- [Vyzkoušejte](how-to-create-group-machine-dependencies-agentless.md) vizualizaci závislostí bez agentů pro virtuální počítače VMware.
- Přečtěte si [běžné otázky](common-questions-discovery-assessment.md#what-is-dependency-visualization) k vizualizaci závislostí.


