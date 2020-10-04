---
title: Monitorování více časových řad v jednom pravidle výstrahy metriky
description: Výstraha ve velkém měřítku s použitím jednoho pravidla výstrahy pro více časových řad
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 10/04/2020
ms.subservice: alerts
ms.openlocfilehash: 81e09e6d9c6a57339f1d6f1eb5ce4f494555fa19
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91704476"
---
# <a name="monitor-multiple-time-series-in-a-single-metric-alert-rule"></a>Monitorování více časových řad v jednom pravidle výstrahy metriky

Jedno pravidlo upozorňující na metriku se dá použít k monitorování jedné nebo mnoha metrik časových řad, což usnadňuje monitorování prostředků ve velkém měřítku.

## <a name="metric-time-series"></a>Metrika časové řady

Metrika časové řady je série měření (neboli "hodnoty metrik") zaznamenaných v časovém intervalu. 

Například:

- Využití procesoru virtuálního počítače
- Příchozí bajty (příchozí) na účet úložiště
- Počet neúspěšných žádostí webové aplikace



## <a name="alert-rule-on-a-single-time-series"></a>Pravidlo výstrahy pro jednu časovou řadu
Pravidlo výstrahy sleduje jednu časovou řadu, pokud splňuje všechny následující podmínky:
-   Pravidlo monitoruje jeden cílový prostředek. 
-   Obsahuje jednu podmínku
-   Vyhodnocuje metriku bez výběru dimenzí (za předpokladu, že metrika podporuje dimenze).

Příklad takového pravidla výstrahy (se zobrazenými pouze souvisejícími vlastnostmi):
-   Cílový prostředek: *myVM1*
-   Metrika: *procento využití procesoru*
-   Operátor: je *větší než*
-   Prahová hodnota: *70*


Pro toto pravidlo upozornění se sleduje jedna časová řada metrik:
-   Procentuální hodnota CPU, kde *Resource*= ' myVM1 ' > 70%

![Pravidlo výstrahy pro jednu časovou řadu](media/alerts-metric-multiple-time-series-single-rule/simple-alert-rule.png)

## <a name="alert-rule-on-multiple-time-series"></a>Pravidlo výstrahy na více časových řadách
Pravidlo výstrahy sleduje více časových řad, pokud používá alespoň jednu z následujících funkcí: 
-   Více prostředků
-   Několik podmínek 
-   Více dimenzí


## <a name="multiple-resources-multi-resource"></a>Více prostředků (více prostředků)

Jedno pravidlo upozornění na metriku může sledovat víc prostředků, pokud jsou prostředky stejného typu a existují ve stejné oblasti Azure. Použití tohoto typu pravidla snižuje složitost a celkový počet pravidel upozornění, která je třeba udržovat. 

Příklad takového pravidla výstrahy:
-   Cílový prostředek: *myVM1, myVM2*
-   Metrika: *procento využití procesoru*
-   Operátor: je *větší než*
-   Prahová hodnota: *70*

Pro toto pravidlo upozornění se dvě metriky monitorují samostatně:
-   Procentuální hodnota CPU, kde *Resource*= ' myVM1 ' > 70%
-   Procentuální hodnota CPU, kde *Resource*= ' myVM2 ' > 70%

![Pravidlo upozornění s více prostředky](media/alerts-metric-multiple-time-series-single-rule/multi-resource-alert-rule.png)
 
V pravidle výstrahy s více prostředky je podmínka vyhodnocena **zvlášť** pro každý z prostředků (nebo přesněji, u každé z časových řad metriky, které odpovídají každému prostředku). To znamená, že výstrahy se také aktivují pro jednotlivé prostředky zvlášť.

Předpokládejme například, že jsme nastavili výše uvedené pravidlo upozornění, které monitoruje procesor nad 70%. Ve vyhodnoceném časovém období (tj. za posledních 5 minut)
-   *Procento využití CPU* *myVM1* je větší než 70%. 
-   *Procentuální hodnota CPU* *myVM2* je v 50%.

Pravidlo výstrahy se spouští na *myVM1*, ale ne *myVM2*. Tyto aktivované výstrahy jsou nezávislé. Můžou se také vyřešit v různých časech v závislosti na jednotlivých způsobech jednotlivých virtuálních počítačů.

