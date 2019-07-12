---
title: Horizontální navýšení kapacity clusteru služby Průzkumník dat Azure
description: Tento článek popisuje kroky pro horizontální navýšení kapacity a škálování v clusteru Azure Průzkumník dat na základě změny poptávky.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 29bfcc42462a667850f0b2e1bbda3d29cd1597ab
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571514"
---
# <a name="manage-cluster-horizontal-scaling-to-accommodate-changing-demand"></a>Správa horizontální škálování clusteru tak, aby vyhovovaly měnících se požadavků

Nastavování velikosti clusteru správně je důležité pro výkon Průzkumník dat Azure. Ale nemůže být předpovězen nároky na cluster s absolutní přesnost. Velikost statické clusteru může vést k nízkého využití nebo overutilization, ani jedno z nich je ideální.

Lepším řešením je *škálování* cluster, přidávání a odebírání kapacitu s měnícími poptávky. Existují dva pracovní postupy pro změnu velikosti: 
* Horizontální škálování, nazývá se také škálování navýšení nebo snížení kapacity.
* Vertikální škálování, nazývá se také vertikálně navýšit nebo snížit kapacitu.

Tento článek vysvětluje horizontální škálování pracovního postupu.

Horizontální škálování umožňuje škálovat počet instancí automaticky na základě předdefinovaných pravidel a plány. Zadejte nastavení automatického škálování pro váš cluster na webu Azure Portal, jak je popsáno v tomto článku.

## <a name="steps-to-configure-horizontal-scaling"></a>Postup konfigurace horizontální škálování

Na webu Azure Portal přejděte do Průzkumníku dat prostředku clusteru. V části **nastavení** záhlaví, vyberte **horizontální navýšení kapacity**. 

Vyberte požadovanou automatické škálování metodu: **Ruční škálování**, **optimalizované automatického škálování** nebo **automatického škálování vlastní**.

### <a name="manual-scale"></a>Ruční škálování

Ruční škálování je ve výchozím nastavení se vytvoření clusteru. To znamená, že clusteru mít statické clusteru kapacitu, která se nezmění automaticky. Můžete zvolit statické kapacitu pomocí panelu a nezmění, dokud se příště se změní clusteru pro horizontální navýšení kapacity nastavení.

   ![Ruční škálování – metoda](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale"></a>Optimalizované automatického škálování

Optimalizované automatického škálování je metoda doporučené automatického škálování. Postup konfigurace automatického škálování optimalizovat:

1. Vybrali možnost automatického škálování optimalizovaný a volba dolní mez a horní limit množství instance clusteru, automatickým Škálováním, bude provedeno mezi tyto limity.
2. Klikněte na Uložit.

   ![Metoda optimalizované automatického škálování](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

Až kliknete na Uložit optimalizovat mechanismus automatického škálování začne pracovat a je akce se nebude zobrazovat v protokolu aktivit clusteru. Tuto metodu automatického škálování je optimalizace výkonu clusteru a náklady: Pokud cluster se spustí zobrazíte stav nízkého využití ho bude možné škálovat se změnami, které ponechat stejné a nižší náklady na výkon a cluster se spustí se získat stav služby overutilization, bude se jednat o horizontálním navýšením kapacity zajistit, aby že dobře funguje

### <a name="custom-autoscale"></a>Vlastní automatické škálování

Metoda vlastní automatického škálování vám umožní škálování clusteru dynamicky na základě metrik, který zadáte. Následující obrázek znázorňuje tok a postup konfigurace automatického škálování vlastní. Další podrobnosti podle obrázku.

1. V **název nastavení automatického škálování** pole, zadejte název, jako třeba *horizontální navýšení kapacity: využití mezipaměti*. 

   ![Pravítko měřítka](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. Pro **režim škálování**vyberte **škálování podle metriky**. Tento režim umožňuje dynamické škálování. Můžete také vybrat **škálovat na konkrétní počet instancí**.

3. Vyberte **+ přidat pravidlo**.

4. V **pravítko měřítka** části na pravé straně, zadejte hodnoty pro každé nastavení.

    **Kritéria**

    | Nastavení | Popis a hodnotu |
    | --- | --- |
    | **Časová agregace** | Vyberte kritéria agregaci, jako je například **průměrné**. |
    | **Název metriky** | Vyberte metriku, chcete, aby operace škálování na základě na, například **využití mezipaměti**. |
    | **Statistika agregačního intervalu** | Výběr mezi **průměrné**, **minimální**, **maximální**, a **součet**. |
    | **– Operátor** | Vyberte příslušnou možnost, například **větší než nebo rovna hodnotě**. |
    | **Prahová hodnota** | Zvolte příslušnou hodnotu. Pro využití mezipaměti, třeba 80 procent je dobrým výchozím bodem. |
    | **Doba trvání (v minutách)** | Zvolte odpovídající množství času pro systém vás pod rouškou zpět při výpočtu metrik. Začněte s výchozí hodnotu 10 minut. |
    |  |  |

    **Akce**

    | Nastavení | Popis a hodnotu |
    | --- | --- |
    | **Operace** | Vyberte příslušnou možnost v vertikálně nebo horizontálně navýšit kapacitu. |
    | **Počet instancí** | Vyberte počet uzlů nebo instance, které chcete přidat nebo odebrat, když se splní podmínku metriky. |
    | **Přestávka (minuty)** | Zvolte vhodný časový interval čekání mezi operace škálování. Začněte s výchozí pět minut. |
    |  |  |

5. Vyberte **Přidat**.

6. V **limity instancí** části na levé straně, zadejte hodnoty pro každé nastavení.

    | Nastavení | Popis a hodnotu |
    | --- | --- |
    | **Minimum** | Počet instancí, které váš cluster nebude škálování níže, bez ohledu na využití. |
    | **Maximální počet** | Počet instancí, které váš cluster nebude škálování nad, bez ohledu na využití. |
    | **Výchozí** | Výchozí počet instancí. Toto nastavení se používá, pokud dochází k problémům s čtení metrik prostředku. |
    |  |  |

7. Vyberte **Uložit**.

Nyní jste nakonfigurovali operace škálování pro váš cluster Průzkumník dat Azure. Přidáte další pravidlo pro operace škálování na méně instancí. Pokud potřebujete pomoc s problémy škálování clusterů [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal.

## <a name="next-steps"></a>Další postup

* [Sledování výkonu, stavu a využití s metrikami Průzkumník dat Azure](using-metrics.md)
* [Správa clusteru vertikální škálování](manage-cluster-vertical-scaling.md) vhodné k určení velikosti clusteru.
