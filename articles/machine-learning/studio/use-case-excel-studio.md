---
title: Migrace analýz z Excelu
titleSuffix: ML Studio (classic) - Azure
description: Porovnání lineárních regresních modelů v Excelu a v Azure Machine Learning Studio (klasické)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 85bae9bfc10460b51935c6eb1e14e3a3dd816a8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217799"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio-classic"></a>Migrace analýz z Excelu do Azure Machine Learning Studio (klasická)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> *Kate Baroni* a *Ben Boatman* jsou architekti podnikových řešení v centru excelence Společnosti Microsoft Pro přehledy dat. V tomto článku popisují jejich zkušenosti migrace existující regresní analýzy sady na cloudové řešení pomocí Azure Machine Learning Studio (klasické).

## <a name="goal"></a>Cíl

Náš projekt začal s ohledem na dva cíle: 

1. Pomocí prediktivní analýzy můžete zlepšit přesnost měsíčních prognóz příjmů naší organizace 
2. Azure Machine Learning Studio (klasické) můžete potvrdit, optimalizovat, zvýšit rychlost a škálovat naše výsledky. 

Stejně jako mnoho firem, naše organizace prochází měsíčním procesem předpovídání příjmů. Náš malý tým obchodních analytiků byl pověřen používáním Azure Machine Learning Studio (klasické) k podpoře procesu a zlepšení přesnosti prognózy. Tým strávil několik měsíců shromažďováním dat z více zdrojů a spouštěním atributů dat prostřednictvím statistické analýzy identifikující klíčové atributy relevantní pro prognózu prodeje služeb. Dalším krokem bylo zahájení vytváření prototypů statistických regresních modelů na datech v aplikaci Excel. Během několika týdnů jsme měli regresní model aplikace Excel, který překonal aktuální procesy prognózy oblasti a financí. To se stalo základním výsledkem predikce. 

Poté jsme udělali další krok k přesunutí naší prediktivní analýzy do studia (klasické), abychom zjistili, jak studio (klasické) může zlepšit prediktivní výkon.

## <a name="achieving-predictive-performance-parity"></a>Dosažení prediktivní výkonnostní parity
Naší první prioritou bylo dosažení parity mezi regresními modely Studio (classic) a Excel. Vzhledem ke stejným datům a stejnému rozdělení pro data školení a testování jsme chtěli dosáhnout prediktivní parity výkonu mezi aplikacemi Excel a Studio (klasika). Zpočátku jsme selhali. Model aplikace Excel překonal studio (klasický) model. Selhání bylo způsobeno nedostatečným pochopením nastavení základního nástroje ve studiu (klasika). Po synchronizaci s produktovým týmem Studio (classic) jsme lépe porozuměli základnímu nastavení potřebnému pro naše datové sady a dosáhli parity mezi oběma modely. 

### <a name="create-regression-model-in-excel"></a>Vytvoření regresního modelu v Excelu
Naše regrese aplikace Excel používá standardní lineární regresní model nalezený v nástroji Excel Analysis ToolPak. 

Vypočítali jsme *průměrnou absolutní % chybu* a použili ji jako míru výkonu modelu. Trvalo 3 měsíce, než se dospělo k pracovnímu modelu pomocí aplikace Excel. Přinesli jsme hodně učení do Studia (klasický) experiment, který nakonec byl přínosný pro pochopení požadavků.

### <a name="create-comparable-experiment-in-studio-classic"></a>Vytvořit srovnatelný experiment ve studiu (klasické)
Postupovali jsme takto, abychom vytvořili náš experiment ve Studiu (klasika): 

1. Nahrál datovou sadu jako csv soubor do studia (klasický) (velmi malý soubor)
2. Vytvoření nového experimentu a použití modulu [Vybrat sloupce v datové sadě][select-columns] k výběru stejných datových funkcí, které se používají v Excelu 
3. Pomocí modulu [Rozdělit data][split] (s režimem *relativního výrazu)* můžete data rozdělit do stejných trénovacích datových sad, jako tomu bylo v excelu. 
4. Experimentováno s modulem [lineární regrese][linear-regression] (pouze výchozí možnosti), zdokumentováno a porovnání výsledků s naším regresním modelem aplikace Excel

### <a name="review-initial-results"></a>Zkontrolovat počáteční výsledky
Zpočátku model Aplikace Excel jasně překonal studiový (klasický) model: 

