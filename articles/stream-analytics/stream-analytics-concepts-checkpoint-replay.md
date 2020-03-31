---
title: Kontrolní bod a koncepty obnovení opětovného přehrání ve službě Azure Stream Analytics
description: Tento článek popisuje koncepty obnovení úloh se kontrolním bodem a přehráním úloh ve službě Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f5bb2b97d7da770828c2f4f03167483ad2044c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426390"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Koncepty kontrolního bodu a přehrávání v pracovních úlohách Azure Stream Analytics
Tento článek popisuje interní kontrolní bod a přehrání koncepty ve službě Azure Stream Analytics a dopad těchto mají na obnovení úlohy. Při každém spuštění úlohy Stream Analytics jsou informace o stavu udržovány interně. Tyto informace o stavu jsou pravidelně ukládány do kontrolního bodu. V některých scénářích informace o kontrolním bodu se používá pro obnovení úlohy, pokud dojde k selhání úlohy nebo upgradu. Za jiných okolností kontrolní bod nelze použít pro obnovení a přehrání je nezbytné.

## <a name="stateful-query-logicin-temporal-elements"></a>Logika stavového dotazu v časových prvcích
Jednou z jedinečných funkcí úlohy Azure Stream Analytics je provádět stavové zpracování, jako jsou agregace v okně, časová spojení a časové analytické funkce. Každý z těchto operátorů uchovává informace o stavu při spuštění úlohy.Maximální velikost okna pro tyto prvky dotazu je sedm dní. 

Koncept časového okna se zobrazí v několika prvcích dotazu Služby Stream Analytics:
1. Okenní agregáty (GROUP BY of Tumbling, Hopping, a Posuvné okna)

2. Časová spojení (SPOJENÍ s DATEDIFF)

3. Časové analytické funkce (ISFIRST, LAST a MAS s LIMIT DURATION)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Obnovení úlohy z selhání uzlu, včetně upgradu operačního systému
Pokaždé, když se spustí úloha Stream Analytics, interně se škálovat tak, aby fungovala napříč více pracovními uzly. Stav každého pracovního uzlu je každých několik minut kontrolním bodem, což pomáhá systému obnovit, pokud dojde k selhání.

Někdy může dojít k selhání daného pracovního uzlu nebo může dojít k upgradu operačního systému pro tento pracovní uzel. Chcete-li obnovit automaticky, Stream Analytics získá nový uzel v pořádku a stav předchozího pracovního uzlu se obnoví z nejnovějšího dostupného kontrolního bodu. Chcete-li pokračovat v práci, malé množství přehrání je nutné obnovit stav od okamžiku, kdy je kontrolní bod přijata. Obvykle je mezera obnovení jen několik minut. Pokud je pro úlohu vybráno dostatečné množství jednotek streamování, mělo by být přehrávání dokončeno rychle. 

V plně paralelní dotaz čas potřebný k doplnění po selhání pracovního uzlu je úměrná:

[rychlost vstupních událostí] x [délka mezery] / [počet oddílů zpracování]

Pokud někdy zjistíte významné zpoždění zpracování z důvodu selhání uzlu a upgradu operačního systému, zvažte vytvoření dotazu plně paralelní a škálování úlohy přidělit více jednotek streamování. Další informace najdete v [tématu Škálování úlohy Azure Stream Analytics pro zvýšení propustnost .](stream-analytics-scale-jobs.md)

Aktuální Stream Analytics nezobrazuje přehled, když probíhá tento druh procesu obnovení.

## <a name="job-recovery-from-a-service-upgrade"></a>Obnovení úlohy z upgradu služby 
Microsoft příležitostně upgraduje binární soubory, které spouštějí úlohy Stream Analytics ve službě Azure. V těchto časech jsou spuštěné úlohy uživatelů upgradovány na novější verzi a úloha se automaticky restartuje. 

