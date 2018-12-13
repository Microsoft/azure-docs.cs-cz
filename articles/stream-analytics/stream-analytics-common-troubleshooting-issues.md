---
title: Běžné problémy pro řešení potíží v Azure Stream Analytics
description: Tento článek popisuje některé běžné problémy v Azure Stream Analytics a kroky, chcete-li tyto problémy vyřešit.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: a2c7ceae342124f06fcfe8dc18b1a69f7176f4e1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090971"
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Běžné problémy v Stream Analytics a postup řešení potíží

## <a name="troubleshoot-malformed-input-events"></a>Řešení potíží s události nemají správný formát vstupu

 Serializace potíže jsou způsobeny, když vstupní datový proud vaší úlohy Stream Analytics obsahuje špatně vytvořené zprávy. Například může být poškozená zpráva způsobené chybějící závorka nebo chybějící závorka v objektu JSON nebo nesprávný časové razítko formátu v poli čas. 
 
 Když úloha Stream Analytics přijímá chybnou zprávu z vstup, zahodí a upozorní uživatele s upozorněním. Symbol upozornění se zobrazí na **vstupy** dlaždici vaší úlohy Stream Analytics (Tento znak upozornění existuje za předpokladu, že je úloha ve spuštěném stavu):

![Vstupy dlaždici na řídicím panelu Azure Stream Analytics](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Další informace zobrazíte povolte diagnostické protokoly, chcete-li zobrazit podrobnosti upozornění. Pro vstupní události nemají správný formát, protokoly spuštění obsahovat zprávu, která bude vypadat takto: "zpráva: nešlo deserializovat vstupní události z prostředku <blob URI> jako dokumenty json". 

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

1. Přejděte na vstupní dlaždice a klikněte na tlačítko Zobrazit upozornění.

2. Vstupní podrobnosti o dlaždici se zobrazí sada upozornění s podrobnostmi o problému. Tady je ukázková zpráva upozornění, toto upozornění zobrazuje oddíl, posun a pořadová čísla níž se nachází poškozená data JSON. 

   ![Vstupní zpráva upozornění s posunem](media/stream-analytics-malformed-events/warning-message-with-offset.png)

3. Chcete-li získat data JSON, který má nesprávný formát, spusťte kód CheckMalformedEvents.cs. Je k dispozici v tomto příkladu [úložiště ukázek Githubu](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Tento kód čte ID oddílu, posun a vytiskne data, která se nachází v tento posun. 

4. Jakmile načtete data, můžete analyzovat a opravit formát serializace.

5. Můžete také [číst události ze služby IoT Hub pomocí Průzkumníka Service Bus](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="delayed-output"></a>Zpožděné výstupu

### <a name="first-output-is-delayed"></a>První výstupní je zpožděno.
Při spuštění úlohy Stream Analytics se číst události vstupu, ale ve výstupu vytvořených v některých případech může dojít ke zpoždění.

Velké časové hodnoty prvků dočasných dotazů může přispět ke zpoždění výstup. Vytvořit správnou výstup přes velký časová okna, úlohu streamování spuštění podle čtení dat z nejnovějších možných doba (až sedmi dnů zpětně) tak, aby vyplnil časový interval. Během této doby není vytvořen žádný výstup, až do dokončení můžete projít čtení nevyřízené události vstupu. Tento problém může docházet, pokud systém upgraduje datových proudů úloh, proto restartování úlohy. Tyto upgrady obvykle dojde k jednou každých několik měsíců. 

Proto používejte podle vlastního uvážení při návrhu dotazu Stream Analytics. Pokud používáte velké časové okno (více než několik hodin až sedm dní) pro dočasné prvky v syntaxi dotazu úlohy by mohlo způsobit zpoždění v první výstupní při spuštění nebo restartování úlohy.  

Jeden omezení rizik pro tento druh první výstupní zpoždění je pomocí technik paralelizace dotazů (dělení dat), nebo přidat víc jednotek streamování, pro zvýšení propustnosti, dokud úloha zachytí.  Další informace najdete v tématu [klíčových faktorů při vytváření úlohy Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)

Těchto faktorů, které ovlivňují včasnosti první výstup, který je generován:

1. Použití agregací (skupiny podle z Přeskakujícího, kdy se skok provádí a klouzavé windows)
   - Přeskakujícího nebo kdy se skok provádí agregace okno se výsledky generují na konci časového rámce okna. 
   - Pro posuvné okno výsledky jsou generovány, pokud událost zadá nebo ji opustí posuvné okno. 
   - Pokud máte v úmyslu použít velkých okna velikost (> 1 hodina), doporučujeme zvolit skákající nebo posuvné okno, kde můžete zobrazit výstup častěji.

2. Použijte dočasné spojení (spojení s DATEDIFF)
   - Shody jsou generovány, co nejdříve po obou stranách odpovídající události doručení.
   - Na konci okna DATEDIFF s ohledem na levé straně každé události je generován data, která nemá shodu (LEVÉ vnější spojení).

3. Použijte dočasné analytických funkcí (ISFIRST, LAST a PRODLEVA s dobou trvání LIMIT)
   - Pro analytické funkce je výstup vygenerován pro každou událost, neexistuje žádné zpoždění.

### <a name="output-falls-behind"></a>Zpozdit výstup
Během normálního úlohy Pokud zjistíte, že výstupu úlohy se opožďuje za (déle a delší latence), můžete určit hlavní příčiny, prozkoumáním těchto faktorů:
- Určuje, zda je omezené příjem dat jímky
- Určuje, zda je omezené nadřazený zdroj
- Určuje, zda zpracování logiky v dotazu je náročné na výpočetní prostředky

Chcete-li zobrazit tyto podrobnosti najdete na webu Azure Portal vyberte úlohu streamování a vyberte **diagram úloh**. Pro každý vstupní je na oddíl nevyřízených položek událostí metriku. Pokud metrika nevyřízených položek událostí narůstá, je indikátor, že jsou omezeny systémové prostředky. Potenciálně, která je z důvodu omezení výstupní jímky nebo vysoké využití procesoru. Další informace o použití diagramu úloh najdete v tématu [řízené daty ladění pomocí diagramu úloh](stream-analytics-job-diagram-with-metrics.md).

## <a name="handle-duplicate-records-in-azure-sql-database-output"></a>Popisovač duplicitní záznamy ve výstupu Azure SQL Database

Při konfiguraci Azure SQL database jako výstup do úlohy Stream Analytics hromadně vloží záznamy do cílové tabulky. Obecně platí, Azure stream analytics zaručuje [alespoň jednou]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) do výstupní jímky jeden stále [dosáhnout přesně-jednou]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) SQL výstup, pokud tabulka SQL má jedinečné omezení definovaný. 

Omezení jedinečných klíčů jsou nastaveny na tabulku SQL, a během existují duplicitní záznamy nebude vložen do tabulky SQL, Azure Stream Analytics odebere duplicitní záznam. Rozdělí data do dávek a rekurzivně vkládání dávek, dokud nebude nalezen jeden duplicitní záznam. Pokud úloha streamování se značný počet duplicitních řádků, toto rozdělení a vložit procesu má ignorovat duplicity jednu po druhé, což je méně efektivní a časově náročné. Pokud se zobrazí několik klíčových porušení upozornění v protokolu aktivit za poslední hodinu, je pravděpodobné, že výstup SQL zpomalení celý projekt. 

Chcete-li vyřešit tento problém, měli byste [nastavit index]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) , který způsobuje narušení klíče když zapnete možnost IGNORE_DUP_KEY. Povolením této možnosti umožní duplicitní hodnoty mají ignorovat během operace hromadného vložení SQL a SQL Azure jednoduše vytvoří upozornění místo chyby. Azure Stream Analytics už nevytváří chyby porušení primárního klíče.

Při konfiguraci IGNORE_DUP_KEY pro několik typů indexů, mějte na paměti následující poznámky:

* Nelze nastavit IGNORE_DUP_KEY na primární klíč nebo jedinečné omezení, která používá příkaz ALTER INDEX, je nutné vyřadit a znovu vytvořte index.  
* Můžete nastavit možnost IGNORE_DUP_KEY na hodnotu pomocí příkazu ALTER INDEX pro jedinečný index, který se liší od primární klíč nebo jedinečné omezení a vytvořit pomocí definition CREATE INDEX nebo INDEX.  
* IGNORE_DUP_KEY neplatí pro indexy columnstore, protože nelze vynutit jedinečnost takové indexů.  

## <a name="next-steps"></a>Další postup
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
