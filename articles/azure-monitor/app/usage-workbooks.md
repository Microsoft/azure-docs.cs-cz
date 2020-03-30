---
title: Vytváření interaktivních sestav pomocí sešitů Azure Monitoru | Dokumenty Microsoftu
description: Zjednodušení složitých sestav pomocí předem sestavených a vlastních parametrizovaných sešitů
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 15543f7f761c707e8eff8e0cc0a0e4532475ddf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670997"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Vytváření interaktivních sestav pomocí sešitů Azure Monitoru

Sešity kombinují text, [dotazy Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics), metriky Azure a parametry do bohatých interaktivních sestav. Sešity jsou upravitelné všemi ostatními členy týmu, kteří mají přístup ke stejným prostředkům Azure.

Sešity jsou užitečné pro scénáře, jako jsou:

* Zkoumání využití aplikace, když neznáte metriky zájmu předem: počet uživatelů, retenční sazby, míry konverze, atd. Na rozdíl od jiných nástrojů pro analýzu využití umožňují sešity kombinovat různé druhy vizualizací a analýz, což je činí skvělými pro tento druh průzkumu volného tvaru.
* Vysvětlete svému týmu, jak si nově vydaná funkce vede, a zobrazíte tak počty uživatelů pro klíčové interakce a další metriky.
* Sdílení výsledků experimentu A/B ve vaší aplikaci s ostatními členy vašeho týmu. Můžete vysvětlit cíle experimentu s textem a pak zobrazit každou metriku využití a dotaz Analytics použitý k vyhodnocení experimentu spolu s jasnými popisky pro to, zda byla každá metrika nad nebo pod cílem.
* Nahlášení dopadu výpadku na používání aplikace, kombinování dat, textové vysvětlení a diskuse o dalších krocích, jak zabránit výpadkům v budoucnu.

## <a name="starting-with-a-template-or-saved-workbook"></a>Začínáme s šablonou nebo uloženým sešitem

Sešit se skládá z oddílů skládajících se z nezávisle upravitelných grafů, tabulek, textu a vstupních ovládacích prvků. Chcete-li lépe porozumět sešitům, je nejlepší je otevřít. 

V yberte **Sešity** z nabídky na levé straně z prostředí Application Insights pro vaši aplikaci.

![Snímek obrazovky s navigací do sešitů](./media/usage-workbooks/001-workbooks.png)

Tím se spustí galerie sešitů s řadou předem sestavených sešitů, které vám pomůžou začít.

![Snímek obrazovky s galerií sešitů](./media/usage-workbooks/002-workbook-gallery.png)

Začneme s výchozí **šablonou**, která se nachází pod nadpisem **Rychlý start**.

