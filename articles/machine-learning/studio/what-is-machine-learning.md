---
title: Co je Machine Learning v Azure? | Dokumenty Microsoft
description: Dočtete se o základních konceptech strojového učení v cloudu, dozvíte se, k čemu ho můžete využít, a seznámíte se s terminologií strojového učení.
keywords: co je strojové učení, terminologie strojového učení, prediktivní, co je prediktivní analýza, terminologie strojového učení
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: eaee083e-eaa1-4408-838b-93e51423d159
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/23/2018
ms.openlocfilehash: 36095d24f091ac195fe95c839924b15efae05232
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-machine-learning-in-the-azure-cloud"></a>Úvod do služby Machine Learning v cloudu Azure

## <a name="what-is-machine-learning"></a>Co je strojové učení?
Strojové učení je technika datové vědy, která umožňuje počítačům s využitím existujících dat předvídat budoucí chování, výsledky a trendy. Pomocí strojového učení se počítače můžou učit, aniž by byly explicitně naprogramované.

Díky předpovědím neboli predikcím ze strojového učení můžou být aplikace a zařízení chytřejší. Při online nakupování je díky strojovému učení možné na základě již pořízeného zboží doporučit produkty, které by se vám mohly líbit. Po protažení platební karty čtečkou porovnává strojově učený proces danou transakci s databází, a pomáhá tak odhalovat podvody. A takový robotický vysavač na základě strojového učení zjišťuje, jestli už v místnosti dostatečně uklidil.

Stručný přehled získáte třeba zhlédnutím série videí [Datová věda pro začátečníky](data-science-for-beginners-the-5-questions-data-science-answers.md). Datová věda pro začátečníky vám bez nesrozumitelných výrazů a složité matematiky představí strojové učení a krok za krokem vás provede jednoduchým prediktivním modelem.

## <a name="what-is-machine-learning-in-the-microsoft-azure-cloud"></a>Co je Machine Learning v cloudu Microsoft Azure?
Azure Machine Learning je cloudová služba pro prediktivní analýzu, která umožňuje rychle vytvářet a nasazovat prediktivní modely jako analytická řešení.

