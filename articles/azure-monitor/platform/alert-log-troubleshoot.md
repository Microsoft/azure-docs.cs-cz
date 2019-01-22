---
title: Řešení potíží s upozorněními protokolu ve službě Azure Monitor
description: Běžné problémy a chyby a řešení pro protokolu upozornění pravidla v Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 56d76cd43b63a389569ae39c1e987a5fccbb9793
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54429442"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Řešení potíží s upozorněními protokolu ve službě Azure Monitor  

## <a name="overview"></a>Přehled

Tento článek ukazuje, jak řešit běžné problémy, kterým dochází při nastavování upozornění protokolů ve službě Azure monitor. Poskytuje také řešení, která často kladené dotazy týkající se konfigurace upozornění protokolů nebo funkce. 

Termín **upozornění protokolů** popisuje výstrahy, fire podle vlastního dotazu v [Log Analytics](../learn/tutorial-viewdata.md) nebo [Application Insights](../../azure-monitor/app/analytics.md). Další informace o funkci, terminologie a typy v [upozornění - Přehled protokolů](../platform/alerts-unified-log.md).

> [!NOTE]
> Tento článek nebere v úvahu případech, kdy se zobrazí na webu Azure portal a výstraha spuštěná pravidla a provádí přidružené skupiny akcí oznámení. Pro tyto případy, najdete informace v článku na [skupiny akcí](../platform/action-groups.md).


## <a name="log-alert-didnt-fire"></a>Neměli aktivovat upozornění protokolu

