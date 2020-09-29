---
title: Vytváření interaktivních sestav ve službě Azure Monitor pro virtuální počítače s využitím sešitů
description: Zjednodušte vytváření složitých sestav s předdefinovanými a vlastními parametrizovanými sešity pro Azure Monitor pro virtuální počítače.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 8ae8c633bdfca72d6e383715cac9b1bcdddd9d17
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449795"
---
# <a name="create-interactive-reports-azure-monitor-for-vms-with-workbooks"></a>Vytváření interaktivních sestav ve službě Azure Monitor pro virtuální počítače s využitím sešitů

Sešity kombinují text, [dotazy protokolů](../log-query/query-language.md), metriky a parametry do propracovaných interaktivních sestav. Sešity mohou upravovat všichni ostatní členové týmu, kteří mají přístup ke stejným prostředkům Azure.

Sešity jsou užitečné pro scénáře, jako například:

* Prozkoumejte využití virtuálního počítače, když neznáte metriky, které vás zajímají předem: využití procesoru, místo na disku, paměť, závislosti sítě atd. Na rozdíl od jiných analytických nástrojů se vám sešity můžou kombinovat s několika různými typy vizualizací a analýz, takže jsou skvělé pro tento druh průzkumu volného tvaru.
* Zobrazením metrik pro klíčové čítače a další události protokolu Vysvětlete vašemu týmu, jak je prováděn nedávno zřízený virtuální počítač.
* Sdílení výsledků experimentu změny velikosti virtuálního počítače s ostatními členy týmu. Můžete vysvětlit cíle experimentu s textem a pak zobrazit jednotlivé metriky využití a analytické dotazy použité k vyhodnocení experimentu spolu s jasnými voláními pro skutečnost, zda byla každá metrika nad nebo pod cílovou.
* Oznamujeme dopad výpadku při používání vašeho virtuálního počítače, kombinování dat, vysvětlení textu a diskuzi o dalších krocích, aby se předešlo výpadkům v budoucnosti.

Následující tabulka shrnuje sešity, které Azure Monitor pro virtuální počítače obsahuje, abyste mohli začít.

| sešit | Popis | Obor |
|----------|-------------|-------|
| Výkon | Poskytuje přizpůsobitelnou verzi našeho horního zobrazení seznamu a grafů v jednom sešitu, který využívá všechny čítače výkonu Log Analytics, které jste povolili.| Ve velkém měřítku |
| Čítače výkonu | Zobrazení grafu na nejvyšší úrovni napříč celou sadou čítačů výkonu. | Ve velkém měřítku |
| Připojení | Připojení poskytují podrobné zobrazení příchozích a odchozích připojení z monitorovaných virtuálních počítačů. | Ve velkém měřítku |
| Aktivní porty | Obsahuje seznam procesů, které jsou svázané s porty na monitorovaných virtuálních počítačích a jejich aktivitou ve zvoleném časovém období. | Ve velkém měřítku |
| Otevřené porty | Poskytuje počet otevřených portů na monitorovaných virtuálních počítačích a podrobnosti o těchto otevřených portech. | Ve velkém měřítku |
| Neúspěšná připojení | Zobrazit počet neúspěšných připojení na monitorovaných virtuálních počítačích, trend selhání a v případě, že procento chyb roste v čase. | Ve velkém měřítku |
| Zabezpečení a audit | Analýza provozu protokolu TCP/IP, která hlásí celkový počet připojení, škodlivá připojení, kde se koncové body IP nacházejí globálně.  Pokud chcete povolit všechny funkce, budete muset povolit detekci zabezpečení. | Ve velkém měřítku |
| Provoz TCP | Seřazená sestava pro vaše monitorované virtuální počítače a jejich odeslané, přijímané a celkové síťové přenosy v mřížce a zobrazené jako Trendová čára. | Ve velkém měřítku |
| Porovnání provozu | V těchto sešitech můžete porovnávat trendy síťových přenosů pro jeden počítač nebo skupinu počítačů. | Ve velkém měřítku |
| Výkon | Poskytuje přizpůsobitelnou verzi našeho zobrazení výkonu, která využívá všechny čítače výkonu Log Analytics, které jste povolili. | Jeden virtuální počítač | 
| Připojení | Připojení poskytují podrobné zobrazení příchozích a odchozích připojení z virtuálního počítače. | Jeden virtuální počítač |
 