V současné době není zachován formát kontrolního bodu obnovení mezi upgrady. V důsledku toho musí být stav dotazu streamování obnoven zcela pomocí techniky přehrání. Chcete-li povolit úlohy Stream Analytics přehrát přesně stejný vstup z dříve, je důležité nastavit zásady uchovávání informací pro zdrojová data alespoň velikosti oken v dotazu. Pokud tak neučiníte, může dojít k nesprávné nebo částečné výsledky během upgradu služby, protože zdrojová data nemusí být zachována dostatečně daleko zpět zahrnout velikost celého okna.

Obecně je množství potřebného přehrání úměrné velikosti okna vynásobené průměrnou mírou událostí. Například pro úlohu s vstupní rychlostí 1000 událostí za sekundu velikost okna větší než jedna hodina se považuje za velikost velké přehrání. Až jednu hodinu dat může být nutné znovu zpracovat k inicializaci stavu, aby mohl vytvářet úplné a správné výsledky, což může způsobit zpožděný výstup (žádný výstup) po delší dobu. Dotazy bez oken nebo jiných časových `JOIN` `LAG`operátorů, jako je nebo , by měly nulový záznam.

## <a name="estimate-replay-catch-up-time"></a>Odhad doby dohánění přehrání
Chcete-li odhadnout délku zpoždění z důvodu upgradu služby, můžete postupovat podle této techniky:

1. Načtěte vstupní centrum událostí s dostatečnými daty, která pokryjí největší velikost okna v dotazu, při očekávané rychlosti událostí. Časové razítko událostí by mělo být v blízkosti času nástěnných hodin po celou tuto dobu, jako by se jedná o živý vstupní kanál. Pokud máte například v dotazu 3denní okno, odesílejte události do centra událostí po dobu tří dnů a pokračujte v odesílání událostí. 

2. Spusťte úlohu pomocí **funkce Nyní** jako čas zahájení. 

3. Změřte čas mezi časem zahájení a generováním prvního výstupu. Čas je hrubý, kolik zpoždění úlohy vznikne během upgradu služby.

4. Pokud zpoždění je příliš dlouhá, zkuste rozdělit úlohu a zvýšit počet su, takže zatížení je rozložena do více uzlů. Případně zvažte zmenšení velikosti oken v dotazu a proveďte další agregaci nebo jiné stavové zpracování na výstupu vytvořeném úlohou Stream Analytics v následném jímce (například pomocí databáze Azure SQL).

Obecné obavy o stabilitu služeb během upgradu klíčových úloh zvažte spuštění duplicitních úloh ve spárovaných oblastech Azure. Další informace naleznete [v tématu Guarantee Stream Analytics spolehlivost úlohběhem aktualizací služeb](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Obnovení úlohy z uživatelem iniciované zastavení a spuštění
Chcete-li upravit syntaxi dotazu na úlohu streamování nebo upravit vstupy a výstupy, úloha musí být zastavena, aby bylo možné provést změny a upgradovat návrh úlohy. V takových scénářích, když uživatel zastaví úlohu streamování a spustí ji znovu, scénář obnovení je podobný upgradu služby. 

Data kontrolního bodu nelze použít pro restartování úlohy iniciované uživatelem. Chcete-li odhadnout zpoždění výstupu během takového restartování, použijte stejný postup, jak je popsáno v předchozí části a použít podobné zmírnění, pokud zpoždění je příliš dlouhá.

## <a name="next-steps"></a>Další kroky
Další informace o spolehlivosti a škálovatelnosti naleznete v těchto článcích:
- [Kurz: Nastavení výstrah pro úlohy Azure Stream Analytics](stream-analytics-set-up-alerts.md)
- [Škálování úlohy Azure Stream Analytics za účelem zvýšení propustnosti](stream-analytics-scale-jobs.md)
- [Zaručit spolehlivost úloh Stream Analytics během aktualizací služeb](stream-analytics-job-reliability.md)
