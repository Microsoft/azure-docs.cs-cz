---
title: Správa horizontálního škálování clusteru (horizontální navýšení kapacity) tak, aby odpovídalo poptávce v Průzkumníku dat Azure
description: Tento článek popisuje kroky horizontálního navýšení kapacity a škálování v clusteru Azure Data Explorer na základě měnící se poptávky.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: ff7420619cffc2287ab8ff6332df605d56329549
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664129"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>Správa horizontálního škálování clusteru (horizontálního navýšení kapacity) v Průzkumníku dat Azure tak, aby vyhovovaly měnícím se poptávce

Vhodné nastavení velikosti clusteru je důležité pro výkon Průzkumníka dat Azure. Velikost statického clusteru může vést k nedostatečnému využití nebo nadměrnému využití, z nichž ani jedno není ideální. Vzhledem k tomu, že poptávku v clusteru nelze předpovědět s absolutní přesností, je lepší *škálovat* cluster, přidávat a odebírat kapacitu a prostředky procesoru s měnící se poptávkou. 

Existují dva pracovní postupy pro škálování clusteru Průzkumníka dat Azure: 
* Horizontální škálování, nazývané také změna měřítka dovnitř a ven.
* [Vertikální škálování](manage-cluster-vertical-scaling.md), nazývané také škálování nahoru a dolů.
Tento článek vysvětluje pracovní postup horizontální škálování.

## <a name="configure-horizontal-scaling"></a>Konfigurace vodorovného měřítka

Pomocí vodorovného měřítka můžete automaticky škálovat počet instancí na základě předdefinovaných pravidel a plánů. Určení nastavení automatického škálování pro váš cluster:

1. Na webu Azure Portal přejděte na prostředek clusteru Azure Data Explorer. V části **Nastavení**vyberte **Horizontální navýšení kapacity**. 

2. V okně **Škálování vyberte** požadovanou metodu automatického **škálování:** **Ruční škálování**, **Optimalizované automatické škálování**nebo Vlastní automatické škálování .

### <a name="manual-scale"></a>Ruční měřítko

