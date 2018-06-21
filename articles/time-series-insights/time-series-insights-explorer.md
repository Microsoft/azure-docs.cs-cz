---
title: Prozkoumejte dat pomocí Průzkumníka Statistika řady čas Azure | Microsoft Docs
description: Tento článek popisuje použití Průzkumníka Azure časové řady Insights ve webovém prohlížeči globální zobrazení velkých objemů dat se krátce zobrazit a ověřit prostředí IoT.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: kfile
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: d3b8973e8798e5b1f32d9e882db8a06ad7486ed8
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295334"
---
# <a name="azure-time-series-insights-explorer"></a>Průzkumník Azure Statistika časové řady
Tento článek popisuje různé funkce a možnosti dostupné v rámci webové aplikace explorer Statistika časové řady. Pomocí Průzkumníka časové řady Insights ve webovém prohlížeči vytvořit vizualizacemi vašich dat.
 
Azure Time Series Insights je plně spravovaná služba pro analýzy, ukládání a vizualizace, která usnadňuje zkoumání a analýzy miliard událostí IoT současně. Poskytuje globální zobrazení dat, která vám umožní rychle ověřit řešení IoT a vyhnout nákladným odstávkám důležité zařízení. Můžete zjistit skrytá trendy, spot anomálií a provedení analýzy příčin skoro v reálném čase. Časové řady Statistika explorer je aktuálně ve verzi public preview.

## <a name="prerequisites"></a>Požadavky

Před použitím Průzkumníka časové řady Insights, musíte provést tyto akce:
- Vytvořte prostředí Statistika časové řady
- Poskytovat přístup ke svému účtu v prostředí
- Přidat zdroje událostí k načítání dat a jejich uložení

## <a name="explore-and-query-data"></a>Prozkoumejte a zadávat dotazy na data
V rámci připojení vaší zdroj události pro vaše prostředí časové řady Statistika počet minut vám umožní zkoumat a dotazování na data časové řady.

