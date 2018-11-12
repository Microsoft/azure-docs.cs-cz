---
title: Řešení potíží s upozorněními protokolu ve službě Azure Monitor
description: Běžné problémy a chyby a řešení pro protokolu upozornění pravidla v Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 5572c80879584e7f6df650263ae455a134ee4088
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283593"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Řešení potíží s upozorněními protokolu ve službě Azure Monitor  

## <a name="overview"></a>Přehled
Tento článek ukazuje, že zpracování běžných problémů viděli při nastavování upozornění protokolů do Azure monitoru. A poskytovat řešení na nejčastější dotazy týkající se konfigurace upozornění protokolů nebo funkce. Termín **upozornění protokolů** k popisu výstrahy, pokud je signál vlastní dotaz na základě [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) nebo [Application Insights](../application-insights/app-insights-analytics.md). Další informace o funkci, terminologie a typy z [upozornění - Přehled protokolů](monitor-alerts-unified-log.md).

> [!NOTE]
> Tento článek nebere v úvahu případy při pravidlo upozornění se zobrazí jako aktivuje webu Azure portal a oznámení prostřednictvím přidružené skupiny akcí. Pro tyto případy, najdete informace v článku na [skupiny akcí](monitoring-action-groups.md).


## <a name="log-alert-didnt-fire"></a>Neměli aktivovat upozornění protokolu

Další podrobné jsou některé běžné důvody, proč nakonfigurovaného [pravidel upozornění protokolů ve službě Azure Monitor](alert-log.md) nebude aktivují v [Azure Alerts](monitoring-alerts-managing-alert-states.md), pokud očekáváte, že se nebudou vydány. 

### <a name="data-ingestion-time-for-logs"></a>Doba příjem dat protokolů
Pravidelně spuštěním dotazu zajišťované zákazníkem, na základě protokolu upozornění funguje [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) nebo [Application Insights](../application-insights/app-insights-analytics.md). Obě využívají sílu Analytics, která zpracovává obrovské množství dat protokolu a poskytuje funkce na stejné. Jak služba Log Analytics zahrnuje zpracování po mnoho terabajtů dat z tisíce zákazníků a z různých zdrojů po celém světě – služba je náchylný k časovou prodlevu. Další informace najdete v tématu [doba příjem dat v Log Analytics](../log-analytics/log-analytics-data-ingestion-time.md).

Abyste vyřešili zpoždění příjmu dat, ke kterému může dojít v Log Analytics nebo protokoly Application Insights. upozornění protokolu vyčká a pokusí se znovu po určité době, když zjistí, že není dosud objem přijatých dat za toto časové období výstrah. Upozornění protokolů má skupinu exponenciálně rostoucím čas čekání tak, aby se ujistěte se, že jsme čekací doba potřebná pro dat přijatý službou Log Analytics. Proto pokud protokoly dotazovat vašich pravidel upozornění protokolů jsou ovlivněny ingestování zpoždění, pak upozornění protokolu se aktivuje pouze po data jsou k dispozici v Log Analytics po ingestování a po exponenciální časové prodlevy kvůli protokolu služba upozornění opakováním více než jednou v prozatím .

