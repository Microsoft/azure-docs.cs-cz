---
title: Zjistěte, jak fungují upozornění na metriky v Azure Monitoru.
description: Získejte přehled o tom, co můžete dělat s upozorněními na metriky a jak fungují ve Službě Azure Monitor.
ms.date: 03/17/2020
ms.topic: conceptual
ms.subservice: alerts
ms.openlocfilehash: b6d8bc69a407838025c5e78e0a1c773ab457c409
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480228"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Principy fungování upozornění na metriky ve službě Azure Monitor

Upozornění metrik y v Azure Monitoru fungují nad vícedimenzionální metriky. Tyto metriky mohou být [metriky platformy](alerts-metric-near-real-time.md#metrics-and-dimensions-supported), [vlastní metriky](../../azure-monitor/platform/metrics-custom-overview.md), [oblíbené protokoly z Azure Monitoru převedené na metriky](../../azure-monitor/platform/alerts-metric-logs.md) a metriky Application Insights. Upozornění metriky vyhodnocovat v pravidelných intervalech zkontrolovat, zda podmínky na jedné nebo více časových řad metriky jsou pravdivé a upozorní vás, když jsou splněny hodnocení. Upozornění metriky jsou stavové, to znamená, že pouze odesílat oznámení při změně stavu.

## <a name="how-do-metric-alerts-work"></a>Jak fungují upozornění na metriky?

Pravidlo upozornění metriky můžete definovat zadáním cílového prostředku, který má být monitorován, názvu metriky, typu podmínky (statické nebo dynamické) a podmínky (operátor a prahová hodnota/citlivost) a skupiny akcí, které se aktivují při spuštění pravidla výstrahy. Typy podmínek ovlivňují způsob určení prahových hodnot. [Další informace o typu podmínky dynamické prahové hodnoty a možnosti citlivosti](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Pravidlo výstrahy s typem statické podmínky

Řekněme, že jste vytvořili jednoduché pravidlo upozornění na metriku statické prahové hodnoty takto:

- Cílový prostředek (prostředek Azure, který chcete monitorovat): myVM
- Metrika: Procento procesoru
- Typ podmínky: Statický
- Agregace času (Statistika, která je spuštěna přes nezpracované hodnoty metriky. Podporované časové agregace jsou Min, Max, Průměr, Součet, Počet): Průměr
- Tečka (Okno zpětného pohledu, ve kterém jsou kontrolovány metrické hodnoty): Za posledních 5 minut
- Frekvence (Frekvence, s jakou metrika výstraha kontroluje, zda jsou splněny podmínky): 1 min
- Operátor: Větší než
- Práh: 70

Od okamžiku vytvoření pravidla výstrahy se monitor spouští každých 1 min a vyhledá hodnoty metrik za posledních 5 minut a zkontroluje, zda průměr těchto hodnot překročí 70. Pokud je splněna podmínka, která je, že průměrné procento procesoru za posledních 5 minut překročí 70, pravidlo výstrahy vyvolá aktivované oznámení. Pokud jste nakonfigurovali e-mail nebo akci webového háčku ve skupině akcí přidružené k pravidlu výstrahy, obdržíte aktivované oznámení o obou.

Pokud používáte více podmínek v jednom pravidle, pravidlo "ands" podmínky společně.  To znamená, že výstraha se aktivuje, když se všechny podmínky ve výstraze vyhodnotí jako pravdivé a vyřeší, když jedna z podmínek již není pravdivá. A příklad tohoto typu výstrahy by být upozorněni, když "PROCESOR vyšší než 90 %" a "délka fronty je více než 300 položek". 

### <a name="alert-rule-with-dynamic-condition-type"></a>Pravidlo výstrahy s dynamickým typem podmínky

Řekněme, že jste vytvořili jednoduché pravidlo upozornění metriky Dynamické prahové hodnoty takto:

- Cílový prostředek (prostředek Azure, který chcete monitorovat): myVM
- Metrika: Procento procesoru
- Typ podmínky: Dynamický
- Agregace času (Statistika, která je spuštěna přes nezpracované hodnoty metriky. Podporované časové agregace jsou Min, Max, Průměr, Součet, Počet): Průměr
- Tečka (Okno zpětného pohledu, ve kterém jsou kontrolovány metrické hodnoty): Za posledních 5 minut
- Frekvence (Frekvence, s jakou metrika výstraha kontroluje, zda jsou splněny podmínky): 1 min
- Operátor: Větší než
- Citlivost: Střední
- Ohlédnutí období: 4
- Počet porušení: 4

Po vytvoření pravidla výstrahy získá algoritmus strojového učení dynamických prahových hodnot historická data, která jsou k dispozici, vypočítá prahovou hodnotu, která nejlépe vyhovuje vzoru chování metrikových řad, a bude se průběžně učit na základě nových dat, aby prahovou hodnotu.

Od okamžiku vytvoření pravidla výstrahy se monitorování spouští každých 1 min a vyhledá hodnoty metrik y za posledních 20 minut seskupených do 5 minut a zkontroluje, zda průměr hodnot období v každém ze 4 období překračuje očekávanou prahovou hodnotu. Pokud je splněna podmínka, která je, že průměrné procento procesoru za posledních 20 minut (čtyři 5 minut období) odchýlil od očekávané chování čtyřikrát, pravidlo výstrahy vyvolá aktivované oznámení. Pokud jste nakonfigurovali e-mail nebo akci webového háčku ve skupině akcí přidružené k pravidlu výstrahy, obdržíte aktivované oznámení o obou.

### <a name="view-and-resolution-of-fired-alerts"></a>Zobrazení a řešení vymlážených výstrah

Výše uvedené příklady spouštění pravidel výstrah lze také zobrazit na portálu Azure v okně **Všechny výstrahy.**

Řekněme, že využití na "myVM" je i nadále nad prahovou hodnotou v následných kontrolách, pravidlo výstrahy se znovu nevznítí, dokud nebudou podmínky vyřešeny.

Po nějaké době se využití na "myVM" vrátí do normálu (jde pod prahovou hodnotu). Pravidlo výstrahy sleduje podmínku ještě dvakrát, aby bylo odesláno vyřešené oznámení. Pravidlo výstrahy odešle vyřešenou/deaktivovanou zprávu, pokud není splněna podmínka výstrahy po dobu tří po sobě jdoucích období, aby se snížil hluk v případě, že se v ymamápodmínky zmátnou.

Jako vyřešené oznámení se odesílá prostřednictvím webových háčků nebo e-mailu, stav instance výstrahy (tzv. stav monitorování) na webu Azure Portal je také nastavena na vyřešen.

### <a name="using-dimensions"></a>Použití kót

Upozornění metrik y ve službě Azure Monitor také podporují monitorování kombinací hodnot s více dimenzemi pomocí jednoho pravidla. Pochopme, proč můžete pomocí příkladu použít více kombinací dimenzí.

Řekněme, že máte plán služby App Service pro váš web. Chcete sledovat využití procesoru ve více instancích, které spouštějí váš web nebo aplikaci. Můžete to udělat pomocí pravidla upozornění metriky takto:

- Cílový zdroj: myAppServicePlan
- Metrika: Procento procesoru
- Typ podmínky: Statický
- Dimenze
  - Instance = InstanceName1, InstanceName2
- Agregace času: Průměr
- Období: Za posledních 5 minut
- Frekvence: 1 min
- Operátor: VětšíNež
- Práh: 70

Stejně jako dříve, toto pravidlo sleduje, pokud průměrné využití procesoru za posledních 5 minut překročí 70 %. Se stejným pravidlem však můžete sledovat dvě instance, které spouštějí váš web. Každá instance bude monitorována individuálně a budete dostávat oznámení jednotlivě.

Řekněme, že máte webovou aplikaci, která je vidět masivní poptávku a budete muset přidat další instance. Výše uvedené pravidlo stále monitoruje pouze dvě instance. Pravidlo však můžete vytvořit následujícím způsobem:

- Cílový zdroj: myAppServicePlan
- Metrika: Procento procesoru
- Typ podmínky: Statický
- Dimenze
  - Instance = *
- Agregace času: Průměr
- Období: Za posledních 5 minut
- Frekvence: 1 min
- Operátor: VětšíNež
- Práh: 70

Toto pravidlo bude automaticky sledovat všechny hodnoty instance, tj. můžete sledovat své instance, jak se objeví, aniž byste museli znovu upravovat pravidlo upozornění metriky.

Při sledování více dimenzí pravidlo upozornění dynamických prahových hodnot může vytvořit přizpůsobené prahové hodnoty pro stovky metrických řad najednou. Dynamické prahové hodnoty má za následek méně pravidel výstrah pro správu a významné úspory času při správě a vytváření pravidel výstrah.

Řekněme, že máte webovou aplikaci s mnoha instancemi a nevíte, co je nejvhodnější prahová hodnota. Výše uvedená pravidla budou vždy používat prahovou hodnotu 70%. Pravidlo však můžete vytvořit následujícím způsobem:

- Cílový zdroj: myAppServicePlan
- Metrika: Procento procesoru
- Typ podmínky: Dynamický
- Dimenze
  - Instance = *
- Agregace času: Průměr
- Období: Za posledních 5 minut
- Frekvence: 1 min
- Operátor: VětšíNež
- Citlivost: Střední
- Ohlédnutí období: 1
- Počet porušení: 1

Toto pravidlo monitoruje, pokud průměrné využití procesoru za posledních 5 minut překročí očekávané chování pro každou instanci. Stejné pravidlo můžete sledovat instance, jak se objeví, aniž byste museli znovu upravovat pravidlo upozornění metriky. Každá instance získá prahovou hodnotu, která odpovídá vzoru chování metriky řady a bude se průběžně měnit na základě nových dat, aby byla prahová hodnota přesnější. Stejně jako dříve, každá instance bude sledována individuálně a budete dostávat oznámení individuálně.

Rostoucí doba zpětného zhlédnutí a počet porušení mohou také umožnit filtrování výstrah pouze upozornění na definici významné odchylky. [Přečtěte si další informace o rozšířených možnostech dynamických prahových hodnot](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Monitorování ve velkém měřítku pomocí upozornění na metriky v Azure Monitoru

Zatím jste viděli, jak by se jedna metrika výstraha mohla použít ke sledování jedné nebo mnoha časových řad metrik souvisejících s jedním prostředkem Azure. Mnohokrát můžete chtít stejné pravidlo výstrahy použít pro mnoho prostředků. Azure Monitor také podporuje monitorování více prostředků (stejného typu) s jedním pravidlem upozornění metriky pro prostředky, které existují ve stejné oblasti Azure. 

Tato funkce je v současné době podporovaná pro metriky platformy (ne vlastní metriky) pro následující služby v následujících cloudech Azure:

| Služba | Veřejný Azure | Státní správa | Čína |
|:--------|:--------|:--------|:--------|
| Virtual Machines  | **Ano** | Ne | Ne |
| Databáze sql serverů | **Ano** | **Ano** | Ne |
| Elastické fondy serveru SQL | **Ano** | **Ano** | Ne |
| Hraniční zařízení datové schránky | **Ano** | **Ano** | Ne |

Rozsah monitorování můžete zadat jedním ze tří způsobů. Například u virtuálních počítačů můžete zadat obor jako:  

- seznam virtuálních počítačů v jedné oblasti Azure v rámci předplatného
- všechny virtuální počítače (v jedné oblasti Azure) v jedné nebo více skupinách prostředků v předplatném
- všechny virtuální počítače (v jedné oblasti Azure) v jednom předplatném

Vytváření pravidel upozornění metriky, která monitorují více prostředků, je jako [vytváření jakékoli jiné metriky,](alerts-metric.md) která monitoruje jeden prostředek. Jediným rozdílem je, že byste vybrali všechny prostředky, které chcete sledovat. Tato pravidla můžete také vytvořit prostřednictvím [šablon Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-a-metric-alert-that-monitors-multiple-resources). Obdržíte jednotlivá oznámení pro každý sledovaný prostředek.

## <a name="typical-latency"></a>Typická latence

U upozornění na metriky se obvykle zobrazí upozornění za méně než 5 minut, pokud nastavíte frekvenci pravidel výstrahy na 1 min. V případě vysoké zatížení pro oznamovací systémy se může zobrazit delší latence.

## <a name="supported-resource-types-for-metric-alerts"></a>Podporované typy prostředků pro upozornění na metriky

Úplný seznam podporovaných typů prostředků naleznete v tomto [článku](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).


## <a name="next-steps"></a>Další kroky

- [Přečtěte si, jak vytvářet, zobrazovat a spravovat upozornění na metriky v Azure](alerts-metric.md)
- [Zjistěte, jak nasadit upozornění na metriky pomocí šablon Azure Resource Manageru](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [Další informace o skupinách akcí](action-groups.md)
- [Další informace o typu podmínky Dynamické prahové hodnoty](alerts-dynamic-thresholds.md)