Ruční škálování je výchozí nastavení při vytváření clusteru. Cluster má statickou kapacitu, která se automaticky nemění. Statickou kapacitu vyberete pomocí pruhu **počtu instancí.** Škálování clusteru zůstane v tomto nastavení, dokud neprovedete další změnu.

   ![Metoda ručního měřítka](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale-preview"></a>Optimalizované automatické škálování (náhled)

Optimalizované automatické škálování je doporučená metoda automatického škálování. Tato metoda optimalizuje výkon clusteru a náklady. Pokud se cluster přiblíží stavu nedostatečného využití, bude škálován. Tato akce snižuje náklady, ale udržuje úroveň výkonu. Pokud se cluster blíží stavu nadměrného využití, bude škálován, aby byl zachován optimální výkon. Konfigurace optimalizovaného automatického škálování:

1. Vyberte **Optimalizované automatické škálování**. 

1. Vyberte minimální počet instancí a maximální počet instancí. Automatické škálování clusteru se pohybuje mezi těmito dvěma čísly na základě zatížení.

1. Vyberte **Uložit**.

   ![Optimalizovaná metoda automatického škálování](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

Optimalizované automatické škálování začíná fungovat. Jeho akce jsou teď viditelné v protokolu aktivit Azure clusteru.

#### <a name="logic-of-optimized-autoscale"></a>Logika optimalizovaného automatického škálování 

**Horizontální navýšení kapacity**

Když se váš cluster blíží stavu nadměrného využití, horizontální navýšení kapacity pro udržení optimálního výkonu. Horizontální navýšení kapacity dojde, když:
* Počet instancí clusteru je nižší než maximální počet instancí definovanýuživatelem.
* Využití mezipaměti je vysoké více než hodinu.
* Procesor je vysoký více než hodinu.
* Využití požití je vysoké více než hodinu.

> [!NOTE]
> Horizontální navýšení kapacity logiky není aktuálně zvážit metriku využití ingestování. Pokud je tato metrika důležitá pro případ použití, použijte [vlastní automatické škálování](#custom-autoscale).

**Horizontální snížení kapacity**

Když se váš cluster přiblíží stavu nedostatečného využití, škálujte v nastavení, abyste snížili náklady, ale zachováte výkon. Více metrik se používá k ověření, že je bezpečné škálování v clusteru. Následující pravidla jsou vyhodnocována každou hodinu po dobu 6 hodin před provedením stupnice v:
* Počet instancí je vyšší než 2 a vyšší než minimální počet definovaných instancí.
* Chcete-li zajistit, že nedojde k přetížení prostředků, musí být před provedením škálování v následujícím zařízení ověřeny následující metriky: 
    * Využití mezipaměti není vysoké
    * CPU je pod průměrem 
    * Využití požití je podprůměrem 
    * Využití ingestování datových proudů (pokud se používá ingestestování streamování) není vysoká
    * Keep alive události jsou nad definované minimum, správně zpracovány a včas.
    * Žádné omezení dotazu 
    * Počet neúspěšných dotazů je nižší než definované minimum.

> [!NOTE]
> Škálování v logice aktuálně vyžaduje 7denní vyhodnocení před implementací optimalizovaného škálování. Toto hodnocení probíhá jednou za 24 hodin. Pokud je nutná rychlá změna, použijte [ruční měřítko](#manual-scale).

### <a name="custom-autoscale"></a>Vlastní automatické škálování

Pomocí vlastního automatického škálování můžete dynamicky škálovat cluster na základě zadaných metrik. Následující obrázek znázorňuje tok a kroky konfigurace vlastního automatického škálování. Další podrobnosti následují podle grafiky.

1. Do pole **Název nastavení automatického škálování** zadejte název, například *Horizontální navýšení kapacity: využití mezipaměti*. 

   ![Pravidlo měřítka](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. V **režimu škálování**vyberte **měřítko na základě metriky**. Tento režim poskytuje dynamické škálování. Můžete také vybrat **měřítko pro konkrétní počet instancí**.

3. Vyberte **+ Přidat pravidlo**.

4. V části **Měřítko pravidla** vpravo zadejte hodnoty pro každé nastavení.

    **Kritéria**

    | Nastavení | Popis a hodnota |
    | --- | --- |
    | **Časová agregace** | Vyberte kritéria agregace, například **Průměr**. |
    | **Název metriky** | Vyberte metriku, na které má být operace škálování založena, například **Využití mezipaměti**. |
    | **Statistika agregačního intervalu** | Vyberte **mezi průměrem**, **minimem**, **maximem**a **součtem**. |
    | **Operátor** | Zvolte příslušnou možnost, například **Větší než nebo rovno**. |
    | **Práh** | Zvolte vhodnou hodnotu. Například pro využití mezipaměti 80 procent je dobrým výchozím bodem. |
    | **Doba trvání (v minutách)** | Zvolte vhodné množství času, po které se systém může při výpočtu metrik ohlížet zpět. Začněte s výchozím nastavením 10 minut. |
    |  |  |

    **Akce**

    | Nastavení | Popis a hodnota |
    | --- | --- |
    | **Operace** | Zvolte vhodnou možnost pro škálování v nebo horizontální navýšení kapacity. |
    | **Počet instancí** | Zvolte počet uzlů nebo instancí, které chcete přidat nebo odebrat, když je splněna podmínka metriky. |
    | **Přestávka (minuty)** | Zvolte vhodný časový interval, který chcete čekat mezi operacemi škálování. Začněte s výchozím nastavením pěti minut. |
    |  |  |

5. Vyberte **Přidat**.

6. V části **Limity instancí** vlevo zadejte hodnoty pro každé nastavení.

    | Nastavení | Popis a hodnota |
    | --- | --- |
    | **Minimální** | Počet instancí, které váš cluster nebude škálovat níže, bez ohledu na využití. |
    | **Maximum** | Počet instancí, které váš cluster nebude škálovat výše, bez ohledu na využití. |
    | **Výchozí** | Výchozí počet instancí. Toto nastavení se používá, pokud jsou problémy se čtením metrik prostředků. |
    |  |  |

7. Vyberte **Uložit**.

Teď jste nakonfigurovali horizontální škálování pro cluster Průzkumníka dat Azure. Přidejte další pravidlo pro vertikální změnu měřítka. Pokud potřebujete pomoc s problémy s škálováním clusteru, [otevřete žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal.

## <a name="next-steps"></a>Další kroky

* [Sledování výkonu, stavu a využití Azure Data Exploreru pomocí metrik](using-metrics.md)
* [Spravujte vertikální škálování clusteru](manage-cluster-vertical-scaling.md) pro příslušnou velikost clusteru.
