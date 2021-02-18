---
title: Přehled Log Analytics v Azure Monitor
description: Popisuje Log Analytics, který je nástrojem v Azure Portal použitém k úpravám a spouštění dotazů protokolu pro analýzu dat v protokolech Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/04/2020
ms.openlocfilehash: 485c2cc6c95f5922025fe887f7c25131e849a01d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100612784"
---
# <a name="overview-of-log-analytics-in-azure-monitor"></a>Přehled Log Analytics v Azure Monitor
Log Analytics je nástroj v Azure Portal, který se používá k úpravě a spouštění dotazů protokolu s daty v protokolech Azure Monitor. Můžete napsat jednoduchý dotaz, který vrátí sadu záznamů, a pak pomocí funkcí Log Analytics řadit, filtrovat a analyzovat. Nebo můžete napsat pokročilejší dotaz, který provede statistickou analýzu a vizualizuje výsledky v grafu, aby identifikoval konkrétní trend. Ať už pracujete s výsledky dotazů interaktivně nebo je použijete s jinými Azure Monitor funkcemi, jako jsou výstrahy nebo sešity dotazů protokolu, Log Analytics je nástroj, který budete používat pro zápis a testování. 


> [!TIP]
> Tento článek obsahuje popis Log Analytics a všech jeho funkcí. Pokud chcete přejít přímo do kurzu, přečtěte si článek [Log Analytics kurz](../log-query/log-analytics-tutorial.md).



## <a name="starting-log-analytics"></a>Spouští se Log Analytics
Spustí Log Analytics z **protokolů** v nabídce **Azure monitor** v Azure Portal. Tato možnost se zobrazí také v nabídce pro většinu prostředků Azure. Bez ohledu na to, odkud ji začnete, bude to stejný nástroj Log Analytics. Nabídka, kterou použijete k zahájení Log Analytics určuje data, která budou dostupná, i když. Pokud ho spustíte z nabídky **Azure monitor** nebo z nabídky **pracovní prostory Log Analytics** , budete mít přístup ke všem záznamům v pracovním prostoru. Pokud vyberete **protokoly** z jiného typu prostředku, budou se data omezovat na data protokolu pro daný prostředek. Podrobnosti najdete [v tématu Rozsah dotazů protokolu a časový rozsah v Azure Monitor Log Analytics](../log-query/scope.md) .

