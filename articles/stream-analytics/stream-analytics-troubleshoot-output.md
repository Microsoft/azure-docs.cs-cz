---
title: Řešení potíží s výstupy Azure Stream Analytics
description: Tento článek popisuje techniky řešení potíží s vašimi výstupními připojeními v Azure Stream Analytics úlohách.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 5652df0cf142af2ff96590368892530abcb3d667
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133221"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Řešení potíží s výstupy Azure Stream Analytics

Tento článek popisuje běžné problémy s Azure Stream Analyticsmi výstupními připojeními, jak řešit problémy s výstupem a jak tyto problémy vyřešit. Mnoho kroků pro řešení potíží vyžaduje, aby byly pro vaši úlohu Stream Analytics povolené prostředky a další diagnostické protokoly. Pokud nemáte Povolené protokoly prostředků, přečtěte si téma [řešení potíží s Azure Stream Analytics pomocí protokolů prostředků](stream-analytics-job-diagnostic-logs.md).

## <a name="output-not-produced-by-job"></a>Výstup nevytvořený úlohou

1.  Ověřte připojení ke výstupům pomocí tlačítka **Testovat připojení** pro každý výstup.

2.  Podívejte se na [**sledování metrik**](stream-analytics-monitoring.md) na kartě **monitorování** . Vzhledem k tomu, že jsou hodnoty agregované, jsou metriky zpožděné o několik minut.
   * Pokud jsou vstupní události větší než 0, může úloha číst vstupní data. Pokud vstupní události nejsou větší než 0, dojde k problému se vstupem úlohy. Informace o řešení problémů se vstupním připojením najdete v tématu [řešení potíží se vstupními připojeními](stream-analytics-troubleshoot-input.md) .
   * Pokud jsou chyby převodu dat větší než 0 a stoupání, přečtěte si část [Azure Stream Analytics chyby dat](data-errors.md) , kde najdete podrobné informace o chybách při převodu dat.
   * Pokud jsou chyby za běhu větší než 0, může vaše úloha přijímat data, ale při zpracování dotazu generuje chyby. Chyby najdete v [protokolech auditu](../azure-resource-manager/management/view-activity-logs.md) a ve stavu filtru při *selhání* .
   * Pokud InputEvents je větší než 0 a OutputEvents se rovná 0, jedna z následujících možností je true:
      * Zpracování dotazu mělo za výsledek nulu výstupních událostí.
      * Události nebo pole mohou být poškozena a výsledkem zpracování dotazu je nulový výstup.
      * Úloha nemohla odeslat data do výstupní jímky pro účely připojení nebo ověřování.

   Ve všech dříve uvedených chybových případech vysvětlují zprávy o operacích, které obsahují další podrobnosti (včetně toho, co se děje), s výjimkou případů, kdy logika dotazu vyfiltruje všechny události. Pokud zpracování více událostí generuje chyby, jsou chyby shrnuty každých 10 minut.

## <a name="job-output-is-delayed"></a>Výstup úlohy je zpožděný

### <a name="first-output-is-delayed"></a>První výstup je zpožděný.

Po spuštění úlohy Stream Analytics se načtou vstupní události, ale v některých případech může dojít ke zpoždění generování výstupu.

Dlouhé hodnoty času v dočasných prvcích dotazů mohou přispět k zpoždění výstupu. Aby se vytvořil správný výstup v rámci velkých oken času, úloha streamování se spustí tak, že si přečte data z poslední možné doby (až do sedmi dnů předá) a vyplní časový interval. Během této doby se nevytvoří žádný výstup, dokud není dokončená čtení nezpracovaných vstupních událostí. Tento problém se může nacházet v případě, že systém upgraduje úlohy streamování, takže úlohu restartuje. K těmto inovacím obvykle dochází jednou za několik měsíců.

Proto při navrhování Stream Analyticsho dotazu použijte volitelné rozhodnutí. Pokud používáte velký časový interval (více než několik hodin, až sedm dní) pro dočasné prvky v syntaxi dotazu úlohy, může to způsobit zpoždění prvního výstupu při spuštění nebo restartování úlohy.  

Jedním z rizik pro tento druh prvního zpoždění výstupu je použití technik paralelního zpracování dotazů (rozdělení dat) nebo přidání dalších jednotek streamování pro zvýšení propustnosti, dokud se úloha nedosáhne.  Další informace najdete v tématu [týkajícím se vytváření úloh Stream Analytics](stream-analytics-concepts-checkpoint-replay.md) .

Tyto faktory ovlivňují časovou osu prvního generovaného výstupu:

