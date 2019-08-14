---
title: Migrace analýz z Excelu
titleSuffix: Azure Machine Learning Studio
description: Porovnání modelů lineární regrese v aplikaci Excel a v Azure Machine Learning Studio
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7db66f6f4efa5e48f2af9380115de8bcfb75cb86
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "67786670"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio"></a>Migrace analýz z Excelu na Azure Machine Learning Studio

> *Kate Baroni* a *Robert člun* jsou architekti podnikových řešení v centru pro data Insights společnosti Microsoft. V tomto článku popisují své možnosti migrace stávající regresní analytické sady do cloudového řešení pomocí Azure Machine Learning Studio.

## <a name="goal"></a>Cíl

Náš projekt byl zahájen se dvěma záměry: 

1. Použití prediktivních analýz ke zlepšení přesnosti odhadů měsíčních výnosů v naší organizaci 
2. Použijte Azure Machine Learning Studio k potvrzení, optimalizaci, zvýšení rychlosti a škálování našich výsledků. 

Podobně jako v mnoha firmách naše organizace prochází měsíčním procesem prognózování výnosů. Náš malý tým obchodních analytiků pracoval s použitím Azure Machine Learning Studio k podpoře procesu a zlepšení přesnosti prognózy. Tým strávil několik měsíců shromažďováním dat z více zdrojů a spouštěním datových atributů prostřednictvím statistické analýzy, které určují klíčové atributy, které se týkají prognózování prodejních služeb. Dalším krokem bylo začít vytváření prototypů statistických regresních modelů pro data v Excelu. Během několika týdnů máme regresní model Excelu, který vyvolal aktuální pole a finanční procesy prognózy. Tato hodnota se stala výsledkem předpovědi standardních hodnot. 

Následně jsme provedli další krok přesunu prediktivních analýz do studia, abyste zjistili, jak by Studio mohlo zlepšit prediktivní výkon.

## <a name="achieving-predictive-performance-parity"></a>Dosažení prediktivního parity výkonu
Naši první prioritou bylo dosáhnout parity mezi regresními modely studia a Excelu. Vzhledem ke stejným datům a ke stejnému rozdělení pro školení a testování dat jsme chtěli dosáhnout prediktivního zvýšení výkonu mezi Excelem a Studio. Zpočátku jsme se nezdařili. Excelový model vykonáváný model studia. Příčinou této chyby bylo nedostatečné porozumění základnímu nastavení nástroje v studiu. Po synchronizaci s produktovým týmem pro Studio jsme získali lepší informace o základním nastavení potřebném pro naše sady dat a dosažení parity mezi oběma modely. 

### <a name="create-regression-model-in-excel"></a>Vytvoření regresního modelu v Excelu
Naše Excelová regrese používala standardní lineární regresní model, který najdete v analytickém doplňku Excelu. 

Vypočítali jsme *střední hodnotu absolutního% chyby* a použili ji jako míru výkonu pro model. Dosažení pracovního modelu pomocí Excelu trvalo 3 měsíce. Mnohé z našich kurzů jsme zavedli do experimentu studia, který je nakonec užitečný pro porozumění požadavkům.

### <a name="create-comparable-experiment-in-studio"></a>Vytváření srovnatelných experimentů v studiu
Provedli jsme tyto kroky k vytvoření našeho experimentu v studiu: 

1. Nahráli datovou sadu jako soubor CSV do studia (velmi malý soubor).
2. Vytvořili jsme nový experiment a použili jste modul [Vybrat sloupce v datové sadě][select-columns] k výběru stejných datových funkcí používaných v Excelu. 
3. Používali jste modul [rozdělit data][split] (s relativním režimem *výrazů* ) k rozdělení dat do stejných školicích datových sad, které byly provedeny v aplikaci Excel. 
4. Experimentování s modulem [lineární regrese][linear-regression] (pouze výchozí možnosti), zdokumentované a porovnány s výsledky do našeho modelu Excel regrese

### <a name="review-initial-results"></a>Kontrola počátečních výsledků
Nejprve model aplikace Excel jasně provedl model studia: 

