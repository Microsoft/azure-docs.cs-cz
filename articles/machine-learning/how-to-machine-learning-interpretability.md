---
title: Interpretace modelu v Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Naučte se vysvětlit, proč model předpovědi pomocí sady SDK Azure Machine Learning. Dá se použít během školení a odvození, abyste porozuměli tomu, jak model dělá předpovědi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 339ab811969a3de6ce87d529e1bf77f325be4071
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75968492"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Interpretace modelu v Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Přehled interpretace modelu

Interpretace je zásadní pro odborníky přes data a obchodní rozhodnutí, aby se zajistilo dodržování zásad společnosti, oborových standardů a předpisů pro státní správu:
+ Vědečtí data potřebují možnost vysvětlit jejich modely vedoucím pracovníkům a zúčastněným stranám, aby mohli pochopit hodnotu a přesnost jejich zjištění. 
+ Tvůrci obchodních rozhodnutí potřebují klidně možnost zajistit transparentnost, aby koncoví uživatelé mohli získat a udržovat jejich vztah důvěryhodnosti.

Povolení funkce vysvětlení modelu strojového učení je důležité během dvou hlavních fází vývoje modelu:
+ Během fáze školení cyklu vývoje modelu Machine Learning. Návrháři modelů a vyhodnocovací filtry mohou použít výstup interpretace modelu k ověření hypotézy a vztahu důvěryhodnosti se zúčastněnými stranami. Využívají také přehledy modelu pro ladění, ověřování chování modelu odpovídá jejich cílům a ke kontrole bias nebo nedůležitých funkcí.
+ V rámci fáze Inferencing, která má transparentnost kolem nasazených modelů, umožňuje vedoucím pochopit, jak je nasazený, jak model funguje a jak se jeho rozhodnutí zpracovávají a mají dopad na lidi v reálném životě. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretace pomocí Azure Machine Learning

V tomto článku se dozvíte, jak se v sadě SDK implementují koncepty interpretace modelu.

Pomocí tříd a metod v sadě SDK můžete získat:
+ Hodnoty důležitosti funkcí pro nezpracované i inženýrské funkce
+ Možnost interpretace datových sad reálného světa ve velkém měřítku, během školení a odvození.
+ Interaktivní vizualizace, které pomáhají při zjišťování vzorů v datech a vysvětlení v době školení


Ve strojovém učení jsou **funkcemi** datová pole, která slouží k předpovědi cílového datového bodu. Například pro předpověď úvěrového rizika je možné použít datová pole pro stáří, velikost účtu a stáří účtu. V tomto případě jsou **funkce**stáří, velikost účtu a stáří účtu. Důležitost funkcí vysvětluje, jak každé datové pole ovlivnilo předpovědi modelu. Například věk může být v předpovědi silně využíván, zatímco velikost účtu a stáří neovlivňují přesnost předpovědi významně. Tento proces umožňuje datovým vědcům vysvětlit výsledné předpovědi, aby zúčastněné strany měly přehled o tom, jaké datové body jsou v modelu nejdůležitější.

Pomocí těchto nástrojů můžete vysvětlovat modely strojového učení **globálně na všech datech**nebo **lokálně na konkrétním datovém bodu** pomocí snadno použitelného a škálovatelného datového bodu s využitím nejmodernějších technologií.

