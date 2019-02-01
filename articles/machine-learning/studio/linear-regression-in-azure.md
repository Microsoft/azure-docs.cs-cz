---
title: Migrace analytics z Excelu do Azure Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Porovnání modelů lineární regrese v aplikaci Excel a nástroji Azure Machine Learning Studio
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 12c82d440613078e9f0593ddb9fb6c7fe7b8c362
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488346"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio"></a>Migrace analytics z Excelu do Azure Machine Learning Studio

> *Kate Baroni* a *Robert Boatman* jsou podnikové architekty řešení ve společnosti Microsoft Data Insights bez System Center. V tomto článku popisují práci migrace sady existující regresní analýzy na cloudové řešení, které využívá Azure Machine Learning Studio.

## <a name="goal"></a>Cíl

Náš projekt spustit s dva cíle v úvahu: 

1. Využití prediktivních analýz, aby se zlepšila přesnost naší organizace měsíční výnosy projekce 
2. Pomocí Azure Machine Learning Studio můžete potvrdit, optimalizovat, zvýšení rychlosti a škálování naší výsledků. 

Stejně jako řada podniků naši organizaci prochází měsíční výnosy proces Prognózování. Úkolem naší malý tým specialisty na obchodní analýzu bylo proces a zvyšte přesnost předpovědi pomocí Azure Machine Learning Studio. Tým strávil několik měsíců, shromažďování dat z více zdrojů a s atributy dat prostřednictvím statistická analýza identifikace klíčových atributů, které jsou relevantní pro prognózy prodeje služeb. Dalším krokem bylo začít vytváření prototypů statistické regresní modely na datech v aplikaci Excel. Během pár týdnů měli jsme regresní model aplikace Excel, který byl překonají aktuálního pole a finanční Prognózování procesy. To se výsledek předpovědi směrného plánu. 

Jsme pak trvalo dál přesunem Studio a zjistěte, jak může zlepšit Studio prediktivní výkonu našich prediktivní analýzy.

## <a name="achieving-predictive-performance-parity"></a>Dosažení parity prediktivní výkonu
Naší hlavní prioritou došlo k dosažení parity mezi regresních modelů Studio a Excel. Pro trénování a testování dat zadané na stejná data a stejné rozdělení, chtěli bychom dosažení parity prediktivní výkon mezi Excelem a Studio. Zpočátku se nám nepovedlo. Modelu Excelu překonal Studio modelu. K selhání došlo kvůli nedostatku porozumění nastavení základní nástroje v sadě Studio. Po synchronizaci s produktovým týmem Studio jsme získali lépe pochopit základní nastavení požadovaná pro naše datové sady a dosáhnout rozdíly mezi těmito dvěma modely. 

### <a name="create-regression-model-in-excel"></a>Vytvořit regresní model v aplikaci Excel
Naše aplikace Excel Regrese používá trénování modelu lineární regrese standardní součástí analytické aplikace Excel. 

Můžeme vypočítat *% střední absolutní chyba* a použít ho jako míra výkonu pro model. Jakou trvalo tři měsíce můžete přejít na pracovní model pomocí aplikace Excel. Můžeme převést do režimu velkou část učení do Studio experimentu, který nakonec bylo výhodné v Principy požadavků.

### <a name="create-comparable-experiment-in-studio"></a>Vytvoření srovnatelné experimentu v nástroji Studio
Jsme postupovali podle těchto kroků k vytvoření naší experimentu v nástroji Studio: 

1. Nahrání datové sady jako soubor csv a Studio (velmi malý soubor)
2. Vytvoří nový experiment a použít [výběr sloupců v datové sadě] [ select-columns] modul se vybere stejné funkce data použít v aplikaci Excel 
3. Použít [rozdělení dat] [ split] modulu (s *relativní výraz* režimu) rozdělení dat do stejné cvičných datových sad, jako kdyby byla provedena v aplikaci Excel 
4. Experimentovali s [lineární regrese] [ linear-regression] modulu (výchozí možnosti pouze), zdokumentované a porovnat výsledky pro náš regresní model aplikace Excel

