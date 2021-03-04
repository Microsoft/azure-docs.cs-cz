---
title: Přehled kontejnerových přehledů | Microsoft Docs
description: Tento článek popisuje službu Container Insights, která monitoruje řešení AKS Container Insights a hodnotu, kterou zajišťuje, pomocí monitorování stavu clusterů AKS a Container Instances v Azure.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 1055e2228c6625ae24e6bf388cf297e3e3363666
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723380"
---
# <a name="container-insights-overview"></a>Přehled kontejneru Insights

Container Insights je funkce navržená tak, aby sledovala výkon úloh kontejneru nasazených na:

- Managed Kubernetes clusterů hostovaných ve [službě Azure Kubernetes (AKS)](../../aks/intro-kubernetes.md)
- Samostatné spravované clustery Kubernetes hostované v Azure pomocí [stroje AKS](https://github.com/Azure/aks-engine)
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- Samostatné spravované clustery Kubernetes hostované na [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) nebo místně
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)
- [Kubernetes s povoleným ARC Azure](../../azure-arc/kubernetes/overview.md) (Preview)

Container Insights podporuje clustery s operačním systémem Linux a Windows Server 2019. Moduly runtime kontejnerů, které podporuje, jsou Docker, Moby a jakýkoli CRI kompatibilní modul runtime, jako je například CRI-O a kontejner.

Monitorování vašich kontejnerů je důležité, zejména pokud používáte produkční cluster ve velkém měřítku s více aplikacemi.

Služba Container Insights poskytuje přehled o výkonu díky shromažďování metrik paměti a procesoru z řadičů, uzlů a kontejnerů, které jsou k dispozici v Kubernetes prostřednictvím rozhraní API metrik. Shromažďují se také protokoly kontejnerů.  Po povolení monitorování z clusterů Kubernetes se metriky a protokoly automaticky shromažďují pomocí kontejnerové verze Log Analytics agenta pro Linux. Metriky se zapisují do úložiště metrik a data protokolu se zapisují do úložiště logs přidruženého k vašemu pracovnímu prostoru [Log Analytics](../logs/log-query-overview.md) .

![Architektura kontejneru Insights](./media/container-insights-overview/azmon-containers-architecture-01.png)

## <a name="what-does-container-insights-provide"></a>Co poskytuje kontejner Insights?

Container Insights nabízí komplexní monitorování s využitím různých funkcí Azure Monitor. Tyto funkce umožňují pochopit výkon a stav clusteru Kubernetes s operačním systémem Linux a Windows Server 2019 a zatížení kontejnerů. Díky službě Container Insights můžete:

* Identifikujte kontejnery AKS, které jsou spuštěny na uzlu, a jejich průměrné využití procesoru a paměti. Tato znalostní báze vám může přispět k identifikaci kritických bodů prostředků.
* Identifikujte využití procesoru a paměti u skupin kontejnerů a jejich kontejnerů hostovaných v Azure Container Instances.
* Identifikujte, kde se kontejner nachází v řadiči nebo pod. Tato znalostní báze vám může pomáhat zobrazit celkový výkon řadiče nebo pod ním.
* Prohlédněte si využití prostředků u úloh běžících na hostiteli, které nesouvisí se standardními procesy, které podporují rozhraní pod.
* Pochopení chování clusteru v rámci průměrného a nejtěžšího zatížení. Tato znalostní báze vám pomůže identifikovat požadavky na kapacitu a určit maximální zatížení, které může cluster tolerovat.
* Nakonfigurujte výstrahy tak, aby vás proaktivně upozornily nebo zaznamenaly, když využití procesoru a paměti na uzlech nebo kontejnerech překračuje vaše prahové hodnoty nebo když dojde ke změně stavu v clusteru v souhrnu stavu infrastruktury nebo uzlů.
* Integrací s [Prometheus](https://prometheus.io/docs/introduction/overview/) můžete zobrazit metriky aplikací a úloh, které shromažďuje z uzlů a Kubernetes pomocí [dotazů](container-insights-log-search.md) pro vytváření vlastních výstrah, řídicích panelů a podrobných podrobných analýz.
* Monitorujte úlohy kontejneru [nasazené do](https://github.com/Azure/aks-engine) místního a AKSového stroje AKS engine [na Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview).
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

Podívejte se na následující video, které poskytuje podrobné podrobně úrovně, které vám pomůžou získat informace o monitorování clusteru AKS s využitím služby Container Insights.

> [!VIDEO https://youtu.be/XEdwGvS2AwA]

## <a name="how-do-i-access-this-feature"></a>Návody k této funkci přistupovat?

Ke kontejneru najdete dva způsoby, od Azure Monitor nebo přímo z vybraného clusteru AKS. Z Azure Monitor máte globální perspektivu všech nasazených kontejnerů, které jsou monitorované a které nejsou, což vám umožní vyhledávat a filtrovat v rámci předplatných a skupin prostředků a pak přejít do kontejneru Insights z vybraného kontejneru.  V opačném případě můžete k této funkci přistupovat přímo z vybraného kontejneru AKS ze stránky AKS.

![Přehled metod pro přístup ke kontejneru Insights](./media/container-insights-overview/azmon-containers-experience.png)

Pokud vás zajímá monitorování a Správa hostitelů kontejnerů pro Docker a Windows, kteří používají mimo AKS k zobrazení konfigurace, auditu a využití prostředků, prostudujte si [řešení pro monitorování kontejnerů](./containers.md).

## <a name="next-steps"></a>Další kroky

Pokud chcete začít monitorovat cluster Kubernetes, přečtěte si, [Jak povolit službě Container Insights](container-insights-onboard.md) pochopit požadavky a dostupné metody pro povolení monitorování.
