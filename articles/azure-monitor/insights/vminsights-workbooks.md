---
title: Vytváření interaktivních sestav ve službě Azure Monitor pro virtuální počítače s využitím sešitů
description: Zjednodušte složité vytváření sestav pomocí předdefinovaných a vlastních parametrizovaných sešitů pro Azure Monitor pro virtuální počítače.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: a6ab126c3a5b0d2a82b17fac42dcc9e20f6aba3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480449"
---
# <a name="create-interactive-reports-azure-monitor-for-vms-with-workbooks"></a>Vytváření interaktivních sestav ve službě Azure Monitor pro virtuální počítače s využitím sešitů

Sešity kombinují text, [dotazy protokolu](../log-query/query-language.md), metriky a parametry do bohatých interaktivních sestav. Sešity jsou upravitelné všemi ostatními členy týmu, kteří mají přístup ke stejným prostředkům Azure.

Sešity jsou užitečné pro scénáře, jako jsou:

* Zkoumání využití virtuálního počítače, když neznáte metriky zájmu předem: využití procesoru, místo na disku, paměť, síťové závislosti atd. Na rozdíl od jiných nástrojů pro analýzu využití umožňují sešity kombinovat různé druhy vizualizací a analýz, což je činí skvělými pro tento druh průzkumu volného tvaru.
* Vysvětlení týmu, jak si nedávno zřízený virtuální počítač vede, zobrazením metrik pro čítače klíčů a další události protokolu.
* Sdílení výsledků experimentu pro změna velikosti virtuálního počítače s ostatními členy vašeho týmu. Můžete vysvětlit cíle experimentu s textem a pak zobrazit jednotlivé metriky využití a analytické dotazy použité k vyhodnocení experimentu spolu s jasnými popisky pro to, zda byla každá metrika nad nebo pod cílem.
* Nahlášení dopadu výpadku na využití virtuálního počítače, kombinování dat, vysvětlení textu a diskuse o dalších krocích, jak zabránit výpadkům v budoucnu.

Následující tabulka shrnuje sešity, které Azure Monitor pro virtuální počítače obsahuje, abyste mohli začít.

| sešit | Popis | Rozsah |
|----------|-------------|-------|
| Výkon | Poskytuje přizpůsobitelnou verzi našeho zobrazení Seznamu n a grafů v jednom sešitu, který využívá všechny čítače výkonu Analýzy protokolů, které jste povolili.| Ve velkém měřítku |
| Čítače výkonu | Zobrazení grafu Top N v široké sadě čítačů výkonu. | Ve velkém měřítku |
| Připojení | Připojení poskytuje podrobný pohled na příchozí a odchozí připojení z monitorovaných virtuálních počítačů. | Ve velkém měřítku |
| Aktivní porty | Obsahuje seznam procesů, které jsou vázány na porty na monitorovaných virtuálních počítačích a jejich aktivitu ve zvoleném časovém rámci. | Ve velkém měřítku |
| Otevřené porty | Poskytuje počet otevřených portů na monitorovaných virtuálních počítačích a podrobnosti o těchto otevřených portech. | Ve velkém měřítku |
| Neúspěšná připojení | Zobrazte počet neúspěšných připojení na monitorovaných virtuálních počítačích, trend selhání a pokud se procento selhání v průběhu času zvyšuje. | Ve velkém měřítku |
| Zabezpečení a audit | Analýza přenosů protokolu TCP/IP, která hlásí celková připojení, škodlivá připojení, kde se koncové body IP nacházejí globálně.  Chcete-li povolit všechny funkce, budete muset povolit zjišťování zabezpečení. | Ve velkém měřítku |
| Provoz TCP | Hodnocená sestava pro monitorované virtuální počítače a jejich odeslané, přijaté a celkové síťové přenosy v mřížce a zobrazené jako trendová čára. | Ve velkém měřítku |
| Porovnání provozu | Tyto sešity umožňují porovnat trendy síťového provozu pro jeden počítač nebo skupinu počítačů. | Ve velkém měřítku |
| Výkon | Poskytuje přizpůsobitelnou verzi našeho zobrazení výkonu, která využívá všechny čítače výkonu Analýzy protokolů, které jste povolili. | Jeden virtuální počítač | 
| Připojení | Připojení poskytuje podrobné zobrazení příchozích a odchozích připojení z virtuálního počítače. | Jeden virtuální počítač |
 