Tady je několik běžných příčin, proč nakonfigurovaného [pravidel upozornění protokolů ve službě Azure Monitor](../platform/alerts-log.md) nezobrazí stav [jako *aktivuje* očekával](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Doba příjem dat protokolů

Upozornění protokolu pravidelně spouští dotaz na základě [Log Analytics](../learn/tutorial-viewdata.md) nebo [Application Insights](../../azure-monitor/app/analytics.md). Protože Log Analytics zpracovává mnoho terabajtů dat z tisíce zákazníků z různých zdrojů po celém světě, tato služba je náchylný k různým časovou prodlevu. Další informace najdete v tématu [Čas pro příjem dat v Log Analytics](../platform/data-ingestion-time.md).

Ke zmírnění zpoždění příjmu dat, systému vyčká a pokusí znovu výstraha dotazu více než jednou pokud zjistí, že zatím není přijatých potřebná data. Systém má exponenciálně rostoucím čekací doba nastavena. Protokol výstrah pouze aktivační události po dat je k dispozici, takže jejich zpoždění může být způsobeno ingestování protokol pomalých operací. 

### <a name="incorrect-time-period-configured"></a>Nakonfigurované správné časové období

Jak je popsáno v článku na [terminologie pro výstrahy protokolu](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), čas období uvedená v konfiguraci Určuje časový rozsah dotazu. Dotaz vrátí pouze záznamy, které byly vytvořeny v tomto časovém rozsahu. Časové období omezuje data načtena dotaz protokolu, aby se zabránilo zneužití a připojení, vyřešíte nepřetržitou jakýkoli příkaz čas (jako je *před*) používaných v dotazu protokolu. Například toto časové období je nastavený na 60 minut a spuštění dotazu v 13:15, se používají pouze záznamy vytvořené mezi 12:15 PM a 1:15 PM pro dotaz protokolu. Pokud dotaz protokolu používá čas příkaz podobný *před (1d)*, protože toto časové období je nastavena na tento interval.* dotaz stále pouze používá data mezi 12:15 PM a 1:15 PM

Proto zkontrolujte v konfiguraci tohoto časového období odpovídá vašemu dotazu. Například bylo uvedeno dříve, pokud používá dotaz protokolu *před (1d)* jak je znázorněno s zelené značky, pak časové období musí být nastavená na 24 hodin nebo 1 440 minut (jak je uvedeno v Red), aby se dotaz provádí tak, jak má.

![Časové období](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Potlačit výstrahy nastavit možnost

Jak je popsáno v kroku 8 tohoto článku na [vytváření pravidel upozornění protokolů na webu Azure portal](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), poskytují upozornění protokolů **potlačit výstrahy** možnost potlačit akce aktivuje a oznámení pro nakonfigurované čas. V důsledku toho může myslíte, že nebyla ve skutečnosti nebyla, ale došlo k potlačení aktivuje výstrahu.  

![Potlačit upozornění](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Pravidlo upozornění metriky měření je nesprávný

**Upozornění protokolů měření metriky** jsou podtypem typu upozornění protokolů, které mají speciální funkce a syntaxi s omezeným přístupem dotaz na upozornění. Základě měření metriky pravidel upozornění protokolů vyžaduje dotazu výstup bude metrik časové řady; To znamená tabulku s distinct stejné velikosti časová období společně s odpovídající agregované hodnoty. Kromě toho se uživatelé mohou mít další proměnné v tabulce spolu s AggregatedValue. Tyto proměnné slouží k seřazení tabulky. 

Předpokládejme například, že pravidel upozornění protokolů měření metriky byla nakonfigurovaná jako:

- Dotaz byl: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- časové období 6 hodin
- Prahová hodnota 50
- alert logic tří po sobě jdoucí porušení
- Agregace: při vybrána jako $table

Vzhledem k tomu, že příkaz zahrnuje *... vytvořit souhrn podle* a k dispozici dvě proměnné (časové razítko & $table), systém zvolí $table k *agregované po*. Seřadí tabulku výsledků podle pole *$table* jak je znázorněno níže a poté hledá v několika AggregatedValue pro každý typ tabulky (například availabilityResults) Chcete-li zobrazit, pokud došlo po sobě jdoucí porušení 3 nebo více.

![Metriky měření provádění dotazu s více hodnotami](media/alert-log-troubleshoot/LogMMQuery.png)

Jako *agregované po* je definován na *$table*, data je seřazená podle sloupce $table (stejně jako v RED); potom jsme skupině a zkuste najít typy *agregované po* pole (to znamená) $table pro Příklad: hodnoty pro availabilityResults bude považovat za jeden vykreslení na entitu (jako ve zvýrazněných oranžovou). V tuto zobrazovanou hodnotu/entitu služba upozornění kontroluje tří po sobě jdoucí porušení, ke kterým dochází (jako je uvedené v zelené) pro výstrahy, které se aktivují pro tabulkovou hodnotu "availabilityResults". Podobně pokud se pro jakoukoli jinou hodnotu $table Pokud jsou tři po sobě jdoucí porušení - další oznámení se budou aktivovat pro stejnou věc; pomocí výstrah služby automaticky řazení hodnot v jedné vykreslení na entitu (jako oranžová) podle času.

Nyní předpokládejme, že metriky měření úpravy pravidel upozornění protokolů a byl dotaz `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` se zbytkem config zbývající stejná jako před zahrnutím logika upozornění pro tři po sobě jdoucí porušení. Možnost "Agregace při" v tomto případě bude ve výchozím nastavení: časové razítko. Protože zadat pouze jednu hodnotu v dotazu pro *... vytvořit souhrn podle* (to znamená) *časové razítko*; je podobný předchozímu příkladu na konci provádění výstup by měl, jak je znázorněno níže.

   ![Metriky měření provádění dotazu s hodnotou singulární](media/alert-log-troubleshoot/LogMMtimestamp.png)

Jako *agregované po* je definován na časové razítko, data seřazená podle *časové razítko* sloupec (stejně jako v RED); a potom jsme Seskupit podle časového razítka například: hodnoty `2018-10-17T06:00:00Z` se považuje za jednu vykreslení na entitu (například zvýrazněn oranžově). V tuto zobrazovanou hodnotu/entitu najdete služba upozornění, že žádné po sobě jdoucí porušení vyskytujících (protože každá hodnota časového razítka má pouze jedna položka) a proto výstrahy se nikdy se aktivují. Proto v takovém případě uživatel musí buď:

- Přidejte fiktivní proměnnou nebo existující proměnnou (například $table) správně řazení Hotovo pomocí "Aggregate po" pole nakonfigurovaná
- (Nebo) znovu nakonfigurovat pravidlo upozornění na použití logika upozornění na základě *celkový počet porušení* místo toho správně

## <a name="log-alert-fired-unnecessarily"></a>Zbytečně aktivováno upozornění protokolu

Další podrobné jsou některé běžné důvody, proč nakonfigurovaného [pravidel upozornění protokolů ve službě Azure Monitor](../platform/alerts-log.md) můžou být vyvolány v [Azure Alerts](../platform/alerts-managing-alert-states.md), když jste Neočekáváme, že se nebudou vydány.

### <a name="alert-triggered-by-partial-data"></a>Výstraha se aktivuje částečná data

Provozování Log Analytics a Application Insights Analytics podléhají zpoždění ingestování a zpracování. to v době při spuštění dotaz na upozornění protokolu zadaná - může být případ žádná data k dispozici nebo jenom některá data, které jsou k dispozici. Další informace najdete v tématu [Čas pro příjem dat v Log Analytics](../platform/data-ingestion-time.md).

V závislosti na konfiguraci pravidla upozornění, může mít chybné spalování neexistuje žádná data nebo částečná data v protokolech v době spuštění výstrahy. V takovém případě doporučujeme vám změnit dotaz na upozornění nebo konfigurace. 

Například, pokud je nakonfigurovaný pravidel upozornění protokolů aktivovat, když počet výsledků z dotazu analytics je menší než 5, pak upozornění aktivuje, když není žádná data (žádný záznam) nebo částečné výsledky (jeden záznam). Po prodlevě příjmu dat, ale stejný dotaz s úplnou může poskytnout výsledek 10 záznamů.

### <a name="alert-query-output-misunderstood"></a>Dotaz na upozornění výstupu nesprávně pochopeny

Poskytuje logiku pro výstrahy protokolu v dotazu analytics. Analytický dotaz může používat různé velké objemy dat a matematických funkcí.  Výstrahy služby provede dotaz na zadaných s daty pro zadané časové období. Výstrahy služby díky malých změn zadaný dotaz podle typu výstrahy zvolili. To lze zobrazit v části "Dotazu má být proveden" *konfigurovat logiku signálů* obrazovky, jak je znázorněno níže: ![Provedení dotazu](media/alert-log-troubleshoot/LogAlertPreview.png)

Jak ukazuje příklad **dotaz, který se spustí** pole je, cokoli běží služba upozornění protokolu. Můžete spustit stanovených dotazu, stejně jako timespan prostřednictvím [portál Analytics](../log-query/portals.md) nebo [rozhraní API pro analýzu](https://docs.microsoft.com/rest/api/loganalytics/) Pokud chcete pochopit, co výstraha dotazu výstup, může být před samotným vytvořením výstrahy.

## <a name="next-steps"></a>Další postup

- Další informace o [upozornění protokolů ve výstrahách Azure](../platform/alerts-unified-log.md)
- Další informace o [Application Insights](../../azure-monitor/app/analytics.md)
- Další informace o [Log Analytics](../../log-analytics/log-analytics-overview.md)
