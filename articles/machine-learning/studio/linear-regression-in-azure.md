---
title: Pomocí lineární regrese v Machine Learning | Microsoft Docs
description: Porovnání modelů lineární regrese v aplikaci Excel a v nástroji Azure Machine Learning Studio
metakeywords: ''
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 417ae6ab-de4f-4bdd-957a-d96133234656
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 2ea5a2720542217d3bb6a0a2b1309312fb74a953
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="using-linear-regression-in-azure-machine-learning"></a>Používání lineární regrese ve službě Azure Machine Learning
> *Kate Baroni* a *Ben Boatman* jsou podnikové řešení architekty ve společnosti Microsoft Data Statistika špičkové Center. V tomto článku popisují jejich prostředí migrace sady analysis existující regrese na cloudové řešení pomocí Azure Machine Learning. 
> 
> 

&nbsp; 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="goal"></a>Cíl
Naše projektu začít s dva cíle v paměti: 

1. Použijte k zajištění přesnosti naší organizace měsíční výnosy projekce prediktivní analýzy 
2. Pomocí Azure Machine Learning k potvrzení, optimalizace, zvýšení rychlosti a škálování naše výsledků. 

Například mnoho firem naší organizace projde měsíční výnosy prognózy procesu. Úkolem naší malý tým obchodní analytici bylo pomocí Azure Machine Learning podporují procesu a zvyšte tak přesnost předpovědi. Tým stráví několik měsíců, shromažďování dat z více zdrojů a spouštění atributy datového prostřednictvím statistické analýzy, identifikace klíčových atributů, které jsou relevantní pro prognózy prodeje služby. Dalším krokem se zahájíte vytváření prototypů statistické regrese modely na data v aplikaci Excel. Během pár týdnů jsme měli regresní model aplikace Excel, který byl outperforming aktuální pole a finanční prognózy procesy. To se stala výsledek předpovědi směrného plánu. 

Potom vzali jsme dalším krokem při přechodu naše prediktivní analýzy na Azure Machine Learning a zjistěte, jak může vylepšit Machine Learning na prediktivní výkon.

## <a name="achieving-predictive-performance-parity"></a>Dosažení paritní prediktivní výkonu
Naše nejvyšší prioritu bylo dosáhnout rozdíly mezi regrese modely Machine Learning a Excel. Zadána stejná data a stejné rozdělení pro trénování a testování dat, jsme chtěli dosáhnout prediktivní výkonu rozdíly mezi aplikace Excel a Machine Learning. Původně se nepovedlo. Model aplikace Excel outperformed model Machine Learning. K selhání došlo z důvodu chybějících Principy nastavení základní nástroj v Machine Learning. Po synchronizaci s produktový tým Machine Learning jsme získávají lépe porozumět základní nastavení požadovaná pro naše sady dat a dosáhnout rozdíly mezi dva modely. 

### <a name="create-regression-model-in-excel"></a>Vytvořit regresní model v aplikaci Excel
Naše aplikace Excel Regrese používá model standardní lineární regrese v aplikaci Excel analytické nalezen. 

Jsme vypočítat *% střední absolutní chyba* a použít jako měření výkonu pro model. Trvalo 3 měsíce přijaty ve model pracovní pomocí aplikace Excel. Můžeme uvést do režimu většinu learning do experimentu Machine Learning Studio, která byla nakonec výhodné v Principy požadavků.

### <a name="create-comparable-experiment-in-azure-machine-learning"></a>Vytvoření porovnatelný z hlediska experimentu v nástroji Azure Machine Learning
Jsme postupovali podle těchto kroků můžete vytvořit naše experimentu v nástroji Machine Learning Studio: 

1. Odesílané datovou sadu jako soubor csv do nástroje Machine Learning Studio (velmi malý soubor)
2. Vytvoří nový experiment a použít [výběr sloupců v datové sadě] [ select-columns] modulu vyberte stejné funkce dat použít v aplikaci Excel 
3. Použít [rozdělení dat] [ split] modulu (s *relativní výraz* režim) k rozdělení dat do stejné datové sady školení, jak bylo v aplikaci Excel 
4. Experimented s [lineární regrese] [ linear-regression] modulu (výchozí možnosti pouze), zdokumentované a porovnání výsledky do našich regresní model aplikace Excel