## <a name="creating-a-new-workbook"></a>Vytváření nového sešitu

Sešit je tvořen oddíly, které se skládají z nezávisle upravitelných grafů, tabulek, textových a vstupních ovládacích prvků. Abychom lépe porozuměli sešitům, začněte tím, že otevřete šablonu a projdeme vytvořením vlastního sešitu. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **Virtual Machines**.

3. V seznamu vyberte virtuální počítač.

4. Na stránce virtuální počítač v části **monitorování** vyberte **přehledy**.

5. Na stránce s přehledy virtuálních počítačů vyberte kartu **výkon** nebo **mapy** a pak na stránce klikněte na odkaz **Zobrazit sešity** . V rozevíracím seznamu vyberte **Přejít na galerii**.

    ![Snímek obrazovky s rozevíracím seznamem sešitu](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Tím se spustí Galerie sešitů s několika předem sestavenými sešity, které vám pomůžou začít.

7. Kliknutím na **Nový**vytvořte nový sešit.

    ![Snímek obrazovky Galerie sešitů](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Úpravy oddílů sešitů

Sešity mají dva režimy: **režim úprav**a **režim čtení**. Když se poprvé spustí nový sešit, otevře se v **režimu úprav**. Zobrazuje celý obsah sešitu včetně všech kroků a parametrů, které jsou jinak skryté. **Režim čtení** prezentuje zjednodušené zobrazení stylů sestav. Režim čtení vám umožňuje vyříznout složitost, která se stala vytvořením sestavy, zatímco pořád má podkladovou mechaniku jenom pár kliknutí v případě potřeby pro úpravy.

![Snímek obrazovky oddílu Virtual Machines sešitu v Azure Monitor zobrazení nového sešitu v režimu úprav s zvýrazněnými ovládacími prvky pro úpravy](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Po dokončení úprav oddílu klikněte v levém dolním rohu oddílu na **hotové úpravy** .

2. Chcete-li vytvořit duplikát oddílu, klikněte na ikonu **klonovat tuto část** . Vytváření duplicitních oddílů je skvělým způsobem, jak iterovat na dotaz, aniž by došlo ke ztrátě předchozích iterací.

3. Chcete-li v sešitu přesunout oddíl nahoru, klikněte na ikonu **Přesunout nahoru** nebo **Přesunout dolů** .

4. Pokud chcete oddíl trvale odebrat, klikněte na ikonu **Odebrat** .

## <a name="adding-text-and-markdown-sections"></a>Přidávání textových oddílů a oddílů Markdown

Přidávání nadpisů, vysvětlení a komentářů k vašim sešitům usnadňuje vytvoření sady tabulek a grafů do mluveného komentáře. Textové oddíly v sešitech podporují [syntaxi Markdownu](https://daringfireball.net/projects/markdown/) pro formátování textu, jako jsou nadpisy, tučné písmo, kurzíva a seznamy s odrážkami.

Chcete-li přidat textový oddíl do sešitu, použijte tlačítko **Přidat text** v dolní části sešitu nebo dolní část libovolné části.

## <a name="adding-query-sections"></a>Přidávání oddílů dotazů

![Část dotazu v sešitech](media/vminsights-workbooks/005-workbook-query-section.png)

Chcete-li přidat do sešitu oddíl dotazu, použijte tlačítko **Přidat dotaz** ve spodní části sešitu nebo v dolní části části.

Oddíly dotazů jsou vysoce flexibilní a dají se použít k zodpovězení otázek, jako je:

* Jak bylo využití CPU ve stejném časovém období jako zvýšení síťového provozu?
* Jaký byl trend dostupného místa na disku za poslední měsíc?
* Kolik chyb připojení k síti mělo za poslední dva týdny moje prostředí pro virtuální počítače? 

Nebudete se také omezovat jenom na dotazování z kontextu virtuálního počítače, ze kterého jste sešit spustili. Můžete zadávat dotazy na více virtuálních počítačů a také Log Analytics pracovní prostory, pokud máte přístupová oprávnění k těmto prostředkům.

Chcete-li zahrnout data z jiných Log Analytics pracovních prostorů nebo z konkrétní Application Insights aplikace pomocí identifikátoru **pracovního prostoru** . Další informace o dotazech mezi prostředky najdete v [oficiálních pokynech](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Pokročilé nastavení analytického dotazu

Každá část má vlastní Rozšířená nastavení, která jsou přístupná prostřednictvím ![ ikony pro úpravy oddílu sešity nastavení ](media/vminsights-workbooks/006-settings.png) umístěné napravo od tlačítka **přidat parametry** .

![Snímek obrazovky dialogového okna Upřesnit nastavení v části sešit Virtual Machines Azure Monitor. Ikona, která otevře dialogové okno, je zvýrazněna.](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Vlastní šířka**    | Provede položku v libovolné velikosti, takže můžete umístit mnoho položek na jeden řádek, což vám umožní lépe uspořádat grafy a tabulky do propracovaných interaktivních sestav.  |
| **Podmíněně viditelné** | Určuje, že se mají skrýt kroky na základě parametru v režimu čtení. |
| **Exportovat parametr**| Umožňuje vybranému řádku v mřížce nebo grafu způsobit pozdější postup změny hodnot nebo zobrazení viditelných položek.  |
| **Zobrazit dotaz, pokud se neupravuje** | Zobrazí dotaz nad grafem nebo tabulkou, a to i v režimu čtení.
| **Při úpravách zobrazit tlačítko otevřít v analýze** | Přidá ikonu Blue Analytics do pravého horního rohu grafu, aby bylo možné přístup jedním kliknutím.|

Většina těchto nastavení je poměrně intuitivní, ale pro pochopení **exportu parametru** je lepší prozkoumávat sešit, který tuto funkci využívá.

Jeden z předem připravených sešitů – **přenos TCP**poskytuje informace o metrikách připojení z virtuálního počítače.

První část sešitu je založena na datech dotazů protokolu. Druhá část je také založená na datech dotazů protokolu, ale výběr řádku v první tabulce bude interaktivně aktualizovat obsah grafů:

![Snímek obrazovky oddílu Virtual Machines v Azure Monitor zobrazující předem sestavený provoz TCP v sešitu.](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

Chování je možné prostřednictvím použití **při výběru položky, export** upřesňujících nastavení parametrů, které jsou povoleny v dotazu protokolu tabulky.

![Snímek obrazovky dialogového okna Upřesnit nastavení pro sešit Virtual Machines s možností "když je položka vybrána, možnost exportovat parametr".](media/vminsights-workbooks/009-settings-export.png)

Druhý dotaz protokolu pak použije exportované hodnoty, když se vybere řádek, aby se vytvořila sada hodnot, která se pak používá v záhlaví oddílu a grafech. Pokud není vybraný žádný řádek, skryje nadpis a grafy oddílu. 

Například skrytý parametr v druhé části používá následující odkaz z řádku vybraného v mřížce:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Přidávání oddílů metrik

Oddíly metrik poskytují úplný přístup, který umožňuje začlenit do interaktivních sestav Azure Monitor data metrik. V Azure Monitor pro virtuální počítače budou předem připravené sešity obvykle obsahovat analytická data dotazů, nikoli data metriky.  Můžete se rozhodnout vytvořit sešity s daty metriky, abyste mohli plně využít výhod obou funkcí na jednom místě. Máte také možnost přijímat data metrik z prostředků v libovolném z předplatných, ke kterým máte přístup.

Tady je příklad dat virtuálního počítače, která jsou načítána do sešitu, aby bylo možné vytvořit vizualizaci mřížky výkonu procesoru:

![Snímek obrazovky s oddílem metriky v sešitu virtuálního počítače v Azure Monitor. Výkon procesoru pro každý virtuální počítač se zobrazuje graficky.](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Přidávání oddílů parametrů

Parametry sešitu umožňují změnit hodnoty v sešitu bez nutnosti ručně upravovat oddíly dotazu nebo textu. Tím se eliminuje nutnost potřebovat porozumět základnímu dotazovacímu jazyku a významně rozšířit potenciální cílovou skupinu vytváření sestav na základě sešitu.

Hodnoty parametrů jsou nahrazeny v dotazech, textu nebo jiných oddílech parametrů vložením názvu parametru do složených závorek, například ``{parameterName}`` . Názvy parametrů jsou omezené na podobná pravidla jako identifikátory JavaScriptu, abecední znaky nebo podtržítka, následované alfanumerickými znaky nebo podtržítkem. Například **a1** je povoleno, ale **1a** není povoleno.

Parametry jsou lineární, počínaje horním okrajem sešitu a natékání dolů na pozdější kroky.  Parametry deklarované později v sešitu mohou přepsat parametry, které byly dříve deklarovány. To také umožňuje parametrům, které používají dotazy pro přístup k hodnotám z parametrů definovaných dříve. V samotném kroku parametru jsou parametry také lineární, zleva doprava, kde parametry vpravo mohou záviset na parametru deklarovaném dříve v tomto kroku.
 
Existují čtyři různé typy parametrů, které jsou aktuálně podporovány:

|                  |      |
| ---------------- |:-----|
| **Text**    | Umožňuje uživateli upravit textové pole a volitelně můžete zadat dotaz k vyplnění výchozí hodnoty. |
| **Rozevírací seznam** | Umožňuje uživateli vybrat ze sady hodnot. |
| **Výběr časového rozsahu**| Umožňuje uživateli vybrat z předdefinované sady hodnot časového rozsahu nebo vybrat z vlastního časového rozsahu.|
| **Výběr prostředku** | Umožňuje uživateli vybrat z prostředků vybraných pro sešit.|

### <a name="using-a-text-parameter"></a>Použití textového parametru

Hodnota typ uživatele v textovém poli se nahradí přímo v dotazu bez uvozovacího příkazu ani quotes. Pokud je hodnota, kterou potřebujete, řetězec, dotaz by měl obsahovat uvozovky kolem parametru (například **{Parameter}**).

Parametr text umožňuje použití hodnoty v textovém poli kdekoli. Může to být název tabulky, název sloupce, název funkce, operátor atd.  Typ textového parametru má nastavení **načíst výchozí hodnotu z analytického dotazu**, které umožňuje autorovi sešitu použít dotaz k naplnění výchozí hodnoty tohoto textového pole.

Při použití výchozí hodnoty z dotazu protokolu je jako výchozí hodnota použita pouze první hodnota prvního řádku (řádek 0, sloupec 0). Proto je doporučeno omezit dotaz tak, aby vracel pouze jeden řádek a jeden sloupec. Všechna ostatní data vrácená dotazem jsou ignorována. 

Libovolná hodnota, kterou dotaz vrátí, se nahradí přímo bez uvozovacích znaků nebo citací. Pokud dotaz nevrátí žádné řádky, výsledek parametru je buď prázdný řetězec (Pokud parametr není požadován) nebo undefined (Pokud je parametr požadován).

### <a name="using-a-drop-down"></a>Použití rozevíracího seznamu

Typ parametru rozevíracího seznamu umožňuje vytvořit ovládací prvek rozevíracího seznamu, který umožňuje výběr jedné nebo více hodnot.

Rozevírací seznam se naplní dotazem protokolu nebo JSON. Pokud dotaz vrátí jeden sloupec, hodnoty v tomto sloupci jsou hodnoty a popisek v ovládacím prvku rozevírací seznam. Pokud dotaz vrátí dva sloupce, je prvním sloupcem hodnota a druhý sloupec je popisek zobrazený v rozevíracím seznamu. Pokud dotaz vrátí tři sloupce, použije se třetí sloupec k označení výchozího výběru v tomto rozevíracím seznamu. Tento sloupec může být libovolného typu, ale nejjednodušší je použít bool nebo číselné typy, kde 0 je false a 1 je pravda.

Pokud je sloupec typem řetězce, hodnota null nebo prázdný řetězec se považuje za false a jakákoli jiná hodnota se považuje za true. U rozevíracích seznamu pro jednotlivé výběry se jako výchozí výběr použije první hodnota s hodnotou true.  U rozevíracích seznamu vícenásobného výběru se jako výchozí vybraná sada použijí všechny hodnoty s hodnotou true. Položky v rozevíracím seznamu se zobrazí v jakémkoli pořadí, ve kterém dotaz vrátil řádky. 

Pojďme se podívat na parametry, které jsou k dispozici v sestavě Přehled připojení. Klikněte na symbol úprav vedle **směr**.

![Snímek obrazovky s oddílem pro přidání a úpravu parametrů sestavy v Azure Monitor. Je vybrána ikona pro úpravu pro parametr směru.](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Tím se spustí položka nabídky **Upravit parametr** .

![Snímek obrazovky dialogového okna Upravit parametr Název parametru je směr, typ parametru je rozevírací seznam a je vybrána možnost získat data z formátu JSON.](media/vminsights-workbooks/012-workbook-edit-parameter.png)

KÓD JSON umožňuje vygenerovat libovolnou tabulku naplněnou obsahem. Například následující kód JSON generuje v rozevíracím seznamu dvě hodnoty:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Podrobnější příklad používá rozevírací seznam pro výběr ze sady čítačů výkonu podle názvu:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

Dotaz zobrazí výsledky následujícím způsobem:

![Rozevírací seznam čítače výkonu](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Rozevírací nabídky jsou neuvěřitelně výkonné nástroje pro přizpůsobení a vytváření interaktivních sestav.

### <a name="time-range-parameters"></a>Parametry časového rozsahu

I když můžete vytvořit vlastní parametr časového rozsahu prostřednictvím rozevíracího seznamu, můžete použít také typ parametru časového rozsahu, pokud nepotřebujete stejnou míru flexibility. 

Typy parametrů časového rozsahu mají 15 výchozích rozsahů, které přecházejí z pěti minut na posledních 90 dní. K dispozici je také možnost Povolit vlastní výběr časového rozsahu, který umožňuje operátorovi sestavy zvolit explicitní hodnoty spuštění a zastavení pro časový rozsah.

### <a name="resource-picker"></a>Výběr prostředku

Typ parametru výběru prostředku vám umožní určit rozsah sestavy na určité typy prostředků. Příkladem předem sestaveného sešitu, který využívá typ výběru prostředku, je sešit **výkonu** .

![Rozevírací seznam pracovní prostory](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Ukládání sešitů a jejich sdílení v rámci týmu

Sešity se ukládají v pracovním prostoru Log Analytics nebo v prostředku virtuálního počítače v závislosti na tom, jak přistupujete k galerii sešitů. Sešit lze uložit do oddílu **Mé sestavy** , který je pro vás soukromý nebo v oddílu **sdílené sestavy** , který je přístupný všem uživatelům s přístupem k danému prostředku. Chcete-li zobrazit všechny sešity v prostředku, klikněte na panelu akcí na tlačítko **otevřít** .

Sdílení sešitu, který je aktuálně v **Mé sestavy**:

1. Na panelu akcí klikněte na **otevřít** .
2. Klikněte na "..." tlačítko vedle sešitu, který chcete sdílet
3. Klikněte na **přesunout ke sdíleným sestavám**.

Pokud chcete sešit sdílet s odkazem nebo e-mailem, klikněte na panelu akcí na **sdílet** . Mějte na paměti, že příjemci odkazu potřebují k tomuto prostředku přístup v Azure Portal k zobrazení sešitu. K provedení úprav potřebují příjemci pro daný prostředek aspoň oprávnění Přispěvatel.

Chcete-li připnout odkaz na sešit na řídicí panel Azure:

1. Na panelu akcí klikněte na **otevřít** .
2. Klikněte na "..." tlačítko vedle sešitu, který chcete připnout
3. Klikněte na **Připnout na řídicí panel**.

## <a name="next-steps"></a>Další kroky

- Pokud chcete zjistit omezení a celkový výkon virtuálních počítačů, přečtěte si téma [zobrazení výkonu virtuálního počítače Azure](vminsights-performance.md).

- Další informace o zjištěných závislostech aplikace najdete v tématu [zobrazení mapy Azure monitor pro virtuální počítače](vminsights-maps.md).