Můžete pracovat s knihovnou algoritmů připravených k použití, vytvářet pomocí nich modely na počítači připojeném k internetu a rychle nasadit prediktivní řešení. Začněte s příklady a řešeními připravenými k použití v [galerii Azure AI](https://gallery.cortanaintelligence.com/).

![Co je strojové učení? Základní pracovní postup pro zprovoznění prediktivních analýz v Azure Machine Learning](./media/what-is-machine-learning/machine-learning-service-parts-and-workflow.png)

Nejenže Azure Machine Learning poskytuje nástroje pro modelování prediktivních analýz, ale přináší také plně spravovanou službu, pomocí které můžete své prediktivní modely nasazovat jako k použití připravené webové služby.

## <a name="what-is-predictive-analytics"></a>Co je prediktivní analýza?
Při prediktivní analýze se používají matematické vzorce, neboli algoritmy, které analýzou historických či aktuálních dat identifikují vzorce a trendy a snaží se pomocí nich předpovídat budoucí události.

## <a name="tools-to-build-complete-machine-learning-solutions-in-the-cloud"></a>Nástroje pro sestavování kompletních řešení pro strojové učení v cloudu
Azure Machine Learning obsahuje vše, co potřebujete k vytváření kompletních řešení prediktivní analýzy v cloudu: od velké knihovny algoritmů přes studio pro vytváření modelů po snadný způsob, jak model nasadit jako webovou službu. Rychle vytvořte, otestujte, zprovozněte a spravujte prediktivní modely.

### <a name="machine-learning-studio-create-predictive-models"></a>Machine Learning Studio: Vytváření prediktivních modelů
Rychlé vytváření prediktivních modelů v nástroji [Machine Learning Studio](what-is-ml-studio.md) usnadňuje přetahování myší a propojování modulů. Můžete experimentovat s různými kombinacemi, [sami si to zdarma vyzkoušejte](https://studio.azureml.net/?selectAccess=true&o=2).

* V [galerii Azure AI](gallery-how-to-use-contribute-publish.md) si můžete vyzkoušet analytická řešení připravená ostatními uživateli nebo přispět svým vlastním. V komunitě můžete klást otázky nebo publikovat komentáře k experimentům, případně sdílet odkazy na experimenty prostřednictvím sociálních sítí, jako je LinkedIn a Twitter.

  ![Zkoušení prediktivních experimentů nebo poskytování vlastních v galerii Azure AI](./media/what-is-machine-learning/machine-learning-cortana-intelligence-gallery.png)
* Díky rozsáhlé knihovně [algoritmů a modulů pro strojové učení](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) v nástroji Machine Learning Studio se můžete hned pustit do sestavování prediktivních modelů. Můžete vybírat z ukázkových experimentů, balíčků R a Python a nejlepších algoritmů ve své třídě z divizí Microsoftu, jako jsou Xbox a Bing. Moduly ze Studia můžete rozšířit vlastními skripty [R](extend-your-experiment-with-r.md) a [Python](execute-python-scripts.md).

  ![Co je to prediktivní analýza: Příklad experimentu s prediktivní analýzou v nástroji Azure Machine Learning Studio](./media/what-is-machine-learning/azure-machine-learning-studio-predictive-score-experiment.png)

### <a name="operationalize-predictive-analytics-solutions-by-publishing-your-own"></a>Zprovoznění vlastních řešení prediktivní analýzy jejich publikováním
Následující kurzy vám ukážou, jak zprovoznit vlastní modely prediktivní analýzy:

 * [Nasazování webových služeb](publish-a-machine-learning-web-service.md)
 * [Přeučování modelů prostřednictvím API](retrain-models-programmatically.md)
 * [Správa koncových bodů webové služby](create-endpoint.md)
 * [Škálování webové služby](scaling-webservice.md)
 * [Využívání webových služeb](consume-web-services.md)

## <a name="key-machine-learning-terms-and-concepts"></a>Klíčová terminologie a koncepty strojového učení
Pojmy z oblasti strojového učení nemusí být na první pohled úplně jasné. Ke klíčovým pojmům zde najdete definice, které vám leccos objasní. Do komentářů dole na stránce nám můžete napsat, jaké další termíny byste chtěli definovat.

### <a name="data-exploration-descriptive-analytics-and-predictive-analytics"></a>Zkoumání dat, deskriptivní analýza a prediktivní analýza

**Zkoumání dat** je proces shromažďování informací o velké a často nestrukturované datové sadě s cílem najít charakteristiky pro přesně zacílenou analýzu.

**Dolování dat** označuje automatizované zkoumání dat.

**Deskriptivní analýza** je proces analyzování datové sady s cílem shrnout, co se stalo. Velká většina obchodních analýz – například prodejní sestavy, webové metriky a analýzy sociálních sítí – je deskriptivních.

**Prediktivní analýza** je proces vytváření modelů na základě historických nebo aktuální dat s cílem předpovídat budoucí výsledky.

### <a name="supervised-and-unsupervised-learning"></a>Učení se supervizí a bez supervize
 V algoritmech **učení se supervizí** probíhá trénink na označených datech – jinými slovy na datech zahrnujících příklady žádoucích odpovědí. Například v modelu, který identifikuje podvodné použití platební karty, by trénink probíhal na datové sadě s označenými datovými body transakcí, o kterých se ví, že byly, resp. nebyly podvodné. Většina strojového učení probíhá se supervizí.

 **Učení bez supervize** probíhá na neoznačených datech a cílem je nalezení vztahů a souvislostí v nich. Cílem může být například nalezení demograficky definovaných skupin zákazníků s podobnými nákupními návyky.

### <a name="model-training-and-evaluation"></a>Trénování a hodnocení modelů
Model strojového učení je abstrakcí otázky, na kterou hledáte odpověď, nebo výsledku, který chcete předpovědět. Modely se trénují a vyhodnocují na základě existujících dat.

#### <a name="training-data"></a>Data pro trénink
Při tréninku modelů používáte známou datovou sadu a podle charakteru dat model upravujete tak, abyste co nejvíce zpřesnili jeho výstupy. V Azure Machine Learning se model sestavuje z algoritmického modulu, který zpracovává trénovací data a funkční moduly, například modul pro stanovení skóre.

Pokud v rámci učení se supervizí trénujete model pro odhalování podvodů, používáte sadu transakcí, které jsou označeny jako podvodné nebo platné. Datovou sadu náhodně rozdělíte a pak jednu část použijete k natrénování modelu a další část k otestování nebo vyhodnocení modelu.

#### <a name="evaluation-data"></a>Data pro vyhodnocení
Jakmile model natrénujete, vyhodnotíte ho na zbývajících testovacích datech. Využijete k tomu data, u kterých již znáte výsledky, aby bylo možné určit, zda je váš model schopen přesné predikce.

## <a name="other-common-machine-learning-terms"></a>Další běžné pojmy používané při strojovém učení
* **algoritmus**: Samostatná sada pravidel používaná k řešení problémů prostřednictvím zpracování dat, matematických výpočtů nebo automatizovaného posuzování.
* **detekce anomálií**: Model, který nachází a označuje neobvyklé události či hodnoty a pomáhá objevovat problémy. Detektor podvodů s platebními kartami například vyhledává nezvyklé nákupy.
* **kategorizovaná data**: Data, která jsou uspořádána do kategorií a která lze rozdělit do skupin. Kategorizovaná datová sada pro automobily by například mohla specifikovat rok výroby, značku, model a cenu.
* **klasifikace**: Model pro uspořádání datových bodů do kategorií na základě datové sady, u které je již seskupení do kategorií známo.
* **konstruování příznaků**: Proces extrahování nebo výběru příznaků či atributů souvisejících s datovou sadou s cílem zvýšit její využitelnost a vylepšit výsledky. Například využitelnost dat o leteckých tarifech lze vylepšit určením dnů v týdnu a svátků. Viz [Výběr a konstruování příznaků v Azure Machine Learning](../team-data-science-process/create-features.md).
* **modul**: Funkční součást modelu v nástroji Machine Learning Studio, například modul pro zadávání dat, který umožňuje zadávat a upravovat malé datové sady. Určitým typem modulu v nástroji Machine Learning Studio je i algoritmus.
* **model**: Model v rámci učení se supervizí představuje produkt experimentu strojového učení a skládá se z dat pro trénink, algoritmického modulu a funkčních modulů, jako je modul Určení skóre modelu.
* **číselná data**: Data, která mají význam jako měření (spojitá data) nebo počty (diskrétní neboli nespojitá data). Označují se také jako *kvantitativní data*.
* **rozdělení**: Metoda, podle které data rozdělíte do vzorků. Další informace naleznete v tématu [Rozdělení a vzorky](https://msdn.microsoft.com/library/azure/dn905960.aspx).
* **predikce**: Hodnota nebo hodnoty předpovězené z modelu strojového učení. Možná jste se také setkali s termínem „predikované skóre“. To však nepředstavuje konečný výstup modelu. Podle skóre se řídí vyhodnocení modelu.
* **regrese**: Model pro predikci hodnoty na základě nezávislých proměnných, například předpověď ceny automobilu na základě roku výroby a značky.
* **skóre**: Předpovězená hodnota generovaná z natrénovaného klasifikačního nebo regresního modelu na základě [modulu pro stanovení skóre](https://msdn.microsoft.com/library/azure/dn905995.aspx) v nástroji Machine Learning Studio. Klasifikační modely rovněž vracejí skóre pro pravděpodobnost předpovězené hodnoty. Po vygenerování skóre z modelu můžete vyhodnotit jeho přesnost pomocí [modulu pro vyhodnocení modelu](https://msdn.microsoft.com/library/azure/dn905915.aspx).
* **vzorek**: Část datové sady, která by měla představovat celek. Vzorky lze vybírat náhodně nebo podle konkrétních příznaků či atributů datové sady.

## <a name="next-steps"></a>Další kroky
Se základy prediktivní analýzy a strojového učení se můžete seznámit v [podrobném kurzu](create-experiment.md) a [na základě ukázek](sample-experiments.md).  

<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/