|  | Excel | Studio (Classic) |
| --- |:---:|:---:|
| Výkon | | |
| <ul style="list-style-type: none;"><li>Upravený r-čtverec</li></ul> |0.96 |Není dostupné. |
| <ul style="list-style-type: none;"><li>Koeficient <br />Stanovení</li></ul> |Není dostupné. |0,78<br />(nízká přesnost) |
| Průměrná absolutní chyba |$9.5M |$ 19.4M |
| Průměrná absolutní chyba (%) |6.03% |12.2% |

Když jsme spustili náš proces a výsledky vývojáři a datoví vědci v týmu Machine Learning, rychle poskytli několik užitečných tipů. 

* Při použití modulu [lineární regrese][linear-regression] v aplikaci Studio (klasické), jsou k dispozici dvě metody:
  * Online Gradient Descent: Může být vhodnější pro větší-měřítku problémy
  * Obyčejné nejmenší čtverce: Toje metoda, kterou většina lidí myslí, když slyší lineární regresi. Pro malé datové sady mohou být běžné nejmenší čtverce vhodnější volbou.
* Zvažte úpravu parametru Hmotnost regularizace L2, abyste zlepšili výkon. Ve výchozím nastavení je nastavena na 0,001, ale pro naši malou datovou sadu ji nastavíme na 0,005, abychom zlepšili výkon. 

### <a name="mystery-solved"></a>Záhada vyřešena!
Když jsme použili doporučení, dosáhli jsme stejného základního výkonu ve studiu (klasické) jako v aplikaci Excel: 

|  | Excel | Studio (klasické) (Iniciály) | Studio (klasické) w / Nejmenší čtverce |
| --- |:---:|:---:|:---:|
| Označená hodnota |Skutečné hodnoty (číselné) |Stejné |Stejné |
| Studenta |Analýza dat > aplikace Excel -> regrese |Lineární regrese. |Lineární regrese |
| Možnosti studenta |Není dostupné. |Ve výchozím nastavení |obyčejné nejmenší čtverce<br />L2 = 0,005 |
| Sada dat |26 řádků, 3 funkce, 1 štítek. Všechny číselné. |Stejné |Stejné |
| Rozdělení: Vlak |Excel vyškolený na prvních 18 řádcích, testovaných na posledních 8 řádcích. |Stejné |Stejné |
| Rozdělit: Test |Vzorec regrese aplikace Excel aplikovaný na posledních 8 řádků |Stejné |Stejné |
| **Výkon** | | | |
| Upravený r-čtverec |0.96 |Není dostupné. | |
| Determinační koeficient |Není dostupné. |0,78 |0.952049 |
| Průměrná absolutní chyba |$9.5M |$ 19.4M |$9.5M |
| Průměrná absolutní chyba (%) |<span style="background-color: 00FF00;">6.03%</span> |12.2% |<span style="background-color: 00FF00;">6.03%</span> |

Kromě toho koeficienty aplikace Excel ve srovnání dobře s váhy funkcí v azure trénovaný model:

|  | Koeficienty aplikace Excel | Váhy funkcí Azure |
| --- |:---:|:---:|
| Zachytit/zkreslení |19470209.88 |19328500 |
| A |0.832653063 |0.834156 |
| Součást B |11071967.08 |11007300 |
| Funkce C |25383318.09 |25140800 |

## <a name="next-steps"></a>Další kroky
Chtěli jsme využívat webovou službu Machine Learning v aplikaci Excel. Naši obchodní analytici spoléhají na Excel a potřebovali jsme způsob, jak zavolat webovou službu Machine Learning s řadou dat aplikace Excel a nechat ji vrátit předpovídanou hodnotu aplikaci Excel. 

Také jsme chtěli optimalizovat náš model pomocí možností a algoritmů dostupných ve Studiu (klasika).

### <a name="integration-with-excel"></a>Integrace s Excelem
Naším řešením bylo zprovoznit náš model regrese strojového učení vytvořením webové služby z trénovaného modelu. Během několika minut byla webová služba vytvořena a mohli bychom ji zavolat přímo z aplikace Excel a vrátit předpokládanou hodnotu výnosů. 

