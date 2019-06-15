---
title: Vytváření interaktivních sestav se sešity Azure Monitor | Dokumentace Microsoftu
description: Zjednodušte komplexní vytváření sestav pomocí předdefinované a vlastní parametry sešity pro monitorování Azure pro virtuální počítače.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2019
ms.author: magoedte
ms.openlocfilehash: 90c236347380bb5d5e51db56d0f431d2659a7258
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61387060"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Vytváření interaktivních sestav se sešity Azure Monitor

Sešity kombinovat text, [protokolu dotazy](../log-query/query-language.md), metriky a parametrů do bohaté interaktivní sestavy. Sešity se upravovat jiným členem týmu, kteří mají přístup ke stejným prostředkům Azure.

Sešity jsou užitečné pro scénáře, jako je například:

* Zkoumání využití virtuálního počítače při předem neznáte metriky, které vás zajímají: Využití procesoru, místo na disku, paměť, síťové závislosti, atd. Na rozdíl od jiných nástrojů pro analýzu využití pracovní sešity umožňují kombinovat více typů vizualizace a analýzy, díky kterým jsou skvělé pro tento druh zkoumání volném.
* Ke svému týmu vysvětlující, jaký je výkon virtuálního počítače s nedávno připraveným zobrazením metriky klíčových čítačů a další události protokolu.
* Sdílení výsledků pokusu se změny velikosti virtuálního počítače s ostatními členy týmu. Vysvětlete cíle pro experiment s textem a potom ukazují jednotlivých využití dotazy na metriky a analýzy k vyhodnocení, experiment, spolu s vymazat značek pro Určuje, zda jednotlivé metriky se nad nebo pod cíl.
* Na používání vašeho virtuálního počítače, kombinování dat, text vysvětlení a diskuzi o dalších krocích v budoucnosti zabránilo výpadkům Reporting dopadu kvůli výpadku.

Azure Monitor pro virtuální počítače obsahuje několik sešitů, které vám pomůžou začít a následující tabulka shrnuje jejich.

| Sešit | Popis | Scope |
|----------|-------------|-------|
| Výkon | Poskytuje přizpůsobitelné verze našich nejlepších N a zobrazení grafů v jedné sešitu, který využívá všechny čítače výkonu Log Analytics, které jste povolili.| Ve velkém měřítku |
| Čítače výkonu | Zobrazení grafu N nejlepších napříč celou sadu čítačů výkonu. | Ve velkém měřítku |
| Připojení | Připojení poskytuje podrobný přehled toho, příchozí a odchozí připojení z monitorovaných virtuálních počítačů. | Ve velkém měřítku |
| Aktivní porty | Poskytuje seznam procesů, které mají vázány na porty na monitorovaných virtuálních počítačů a jejich aktivity ve vybraném časovém rámci. | Ve velkém měřítku |
| Otevřené porty | Obsahuje počet portů, které otevřete na monitorovaných virtuálních počítačích a informace o těch otevřít porty. | Ve velkém měřítku |
| Neúspěšná připojení | Zobrazí počet selhání připojení na monitorovaných virtuálních počítačů, trend selhání, a pokud je procento chyb v průběhu času zvětšuje. | Ve velkém měřítku |
| Zabezpečení a audit | Analýza provozu protokolu TCP/IP, který bude hlásit celkový připojení, škodlivý připojení, kde koncovými body IP jsou umístěné globálně.  Pokud chcete povolit všechny funkce, je potřeba povolit zjišťování zabezpečení. | Ve velkém měřítku |
| Provoz TCP | Seřazený sestavy pro monitorovaných virtuálních počítačů a jejich odeslání, přijatý a celkový počet sítí provoz do mřížky a zobrazit jako trendovou linii. | Ve velkém měřítku |
| Porovnání provozu | Tento pracovní sešity umožňuje porovnat trendy provoz sítě pro jeden počítač nebo skupinu počítačů. | Ve velkém měřítku |
| Výkon | Poskytuje přizpůsobitelné verzi naší zobrazení výkonu, který využívá všechny čítače výkonu Log Analytics, které jste povolili. | Jeden virtuální počítač | 
| Připojení | Připojení poskytuje podrobný přehled toho, příchozí a odchozí připojení z vašeho virtuálního počítače. | Jeden virtuální počítač |
 
