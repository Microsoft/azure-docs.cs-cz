---
title: Přehled Azure Monitoru pro kontejnery | Dokumenty společnosti Microsoft
description: Tento článek popisuje Azure Monitor pro kontejnery, které monitoruje řešení AKS Container Insights a hodnotu, kterou poskytuje sledováním stavu clusterů AKS a instancí kontejnerů v Azure.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 3ff2c35ae9f5838447ce90e2a020649427920a43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275227"
---
# <a name="azure-monitor-for-containers-overview"></a>Přehled služby Azure Monitor pro kontejnery

Azure Monitor pro kontejnery je funkce určená ke sledování výkonu úloh kontejnerů nasazených na:

- Spravované clustery Kubernetes hostované ve [službě Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md)
- Samoobslužné clustery Kubernetes hostované v Azure pomocí [AKS Engine](https://github.com/Azure/aks-engine)
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- Clustery Kubernetes s vlastním spravem hostované v [Azure Stacku](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) nebo v místním prostředí
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

Azure Monitor pro kontejnery podporuje clustery s operačním systémem Linux a Windows Server 2019. 

Monitorování kontejnerů je důležité, zejména když používáte produkční cluster ve velkém měřítku s více aplikacemi.

Azure Monitor pro kontejnery poskytuje viditelnost výkonu shromažďováním metrik paměti a procesoru z řadičů, uzlů a kontejnerů, které jsou dostupné v Kubernetes prostřednictvím rozhraní API metriky. Shromažďují se také protokoly kontejnerů.  Po povolení monitorování z clusterů Kubernetes se metriky a protokoly automaticky shromažďují prostřednictvím kontejnerizované verze agenta Log Analytics pro Linux. Metriky se zapisují do úložiště metrik a data protokolu se zapisují do úložiště protokolů přidruženého k pracovnímu prostoru [Log Analytics.](../log-query/log-query-overview.md) 

![Azure Monitor pro architekturu kontejnerů](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Co Azure Monitor pro kontejnery poskytuje?

Azure Monitor pro kontejnery poskytuje komplexní monitorování pomocí různých funkcí Azure Monitoru. Tyto funkce umožňují pochopit výkon a stav clusteru Kubernetes s operačním systémem Linux a Windows Server 2019 a úlohy kontejnerů. S Azure Monitor pro kontejnery můžete:

* Identifikujte kontejnery AKS, které jsou spuštěny na uzlu a jejich průměrné využití procesoru a paměti. Tyto znalosti vám mohou pomoci identifikovat problémová místa prostředků.
* Identifikujte využití procesoru a paměti skupin kontejnerů a jejich kontejnerů hostovaných v instanci kontejneru Azure.  
* Určete, kde se kontejner nachází v řadiči nebo podu. Tyto znalosti vám mohou pomoci zobrazit celkový výkon řadiče nebo podu. 
* Zkontrolujte využití prostředků úloh spuštěných na hostiteli, které nesouvisejí se standardními procesy, které podporují pod.
* Pochopit chování clusteru pod průměrné a nejtěžší zatížení. Tyto znalosti vám mohou pomoci identifikovat potřeby kapacity a určit maximální zatížení, které může cluster udržet. 
* Nakonfigurujte výstrahy tak, aby vás aktivně upozorňovaly nebo zaznamenávaly, když využití procesoru a paměti v uzlech nebo kontejnerech překročí prahové hodnoty nebo když dojde ke změně stavu v clusteru v kumulativní mnoství stavu infrastruktury nebo uzlů.
* Integrujte s [Prometheus](https://prometheus.io/docs/introduction/overview/) a zobrazte metriky aplikací a úloh, které shromažďuje z uzlů a Kubernetes pomocí dotazů k vytvoření [vlastních](container-insights-log-search.md) výstrah, řídicích panelů a podrobné analýzy.
* Monitorování úloh kontejnerů nasazených v místním prostředí [AKS Engine](https://github.com/Azure/aks-engine) a [AKS Engine v Azure Stacku](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
* Monitorování úloh kontejnerů [nasazených do Azure Red Hat OpenShift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >Podpora Azure Red Hat OpenShift je funkce ve verzi Public Preview v tuto chvíli.
    >

Hlavní rozdíly v monitorování clusteru Windows Server ve srovnání s linuxovým clusterem jsou následující:

- Metrika RSS paměti není k dispozici pro uzel a kontejnery systému Windows.
- Informace o kapacitě úložiště disku nejsou k dispozici pro uzly systému Windows.
- Protokoly kontejnerů nejsou k dispozici pro kontejnery spuštěné v uzlech systému Windows.
- Podpora funkcí Live Data (preview) je k dispozici s výjimkou protokolů kontejnerů systému Windows.
- Monitorují se pouze prostředí podů, ne prostředí Dockeru.
- Ve verzi Preview je podporováno maximálně 30 kontejnerů systému Windows Server. Toto omezení se nevztahuje na linuxové kontejnery. 

Podívejte se na následující video poskytující podrobné informace o střední úrovni, které vám pomůže získat informace o monitorování clusteru AKS pomocí Azure Monitor u kontejnerů.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Jak se dostanu k této funkci?

K Azure Monitoru pro kontejnery můžete přistupovat dvěma způsoby, z Azure Monitoru nebo přímo z vybraného clusteru AKS. Z Azure Monitoru máte globální perspektivu všech nasazených kontejnerů, které jsou monitorované a které ne, což vám umožní vyhledávat a filtrovat napříč předplatnými a skupinami prostředků a pak přejít k podrobnostem do Azure Monitoru pro kontejnery z vybraného kontejneru.  V opačném případě můžete přistupovat k funkci přímo z vybraného kontejneru AKS ze stránky AKS.  

![Přehled metod přístupu k Azure Monitoru pro kontejnery](./media/container-insights-overview/azmon-containers-experience.png)

Pokud máte zájem o monitorování a správu hostitelů kontejnerů Dockeru a Windows spuštěných mimo AKS za účelem zobrazení konfigurace, auditu a využití prostředků, přečtěte [si řešení monitorování kontejnerů](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Další kroky

Chcete-li začít sledovat cluster Kubernetes, přečtěte [si, přečtěte si jak povolit Azure Monitor pro kontejnery](container-insights-onboard.md) pochopit požadavky a dostupné metody povolit monitorování. 
