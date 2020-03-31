---
title: Sledování stavu clusteru Kubernetes pomocí Azure Monitoru pro kontejnery | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak můžete zobrazit a analyzovat stav clusterů AKS a neaks pomocí Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: f50ef13efca78bbb5285b99759b8111dc1915ad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843986"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Principy stavu clusteru Kubernetes pomocí Azure Monitoru pro kontejnery

Pomocí Azure Monitor pro kontejnery monitoruje a hlásí stav komponent spravované infrastruktury a všech uzlů spuštěných v libovolném clusteru Kubernetes podporovaném Službou Azure Monitor pro kontejnery. Toto prostředí přesahuje stav clusteru vypočtené a hlášené v [zobrazení více clusterů](container-insights-analyze.md#multi-cluster-view-from-azure-monitor), kde nyní můžete pochopit, pokud jeden nebo více uzlů v clusteru jsou omezené prostředky nebo uzel nebo pod není k dispozici, které by mohly mít vliv na spuštěnou aplikaci v clusteru na základě kurátorovaných metrik.

>[!NOTE]
>Funkce Stav je v tuto chvíli ve verzi Public Preview.
>

Informace o tom, jak povolit Azure Monitor pro kontejnery, najdete [v tématu Onboard Azure Monitor pro kontejnery](container-insights-onboard.md).

>[!NOTE]
>Chcete-li podporovat clustery modulu AKS, ověřte, zda splňují následující požadavky:
>- Používá nejnovější verzi [helmklienta](https://helm.sh/docs/using_helm/).
>- Kontejnerová verze agenta je *microsoft/oms:ciprod11012019*. Informace o inovaci agenta naleznete v [tématu upgrade agenta v clusteru Kubernetes](container-insights-manage-agent.md#upgrade-agent-on-monitored-kubernetes-cluster).
>

## <a name="overview"></a>Přehled

V Azure Monitor pro kontejnery funkce stavu (preview) poskytuje proaktivní monitorování stavu clusteru Kubernetes, který vám pomůže identifikovat a diagnostikovat problémy. To vám dává možnost zobrazit významné zjištěné problémy. Monitoruje stav vašeho clusteru spustit na kontejnerizovaného agenta v clusteru a data stavu se zapisuje do tabulky **KubeHealth** v pracovním prostoru Log Analytics. 

Stav clusteru Kubernetes je založen na řadě scénářů monitorování uspořádaných podle následujících objektů a abstrakcí Kubernetes:

- Infrastruktura Kubernetes – poskytuje souhrn serveru Rozhraní API Kubernetes, replikami a sady daemonsets spuštěné na uzlech nasazených v clusteru vyhodnocením využití procesoru a paměti a dostupnosti podů

    ![Zobrazení souhrnu stavu infrastruktury Kubernetes](./media/container-insights-health/health-view-kube-infra-01.png)

- Uzly – poskytuje souhrn fondů uzlů a stavu jednotlivých uzlů v každém fondu vyhodnocením využití procesoru a paměti a stavu uzlu, jak je uvádí Kubernetes.

    ![Souhrnné zobrazení stavu uzlů](./media/container-insights-health/health-view-nodes-01.png)

V současné době je podporován pouze stav virtuálníkubelet. Stav procesoru a využití paměti virtuálních kubletových uzlů je hlášen jako **Neznámý**, protože z nich není přijat signál.

Všechny monitory jsou zobrazeny v hierarchickém rozložení v podokně Hierarchie stavu, kde agregační monitor představující objekt nebo abstrakci Kubernetes (tj. kubernetesova infrastruktura nebo uzly) je nejvyšší monitor odrážející kombinovaný stav všech závislé dětské monitory. Klíčové scénáře monitorování používané k odvození stavu jsou:

* Vyhodnoťte využití procesoru z uzlu a kontejneru.
* Vyhodnoťte využití paměti z uzlu a kontejneru.
* Stav podů a uzlů na základě výpočtu jejich pohotovostního stavu hlášeného Kubernetesem.

Toto jsou ikony používané k označení stavu:

|Ikona|Význam|  
|--------|-----------|  
|![Zelená ikona zaškrtnutí označující bezvadný stav](./media/container-insights-health/healthyicon.png)|Úspěch, stav je OK (zelený)|  
|![Žlutý trojúhelník a vykřičník představující upozornění](./media/container-insights-health/warningicon.png)|Varování (žlutý)|  
|![Červené tlačítko s bílým symbolem X označující kritický stav](./media/container-insights-health/criticalicon.png)|Kritický (červený)|  
|![Ikona šedého](./media/container-insights-health/grayicon.png)|Neznámý (šedý)|  

## <a name="monitor-configuration"></a>Konfigurace monitoru

Informace o chování a konfiguraci každého monitoru podporujícího funkci Azure Monitor for containers Health najdete v [tématu Průvodce konfigurací monitorování stavu](container-insights-health-monitors-config.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com). 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>Zobrazení stavu clusteru AKS nebo jiného clusteru aks

Přístup k funkci Azure Monitor pro kontejnery Stav (preview) je k dispozici přímo z clusteru AKS výběrem **přehledy** z levého podokna na webu Azure Portal. V části **Přehledy** vyberte **Kontejnery**. 

Chcete-li zobrazit stav z clusteru, který není AKS clusteru, který je cluster AKS Engine hostovaný místně nebo v Azure Stack, vyberte **Azure Monitor** z levého podokna na webu Azure Portal. V části **Přehledy** vyberte **Kontejnery**.  Na stránce s více clustery vyberte ze seznamu cluster, který není aks.

V Azure Monitor pro kontejnery, na stránce **Clusteru,** vyberte **Stav**.

![Příklad řídicího panelu stavu clusteru](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>Kontrola stavu clusteru

Když se otevře stránka Stav, ve výchozím nastavení je v **mřížce Aspekt stavu** **vybrána infrastruktura Kubernetes.**  Mřížka shrnuje aktuální stav souhrnu infrastruktury Kubernetes a uzlů clusteru. Výběrem obou aspektů stavu se aktualizují výsledky v podokně Hierarchie stavu (tj. v prostředním podokně) a zobrazí se všechny podřízené monitory v hierarchickém rozložení a zobrazí jejich aktuální stav. Chcete-li zobrazit další informace o závislém monitoru, můžete vybrat jeden a podokno vlastností se automaticky zobrazí na pravé straně stránky. 

![Podokno vlastností stavu clusteru](./media/container-insights-health/health-view-property-pane.png)

V podokně vlastností se dozvíte následující:

- Na kartě **Přehled** zobrazuje aktuální stav vybraného monitoru, kdy byl monitor naposledy vypočítán a kdy došlo k poslední změně stavu. Další informace jsou zobrazeny v závislosti na typu monitoru vybraného v hierarchii.

    Pokud vyberete agregační monitorování v podokně Hierarchie stavu, v kartě **Přehled** v podokně vlastností se zobrazí souhrn celkového počtu podřízených monitorů v hierarchii a počet agregačních monitorů v kritickém stavu, upozornění a stavu V pořádku. 

    ![Karta Přehled podokna vlastností stavu pro agregovaný monitor](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Pokud vyberete monitorování jednotky v podokně Hierarchie stavu, zobrazí se také v části **Poslední stav změnit** předchozí vzorky vypočtené a hlášené kontejnerizovaným agentem během posledních čtyř hodin. To je založeno na výpočtu monitorování jednotek pro porovnání několika po sobě jdoucích hodnot k určení jeho stavu. Například pokud jste vybrali *pod ready stav* jednotky monitoru, zobrazí poslední dva vzorky řízené parametrem *ConsecutiveSamplesForStateTransition*. Další informace naleznete v podrobném popisu [jednotkových monitorů](container-insights-health-monitors-config.md#unit-monitors).
    
    ![Karta Přehled podokna vlastností stavu](./media/container-insights-health/health-overview-unit-monitor.png)

    Pokud je čas hlášený **poslední změnou stavu** den nebo starší, je výsledkem žádné změny stavu monitoru. Pokud je však poslední vzorek přijatý pro monitorování jednotky starší než čtyři hodiny, pravděpodobně to znamená, že kontejnerizovaný agent neodesílal data. Pokud agent ví, že určitý prostředek existuje, například uzel, ale nepřijal data z monitorování využití procesoru uzlu nebo paměti (jako příklad), je stav monitoru nastaven na **neznámý**.  

- Na kartě**Config** se zobrazuje výchozí nastavení konfiguračních parametrů (pouze pro monitorování jednotek, nikoli pro agregační monitory) a jejich hodnoty.
- Na kartě **Znalosti** obsahuje informace vysvětlující chování monitoru a způsob vyhodnocení stavu není v pořádku.

Data monitorování na této stránce se neaktualizují automaticky a je třeba vybrat **aktualizovat** v horní části stránky, abyste viděli nejnovější stav přijatý z clusteru.

## <a name="next-steps"></a>Další kroky

Chcete-li zobrazit předdefinované dotazy a příklady pro vyhodnocení nebo přizpůsobení tak, aby bylo možné zobrazit, vizualizovat nebo analyzovat clustery, zobrazit [příklady dotazů](container-insights-log-search.md#search-logs-to-analyze-data) protokolu.