## <a name="creating-a-new-workbook"></a>Vytvoření nového sešitu

Sešit se skládá z oddílů skládajících se z nezávisle upravitelných grafů, tabulek, textu a vstupních ovládacích prvků. Chcete-li lépe porozumět sešitům, začněme otevřením šablony a projděte si vytvoření vlastního sešitu. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. Vyberte **virtuální počítače**.

3. V seznamu vyberte virtuální počítač.

4. Na stránce Virtuální počítač včásti **Monitorování** vyberte **Přehledy**.

5. Na stránce Přehledy virtuálních počítači vyberte **kartu Výkon** nebo **Mapy** a pak z odkazu na stránce vyberte **Zobrazit sešity.** V rozevíracím seznamu vyberte **Přejít do galerie**.

    ![Snímek obrazovky s rozevíracím seznamem sešitu](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Tím se spustí galerie sešitů s řadou předem sestavených sešitů, které vám pomůžou začít.

7. Vytvořte nový sešit výběrem **možnosti Nový**.

    ![Snímek obrazovky s galerií sešitů](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Úpravy oddílů sešitů

Sešity mají dva režimy: **režim úprav**a **režim čtení**. Při prvním spuštění nového sešitu se otevře v **režimu úprav**. Zobrazuje veškerý obsah sešitu, včetně všech kroků a parametrů, které jsou jinak skryté. **Režim čtení** představuje zjednodušené zobrazení stylu sestavy. Režim čtení umožňuje abstraktní pryč složitost, která šla do vytvoření sestavy, zatímco stále mají základní mechaniky jen pár kliknutí v případě potřeby pro úpravy.

![Ovládací prvky úprav oddílu Sešity Azure Monitor pro virtuální počítače](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Až budete úpravy oddílu hotová, klikněte v levém dolním rohu oddílu na **Hotovo.**

2. Chcete-li vytvořit duplikát oddílu, klepněte na ikonu **Klonovat tento oddíl.** Vytváření duplicitních oddílů je skvělý způsob, jak iterate na dotaz bez ztráty předchozíiterace.

3. Pokud chcete oddíl v sešitu posunout nahoru, klikněte na ikonu **Přesunout nahoru** nebo **Přesunout dolů.**

4. Chcete-li oddíl trvale odebrat, klepněte na ikonu **Odebrat.**

## <a name="adding-text-and-markdown-sections"></a>Přidávání textových oddílů a oddílů Markdown

Přidání nadpisů, vysvětlení a komentářů do sešitů pomáhá přeměnit sadu tabulek a grafů na vyprávění. Textové oddíly v sešitech podporují [syntaxi Markdownu](https://daringfireball.net/projects/markdown/) pro formátování textu, jako jsou nadpisy, tučné písmo, kurzíva a seznamy s odrážkami.

Pokud chcete do sešitu přidat textový oddíl, použijte tlačítko **Přidat text** v dolní části sešitu nebo v dolní části libovolného oddílu.

## <a name="adding-query-sections"></a>Přidávání oddílů dotazů

![Oddíl Dotaz v sešitech](media/vminsights-workbooks/005-workbook-query-section.png)

Pokud chcete do sešitu přidat oddíl dotazu, použijte tlačítko **Přidat dotaz** v dolní části sešitu nebo v dolní části libovolného oddílu.

Části dotazu jsou vysoce flexibilní a lze je použít k zodpovězení otázek, jako jsou:

* Jaké bylo využití procesoru ve stejném časovém období jako zvýšení síťového provozu?
* Jaký byl trend dostupného místa na disku za poslední měsíc?
* Kolik selhání síťového připojení se moje prostředí virtuálního připojení za poslední dva týdny? 

Také nejste omezeni pouze na dotazování z kontextu virtuálního počítače, ze které jste sešit spustili. Můžete dotazovat napříč více virtuálních počítačů, stejně jako Log Analytics pracovníprostory, pokud máte přístup oprávnění k těmto prostředkům.

Chcete-li zahrnout data z jiných pracovních prostorů Analýzy protokolů nebo z konkrétní aplikace Application Insights pomocí identifikátoru **pracovního prostoru.** Další informace o dotazech na příčné zdroje naleznete v [oficiálních pokynech](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Pokročilá nastavení analytického dotazu

Každá část má vlastní pokročilá nastavení, která ![jsou přístupná](media/vminsights-workbooks/006-settings.png) prostřednictvím ikony ovládacích prvků úprav oddílu Sešity, která se nachází vpravo od tlačítka **Přidat parametry.**

![Ovládací prvky úprav oddílu Sešity Azure Monitor pro virtuální počítače](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Vlastní šířka**    | Nastavuje položku libovolnou velikost, takže se na jeden řádek vejde mnoho položek, což vám umožní lépe uspořádat grafy a tabulky do bohatých interaktivních sestav.  |
| **Podmíněně viditelné** | Určete, chcete-li skrýt kroky založené na parametru v režimu čtení. |
| **Export parametru**| Povolte vybranému řádku v mřížce nebo grafu, aby pozdější kroky změnily hodnoty nebo se staly viditelnými.  |
| **Zobrazit dotaz při neúpravách** | Zobrazí dotaz nad grafem nebo tabulkou, i když je v režimu čtení.
| **Zobrazit tlačítko Otevřít v analytice, když neupravujete** | Přidá modrou ikonu Analytics do pravého rohu grafu, aby byl umožněn přístup jedním kliknutím.|

Většina těchto nastavení je poměrně intuitivní, ale pro pochopení **exportu parametru** je lepší prozkoumat sešit, který využívá tuto funkci.

Jeden z předem vytvořených sešitů – **TCP Traffic**, poskytuje informace o metrikách připojení z virtuálního počítači.

První část sešitu je založena na datech dotazu protokolu. Druhá část je také založena na datech dotazu protokolu, ale výběr řádku v první tabulce interaktivně aktualizuje obsah grafů:

![Ovládací prvky úprav oddílu Sešity Azure Monitor pro virtuální počítače](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

Chování je možné pomocí **Při výběru položky exportovat parametr** upřesňující nastavení, které jsou povoleny v dotazu protokolu tabulky.

![Ovládací prvky úprav oddílu Sešity Azure Monitor pro virtuální počítače](media/vminsights-workbooks/009-settings-export.png)

Druhý dotaz protokolu pak použije exportované hodnoty, když je vybrán řádek k vytvoření sady hodnot, které jsou pak použity nadpisem oddílu a grafy. Pokud není vybrán žádný řádek, skryje záhlaví oddílu a grafy. 

Například skrytý parametr v druhé části používá následující odkaz z řádku vybraného v mřížce:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Přidávání oddílů metrik

Části metrik vám poskytují úplný přístup k začlenění dat metrik Azure Monitoru do interaktivních přehledů. V Azure Monitor pro virtuální počítače, předem sestavené sešity obvykle obsahují data analytického dotazu, nikoli metrických dat.  Můžete se rozhodnout vytvořit sešity s metrickými daty, což vám umožní plně využít to nejlepší z obou funkcí na jednom místě. Máte také možnost získat metrická data z prostředků v libovolném předplatných, ke které máte přístup.

Tady je příklad dat virtuálních strojů, která jsou vtahována do sešitu a poskytují vizualizaci výkonu procesoru:

![Ovládací prvky úprav oddílu Sešity Azure Monitor pro virtuální počítače](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Přidávání oddílů parametrů

Parametry sešitu umožňují měnit hodnoty v sešitu, aniž byste museli ručně upravovat části dotazu nebo textu. Tím se odstraní požadavek, že potřebujete porozumět základnímu dotazovacímu jazyku analýzy, a výrazně se rozšíří potenciální cílová skupina sestav založených na sešitu.

Hodnoty parametrů jsou nahrazeny v dotazu, textu nebo jiných parametrech umístěním názvu ``{parameterName}``parametru do složených závorek, například . Názvy parametrů jsou omezeny na podobná pravidla jako identifikátory JavaScriptu, abecední znaky nebo podtržítka následovaná alfanumerickými znaky nebo podtržítky. Například **a1** je povoleno, ale **1a** není povoleno.

Parametry jsou lineární, počínaje horní částí sešitu a stékající dolů do pozdějších kroků.  Parametry deklarované později v sešitu mohou přepsat parametry, které byly deklarovány dříve. To také umožňuje parametry, které používají dotazy pro přístup k hodnotám z parametrů definovaných dříve. V rámci samotného kroku parametru jsou parametry také lineární, zleva doprava, kde parametry vpravo mohou záviset na parametru deklarovaném dříve ve stejném kroku.
 
V současné době jsou podporovány čtyři různé typy parametrů:

|                  |      |
| ---------------- |:-----|
| **Text**    | Umožňuje uživateli upravit textové pole a volitelně můžete zadat dotaz k vyplnění výchozí hodnoty. |
| **Rozevírací seznam** | Umožňuje uživateli vybrat si ze sady hodnot. |
| **Výběr časového rozsahu**| Umožňuje uživateli vybrat si z předdefinované sady hodnot časového rozsahu nebo vybrat z vlastního časového rozsahu.|
| **Výběr zdrojů** | Umožňuje uživateli vybrat si ze zdrojů vybraných pro sešit.|

### <a name="using-a-text-parameter"></a>Použití textového parametru

Hodnota, kterou uživatel zadá v textovém poli, je nahrazena přímo v dotazu bez úniku nebo citace. Pokud je hodnota, kterou potřebujete, řetězec, dotaz by měl mít uvozovky kolem parametru (například **{parameter}'**).

Parametr text umožňuje použít hodnotu v textovém poli kdekoli. Může se jedná o název tabulky, název sloupce, název funkce, operátor atd.  Typ parametru textu má nastavení **Získat výchozí hodnotu z analytického dotazu**, která autorovi sešitu umožňuje použít dotaz k naplnění výchozí hodnoty pro toto textové pole.

Při použití výchozí hodnoty z dotazu protokolu se jako výchozí hodnota používá pouze první hodnota prvního řádku (řádek 0, sloupec 0). Proto se doporučuje omezit dotaz vrátit pouze jeden řádek a jeden sloupec. Všechna ostatní data vrácená dotazem jsou ignorována. 

Bez ohledu na hodnotu dotazvrátí bude nahrazen přímo bez úniku nebo citace. Pokud dotaz vrátí žádné řádky, výsledkem parametru je buď prázdný řetězec (pokud parametr není vyžadován) nebo undefined (pokud je parametr požadován).

### <a name="using-a-drop-down"></a>Použití rozevíracího

Typ rozevíracího parametru umožňuje vytvořit ovládací prvek rozevíracího souboru, který umožňuje výběr jedné nebo více hodnot.

Rozevírací seznam je naplněn dotazem protokolu nebo JSON. Pokud dotaz vrátí jeden sloupec, hodnoty v tomto sloupci jsou hodnota i popisek v rozevíracím ovládacím prvku. Pokud dotaz vrátí dva sloupce, první sloupec je hodnota a druhý sloupec je popisek zobrazený v rozevíracím seznamu. Pokud dotaz vrátí tři sloupce, třetí sloupec se používá k označení výchozí výběr v tomto rozevíracím seznam. Tento sloupec může být libovolný typ, ale nejjednodušší je použití bool nebo číselné typy, kde 0 je false a 1 je true.

Pokud je sloupec typ řetězce, null/prázdný řetězec je považován za false a všechny ostatní hodnoty je považován za true. Pro rozevírací seznamy s jedním výběrem se jako výchozí výběr použije první hodnota s hodnotou true.  Pro více rozevíracích seznamů výběru jsou jako výchozí vybraná sada použity všechny hodnoty se skutečnou hodnotou. Položky v rozevíracím souboru jsou zobrazeny v libovolném pořadí, ve které dotaz vrátil řádky. 

Podívejme se na parametry, které jsou k dispozici v sestavě Přehled připojení. Klepněte na symbol úprav vedle **položky Směr**.

![Ovládací prvky úprav oddílu Sešity Azure Monitor pro virtuální počítače](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Tím se spustí položka nabídky **Upravit parametry.**

![Ovládací prvky úprav oddílu Sešity Azure Monitor pro virtuální počítače](media/vminsights-workbooks/012-workbook-edit-parameter.png)

JSON umožňuje generovat libovolnou tabulku naplněnou obsahem. Například následující JSON generuje dvě hodnoty v rozevíracím jsouvu:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Vhodnějším příkladem je použití rozevíracího souboru k výběru ze sady čítačů výkonu podle názvu:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

Dotaz zobrazí výsledky následujícím způsobem:

![Rozevírací nabídku čítačů Perf](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Rozevírací seznamy jsou neuvěřitelně výkonné nástroje pro přizpůsobení a vytváření interaktivních sestav.

### <a name="time-range-parameters"></a>Parametry časového rozsahu

Zatímco můžete vytvořit vlastní parametr časového rozsahu pomocí typu parametru rozevíracího pole, můžete také použít typ parametru časového rozsahu out-of-box, pokud nepotřebujete stejný stupeň flexibility. 

Typy parametrů časového rozsahu mají 15 výchozírozsahy, které přejdou od pěti minut do posledních 90 dnů. K dispozici je také možnost povolit výběr vlastního časového rozsahu, která umožňuje operátoru sestavy zvolit explicitní počáteční a stopové hodnoty pro časový rozsah.

### <a name="resource-picker"></a>Výběr zdrojů

Typ parametru výběru prostředků umožňuje určit určité typy prostředků. Příkladem předem sestaveného sešitu, který využívá typ výběru prostředků, je sešit **Výkonu.**

![Rozevírací řešení pracovních prostorů](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Ukládání sešitů a jejich sdílení v rámci týmu

Sešity se ukládají v pracovním prostoru Analýzy protokolů nebo v prostředku virtuálního počítače v závislosti na tom, jak přistupujete ke galerii sešitů. Sešit lze uložit do části **Moje sestavy,** která je pro vás soukromá, nebo v části **Sdílené sestavy,** která je přístupná všem uživatelům s přístupem k prostředku. Chcete-li zobrazit všechny sešity v zdroji, klepněte na tlačítko **Otevřít** na panelu akcí.

Sdílení sešitu, který je aktuálně v **seznamu Moje sestavy**:

1. Na panelu akcí klikněte na **Otevřít.**
2. Klikněte na "..." vedle sešitu, který chcete sdílet
3. Klikněte na **Přesunout do sdílených sestav**.

Pokud chcete sešit sdílet s odkazem nebo e-mailem, klikněte na panelu akcí na **Sdílet.** Mějte na paměti, že příjemci odkazu potřebují přístup k tomuto prostředku na webu Azure Portal, aby mohly sešit zobrazit. K úpravám potřebují příjemci alespoň oprávnění přispěvatele pro prostředek.

Připnutí odkazu na sešit na řídicí panel Azure:

1. Na panelu akcí klikněte na **Otevřít.**
2. Klikněte na "..." vedle sešitu, který chcete připnout
3. Klikněte **na Připnout na řídicí panel**.

## <a name="next-steps"></a>Další kroky

- Informace o omezeních a celkovém výkonu virtuálních počítačích najdete [v tématu Zobrazení výkonu virtuálních počítačích Azure](vminsights-performance.md).

- Informace o zjištěných závislostech aplikací najdete [v tématu Zobrazení mapy Azure Monitor for VM .](vminsights-maps.md)
