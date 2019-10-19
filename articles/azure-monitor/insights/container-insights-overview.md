---
title: Přehled Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje Azure Monitor pro kontejnery, které monitorují řešení AKS Container Insights a hodnotu, kterou zajišťuje, pomocí monitorování stavu clusterů AKS a Container Instances v Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 08/02/2019
ms.openlocfilehash: 8a6a9862679508971edd23b2d4220446c2692d72
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555372"
---
# <a name="azure-monitor-for-containers-overview"></a>Přehled Azure Monitor for Containers

Azure Monitor for Containers je funkce navržená tak, aby sledovala výkon úloh kontejneru nasazených do Azure Container Instances nebo spravovaných clusterů Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS). Monitorování vašich kontejnerů je důležité, zejména pokud používáte produkční cluster ve velkém měřítku s více aplikacemi.

Azure Monitor pro kontejnery poskytují přehled o výkonu tím, že shromažďují metriky paměti a procesoru z řadičů, uzlů a kontejnerů, které jsou k dispozici v Kubernetes prostřednictvím rozhraní API metrik. Shromažďují se také protokoly kontejnerů.  Po povolení monitorování z clusterů Kubernetes se metriky a protokoly automaticky shromažďují pomocí kontejnerové verze Log Analytics agenta pro Linux. Metriky se zapisují do úložiště metrik a data protokolu se zapisují do úložiště logs přidruženého k vašemu pracovnímu prostoru [Log Analytics](../log-query/log-query-overview.md) . 

![Architektura Azure Monitor for Containers](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Co Azure Monitor kontejnerů poskytuje?

Azure Monitor pro kontejnery nabízí ucelené monitorování pomocí různých funkcí Azure Monitor, které vám umožní pochopit výkon a stav clusteru Kubernetes a zatížení kontejnerů. Pomocí Azure Monitor pro kontejnery můžete:

* Identifikujte kontejnery AKS, které jsou spuštěny na uzlu, a jejich průměrné využití procesoru a paměti. Tato znalostní báze vám může přispět k identifikaci kritických bodů prostředků.
* Identifikujte využití procesoru a paměti u skupin kontejnerů a jejich kontejnerů hostovaných v Azure Container Instances.  
* Identifikujte, kde se kontejner nachází v řadiči nebo pod. Tato znalostní báze vám může pomáhat zobrazit celkový výkon řadiče nebo pod ním.
* Prohlédněte si využití prostředků u úloh běžících na hostiteli, které nesouvisí se standardními procesy, které podporují rozhraní pod.
* Pochopení chování clusteru v rámci průměrného a nejtěžšího zatížení. Tato znalostní báze vám pomůže identifikovat požadavky na kapacitu a určit maximální zatížení, které může cluster tolerovat. 
* Konfigurace výstrah k proaktivnímu upozorňování nebo zaznamenání, když využití procesoru a paměti na uzlech nebo kontejnerech překročí vaše prahové hodnoty.
* Integrací s [Prometheus](https://prometheus.io/docs/introduction/overview/) můžete zobrazit metriky aplikací a úloh, které shromažďuje z uzlů a Kubernetes pomocí [dotazů](container-insights-log-search.md) pro vytváření vlastních výstrah, řídicích panelů a podrobných podrobných analýz.

    >[!NOTE]
    >Podpora pro Prometheus je ve verzi Public Preview v současnosti funkce.
    >

* Monitorujte úlohy kontejneru [nasazené do AKS-Engine](https://github.com/microsoft/OMS-docker/tree/aks-engine).

Podívejte se na následující video, které poskytuje podrobné podrobně úrovně, které vám pomůžou získat informace o monitorování clusteru AKS pomocí Azure Monitor pro kontejnery.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Návody k této funkci přistupovat?

K Azure Monitor kontejnerů můžete přistupovat dvěma způsoby, od Azure Monitor nebo přímo z vybraného clusteru AKS. Z Azure Monitor máte globální perspektivu všech nasazených kontejnerů, které jsou monitorované a které nejsou, což vám umožní vyhledávat a filtrovat v rámci předplatných a skupin prostředků a pak přejít na Azure Monitor pro kontejnery z vybraný kontejner.  V opačném případě můžete k této funkci přistupovat přímo z vybraného kontejneru AKS ze stránky AKS.  

![Přehled metod pro přístup k Azure Monitor pro kontejnery](./media/container-insights-overview/azmon-containers-experience.png)

Pokud vás zajímá monitorování a Správa hostitelů kontejnerů pro Docker a Windows, kteří používají mimo AKS k zobrazení konfigurace, auditu a využití prostředků, prostudujte si [řešení pro monitorování kontejnerů](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Další kroky

Pokud chcete začít monitorovat cluster AKS, přečtěte si, [Jak povolit Azure monitor kontejnerů](container-insights-onboard.md) pro pochopení požadavků a dostupných metod, které umožňují monitorování.  
