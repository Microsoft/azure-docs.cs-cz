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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: f46d6f9a8ed17d9ce7b6dd3ff1e797306dd48bf9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101921"
---
# <a name="azure-monitor-for-containers-overview"></a>Azure Monitor pro kontejnery – přehled

Azure Monitor pro kontejnery je funkce určené k monitorování výkonu úlohy kontejneru nasadit do Azure Container Instances nebo spravované clustery Kubernetes hostované ve službě Azure Kubernetes Service (AKS). Monitorování kontejnerů je důležité, zejména v případě, že spouštíte produkční cluster ve velkém měřítku, s několika aplikacemi.

Azure Monitor pro kontejnery vám poskytne přehled o výkonu shromažďováním paměti a procesoru metriky z řadiče, uzly a kontejnerů, které jsou k dispozici v Kubernetes prostřednictvím rozhraní API metrik. Shromažďují se také protokoly kontejnerů.  Když povolíte monitorování z clusterů Kubernetes, tyto metriky a protokoly jsou automaticky shromažďovaná za vás prostřednictvím kontejnerizovaných verzi agenta Log Analytics pro Linux a uloženy v vaše [Log Analytics](../../azure-monitor/log-query/log-query-overview.md) pracovního prostoru. 
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Co dělá monitorování Azure pro kontejnery poskytují?

Azure Monitor pro kontejnery zahrnuje několik předdefinovaných zobrazení, které ukazují nacházejícím úlohy kontejneru a co ovlivňuje výkon stav monitorovaných clusteru Kubernetes, abyste mohli:  

* Identifikujte AKS kontejnery, které běží na uzlu a jejich průměrné využití procesoru a paměti. Tyto znalosti můžete identifikovat kritické body prostředků.
* Identifikujte využití procesoru a paměti skupiny kontejnerů a jejich kontejnerů hostované ve službě Azure Container Instances.  * Identifikujte, ve kterém se nachází kontejneru v kontroleru nebo pod. Tyto znalosti můžete zobrazit kontroleru nebo pod na celkový výkon.
* Zkontrolujte využití prostředků úlohy běžící na hostiteli, které nesouvisí s standardních procesů, které podporují pod.
* Pochopte chování clusteru ve skupinovém rámečku průměrných a rozděluje zatížení. Tyto znalosti můžete určit, potřeb kapacity a určení maximálního zatížení, který může cluster tolerovat. 

## <a name="how-do-i-access-this-feature"></a>Jak získám přístup do této funkce?
Azure Monitor lze použít pro kontejnery dva způsoby, ze služby Azure Monitor nebo přímo z vybraných clusteru AKS. Ze služby Azure Monitor, ke kterým máte nasazené globální perspektivy všechny kontejnery, které jsou monitorovány a které nejsou, umožňuje vyhledávat a filtrovat napříč vaší skupiny prostředků a předplatná a pak přejdete do služby Azure Monitor pro kontejnery z vybraného kontejner.  V opačném případě můžete jednoduše přistupovat funkci přímo z vybraného kontejneru AKS ze stránky s AKS.  

![Přehled metod pro přístup k Azure Monitor pro kontejnery](./media/container-insights-overview/azmon-containers-views-1812.png)

Pokud vás zajímá monitorování a správu Docker a Windows najdete v hostitelích kontejnerů na zobrazení konfigurace, auditování a využití prostředků [řešení pro monitorování kontejnerů](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Další postup
Chcete-li zahájit monitorování clusteru AKS, přečtěte si téma [Azure připojit jak monitorovat pro kontejnery](container-insights-onboard.md) vám pomohou pochopit požadavky a dostupné metody, které chcete povolit monitorování.  