### <a name="review-initial-results"></a>Počáteční výsledků kontroly
Zpočátku modelu Excelu jasně překonal modelu Studio: 

|  | Excel | Studio |
| --- |:---:|:---:|
| Výkon | | |
| <ul style="list-style-type: none;"><li>Upravit čtverec R</li></ul> |0.96 |neuvedeno |
| <ul style="list-style-type: none;"><li>Koeficient <br />Stanovení</li></ul> |neuvedeno |0.78<br />(nízké přesnost) |
| Střední absolutní chyba |$9.5 MIN |$ 19.4 M |
| Střední absolutní chyba (%) |6.03% |12.2% |

Když jsme spustili náš proces a výsledky používají vývojáři a odborníci přes data v týmu Machine Learning, poskytnou rychle několik užitečných tipů. 

* Při použití [lineární regrese] [ linear-regression] modulu v sadě Studio jsou k dispozici dvě metody:
  * Online sestupu: Může být vhodnější pro problémy pracovat ve větším měřítku
  * Běžné čtverců: Toto je metoda, kterou většina lidí si můžete představit při jejich slyšet lineární regrese. Pro malé datové sady může být běžný čtverců více optimální volbou.
* Vezměte v úvahu úprava parametr L2 Regularizace váha ke zlepšení výkonu. Je ve výchozím nastavení má 0,001, ale pro naše malá datová sada jsme ho nastavte na 0,005 ke zlepšení výkonu. 

### <a name="mystery-solved"></a>Co je vyřešen!
Když jsme použili doporučení, jsme dosáhli stejné základní úroveň výkonu v nástroji Studio jako v Excelu: 

|  | Excel | Studio (výchozí) | Studio plánovaným bodem obnovení kratším čtverců |
| --- |:---:|:---:|:---:|
| Hodnota s popiskem |Skutečné hodnoty (číselné) |Stejné |Stejné |
| Student |Excel -> Data analýzy -> regrese |Lineární regrese. |Lineární regrese |
| Možnosti learner |neuvedeno |Výchozí nastavení |běžné čtverců<br />L2 = 0,005 |
| Datové sady |26 řádků, funkce 3, 1 popisek. Všechny číselné. |Stejné |Stejné |
| Rozdělení: Trénování |Excel trénuje nejprve 18 řádky, testovat na posledních 8 řádků. |Stejné |Stejné |
| Rozdělení: Test |Excelovému vzorci regrese použitý pro řádky posledních 8 |Stejné |Stejné |
| **Výkon** | | | |
| Upravit čtverec R |0.96 |neuvedeno | |
| Koeficient spolehlivosti |neuvedeno |0.78 |0.952049 |
| Střední absolutní chyba |$9.5 MIN |$ 19.4 M |$9.5 MIN |
| Střední absolutní chyba (%) |<span style="background-color: 00FF00;"> 6.03%</span> |12.2% |<span style="background-color: 00FF00;"> 6.03%</span> |

Kromě toho koeficienty Excel porovnání dobře s váhy funkce v Azure trénovaného modelu:

|  | Koeficienty aplikace Excel | Váhy funkce Azure |
| --- |:---:|:---:|
| Zachycení/posun |19470209.88 |19328500 |
| Funkce A |0.832653063 |0.834156 |
| Součást B |11071967.08 |11007300 |
| Funkce jazyka C |25383318.09 |25140800 |

## <a name="next-steps"></a>Další kroky
Chtěli jsme se používání této webové služby Machine Learning v aplikaci Excel. Naši obchodní analytici využívají aplikace Excel a potřebujeme způsob, jak volat webovou službu Machine Learning s řádkem Excelových dat a jeho vrátit předpovězené hodnoty aplikace Excel. 

Také chceme optimalizaci náš model pomocí možnosti a algoritmy, které jsou k dispozici v sadě Studio.

### <a name="integration-with-excel"></a>Integrace s Excelem
Naším řešením bylo zprovoznění náš model strojového učení regrese vytvořením webové služby z trénovaného modelu. Během několika minut webová služba vytvořila a jsme lze volat přímo z aplikace Excel k vrácení hodnoty předpovězené výnosy. 

