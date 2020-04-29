---
title: Přehled Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje Azure Monitor pro kontejnery, které monitorují řešení AKS Container Insights a hodnotu, kterou zajišťuje, pomocí monitorování stavu clusterů AKS a Container Instances v Azure.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 3ff2c35ae9f5838447ce90e2a020649427920a43
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79275227"
---
# <a name="azure-monitor-for-containers-overview"></a>Přehled služby Azure Monitor pro kontejnery

Azure Monitor for Containers je funkce navržená tak, aby sledovala výkon úloh kontejneru nasazených na:

- Managed Kubernetes clusterů hostovaných ve [službě Azure Kubernetes (AKS)](../../aks/intro-kubernetes.md)
- Samostatné spravované clustery Kubernetes hostované v Azure pomocí [stroje AKS](https://github.com/Azure/aks-engine)
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- Samostatné spravované clustery Kubernetes hostované na [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) nebo místně
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

Azure Monitor for Containers podporuje clustery s operačním systémem Linux a Windows Server 2019. 

Monitorování vašich kontejnerů je důležité, zejména pokud používáte produkční cluster ve velkém měřítku s více aplikacemi.

Azure Monitor pro kontejnery poskytují přehled o výkonu tím, že shromažďují metriky paměti a procesoru z řadičů, uzlů a kontejnerů, které jsou k dispozici v Kubernetes prostřednictvím rozhraní API metrik. Shromažďují se také protokoly kontejnerů.  Po povolení monitorování z clusterů Kubernetes se metriky a protokoly automaticky shromažďují pomocí kontejnerové verze Log Analytics agenta pro Linux. Metriky se zapisují do úložiště metrik a data protokolu se zapisují do úložiště logs přidruženého k vašemu pracovnímu prostoru [Log Analytics](../log-query/log-query-overview.md) . 

![Architektura Azure Monitor for Containers](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Co Azure Monitor kontejnerů poskytuje?

Azure Monitor for Containers poskytuje komplexní monitorování s využitím různých funkcí Azure Monitor. Tyto funkce umožňují pochopit výkon a stav clusteru Kubernetes s operačním systémem Linux a Windows Server 2019 a zatížení kontejnerů. Pomocí Azure Monitor pro kontejnery můžete:

* Identifikujte kontejnery AKS, které jsou spuštěny na uzlu, a jejich průměrné využití procesoru a paměti. Tato znalostní báze vám může přispět k identifikaci kritických bodů prostředků.
* Identifikujte využití procesoru a paměti u skupin kontejnerů a jejich kontejnerů hostovaných v Azure Container Instances.  
* Identifikujte, kde se kontejner nachází v řadiči nebo pod. Tato znalostní báze vám může pomáhat zobrazit celkový výkon řadiče nebo pod ním. 
* Prohlédněte si využití prostředků u úloh běžících na hostiteli, které nesouvisí se standardními procesy, které podporují rozhraní pod.
* Pochopení chování clusteru v rámci průměrného a nejtěžšího zatížení. Tato znalostní báze vám pomůže identifikovat požadavky na kapacitu a určit maximální zatížení, které může cluster tolerovat. 
* Nakonfigurujte výstrahy tak, aby vás proaktivně upozornily nebo zaznamenaly, když využití procesoru a paměti na uzlech nebo kontejnerech překračuje vaše prahové hodnoty nebo když dojde ke změně stavu v clusteru v souhrnu stavu infrastruktury nebo uzlů.
* Integrací s [Prometheus](https://prometheus.io/docs/introduction/overview/) můžete zobrazit metriky aplikací a úloh, které shromažďuje z uzlů a Kubernetes pomocí [dotazů](container-insights-log-search.md) pro vytváření vlastních výstrah, řídicích panelů a podrobných podrobných analýz.
* Monitorujte úlohy kontejneru [nasazené do](https://github.com/Azure/aks-engine) místního a AKSového stroje AKS engine [na Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
* Monitorujte úlohy kontejneru [nasazené do Azure Red Hat OpenShift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >Podpora pro Azure Red Hat OpenShift je v současnosti funkcí ve verzi Public Preview.
    >

Mezi hlavní rozdíly v monitorování clusteru Windows serveru v porovnání s clusterem Linux patří následující:

- Metrika RSS paměti není k dispozici pro uzly a kontejnery Windows.
- Informace o kapacitě diskového úložiště nejsou k dispozici pro uzly Windows.
- Protokoly kontejneru nejsou k dispozici pro kontejnery běžící v uzlech systému Windows.
- Podpora funkcí živého data (Preview) je dostupná s výjimkou protokolů kontejnerů Windows.
- Monitoruje se jenom pod prostředími, nikoli Docká prostředí.
- Ve verzi Preview se podporuje maximálně 30 kontejnerů Windows serveru. Toto omezení se nevztahuje na kontejnery Linux. 

Podívejte se na následující video, které poskytuje podrobné podrobně úrovně, které vám pomůžou získat informace o monitorování clusteru AKS pomocí Azure Monitor pro kontejnery.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Návody k této funkci přistupovat?

K Azure Monitor kontejnerů můžete přistupovat dvěma způsoby, od Azure Monitor nebo přímo z vybraného clusteru AKS. Z Azure Monitor máte globální perspektivu všech nasazených kontejnerů, které jsou monitorované a které nejsou, což vám umožní vyhledávat a filtrovat v rámci předplatných a skupin prostředků a pak přejít na Azure Monitor kontejnerů z vybraného kontejneru.  V opačném případě můžete k této funkci přistupovat přímo z vybraného kontejneru AKS ze stránky AKS.  

![Přehled metod pro přístup k Azure Monitor pro kontejnery](./media/container-insights-overview/azmon-containers-experience.png)

Pokud vás zajímá monitorování a Správa hostitelů kontejnerů pro Docker a Windows, kteří používají mimo AKS k zobrazení konfigurace, auditu a využití prostředků, prostudujte si [řešení pro monitorování kontejnerů](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Další kroky

Pokud chcete začít monitorovat cluster Kubernetes, přečtěte si, [Jak povolit Azure monitor kontejnerů](container-insights-onboard.md) pro pochopení požadavků a dostupných metod, které umožňují monitorování. 
