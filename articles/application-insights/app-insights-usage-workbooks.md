---
title: Vytváření interaktivních sestav se sešity Azure Monitor | Dokumentace Microsoftu
description: Zjednodušení složitých vytváření sestav pomocí předem připravená a vlastní parametry sešity
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: daviste
ms.pm_owner: daviste;NumberByColors
ms.author: mbullwin
ms.openlocfilehash: 2a9ee67d2763eb1d88ab09520c69dd46ead3e93f
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465925"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Vytváření interaktivních sestav se sešity Azure Monitor

Sešity kombinovat text, [analytických dotazů](https://docs.microsoft.com/azure/application-insights/app-insights-analytics), metriky Azure a parametrů do bohaté interaktivní sestavy. Sešity se upravovat jiným členem týmu, kteří mají přístup ke stejným prostředkům Azure.

Sešity jsou užitečné pro scénáře, jako jsou:

* Zkoumání využití aplikací, pokud neznáte metriky, které vás zajímají předem: počet uživatelů, míry uchovávání dat, míry úspěšnosti atd. Na rozdíl od jiných nástrojů pro analýzu využití pracovní sešity umožňují kombinovat více typů vizualizace a analýzy, díky kterým jsou skvělé pro tento druh zkoumání volném.
* Ke svému týmu vysvětlující, jaký je výkon nově vydané funkci, uživatelem zobrazují počty pro klíče interakce a další metriky.
* Sdílení výsledků A / B experimentovat ve vaší aplikaci s dalšími členy týmu. Vysvětlete cíle pro experiment s textem a potom ukazují jednotlivých metrika využití a dotazu Analytics k vyhodnocení, experiment, spolu s vymazat značek pro Určuje, zda jednotlivé metriky se nad nebo pod cíl.
* Dopad výpadku vyvářet využití aplikací, kombinování dat, text vysvětlení a diskuzi o dalších krocích, aby se zabránilo výpadkům v budoucnu.

## <a name="starting-with-a-template-or-saved-workbook"></a>Od verze šablony nebo uložené sešitu

Sešit je tvořené oddíly skládající se z nezávisle upravovat grafy, tabulky, text a vstupní ovládací prvky. Abyste lépe pochopili, pracovní sešity, je nejlepší otevřete jednu. 

Vyberte **sešity** z nabídky vlevo od uvnitř prostředí služby Application Insights pro vaši aplikaci.

![Snímek obrazovky navigace do sešitů](./media/app-insights-usage-workbooks/001-workbooks.png)

Tím se spustí Galerie sešitu s celou řadou předem připravené pracovní sešity k vám pomůžou začít.

![Snímek obrazovky Galerie sešitu](./media/app-insights-usage-workbooks/002-workbook-gallery.png)

Začneme **výchozí šablona**, který se nachází pod nadpisem **úvodní**.

![Snímek obrazovky Galerie sešitu](./media/app-insights-usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Úpravy, uspořádání, klonování a odstranění sešitu oddíly

Sešity mají dva režimy: **režimu úprav**, a **neupravuje**. Při prvním spuštění výchozí sešit se otevře v **režimu úprav**. Zobrazí veškerý obsah sešitu včetně všech kroků a parametrů, které jsou jinak skryté. **Režim čtení** představuje styl zobrazení zjednodušené sestavy. Můžete provádět abstrakci složitost, která jsou součástí vytváření sestav přitom stále má základní mechanismus jenom několika kliknutí v případě potřeby pro úpravy.

![Application Insights sešity části ovládací prvky pro úpravy](./media/app-insights-usage-workbooks/editing-controls-new.png)

1. Po dokončení úprav oddíl, klikněte na tlačítko **provést úpravy** v levém dolním rohu části.

2. K vytvoření duplicitní oddíl, klikněte na tlačítko **klonovat tento oddíl** ikonu. Vytvoření duplicitní části je skvělý způsob, jak iterovat dotazu bez ztráty předchozími iteracemi.

3. Posunout nahoru oddíl v sešitu, klikněte na tlačítko **nahoru** nebo **přesunout dolů** ikonu.

4. Pokud chcete trvale odebrat oddíl, klikněte na tlačítko **odebrat** ikonu.

## <a name="adding-text-and-markdown-sections"></a>Přidání textu a části Markdownu

Přidání záhlaví, vysvětlení a poznámky k sešity pomůže proměnit sadu tabulek a grafů komentáře. Části textu v sešitech technické podpory [syntaxe Markdownu](https://daringfireball.net/projects/markdown/) pro formátování textu, jako jsou nadpisy, tučné, kurzíva a seznamy s odrážkami.

Chcete-li přidat do sešitu část textu, použijte **přidat text** tlačítko v dolní části sešitu nebo v dolní části libovolné části.

## <a name="adding-query-sections"></a>Přidání dotazu oddílů

![V sešitech části dotazu](./media/app-insights-usage-workbooks/analytics-section-new.png)

Chcete-li přidat do sešitu část dotazu, použijte **přidat dotaz** tlačítko v dolní části sešitu nebo v dolní části libovolné části.

Dotaz oddíly jsou vysoce flexibilní a umožňuje zodpovědět dotazy jako:

* Kolik výjimky váš web vyvolat za stejné období jako pokles využití?
* Jaký byl distribuční časy načtení stránek uživatelům, kteří zobrazují některé stránky?
* Kolik uživatelů zobrazit některé sadu stránek na webu, ale není některých dalších nastavení stránek? To může být užitečné k pochopení, pokud máte clustery uživatelů, kteří používají různé podmnožiny funkčnosti vaší lokality (použít `join` operátor s `kind=leftanti` modifikátor v dotazovací jazyk Log Analytics).

Můžete také nejsou pouze omezeni na dotazování v kontextu aplikace spustí ze sešitu. Můžete zadávat dotazy napříč více Application Insights monitorovat aplikace a zároveň pracovních prostorů Log Analytics, dokud máte přístupová oprávnění k těmto prostředkům.

Dotaz z další externí použití prostředky Application Insights **aplikace** identifikátor.

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Tento dotaz je kombinování požadavků ze tří různých aplikací. Aplikace s názvem app01 aplikaci s názvem app02 a požadavky z místního prostředku Application Insights.

Aby se načetla data z externí použití pracovního prostoru Log Analytics **pracovní prostor** identifikátor.

Další informace o dotazech napříč prostředky najdete [oficiální pokyny](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Upřesňující nastavení analytického dotazu

Každá část má svůj vlastní upřesňující nastavení, které jsou přístupné pomocí ikony nastavení ![Application Insights sešity části ovládací prvky pro úpravy](./media/app-insights-usage-workbooks/005-settings.png) nachází na pravé straně **přidat parametry** tlačítko.

![Application Insights sešity části ovládací prvky pro úpravy](./media/app-insights-usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Vlastní šířky**    | Nastavení této možnosti nastavíte položku na libovolné velikosti, takže můžete přizpůsobit mnoho položek na jeden řádek, abyste mohli lépe uspořádat grafů a tabulek do bohaté interaktivní sestavy.  |
   | **Jako podmíněně viditelnou** | Použijte tím skryjete kroky na základě parametru v režimu čtení. |
   | **Exportovat parametr**| To umožňuje vybraný řádek v mřížce nebo grafu způsobit pozdější kroky změní hodnoty nebo viditelná.  |
   | **Zobrazit dotaz, když neprobíhají úpravy** | Zobrazí se dotaz nad tento graf nebo tabulku i v režimu čtení.
   | **Zobrazit otevřít v analytics tlačítko, pokud nepoužíváte režim úprav** | Tento postup přidá modrou ikonou ve tvaru Analytics na pravém horním rohu grafu umožňující přístup jedním kliknutím.|

Většinu těchto nastavení jsou poměrně intuitivní, ale pro pochopení **exportovat parametr** je lepší prozkoumat sešitu která používá tuto funkci.

Jeden z předem připravené pracovní sešity obsahuje informace o aktivních uživatelů.

První část sešitu je založena na datech analytického dotazu:

![Application Insights sešity části ovládací prvky pro úpravy](./media/app-insights-usage-workbooks/003-active-users.png)

Druhá část je taky založený na analytické dotazy na data, ale výběru řádku v první tabulce interaktivně aktualizuje obsah grafu:

![Application Insights sešity části ovládací prvky pro úpravy](./media/app-insights-usage-workbooks/004-active-users-trend.png)

 To je možné provádět prostřednictvím použití **při výběru položky exportovat parametr** upřesňující nastavení, které jsou povoleny v dotazu Analytics v tabulce.

![Application Insights sešity části ovládací prvky pro úpravy](./media/app-insights-usage-workbooks/007-settings-export.png)

Druhý dotaz analytics pak využívá exportovaných hodnot při výběru řádku. Pokud není vybrán žádný řádek, použije se výchozí řádek, který představuje celkové hodnoty. 

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

## <a name="adding-metrics-sections"></a>Přidání metriky oddílů

Oddíly metrik získáte plný přístup k začlenění dat metrik Azure monitoru interaktivní sestavy. Řadu předem připravené pracovní sešity bude obsahovat analytické dotazy na data a data metriky, abyste mohli plně využít to nejlepší z obou funkcí vše na jednom místě. Máte také možnost, aby se načetla data metriky z prostředků ve všech předplatných, ke kterým máte přístup k.

Tady je příklad dat virtuálních počítačů právě načetli do sešitu poskytnout vizualizaci mřížky výkon procesoru:

![Application Insights sešity části ovládací prvky pro úpravy](./media/app-insights-usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Přidání parametru oddílů

Sešit parametry umožňují změnit bez nutnosti ručně upravit části dotazu nebo textové hodnoty v sešitu.  To eliminuje nutnost by bylo nutné pochopit základní dotazovací jazyk analytics a značně rozšíří potenciální cílovou skupinu na základě sešitu generování sestav.

Hodnoty parametrů se nahradí v dotazu, text nebo jiné části parametr vložením název parametru ve složených závorkách, jako je ``{parameterName}``.  Názvy parametrů jsou omezené na podobnými pravidly jako identifikátory jazyka JavaScript, v podstatě abecední znaky nebo podtržítka, za nímž následuje alfanumerické znaky nebo podtržítka. Například **a1** může, ale **1a** není povolený.

Parametry jsou lineárně závislé, od horní části sešitu a odesílaných do dalších krocích.  Parametry deklarovanými jako později v sešitu můžete přepsat ty, které byly deklarovány další nahoru.  Díky tomu také další až dotazy použijte pro přístup k hodnoty z parametrů definovaných parametrů.  V rámci kroku parametr na samotný parametry jsou také lineární, zleva doprava, kde parametry na pravé straně může záviset na parametr deklarovaný dříve v tomto kroku stejné.
 
Existují čtyři různé typy parametrů, které jsou aktuálně podporovány:

  |         |          |
   | ---------------- |:-----|
   | **Text**    | Uživatel se upravit textové pole a volitelně může zadat dotaz vyplnit výchozí hodnotu. |
   | **Rozevírací seznam** | Uživatel zvolí ze sady hodnot. |
   | **Výběr časového rozsahu**| Uživatel vybírat předdefinovanou sadu hodnot rozsahu času, nebo si můžete vybrat z vlastní časový rozsah.|
   | **Výběr prostředku** | Uživatel zvolí z prostředků vybrané pro sešit.|

### <a name="using-a-text-parameter"></a>Pomocí parametru text

Hodnota, která nahrazuje uživatelské typy v textovém poli přímo v dotazu bez uvozovací znaky nebo uvozovky u. Pokud je hodnota, je třeba řetězec, dotaz by měl mít uvozovky kolem parametru (jako je **'{parameter}'**).

To umožňuje hodnotu v textovém poli, který se má použít kdekoli. Může být název tabulky, název sloupce, název funkce, operátor, atd.

Typ parametru text má nastavení **získat výchozí hodnotu z analytického dotazu**, což umožňuje Autor sešitu použít dotaz k naplnění výchozí hodnota tohoto textového pole.

Při použití výchozí hodnotu z analytického dotazu, pouze první hodnotu na prvním řádku (0 se sloupci 0) se používá jako výchozí hodnotu. Proto se doporučuje omezit dotaz vrátit jen jeden řádek a jeden sloupec. Žádná další data vrácená dotazem se ignoruje. 

Všechno, co dotaz se vrátí hodnota se nahradí přímo bez uvozovací znaky nebo uvozovky u. Pokud dotaz vrací žádné řádky, výsledek parametru je buď prázdný řetězec (Pokud parametr není povinný) nebo nedefinovaný (Pokud je parametr povinný).

### <a name="using-a-dropdown"></a>Pomocí rozevíracího seznamu

Typ parametru rozevíracího seznamu umožňuje vytvořit ovládací prvek rozevírací seznam, povolení výběru jednoho nebo více hodnot.

Rozevírací seznam je vyplněn analytický dotaz. Pokud dotaz vrací jeden sloupec, jsou hodnoty v tomto sloupci **hodnotu** a **popisek** v ovládacím prvku rozevíracího seznamu. Pokud dotaz vrací dva sloupce, na první sloupec je **hodnotu**, a druhý sloupec je **popisek** zobrazeny v rozevírací nabídce.  Pokud dotaz vrací tři sloupce, 3. sloupec slouží k označení výchozí výběr v této rozevírací nabídce.  V tomto sloupci může být libovolný typ, ale nejjednodušší je použití bool nebo číselné typy, kde 0 je NEPRAVDA a 1 je PRAVDA.

 Pokud je ve sloupci Typ řetězce, hodnotu null nebo prázdný řetězec je považován za hodnotu false a jakoukoli jinou hodnotu je považován za hodnotu true. Ovládací prvek DropDowns jeden výběr první hodnota s hodnotou true slouží jako výchozí výběr.  Ovládací prvek DropDowns více výběr všechny hodnoty true hodnotou slouží jako výchozí vybraná sada. Položky v rozevírací nabídce se zobrazí v libovolném pořadí dotaz vrátil řádků. 

Podívejme se na parametry, které jsou součástí sestavy aktivních uživatelů. Kliknutím na symbol upravit vedle **TimeRange**.

![Application Insights sešity části ovládací prvky pro úpravy](./media/app-insights-usage-workbooks/009-time-range.png)

Tím se spustí položku nabídky Upravit parametr:

![Application Insights sešity části ovládací prvky pro úpravy](./media/app-insights-usage-workbooks/010-time-range-edit.png)

Dotaz využívá dotazovací jazyk analytics volá funkce **datatable** , který umožňuje generovat libovolného tabulky, úplné obsahu z dynamického zajišťování air! Například následující dotaz analytics:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Vygeneruje výsledek:

![Application Insights sešity části ovládací prvky pro úpravy](./media/app-insights-usage-workbooks/011-data-table.png)

Hodí víc příklad používá rozevírací seznam pro výběr ze sady zemí podle názvu:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

Výsledky se zobrazí dotaz následujícím způsobem:

![Rozevírací seznam země](./media/app-insights-usage-workbooks/012-country-dropdown.png)

Rozevírací seznamy jsou velmi výkonné nástroje pro přizpůsobení a vytváření interaktivních sestav.

### <a name="time-range-parameters"></a>Parametry rozsah času

Když provedete vlastní parametr vlastní časový rozsah přes typ parametru rozevírací seznam, můžete také typ parametru out-of-box časového rozsahu Pokud už nebudete potřebovat stejnou úroveň flexibilitu. 

Typy parametrů rozsah času mají 15 výchozí rozsahy, které přejít z pěti minut na poslední 90 dnů. Je také možnost povolit výběr vlastního časového rozsahu, který umožňuje operátorovi sestavy vyberte explicitní spuštění a zastavení hodnoty pro časový rozsah.

### <a name="resource-picker"></a>Výběr prostředku

Typ prostředku pro výběr parametru dává možnost k určení oboru sestavy na určité typy prostředků. Je například předem připravených sešit, který využívá výběr typu prostředku **selhání Insights** sešitu.

![Rozevírací seznam země](./media/app-insights-usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Ukládání a sdílení sešitů s týmem

Sešity jsou uloženy v rámci prostředku Application Insights, buď v **Moje sestavy** oddíl, který je pro vás nebo v privátní **sdílené sestavy** oddíl, který je přístupné všem uživatelům s přístupem k Prostředek služby Application Insights. Chcete-li zobrazit všechny soubory v prostředku, klikněte na tlačítko **otevřít** tlačítko na panelu akcí.

Sdílet sešit, který je v současné době **Moje sestavy**:

1. Klikněte na tlačítko **otevřít** na panelu akcí
2. Klikněte na tlačítko "..." vedle sešit, který chcete sdílet
3. Klikněte na tlačítko **přesunout do sdílených sestav**.

Chcete-li sdílet sešit s odkazem nebo e-mailem, klikněte na tlačítko **sdílet** na panelu akcí. Uvědomte si, že příjemci odkazu potřebují přístup k tomuto prostředku na webu Azure Portal k zobrazení tohoto sešitu. K provádění úprav, příjemci potřebovat aspoň oprávnění přispěvatele pro prostředek.

Pokud chcete připnout odkaz k sešitu na řídicí panel Azure:

1. Klikněte na tlačítko **otevřít** na panelu akcí
2. Klikněte na tlačítko "..." vedle sešit, který chcete připnout
3. Klikněte na tlačítko **připnout na řídicí panel**.

## <a name="contributing-workbook-templates"></a>Přispívání šablonách sešitů

Jste vytvořili šablonu Super sešitu máte a chcete ho sdílet s komunitou? Pokud chcete dozvědět víc, navštivte naši [úložiště GitHub se vzorovými](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md).

## <a name="next-steps"></a>Další postup
- Povolit použití prostředí, spusťte odesílání [vlastních událostí](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) nebo [zobrazení stránek](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Pokud jste již posílat vlastní události nebo zobrazení stránek, prozkoumejte nástroje využití se dozvíte, jak uživatelé vaši službu používat.
    - [Uživatelé, relace, události](app-insights-usage-segmentation.md)
    - [Trychtýře](usage-funnels.md)
    - [Uchování](app-insights-usage-retention.md)
    - [Toky uživatele](app-insights-usage-flows.md)
    - [Přidat kontext uživatele](app-insights-usage-send-user-context.md)