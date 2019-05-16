---
title: Přehled služby Azure Monitor pro kontejnery | Dokumentace Microsoftu
description: Tento článek popisuje Azure Monitor pro kontejnery, které monitoruje řešení přehledy o kontejnerech AKS a hodnota, kterou nabízí monitorování stavu clusteru AKS a v Azure Container Instances.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: d6321564672097fbf901d1d33afac9f606fcb63a
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521837"
---
# <a name="azure-monitor-for-containers-overview"></a>Azure Monitor pro kontejnery – přehled

Azure Monitor pro kontejnery je funkce určené k monitorování výkonu úlohy kontejneru nasadit do Azure Container Instances nebo spravované clustery Kubernetes hostované ve službě Azure Kubernetes Service (AKS). Monitorování kontejnerů je důležité, zejména v případě, že spouštíte produkční cluster ve velkém měřítku, s několika aplikacemi.

Azure Monitor pro kontejnery vám poskytne přehled o výkonu shromažďováním paměti a procesoru metriky z řadiče, uzly a kontejnerů, které jsou k dispozici v Kubernetes prostřednictvím rozhraní API metrik. Shromažďují se také protokoly kontejnerů.  Když povolíte monitorování z clusterů Kubernetes, metriky a protokoly se automaticky shromažďují, můžete prostřednictvím kontejnerizovaných verzi agenta Log Analytics pro Linux. Metriky se zapisují do úložiště metriky a data protokolu se zapíšou do protokolů úložiště přidružené k vaší [Log Analytics](../log-query/log-query-overview.md) pracovního prostoru. 

![Azure Monitor pro architekturu kontejnery](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Co dělá monitorování Azure pro kontejnery poskytují?

Azure Monitor pro kontejnery zajišťuje komplexní monitorování zkušenosti s používáním různých funkcí služby Azure Monitor umožňuje pochopit výkon a stav clusteru Kubernetes a úlohy kontejneru. Azure Monitor pro kontejnery můžete:

* Identifikujte AKS kontejnery, které běží na uzlu a jejich průměrné využití procesoru a paměti. Tyto znalosti můžete identifikovat kritické body prostředků.
* Identifikujte využití procesoru a paměti skupiny kontejnerů a jejich kontejnerů hostované ve službě Azure Container Instances.  
* Určete, kde se nachází kontejneru v kontroleru nebo pod. Tyto znalosti můžete zobrazit kontroleru nebo pod na celkový výkon.
* Zkontrolujte využití prostředků úlohy běžící na hostiteli, které nesouvisí s standardních procesů, které podporují pod.
* Pochopte chování clusteru ve skupinovém rámečku průměrných a rozděluje zatížení. Tyto znalosti můžete určit, potřeb kapacity a určení maximálního zatížení, který může cluster tolerovat. 
* Konfigurace výstrahy a proaktivně vás upozorní nebo poznamenejte si ho využití procesoru a paměti na uzlech nebo kontejnery překročení prahových hodnot.  

## <a name="how-do-i-access-this-feature"></a>Jak získám přístup do této funkce?
Azure Monitor lze použít pro kontejnery dva způsoby, ze služby Azure Monitor nebo přímo z vybraných clusteru AKS. Ze služby Azure Monitor, budete mít všechny kontejnery nasazení, které jsou monitorovány a které nejsou, umožňuje vyhledávat a filtrovat napříč vaší skupiny prostředků a předplatná a pak přejdete do služby Azure Monitor pro kontejnery z globální perspektivy Vybraný kontejner.  V opačném případě funkci dostanete přímo z vybraného kontejneru AKS ze stránky s AKS.  

![Přehled metod pro přístup k Azure Monitor pro kontejnery](./media/container-insights-overview/azmon-containers-experience.png)

Pokud vás zajímá monitorování a správu Docker a Windows najdete v hostitelích kontejnerů na zobrazení konfigurace, auditování a využití prostředků [řešení pro monitorování kontejnerů](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Další postup
Chcete-li zahájit monitorování clusteru AKS, zkontrolovat [povolení monitorování Azure pro kontejnery](container-insights-onboard.md) vám pomohou pochopit požadavky a dostupné metody, které chcete povolit monitorování.  