### <a name="incorrect-time-period-configured"></a>Nakonfigurované správné časové období
Jak je popsáno v článku na [terminologie pro výstrahy protokolu](monitor-alerts-unified-log.md#log-search-alert-rule---definition-and-types), určuje časové období, uvádí konfigurace časový rozsah dotazu. Dotaz vrátí pouze záznamy, které byly vytvořeny v tomto časovém rozsahu. Časové období omezuje data načtena dotaz protokolu, aby se zabránilo zneužití a připojení, vyřešíte nepřetržitou jakýkoli příkaz času (například před) používaných v dotazu protokolu. 
*Například toto časové období je nastavený na 60 minut a spuštění dotazu v 13:15, se vrátí pouze záznamy vytvořené mezi 12:15 PM a 1:15 PM do spuštění protokolu dotazu. Když teď dotaz protokolu používá čas příkazu, jako jsou před (1d), protokolu by spustí dotaz pouze pro data mezi 12:15 PM a 1:15 PM - jakoby dat existuje pouze posledních 60 minut. A ne po dobu sedmi dní dat, jak je uvedeno v protokolu dotazu.*

Založené na logiku dotazu, zkontrolujte, pokud má k dispozici odpovídající časové období v konfiguraci. Například bylo uvedeno dříve Pokud v protokolu dotazu používá před (1d), jak je znázorněno s zelené značky - pak časového období by měla být nastavena na 24 hodin nebo 1440 minut (jak je uvedeno červeně), aby zadaný dotaz provede správně předpokládaný.
    ![Časové období](./media/monitor-alerts-unified/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Potlačit výstrahy nastavit možnost
Jak je popsáno v kroku 8 tohoto článku na [vytváření pravidel upozornění protokolů na webu Azure portal](alert-log.md#managing-log-alerts-from-the-azure-portal), upozornění protokolu poskytuje možnost konfigurovat automatické potlačení pravidlo upozornění a jejich předcházení oznámení nebo triggeru stanoveného časového intervalu. Potlačit výstrahy možnost způsobí, že fungují, a přitom není aktivuje skupina akcí po dobu uvedenou v upozornění protokolu **potlačit výstrahy** možnost, a proto může uživatel můžete toto upozornění neměli aktivovat, když ve skutečnosti byla potlačena, protože nakonfigurovaná .
    ![Potlačit výstrahy](./media/monitor-alerts-unified/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Pravidlo upozornění metriky měření je nesprávný
Typ metriky měření pravidel upozornění protokolů není podtypem typu upozornění protokolů, které mají speciální funkce, ale pak využívá omezení syntaxe dotaz na upozornění. Upozornění metriky měření protokolu pravidlo je vyžadováno výstup dotaz na upozornění poskytnout metrik časové řadě – tabulky s dobami odlišné stejně velké času spolu s odpovídajícími hodnotami AggregatedValue vypočítat. Kromě toho můžete uživatelům zvolit, aby v další proměnné tabulky spolu s AggregatedValue jako počítače, uzel atd. lze je řadit pomocí která data v tabulce.

Předpokládejme například, že pravidel upozornění protokolů měření metriky byla nakonfigurovaná jako:
- Dotaz byl: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- časové období 6 hodin
- Prahová hodnota 50
- alert logic tří po sobě jdoucí porušení
- Agregace: při vybrána jako $table

Nyní vzhledem k tomu, že v příkazu jsme použili shrnutí... podle a k dispozici dvě proměnné: časové razítko & $table; upozornění služby zvolí $table "Agregační po" – v podstatě řazení výsledků tabulku podle pole: $table – jak je znázorněno níže a pak vyhledejte více AggregatedValue pro každou tabulku typu (například availabilityResults) zobrazíte, pokud došlo po sobě jdoucí porušení 3 nebo více.

   ![Metriky měření provádění dotazu s více hodnotami](./media/monitor-alerts-unified/LogMMQuery.png)

"Agregační po" je $table – data je seřazená podle sloupce $table (stejně jako v RED); Potom jsme skupině a hledat typy pole "Agregační po" (to znamená) $table – například: hodnoty pro availabilityResults bude považovat za jeden vykreslení na entitu (jako ve zvýrazněných oranžovou). V tuto zobrazovanou hodnotu/entitu – služba upozornění kontroluje tří po sobě jdoucí porušení, ke kterým dochází (jako je uvedené v zelené) pro výstrahy, které se aktivují pro tabulkovou hodnotu "availabilityResults". Podobně pokud pro jakoukoli jinou hodnotu $table Pokud jsou tři po sobě jdoucí porušení – další oznámení se budou aktivovat pro stejné. pomocí výstrah služby automaticky řazení hodnot v jedné vykreslení na entitu (jako oranžová) podle času.

Nyní předpokládejme, že metriky měření úpravy pravidel upozornění protokolů a byl dotaz `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` se zbytkem config zbývající stejná jako před zahrnutím logika upozornění pro tři po sobě jdoucí porušení. Možnost "Agregace při" v tomto případě bude ve výchozím nastavení: časové razítko. Protože zadat pouze jednu hodnotu v dotazu pro souhrn... (to znamená) časové razítko; podobný předchozímu příkladu na konci provádění výstup by měl jak je znázorněno níže. 

   ![Metriky měření provádění dotazu s hodnotou singulární](./media/monitor-alerts-unified/LogMMtimestamp.png)

"Agregační po" je časové razítko – data je seřazená podle sloupec časového razítka (jako v RED); Potom jsme Seskupit podle časového razítka – například: hodnoty `2018-10-17T06:00:00Z` se považuje za jednu vykreslení na entitu (jako ve zvýrazněných oranžovou). V tuto zobrazovanou hodnotu/entitu – bude služba upozornění najít žádné po sobě jdoucí porušení vyskytujících (protože každá hodnota časového razítka má pouze jedna položka) a proto výstrahy se nikdy se aktivují. Proto v takovém případě uživatel musí buď-
- Přidejte fiktivní proměnnou nebo existující proměnnou (například $table) správně řazení Hotovo pomocí "Aggregate po" pole nakonfigurovaná
- (Nebo) znovu nakonfigurovat pravidlo upozornění na použití logika upozornění na základě *celkový počet porušení* místo toho správně
 
## <a name="log-alert-fired-unnecessarily"></a>Zbytečně aktivováno upozornění protokolu
Další podrobné jsou některé běžné důvody, proč nakonfigurovaného [pravidel upozornění protokolů ve službě Azure Monitor](alert-log.md) můžou být vyvolány v [Azure Alerts](monitoring-alerts-managing-alert-states.md), když jste Neočekáváme, že se nebudou vydány.

### <a name="alert-triggered-by-partial-data"></a>Výstraha se aktivuje částečná data
Provozování Log Analytics a Application Insights Analytics podléhají zpoždění ingestování a zpracování. to v době při spuštění dotaz na upozornění protokolu zadaná - může být případ žádná data k dispozici nebo jenom některá data, které jsou k dispozici. Další informace najdete v tématu [doba příjem dat v Log Analytics](../log-analytics/log-analytics-data-ingestion-time.md).

V závislosti na konfiguraci pravidla upozornění, může být chybně spalování v případě, že žádná data nebo částečná data v protokolech v době spuštění výstrahy. V takových případech se doporučuje změnit dotaz na upozornění nebo konfigurace. *Například pokud pravidlo výstrah protokolu je nakonfigurován na aktivovat, když počet výsledků dotazu analytics je menší než (Řekněme) 5; potom když žádná data (žádný záznam) nebo částečné výsledky (jeden záznam) získat aktivovat pravidlo upozornění. Kde – po ingestování zpoždění, kdy stejný dotaz se spustí v Analytics dotaz s úplná data může poskytnout výsledek jako 10 záznamů.*

### <a name="alert-query-output-misunderstood"></a>Dotaz na upozornění výstupu nesprávně pochopeny
Pro upozornění protokolu logiku pro upozornění pochází od uživatele prostřednictvím analytického dotazu. Zadaný dotaz analytics můžete použít různé velké objemy dat a matematické funkce vytvořit konkrétní konstrukce. Výstrahy služby spustí dotaz zajišťované zákazníkem v intervalech zadán s daty za časové období zadaný; upozorňování service umožňuje drobným změní na dotaz zadaný – podle typu výstrahy zvolili a stejné může být dosvědčuje v části "Dotazu má být proveden" Konfigurovat signál logiku obrazovky, jak je znázorněno níže: ![provedení dotazu](./media/monitor-alerts-unified/LogAlertPreview.png)
 
Jak je zobrazeno v **dotaz, který se spustí** jaké protokolu je oddíl se spustí služba upozornění; uživatel může spustit stanovených dotazu, stejně jako timespan prostřednictvím [portál Analytics](../log-analytics/log-analytics-log-search-portals.md) nebo [rozhraní API pro analýzu](https://docs.microsoft.com/rest/api/loganalytics/) -Pokud chtějí porozumět, než vytvoření výstrahy, může být jaký výstup dotaz na upozornění.
 
## <a name="next-steps"></a>Další postup

* Další informace o [upozornění protokolů ve výstrahách Azure](monitor-alerts-unified-log.md)
* Další informace o [Application Insights](../application-insights/app-insights-analytics.md)
* Další informace o [Log Analytics](../log-analytics/log-analytics-queries.md). 