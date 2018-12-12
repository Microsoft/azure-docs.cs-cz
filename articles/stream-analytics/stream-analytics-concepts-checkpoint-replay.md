---
title: Kontrolní bod a opětovného přehrání úlohy obnovení koncepty ve službě Azure Stream Analytics
description: Tento článek popisuje kontrolní bod a opětovného přehrání úlohy obnovení koncepty ve službě Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 9dcfbd4b5fcc8462c88b16f585424166ecd3d499
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088236"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Kontrolní bod a opětovného přehrání koncepty v úlohách Azure Stream Analytics
Tento článek popisuje interní kontrolní bod a opětovného přehrání koncepty v Azure Stream Analytics a sníží se dopad, že ty mají na úlohu obnovení. Pokaždé, když spustí úlohu Stream Analytics, informace o stavu se udržuje interně. Informace o tomto stavu se uloží do kontrolní bod pravidelně. V některých scénářích kontrolní bod informace slouží pro úlohu obnovení, pokud dojde k selhání úlohy nebo upgradu. Za jiných okolností kontrolní bod nelze použít pro obnovení a opakování hry je nezbytné.

## <a name="stateful-query-logicin-temporal-elements"></a>Stavových dotazů logiky v elementech dočasné
Jeden jedinečné funkce úlohy Azure Stream Analytics je stavové zpracování, jako jsou agregace v okně, dočasné spojení a dočasné analytických funkcí. Každý z těchto operátorů uchovává informace o stavu, pokud úloha běží. Je maximální velikost okna pro tyto elementy dotazu je sedm dní. 

Koncept dočasné okno se zobrazí v několika elementy dotazu Stream Analytics:
1. Agregace v okně (skupiny podle z Přeskakujícího, kdy se skok provádí a klouzavé windows)

2. Dočasná spojení (spojení s DATEDIFF)

3. Dočasné analytických funkcí (ISFIRST, LAST a PRODLEVA s dobou trvání LIMIT)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Úloha obnovení po selhání uzlu, včetně upgradu operačního systému
Pokaždé, když spustí úlohu Stream Analytics, interně se horizontálně práci mezi několika uzly pracovního procesu. Každý pracovní uzel je stav byl vytvořen kontrolní bod každých několik minut, což pomáhá systému obnovit, pokud dojde k chybě.

V některých případech dané pracovní uzel může selhat nebo může dojít upgradu operačního systému pro tento uzel pracovního procesu. Automaticky obnovit, Stream Analytics získá nový uzel v pořádku a je z nejnovější dostupné kontrolního bodu obnovit předchozí pracovní uzel stav. Chcete-li pokračovat v práci, je potřeba obnovit stav z čas pořizování kontrolního bodu malé množství opakování. Mezera obnovení je obvykle jenom pár minut. Když vyberete dostatek jednotek streamování pro úlohu, by se měly přehrávání rychle dokončit. 

V plně paralelní dotaz je úměrná čas potřebný k zachycení po selhání uzlu pracovního procesu:

[počet vstupních událostí] x [délka mezera] / [počet zpracování oddílů]

Pokud někdy sledovat zpoždění operací zpracování z důvodu selhání uzlu a operační systém upgradovat, zvažte učinění dotaz plně paralelní a škálovat úlohy přidělit víc jednotek streamování. Další informace najdete v tématu [škálovat úlohy Azure Stream Analytics pro zvýšení prostupnosti](stream-analytics-scale-jobs.md).

Aktuální Stream Analytics nezobrazuje sestavy, když tento druh procesu obnovení probíhá.

## <a name="job-recovery-from-a-service-upgrade"></a>Úlohu obnovení s upgradem služby 
Microsoft příležitostně upgraduje binární soubory, na kterých běží úlohy Stream Analytics ve službě Azure. Za těchto okolností spuštěné úlohy uživatelů jsou upgradovány na novější verzi a úloha automaticky restartuje. 

