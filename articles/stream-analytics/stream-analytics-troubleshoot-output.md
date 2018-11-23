---
title: Řešení potíží s Azure Stream Analytics výstupy
description: Tento článek popisuje postupy pro řešení problémů s připojením vašeho výstup do úlohy Azure Stream Analytics.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 5124e06296962f1b673c77bfe318ff48d7fb11aa
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291319"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Řešení potíží s Azure Stream Analytics výstupy

Tato stránka popisuje běžné problémy s výstupní připojení a jak je řešit.

## <a name="output-not-produced-by-job"></a>Výstup není vytvořený úlohou 
1.  Ověření připojení k výstupy pomocí **Test připojení** tlačítko pro každý výstup.

2.  Podívejte se na [ **monitorování metrik** ](stream-analytics-monitoring.md) na **monitorování** kartu. Protože se agregují hodnoty, metriky jsou zpožděné o několik minut.
    - Pokud vstupní události > 0, úlohy je možné číst vstupní data. Nejsou-li vstupní události > 0, pak:
      - Chcete-li zjistit, zda zdroj dat obsahuje platná data, zkontrolujte pomocí [Service Bus Exploreru](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Tato kontrola platí, pokud úloha používá jako vstup Centrum událostí.
      - Zkontrolujte, zda je formát serializace dat a kódování dat podle očekávání.
      - Pokud úloha používá centra událostí, zkontrolujte, zda je text zprávy *Null*.
      
    - Pokud chyby převodu dat > 0 a tato hodnota roste, může platit následující:
      - Výstupní událost neodpovídá schématu cílové jímky. 
      - Schéma událostí nemusí odpovídat definovaná nebo očekávanému schématu událostí v dotazu.
      - Datové typy některých polí v události nemusí odpovídat očekávání.
      
    - Pokud běhové chyby > 0, znamená to, že úloha může přijímat data, ale generuje chyby při zpracování dotazu.
      - Chcete-li najít chyby, přejděte na [protokoly auditu](../azure-resource-manager/resource-group-audit.md) a filtrujte *se nezdařilo* stav.
      
    - Pokud situací > 0 a výstupní události = 0, znamená to, že je splněna jedna z následujících akcí:
      - Zpracování dotazu mělo za výsledek nulu výstupních událostí.
      - Události nebo jejich pole může být poškozený, což vede k nulový výstup po zpracování dotazů.
      - Úlohu se nepodařilo k zápisu dat do výstupní jímky důvodů připojení nebo ověřování.
      
    - Ve všech výše uvedených chyb případech zprávy provozních protokolů vysvětlují další podrobnosti (včetně, co se děje), s výjimkou případů, kdy logika dotazu vyfiltrovala všechny události. Pokud zpracování několika událostí generuje chyby, Stream Analytics zaznamená první tři chybové zprávy stejného typu během 10 minut do provozních protokolů. Potom potlačí další identické chyby a zobrazí se zpráva, která čte "Chybám dochází příliš rychle a že potlačují se."
    
## <a name="job-output-is-delayed"></a>Výstup úlohy je zpožděno.

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

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Upozornění na porušení klíče s výstupem Azure SQL Database

Při konfiguraci Azure SQL database jako výstup do úlohy Stream Analytics hromadně vloží záznamy do cílové tabulky. Obecně platí, Azure stream analytics zaručuje [alespoň jednou]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) do výstupní jímky jeden stále [dosáhnout přesně-jednou]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) SQL výstup, pokud tabulka SQL má jedinečné omezení definovaný. 

Omezení jedinečných klíčů jsou nastaveny na tabulku SQL, a během existují duplicitní záznamy nebude vložen do tabulky SQL, Azure Stream Analytics odebere duplicitní záznam. Rozdělí data do dávek a rekurzivně vkládání dávek, dokud nebude nalezen jeden duplicitní záznam. Pokud úloha streamování se značný počet duplicitních řádků, toto rozdělení a vložit procesu má ignorovat duplicity jednu po druhé, což je méně efektivní a časově náročné. Pokud se zobrazí několik klíčových porušení upozornění v protokolu aktivit za poslední hodinu, je pravděpodobné, že výstup SQL zpomalení celý projekt. 

Chcete-li vyřešit tento problém, měli byste [nastavit index]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) , který způsobuje narušení klíče když zapnete možnost IGNORE_DUP_KEY. Povolením této možnosti umožní duplicitní hodnoty mají ignorovat během operace hromadného vložení SQL a SQL Azure jednoduše vytvoří upozornění místo chyby. Azure Stream Analytics už nevytváří chyby porušení primárního klíče.

Při konfiguraci IGNORE_DUP_KEY pro několik typů indexů, mějte na paměti následující poznámky:

* Nelze nastavit IGNORE_DUP_KEY na primární klíč nebo jedinečné omezení, která používá příkaz ALTER INDEX, je nutné vyřadit a znovu vytvořte index.  
* Můžete nastavit možnost IGNORE_DUP_KEY na hodnotu pomocí příkazu ALTER INDEX pro jedinečný index, který se liší od primární klíč nebo jedinečné omezení a vytvořit pomocí definition CREATE INDEX nebo INDEX.  
* IGNORE_DUP_KEY neplatí pro indexy columnstore, protože nelze vynutit jedinečnost takové indexů.  

## <a name="get-help"></a>Podpora

Potřebujete další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