![Snímek obrazovky s galerií sešitů](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Úpravy, změna uspořádání, klonování a odstranění oddílů sešitu

Sešity mají dva režimy: **režim úprav**a **režim čtení**. Při prvním spuštění výchozího sešitu se otevře v **režimu úprav**. Zobrazí veškerý obsah sešitu, včetně všech kroků a parametrů, které jsou jinak skryté. **Režim čtení** představuje zjednodušené zobrazení stylu sestavy. To vám umožní abstraktní pryč složitost, která šla do vytváření sestavy, zatímco stále mají základní mechaniky jen pár kliknutí v případě potřeby pro úpravy.

![Ovládací prvky pro úpravy oddílu Přehledy aplikací](./media/usage-workbooks/editing-controls-new.png)

1. Až budete úpravy oddílu hotová, klikněte v levém dolním rohu oddílu na **Hotovo.**

2. Chcete-li vytvořit duplikát oddílu, klepněte na ikonu **Klonovat tento oddíl.** Vytváření duplicitních oddílů je skvělý způsob, jak iterate na dotaz bez ztráty předchozíiterace.

3. Pokud chcete oddíl v sešitu posunout nahoru, klikněte na ikonu **Přesunout nahoru** nebo **Přesunout dolů.**

4. Chcete-li oddíl trvale odebrat, klepněte na ikonu **Odebrat.**

## <a name="adding-text-and-markdown-sections"></a>Přidávání textových oddílů a oddílů Markdown

Přidání nadpisů, vysvětlení a komentářů do sešitů pomáhá přeměnit sadu tabulek a grafů na vyprávění. Textové oddíly v sešitech podporují [syntaxi Markdownu](https://daringfireball.net/projects/markdown/) pro formátování textu, jako jsou nadpisy, tučné písmo, kurzíva a seznamy s odrážkami.

Pokud chcete do sešitu přidat textový oddíl, použijte tlačítko **Přidat text** v dolní části sešitu nebo v dolní části libovolného oddílu.

## <a name="adding-query-sections"></a>Přidávání oddílů dotazů

![Oddíl Dotaz v sešitech](./media/usage-workbooks/analytics-section-new.png)

Pokud chcete do sešitu přidat oddíl dotazu, použijte tlačítko **Přidat dotaz** v dolní části sešitu nebo v dolní části libovolného oddílu.

Části dotazu jsou vysoce flexibilní a lze je použít k zodpovězení otázek, jako jsou:

* Kolik výjimek váš web vyvolal ve stejném časovém období jako pokles využití?
* Jaká byla distribuce časů načítání stránky pro uživatele, kteří si prohlíželi nějakou stránku?
* Kolik uživatelů si zobrazilo některé stránky na vašem webu, ale ne nějakou jinou sadu stránek? To může být užitečné pochopit, pokud máte clustery uživatelů, kteří používají různé podmnožiny funkce vašeho webu (použijte `join` operátor s `kind=leftanti` modifikátorem v [dotazovacím jazyce Kusto](/azure/kusto/query/)).

Nejste také omezeni pouze na dotazování z kontextu aplikace, ze které jste sešit spustili. Můžete dotazovat napříč více aplikací Monitorovaných aplikací, stejně jako Log Analytics pracovníprostory tak dlouho, dokud máte přístup oprávnění k těmto prostředkům.

Chcete-li dotazovat z dalších externích prostředků Application Insights, použijte identifikátor **aplikace.**

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Tento dotaz kombinuje požadavky ze tří různých aplikací. Aplikace s názvem app01, aplikace s názvem app02 a požadavky z místního prostředku Application Insights.

Chcete-li získat data z externího pracovního prostoru Log Analytics, použijte identifikátor **pracovního prostoru.**

Další informace o dotazech na příčné zdroje naleznete v [oficiálních pokynech](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Pokročilá nastavení analytického dotazu

Každá sekce má své vlastní pokročilé nastavení, které ![jsou přístupné prostřednictvím](./media/usage-workbooks/005-settings.png) ovládacích prvků pro úpravy oddílu Application Insights na ikonu nastavení umístěné vpravo od tlačítka **Přidat parametry.**

![Ovládací prvky pro úpravy oddílu Přehledy aplikací](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Vlastní šířka**    | Nastavte tuto možnost tak, aby se položka měla libovolně měnit, takže se na jeden řádek vejde mnoho položek, což vám umožní lépe uspořádat grafy a tabulky do bohatých interaktivních sestav.  |
   | **Podmíněně viditelné** | Pomocí této možnosti můžete skrýt kroky založené na parametru v režimu čtení. |
   | **Export parametru**| To umožňuje vybranému řádku v mřížce nebo grafu způsobit pozdější kroky ke změně hodnot nebo zobrazení.  |
   | **Zobrazit dotaz při neúpravách** | Zobrazí se dotaz nad grafem nebo tabulkou, i když je v režimu čtení.
   | **Zobrazit tlačítko Otevřít v analytice, když neupravujete** | Tím přidáte modrou ikonu Analytics do pravého rohu grafu, abyste umožnili přístup jedním kliknutím.|

Většina těchto nastavení je poměrně intuitivní, ale pro pochopení **exportu parametru** je lepší prozkoumat sešit, který využívá tuto funkci.

Jeden z předem vytvořených sešitů poskytuje informace o aktivních uživatelích.

První část sešitu je založena na datech dotazu Analytic:

![Ovládací prvky pro úpravy oddílu Přehledy aplikací](./media/usage-workbooks/003-active-users.png)

Druhá část je také založena na datech analytického dotazu, ale výběr řádku v první tabulce interaktivně aktualizuje obsah grafu:

![Ovládací prvky pro úpravy oddílu Přehledy aplikací](./media/usage-workbooks/004-active-users-trend.png)

 To je možné pomocí možnosti **Když je vybraná položka, exportujte** upřesňující nastavení parametrů, která jsou povolena v dotazu Analytics v tabulce.

![Ovládací prvky pro úpravy oddílu Přehledy aplikací](./media/usage-workbooks/007-settings-export.png)

Druhý analytický dotaz pak použije exportované hodnoty, když je vybrán řádek. Pokud není vybrán žádný řádek, je výchozí řádek představující celkové hodnoty. 

```
let start = startofday(ago({TimeRange} + {Metric}));
union customEvents, pageViews
| where timestamp >= start
| where name in ({Activities}) or '*' in ({Activities}) or ('%' in ({Activities}) and itemType == 'pageView') or ('#' in ({Activities}) and itemType == 'customEvent')
{OtherFilters}
| where '{Filter}' == '' or '{Filter}' == '🔸 Overall' or {AnalyzeBy} == replace('🔹 ', '', '{Filter}')
| evaluate activity_engagement(user_Id, timestamp, start, now(), 1d, {Metric})
| where timestamp >= startofday(ago({TimeRange}))
| project timestamp, ["Active User"] = dcount_activities_outer
| render timechart 
```

## <a name="adding-metrics-sections"></a>Přidávání oddílů metrik

Části metrik vám poskytují úplný přístup k začlenění dat metrik Azure Monitoru do interaktivních přehledů. Mnoho předem vytvořených sešitů bude obsahovat analytická data dotazů i metrická data, která vám umožní plně využít to nejlepší z obou funkcí na jednom místě. Máte také možnost získat metrická data z prostředků v libovolném předplatných, ke které máte přístup.

Tady je příklad dat virtuálního počítače, která jsou vtahována do sešitu, aby byla zajištěna vizualizace výkonu procesoru:

![Ovládací prvky pro úpravy oddílu Přehledy aplikací](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Přidávání oddílů parametrů

Parametry sešitu umožňují měnit hodnoty v sešitu, aniž byste museli ručně upravovat části dotazu nebo textu.  Tím se odstraní požadavek, že potřebujete porozumět základnímu dotazovacímu jazyku analýzy, a výrazně se rozšíří potenciální cílová skupina sestav založených na sešitu.

Hodnoty parametrů jsou nahrazeny v dotazu, textu nebo jiných parametrech umístěním názvu ``{parameterName}``parametru do složených závorek, například .  Názvy parametrů jsou omezeny na podobná pravidla jako identifikátory JavaScriptu, v podstatě abecední znaky nebo podtržítka, následované alfanumerickými znaky nebo podtržítky. Například **a1** je povoleno, ale **1a** není povoleno.

Parametry jsou lineární, počínaje horní částí sešitu a stékající dolů do pozdějších kroků.  Parametry deklarované později v sešitu mohou přepsat ty, které byly deklarovány dále nahoru.  To také umožňuje parametry, které používají dotazy pro přístup k hodnotám z parametrů definovaných dále nahoru.  V rámci samotného kroku parametru jsou parametry také lineární, zleva doprava, kde parametry vpravo mohou záviset na parametru deklarovaném dříve ve stejném kroku.
 
V současné době jsou podporovány čtyři různé typy parametrů:

  |         |          |
   | ---------------- |:-----|
   | **Text**    | uživatel upraví textové pole a můžete volitelně zadat dotaz k vyplnění výchozí hodnoty. |
   | **Rozevírací seznam** | Uživatel si vybere ze sady hodnot. |
   | **Výběr časového rozsahu**| Uživatel vybere z předdefinované sady hodnot časového rozsahu nebo z vlastního časového rozsahu.|
   | **Výběr zdrojů** | Uživatel vybere ze zdrojů vybraných pro sešit.|

### <a name="using-a-text-parameter"></a>Použití textového parametru

Hodnota, kterou uživatel zadá v textovém poli, je nahrazena přímo v dotazu bez úniku nebo citace. Pokud je hodnota, kterou potřebujete, řetězec, dotaz by měl mít uvozovky kolem parametru (například **{parameter}'**).

To umožňuje hodnotu v textovém poli, které mají být použity kdekoli. Může se jedná o název tabulky, název sloupce, název funkce, operátor atd.

Typ parametru textu má nastavení **Získat výchozí hodnotu z analytického dotazu**, která autorovi sešitu umožňuje použít dotaz k naplnění výchozí hodnoty pro toto textové pole.

Při použití výchozí hodnoty z analytického dotazu se jako výchozí hodnota použije pouze první hodnota prvního řádku (řádek 0, sloupec 0). Proto se doporučuje omezit dotaz vrátit pouze jeden řádek a jeden sloupec. Všechna ostatní data vrácená dotazem jsou ignorována. 

Bez ohledu na hodnotu dotazvrátí bude nahrazen přímo bez úniku nebo citace. Pokud dotaz vrátí žádné řádky, výsledkem parametru je buď prázdný řetězec (pokud parametr není vyžadován) nebo undefined (pokud je parametr požadován).

### <a name="using-a-dropdown"></a>Použití rozevíracího a kontinua

Typ rozevíracího parametru umožňuje vytvořit ovládací prvek rozevíracího souboru, který umožňuje výběr jedné nebo více hodnot.

Rozevírací seznam je naplněn analytickým dotazem. Pokud dotaz vrátí jeden sloupec, hodnoty v tomto sloupci jsou **hodnota** i **popisek** v ovládacím prvku rozevíracího souboru. Pokud dotaz vrátí dva sloupce, první sloupec je **hodnota**a druhý sloupec je **popisek** zobrazený v rozevíracím seznamu.  Pokud dotaz vrátí tři sloupce, třetí sloupec se použije k označení výchozího výběru v tomto rozevíracím seznamu.  Tento sloupec může být libovolný typ, ale nejjednodušší je použití bool nebo číselné typy, kde 0 je false a 1 je true.

 Pokud je sloupec typ řetězce, null/prázdný řetězec je považován za false a všechny ostatní hodnoty je považován za true. Pro rozevírací seznamy s jedním výběrem se jako výchozí výběr použije první hodnota s hodnotou true.  Pro více rozevíracích seznamů výběru jsou jako výchozí vybraná sada použity všechny hodnoty se skutečnou hodnotou. Položky v rozevíracím souboru jsou zobrazeny v libovolném pořadí, ve které dotaz vrátil řádky. 

Podívejme se na parametry, které jsou k dispozici v sestavě Aktivní uživatelé. Klepněte na symbol úprav vedle **položky TimeRange**.

![Ovládací prvky pro úpravy oddílu Přehledy aplikací](./media/usage-workbooks/009-time-range.png)

Tím se spustí položka nabídky Upravit parametry:

![Ovládací prvky pro úpravy oddílu Přehledy aplikací](./media/usage-workbooks/010-time-range-edit.png)

Dotaz používá funkci analytického dotazovacího jazyka nazvanou **datová tabulka,** která umožňuje generovat libovolnou tabulku plnou obsahu z čista jasna! Například následující analytický dotaz:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Generuje výsledek:

![Ovládací prvky pro úpravy oddílu Přehledy aplikací](./media/usage-workbooks/011-data-table.png)

Vhodnějším příkladem je použití rozevíracího souboru k výběru ze sady zemí nebo oblastí podle názvu:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

Dotaz zobrazí výsledky následujícím způsobem:

![Rozevírací skupina země](./media/usage-workbooks/012-country-dropdown.png)

Rozevírací seznamy jsou neuvěřitelně výkonné nástroje pro přizpůsobení a vytváření interaktivních sestav.

### <a name="time-range-parameters"></a>Parametry časového rozsahu

Zatímco můžete vytvořit vlastní parametr časového rozsahu pomocí typu parametru rozevíracího pole, můžete také použít typ parametru časového rozsahu out-of-box, pokud nepotřebujete stejný stupeň flexibility. 

Typy parametrů časového rozsahu mají 15 výchozírozsahy, které přejdou od pěti minut do posledních 90 dnů. K dispozici je také možnost povolit výběr vlastního časového rozsahu, která umožňuje operátoru sestavy zvolit explicitní počáteční a stopové hodnoty pro časový rozsah.

### <a name="resource-picker"></a>Výběr zdrojů

Typ parametru výběru prostředků umožňuje určit určité typy prostředků. Příkladem předem sestaveného sešitu, který využívá typ výběru prostředků, je sešit **Přehledy selhání.**

![Rozevírací skupina země](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Ukládání sešitů a jejich sdílení v rámci týmu

Sešity se ukládají do prostředku Application Insights, buď v části **Moje sestavy,** která je pro vás soukromá, nebo v části **Sdílené sestavy,** která je přístupná všem uživatelům s přístupem k prostředku Přehledy aplikací. Chcete-li zobrazit všechny sešity v zdroji, klepněte na tlačítko **Otevřít** na panelu akcí.

Sdílení sešitu, který je aktuálně v **seznamu Moje sestavy**:

1. Na panelu akcí klikněte na **Otevřít.**
2. Klikněte na "..." vedle sešitu, který chcete sdílet
3. Klikněte na **Přesunout do sdílených sestav**.

Pokud chcete sešit sdílet s odkazem nebo e-mailem, klikněte na panelu akcí na **Sdílet.** Mějte na paměti, že příjemci odkazu potřebují přístup k tomuto prostředku na webu Azure Portal, aby mohly sešit zobrazit. K úpravám potřebují příjemci alespoň oprávnění přispěvatele pro prostředek.

Připnutí odkazu na sešit na řídicí panel Azure:

1. Na panelu akcí klikněte na **Otevřít.**
2. Klikněte na "..." vedle sešitu, který chcete připnout
3. Klikněte **na Připnout na řídicí panel**.

## <a name="contributing-workbook-templates"></a>Přispívání šablon sešitů

Vytvořili jste úžasnou šablonu sešitu a chcete ji sdílet s komunitou? Chcete-li se dozvědět více, navštivte náš [úložiště GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md).

## <a name="next-steps"></a>Další kroky
- Chcete-li povolit možnosti využití, začněte odesílat [vlastní události](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) nebo [zobrazení stránek](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Pokud už vlastní události nebo zobrazení stránek odesíláte, prozkoumejte nástroje využití a zjistěte, jak uživatelé vaši službu používají.
    - [Uživatelé, relace, události](../../azure-monitor/app/usage-segmentation.md)
    - [Trychtýře](../../azure-monitor/app/usage-funnels.md)
    - [Uchovávání](../../azure-monitor/app/usage-retention.md)
    - [Toky uživatele](../../azure-monitor/app/usage-flows.md)
    - [Přidání kontextu uživatele](../../azure-monitor/app/usage-send-user-context.md)