1. Použití agregovaných agregačních hodnot (skupin podle bubnu, skákající a klouzavého okna)
   - V případě agregací nebo skákající oken se výsledky generují na konci časového rámce okna.
   - Pro posuvné okno jsou výsledky generovány, když událost zadá nebo ukončí posuvné okno.
   - Pokud plánujete použít velkou velikost okna (> 1 hodina), je nejlepší zvolit skákající nebo posuvné okno, abyste mohli výstup zobrazit častěji.

2. Použití dočasná spojení (spojení s DATEDIFF)
   - Shody jsou generovány, jakmile se dorazí na obě strany odpovídajících událostí.
   - Data, která nemají shodu (levé vnější spojení), se generují na konci okna DATEDIFF s ohledem na každou událost na levé straně.

3. Použití dočasných analytických funkcí (například FIRST, LAST a LAG s TRVÁNÍm LIMITu)
   - Pro analytické funkce je výstup vygenerován pro každou událost, neexistuje žádná prodleva.

### <a name="output-falls-behind"></a>Výstup spadá pod

Pokud při běžném provozu úlohy zjistíte, že výstup úlohy je na konci (delší a delší latence), můžete určit hlavní příčiny tím, že prozkoumáte tyto faktory:
- Bez ohledu na to, jestli je jímka pro příjem dat omezená
- Bez ohledu na to, jestli je nadřazený zdroj omezený
- Určuje, jestli je logika zpracování v dotazu náročná na výpočetní výkon.

Chcete-li tyto podrobnosti zobrazit, vyberte v Azure Portal úlohu streamování a vyberte **diagram úlohy**. U každého vstupu je metrika události nevyřízených položek na oddíl. Pokud se metrika události nevyřízených položek stále zvyšuje, je indikátorem, že systémové prostředky jsou omezené. Potenciálně to může být způsobeno omezením výstupní jímky nebo vysokým PROCESORem. Další informace o používání diagramu úloh naleznete v tématu [ladění řízené daty pomocí diagramu úloh](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Upozornění na porušení klíče s výstupem Azure SQL Database

Když nakonfigurujete službu Azure SQL Database jako výstup do Stream Analytics úlohy, hromadně vloží záznamy do cílové tabulky. Obecně platí, že Azure Stream Analytics garantuje [alespoň jedno doručení](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) do výstupní jímky, takže když tabulka SQL má definováno jedinečné omezení, může pro výstup SQL stále [dosáhnout přesného doručení]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) .

Jakmile budou v tabulce SQL nastavena omezení jedinečnosti klíčů a v tabulce SQL jsou vloženy duplicitní záznamy, Azure Stream Analytics odstraní duplicitní záznam. Rozdělí data na dávky a rekurzivně vloží dávky, dokud nebude nalezen jeden duplicitní záznam. Pokud má úloha streamování velký počet duplicitních řádků, musí tento proces rozdělení a vložení ignorovat duplicitní hodnoty po jednom, což je méně efektivní a časově náročné. Pokud se v protokolu aktivit během poslední hodiny zobrazí zpráva s upozorněním na porušení klíčů, je pravděpodobnější, že váš výstup SQL zpomaluje celou úlohu.

Chcete-li tento problém vyřešit, je nutné [nakonfigurovat index]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) , který způsobuje porušení klíče, povolením možnosti IGNORE_DUP_KEY. Povolením této možnosti umožníte, aby se duplicitní hodnoty v SQL ignorovaly během hromadných vložení a SQL Azure jednoduše vznikne varovná zpráva namísto chyby. Azure Stream Analytics již nevytváří chyby narušení primárního klíče.

Při konfiguraci IGNORE_DUP_KEY pro několik typů indexů Pamatujte na následující poznámky:

* Nemůžete nastavit IGNORE_DUP_KEY pro primární klíč nebo jedinečné omezení, které používá příkaz ALTER INDEX, je nutné index vyřadit a znovu vytvořit.  
* Možnost IGNORE_DUP_KEY můžete nastavit pomocí příkazu ALTER INDEX pro jedinečný index, který se liší od omezení primárního klíče a JEDINEČNosti a vytvořen pomocí definice vytvořit INDEX nebo INDEX.  

* IGNORE_DUP_KEY se nevztahují na indexy úložiště sloupců, protože u takových indexů nemůžete vymáhat jedinečnost.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Názvy sloupců jsou použitay nižšími než Azure Stream Analytics
Při použití původní úrovně kompatibility (1,0) Azure Stream Analytics použít ke změně názvů sloupců malými písmeny. Toto chování bylo opraveno v novějších úrovních kompatibility. Aby bylo možné zachovat případ, doporučujeme zákazníkům, aby přešli na úroveň kompatibility 1,1 a novější. Další informace o [úrovni kompatibility pro úlohy Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)najdete v.

## <a name="get-help"></a>Podpora

Pokud potřebujete další pomoc, vyzkoušejte naši [Azure Stream Analytics Fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