|  | Excel | Studio |
| --- |:---:|:---:|
| Výkon | | |
| <ul style="list-style-type: none;"><li>Upravený čtvereček R</li></ul> |0.96 |Není k dispozici |
| <ul style="list-style-type: none;"><li>Koeficient <br />Analýzy</li></ul> |Není k dispozici |0.78<br />(nízká přesnost) |
| Střední absolutní chyba |– 9,5 M |$19.4 M |
| Střední absolutní chyba (%) |6.03% |12.2% |

Když jsme spustili náš proces a výsledky od vývojářů a odborníků na data na Machine Learning týmu, rychle vám poskytnou několik užitečných tipů. 

* Při použití modulu [lineární regrese][linear-regression] v nástroji Studio jsou k dispozici dvě metody:
  * Online Gradientový přechod: Může být vhodnější pro problémy s větším rozsahem.
  * Běžné nejmenší čtverce: Toto je metoda, kterou většina lidí myslíme při poslechu lineární regrese. V případě malých datových sad může být obyčejná volba velmi optimální.
* Zvažte možnost Upravit parametr váhy pro použití v L2, aby se zlepšil výkon. Ve výchozím nastavení je nastavená na 0,001, ale pro naši malou datovou sadu nastavíme ji na 0,005, aby se zlepšil výkon. 

### <a name="mystery-solved"></a>Mystery vyřešeno!
Když jsme použili doporučení, dosáhli jsme stejný základní výkon jako v studiu jako v Excelu: 

|  | Excel | Studio (počáteční) | Studia s minimálními čtverci |
| --- |:---:|:---:|:---:|
| Hodnota s popiskem |Skutečné hodnoty (číselné) |jedné |jedné |
| Learner |Excel – > Analýza dat – > regrese |Lineární regrese. |Lineární regrese |
| Možnosti naučí se |Není k dispozici |Výchozí nastavení |obvyklé nejmenší čtverce<br />L2 = 0,005 |
| Sada dat |26 řádků, 3 funkce, 1 popisek. Všechny číselné. |jedné |jedné |
| Rozdělení Trénování |Aplikace Excel je vyškolená na prvních 18 řádcích, testováno na posledních 8 řádků. |jedné |jedné |
| Rozdělení Test |Vzorec regrese Excelu aplikovaný na posledních 8 řádků |jedné |jedné |
| **Výkon** | | | |
| Upravený čtvereček R |0.96 |Není k dispozici | |
| Koeficient určení |Není k dispozici |0.78 |0.952049 |
| Střední absolutní chyba |– 9,5 M |$19.4 M |– 9,5 M |
| Střední absolutní chyba (%) |<span style="background-color: 00FF00;"> 6.03%</span> |12.2% |<span style="background-color: 00FF00;"> 6.03%</span> |

Kromě toho jsou excelové koeficienty v porovnání s váhy funkcí v modelu školení Azure:

|  | Excelové koeficienty | Váhy funkcí Azure |
| --- |:---:|:---:|
| Zachytit/bias |19470209.88 |19328500 |
| Funkce A |0.832653063 |0.834156 |
| Funkce B |11071967.08 |11007300 |
| Funkce C |25383318.09 |25140800 |

## <a name="next-steps"></a>Další kroky
Chtěli bychom využít webovou službu Machine Learning v Excelu. Naši obchodní analytiké spoléhají na aplikaci Excel a potřebovali jsme způsob, jak volat webovou službu Machine Learning s využitím řádku excelových dat a vrátit předpokládanou hodnotu do Excelu. 

Chtěli jsme také optimalizovat náš model pomocí možností a algoritmů dostupných v studiu.

### <a name="integration-with-excel"></a>Integrace s aplikací Excel
Naše řešení mělo zprovoznění náš Machine Learning regresní model vytvořením webové služby z trained model. Během několika minut byla webová služba vytvořena a můžeme ji volat přímo z Excelu, aby vracela předpokládanou hodnotu výnosů. 

Část *řídicí panel webové služby* obsahuje excelový sešit ke stažení. Sešit je předem zformátován pomocí rozhraní Web Service API a informací o schématu Embedded. Když kliknete na *Stáhnout excelový sešit*, sešit se otevře a můžete ho uložit do svého místního počítače. 