Část *Řídicí panel webových služeb* obsahuje sešit aplikace Excel ke stažení. Sešit je dodáván s předformátovaným rozhraním API webové služby a vloženými informacemi o schématu. Po kliknutí na *stáhnout sešit aplikace Excel*se sešit otevře a můžete jej uložit do místního počítače. 

![Stažení excelového sešitu z řídicího panelu webových služeb](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

Když je sešit otevřený, zkopírujte předdefinované parametry do modré části Parametr, jak je znázorněno níže. Po zadání parametrů excel zavolá na webovou službu Machine Learning a předpokládané popisky s výsledky se zobrazí v zelené části Předpovídané hodnoty. Sešit bude nadále vytvářet předpovědi pro parametry na základě trénovaného modelu pro všechny položky řádků zadané v části Parametry. Další informace o použití této funkce najdete v [tématu Využití webové služby Azure Machine Learning web service z Aplikace Excel](consuming-from-excel.md). 

![Šablona Excelsešit připojení k nasazené webové služby](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>Optimalizace a další experimenty
Teď, když jsme měli směrný plán s naším modelem aplikace Excel, jsme se posunuli vpřed k optimalizaci našeho modelu lineární regrese strojového učení. Použili jsme modul [Filter-Based Feature Selection][filter-based-feature-selection] ke zlepšení našeho výběru počátečních datových prvků a to nám pomohlo dosáhnout zlepšení výkonu o 4,6% průměrná absolutní chyba. Pro budoucí projekty budeme používat tuto funkci, která by nám mohla ušetřit týdny v iterace prostřednictvím datových atributů najít správnou sadu funkcí pro modelování. 

Dále plánujeme zahrnout další algoritmy, jako je [Bayesian][bayesian-linear-regression] nebo [Boosted Decision Trees][boosted-decision-tree-regression] v našem experimentu pro porovnání výkonu. 

Pokud chcete experimentovat s regresí, dobrou datovou sadou, kterou můžete vyzkoušet, je ukázková datová sada regrese energetické účinnosti, která má velké množství číselných atributů. Datová sada je k dispozici jako součást ukázkové datové sady v aplikaci Studio (classic). Můžete použít různé výukové moduly předpovědět buď topné zatížení nebo chladicí zatížení. Níže uvedená tabulka je porovnání výkonu různých regresních poznatků oproti datové sadě energetické účinnosti, která předpovídá cílovou proměnnou Chladicí zatížení: 

| Model | Průměrná absolutní chyba | Střední kvadratická chyba kořenového adresáře | Relativní absolutní chyba | Relativní kvadratrovaná chyba | Determinační koeficient |
| --- | --- | --- | --- | --- | --- |
| Posílený rozhodovací strom |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Lineární regrese (gradientní sestup) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Regrese neurální sítě |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Lineární regrese (obyčejné nejmenší čtverce) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Klíčové stánek s jídlem
Naučili jsme se hodně ze spuštění regrese aplikace Excel a experimentů Studio (klasické) paralelně. Vytvoření základního modelu v aplikaci Excel a jeho porovnání s modely pomocí [lineární regrese strojového][linear-regression] učení nám pomohlo naučit studio (klasika) a objevili jsme příležitosti ke zlepšení výběru dat a výkonu modelu. 

Také jsme zjistili, že je vhodné použít [výběr funkcí založených na filtru][filter-based-feature-selection] k urychlení budoucích projektů předpovědi. Použitím výběru funkcí na vaše data můžete vytvořit vylepšený model ve studiu (klasické) s lepším celkovým výkonem. 

Možnost přenést prediktivní analytické prognózy ze studia (klasické) do aplikace Excel systémově umožňuje významné zvýšení schopnosti úspěšně poskytovat výsledky širokému publiku podnikových uživatelů. 

## <a name="resources"></a>Prostředky
Zde jsou některé zdroje, které vám pomohou pracovat s regresí: 

* Regrese v aplikaci Excel. Pokud jste nikdy nezkoušeli regresi v aplikaci Excel, tento kurz usnadňuje:[https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* Regrese vs prognózování. Tyler Chessman napsal blogový článek vysvětlující, jak dělat prognózy časových řad v aplikaci Excel, který obsahuje dobrý popis lineární regrese pro začátečníky. [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* Obyčejné nejmenší čtverce lineární regrese: Nedostatky, problémy a úskalí. Pro úvod a diskusi o regresi: [ https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

