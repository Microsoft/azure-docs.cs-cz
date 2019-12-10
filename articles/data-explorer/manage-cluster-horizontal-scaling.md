---
title: Správa horizontálního škálování clusteru (horizontální navýšení kapacity) v Azure Průzkumník dat, aby se vešly měnící se požadavky
description: Tento článek popisuje kroky k horizontálnímu navýšení kapacity a škálování v clusteru Azure Průzkumník dat na základě změny poptávky.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 52a9c0a13723361bbc93362cdd9e2c73ef0372f2
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74942235"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>Správa horizontálního škálování clusteru (horizontální navýšení kapacity) v Azure Průzkumník dat, aby se vešly měnící se požadavky

Odpovídající změna velikosti clusteru je zásadní pro výkon Azure Průzkumník dat. Statická velikost clusteru může vést k použití nebo převzetí služeb při selhání, ani z toho, co není ideální. Vzhledem k tomu, že poptávka na clusteru se nedá předpovědět s absolutní přesností, je lepší *škálovat* cluster, přidávat a odebírat kapacitu a prostředky procesoru se měnícími se požadavky. 

K dispozici jsou dva pracovní postupy pro škálování clusteru Azure Průzkumník dat: 
* Horizontální škálování, označované také jako škálování v a ven.
* [Vertikální škálování](manage-cluster-vertical-scaling.md), označované také jako horizontální navýšení kapacity.
Tento článek vysvětluje pracovní postup horizontálního škálování.

## <a name="configure-horizontal-scaling"></a>Konfigurace horizontálního škálování

Pomocí horizontálního škálování můžete automaticky škálovat počet instancí na základě předdefinovaných pravidel a plánů. Určení nastavení automatického škálování pro váš cluster:

1. V Azure Portal přejdete do svého prostředku clusteru Azure Průzkumník dat. V části **Nastavení**vyberte **horizontální**navýšení kapacity. 

2. V okně **horizontální** navýšení kapacity vyberte metodu automatického škálování, kterou chcete: **Ruční škálování**, **optimalizované automatické škálování**nebo **vlastní automatické škálování**.

### <a name="manual-scale"></a>Ruční škálování

