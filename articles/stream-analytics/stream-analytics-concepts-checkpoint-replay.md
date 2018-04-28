---
title: Kontrolní bod a opětovného přehrání koncepty obnovení úlohy v Azure Stream Analytics
description: Tento článek popisuje kontrolního bodu a opětovného přehrání koncepty obnovení úlohy v Azure Stream Analytics.
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: 1a7cb6c5d9c3383b127ce38ae21bb2dc811e1f2e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Kontrolní bod a opětovného přehrání koncepty v úlohy Azure Stream Analytics
Tento článek popisuje interní kontrolní bod a opětovného přehrání koncepty v Azure Stream Analytics a dopad na těch, které mít na úlohu obnovení. Při každém spuštění úlohy Stream Analytics informace o stavu zachovaný interně. Aby se informace o stavu pravidelně uloží do kontrolní bod. V některých scénářích kontrolního bodu informace slouží pro úlohy obnovení, pokud dojde k selhání úlohy nebo upgradu. Za jiných okolností kontrolní bod nelze použít pro obnovení a je nezbytné přehrání.

## <a name="stateful-query-logic-in-temporal-elements"></a>Stavová dotazu logiky v elementech dočasné
Jedním z jedinečné možnosti úlohy Azure Stream Analytics je provést stavová zpracování, např. agregací v časových oknech, dočasných spojení a dočasné analytické funkce. Každý z těchto operátorů udržuje informace o stavu spuštění úlohy. Je maximální velikost okna pro tyto elementy dotazu je sedm dní. 

Koncept dočasné okno se zobrazí v několika elementy dotaz služby Stream Analytics:
1. Oddílové agregace (skupiny podle z Přeskakujícího, posílání a klouzavé windows)

2. Dočasné spojení (spojení s DATEDIFF)

3. Dočasné analytické funkce (ISFIRST, poslední a PRODLEVA s LIMIT DURATION)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Úloha obnovení po selhání uzlu, včetně upgradu operačního systému
Při každém spuštění úlohy Stream Analytics interně jej je škálovat na více systémů pro práci mezi několika uzly pracovního procesu. Každý pracovní uzel stavu vytvořen kontrolní bod každých několik minut, který pomáhá systém obnovit, pokud dojde k chybě.

V některých případech daného pracovního uzlu může selhat nebo může dojít upgradu operačního systému, pro tento uzel pracovního procesu. Pokud chcete obnovit automaticky, Stream Analytics získá nový uzel v pořádku a má obnovit stav uzlu předchozí pracovníka z nejnovější dostupné kontrolního bodu. Chcete-li pokračovat v práci, je nutné k obnovení stavu od okamžiku, kdy kontrolní bod se pořídí malé množství opětovného přehrání. Mezera obnovení je obvykle jen několik minut. Pokud dostatečný počet jednotek streamování, jsou vybraná pro úlohu, by se měly přehrávání rychle dokončit. 

Ve plně paralelní dotazu je úměrná době potřebné k zachycení po selhání uzlu pracovního procesu:

[počet vstupních událostí] x [délka mezera] / [počet zpracování oddílů]

Pokud zjistíte někdy prodleva významné zpracování z důvodu selhání uzlu a operační systém upgradovat, zvažte provedení dotazu plně paralelní a škálování úlohu přidělit více jednotek streamování. Další informace najdete v tématu [škálování úlohu služby Azure Stream Analytics, pokud chcete zvýšit propustnost](stream-analytics-scale-jobs.md).

Aktuální Stream Analytics nezobrazuje sestavy, když probíhá tento druh proces obnovení.

## <a name="job-recovery-from-a-service-upgrade"></a>Úloha obnovení z aktualizace služby 
Microsoft příležitostně upgraduje binární soubory, které spouštějí úlohy Stream Analytics ve službě Azure. V těchto časech spuštěné úlohy uživatelů nebudou upgradovány na novější verzi a úloha se automaticky restartuje. 

V současné době není mezi upgrady zachován formát kontrolního bodu obnovení. V důsledku toho se streamování dotazu musí být obnovit stav zcela pomocí opětovného přehrání techniky. Chcete-li povolit úlohy Stream Analytics opakování přesně stejný vstup z před je důležité nastavit zásady uchovávání informací pro zdroj dat na alespoň okna velikostí v dotazu. Tak neučiníte může vést k nesprávné nebo částečné výsledky během upgradu služby, vzhledem k tomu, že zdrojová data nemusí být zachováno dostatečně zpět zahrnout velikost celé okno.

Obecně platí množství opětovného přehrání potřeby je úměrná velikosti okna násobí hodnotou průměrný počet událostí. Jako příklad pro úlohu s vstupní počet 1000 událostí za sekundu velikost okna, která je větší než jedna hodina je považován za velikost velké opětovného přehrání. Pro dotazy s velikostí velkých opakování můžete pozorovat zpožděné výstup (žádný výstup) některé delší dobu. 

## <a name="estimate-replay-catch-up-time"></a>Odhad opětovného přehrání opravný času
Chcete-li odhadnout délka zpoždění kvůli upgradu služby, proveďte tento postup:

1. Načtěte vstupní centra událostí s dostatečného množství dat tak, aby pokrývalo největší velikost okna v dotazu, rychlostí Očekávaná událost. Časové razítko události by měl být blízko skutečný čas v rámci tohoto časového období, jako Pokud je za provozu vstup informačního kanálu. Pokud máte okno den 3 v dotazu, odesílání událostí do centra událostí tři dny a pokračovat v odesílání událostí. 

2. Spustí úlohu pomocí **nyní** jako počáteční datum. 

3. Měření čas mezi časem spuštění a když se vygeneruje první výstup. Čas je hrubý kolik zpoždění úlohy by způsobit během upgradu služby.

4. Pokud zpoždění je příliš dlouhý, zkuste rozdělit na oddíly úlohu a zvýšit počet služby SUs, takže je zatížení šíření na další uzly. Případně zvažte snížení velikosti okna v dotazu a provést další agregace nebo jiných stateful zpracování na výstup vytvořený v úloze Stream Analytics v podřízené jímky (například pomocí Azure SQL database).

Pro obecné služby stabilitu problém během upgradu zvláště kritické úlohy zvažte spuštění duplicitní úlohy v spárované oblastech Azure. Další informace najdete v tématu [úlohy Stream Analytics záruku spolehlivosti se během aktualizace služby](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Úloha obnovení z spuštění a zastavení iniciované uživatelem
Úpravy syntaxe dotazu na úlohu streamování, nebo upravte vstupy a výstupy, je potřeba úlohy zastaveny, aby se provést změny a upgradujte úlohy návrhu. V takových scénářů když uživatel zastaví úlohu streamování a znovu ji spustí scénář obnovení je podobná aktualizace služby. 

Data kontrolního bodu nelze použít pro restartování úlohy inicializované uživatelem. K zjištění přibližné hodnoty zpoždění výstupu během takové restartování, použijte stejný postup, jak je popsáno v předchozí části a použít podobné zmírnění dopadů, pokud zpoždění je příliš dlouhý.

## <a name="next-steps"></a>Další postup
Další informace o spolehlivosti a škálovatelnosti najdete v těchto článcích:
- [Kurz: Nastavení výstrah pro úlohy Azure Stream Analytics](stream-analytics-set-up-alerts.md)
- [Škálování úlohu služby Azure Stream Analytics, pokud chcete zvýšit propustnost](stream-analytics-scale-jobs.md)
- [Zajistit spolehlivost úlohy Stream Analytics během aktualizace služby](stream-analytics-job-reliability.md)
