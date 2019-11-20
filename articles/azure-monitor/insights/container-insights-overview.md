---
title: Přehled služby Azure Monitor pro kontejnery | Dokumentace Microsoftu
description: Tento článek popisuje Azure Monitor pro kontejnery, které monitoruje řešení přehledy o kontejnerech AKS a hodnota, kterou nabízí monitorování stavu clusteru AKS a v Azure Container Instances.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/18/2019
ms.openlocfilehash: 97aad6f3853de07a3c4d2fc694c1dcefbd0e82a4
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195123"
---
# <a name="azure-monitor-for-containers-overview"></a>Azure Monitor pro kontejnery – přehled

Azure Monitor for Containers je funkce navržená tak, aby sledovala výkon úloh kontejneru nasazených na:

- Managed Kubernetes clusterů hostovaných ve službě Azure Kubernetes (AKS)
- Azure Container Instances
- Samostatné spravované clustery Kubernetes hostované na Azure Stack nebo místně
- Azure Red Hat OpenShift

Monitorování kontejnerů je důležité, zejména v případě, že spouštíte produkční cluster ve velkém měřítku, s několika aplikacemi.

Azure Monitor pro kontejnery vám poskytne přehled o výkonu shromažďováním paměti a procesoru metriky z řadiče, uzly a kontejnerů, které jsou k dispozici v Kubernetes prostřednictvím rozhraní API metrik. Shromažďují se také protokoly kontejnerů.  Po povolení monitorování z clusterů Kubernetes se metriky a protokoly automaticky shromažďují pomocí kontejnerové verze Log Analytics agenta pro Linux. Metriky se zapisují do úložiště metrik a data protokolu se zapisují do úložiště logs přidruženého k vašemu pracovnímu prostoru [Log Analytics](../log-query/log-query-overview.md) . 

![Architektura Azure Monitor for Containers](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Co dělá monitorování Azure pro kontejnery poskytují?

Azure Monitor pro kontejnery nabízí ucelené monitorování pomocí různých funkcí Azure Monitor, které vám umožní pochopit výkon a stav clusteru Kubernetes a zatížení kontejnerů. Pomocí Azure Monitor pro kontejnery můžete:

* Identifikujte AKS kontejnery, které běží na uzlu a jejich průměrné využití procesoru a paměti. Tyto znalosti můžete identifikovat kritické body prostředků.
* Identifikujte využití procesoru a paměti skupiny kontejnerů a jejich kontejnerů hostované ve službě Azure Container Instances.  
* Identifikujte, kde se kontejner nachází v řadiči nebo pod. Tyto znalosti můžete zobrazit kontroleru nebo pod na celkový výkon. 
* Zkontrolujte využití prostředků úlohy běžící na hostiteli, které nesouvisí s standardních procesů, které podporují pod.
* Pochopte chování clusteru ve skupinovém rámečku průměrných a rozděluje zatížení. Tyto znalosti můžete určit, potřeb kapacity a určení maximálního zatížení, který může cluster tolerovat. 
* Nakonfigurujte výstrahy tak, aby vás proaktivně upozornily nebo zaznamenaly, když využití procesoru a paměti na uzlech nebo kontejnerech překračuje vaše prahové hodnoty nebo když dojde ke změně stavu v clusteru v souhrnu stavu infrastruktury nebo uzlů.
* Integrací s [Prometheus](https://prometheus.io/docs/introduction/overview/) můžete zobrazit metriky aplikací a úloh, které shromažďuje z uzlů a Kubernetes pomocí [dotazů](container-insights-log-search.md) pro vytváření vlastních výstrah, řídicích panelů a podrobných podrobných analýz.

    >[!NOTE]
    >Podpora pro Prometheus je ve verzi Public Preview v současnosti funkce.
    >

* Monitorujte úlohy kontejneru [nasazené do](https://github.com/microsoft/OMS-docker/tree/aks-engine) místního a AKSového stroje AKS engine [na Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
* Monitorujte úlohy kontejneru [nasazené do Azure Red Hat OpenShift](../../openshift/intro-openshift.md).

Podívejte se na následující video, které poskytuje podrobné podrobně úrovně, které vám pomůžou získat informace o monitorování clusteru AKS pomocí Azure Monitor pro kontejnery.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Jak získám přístup do této funkce?

Azure Monitor lze použít pro kontejnery dva způsoby, ze služby Azure Monitor nebo přímo z vybraných clusteru AKS. Z Azure Monitor máte globální perspektivu všech nasazených kontejnerů, které jsou monitorované a které nejsou, což vám umožní vyhledávat a filtrovat v rámci předplatných a skupin prostředků a pak přejít na Azure Monitor pro kontejnery z vybraný kontejner.  V opačném případě můžete k této funkci přistupovat přímo z vybraného kontejneru AKS ze stránky AKS.  

![Přehled metod pro přístup k Azure Monitor pro kontejnery](./media/container-insights-overview/azmon-containers-experience.png)

Pokud vás zajímá monitorování a Správa hostitelů kontejnerů pro Docker a Windows, kteří používají mimo AKS k zobrazení konfigurace, auditu a využití prostředků, prostudujte si [řešení pro monitorování kontejnerů](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Další kroky

Pokud chcete začít monitorovat cluster Kubernetes, přečtěte si, [Jak povolit Azure monitor kontejnerů](container-insights-onboard.md) pro pochopení požadavků a dostupných metod, které umožňují monitorování. 