*Řídicího panelu webové služby* část obsahuje ke stažení Excelový sešit. Sešit obsahuje předem formátovaný webové rozhraní API a schémat informace o služby vložené. Po kliknutí na *stáhněte si Excelový sešit*, sešit se otevře, a můžete ji uložit do místního počítače. 

![][1]

S otevřete sešit zkopírujte předdefinované parametry do modré části, jak je znázorněno níže. Po zadání parametrů, Excel, volá do webové služby Machine Learning a predikované skóre popisků se zobrazí v části zelené Předpovězeným hodnotám. Sešit nadále vytvářet předpovědi pro parametry podle trénovaný model pro všechny položky řádku zadaný v poli parametrů. Další informace o tom, jak tuto funkci používat, naleznete v tématu [využívání webové služby Azure Machine Learning z Excelu](consuming-from-excel.md). 

![][2]

### <a name="optimization-and-further-experiments"></a>Optimalizace a další pokusy
Teď, když jsme měli směrný plán s náš model aplikace Excel, jsme přesunuli dopředu optimalizovat náš Model strojového učení lineární regrese. Jsme použili modulu [výběr součástí na základě filtru] [ filter-based-feature-selection] ke zlepšení našich výběru počáteční data prvky a to nám povedlo dosáhnout zlepšení výkonu 4.6 % znamená absolutní chyba. Pro všechny budoucí projekty budeme používat tuto funkci, která se dá ušetřit nám týdny v iterace v rámci datové atributy k nalezení správné sady funkcí pro modelování. 

Dále jsme chcete zahrnout další algoritmy, jako je [Bayesova] [ bayesian-linear-regression] nebo [vylepšené rozhodovací stromy] [ boosted-decision-tree-regression] v našich experimentu k porovnání výkon. 

Pokud chcete experimentovat s regrese, je dobré datovou sadu, která akci ukázkovou datovou sadou energetickou efektivitu regrese, který má spoustu číselné atributy. Datová sada je dodáván jako součást ukázkových datových sad v nástroji Studio. Škály výukových moduly můžete použít k predikci vytápění zatížení nebo chlazení zatížení. Následující graf je že porovnání výkonu různých regrese učí proti předpověď energetickou účinnost datovou sadu pro Cílová proměnná chlazení zatížení: 

| Model | Střední absolutní chyba | Střední kořenové spolehlivosti chyba | Relativní absolutní chyba | Relativní spolehlivosti chyba | Koeficient spolehlivosti |
| --- | --- | --- | --- | --- | --- |
| Posílený rozhodovací strom |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Lineární regrese (sestupu) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Regrese neuronové sítě |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Lineární regrese (běžný čtverců) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Stěžejní zjištění
Jsme se naučili mnohem ve spuštěné aplikace Excel regrese a experimentů Studio paralelně. Vytvoření základního modelu v Excelu a srovnáním s modely s využitím strojového učení [lineární regrese] [ linear-regression] pomohl nám další Studio a zjistili jsme příležitosti k vylepšení výběr dat a modelu výkon. 

Zjistili jsme také, že je vhodné použít [výběr součástí na základě filtru] [ filter-based-feature-selection] ke zrychlení předpovědi budoucí projekty. S použitím výběr funkcí k vašim datům, můžete vytvořit vylepšené modelu v sadě Studio s lepší výkon. 

Možnost přenášet prediktivních analytických odhady ze sady Studio do aplikace Excel systemically umožňuje významné zvýšení schopnost úspěšně poskytovat výsledky pro cílovou skupinu široké obchodní uživatele. 

## <a name="resources"></a>Zdroje a prostředky
Tady jsou některé prostředky pro dokážete pracovat regrese: 

* Regrese v aplikaci Excel. Pokud jste se pokusili nikdy Regrese v Excelu, v tomto kurzu umožňuje snadno: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Prognózování regrese vs. Tyler Chessman napsal blogový článek s vysvětlením, jak časové řady Prognózování v aplikaci Excel, která obsahuje popis pro začátečníky dobré lineární regrese. [http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts) 
* Běžné nejméně Squares lineární regrese: Chyby, problémy a nástrahy. Úvod a diskuzi o regrese: [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

[1]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