Ruční škálování je výchozí nastavení při vytváření clusteru. Cluster má statickou kapacitu, která se nemění automaticky. Statickou kapacitu vyberete pomocí řádku **počet instancí** . Škálování clusteru zůstane v tomto nastavení, dokud neprovedete další změnu.

   ![Ruční škálování – metoda](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale-preview"></a>Optimalizované automatické škálování (Preview)

Optimalizované automatické škálování je doporučovanou metodou automatického škálování. Tato metoda optimalizuje výkon a náklady clusteru. Pokud cluster přistupuje ke stavu s využitím, bude se škálovat na. Tato akce snižuje náklady, ale udržuje úroveň výkonu. Pokud cluster přistupuje ke stavu nadlimitního využití, bude se škálovat na zachování optimálního výkonu. Konfigurace optimalizovaného automatického škálování:

1. Vyberte **optimalizované automatické škálování**. 

1. Vyberte minimální počet instancí a maximální počet instancí. Automatické škálování clusteru mezi těmito dvěma čísly závisí na zatížení.

1. Vyberte **Save** (Uložit).

   ![Optimalizovaná metoda automatického škálování](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

Optimalizované automatické škálování začíná pracovat. Akce se teď zobrazují v protokolu aktivit Azure clusteru.

#### <a name="logic-of-optimized-autoscale"></a>Logika optimalizovaného automatického škálování 

**Horizontální navýšení kapacity**

Když váš cluster přistupuje ke stavu nadlimitního využití, horizontální navýšení kapacity a udržení optimálního výkonu. K horizontálnímu navýšení kapacity dojde v těchto případech:
* Počet instancí clusteru je pod maximálním počtem instancí definovaných uživatelem.
* Využití mezipaměti je po celou hodinu vysoké.

> [!NOTE]
> Logika horizontálního navýšení kapacity aktuálně nebere v úvahu využití příjmu a metriky procesoru. Pokud jsou tyto metriky důležité pro váš případ použití, použijte [vlastní automatické škálování](#custom-autoscale).

**Horizontální navýšení kapacity**

Když váš cluster přistupuje ke stavu nižšího využití, Škálujte, abyste snížili náklady, ale zachovali výkon. K ověření, že je v clusteru bezpečné škálování, se používá několik metrik. Následující pravidla jsou vyhodnocována každý den po dobu 7 dnů před provedením horizontálního navýšení kapacity:
* Počet instancí je nad 2 a nad minimálním počtem definovaných instancí.
* Aby se zajistilo, že neexistují žádné přetížení prostředků, je nutné před provedením škálování ověřit následující metriky: 
    * Využití mezipaměti není vysoké
    * PROCESOR je pod průměrem 
    * Využití příjmu je pod průměrem. 
    * Použití ingestování pro příjem dat (Pokud se používá streamování příjmu) není vysoké.
    * Události Keep Alive jsou nad definovaným minimem, zpracované správně a včas.
    * Neomezovat žádné dotazy 
    * Počet neúspěšných dotazů je pod definovaným minimem.

> [!NOTE]
> Škálování v logice momentálně vyžaduje 7 dní zkušební období před implementací optimalizovaného škálování v. Toto hodnocení proběhne jednou za 24 hodin. Pokud je potřeba rychlá změna, použijte [Ruční škálování](#manual-scale).

### <a name="custom-autoscale"></a>Vlastní automatické škálování

Pomocí vlastního automatického škálování můžete cluster dynamicky škálovat na základě metrik, které zadáte. Následující obrázek znázorňuje tok a kroky pro konfiguraci vlastního automatického škálování. Další podrobnosti následují po obrázku.

1. Do pole **název nastavení automatického škálování** zadejte název, jako je například horizontální navýšení *kapacity: využití mezipaměti*. 

   ![Pravítko měřítka](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. V případě **režimu škálování**vyberte **škálovat na základě metriky**. Tento režim poskytuje dynamické škálování. Můžete také vybrat možnost **škálovat na konkrétní počet instancí**.

3. Vyberte **+ Přidat pravidlo**.

4. V části **pravidlo škálování** na pravé straně zadejte hodnoty pro každé nastavení.

    **Kritéria**

    | Nastavení | Popis a hodnotu |
    | --- | --- |
    | **Časová agregace** | Vyberte kritéria agregace, například **průměr**. |
    | **Název metriky** | Vyberte metriku, na které se má operace škálování zakládat, jako je například **využití mezipaměti**. |
    | **Statistika časových intervalů** | Vyberte **průměrnou**, **minimální**, **maximální**a **součet**. |
    | **– Operátor** | Vyberte odpovídající možnost, například je **větší nebo rovna**. |
    | **Mezí** | Vyberte vhodnou hodnotu. Například pro využití mezipaměti je dobrým výchozím bodem 80 procent. |
    | **Doba trvání (v minutách)** | Vyberte odpovídající čas, po který se má systém při výpočtu metriky podívat na pozadí. Začněte s výchozím nastavením 10 minut. |
    |  |  |

    **Akce**

    | Nastavení | Popis a hodnotu |
    | --- | --- |
    | **Operace** | Vyberte vhodnou možnost pro horizontální navýšení nebo navýšení kapacity. |
    | **Počet instancí** | Vyberte počet uzlů nebo instancí, které chcete přidat nebo odebrat, když je splněna podmínka metriky. |
    | **Doba vychladnutí (minuty)** | Vyberte vhodný časový interval pro čekání mezi operacemi škálování. Začněte s výchozím nastavením pět minut. |
    |  |  |

5. Vyberte **Přidat**.

6. V části **omezení instancí** na levé straně zadejte hodnoty pro každé nastavení.

    | Nastavení | Popis a hodnotu |
    | --- | --- |
    | **Minimálně** | Počet instancí, které váš cluster nebude škálovat níže, bez ohledu na využití. |
    | **Velikosti** | Počet instancí, které váš cluster nebude škálovat výše, bez ohledu na využití. |
    | **Výchozí** | Výchozí počet instancí. Toto nastavení se používá, pokud dochází k problémům se čtením metrik prostředků. |
    |  |  |

7. Vyberte **Save** (Uložit).

Nyní jste nakonfigurovali horizontální škálování pro váš cluster Průzkumník dat Azure. Přidejte další pravidlo pro vertikální škálování. Pokud potřebujete pomoc s problémy s škálováním clusteru, [otevřete žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) v Azure Portal.

## <a name="next-steps"></a>Další kroky

* [Monitorování výkonu, stavu a využití Azure Průzkumník dat s metrikami](using-metrics.md)
* [Správa vertikálního škálování clusteru](manage-cluster-vertical-scaling.md) pro vhodné určení velikosti clusteru