Další informace o pravidlech výstrah s více prostředky a typech prostředků podporovaných pro tuto funkci najdete v tématu [monitorování ve velkém měřítku s využitím výstrah metrik v Azure monitor](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

> [!NOTE] 
> V pravidle výstrahy metriky, které monitorují více prostředků, je povolena pouze jedna podmínka.

## <a name="multiple-conditions-multi-condition"></a>Více podmínek (vícehodnotový stav)

Jedno pravidlo výstrahy metriky může také monitorovat až pět podmínek na jedno pravidlo výstrahy. 

Například:

- Cílový prostředek: *myVM1*
- Condition1
  - Metrika: *procento využití procesoru*
  - Operátor: je *větší než*
  - Prahová hodnota: *70*
- Podmínka2
  - Metrika: *Celková síť*
  - Operátor: je *větší než*
  - Prahová hodnota: *20 MB*

Pro toto pravidlo upozornění se monitorují dvě časové řady metrik:

- Procentuální hodnota CPU, kde *Resource*= ' myVM1 ' > 70%
- Síť v celkovém umístění, kde *prostředek*= ' myVM1 ' > 20 MB

![Pravidlo upozornění s více podmíněnými podmínkami](media/alerts-metric-multiple-time-series-single-rule/multi-condition-alert-rule.png)
 
Operátor AND se používá mezi podmínkami. Pravidlo výstrahy vyvolá výstrahu, pokud jsou splněny **všechny** podmínky. Vyvolaná výstraha se přeloží, pokud není splněna alespoň jedna z podmínek. 

> [!NOTE]
> Existují omezení při použití dimenzí v pravidle výstrahy s několika podmínkami. Další informace najdete v tématu [omezení při použití dimenzí v pravidle výstrahy metrik s několika podmínkami](alerts-troubleshoot-metric.md#restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions).


## <a name="multiple-dimensions-multi-dimension"></a>Více dimenzí (více dimenzí)

Jedno pravidlo výstrahy metriky může také monitorovat více hodnot dimenzí metriky. Dimenze metriky jsou páry název-hodnota, které obsahují další data pro popis hodnoty metriky. Například metrika **transakcí** účtu úložiště má dimenzi s názvem **rozhraní API**, která popisuje název rozhraní API volaného jednotlivými transakcemi (například getblob, DeleteBlob, PutPage). Použití dimenzí je volitelné, ale umožňuje filtrovat metriku a jenom monitorovat konkrétní časovou řadu, místo monitorování metriky jako agregace všech hodnot dimenzí dohromady. 

Můžete například zvolit, že se má vygenerovat výstraha v případě, že je počet transakcí vysoký napříč všemi názvy rozhraní API (což jsou agregovaná data), nebo ho dále rozdělte na upozornění, když je počet transakcí vysoký pro konkrétní názvy rozhraní API. 

Příkladem pravidla výstrahy monitorování více dimenzí je:

- Cílový prostředek: *myStorage1*
- Metrika: *transakcí*
- Dimenze
  * Název rozhraní API = *getblob, DeleteBlob, PutPage*
- Operátor: je *větší než*
- Prahová hodnota: *70*

Pro toto pravidlo upozornění se sleduje tři časové řady metrik:

- Transakce, kde *Resource*= ' myStorage1 ' a ' *název rozhraní API*= ' getblob ' > 70
- Transakce, kde *Resource*= ' myStorage1 ' a *rozhraní API Name*= ' DeleteBlob ' > 70
- Transakce, kde *Resource*= ' myStorage1 ' a *rozhraní API Name*= ' PutPage ' > 70

![Pravidlo upozornění s více dimenzemi s hodnotami z jedné dimenze](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-1.png)

Pravidlo upozornění metriky s více dimenzemi může také monitorovat více hodnot dimenzí z **různých** dimenzí metriky. V tomto případě pravidlo výstrahy **samostatně** sleduje všechny kombinace hodnot dimenzí vybraných hodnot dimenzí.

Příklad tohoto typu pravidla výstrahy:

- Cílový prostředek: *myStorage1*
- Metrika: *transakcí*
- Dimenze
  * Název rozhraní API = *getblob, DeleteBlob, PutPage*
  * Ověřování = *SAS, AccountKey*
- Operátor: je *větší než*
- Prahová hodnota: *70*

Pro toto pravidlo upozornění se šestá řada metrik-Series monitoruje samostatně:

- Transakce, kde *Resource*= ' myStorage1 ' a *rozhraní API Name*= ' Getblob ' a *Authentication*= ' SAS ' > 70
- Transakce, kde *Resource*= ' myStorage1 ' a *rozhraní API Name*= ' Getblob ' a *Authentication*= ' AccountKey ' > 70
- Transakce, kde *Resource*= ' myStorage1 ' a *rozhraní API Name*= ' DeleteBlob ' a *Authentication*= ' SAS ' > 70
- Transakce, kde *Resource*= ' myStorage1 ' a *rozhraní API Name*= ' DeleteBlob ' a *Authentication*= ' AccountKey ' > 70
- Transakce, kde *Resource*= ' myStorage1 ' a *rozhraní API Name*= ' PutPage ' a *Authentication*= ' SAS ' > 70
- Transakce, kde *Resource*= ' myStorage1 ' a *rozhraní API Name*= ' PutPage ' a *Authentication*= ' AccountKey ' > 70

![Pravidlo upozornění s více dimenzemi s hodnotami z více dimenzí](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-2.png)
 
### <a name="advanced-multi-dimension-features"></a>Rozšířené funkce více dimenzí

1.  **Výběr všech aktuálních a budoucích dimenzí** – můžete se rozhodnout, že budete monitorovat všechny možné hodnoty dimenze, včetně budoucích hodnot. Toto pravidlo výstrahy se automaticky škáluje tak, aby sledovalo všechny hodnoty dimenze, aniž byste museli upravovat pravidlo upozornění pokaždé, když je hodnota dimenze přidána nebo odebrána.
2.  **Vyloučení dimenzí** – výběr operátoru ' ≠ ' (Exclude) pro hodnotu dimenze je ekvivalentem výběru všech ostatních hodnot této dimenze, včetně budoucích hodnot.
3.  **Nové a vlastní dimenze** – hodnoty dimenze zobrazené v Azure Portal jsou založené na datech metrik shromážděných za poslední tři dny. Pokud Hledaná hodnota dimenze ještě není vygenerována, můžete přidat vlastní hodnotu dimenze.

![Rozšířené funkce více dimenzí](media/alerts-metric-multiple-time-series-single-rule/advanced-features.png)


## <a name="metric-alerts-pricing"></a>Cenové výstrahy metriky

Ceny pravidel upozornění metrik jsou k dispozici na [stránce Azure monitor ceny](https://azure.microsoft.com/pricing/details/monitor/).

Při vytváření pravidla upozornění na metriky je zadaný odhad ceny založený na vybraných funkcích a počtu monitorovaných časových řad, které se určují z konfigurace pravidla a hodnot aktuální metriky. Měsíční poplatek je však založen na skutečných hodnoceních časové řady a může se proto lišit od původního odhadu, pokud některé časové řady neobsahují data k vyhodnocení, nebo pokud pravidlo výstrahy používá funkce, které mohou být dynamicky škálovatelny.

Pravidlo výstrahy může například ukazovat vysoký odhad ceny, pokud využívá funkci více dimenzí a je vybrán velký počet kombinací hodnot dimenzí, což vede k monitorování řady časových řad. Ale skutečné poplatky za toto pravidlo výstrahy můžou být nižší, pokud ne všechny řady časových řad, které vyplývají z kombinací hodnot dimenzí, mají ve skutečnosti data k vyhodnocení.

## <a name="number-of-time-series-monitored-by-a-single-alert-rule"></a>Počet časových řad monitorovaných jedním pravidlem výstrahy

Aby nedocházelo k nadměrným nákladům, každé pravidlo výstrahy může ve výchozím nastavení monitorovat až 5000 časových řad. Pokud chcete toto omezení z vašeho předplatného vynechat, otevřete lístek podpory.


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o monitorování ve škálování pomocí výstrah metrik a [dynamických prahových hodnot](alerts-dynamic-thresholds.md).
