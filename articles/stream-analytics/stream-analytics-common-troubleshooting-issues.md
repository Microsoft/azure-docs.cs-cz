---
title: Běžné problémy řešení v Azure Stream Analytics
description: Tento článek popisuje některé běžné problémy v Azure Stream Analytics a kroky k řešení těchto problémů.
services: stream-analytics
author: jasonwhowell
manager: kfile
ms.author: jasonh
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: e04d1072acee635235b0a5bd8465ca38c861017b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Běžné problémy při Stream Analytics a kroky pro řešení potíží

## <a name="troubleshoot-malformed-input-events"></a>Řešení potíží s událostmi chybná vstupní

 Pokud vstupní datový proud úlohy Stream Analytics obsahuje poškozených zpráv způsobuje problémy serializace. Například chybnou zprávu může být způsobené chybějící závorky nebo chybějící složené závorce v objektu JSON, nebo jsou nesprávné časové razítko formátu v poli čas. 
 
 Když úloha Stream Analytics přijme zprávu o poškozený z vstup, zahodí zprávy a upozorní uživatele s upozorněním. Symbol upozornění se zobrazí na **vstupy** dlaždice úlohy Stream Analytics (registrace upozornění existuje, dokud je úloha ve stavu spuštění):

![Vstupy dlaždice](media/stream-analytics-malformed-events/inputs_tile.png)

Další informace najdete povolení protokolů diagnostiky k zobrazení podrobností o upozornění. Chybná vstupních událostech, protokoly spouštění obsahovat položku s zprávu, která vypadá takto: "zpráva: nešlo deserializovat vstupní událostí z prostředku <blob URI> jako json". 

### <a name="troubleshooting-steps"></a>Řešení potíží

1. Přejděte na dlaždici vstupní a klikněte na tlačítko Zobrazit upozornění.

2. Na dlaždici vstupní podrobnosti zobrazí sadu upozornění s podrobnostmi o problému. Toto je upozornění na příkladu, upozornění se zobrazuje oddíl, posun a pořadová čísla je poškozená data JSON. 

   ![Upozornění s posunem](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Chcete-li získat data JSON, která nemá správný formát, spusťte CheckMalformedEvents.cs. Je k dispozici v tomto příkladu [úložiště GitHub ukázky](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Tento kód čte ID oddílu, posun a výtisků data, která se nachází v tomto posun. 

4. Jakmile načtete data, můžete analyzovat a opravit formát serializace. 

## <a name="delayed-output"></a>Zpožděné výstup

### <a name="first-output-is-delayed"></a>První výstup je zpožděno.
Při spuštění úlohy Stream Analytics se čtou vstupních událostech, ale ve výstupu se vytváří v některých případech může být zpoždění.

Velké časové hodnoty v elementech dočasné dotaz můžete přispívat ke zpoždění výstup. K vytvoření správné výstupu přes velké časových oken, úloha streamování spuštění načtením dat z nejnovější možných dobu (až sedm dní před) k vyplnění časový interval. Během této doby se žádný výsledek až do dokončení opravný čtení nezpracovaných vstupní událostí. Tento problém můžete surface v případě systému upgradu úloha streamování, proto restartování úlohy. Tyto upgrady obecně dojít jednou každých několik měsíců. 

Proto pomocí svého rozhodnutí při návrhu dotazu Stream Analytics. Pokud používáte velké časové okno (více než několik hodin, až sedm dní) pro dočasné prvky v syntaxi dotazu úlohy, může dojít ke zpoždění v první výstupní při spuštění nebo restartování úlohy.  

Jeden zmírnění dopadů pro tento typ první výstupní zpoždění je použít techniky dotazu paralelizace (dělení dat), nebo přidejte další jednotky streamování ke zlepšení propustnosti, dokud úloha zachytí.  Další informace najdete v tématu [klíčových faktorů při vytváření úlohy Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)

Tyto faktory vliv včasnosti první výstupu, který se vygeneruje:

1. Použití agregací v časových oknech (skupiny pomocí nástroje Přeskakujícího, posílání a klouzavé windows)
   - Přeskakujícího nebo posílání okno agregace, jsou generovány, výsledky na konci časový rámec okna. 
   - Pro posuvné okno výsledky jsou generovány, když událost zadá nebo ji opustí posuvné okno. 
   - Pokud máte v úmyslu použít velikost okna velké (> 1 hodina), je nejlepší zvolit skákající nebo posuvné okno, takže uvidíte výstup častěji.

2. Použití dočasných spojení (spojení s DATEDIFF)
   - Odpovídá jsou generovány ihned po obou stranách odpovídající události doručení.
   - Data, která nemá odpovídající (LEVÉHO vnějšího spojení) je generována na konci tohoto okna DATEDIFF s ohledem na každé události na levé straně.

3. Použijte dočasné analytické funkce (ISFIRST, poslední a PRODLEVA s LIMIT DURATION)
   - Pro analytické funkce výstup se generuje pro každou událost, neexistuje žádné zpoždění není.

### <a name="output-falls-behind"></a>Výstup nevejde.
Při běžném provozu úlohy Pokud zjistíte, že výstup úlohy je návratem za (déle a delší latence), je přesné určení požadovaného, tak, že prověří tyto faktory:
- Jestli je omezen podřízené jímka
- Jestli je omezen nadřazený zdroj
- Jestli zpracování logiky v dotazu je náročné na výkon

Chcete-li zobrazit podrobnosti, na portálu Azure vyberte úlohu streamování a vyberte **úlohy diagram**. Pro každý vstupní je za oddílu nevyřízené události metriky. Pokud metriku nevyřízených událostí i nadále, je jako ukazatel, že jsou omezené systémové prostředky. Potenciálně, je z důvodu omezení výstupní jímku nebo vysoké využití procesoru. Další informace o použití diagram úlohy najdete v části [řízené daty ladění pomocí úlohy diagram](stream-analytics-job-diagram-with-metrics.md).

## <a name="handle-duplicate-records-in-azure-sql-database-output"></a>Popisovač duplicitní záznamy ve službě Azure SQL Database výstupu

Při konfiguraci Azure SQL database jako výstup do úlohy Stream Analytics, hromadně vloží záznamy do cílové tabulky. Obecně platí, služby Azure stream analytics zaručuje [alespoň jednou doručení]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) do výstupní jímku jeden stále [dosáhnout přesně-jednou doručení]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) do SQL výstup, když SQL tabulka má jedinečné omezení definovaná. 

