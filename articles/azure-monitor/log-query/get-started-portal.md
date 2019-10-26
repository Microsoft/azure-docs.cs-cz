---
title: Začínáme s Azure Monitor Log Analytics | Microsoft Docs
description: Tento článek popisuje kurz použití Log Analytics v Azure Portal k zápisu dotazů.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 07/19/2019
ms.openlocfilehash: 1117ebbb8d2c3b133156c6b63a0ab13185f9f4a5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933051"
---
# <a name="get-started-with-log-analytics-in-azure-monitor"></a>Začínáme s Log Analytics v Azure Monitor

> [!NOTE]
> Pokud shromažďujete data alespoň z jednoho virtuálního počítače, můžete toto cvičení použít ve vlastním prostředí. Pokud ne, použijte naše ukázkové [prostředí](https://portal.loganalytics.io/demo), které obsahuje spoustu ukázkových dat.

V tomto kurzu se naučíte používat Log Analytics v Azure Portal k zápisu dotazů protokolu Azure Monitor log. Naučíte se, jak:

- Použití Log Analytics k zápisu jednoduchého dotazu
- Pochopení schématu dat
- Filtrování, řazení a seskupování výsledků
- Použít časový rozsah
- Vytváření grafů
- Uložení a načtení dotazů
- Exportovat a sdílet dotazy

Kurz týkající se psaní dotazů na protokoly najdete v tématu [Začínáme s dotazy protokolu v Azure monitor](get-started-queries.md).<br>
Další podrobnosti o dotazech protokolu najdete v tématu [Přehled dotazů protokolu v Azure monitor](log-query-overview.md).

## <a name="meet-log-analytics"></a>Splnění Log Analytics
Log Analytics je webový nástroj, který se používá pro zápis a spouštění Azure Monitorch dotazů protokolu. Otevřete ho tak, že v nabídce Azure Monitor vyberete **protokoly** . Začíná novým prázdným dotazem.

![Domovská stránka](media/get-started-portal/homepage.png)

## <a name="firewall-requirements"></a>Požadavky na bránu firewall
Pokud chcete použít Log Analytics, váš prohlížeč vyžaduje přístup k následujícím adresám. Pokud Váš prohlížeč přistupuje k Azure Portal přes bránu firewall, musíte povolit přístup k těmto adresám.

| identifikátor URI | IP | Porty |
|:---|:---|:---|
| portal.loganalytics.io | Dynamický | 80 443 |
| api.loganalytics.io | Dynamický | 80 443 |
| docs.loganalytics.io | Dynamický | 80 443 |

## <a name="basic-queries"></a>Základní dotazy
Dotazy se dají použít k vyhledávání podmínek, identifikaci trendů, analýze vzorů a poskytování mnoha dalších přehledů na základě vašich dat. Začněte se základním dotazem:

```Kusto
Event | search "error"
```

Tento dotaz vyhledá v tabulce _událostí_ záznamy, které obsahují _chybu_ v jakékoli vlastnosti.

Dotazy mohou začít buď s názvem tabulky, nebo s příkazem [hledání](/azure/kusto/query/searchoperator) . Výše uvedený příklad začíná s _událostí_název tabulky, která načte všechny záznamy z tabulky událostí. Znak kanálu (|) odděluje příkazy, takže výstup prvního z nich slouží jako vstup z následujícího příkazu. Do jednoho dotazu můžete přidat libovolný počet příkazů.

Dalším způsobem, jak stejný dotaz napsat, bude:

```Kusto
search in (Event) "error"
```

V tomto příkladu je **hledání** vymezeno na tabulku _událostí_ a všechny záznamy v této tabulce jsou vyhledány při výskytu _chyby_.

## <a name="running-a-query"></a>Spuštění dotazu
Spusťte dotaz kliknutím na tlačítko **Spustit** nebo stisknutím **SHIFT + ENTER**. Vezměte v úvahu následující podrobnosti, které určují kód, který se spustí, a vrácená data:

- Zalomení řádků: jediné přerušení usnadňuje čtení dotazů. Více konců řádků je rozděleno do samostatných dotazů.
- Cursor: Umístěte kurzor někam do dotazu, aby se spustil. Aktuální dotaz se považuje za kód, dokud se nenajde prázdný řádek.
- Časový rozsah – ve výchozím nastavení je nastavený časový rozsah za _posledních 24 hodin_ . Chcete-li použít jiný rozsah, použijte pro výběr času nebo přidejte do dotazu explicitní filtr časového rozsahu.


## <a name="understand-the-schema"></a>Vysvětlené schématu
Schéma je kolekce tabulek vizuálně seskupených do logické kategorie. Některé z kategorií jsou z monitorování řešení. Kategorie _LogManagement_ obsahuje běžná data, jako jsou události Windows a syslog, údaje o výkonu a prezenční signály agenta.

![Schéma](media/get-started-portal/schema.png)

V každé tabulce jsou data uspořádána do sloupců s různými datovými typy, které jsou označeny ikonami vedle názvu sloupce. Například tabulka _událostí_ zobrazená na snímku obrazovky obsahuje sloupce, jako je například _Computer_ , což je text, _EventCategory_ , což je číslo a _TimeGenerated_ , což je datum a čas.

## <a name="filter-the-results"></a>Filtrování výsledků
Začněte tím, že získáte vše v tabulce _událostí_ .

```Kusto
Event
```

Log Analytics automatické obory výsledků:

- Časový rozsah: ve výchozím nastavení jsou dotazy omezené na posledních 24 hodin.
- Počet výsledků: výsledky jsou omezené na maximum 10 000 záznamů.

Tento dotaz je velmi obecný a vrací příliš mnoho výsledků, aby byly užitečné. Výsledky můžete filtrovat buď prostřednictvím prvků tabulky, nebo explicitně přidáním filtru do dotazu. Filtrování výsledků prostřednictvím prvků tabulky se vztahuje na existující sadu výsledků, zatímco filtr na samotný dotaz vrátí novou filtrovanou sadu výsledků a může proto vytvořit přesnější výsledky.

### <a name="add-a-filter-to-the-query"></a>Přidejte filtr do dotazu.
Nalevo od každého záznamu se nachází šipka. Kliknutím na tuto šipku otevřete podrobnosti konkrétního záznamu.

Najeďte myší nad název sloupce pro ikony "+" a "-", které se mají zobrazit. Chcete-li přidat filtr, který vrátí pouze záznamy se stejnou hodnotou, klikněte na symbol "+". Kliknutím na "–" vyloučíte záznamy s touto hodnotou a potom kliknutím na tlačítko **Spustit** spusťte dotaz znovu.

![Přidat filtr do dotazu](media/get-started-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>Filtrovat přes prvky tabulky
Teď se podíváme na události se závažností _chyby_. Tento parametr je určený ve sloupci s názvem _EventLevelName_. Pro zobrazení tohoto sloupce se budete muset posunout doprava.

Klikněte na ikonu filtru vedle názvu sloupce a v místním okně vyberte _hodnoty začínající_ textovou _chybou_:

![Filtrovat](media/get-started-portal/filter.png)


## <a name="sort-and-group-results"></a>Řazení a seskupení výsledků
Výsledky jsou nyní zúžené tak, aby zahrnovaly pouze chybové události z SQL Server vytvořené za posledních 24 hodin. Výsledky nejsou ale seřazené jakýmkoli způsobem. Pokud chcete výsledky seřadit podle konkrétního sloupce, jako je například _časové razítko_ , klikněte na název sloupce. Jedno kliknutí seřadí vzestupné pořadí, zatímco druhé kliknutí bude seřazeno sestupně.

![Seřadit sloupec](media/get-started-portal/sort-column.png)

Další možností uspořádání výsledků je seskupení. Chcete-li seskupit výsledky podle konkrétního sloupce, jednoduše přetáhněte záhlaví sloupce nad ostatní sloupce. Chcete-li vytvořit podskupiny, přetáhněte horní panel také další sloupce.

![Skupiny](media/get-started-portal/groups.png)

## <a name="select-columns-to-display"></a>Vyberte sloupce, které se mají zobrazit.
Tabulka výsledků často obsahuje mnoho sloupců. Možná zjistíte, že některé z vrácených sloupců nejsou ve výchozím nastavení zobrazeny, nebo můžete chtít odebrat některé sloupce, které jsou zobrazeny. Chcete-li vybrat sloupce, které chcete zobrazit, klikněte na tlačítko sloupce:

![Vybrat sloupce](media/get-started-portal/select-columns.png)


## <a name="select-a-time-range"></a>Vyberte časový rozsah.
Ve výchozím nastavení Log Analytics použije _posledních 24 hodin_ časového rozsahu. Chcete-li použít jiný rozsah, vyberte jinou hodnotu prostřednictvím výběru času a klikněte na tlačítko **Spustit**. Kromě přednastavených hodnot můžete použít možnost _vlastní časový rozsah_ k výběru absolutního rozsahu dotazu.

![Výběr času](media/get-started-portal/time-picker.png)

Když vyberete vlastní časový rozsah, vybrané hodnoty jsou ve formátu UTC, což může být jiné než vaše místní časové pásmo.

Pokud dotaz explicitně obsahuje filtr pro _TimeGenerated_, zobrazí se v poli název pro výběr času _nastavení v dotazu_. Ruční výběr bude zakázán, aby se zabránilo konfliktu.


## <a name="charts"></a>spojnic
Kromě vracení výsledků v tabulce je možné výsledky dotazu prezentovat ve vizuálních formátech. Jako příklad použijte následující dotaz:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Ve výchozím nastavení se výsledky zobrazují v tabulce. Kliknutím na _graf_ zobrazíte výsledky v grafickém zobrazení:

![Pruhový graf](media/get-started-portal/bar-chart.png)

Výsledky se zobrazují v skládaném pruhovém grafu. Klikněte na _Skládaný sloupcový_ a vyberte _výseč_ , aby se zobrazilo jiné zobrazení výsledků:

![Výsečový graf](media/get-started-portal/pie-chart.png)

Různé vlastnosti zobrazení, například osy x a y nebo seskupení a rozdělení předvoleb, lze změnit ručně z ovládacích panelů.

Můžete také nastavit preferované zobrazení v samotném dotazu pomocí operátoru vykreslení.

### <a name="smart-diagnostics"></a>Inteligentní Diagnostika
V případě, že je v timechart náhlé špička nebo krok, může se na řádku zobrazit zvýrazněný bod. To znamená, že _inteligentní Diagnostika_ identifikovala kombinaci vlastností, které odfiltrují náhlé změny. Kliknutím na bod získáte další podrobnosti o filtru a zobrazíte filtrovanou verzi. To vám může přispět k identifikaci toho, co způsobilo změnu:

![Inteligentní Diagnostika](media/get-started-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>Připnout na řídicí panel
Pokud chcete připnout diagram nebo tabulku na některý ze sdílených řídicích panelů Azure, klikněte na ikonu připnutí. Všimněte si, že tato ikona se přesunula na horní část okna Log Analytics, která se liší od obrazovky níže.

![Připnout na řídicí panel](media/get-started-portal/pin-dashboard.png)

Určitá zjednodušení se u grafu aplikují při jeho připnutí na řídicí panel:

- Sloupce a řádky tabulky: aby bylo možné tabulku připnout na řídicí panel, musí mít čtyři nebo méně sloupců. Zobrazí se pouze prvních sedm řádků.
- Časové omezení: dotazy se automaticky omezí na posledních 14 dní.
- Omezení počtu přihrádek: Pokud zobrazíte graf, který má spoustu diskrétních přihrádek, méně vyplněné přihrádky se automaticky seskupí do jedné přihrádky _ostatní_ .

## <a name="save-queries"></a>Uložení dotazů
Po vytvoření užitečného dotazu ho můžete chtít uložit nebo sdílet s ostatními. Ikona **Uložit** je na horním panelu.

Jako funkci můžete uložit celou stránku dotazu nebo jeden dotaz. Funkce jsou dotazy, které mohou být také odkazovány jinými dotazy. Chcete-li uložit dotaz jako funkci, je nutné zadat alias funkce, což je název, který se používá k volání tohoto dotazu, pokud na něj odkazují jiné dotazy.

![Save – funkce](media/get-started-portal/save-function.png)

>[!NOTE]
>Následující znaky jsou podporovány – `a–z, A–Z, 0-9, -, _, ., <space>, (, ), |` v poli **název** při ukládání nebo úpravách uloženého dotazu.

Log Analytics dotazy se vždycky ukládají do vybraného pracovního prostoru a sdílí se s ostatními uživateli tohoto pracovního prostoru.

## <a name="load-queries"></a>Načíst dotazy
Ikona Průzkumník dotazů je v pravé horní části. Zobrazí se seznam všech uložených dotazů podle kategorie. Umožňuje také označit konkrétní dotazy jako oblíbené položky a rychle je najít v budoucnu. Dvojím kliknutím na uložený dotaz ho přidejte do aktuálního okna.

![Průzkumník dotazů](media/get-started-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>Exportovat a sdílet jako odkaz
Log Analytics podporuje několik metod exportu:

- Excel: uložte výsledky jako soubor CSV.
- Power BI: Exportujte výsledky do Power BI. Podrobnosti najdete v tématu [Import dat protokolu Azure monitor do Power BI](../../azure-monitor/platform/powerbi.md) .
- Sdílet odkaz: samotný dotaz může být sdílen jako odkaz, který lze následně odeslat a spustit jinými uživateli, kteří mají přístup ke stejnému pracovnímu prostoru.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [psaní Azure Monitorch dotazů protokolu](get-started-queries.md).