### <a name="review-initial-results"></a>Zkontrolujte počáteční výsledky
Na první pohled model aplikace Excel jasně outperformed model Machine Learning Studio: 

|  | Excel | Studio |
| --- |:---:|:---:|
| Výkon | | |
| <ul style="list-style-type: none;"><li>Upraví hranaté R</li></ul> |0.96 |neuvedeno |
| <ul style="list-style-type: none;"><li>Koeficient <br />Rozhodnutí</li></ul> |neuvedeno |0.78<br />(nízkou přesnost) |
| Střední absolutní chyba |$9. 5M |$ 19.4 M |
| Střední absolutní chyba (%) |6.03% |12.2% |

Když jsme narazili naše proces a výsledky datových vědců a vývojářů na tým Machine Learning, poskytnou rychle některé užitečné tipy. 

* Při použití [lineární regrese] [ linear-regression] modulu v nástroji Machine Learning Studio, jsou k dispozici dvě metody:
  * Online klesání přechodu: Může být vhodnější pro problémy s větším měřítku
  * Obyčejnou nejmenší čtverce: Toto je metoda, kterou většina lidí zamyslet nad při zazní lineární regrese. Pro malé datové sady může být obyčejnou čtverce nejmenší více optimální volbou.
* Zvažte, postupně je upravujte parametr L2 regulaci váhy ke zlepšení výkonu. Je ve výchozím nastavení má 0,001, ale pro naše malé datové sady jsme ji nastavit na 0,005 ke zlepšení výkonu. 

### <a name="mystery-solved"></a>Mystery vyřeší!
Když jsme použili doporučení, jsme dosáhli stejné základní úroveň výkonu v nástroji Machine Learning Studio jako v aplikaci Excel: 

|  | Excel | Studio (výchozí) | Studio s nejmenší čtverce |
| --- |:---:|:---:|:---:|
| Hodnota s popiskem |Skutečné hodnoty (číslice) |stejné |stejné |
| Student |Excel -> datové analýzy -> regrese |Lineární regrese. |Lineární regrese |
| Možnosti student |neuvedeno |Výchozí hodnoty |obyčejnou nejmenší čtverce<br />L2 = 0,005 |
| Datové sady |26 řádky, funkce 3, 1 popisek. Všechny číselný. |stejné |stejné |
| Rozdělení: Train |Excel trénink na první 18 řádky, otestovali na poslední 8 řádky. |stejné |stejné |
| Rozdělení: Test |Vzorec regrese použitý pro poslední 8 řádky v aplikaci Excel |stejné |stejné |
| **Výkon** | | | |
| Upraví hranaté R |0.96 |neuvedeno | |
| Koeficient spolehlivosti |neuvedeno |0.78 |0.952049 |
| Střední absolutní chyba |$9. 5M |$ 19.4 M |$9. 5M |
| Střední absolutní chyba (%) |<span style="background-color: 00FF00;"> 6.03%</span> |12.2% |<span style="background-color: 00FF00;"> 6.03%</span> |

Kromě toho koeficienty aplikace Excel porovnání dobře s váhou funkce v Azure trénovaného modelu:

|  | Koeficienty aplikace Excel | Váhu funkcí služby Azure |
| --- |:---:|:---:|
| Krádež nebo odchylka |19470209.88 |19328500 |
| Funkce A |0.832653063 |0.834156 |
| Součást B |11071967.08 |11007300 |
| Funkce C |25383318.09 |25140800 |

## <a name="next-steps"></a>Další kroky
Jsme chtěli využívat webové službě Machine Learning v aplikaci Excel. Naše obchodní analytici závisí na aplikaci Excel a potřebujeme způsob, jak volání webové služby Machine Learning s řádek dat v aplikaci Excel a mějte ho vrátit předpovězené hodnoty do aplikace Excel. 

Také chtěli jsme optimalizovat našeho modelu pomocí možnosti a algoritmy, které jsou k dispozici v nástroji Machine Learning Studio.

### <a name="integration-with-excel"></a>Integrace s aplikace Excel
Naše řešení bylo zprovoznit naše regresní model Machine Learning vytvořením webové služby ze naučeného modelu. Během několika minut webové služby byl vytvořen a jsme může volat přímo z aplikace Excel k vrácení hodnoty předpovězené výnosy. 