Jakmile jedinečné omezení klíčů se nastaví na tabulku SQL a existují duplicitní záznamy vkládání do tabulky SQL, Azure Stream Analytics odebere duplicitní záznam. Rozdělí data do dávky a vkládání na listy, dokud nebude nalezen jeden duplicitní záznam rekurzivně. Pokud je úloha streamování řadu duplicitní řádky, toto rozdělení a vložit proces má ignorovat duplicity jeden po druhém, což je méně efektivní a časově náročné. Pokud se zobrazí více klíčů porušení upozornění v protokolu aktivit v rámci poslední hodinu, je pravděpodobné, že výstupu SQL je zpomalení celé úlohy. 

Chcete-li vyřešit tento problém, měli byste [konfigurace index]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) , který způsobuje selhání klíče povolením možnosti IGNORE_DUP_KEY. Tuto možnost povolíte duplicitní hodnoty, který se má ignorovat SQL během hromadné vložení a SQL Azure jednoduše vytvoří zprávu s upozorněním místo k chybě. Azure Stream Analytics už nevytváří chyby porušení primárního klíče.

Všimněte si následujících připomínky při konfiguraci IGNORE_DUP_KEY pro několik typů indexy:

* Na primární klíč nebo jedinečné omezení, které používá příkaz ALTER INDEX nelze nastavit IGNORE_DUP_KEY, je třeba vyřadit a znovu vytvořte index.  
* Můžete nastavit možnost IGNORE_DUP_KEY pomocí příkazu ALTER INDEX pro jedinečný index, který se liší od primární klíč nebo jedinečné omezení a vytvořit pomocí definice CREATE INDEX nebo INDEX.  
* IGNORE_DUP_KEY se nevztahuje na indexy úložiště sloupců, protože nelze vynutit jedinečnost tyto indexy.  

## <a name="next-steps"></a>Další postup
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
