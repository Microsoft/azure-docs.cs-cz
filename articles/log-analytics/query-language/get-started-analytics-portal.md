---
title: Začínáme se službou Log Analytics na portálu Azure portal | Dokumentace Microsoftu
description: Tento článek obsahuje kurz pro psaní dotazů pomocí Log Analytics na portálu Azure portal.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: bwren
ms.openlocfilehash: 9d045fefaccc9bc155f80e7b40c340d6531a7dda
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52683043"
---
# <a name="get-started-with-log-analytics-in-the-azure-portal"></a>Začínáme se službou Log Analytics na portálu Azure portal

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

V tomto kurzu se dozvíte, jak psát dotazy Log Analytics pomocí Log Analytics stránky na webu Azure Portal (aktuálně ve verzi preview). To se dozvíte, jak do:

- Zápis jednoduchých dotazů
- Pochopení schématu dat
- Filtrace, řazení a seskupení výsledků
- Použít časový rozsah
- Vytváření grafů
- Uložit a načíst dotazy
- Export a sdílet dotazy


## <a name="meet-the-log-analytics-page"></a>Splnění stránce Log Analytics 
Na stránce Log Analytics je webový nástroj pro zápis a spouštění dotazů Azure Log Analytics. Otevřete ho tak, že vyberete **protokoly (preview)** v nabídce Log Analytics. Spustí se nový prázdný dotaz.

![Domovská stránka](media/get-started-analytics-portal/homepage.png)



## <a name="basic-queries"></a>Základní dotazy
Hledané termíny, rozpoznávejte trendy, analyzovat vzory a poskytují mnoho přehledy na základě vašich dat je možné dotazy. Začínáme s základní dotazy:

```Kusto
Event | search "error"
```

Tento dotaz vyhledá _události_ tabulky pro záznamy, které obsahují pojem "Chyba" v jakékoli vlastnosti.

Dotazy můžete spustit buď pomocí názvu tabulky nebo **hledání** příkazu. Výše uvedený příklad začíná název tabulky _události_, která definuje obor dotazu. Znak svislé čáry (|) odděluje příkazy, takže výstup první z nich ve vstupu následující příkaz. Můžete přidat libovolný počet příkazů do jednoho dotazu.

Jiný způsob psaní tohoto stejného dotazu by byl:

```Kusto
search in (Event) "error"
```

V tomto příkladu **hledání** je vymezen _události_ tabulku a všechny záznamy v této tabulce jsou prohledány na termín "Chyba".

## <a name="running-a-query"></a>Spuštění dotazu
Spusťte dotaz kliknutím **spustit** tlačítko nebo stisknutím klávesy **Shift + Enter**. Vezměte v úvahu následující informace, které určíte, kód, který se spustí a data, která je vrácena:

- Konce řádků: jeden konec umožňuje volbu dotazu. Více konce řádků ho rozdělit na samostatné dotazy.
- Cursor: Umístěte kurzor někam uvnitř dotazu k jeho provedení. Aktuální dotaz se považuje za kód, dokud nenajde prázdný řádek.
- Časový rozsah - časový rozsah _posledních 24 hodin_ ve výchozím nastavení. Pokud chcete použít jiný rozsah, použijte Výběr času nebo přidat explicitní čas filtr rozsahu do dotazu.


## <a name="understand-the-schema"></a>Vysvětlené schématu
Schéma je kolekce tabulek vizuálně seskupené pod logických kategorií. Některé z kategorií jsou z monitorování řešení. _LogManagement_ kategorie obsahuje běžné data, jako jsou Windows a protokolu Syslog události, údaje o výkonu a prezenční signály klienta.

![Schéma](media/get-started-analytics-portal/schema.png)

V každé tabulce dat uspořádány do sloupce s různými datovými typy je určeno ikonami vedle jejich názvu sloupce. Například _události_ tabulka ukazuje snímek obrazovky obsahuje sloupce, například _počítače_ což je text, _EventCategory_ což je číslo, a  _TimeGenerated_ což je datum a čas.

## <a name="filter-the-results"></a>Filtrování výsledků
Začněte tím, že tu být všechno _události_ tabulky.

