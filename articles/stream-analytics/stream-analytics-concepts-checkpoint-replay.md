---
title: Vytváření kontrolních bodů a opětovného přehrání konceptů v Azure Stream Analytics
description: Tento článek popisuje koncepty a opětovné přehrání konceptů obnovení úloh v Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: df31f8538bb9eabeca37fe4c52c4443fd447e415
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015314"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Koncepty kontrolního bodu a přehrání v úlohách Azure Stream Analytics
Tento článek popisuje interní body kontrolního bodu a přehrání v Azure Stream Analytics a dopad na obnovení úlohy. Při každém spuštění úlohy Stream Analytics se informace o stavu udržují interně. Tyto informace o stavu se pravidelně ukládají do kontrolního bodu. V některých scénářích se pro obnovení úlohy použijí informace kontrolního bodu, pokud dojde k selhání nebo upgradu úlohy. V jiných případech se kontrolní bod nedá použít k obnovení a je potřeba ho přehrát znovu.

## <a name="stateful-query-logic-in-temporal-elements"></a>Logika stavových dotazů v dočasnách prvcích
Jednou z jedinečných možností Azure Stream Analytics úlohy je provádět stavové zpracování, jako jsou například agregovaná okna, dočasná spojení a dočasné analytické funkce. Každý z těchto operátorů uchovává informace o stavu při spuštění úlohy. Maximální velikost okna pro tyto prvky dotazu je sedm dní. 

Koncept dočasného okna se zobrazí v několika Stream Analyticsch prvcích dotazu:
1. Okna agregace (seskupení podle bubnu, skákající a posuvných oken)

2. Dočasná spojení (spojit s DATEDIFF)

3. Dočasné analytické funkce (jako první, poslední a PRODLEVa s dobou omezení)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Obnovení úlohy z neúspěšného uzlu, včetně upgradu operačního systému
Pokaždé, když se spustí úloha Stream Analytics, interně se škáluje tak, aby fungovala v několika pracovních uzlech. Každý stav pracovního uzlu je každé několik minut popsán příslušně, což pomáhá obnovit systém, pokud dojde k selhání.

V některých případech se může stát, že určitý pracovní uzel selže nebo dojde k upgradu operačního systému pro tento pracovní uzel. K automatickému obnovení Stream Analytics získá nový uzel v pořádku a předchozí stav pracovního uzlu se obnoví z posledního dostupného kontrolního bodu. Aby bylo možné pokračovat v práci, je potřeba pro obnovení stavu z doby, kdy je kontrolní bod proveden, malé množství opakování. Mezera obnovy je obvykle jenom několik minut. Je-li pro úlohu vybrán dostatečný počet jednotek streamování, je třeba provést opětovné přehrání. 

V plně paralelním dotazu je čas potřebný k zachytávání po selhání pracovního uzlu úměrný:

[míra vstupních událostí] x [Délka mezery]/[počet zpracovávaných oddílů]

Pokud někdy obdržíte významné zpoždění při zpracování kvůli selhání uzlu a upgradu operačního systému, zvažte, že dotaz je plně paralelní a škálovat úlohu pro přidělení dalších jednotek streamování. Další informace najdete v tématu [škálování Azure Stream Analytics úlohy za účelem zvýšení propustnosti](stream-analytics-scale-jobs.md).

Pokud tento druh procesu obnovení probíhá, aktuální Stream Analytics nezobrazuje sestavu.

## <a name="job-recovery-from-a-service-upgrade"></a>Obnovení úlohy z upgradu služby 
Microsoft občas provede upgrade binárních souborů, které spouštějí Stream Analytics úlohy ve službě Azure. V této době se spuštěné úlohy uživatelů upgradují na novější verzi a úloha se restartuje automaticky. 

V současné době není formát kontrolního bodu obnovení zachován mezi upgrady. V důsledku toho je nutné obnovit stav dotazu streamování výhradně pomocí techniky přehrání. Aby bylo možné, aby Stream Analytics úlohy přehrály přesně stejný vstup než předtím, je důležité nastavit zásady uchovávání dat pro zdrojová data alespoň na velikosti oken v dotazu. V takovém případě může docházet k nesprávným nebo částečným výsledkům během upgradu služby, protože zdrojová data nemusí být dostatečně velká, aby bylo možné zahrnout celou velikost okna.

Velikost potřebného přehrání je obecně úměrná velikosti okna vynásobené průměrnou sazbou události. Například pro úlohu se vstupní rychlostí 1000 událostí za sekundu je velikost okna větší než jedna hodina považována za velikost většího počtu přehrání. Po dobu až jedné hodiny bude nutné znovu zpracovat data, aby bylo možné stav inicializovat, aby bylo možné vytvořit úplné a správné výsledky, což může způsobit zpožděný výstup (žádný výstup) pro delší dobu. Dotazy bez oken nebo jiných dočasných operátorů, jako je například `JOIN` nebo `LAG` , by měly nulové přehrání.

## <a name="estimate-replay-catch-up-time"></a>Odhad doby zachycení přehrání
Chcete-li odhadnout délku zpoždění v důsledku upgradu služby, můžete postupovat podle tohoto postupu:

1. Načtěte vstupní centrum událostí s dostatečnými daty, abyste pokryli největší velikost okna v dotazu, a to za očekávanou rychlost události. Časové razítko události by mělo být v průběhu tohoto časového období blízko času, jako by to byl živý vstupní kanál. Pokud máte například v dotazu tříleté okno, pošlete události do centra událostí po dobu tří dní a pokračujte v posílání událostí. 

2. Spusťte úlohu **hned** jako počáteční čas. 

3. Změřte čas mezi časem spuštění a při vygenerování prvního výstupu. Čas je přibližný, kolik času by úloha během upgradu služby znamenala.

4. Pokud je zpoždění příliš dlouhé, zkuste vytvořit oddíly úlohy a zvýšit počet služby SUs, aby bylo zatížení rozděleno na více uzlů. Případně zvažte zmenšení velikosti oken v dotazu a proveďte další agregaci nebo jiné stavové zpracování výstupu vypracovaného úlohou Stream Analytics v jímky pro příjem dat (například pomocí Azure SQL Database).

Pro všeobecnou pozornost stability služeb během upgradu důležitých úloh je potřeba zvážit spouštění duplicitních úloh v spárovaných oblastech Azure. Další informace najdete v tématu [záruka Stream Analytics spolehlivost úloh během aktualizací služby](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Obnovení úlohy od uživatele, který zahájil zastavení a zahájení
Chcete-li upravit syntaxi dotazu na úlohu streamování nebo upravit vstupy a výstupy, je nutné úlohu zastavit, aby provedla změny a Upgradoval návrh úlohy. V takových scénářích když uživatel zastaví úlohu streamování a znovu ji spustí, scénář obnovení je podobný upgradu služby. 

Data kontrolního bodu nelze použít pro uživatele iniciující restart úlohy. K odhadu zpoždění výstupu během restartování použijte stejný postup, jak je popsáno v předchozí části, a pokud je zpoždění příliš dlouhé, použijte podobné zmírnění.

## <a name="next-steps"></a>Další kroky
Další informace o spolehlivosti a škálovatelnosti najdete v těchto článcích:
- [Kurz: nastavení výstrah pro úlohy Azure Stream Analytics](stream-analytics-set-up-alerts.md)
- [Škálování Azure Stream Analytics úlohy za účelem zvýšení propustnosti](stream-analytics-scale-jobs.md)
- [Záruka Stream Analytics spolehlivost úloh během aktualizací služby](stream-analytics-job-reliability.md)