## <a name="starting-with-a-template-or-saved-workbook"></a>Od verze šablony nebo uložené sešitu

Sešit je tvořené oddíly skládající se z nezávisle upravovat grafy, tabulky, text a vstupní ovládací prvky. Abyste lépe pochopili, pracovní sešity, můžeme začít tak, že otevřete šablonu a provede procesem vytvoření vlastní sešitu. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **virtuálních počítačů**.

3. V seznamu vyberte virtuální počítač.

4. Na stránce virtuální počítač v **monitorování** vyberte **Insights (preview)** .

5. Na stránce přehledu virtuálního počítače, vyberte **výkonu** nebo **mapy** kartu a potom vyberte **zobrazení sešitů** z odkazu na stránce. 

    ![Snímek obrazovky navigace do sešitů](media/vminsights-workbooks/workbook-option-01.png)

6. V rozevíracím seznamu vyberte **přejít do Galerie** v dolní části seznamu.

    ![Snímek obrazovky sešitu rozevíracího seznamu](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Spustí Galerie sešitu s celou řadou předem připravené pracovní sešity k vám pomůžou začít.

7. Začneme **výchozí šablona**, který se nachází pod nadpisem **úvodní**.

    ![Snímek obrazovky Galerie sešitu](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Úpravy části sešitu

Sešity mají dva režimy: **režimu úprav**, a **neupravuje**. Při prvním spuštění výchozí šablony sešit se otevře v **režimu úprav**. Zobrazí veškerý obsah sešitu včetně všech kroků a parametrů, které jsou jinak skryté. **Režim čtení** představuje styl zobrazení zjednodušené sestavy. Režim čtení umožňuje abstrakci složitost, která jsou součástí vytváření sestav přitom stále má základní mechanismus jenom několika kliknutí v případě potřeby pro úpravy.

![Azure Monitor pro virtuální počítače sešity oddíl úpravy ovládacích prvků](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Po dokončení úprav oddíl, klikněte na tlačítko **provést úpravy** v levém dolním oddílu.

2. K vytvoření duplicitní oddíl, klikněte na tlačítko **klonovat tento oddíl** ikonu. Vytvoření duplicitní části je skvělý způsob, jak iterovat dotazu bez ztráty předchozími iteracemi.

3. Posunout nahoru oddíl v sešitu, klikněte na tlačítko **nahoru** nebo **přesunout dolů** ikonu.

4. Pokud chcete trvale odebrat oddíl, klikněte na tlačítko **odebrat** ikonu.

## <a name="adding-text-and-markdown-sections"></a>Přidání textu a části Markdownu

Přidání záhlaví, vysvětlení a poznámky k sešity pomůže proměnit sadu tabulek a grafů komentáře. Části textu v sešitech technické podpory [syntaxe Markdownu](https://daringfireball.net/projects/markdown/) pro formátování textu, jako jsou nadpisy, tučné, kurzíva a seznamy s odrážkami.

Chcete-li přidat do sešitu část textu, použijte **přidat text** tlačítko v dolní části sešitu nebo v dolní části libovolné části.

## <a name="adding-query-sections"></a>Přidání dotazu oddílů

![V sešitech části dotazu](media/vminsights-workbooks/005-workbook-query-section.png)

Chcete-li přidat do sešitu část dotazu, použijte **přidat dotaz** tlačítko v dolní části sešitu nebo v dolní části libovolné části.

Dotaz oddíly jsou vysoce flexibilní a umožňuje zodpovědět dotazy jako:

* Jak se Moje využití procesoru během stejné období jako zvýšení provozu v síti?
* Jak se trend v volného místa na disku za poslední měsíc?
* Za poslední dva týdny kolik selhání připojení k síti setkali Můj virtuální počítač? 

Můžete také nejsou pouze omezeni na dotazování z kontextu virtuální počítač spustí ze sešitu. Dotazování napříč více virtuálních počítačů a zároveň pracovních prostorů Log Analytics, jako mají oprávnění přístupu k těmto prostředkům.

Chcete-li zahrnout data z jiných pracovních prostorech Log Analytics nebo z konkrétního pomocí aplikace Application Insights **pracovní prostor** identifikátor. Další informace o dotazech napříč prostředky, najdete v tématu [oficiální pokyny](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Upřesňující nastavení analytického dotazu

Každá část má svůj vlastní upřesňující nastavení, které jsou přístupné přes nastavení ![sešity části ovládací prvky pro úpravy](media/vminsights-workbooks/006-settings.png) ikona se nachází na pravé straně **přidat parametry** tlačítko.

![Azure Monitor pro virtuální počítače sešity oddíl úpravy ovládacích prvků](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Vlastní šířky**    | Díky položku libovolné velikosti, takže můžete přizpůsobit mnoho položek na jeden řádek, abyste mohli lépe uspořádat grafů a tabulek do bohaté interaktivní sestavy.  |
| **Jako podmíněně viditelnou** | Zadejte skryjete kroky na základě parametru v režimu čtení. |
| **Exportovat parametr**| Povolit vybraný řádek v mřížce nebo grafu způsobit pozdější kroky změní hodnoty nebo viditelná.  |
| **Zobrazit dotaz, když neprobíhají úpravy** | Zobrazí dotaz nad tento graf nebo tabulku, i když v režimu čtení.
| **Zobrazit otevřít v analytics tlačítko, pokud nepoužíváte režim úprav** | Přidá modrou ikonou ve tvaru Analytics pravém horním rohu grafu umožňující přístup jedním kliknutím.|

Většinu těchto nastavení jsou poměrně intuitivní, ale pro pochopení **exportovat parametr** je lepší prozkoumat sešitu která používá tuto funkci.

Jeden z předem připravené pracovní sešity - **provoz TCP**, obsahuje informace týkající se metrik připojení z virtuálního počítače.

První část sešitu podle dat protokolu dotazu. Druhá část je taky založený na protokolu dotaz na data, ale výběru řádku v první tabulce interaktivně aktualizuje obsah grafy:

![Azure Monitor pro virtuální počítače sešity oddíl úpravy ovládacích prvků](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

Chování je možné provádět prostřednictvím použití **při výběru položky exportovat parametr** upřesňující nastavení, které jsou povolené v tabulky protokolu dotazu.

![Azure Monitor pro virtuální počítače sešity oddíl úpravy ovládacích prvků](media/vminsights-workbooks/009-settings-export.png)

Druhý dotaz protokolu potom využívá exportovaných hodnot při výběru řádku k vytvoření sadu hodnot, které jsou pak používány nadpis oddílu a grafy. Pokud není vybrán žádný řádek, skryje nadpis oddílu a grafy. 

Skrytý parametr v druhé části například používá následující odkaz z Určuje vybraný řádek v tabulce:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Přidání metriky oddílů

Oddíly metrik získáte plný přístup k začlenění dat metrik Azure monitoru interaktivní sestavy. Ve službě Azure Monitor pro virtuální počítače bude předem připravené pracovní sešity obvykle obsahují analytické dotazy na data, nikoli data metriky.  Můžete k vytváření sešitů s daty metrik, abyste mohli plně využít to nejlepší z obou funkcí vše na jednom místě. Máte také možnost, aby se načetla data metriky z prostředků ve všech předplatných, ke kterým máte přístup k.

Tady je příklad dat virtuálních počítačů právě načetli do sešitu poskytnout vizualizaci mřížky výkon procesoru:

![Azure Monitor pro virtuální počítače sešity oddíl úpravy ovládacích prvků](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Přidání parametru oddílů

Sešit parametry umožňují změnit bez nutnosti ručně upravit části dotazu nebo textové hodnoty v sešitu. To eliminuje nutnost by bylo nutné pochopit základní dotazovací jazyk analytics a značně rozšíří potenciální cílovou skupinu na základě sešitu generování sestav.

Hodnoty parametrů se nahradí v dotazu, text nebo jiné části parametr vložením název parametru ve složených závorkách, jako je ``{parameterName}``. Názvy parametrů jsou omezené na podobnými pravidly jako identifikátory jazyka JavaScript, abecední znaky nebo podtržítka, za nímž následuje alfanumerické znaky nebo podtržítka. Například **a1** může, ale **1a** není povolený.

Parametry jsou lineárně závislé, od horní části sešitu a odesílaných do dalších krocích.  Parametry deklarovanými jako později v sešitu můžete přepsat parametry, které byly dříve deklarovány. Díky tomu taky parametry, které používají dotazy pro přístup k hodnoty z parametrů definovaných výše. V rámci kroku parametr na samotný parametry jsou také lineární, zleva doprava, kde parametry na pravé straně může záviset na parametr deklarovaný dříve v tomto kroku stejné.
 
Existují čtyři různé typy parametrů, které jsou aktuálně podporovány:

|                  |      |
| ---------------- |:-----|
| **text**    | Umožňuje uživateli upravit textové pole a volitelně může zadat dotaz vyplnit výchozí hodnotu. |
| **Rozevírací seznam** | Umožňuje uživateli zvolit ze sady hodnot. |
| **Výběr časového rozsahu**| Umožňuje uživateli vybrat z předdefinovanou sadu hodnot rozsahu času, nebo si můžete vybrat z vlastní časový rozsah.|
| **Výběr prostředku** | Umožňuje uživateli zvolit ze sešitu počet vybraných prostředků.|

### <a name="using-a-text-parameter"></a>Pomocí parametru text

Hodnota, která nahrazuje uživatelské typy v textovém poli přímo v dotazu bez uvozovací znaky nebo uvozovky u. Pokud je hodnota, je třeba řetězec, dotaz by měl mít uvozovky kolem parametru (jako je **'{parameter}'** ).

Text parametr povoluje hodnotu v textovém poli, který se má použít kdekoli. Může být název tabulky, název sloupce, název funkce, operátor, atd.  Typ parametru text má nastavení **získat výchozí hodnotu z analytického dotazu**, který umožňuje Autor sešitu použít dotaz k naplnění výchozí hodnotu pro toto textové pole.

Při použití výchozí hodnoty z dotazu protokolu, pouze první hodnotu na prvním řádku (0 se sloupci 0) se používá jako výchozí hodnotu. Proto se doporučuje omezit dotaz vrátit jen jeden řádek a jeden sloupec. Žádná další data vrácená dotazem se ignoruje. 

Všechno, co dotaz se vrátí hodnota se nahradí přímo bez uvozovací znaky nebo uvozovky u. Pokud dotaz vrací žádné řádky, výsledek parametru je buď prázdný řetězec (Pokud parametr není povinný) nebo nedefinovaný (Pokud je parametr povinný).

### <a name="using-a-drop-down"></a>Pomocí rozevíracího seznamu

Typ parametru rozevíracího seznamu umožňuje vytvořit ovládací prvek rozevírací seznam umožňuje výběr z jednoho nebo více hodnot.

Rozevírací seznam je vyplněn dotaz protokolu nebo JSON. Pokud dotaz vrací jeden sloupec, jsou hodnoty v tomto sloupci hodnotu a popisek v ovládacím prvku rozevíracího seznamu. Pokud dotaz vrací dva sloupce, na první sloupec je hodnota a druhý sloupec je popisek zobrazený v rozevíracím seznamu. Pokud dotaz vrací tři sloupce, třetí sloupec se používá k označení výchozí výběr v rozevíracím seznamu. V tomto sloupci může být libovolný typ, ale nejjednodušší je použití bool nebo číselné typy, kde 0 je NEPRAVDA a 1 je PRAVDA.

Pokud je ve sloupci Typ řetězce, hodnotu null nebo prázdný řetězec je považován za hodnotu false a jakoukoli jinou hodnotu je považován za hodnotu true. Pro jeden výběr rozevírací seznamy první hodnota s hodnotou true slouží jako výchozí výběr.  Pro více výběr rozevírací seznamy všechny hodnoty true hodnotou slouží jako výchozí vybraná sada. Položky v rozevíracím seznamu jsou uvedeny v libovolném pořadí dotaz vrátil řádků. 

Podívejme se na parametry, které jsou k dispozici v sestavě přehled připojení. Kliknutím na symbol upravit vedle **směr**.

![Azure Monitor pro virtuální počítače sešity oddíl úpravy ovládacích prvků](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Tím se spustí **upravit parametr** položky nabídky.

![Azure Monitor pro virtuální počítače sešity oddíl úpravy ovládacích prvků](media/vminsights-workbooks/012-workbook-edit-parameter.png)

JSON umožňuje generovat libovolné tabulku s obsahem. Například následující kód JSON generuje dvě hodnoty v rozevíracího seznamu:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Hodí víc příklad používá rozevírací seznam pro výběr ze sady čítačů výkonu podle názvu:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

Výsledky se zobrazí dotaz následujícím způsobem:

![Rozevírací seznam čítačů výkonu](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Rozevírací seznamy jsou velmi výkonné nástroje pro přizpůsobení a vytváření interaktivních sestav.

### <a name="time-range-parameters"></a>Parametry rozsah času

Když provedete vlastní parametr vlastní časový rozsah přes typ parametru rozevírací seznam, můžete také typ parametru out-of-box časového rozsahu Pokud už nebudete potřebovat stejnou úroveň flexibilitu. 

Typy parametrů rozsah času mají 15 výchozí rozsahy, které přejít z pěti minut na poslední 90 dnů. Je také možnost povolit výběr vlastního časového rozsahu, který umožňuje operátorovi sestavy vyberte explicitní spuštění a zastavení hodnoty pro časový rozsah.

### <a name="resource-picker"></a>Výběr prostředku

Typ prostředku pro výběr parametru dává možnost k určení oboru sestavy na určité typy prostředků. Je například předem připravených sešit, který využívá výběr typu prostředku **výkonu** sešitu.

![Rozevírací seznam pracovních prostorů](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Ukládání a sdílení sešitů s týmem

Sešity jsou uloženy v rámci pracovního prostoru Log Analytics nebo prostředek virtuálního počítače, v závislosti na tom, jak získat přístup k galerii sešity. Sešit je možné ukládat do **Moje sestavy** oddíl, který je pro vás nebo v privátní **sdílené sestavy** oddíl, který je přístupné všem uživatelům přístup k prostředku. Chcete-li zobrazit všechny soubory v prostředku, klikněte na tlačítko **otevřít** tlačítko na panelu akcí.

Sdílet sešit, který je v současné době **Moje sestavy**:

1. Klikněte na tlačítko **otevřít** na panelu akcí
2. Klikněte na tlačítko "..." vedle sešit, který chcete sdílet
3. Klikněte na tlačítko **přesunout do sdílených sestav**.

Chcete-li sdílet sešit s odkazem nebo e-mailem, klikněte na tlačítko **sdílet** na panelu akcí. Uvědomte si, že příjemci odkazu potřebují přístup k tomuto prostředku na webu Azure Portal k zobrazení tohoto sešitu. K provádění úprav, příjemci potřebovat aspoň oprávnění přispěvatele pro prostředek.

Pokud chcete připnout odkaz k sešitu na řídicí panel Azure:

1. Klikněte na tlačítko **otevřít** na panelu akcí
2. Klikněte na tlačítko "..." vedle sešit, který chcete připnout
3. Klikněte na tlačítko **připnout na řídicí panel**.

## <a name="next-steps"></a>Další postup
Další informace o použití funkce stavu, najdete v článku [zobrazení stavu virtuálních počítačů Azure](vminsights-health.md), nebo chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md). 