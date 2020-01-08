---
title: Vytváření interaktivních sestav pomocí Azure Monitorch sešitů | Dokumentace Microsoftu
description: Zjednodušení složitých sestav pomocí předem sestavených a vlastních parametrizovaných sešitů
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 8e14b065132e648f9c800655cfe69e5a2ec80c41
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432259"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Vytváření interaktivních sestav pomocí Azure Monitorch sešitů

Sešity kombinují text, [analytické dotazy](https://docs.microsoft.com/azure/application-insights/app-insights-analytics), metriky Azure a parametry do propracovaných interaktivních sestav. Sešity mohou upravovat všichni ostatní členové týmu, kteří mají přístup ke stejným prostředkům Azure.

Sešity jsou užitečné pro scénáře, jako jsou:

* Prozkoumejte využití vaší aplikace, když neznáte metriky, které vás zajímají předem: počet uživatelů, míry uchovávání, přepočítací tarify atd. Na rozdíl od jiných analytických nástrojů se vám sešity můžou kombinovat s několika různými typy vizualizací a analýz, takže jsou skvělé pro tento druh průzkumu volného tvaru.
* Vysvětlení týmu, jak nově vydaná funkce probíhá, díky zobrazení počtů uživatelů pro klíčové interakce a další metriky.
* Sdílení výsledků experimentu A/B v aplikaci s ostatními členy týmu. Můžete vysvětlit cíle experimentu s textem a pak zobrazit jednotlivé metriky využití a analytické dotazy použité k vyhodnocení experimentu spolu s jasnými voláními pro skutečnost, zda byla každá metrika nad nebo pod cílovou.
* Oznamujeme dopad výpadku při používání vaší aplikace, kombinování dat, vysvětlení textu a diskuzi o dalších krocích, aby se předešlo výpadkům v budoucnosti.

## <a name="starting-with-a-template-or-saved-workbook"></a>Počínaje šablonou nebo uloženým sešitem

Sešit je tvořen oddíly, které se skládají z nezávisle upravitelných grafů, tabulek, textových a vstupních ovládacích prvků. Aby lépe porozuměl sešitům, je nejlepší ho otevřít. 

V nabídce na levé straně vyberte **sešity** z prostředí Application Insights pro vaši aplikaci.

![Snímek obrazovky s navigací k sešitům](./media/usage-workbooks/001-workbooks.png)

Tím se spustí Galerie sešitů s několika předem sestavenými sešity, které vám pomůžou začít.

![Snímek obrazovky Galerie sešitů](./media/usage-workbooks/002-workbook-gallery.png)

Začneme s **výchozí šablonou**, která je umístěná v části **rychlý Start**nadpisu.

![Snímek obrazovky Galerie sešitů](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Úpravy, změna uspořádání, klonování a odstraňování oddílů sešitu

Sešity mají dva režimy: **režim úprav**a **režim čtení**. Když se výchozí sešit poprvé spustí, otevře se v **režimu úprav**. Zobrazí se veškerý obsah sešitu včetně všech kroků a parametrů, které jsou jinak skryté. **Režim čtení** prezentuje zjednodušené zobrazení stylů sestav. To vám umožňuje předcházet složitou složitost, která se stala vytvořením sestavy, zatímco pořád má podkladovou mechaniku v případě potřeby k úpravám jenom několik kliknutí.

![Ovládací prvky pro úpravy oddílu Application Insights sešity](./media/usage-workbooks/editing-controls-new.png)

1. Až budete hotovi s úpravou oddílu, klikněte v levém dolním rohu oddílu na **hotové úpravy** .

2. Chcete-li vytvořit duplikát oddílu, klikněte na ikonu **klonovat tuto část** . Vytváření duplicitních oddílů je skvělým způsobem, jak iterovat na dotaz, aniž by došlo ke ztrátě předchozích iterací.

3. Chcete-li v sešitu přesunout oddíl nahoru, klikněte na ikonu **Přesunout nahoru** nebo **Přesunout dolů** .

4. Pokud chcete oddíl trvale odebrat, klikněte na ikonu **Odebrat** .

## <a name="adding-text-and-markdown-sections"></a>Přidání textových a Markdownuch oddílů

Přidávání nadpisů, vysvětlení a komentářů k vašim sešitům usnadňuje vytvoření sady tabulek a grafů do mluveného komentáře. Textové oddíly v sešitech podporují [syntaxi Markdownu](https://daringfireball.net/projects/markdown/) pro formátování textu, jako jsou nadpisy, tučné písmo, kurzíva a seznamy s odrážkami.

Chcete-li přidat textový oddíl do sešitu, použijte tlačítko **Přidat text** v dolní části sešitu nebo dolní část libovolné části.

## <a name="adding-query-sections"></a>Přidávání sekcí dotazů

![Část dotazu v sešitech](./media/usage-workbooks/analytics-section-new.png)

Chcete-li přidat do sešitu oddíl dotazu, použijte tlačítko **Přidat dotaz** ve spodní části sešitu nebo v dolní části části.

Oddíly dotazů jsou vysoce flexibilní a dají se použít k zodpovězení otázek, jako je:

* Kolik výjimek vyvolala vaše lokalita během stejného časového období jako pokles využití?
* Jaká byla distribuce časů načítání stránky pro uživatele, kteří si stránku prohlížejí?
* Kolik uživatelů navštívilo určitou sadu stránek na webu, ale ne některé jiné sady stránek? To může být užitečné pochopit, jestli máte clustery uživatelů, kteří používají různé podmnožiny funkcí vaší lokality (použijte operátor `join` s modifikátorem `kind=leftanti` v [dotazovacím jazyce Kusto](/azure/kusto/query/)).

Nebudete se také omezovat jenom na dotazování z kontextu aplikace, ze které jste sešit spustili. Můžete zadávat dotazy na více Application Insights monitorovaných aplikací a také Log Analytics pracovní prostory, pokud máte přístupová oprávnění k těmto prostředkům.

K dotazování z dalších externích prostředků Application Insights použijte identifikátor **aplikace** .

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Tento dotaz kombinuje požadavky ze tří různých aplikací. Aplikace s názvem app01, aplikace s názvem app02 a požadavky z prostředku místní Application Insights.

Pokud chcete načíst data z externího pracovního prostoru Log Analytics použijte identifikátor **pracovního prostoru** .

Další informace o dotazech mezi prostředky najdete v [oficiálních pokynech](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Pokročilé nastavení analytického dotazu

Každá část má vlastní Rozšířená nastavení, která jsou přístupná prostřednictvím ikony nastavení ![Application Insights sešity editační ovládací prvky](./media/usage-workbooks/005-settings.png) umístěné napravo od tlačítka **přidat parametry** .

![Ovládací prvky pro úpravy oddílu Application Insights sešity](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Vlastní šířka**    | Tuto možnost nastavte, pokud chcete mít položku v libovolné velikosti, takže můžete umístit mnoho položek na jeden řádek, což vám umožní lépe uspořádat grafy a tabulky do propracovaných interaktivních sestav.  |
   | **Podmíněně viditelné** | Tuto hodnotu použijte ke skrytí kroků na základě parametru v režimu čtení. |
   | **Exportovat parametr**| Díky tomu může vybraný řádek v mřížce nebo grafu způsobit pozdější postup změny hodnot nebo zobrazení.  |
   | **Zobrazit dotaz, pokud se neupravuje** | Tím se zobrazí dotaz nad graf nebo tabulka, i když v režimu čtení.
   | **Při úpravách zobrazit tlačítko otevřít v analýze** | Tím se přidá ikona Blue Analytics do pravého horního rohu grafu umožňující přístup jedním kliknutím.|

Většina těchto nastavení je poměrně intuitivní, ale pro pochopení **exportu parametru** je lepší prozkoumávat sešit, který tuto funkci využívá.

Jeden z předem připravených sešitů poskytuje informace o aktivních uživatelích.

První část sešitu je založena na analytických datech dotazů:

![Ovládací prvky pro úpravy oddílu Application Insights sešity](./media/usage-workbooks/003-active-users.png)

Druhá část je také založená na analytických dotazech, ale výběr řádku v první tabulce bude interaktivně aktualizovat obsah grafu:

![Ovládací prvky pro úpravy oddílu Application Insights sešity](./media/usage-workbooks/004-active-users-trend.png)

 To je možné v **případě, že je vybrána položka, exportovat** upřesňující nastavení parametrů, která jsou povolena v dotazu Analytics tabulky.

![Ovládací prvky pro úpravy oddílu Application Insights sešity](./media/usage-workbooks/007-settings-export.png)

Druhý analytický dotaz pak použije exportované hodnoty, když je vybrán řádek. Pokud není vybraný žádný řádek, použije se výchozí řádek, který představuje celkové hodnoty. 

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

## <a name="adding-metrics-sections"></a>Přidávání sekcí metriky

Oddíly metrik poskytují úplný přístup, který umožňuje začlenit do interaktivních sestav Azure Monitor data metrik. Mnohé z předem připravených sešitů budou obsahovat data analytických dotazů a data metriky, které vám umožní plně využít výhod obou funkcí na jednom místě. Máte také možnost přijímat data metrik z prostředků v libovolném z předplatných, ke kterým máte přístup.

Tady je příklad dat virtuálního počítače, která jsou načítána do sešitu, aby bylo možné vytvořit vizualizaci mřížky výkonu procesoru:

![Ovládací prvky pro úpravy oddílu Application Insights sešity](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Přidávání oddílů parametrů

Parametry sešitu umožňují změnit hodnoty v sešitu bez nutnosti ručně upravovat oddíly dotazu nebo textu.  Tím se eliminuje nutnost potřebovat porozumět základnímu dotazovacímu jazyku a významně rozšířit potenciální cílovou skupinu vytváření sestav na základě sešitu.

Hodnoty parametrů jsou nahrazeny v části Query, text nebo jiné oddíly parametrů vložením názvu parametru do složených závorek, například ``{parameterName}``.  Názvy parametrů jsou omezené na podobná pravidla jako identifikátory JavaScriptu, v podstatě abecední znaky nebo podtržítka, následované alfanumerickými znaky nebo podtržítky. Například **a1** je povoleno, ale **1a** není povoleno.

Parametry jsou lineární, počínaje horním okrajem sešitu a natékání dolů na pozdější kroky.  Parametry deklarované později v sešitu mohou přepsat ty, které byly deklarovány dále.  To také umožňuje parametrům, které používají dotazy pro přístup k hodnotám z parametrů definovaných dále.  V samotném kroku parametru jsou parametry také lineární, zleva doprava, kde parametry vpravo mohou záviset na parametru deklarovaném dříve v tomto kroku.
 
Existují čtyři různé typy parametrů, které jsou aktuálně podporovány:

  |         |          |
   | ---------------- |:-----|
   | **Text**    | uživatel upraví textové pole a volitelně můžete zadat dotaz, který bude vyplnit výchozí hodnotu. |
   | **Rozevírací seznam** | Uživatel se vybere ze sady hodnot. |
   | **Výběr časového rozsahu**| Uživatel se vybere z předdefinované sady hodnot časového rozsahu nebo vybere z vlastního časového rozsahu.|
   | **Výběr prostředku** | Uživatel se vybere z prostředků vybraných pro sešit.|

### <a name="using-a-text-parameter"></a>Použití textového parametru

Hodnota uživatelské typy v textovém poli je nahrazena přímo v dotazu bez uvozovacích znaků nebo citací. Pokud je hodnota, kterou potřebujete, řetězec, dotaz by měl obsahovat uvozovky kolem parametru (například **{Parameter}** ).

To umožňuje, aby se hodnota v textovém poli použila kdekoli. Může to být název tabulky, název sloupce, název funkce, operátor atd.

Typ textového parametru má nastavení **načíst výchozí hodnotu z analytického dotazu**, které umožňuje autorovi sešitu použít dotaz k naplnění výchozí hodnoty tohoto textového pole.

Při použití výchozí hodnoty z analytického dotazu je jako výchozí hodnota použita pouze první hodnota prvního řádku (řádek 0, sloupec 0). Proto je doporučeno omezit dotaz tak, aby vracel pouze jeden řádek a jeden sloupec. Všechna ostatní data vrácená dotazem jsou ignorována. 

Libovolná hodnota, kterou dotaz vrátí, se nahradí přímo bez uvozovacích znaků nebo citací. Pokud dotaz nevrátí žádné řádky, výsledek parametru je buď prázdný řetězec (Pokud parametr není požadován) nebo undefined (Pokud je parametr požadován).

### <a name="using-a-dropdown"></a>Použití rozevíracího seznamu

Typ parametru rozevíracího seznamu umožňuje vytvořit ovládací prvek rozevíracího seznamu, který umožňuje výběr jedné nebo více hodnot.

Rozevírací seznam se naplní dotazem Analytics. Pokud dotaz vrátí jeden sloupec, hodnoty v tomto sloupci budou **hodnotou** a **popiskem** v ovládacím prvku rozevírací seznam. Pokud dotaz vrátí dva sloupce, je prvním sloupcem **hodnota**a druhý sloupec je **popisek** zobrazený v rozevíracím seznamu.  Pokud dotaz vrátí tři sloupce, použije se třetí sloupec k označení výchozího výběru v tomto rozevíracím seznamu.  Tento sloupec může být libovolného typu, ale nejjednodušší je použít bool nebo číselné typy, kde 0 je false a 1 je pravda.

 Pokud je sloupec typem řetězce, hodnota null nebo prázdný řetězec se považuje za false a jakákoli jiná hodnota se považuje za true. U rozevíracích seznamů s jedním výběrem se jako výchozí výběr použije první hodnota s hodnotou true.  U rozevíracích seznamů s vícenásobným výběrem se jako výchozí vybraná sada použije všechny hodnoty s hodnotou true. Položky v rozevíracím seznamu jsou zobrazeny v jakémkoli pořadí, ve kterém dotaz vrátil řádky. 

Pojďme se podívat na parametry přítomné v sestavě aktivní uživatelé. Klikněte na symbol úprav vedle **TimeRange**.

![Ovládací prvky pro úpravy oddílu Application Insights sešity](./media/usage-workbooks/009-time-range.png)

Tím se spustí položka nabídky upravit parametr:

![Ovládací prvky pro úpravy oddílu Application Insights sešity](./media/usage-workbooks/010-time-range-edit.png)

Dotaz používá funkci dotazovacího jazyka Analytics s názvem **DataTable** , která umožňuje vygenerovat libovolnou tabulku s plným obsahem, z tenkého vzduchu. Například následující analytický dotaz:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Vygeneruje výsledek:

![Ovládací prvky pro úpravy oddílu Application Insights sešity](./media/usage-workbooks/011-data-table.png)

Více použitelným příkladem je použití rozevírací nabídky k výběru ze sady zemí nebo oblastí podle názvu:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

Dotaz zobrazí výsledky následujícím způsobem:

![Rozevírací nabídka země](./media/usage-workbooks/012-country-dropdown.png)

Rozevírací seznamy jsou neuvěřitelně výkonné nástroje pro přizpůsobení a vytváření interaktivních sestav.

### <a name="time-range-parameters"></a>Parametry časového rozsahu

I když můžete vytvořit vlastní parametr časového rozsahu prostřednictvím rozevíracího seznamu, můžete použít také typ parametru časového rozsahu, pokud nepotřebujete stejnou míru flexibility. 

Typy parametrů časového rozsahu mají 15 výchozích rozsahů, které přecházejí z pěti minut na posledních 90 dní. K dispozici je také možnost Povolit vlastní výběr časového rozsahu, který umožňuje operátorovi sestavy zvolit explicitní hodnoty spuštění a zastavení pro časový rozsah.

### <a name="resource-picker"></a>Výběr prostředku

Typ parametru výběru prostředku vám umožní určit rozsah sestavy na určité typy prostředků. Příkladem předem sestaveného sešitu, který využívá typ výběru prostředku, je sešit s **přehledem selhání** .

![Rozevírací nabídka země](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Ukládání a sdílení sešitů ve vašem týmu

Sešity se ukládají v rámci Application Insights prostředku, a to buď v části **Mé sestavy** , která je pro vás nebo v části **sdílené sestavy** přístupná pro všechny s přístupem k prostředku Application Insights. Chcete-li zobrazit všechny sešity v prostředku, klikněte na panelu akcí na tlačítko **otevřít** .

Sdílení sešitu, který je aktuálně v **Mé sestavy**:

1. Na panelu akcí klikněte na **otevřít** .
2. Klikněte na "..." tlačítko vedle sešitu, který chcete sdílet
3. Klikněte na **přesunout ke sdíleným sestavám**.

Pokud chcete sešit sdílet s odkazem nebo e-mailem, klikněte na panelu akcí na **sdílet** . Mějte na paměti, že příjemci odkazu potřebují k tomuto prostředku přístup v Azure Portal k zobrazení sešitu. K provedení úprav potřebují příjemci pro daný prostředek aspoň oprávnění Přispěvatel.

Chcete-li připnout odkaz na sešit na řídicí panel Azure:

1. Na panelu akcí klikněte na **otevřít** .
2. Klikněte na "..." tlačítko vedle sešitu, který chcete připnout
3. Klikněte na **Připnout na řídicí panel**.

## <a name="contributing-workbook-templates"></a>Přispívat k šablonám sešitu

Vytvořili jste šablonu superového sešitu a chcete ji sdílet s komunitou? Další informace najdete v našem [úložišti GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md).

## <a name="next-steps"></a>Další kroky
- Chcete-li povolit prostředí používání, začněte odesílat [vlastní události](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) nebo [zobrazení stránek](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Pokud jste už odeslali vlastní události nebo zobrazení stránky, prozkoumejte nástroje využití a zjistěte, jak uživatelé používají vaši službu.
    - [Uživatelé, relace, události](../../azure-monitor/app/usage-segmentation.md)
    - [Trychtýře](../../azure-monitor/app/usage-funnels.md)
    - [Uchování](../../azure-monitor/app/usage-retention.md)
    - [Toky uživatele](../../azure-monitor/app/usage-flows.md)
    - [Přidat kontext uživatele](../../azure-monitor/app/usage-send-user-context.md)