Třídy interpretace jsou zpřístupněny prostřednictvím více balíčků sady SDK. Naučte se [instalovat balíčky SDK pro Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* `azureml.interpret`hlavní balíček obsahující funkce podporované Microsoftem.

* `azureml.contrib.interpret`, náhled a experimentální funkce, které můžete vyzkoušet.

* balíček `azureml.train.automl.automlexplainer` pro interpretaci automatizovaných modelů strojového učení.

> [!IMPORTANT]
> Obsah v oboru názvů `contrib` není plně podporován. Vzhledem k tomu, že experimentální funkce začnou být v zralosti, budou postupně přesunuty do hlavního oboru názvů.

## <a name="how-to-interpret-your-model"></a>Jak interpretovat model

Třídy a metody interpretace můžete použít k pochopení globálního chování modelu nebo konkrétního předpovědiu. Předchozí se nazývá globální vysvětlení a druhá se nazývá místní vysvětlení.

Metody mohou být také zařazeny do kategorií podle toho, zda je metoda model nezávislá nebo specifická pro model. Některé metody cílí na určitý typ modelů. Například vysvětlující strom SHAP se vztahuje pouze na modely založené na stromové struktuře. Některé metody nacházejí s modelem jako s černým polem, jako je například napodobení nebo vysvětlení SHAPi jádra. Balíček `interpret` využívá různé přístupy založené na datových sadách, typech modelů a případech použití.

Výstupem je sada informací o tom, jak daný model provádí předpovědi, například:
* Důležitost globálních/místních relativních funkcí
* Globální/místní funkce a vztah předpovědi

### <a name="explainers"></a>Vysvětlení

Tento balíček používá techniky interpretace vyvinuté v části [Interpret – Community](https://github.com/interpretml/interpret-community/), open source balíčku Pythonu pro školení pro interpretované modely a pomáhá vysvětlovat systémy Blackbox AI. [Interpretace – komunita](https://github.com/interpretml/interpret-community/) slouží jako hostitel pro vysvětlení podporovaná v sadě SDK a aktuálně podporuje následující techniky interpretace:

* **SHAP stromové struktury**: vysvětlení stromu [SHAP](https://github.com/slundberg/shap), které se zaměřuje na rychlost rychlého odhadu SHAP hodnoty, která je specifická pro stromy a komplety stromů.
* **SHAP hluboký světlý**: založený na vysvětlení z [SHAP](https://github.com/slundberg/shap), hlubokého vysvětlení je algoritmus vysokého odhadu pro hodnoty SHAP v modelech hloubkového učení, které se vytvářejí na připojení s DeepLIFT popsanými v [SHAP NIPSovém dokumentu](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). TensorFlow modely a modely Keras používající back-end TensorFlow jsou podporované (k dispozici je také předběžná podpora pro PyTorch).
* **SHAP lineární vysvětlení**: lineární vysvětlení [SHAP](https://github.com/slundberg/shap)vypočítá hodnoty SHAP pro lineární model, volitelně také monitorování korelace mezi funkcemi.

* **SHAP jádra**: vysvětlení jádra [SHAP](https://github.com/slundberg/shap)využívá speciálně váženou lokální lineární regresi k odhadování hodnot SHAP pro libovolný model.
* **Napodobení**přístupnosti: napodobování Vysvětlete na základě nápadu při výuce [globálních náhradních modelů](https://christophm.github.io/interpretable-ml-book/global.html) , které napodobují Blackbox modely. Globální náhradní model je vnitřně interpretně interpretováný model, který je vyškolen na to, aby co nejpřesněji předpovědi model černého pole. Odborník na data může interpretovat náhradní model, aby vykreslil závěry o modelu černého pole. Jako svůj náhradní model můžete použít jeden z následujících způsobů, které lze interpretovat: LightGBM (LGBMExplainableModel), lineární regrese (LinearExplainableModel), stochastického Gradientový model klesání na základě standardu (SGDExplainableModel) a rozhodovací strom ( DecisionTreeExplainableModel).


* **Vysvětlení důležitosti funkce permutace**: význam funkce permutace je technika, která se používá k vysvětlení modelů klasifikace a regrese, které jsou nechte inspirovat pomocí [náhodných doménových struktur Breiman](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (viz oddíl 10). Na nejvyšší úrovni je způsob, jakým funguje, náhodným pohybem dat pro celou datovou sadu a výpočtem množství metriky výkonu pro důležité změny. Čím větší je tato změna, tím důležitější je funkce.

* Nástroj na bázi **vápna** (`contrib`): založený na [vápně](https://github.com/marcotcr/lime), vysvětlecí se vápna používá špičkové místní interpretace modelu nezávislá (vápno) k vytvoření místních náhradních modelů. Na rozdíl od globálních náhradních modelů se VÁPNo zaměřuje na školení místních náhradních modelů pro vysvětlení jednotlivých předpovědi.
* Seznámení s **textem Han** (`contrib`): Nástroj pro vysvětlení textu Han používá hierarchickou síť k získání vysvětlení modelu z textových dat pro daný textový model černého rámečku. Navlakuje na základě předpokládaných výstupů v daném modelu černého rámečku. Po globálním školení v Corpus textu se přidá krok s přípravou pro konkrétní dokument, aby se zlepšila přesnost vysvětlení. HAN používá obousměrný RNN se dvěma úrovněmi pozornosti, pro větu a wordovou pozornost. Jakmile je DNN vyškolený pro model černého pole a vyladěný podle konkrétního dokumentu, uživatel může z vrstev pozornosti extrahovat slova důležitost. HAN se zobrazuje jako přesnější než VÁPNo nebo SHAP pro textová data, ale dražší z hlediska doby školení. Byla provedena vylepšení, která umožňují uživateli inicializovat síť pomocí vkládání slov šetrnější, aby se snížil čas školení. Dobu školení můžete významně zlepšit spuštěním HAN na vzdáleném virtuálním počítači s grafickým procesorem Azure. Implementace HAN je popsána v části [hierarchické pozornosti sítě pro klasifikaci dokumentu (Yang et al., 2016)](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification).


* **Tabulkový**popis: `TabularExplainer` používá následující logiku k vyvolání přímých [SHAP](https://github.com/slundberg/shap) vysvětlení:

    1. Pokud se jedná o model založený na stromu, použijte SHAP `TreeExplainer`, jinak
    2. Pokud se jedná o DNN model, použijte SHAP `DeepExplainer`, jinak
    3. Pokud se jedná o lineární model, použijte SHAP `LinearExplainer`, jinak
    3. Nahlížet jako na model černého boxu a použít `KernelExplainer` SHAP


`TabularExplainer` také výrazně vylepšení funkcí a výkonu prostřednictvím přímých vysvětlení SHAP:

* **Sumarizace inicializační sady dat**. V případech, kdy je rychlost vysvětlení nejdůležitější, shrnujeme inicializační datovou sadu a vygenerujeme malou sadu reprezentativních vzorků, které urychlují globální i místní vysvětlení.
* **Vzorkování sady dat pro vyhodnocení**. Pokud uživatel projde velkou sadou zkušebních vzorků, ale nepotřebuje vyhodnotit všechny z nich, je možné nastavit parametr vzorkování na hodnotu true, aby se globální vysvětlení urychlilo.

Následující diagram znázorňuje aktuální strukturu přímých a meta vysvětlujících.

[Architektura interpretace ![Machine Learning](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Podporované modely

Všechny modely, které jsou výukou v datových sadách v Pythonu `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`nebo `scipy.sparse.csr_matrix`, jsou podporovány balíčkem pro interpretaci `explain` sady SDK.

Funkce vysvětlení přijímají jako vstup oba modely i kanály. Pokud je k dispozici model, model musí implementovat funkci předpovědi `predict` nebo `predict_proba`, která odpovídá konvenci Scikit. Pokud je k dispozici kanál (název skriptu kanálu), funkce vysvětlení předpokládá, že spuštěný skript kanálu vrátí předpověď. Podporujeme modely vyškolené prostřednictvím PyTorch, TensorFlow a Keras architektury hloubkového učení.

### <a name="local-and-remote-compute-target"></a>Cíl pro místní a vzdálené výpočty

Balíček `explain` je navržený tak, aby fungoval s místními i vzdálenými výpočetními cíli. Pokud spustíte místně, funkce SDK nebudou kontaktovat žádné služby Azure. Vysvětlení můžete spustit vzdáleně na Azure Machine Learning COMPUTE a protokolovat informace o vysvětlení do Azure Machine Learning spuštění služby historie. Po zaznamenání těchto informací jsou sestavy a vizualizace z vysvětlení snadno dostupné v Azure Machine Learning pracovním prostoru pro analýzu uživatelů.


## <a name="next-steps"></a>Další kroky

Přečtěte si téma [postup](how-to-machine-learning-interpretability-aml.md) pro povolení interpretace u modelů v místním prostředí i na Azure Machine Learning vzdálených výpočetních prostředků. Další scénáře najdete v [ukázkových poznámkových blocích](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) .
