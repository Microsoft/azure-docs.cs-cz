---
title: Pochopte, jak budou výstrahy metriky fungovat v Azure Monitor.
description: Získejte přehled o tom, co můžete dělat s výstrahami metrik a jak fungují v Azure Monitor.
ms.date: 09/30/2020
ms.topic: conceptual
ms.subservice: alerts
ms.openlocfilehash: 78ec5ff3fc87ef29d25e439b7d4c69bb3a10f3a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578066"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Principy fungování upozornění na metriky ve službě Azure Monitor

Výstrahy metrik v Azure Monitor pracují na multidimenzionálních metrikách. Tyto metriky můžou být [metrikami platforem](alerts-metric-near-real-time.md#metrics-and-dimensions-supported), [vlastní metriky](./metrics-custom-overview.md), [Oblíbené protokoly z Azure monitor převedené na metriky](./alerts-metric-logs.md) a Application Insights metriky. Výstrahy metriky jsou vyhodnocovány v pravidelných intervalech, aby bylo možné zjistit, zda jsou splněné podmínky na jedné nebo více metrik časových řad, a upozorňovat na to, až budou vyhodnocení splněna. Upozornění na metriku jsou stavová, to znamená, že odesílají oznámení pouze při změně stavu.

## <a name="how-do-metric-alerts-work"></a>Jak fungují výstrahy metrik?

Můžete definovat pravidlo výstrahy metriky zadáním cílového prostředku, který se má monitorovat, názvu metriky, typu podmínky (statické nebo dynamické) a podmínky (operátor a prahová hodnota/citlivost) a skupiny akcí, která se aktivuje při aktivaci pravidla výstrahy. Typy podmínek ovlivňují způsob určení prahových hodnot. [Přečtěte si další informace o typu dynamické prahové hodnoty a možnosti citlivosti](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Pravidlo upozornění s typem statické podmínky

Řekněme, že jste vytvořili jednoduché pravidlo výstrahy metriky se statickou prahovou hodnotou následujícím způsobem:

- Cílový prostředek (prostředek Azure, který chcete monitorovat): myVM
- Metrika: procento využití procesoru
- Typ podmínky: static
- Časová agregace (statistika, která se spouští přes nezpracované hodnoty metrik. [Podporovaná časová agregace](metrics-charts.md#changing-aggregation) jsou min, Max, AVG, Total, Count): Average
- Období (pohled na pozadí, na kterém jsou zaškrtnuté hodnoty metrik): za posledních 5 minut
- Frekvence (frekvence, s jakou výstraha metrika kontroluje, jestli jsou podmínky splněné): 1 min
- Operátor: je větší než
- Prahová hodnota: 70

Od okamžiku vytvoření pravidla výstrahy se monitor spouští každých 1 min a hledá metriky za posledních 5 minut a zkontroluje, jestli průměr těchto hodnot překračuje 70. Pokud je splněna podmínka, průměrná procentuální hodnota procesoru za posledních 5 minut překračuje 70, pravidlo výstrahy aktivuje aktivované oznámení. Pokud jste nakonfigurovali akci e-mail nebo webový zavěšení ve skupině akcí přidružené k pravidlu výstrahy, obdržíte i aktivované oznámení.

Pokud v jednom pravidle používáte více podmínek, pravidlo "and" tyto podmínky pohromadě. To znamená, že výstraha se aktivuje, když se všechny podmínky v pravidle výstrahy vyhodnotí jako true, a vyřešte, pokud jedna z podmínek už není pravdivá. Příkladem tohoto typu pravidla výstrahy je monitorování virtuálního počítače Azure a upozornění, když je "procentuální procesor větší než 90%" a "délka fronty je více než 300 položek".

### <a name="alert-rule-with-dynamic-condition-type"></a>Pravidlo upozornění s dynamickým typem podmínky

Řekněme, že jste vytvořili jednoduché pravidlo upozornění metriky dynamického prahového hodnoty následujícím způsobem:

- Cílový prostředek (prostředek Azure, který chcete monitorovat): myVM
- Metrika: procento využití procesoru
- Typ podmínky: dynamické
- Časová agregace (statistika, která se spouští přes nezpracované hodnoty metrik. [Podporovaná časová agregace](metrics-charts.md#changing-aggregation) jsou min, Max, AVG, Total, Count): Average
- Období (pohled na pozadí, na kterém jsou zaškrtnuté hodnoty metrik): za posledních 5 minut
- Frekvence (frekvence, s jakou výstraha metrika kontroluje, jestli jsou podmínky splněné): 1 min
- Operátor: je větší než
- Citlivost: střední
- Doba hledání: 4
- Počet porušení: 4

Po vytvoření pravidla výstrahy budou dynamické prahové hodnoty algoritmu strojového učení získávat historická data, která jsou k dispozici, vypočítat prahovou hodnotu, která nejlépe odpovídá vzoru chování metriky, a bude se průběžně učit na základě nových dat, aby byla prahová hodnota přesnější.

Od okamžiku vytvoření pravidla výstrahy se monitor spouští každých 1 min a prohlíží hodnoty metriky za posledních 20 minut seskupené do 5 minut a kontroluje, jestli průměr hodnot období v každé 4 obdobích překračuje očekávanou prahovou hodnotu. Pokud je splněna podmínka, průměrná procentuální hodnota procesoru za posledních 20 minut (4 5 minut) se odchyluje od očekávaného chování čtyřikrát, pravidlo výstrahy vyvolá aktivované oznámení. Pokud jste nakonfigurovali akci e-mail nebo webový zavěšení ve skupině akcí přidružené k pravidlu výstrahy, obdržíte i aktivované oznámení.

### <a name="view-and-resolution-of-fired-alerts"></a>Zobrazit a vyřešit aktivované výstrahy

Výše uvedené příklady pravidel výstrah lze také zobrazit v Azure Portal v okně **všechna upozornění** .

Řekněme, že použití příkazu "myVM" pokračuje nad prahovou hodnotou v následných kontrolách. pravidlo výstrahy nebude znovu spuštěno, dokud nebudou podmínky vyřešeny.

Po určité době se využití "myVM" vrátí zpět na normální (pod prahovou hodnotou). Pravidlo výstrahy sleduje podmínku ještě dvakrát, aby bylo možné odeslat vyřešené oznámení. Pravidlo výstrahy pošle přeloženou nebo deaktivovanou zprávu, pokud se podmínka výstrahy nesplní po dobu tří po sobě jdoucích období, aby se snížila hlučnost v případě přepíná podmínek.

Jak se vyřešené oznámení odesílá prostřednictvím webových hooků nebo e-mailu, je stav instance výstrahy (nazývaný stav monitorování) v Azure Portal také nastaven na hodnotu Vyřešeno.

### <a name="using-dimensions"></a>Použití dimenzí

Výstrahy metrik v Azure Monitor také podporují monitorování více kombinací hodnot dimenzí s jedním pravidlem. Pojďme pochopit, proč byste mohli použít více kombinací dimenzí s příkladem.

Řekněme, že máte App Service plán pro váš web. Chcete monitorovat využití procesoru u více instancí, na kterých běží web nebo aplikace. Pomocí pravidla výstrahy metriky můžete postupovat takto:

- Cílový prostředek: myAppServicePlan
- Metrika: procento využití procesoru
- Typ podmínky: static
- Dimenze
  - Instance = InstanceName1, InstanceName2
- Časová agregace: průměr
- Období: za posledních 5 minut
- Frekvence: 1 min
- Operátor: GreaterThan
- Prahová hodnota: 70

Podobně jako předtím toto pravidlo monitoruje, pokud průměrné využití CPU za posledních 5 minut překračuje 70%. U stejného pravidla ale můžete monitorovat dvě instance běžící na vašem webu. Každá instance se monitoruje jednotlivě a oznámení se vám budou zobrazovat jednotlivě.

Řekněme, že máte webovou aplikaci, která zobrazuje obrovský požadavek, a budete muset přidat další instance. Výše uvedené pravidlo stále sleduje pouze dvě instance. Pravidlo ale můžete vytvořit následujícím způsobem:

- Cílový prostředek: myAppServicePlan
- Metrika: procento využití procesoru
- Typ podmínky: static
- Dimenze
  - Instance = *
- Časová agregace: průměr
- Období: za posledních 5 minut
- Frekvence: 1 min
- Operátor: GreaterThan
- Prahová hodnota: 70

Toto pravidlo automaticky monitoruje všechny hodnoty pro instanci, tj. své instance můžete monitorovat tak, jak se dostanou, aniž byste museli znovu upravovat pravidlo upozornění na metriky.

Při monitorování více dimenzí může pravidlo pro dynamické prahové hodnoty vytvořit přizpůsobené prahové hodnoty pro stovky řady metrik v čase. Dynamické prahové hodnoty mají za následek méně pravidel výstrah pro správu a významnou dobu ukládání při správě a vytváření pravidel výstrah.

Řekněme, že máte webovou aplikaci s velkým počtem instancí a nevíte, jakou je nejvhodnější prahová hodnota. Výše uvedená pravidla budou vždycky používat prahovou hodnotu 70%. Pravidlo ale můžete vytvořit následujícím způsobem:

- Cílový prostředek: myAppServicePlan
- Metrika: procento využití procesoru
- Typ podmínky: dynamické
- Dimenze
  - Instance = *
- Časová agregace: průměr
- Období: za posledních 5 minut
- Frekvence: 1 min
- Operátor: GreaterThan
- Citlivost: střední
- Hledat zpětná období: 1
- Počet porušení: 1

Toto pravidlo monitoruje, zda průměrné využití procesoru za posledních 5 minut překračuje očekávané chování pro každou instanci. Stejné pravidlo můžete sledovat instance, protože se zobrazí, aniž byste museli znovu upravovat pravidlo upozornění na metriky. Každá instance získá prahovou hodnotu, která odpovídá vzoru chování metrik řady, a bude se průběžně měnit na základě nových dat, aby byla prahová hodnota přesnější. Stejně jako dřív se jednotlivé instance monitorují jednotlivě a oznámení se vám budou zobrazovat jednotlivě.

Zvýšení doby zpětného vyhledávání a počtu porušení může také umožňovat filtrování výstrah jenom na základě definice významné odchylky. [Přečtěte si další informace o rozšířených možnostech pro dynamické prahové hodnoty](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

> [!NOTE]
>
> Doporučujeme vybrat *členitost agregace (period)* , která je větší než *frekvence vyhodnocení*, aby se snížila pravděpodobnost chybějícího prvního vyhodnocení přidané časové řady v následujících případech:
> - Pravidlo upozornění metriky, které monitoruje více dimenzí – když se přidá nová kombinace hodnot dimenze
> - Pravidlo upozornění metriky, které monitoruje více prostředků – při přidání nového prostředku do oboru
> - Pravidlo upozornění na metriku, které monitoruje metriku, která se nevysílá průběžně (zhuštěná metrika) – když se metrika vygeneruje po dobu delší než 24 hodin, kdy se neemitoval



## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Monitorování ve velkém měřítku pomocí výstrah metrik v Azure Monitor

Zatím jste viděli, jak se dá jedna výstraha metriky použít k monitorování jedné nebo mnoha metrik časových řad, které souvisí s jedním prostředkem Azure. V mnoha případech možná budete chtít, aby se stejné pravidlo pro upozornění uplatnilo na mnoho prostředků. Azure Monitor také podporuje monitorování více prostředků (stejného typu) s jedním pravidlem upozornění na metriky pro prostředky, které existují ve stejné oblasti Azure. 

Tato funkce se v současné době podporuje pro metriky platforem (ne pro vlastní metriky) pro následující služby v následujících cloudech Azure:

| Služba | Veřejné Azure | Státní správa | Čína |
|:--------|:--------|:--------|:--------|
| Virtuální počítače<sup>1</sup>  | **Ano** | No | No |
| Databáze systému SQL Server | **Ano** | **Ano** | **Ano** |
| Elastické fondy SQL serveru | **Ano** | **Ano** | **Ano** |
| Fondy kapacity souborů NetApp | **Ano** | **Ano** | **Ano** |
| Svazky NetApp souborů | **Ano** | **Ano** | **Ano** |
| Trezory klíčů | **Ano** | **Ano** | **Ano** |
| Azure Cache for Redis | **Ano** | **Ano** | **Ano** |
| Hraniční zařízení datového boxu | **Ano** | **Ano** | **Ano** |

<sup>1</sup> není podporováno pro metriky sítě virtuálních počítačů (celkem v síti, celkový počet příchozích toků, příchozí toky, odchozí toky, maximální rychlost vytváření toků, maximální rychlost vytváření odchozích toků).

Můžete určit rozsah monitorování jedním pravidlem výstrahy metriky jedním ze tří způsobů. Například u virtuálních počítačů můžete obor zadat jako:  

- Seznam virtuálních počítačů (v jedné oblasti Azure) v rámci předplatného
- všechny virtuální počítače (v jedné oblasti Azure) v jedné nebo více skupinách prostředků v rámci předplatného
- všechny virtuální počítače (v jedné oblasti Azure) v předplatném

> [!NOTE]
>
> Obor pravidla upozornění na více prostředků musí obsahovat alespoň jeden prostředek vybraného typu prostředku.

Vytváření pravidel upozornění na metriky, které monitorují více prostředků, je jako [Vytvoření jakékoli jiné výstrahy metriky](alerts-metric.md) , která monitoruje jeden prostředek. Jediným rozdílem je, že byste vybrali všechny prostředky, které chcete monitorovat. Tato pravidla můžete také vytvořit prostřednictvím [šablon Azure Resource Manager](./alerts-metric-create-templates.md#template-for-a-metric-alert-that-monitors-multiple-resources). U každého monitorovaného prostředku budete dostávat jednotlivá oznámení.

> [!NOTE]
>
> V pravidle výstrahy metriky, které monitorují více prostředků, je povolena pouze jedna podmínka.

## <a name="typical-latency"></a>Typická latence

V případě výstrah metriky se obvykle během 5 minut zobrazí upozornění, pokud nastavíte četnost pravidel upozornění na 1 min. V případě velkého zatížení systémů oznámení se může zobrazit delší latence.

## <a name="supported-resource-types-for-metric-alerts"></a>Podporované typy prostředků pro výstrahy metriky

Úplný seznam podporovaných typů prostředků najdete v tomto [článku](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported).


## <a name="next-steps"></a>Další kroky

- [Naučte se vytvářet, zobrazovat a spravovat upozornění na metriky v Azure.](alerts-metric.md)
- [Naučte se nasazovat výstrahy metrik pomocí šablon Azure Resource Manager.](./alerts-metric-create-templates.md)
- [Další informace o skupinách akcí](action-groups.md)
- [Další informace o typu podmínky dynamického prahového hodnoty](alerts-dynamic-thresholds.md)