*Řídicího panelu webové služby* část obsahuje ke stažení sešitu aplikace Excel. Sešit obsahuje předem formátovaný webové rozhraní API a schémat informace o služby vložených. Když kliknete na tlačítko *stáhnout sešitu aplikace Excel*, otevře se sešit a můžete ho uložit do místního počítače. 

![][1]

S sešit otevřít zkopírujte předdefinované parametry do části blue parametr, jak je uvedeno níže. Po zadání parametrů Excel volá k webové službě Machine Learning a předpokládaných scored popisky se zobrazí v části zelená předpovězené hodnoty. Sešit nadále vytvořit předpovědi parametrů na základě modelu vyškolení pro všechny položky řádku zadaný v poli parametrů. Další informace o tom, jak tuto funkci použít, najdete v části [využívají webové služby Azure Machine Learning z Excelu](consuming-from-excel.md). 

![][2]

### <a name="optimization-and-further-experiments"></a>Optimalizace a další pokusy.
Teď, když jsme měli směrný plán s modelem naše aplikace Excel, můžeme přesunout článek k optimalizaci naše Model strojového učení lineární regrese. Použili jsme modul [na základě filtru výběr funkce] [ filter-based-feature-selection] ke zlepšení našeho výběru počáteční data elementy a pomohl nám dosáhnout zlepšení výkonu % 4.6 znamenat absolutní chyba. Pro budoucí projekty budeme používat tuto funkci, která může uložit nám týdny iterace v rámci atributy datového najít správnou sadu funkce, které chcete použít pro modelování. 

Dalším plánujeme zahrnout další algoritmy jako [Bayesova] [ bayesian-linear-regression] nebo [Boosted Decision Trees] [ boosted-decision-tree-regression] v našem experimentu k porovnání výkonu. 

Pokud chcete experimentovat s regrese, je dobré datové sady a zkuste to ukázkovou datovou sadu energie efektivitu regrese, který má spoustu číselné atributy. Datová sada je dodáván jako součást ukázkových datových sad v nástroji Machine Learning Studio. Celou řadu učení moduly můžete použít k předpovědi vytápění zatížení nebo chlazení zatížení. Následující graf je že výkon porovnání různých regrese zjišťuje pro predikci energetické úspornosti datovou sadu pro Cílová proměnná chlazení zatížení: 

| Model | Střední absolutní chyba | Střední kořenové spolehlivosti chyby | Relativní absolutní chyba | Relativní spolehlivosti chyby | Koeficient spolehlivosti |
| --- | --- | --- | --- | --- | --- |
| Vylepšené rozhodovací strom |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Lineární regrese (přechodu klesání) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Regrese neuronové sítě |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Lineární regrese (obyčejnou nejmenší čtverce) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Klíče Takeaways
Jsme se naučili mnoho z regrese spuštěné aplikace Excel a experimenty Azure Machine Learning paralelně. Vytvoření standardních hodnot modelu v aplikaci Excel a porovná je s modely pomocí Machine Learning [lineární regrese] [ linear-regression] pomohl nám další Azure Machine Learning a jsme zjištěné příležitosti pro zlepšení výkonu dat výběr a modelu. 

Můžeme také zjistil, že je vhodné používat [na základě filtru výběr funkce] [ filter-based-feature-selection] k urychlení projekty budoucí předpovědi. Použitím výběr funkce pro vaše data, můžete vytvořit model vylepšené v Machine Learning s lepší celkový výkon. 

Umožňuje přenos, prediktivní analýzy prognózy ze strojového učení do aplikace Excel systemically umožňuje významné zvýšení v úspěšně poskytovat výsledky pro cílovou skupinu široký obchodní uživatele. 

## <a name="resources"></a>Zdroje a prostředky
Zde jsou některé prostředky pro umožňují pracovat s regrese: 

* Regrese v aplikaci Excel. Pokud jste se nikdy zkusili Regrese v aplikaci Excel, v tomto kurzu lze snadno: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Regrese vs prognózy. Tyler Chessman napsali článek blogu, která vysvětluje, jak časové řady prognózy v aplikaci Excel, který obsahuje popis dobrý začátečníka lineární regrese. [http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts) 
* Obyčejnou čtverce nejmenší lineární regrese: Nedostatky, problémy a nástrahy. Úvod a diskuzi o regrese: [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

[1]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