1. Chcete-li začít, otevřete [explorer časové řady Insights](https://insights.timeseries.azure.com/) ve webovém prohlížeči a vyberte prostředí na levé straně okna. Všechna prostředí, které mají přístup k jsou uvedeny v abecedním pořadí.

2. Jakmile vyberete prostředí, použijte buď **FROM** a **na** konfigurace nahoře, nebo klikněte na tlačítko a přetáhněte přes požadované časové rozpětí.  Klikněte na lupy vpravo, nahoře nebo přes vybraný časový interval klikněte pravým tlačítkem a vyberte **vyhledávání**.  

3. Můžete taky aktualizovat dostupnosti automaticky každou minutu výběrem **automaticky na** tlačítko.  Všimněte si, že se na tlačítko 'Auto-na' se vztahuje pouze na dostupnosti grafu, není obsah hlavní vizualizace.

4. Všimněte si, ikonu cloudu Azure přejdete do prostředí na portálu Azure.

   ![Prostředí Time Series Insights](media/time-series-insights-explorer/explorer1.png)

5. V dalším kroku zobrazí graf, který obsahuje počet všechny události během vybraný časový interval.  Zde máte řadu ovládacích prvků:

    **Podmínky Editor Panel**: místo termín je where dotazu prostředí.  Se nachází na levé straně obrazovky, umožňuje 
      - **Míra**: to rozevírací nabídku ukazuje všechny číselné sloupce (hodnoty Double)
      - **Rozdělení podle**: Tento rozevíracího seznamu zobrazuje kategorií sloupce (řetězce)
      - Můžete povolit krok interpolace, zobrazí minimální a maximální a upravit osu y z ovládacích panelů vedle k měření.  Kromě toho můžete upravit, zda data zobrazená je počet, průměr nebo součet data.
      - Můžete přidat až pět podmínky zobrazíte na stejné osy x.  Použití **kopie rozbalovací** tlačítko Přidat další termín nebo kliknutím na tlačítko **přidat** tlačítko přidáte nový termín.
     
        ![Editor panelu podmínky](media/time-series-insights-explorer/explorer2.png)

      - **Predikát**: Predikát umožňuje rychle vyfiltrovat události pomocí sady operandy, které jsou uvedeny níže. Pokud zadání vyhledávání vyberete kliknutím predikátem bude automaticky aktualizace na základě toto hledání.      Operand podporované typy patří:

         |Operace  |Podporované typy  |Poznámky  |
         |---------|---------|---------|
         |<, >, <=, >=     |  Double, DateTime, časový interval       |         |
         |=, !=, <>     | Řetězec, Bool, Double, DateTime, časový interval, hodnotu NULL        |         |
         |V     | Řetězec, Bool, Double, DateTime, časový interval, hodnotu NULL        |  Všechny operandy musí být stejného typu nebo musí být konstanta NULL.        |
         |MÁ     | Řetězec        |  Na pravé straně jsou povoleny pouze konstantní textové literály. Prázdný řetězec a NULL nejsou povoleny.       |

      - **Příklady dotazů**
      
         ![Ukázky dotazů](media/time-series-insights-explorer/explorer9.png)

6. **Velikost intervalu** posuvníku nástroj umožňuje oddálit intervaly přes stejné časový interval.  To zajišťuje přesnější kontrolu pohybu mezi velké časové úseky které se zobrazí smooth trendy dolů řezy milisekundu, umožní vám zobrazíte podrobné, s vysokým rozlišením kusy vašich dat co nejmenší. Posuvníku výchozí počáteční bod je nastavena jako optimální zobrazení dat z výběru. Vyrovnávání řešení, rychlost dotazu a členitost.

7. **Čas štětce** nástroj umožňuje snadno přejít z jednoho časové rozpětí do jiného umístění Intuitivní UX vepředu a uprostřed pro bezproblémové přesun mezi časových rozsahů.

8. **Uložit** příkaz umožňuje uložit aktuální dotaz a povolit pro sdílení s jinými uživateli prostředí. Pomocí **otevřete**, zobrazí se všechny uložené dotazy a všechny sdílené dotazy jiných uživatelů v prostředí, které máte přístup. 

   ![Dotazy](media/time-series-insights-explorer/explorer3.png)

9. **Zobrazení perspektivy** nástroj poskytuje souběžných zobrazení až čtyři jedinečný dotazů. Tlačítko perspektivy zobrazení můžete najít v pravém horním rohu grafu.  

   ![Zobrazení perspektivy](media/time-series-insights-explorer/explorer4.png)

10. **Grafu** umožňuje vizuálně zkoumat data. Graf nástroje patří:

   - Vyberte nebo klikněte na tlačítko, která umožňuje výběr specifické časový interval nebo jednu datovou řadu.  
   - V době span výběr, můžete zvětšení nebo prozkoumat události.  
   - V rámci datové řady můžete rozdělit řadu podle jiného sloupce, přidat jako nový termín řady, zobrazit pouze vybrané řady, vyloučit vybrané řady, příkazem ping otestovat ta řada nebo prozkoumat události z vybrané řady.
   - V oblasti filtru na levé straně grafu můžete najdete všechny řady zobrazených dat a změna pořadí hodnota nebo název, zobrazit všechny datové řady nebo konkrétně definovaného nebo Odepnout řad.  Můžete také vybrat jednu datovou řadu a řadu rozdělit podle jiného sloupce, řady přidat jako nový termín, zobrazit pouze vybrané řady, vyloučit vybrané řady, připnout ta řada nebo prozkoumat události z vybrané řady.
   - Při zobrazení více podmínek současně, můžete zásobníku, unstack, najdete v části Další data o datové řady a používat stejné osy y mezi všechny podmínky s tlačítka v pravém horním rohu grafu.
 
   ![Nástrojů grafu](media/time-series-insights-explorer/explorer5.png) 

11. **Heatmap** lze rychle rozpoznat jedinečný nebo neobvyklé datové řady v daný dotaz. Pouze jeden hledaný termín můžete vizualizovat jako heatmap.    

   ![Heat mapa](media/time-series-insights-explorer/explorer6.png)

12. **Události**: když zvolíte prozkoumat události při výběru nebo kliknete pravým tlačítkem na výše, události panelu je k dispozici.  Zde se zobrazí všechny nezpracované události a export událostí jako JSON nebo souborů CSV. Všimněte si, že časové řady Statistika ukládá všechna nezpracovaná data.

   ![Události](media/time-series-insights-explorer/explorer7.png)

13. Klikněte **statistiky** karta po zkoumání události vystavit vzory a statistiky sloupce.  

   - **Vzory**: Tato funkce poskytuje proaktivní nejvíce statisticky významný vzory v oblasti vybraná data. To zbavuje kterou přináší nutnost můžete z musel podívejte se na tisíce události pochopit, jaké vzory vlastní nejvíce času a energie. Statistika řady čas navíc umožňuje přejít přímo do těchto statisticky významný vzorků pokračovat v provádění analýzu. Tato funkce je také užitečné pro postmortální vyšetřování historická data. 

   - **Sloupec statistiky**: statistiky sloupce zadejte grafů a tabulek, které rozdělí data z každý sloupec vybrané datové řady přes vybrané časové rozpětí.  
 
      ![STATISTIKA](media/time-series-insights-explorer/explorer8.png) 

Nyní jste viděli různé funkce a možnosti dostupné v rámci webové aplikace explorer Statistika časové řady. 

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
>[Diagnostika a řešení problémů ve vašem prostředí Statistika časové řady](time-series-insights-diagnose-and-solve-problems.md)
