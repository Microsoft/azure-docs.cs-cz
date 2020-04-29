---
title: Monitorování stavu clusteru Kubernetes s využitím Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje, jak můžete zobrazit a analyzovat stav svých clusterů AKS a non-AKS pomocí Azure Monitor for Containers.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: f50ef13efca78bbb5285b99759b8111dc1915ad0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76843986"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Pochopení stavu clusteru Kubernetes s využitím Azure Monitor pro kontejnery

U Azure Monitor pro kontejnery monitoruje a hlásí stav komponent spravované infrastruktury a všechny uzly spuštěné v jakémkoli clusteru Kubernetes podporovaném Azure Monitor for Containers. Toto prostředí překračuje stav clusteru vypočtený a nahlášený v [zobrazení více clusterů](container-insights-analyze.md#multi-cluster-view-from-azure-monitor), kde teď můžete pochopit, jestli je jeden nebo více uzlů v clusteru omezené nebo že uzel nebo uzel je nedostupný, což by mohlo mít vliv na spuštěnou aplikaci v clusteru na základě spravovaných metrik.

>[!NOTE]
>Funkce Health je v tuto chvíli ve verzi Public Preview.
>

Informace o tom, jak povolit Azure Monitor pro kontejnery, najdete v tématu připojení [Azure monitor kontejnerů](container-insights-onboard.md).

>[!NOTE]
>Pro podporu clusterů modulu AKS ověřte, že splňuje tyto požadavky:
>- Používá nejnovější verzi [klienta Helm](https://helm.sh/docs/using_helm/).
>- Verze agenta s kontejnerem je *Microsoft/OMS: ciprod11012019*. Chcete-li upgradovat agenta, přečtěte si téma [Upgrade agenta v clusteru Kubernetes](container-insights-manage-agent.md#upgrade-agent-on-monitored-kubernetes-cluster).
>

## <a name="overview"></a>Přehled

V Azure Monitor pro kontejnery poskytuje funkce stavu (Preview) proaktivní monitorování stavu clusteru Kubernetes, který vám usnadní identifikaci a diagnostiku problémů. Poskytuje možnost Zobrazit zjištěné významné problémy. Monitory vyhodnocující stav clusteru se spouští ve vašem clusteru s kontejnerem a data o stavu se zapisují do tabulky **KubeHealth** v pracovním prostoru Log Analytics. 

Stav clusteru Kubernetes je založený na mnoha scénářích monitorování uspořádaných podle následujících Kubernetes objektů a abstrakcí:

- Infrastruktura Kubernetes – poskytuje souhrn serveru rozhraní Kubernetes API, ReplicaSets a DaemonSets běžících na uzlech nasazených ve vašem clusteru vyhodnocením využití procesoru a paměti a dostupnosti lusků.

    ![Zobrazení souhrnu stavu infrastruktury Kubernetes](./media/container-insights-health/health-view-kube-infra-01.png)

- Uzly – poskytuje souhrn fondů uzlů a stavu jednotlivých uzlů v jednotlivých fondech vyhodnocením využití procesoru a paměti a stavu uzlu, jak je uvedeno v Kubernetes.

    ![Souhrnné zobrazení stavu uzlů](./media/container-insights-health/health-view-nodes-01.png)

V současné době je podporován pouze stav Virtual kubelet. Stav využití CPU a paměti virtuálních kublet uzlů je hlášený jako **Neznámý**, protože z nich není přijat signál.

Všechna monitorování se zobrazují v hierarchickém rozložení v podokně hierarchie stavů, kde agregované monitorování představující objekt Kubernetes nebo abstrakci (tj. Kubernetes infrastruktury nebo uzly) představuje kombinovaný stav všech závislých podřízených sledování. Klíčové scénáře monitorování, které se používají k odvození stavu:

* Vyhodnotit využití procesoru z uzlu a kontejneru.
* Vyhodnotit využití paměti z uzlu a kontejneru.
* Stav lusků a uzlů na základě výpočtu stavu připraveného pomocí Kubernetes.

Toto jsou ikony používané k označení stavu:

|Ikona|Význam|  
|--------|-----------|  
|![Zelená ikona zaškrtnutí označující bezvadný stav](./media/container-insights-health/healthyicon.png)|Úspěch, stav je OK (zelený)|  
|![Žlutý trojúhelník a vykřičník představující upozornění](./media/container-insights-health/warningicon.png)|Varování (žlutý)|  
|![Červené tlačítko s bílým symbolem X označující kritický stav](./media/container-insights-health/criticalicon.png)|Kritický (červený)|  
|![Ikona šedá](./media/container-insights-health/grayicon.png)|Neznámé (šedá)|  

## <a name="monitor-configuration"></a>Konfigurace monitorování

Informace o chování a konfiguraci jednotlivých monitorů, které podporují Azure Monitor pro funkci stavu kontejnerů, najdete v tématu [Průvodce konfigurací monitorování stavu](container-insights-health-monitors-config.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>Zobrazení stavu clusteru AKS nebo non-AKS

Přístup k funkci Azure Monitor for Containers Health (Preview) je k dispozici přímo z clusteru AKS, a to tak, že v levém podokně Azure Portal vyberete **přehledy** . V části **přehledy** vyberte **kontejnery**. 

Pokud chcete zobrazit stav z neAKSho clusteru, který je hostitelem AKS modulu, který je hostovaný místně nebo na Azure Stack, vyberte **Azure monitor** v levém podokně Azure Portal. V části **přehledy** vyberte **kontejnery**.  Na stránce více clusterů vyberte v seznamu cluster bez AKS.

V Azure Monitor pro kontejnery na stránce **cluster** vyberte **stav**.

![Příklad řídicího panelu stavu clusteru](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>Kontrola stavu clusteru

Když se otevře stránka stav, ve výchozím nastavení se v mřížce **aspektů stavu** vybere **Kubernetes infrastruktura** .  Mřížka shrnuje aktuální stav souhrnu stavu infrastruktury Kubernetes a uzlů clusteru. Výběrem možnosti stavový aspekt aktualizujete výsledky v podokně hierarchie stavu (tj. v prostředním podokně) a zobrazíte všechna podřízená monitorování v hierarchickém rozložení a zobrazíte jejich aktuální stav. Chcete-li zobrazit další informace o jakémkoli závislém monitorování, můžete vybrat jeden a podokno vlastností se automaticky zobrazí na pravé straně stránky. 

![Podokno vlastností stavu clusteru](./media/container-insights-health/health-view-property-pane.png)

V podokně vlastností se naučíte následující:

- Na kartě **Přehled** se zobrazuje aktuální stav vybraného monitorování, čas posledního výpočtu monitorování a čas poslední změny stavu. Další informace jsou uvedeny v závislosti na typu monitorování vybraného v hierarchii.

    Pokud vyberete agregované monitorování v podokně hierarchie stavů, na kartě **Přehled** v podokně vlastností se zobrazí souhrn celkového počtu podřízených monitorování v hierarchii a kolik agregačních monitorování je v kritickém stavu, v upozornění a v dobrém stavu. 

    ![Karta Přehled podokna vlastností stavu pro agregované monitorování](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Pokud vyberete monitorování jednotek v podokně hierarchie stavů, zobrazí se také v části **Poslední změna stavu** předchozí ukázky počítané a hlášené agentem kontejneru během posledních čtyř hodin. To je založeno na výpočtu jednotek monitorování pro porovnání několika po sobě jdoucích hodnot a určení jejího stavu. Pokud jste například vybrali monitorování jednotky *stavu s připraveným stavem* , zobrazí se poslední dvě ukázky, které jsou ovládány parametrem *ConsecutiveSamplesForStateTransition*. Další informace najdete v podrobném popisu [monitorování jednotek](container-insights-health-monitors-config.md#unit-monitors).
    
    ![Panel vlastností stavu – karta Přehled](./media/container-insights-health/health-overview-unit-monitor.png)

    Pokud je čas hlášených **Poslední změnou stavu** den nebo starší, jedná se o výsledek žádné změny stavu monitorování. Pokud je však poslední vzorek přijatý pro monitorování jednotky delší než čtyři hodiny, to pravděpodobně znamená, že kontejnerový agent neodesílal data. Pokud agent ví, že určitý prostředek existuje, například uzel, ale nedostal data z monitorování využití procesoru nebo paměti uzlu (jako příklad), pak je stav monitorování nastaven na **Neznámý**.  

- Na kartě**Konfigurace** se zobrazuje výchozí nastavení parametrů konfigurace (jenom pro monitory jednotek, ne pro agregované monitory) a jejich hodnoty.
- Na kartě **znalostní báze** obsahuje informace vysvětlující chování monitorování a jejich vyhodnocování pro stav není v pořádku.

Data monitorování na této stránce se automaticky neaktualizují a v horní části stránky je nutné vybrat možnost **aktualizovat** , aby se zobrazil nejnovější stav přijatý z clusteru.

## <a name="next-steps"></a>Další kroky

V [příkladech dotazů protokolu](container-insights-log-search.md#search-logs-to-analyze-data) si můžete prohlédnout předdefinované dotazy a příklady pro vyhodnocení nebo přizpůsobení výstrah, vizualizaci nebo analýze clusterů.