V současné době formát kontrolního bodu obnovení nezachová se mezi upgrady. V důsledku toho stavu streamování dotazu, je potřeba obnovit zcela způsobem opakování. Aby bylo možné povolit úlohy Stream Analytics přehrát přesně stejný vstup z dříve, je potřeba nastavit zásady uchovávání informací pro zdroj dat na alespoň v okně velikosti v dotazu. Pokud tak neučiníte může vést k nesprávné nebo částečné výsledky během upgradu služby, protože zdroj dat nemusí být zachováno dostatečně daleko zahrnout velikost celé okno.

Obecně je přímo úměrná velikosti okna vynásobené průměrný počet událostí množství potřebné před zneužitím. Jako příklad pro úlohu s objemem vstupní 1000 událostí za sekundu velikost okna, která je větší než jedna hodina se považuje za mají velikost velkých opětovného přehrání. Až jednu hodinu dat bude muset být znovu zpracován inicializovat stav některé delší dobu, může vytvořit úplné a správné výsledky, což může způsobit zpoždění výstup (žádný výstup). Dotazy s žádné windows nebo jiné dočasné operátory, jako jsou `JOIN` nebo `LAG`, bude mít nulovou opětovného přehrání.

## <a name="estimate-replay-catch-up-time"></a>Odhad opakování můžete projít času
Pokud chcete odhadnout délka zpoždění kvůli upgradu služby, provedením tento postup:

1. Načtěte vstupní Centrum událostí s dostatečným množstvím dat pro největší velikost okna v dotazu, rychlostí Očekávaná událost. Časové razítko události musí být blízko skutečný čas v průběhu tohoto období, jako jestli jde živé vstupní informačního kanálu. Pokud máte časové období 3 dní do dotazu, odesílání událostí do centra událostí po dobu tří dnů a pokračovat v odesílání událostí. 

2. Začít používat úlohu **nyní** jako počáteční datum. 

3. Měří čas mezi časem spuštění a pokud je první výstup vygenerován. Čas je hrubý kolik zpoždění úlohy by se uživateli při upgradu služby.

4. Pokud zpoždění je příliš dlouhý, zkuste rozdělit úlohy a zvýšit počet su, tak zatížení rozložen do více uzlů. Případně zvažte snížení velikosti okna v dotazu a provést další agregace nebo jiné stavové zpracování na výstup vytvořený úlohou Stream Analytics v jímce podřízený (například využívající Azure SQL database).

Obecné služby stabilitu problém během upgradu zásadně důležité úlohy zvažte spuštění duplicitní úloh ve spárovaných oblastech Azure. Další informace najdete v tématu [záruku Stream Analytics spolehlivost úloh během aktualizace služby](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Úloha obnovení z uživatelem iniciované zastavení a spuštění
Úpravy syntaxe dotazu na úlohu streamování nebo upravit vstupy a výstupy úlohy je potřeba zastavit provést změny a upgrade úlohy návrhu. V takových scénářích když uživatel zastaví úlohu streamování a spustí znovu, scénář obnovení je podobný upgradu služby. 

Data kontrolního bodu nelze použít pro restartování úlohy iniciované uživatelem. K odhadu zpoždění výstup během těchto restartování, použijte stejný postup, jak je popsáno v předchozí části a využít podobné zmírnění dopadů, pokud je zpoždění je příliš dlouhý.

## <a name="next-steps"></a>Další postup
Další informace o spolehlivosti a škálovatelnosti najdete v těchto článcích:
- [Kurz: Nastavení výstrah pro úlohy Azure Stream Analytics](stream-analytics-set-up-alerts.md)
- [Škálovat úlohy Azure Stream Analytics pro zvýšení prostupnosti](stream-analytics-scale-jobs.md)
- [Záruka spolehlivosti úlohy Stream Analytics během aktualizace služeb](stream-analytics-job-reliability.md)