![Stažení sešitu aplikace Excel z řídicího panelu webové služby](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

Po otevření sešitu zkopírujte předdefinované parametry do části modrý parametr, jak je znázorněno níže. Po zadání parametrů se aplikace Excel zavolá do Machine Learning webové služby a předpovězené popisky s skóre budou zobrazeny v části zelené předpovězené hodnoty. Sešit bude pokračovat v vytváření předpovědi pro parametry založené na vašemu vyškolený model pro všechny položky řádku zadané v parametrech Parameters. Další informace o tom, jak používat tuto funkci, najdete v tématu věnovaném [využívání Azure Machine Learning webové služby z Excelu](consuming-from-excel.md). 

![Šablona excelový sešit připojující se k nasazené webové službě](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>Optimalizace a další experimenty
Teď, když jsme si nastavili směrný plán náš excelový model, jsme se k optimalizaci našeho Machine Learningho modelu lineární regrese přesunuli předem. Využívali jsme [Výběr funkcí založených][filter-based-feature-selection] na filtrech modulů, které vám pomůžou zdokonalit výběr počátečních datových prvků, což nám pomohlo dosáhnout zvýšení výkonu 4,6% střední chyby. V budoucích projektech použijeme tuto funkci, která by mohla ušetřit týdny v průběhu iterace prostřednictvím atributů dat a najít tak správnou sadu funkcí, které se mají použít pro modelování. 

V dalším kroku plánujeme zahrnout do našeho experimentu další algoritmy, jako je [bayesovského rozhodování][bayesian-linear-regression] nebo posílené [rozhodovací stromy][boosted-decision-tree-regression] , a porovnat tak výkon. 

Pokud chcete experimentovat s regresí, dobrá datová sada, která se má vyzkoušet, je ukázková datová sada pro energetickou účinnost, která má spoustu číselných atributů. Datová sada je poskytována jako součást ukázkových datových sad v nástroji Studio. Můžete použít celou řadu výukových modulů k předpovědi buď vyhřívání zatížení nebo chlazení. Níže uvedený graf je porovnání výkonu různých regresí, které se seznámí s předpověďmi datové sady energetické účinnosti pro cílovou proměnnou zatížení pro účely chlazení: 

| Model | Střední absolutní chyba | Střední Chyba v kořenu – chyba | Relativní absolutní chyba | Relativní čtvercová chyba | Koeficient určení |
| --- | --- | --- | --- | --- | --- |
| Zesílený rozhodovací strom |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Lineární regrese (proklesání přechodu) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Regrese neurální sítě |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Lineární regrese (normální aspoň čtverce) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Key poznatky
Zjistili jsme spoustu, díky které se paralelně spouští aplikace Excel regrese a experimenty studia. Vytvoření základního modelu v Excelu a jeho porovnání s modely pomocí Machine Learning [lineární regrese][linear-regression] nám pomohla naučit se Studio a zjistili jsme příležitosti, jak vylepšit výběr dat a výkon modelu. 

Zjistili jsme také, že je vhodné použít [Výběr funkce založený][filter-based-feature-selection] na filtrech k urychlení budoucích předpovědí projektů. Když použijete výběr funkcí na vaše data, můžete v nástroji Studio vytvořit vylepšený model s lepším celkovým výkonem. 

Možnost přenést prediktivní analýzu prognózování z studia do Excelu systemically umožňuje významně zvyšovat možnosti úspěšného poskytování výsledků široké cílové skupině uživatelů. 

## <a name="resources"></a>Zdroje a prostředky
Tady je několik prostředků, které vám pomůžou při práci s regresí: 

* Regrese v aplikaci Excel. Pokud jste nikdy nezkoušeli regresi v Excelu, tento kurz usnadňuje:[https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* Regrese vs – předpověď. Tyler šachy vytvořil článek o blogu, který vysvětluje, jak provádět prognózování časových řad v Excelu, který obsahuje dobrý popis lineární regrese. [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* Normální nejméně Čtvercová lineární regrese: Chyby, problémy a nástrah. Úvod a diskuzi o regresi: [ https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