[![Spustit Log Analytics](media/log-analytics-overview/start-log-analytics.png)](media/log-analytics-overview/start-log-analytics.png#lightbox)

Když začnete Log Analytics, první věc, kterou vidíte, je dialogové okno s [příklady dotazů](../logs/example-queries.md). Tyto kategorie jsou rozdělené podle řešení a můžete procházet nebo Hledat dotazy, které splňují vaše konkrétní požadavky. Možná budete moct najít přesně to, co potřebujete, nebo načíst jeden do editoru a podle potřeby ho upravit. Procházení ukázkových dotazů je ve skutečnosti skvělým způsobem, jak se naučit psát vlastní dotazy. Samozřejmě, pokud chcete začít s prázdným skriptem a napsat ho sami, můžete příklady dotazů zavřít. Pokud se chcete vrátit zpátky, stačí kliknout na **dotazy** v horní části obrazovky.

## <a name="log-analytics-interface"></a>Rozhraní Log Analytics
Následující obrázek identifikuje různé součásti Log Analytics.

[![Log Analytics](media/log-analytics-overview/log-analytics.png)](media/log-analytics-overview/log-analytics.png#lightbox)

### <a name="1-top-action-bar"></a>1. horní panel akcí
Ovládací prvky pro práci s dotazem v okně dotazu.

| Možnost | Popis |
|:---|:---|
| Obor | Určuje rozsah dat použitých pro dotaz. Může se jednat o všechna data v pracovním prostoru Log Analytics nebo data pro konkrétní prostředek napříč několika pracovními prostory. Viz [obor dotazu](../log-query/scope.md). |
| Tlačítko spustit | Kliknutím spustíte vybraný dotaz v okně dotazu. Můžete také stisknout SHIFT + ENTER a spustit dotaz. |
| Výběr času | Vyberte časový rozsah pro data dostupná pro dotaz. Tato aplikace bude přepsána, pokud zahrnete do dotazu filtr času. Viz [Rozsah dotazů protokolu a rozsah času ve Azure Monitor Log Analytics](../log-query/scope.md). |
| Tlačítko Uložit | Uložte dotaz do Průzkumníka dotazů pro pracovní prostor. |
 Tlačítko pro kopírování | Zkopíruje odkaz na dotaz, text dotazu nebo výsledky dotazu do schránky. |
| Tlačítko nové pravidlo upozornění | Vytvoří novou kartu s prázdným dotazem. |
| Tlačítko Exportovat | Exportujte výsledky dotazu do souboru CSV nebo do dotazu Power Query formátu jazyka vzorců pro použití s Power BI. |
| Tlačítko Připnout na řídicí panel | Přidejte výsledky dotazu na řídicí panel Azure. |
| Tlačítko formátovat dotaz | Uspořádá vybraný text pro čitelnost. |
| Tlačítko příklady dotazů | Otevřete dialogové okno Ukázkové dotazy, které se zobrazí při prvním otevření Log Analytics. |
| Tlačítko Průzkumník dotazů | Otevřete **Průzkumník dotazů** , který poskytuje přístup k uloženým dotazům v pracovním prostoru. |


### <a name="2-sidebar"></a>2. postranní panel
Seznam tabulek v pracovním prostoru, Ukázkové dotazy a možnosti filtrování pro aktuální dotaz.

| Karta | Description |
|:---|:---|
| Tabulky | Vypíše tabulky, které jsou součástí vybraného oboru. Vyberte možnost **Seskupit podle** a změňte seskupení tabulek. Najeďte myší na název tabulky, aby se zobrazilo dialogové okno s popisem tabulky a možností, jak zobrazit jeho dokumentaci a zobrazit náhled jeho dat. Rozbalením tabulky zobrazíte její sloupce. Poklikejte na název tabulky nebo sloupce a přidejte ho do dotazu. |
| Dotazy | Seznam ukázkových dotazů, které můžete otevřít v okně dotazu. Toto je stejný seznam, který se zobrazí, když otevřete Log Analytics. Vyberte možnost **Seskupit podle** a změňte seskupení dotazů. Poklikejte na dotaz a přidejte ho do okna dotazu, nebo na něj najeďte myší pro další možnosti. |
| Filtrovat | Vytvoří možnosti filtru na základě výsledků dotazu. Po spuštění dotazu se sloupce zobrazí s různými hodnotami z výsledků. Vyberte jednu nebo více hodnot a potom kliknutím na **použít & spustit** přidejte do dotazu příkaz **WHERE** a spusťte jej znovu. |

### <a name="3-query-window"></a>3. dotaz – okno
V okně dotazu je místo, kde dotaz upravujete. To zahrnuje IntelliSense pro příkazy KQL a barevné kódování pro zlepšení čitelnosti. Kliknutím na v **+** horní části okna otevřete jinou kartu.

V rámci jednoho okna může být více dotazů. Dotaz nemůže obsahovat žádné prázdné řádky, takže můžete v okně oddělit více dotazů s jedním nebo více prázdnými řádky. Aktuální dotaz je ten, který má kurzor umístěný kdekoli v něm.

Chcete-li spustit aktuální dotaz, klikněte na tlačítko **Spustit** nebo stiskněte klávesy SHIFT + ENTER.

### <a name="4-results-window"></a>4. okno výsledků
Výsledky dotazu se zobrazí v okně výsledků. Ve výchozím nastavení se výsledky zobrazují jako tabulka. Chcete-li se zobrazit jako graf, vyberte v okně výsledků buď možnost **graf** , nebo do dotazu přidejte příkaz pro **vykreslení** .

#### <a name="results-view"></a>Výsledky
Zobrazí výsledky dotazu v tabulce uspořádané podle sloupců a řádků. Kliknutím nalevo od řádku rozbalíte jeho hodnoty. Chcete-li změnit seznam sloupců, klikněte na rozevírací nabídku **sloupce** . Výsledky seřaďte kliknutím na název sloupce. Filtrujte výsledky kliknutím na trychtýř vedle názvu sloupce. Zrušte zaškrtnutí políčka filtry a obnovte řazení spuštěním dotazu znovu.

Vyberte **sloupce skupiny** , aby se zobrazil pruh seskupení nad výsledky dotazu. Seskupí výsledky podle libovolného sloupce přetažením na pruh. Vytvořte vnořené skupiny ve výsledcích přidáním dalších sloupců. 

#### <a name="chart-view"></a>Zobrazení grafu
Zobrazí výsledky jako jeden z několika dostupných typů grafů. Typ grafu můžete v příkazu pro **vykreslení** zadat v dotazu nebo ho vybrat z rozevíracího seznamu **typ vizualizace** .

| Možnost | Popis |
|:---|:---|
| **Typ vizualizace** | Typ grafu, který se má zobrazit |
| **Osa X** | Sloupec ve výsledcích, který se má použít pro osu X 
| **Osa Y** | Sloupec ve výsledcích, který se má použít pro osu Y Obvykle se jedná o číselný sloupec. |
| **Rozděleno podle** | Sloupec ve výsledcích, který definuje řadu v grafu. Pro každou hodnotu ve sloupci se vytvoří řada. |
| **Agregace** | Typ agregace, která má být provedena na numerických hodnotách na ose Y. |

## <a name="relationship-to-azure-data-explorer"></a>Vztah k Azure Průzkumník dat
Pokud jste již obeznámeni s webovým uživatelským rozhraním Azure Průzkumník dat, Log Analytics by měl vypadat dobře. To je proto, že je postavená na Azure Průzkumník dat a používá stejný dotazovací jazyk KQL (Kusto Query Language). Log Analytics přidává funkce specifické pro Azure Monitor, jako je filtrování podle časového rozsahu a možnost vytvořit pravidlo výstrahy z dotazu. Oba nástroje obsahují Průzkumníka, který umožňuje prohledat strukturu dostupných tabulek, ale webové uživatelské rozhraní Azure Průzkumník dat primárně funguje s tabulkami v Azure Průzkumník dat databázemi, zatímco Log Analytics pracuje s tabulkami v pracovním prostoru Log Analytics. 

## <a name="next-steps"></a>Další kroky
- Projděte si [kurz použití Log Analytics v Azure Portal](../log-query/log-analytics-tutorial.md).
- Projděte si [kurz týkající se psaní dotazů](../log-query/get-started-queries.md).