```Kusto
Event
```

Na stránce Log Analytics automaticky obory výsledky podle:

- Časový rozsah: ve výchozím nastavení, dotazy jsou omezené na poslední 24 hodin.
- Počet výsledků: výsledky jsou omezeny na maximálně 10 000 záznamů.

Tento dotaz je velmi obecná a vrátí příliš mnoho výsledků. aby byla užitečná. Můžete filtrovat výsledky přes prvky tabulky nebo tak, že explicitně přidáte filtr do dotazu. Filtrování výsledků přes prvky tabulky platí pro stávající sadu výsledků, zatímco filtru samotný dotaz vrátí novou filtrovaných výsledků nastavit a může proto poskytuje přesnější výsledky.

### <a name="add-a-filter-to-the-query"></a>Přidání filtru do dotazu
Existuje šipku nalevo od každého záznamu. Klepnutím na šipku otevřete podrobnosti konkrétního záznamu.

Najeďte myší nad název sloupce pro "+" a "-" k zobrazení ikony. Pokud chcete přidat filtr, který vrátí pouze záznamy se stejnou hodnotou, klikněte na znaménko "+". Klikněte na tlačítko "-" vyloučit záznamy s touto hodnotou a potom klikněte na **spustit** spusťte dotaz znovu.

![Přidání filtru do dotazu](media/get-started-analytics-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>Filtrovat přes prvky tabulky
Nyní zaměřme se na události závažnost _chyba_. Tento parametr je zadán v sloupec s názvem _EventLevelName_. Budete muset posunout doprava najdete v tomto sloupci.

Kliknutím na ikonu filtru vedle záhlaví sloupce a v automaticky otevíraném okně vyberte hodnoty, které _začíná_ text _chyba_:

![Filtr](media/get-started-analytics-portal/filter.png)


## <a name="sort-and-group-results"></a>Seřadit a seskupit výsledky
Výsledky jsou nyní zúží a obsahovat pouze chybové události z SQL serveru vytvořené za posledních 24 hodin. Však nejsou seřazené výsledky žádným způsobem. Chcete-li seřadit výsledky podle konkrétního sloupce, jako například _časové razítko_ například klikněte na záhlaví sloupce. Jedno kliknutí Seřadí seznam vzestupně během druhé kliknutí se seřadit sestupně.

![Sloupec pro řazení](media/get-started-analytics-portal/sort-column.png)

Dalším způsobem, jak výsledky uspořádat je podle skupin. K seskupení výsledků podle konkrétního sloupce, jednoduše přetáhněte záhlaví sloupce vyšší než ostatní sloupce. K vytvoření podskupiny, přetáhněte další sloupce na horním panelu také.

![Skupiny](media/get-started-analytics-portal/groups.png)

## <a name="select-columns-to-display"></a>Vyberte sloupce, které chcete zobrazit
Tabulka výsledků často obsahuje mnoho sloupců. Můžete zjistit, že některé vrácené sloupce se nezobrazují ve výchozím nastavení, nebo můžete chtít odebrat některé sloupce, které jsou zobrazeny. Pokud chcete vybrat sloupce, které chcete zobrazit, klikněte na tlačítko sloupce:

![Výběr sloupců](media/get-started-analytics-portal/select-columns.png)


## <a name="select-a-time-range"></a>Vybrat časový rozsah
Ve výchozím nastavení, Log Analytics stránka se vztahuje _posledních 24 hodin_ časový rozsah. Pokud chcete použít jiný rozsah, vyberte jinou hodnotu prostřednictvím nástroje pro výběr času a klikněte na **spustit**. Kromě přednastavené hodnoty, můžete použít _vlastního časového rozsahu_ možnost vybrat si absolutní rozsahu pro váš dotaz.

![Výběr času](media/get-started-analytics-portal/time-picker.png)

Když vyberete vlastní časový rozsah, vybraných hodnot se ve standardu UTC, což může být jiný než místní časové pásmo.

Pokud dotaz obsahuje explicitně filtr pro _TimeGenerated_, se zobrazí název výběr času _nastavit v dotazu_. Aby se zabránilo konfliktu se zakážou ruční výběr.


## <a name="charts"></a>Grafy
Kromě vracení výsledků v tabulce, můžete výsledky dotazu uvedené v visual formátů. Jako příklad použijte následující dotaz:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Ve výchozím nastavení výsledky se zobrazí v tabulce. Klikněte na tlačítko _grafu_ pro zobrazení výsledků v grafické zobrazení:

![Pruhový graf](media/get-started-analytics-portal/bar-chart.png)

Výsledky jsou zobrazeny skládaném pruhovém grafu. Klikněte na tlačítko _skládaný sloupcový_ a vyberte _výsečový_ do jiného zobrazení výsledků:

![Výsečový graf](media/get-started-analytics-portal/pie-chart.png)

Různé vlastnosti objektu zobrazení, jako je například x a osy y, nebo seskupování a rozdělování předvolby, můžete ručně změnit na ovládacím panelu.

Požadované zobrazení můžete také nastavit v dotazu, použití operátoru vykreslování.

### <a name="smart-diagnostics"></a>Inteligentní Diagnostika
Na časový graf Pokud náhlá Špička nebo kroku ve vašich datech, může se zobrazit bod zvýrazněný na řádku. Znamená to, že _inteligentní Diagnostika_ zjistila kombinací vlastností, které odfiltrovat náhlé změny. Klikněte na bod, abyste získali více podrobností na filtr a zobrazíte filtrované verze. To může pomoci zjistit, co způsobilo změny:

![Inteligentní Diagnostika](media/get-started-analytics-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>Připnout na řídicí panel
Pro Připnutí diagramu nebo tabulku k jedné sdílené řídicí panely Azure, klikněte na ikonu připínáčku.

![Připnout na řídicí panel](media/get-started-analytics-portal/pin-dashboard.png)

Některé zjednodušení jsou použity pro graf připnout na řídicí panel:

- Sloupce a řádky tabulky: Pokud chcete připnout tabulku na řídicí panel, musí mít čtyři nebo méně sloupců. Zobrazí se jenom prvních řádků sedm.
- Čas omezení: dotazy jsou automaticky omezeny na posledních 14 dní.
- Omezení počtu Bin: Pokud zobrazíte graf, který obsahuje mnoho intervalů diskrétní méně mají údaj vyplněný přihrádek budou automaticky seskupeny do jednoho _ostatní_ bin.

## <a name="save-queries"></a>Ukládání dotazů
Po vytvoření užitečného dotazu, můžete chtít uložit nebo sdílet s ostatními. **Uložit** je ikona na horním panelu.

Na stránce celý dotaz nebo pomocí jediného dotazu můžete uložit jako funkce. Funkce jsou dotazy, které lze také odkazovat pomocí jiných dotazů. Aby bylo možné uložit dotaz jako funkce, je nutné zadat alias funkce, což je název používaný k volání tohoto dotazu, když odkazují jiné dotazy.

![Uložit – funkce](media/get-started-analytics-portal/save-function.png)

Dotazy log Analytics jsou vždy uloženy do vybraného pracovního prostoru a sdílet s ostatními uživateli daného pracovního prostoru.

## <a name="load-queries"></a>Načíst dotazy
Ikona Průzkumníka dotazů je v horní pravé oblasti. Vypíšou se všechny uložené dotazy podle kategorie. Také umožňuje označit jako oblíbené položky v budoucnu je rychle vyhledat konkrétní dotazy. Klikněte dvakrát na uložený dotaz se přidá do aktuálního okna.

![Průzkumník dotazů](media/get-started-analytics-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>Export a sdílenou složku jako odkaz
Na stránce Log Analytics podporuje několik metod pro export:

- Excel: Uložte výsledky do souboru .csv.
- Power BI: Exportujte výsledky do power BI. Zobrazit [Import Azure Log Analytics data do Power BI](../log-analytics-powerbi.md) podrobnosti.
- Sdílet odkaz: samotný dotaz je možné sdílet jako odkaz, který lze potom odeslat a spustit další uživatelé, kteří mají přístup do stejného pracovního prostoru.

## <a name="next-steps"></a>Další postup

- Další informace o [zápis dotazy Log Analytics](get-started-queries.md).
