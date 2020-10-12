---
title: Přehled Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje Azure Monitor pro kontejnery, které monitorují řešení AKS Container Insights a hodnotu, kterou zajišťuje, pomocí monitorování stavu clusterů AKS a Container Instances v Azure.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 215427adc38ebd554ca7ac650ec65a05f5a781e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317153"
---
# <a name="azure-monitor-for-containers-overview"></a>Přehled služby Azure Monitor pro kontejnery

Azure Monitor for Containers je funkce navržená tak, aby sledovala výkon úloh kontejneru nasazených na:

- Managed Kubernetes clusterů hostovaných ve [službě Azure Kubernetes (AKS)](../../aks/intro-kubernetes.md)
- Samostatné spravované clustery Kubernetes hostované v Azure pomocí [stroje AKS](https://github.com/Azure/aks-engine)
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- Samostatné spravované clustery Kubernetes hostované na [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910&preserve-view=true) nebo místně
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)
- [Kubernetes s povoleným ARC Azure](../../azure-arc/kubernetes/overview.md) (Preview)

Azure Monitor for Containers podporuje clustery s operačním systémem Linux a Windows Server 2019. Moduly runtime kontejnerů, které podporuje, jsou Docker, Moby a jakýkoli CRI kompatibilní modul runtime, jako je například CRI-O a kontejner.

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
* Monitorujte úlohy kontejneru [nasazené do](https://github.com/Azure/aks-engine) místního a AKSového stroje AKS engine [na Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908&preserve-view=true).
* Monitorujte úlohy kontejneru [nasazené do Azure Red Hat OpenShift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >Podpora pro Azure Red Hat OpenShift je v současnosti funkcí ve verzi Public Preview.
    >

* Monitorujte úlohy kontejneru [nasazené do Azure ARC s povoleným Kubernetes (Preview)](../../azure-arc/kubernetes/overview.md).

Mezi hlavní rozdíly v monitorování clusteru Windows serveru v porovnání s clusterem Linux patří následující:

- Windows nemá metriku RSS paměti a v důsledku toho není k dispozici pro uzly a kontejnery Windows. Metrika [pracovní sady](/windows/win32/memory/working-set) je k dispozici.
- Informace o kapacitě diskového úložiště nejsou k dispozici pro uzly Windows.
- Monitoruje se jenom pod prostředími, nikoli Docká prostředí.
- Ve verzi Preview se podporuje maximálně 30 kontejnerů Windows serveru. Toto omezení se nevztahuje na kontejnery Linux.

Podívejte se na následující video, které poskytuje podrobné podrobně úrovně, které vám pomůžou získat informace o monitorování clusteru AKS pomocí Azure Monitor pro kontejnery.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Návody k této funkci přistupovat?

K Azure Monitor kontejnerů můžete přistupovat dvěma způsoby, od Azure Monitor nebo přímo z vybraného clusteru AKS. Z Azure Monitor máte globální perspektivu všech nasazených kontejnerů, které jsou monitorované a které nejsou, což vám umožní vyhledávat a filtrovat v rámci předplatných a skupin prostředků a pak přejít na Azure Monitor kontejnerů z vybraného kontejneru.  V opačném případě můžete k této funkci přistupovat přímo z vybraného kontejneru AKS ze stránky AKS.

![Přehled metod pro přístup k Azure Monitor pro kontejnery](./media/container-insights-overview/azmon-containers-experience.png)

Pokud vás zajímá monitorování a Správa hostitelů kontejnerů pro Docker a Windows, kteří používají mimo AKS k zobrazení konfigurace, auditu a využití prostředků, prostudujte si [řešení pro monitorování kontejnerů](./containers.md).

## <a name="next-steps"></a>Další kroky

Pokud chcete začít monitorovat cluster Kubernetes, přečtěte si, [Jak povolit Azure monitor kontejnerů](container-insights-onboard.md) pro pochopení požadavků a dostupných metod, které umožňují monitorování.

