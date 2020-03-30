---
title: Interpretace modelu v Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Zjistěte, jak vysvětlit, proč váš model provádí předpovědi pomocí sady Azure Machine Learning SDK. Lze použít během školení a odvození pochopit, jak váš model dělá předpovědi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: b68d2a72dc18f683f2203429908a536db1b5124a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063985"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Interpretace modelu v Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Přehled interpretace modelu

Interpretabilita je pro datové vědce i pracovníky s rozhodovací pravomocí důležitá pro zajištění souladu s firemními politikami, průmyslovými standardy a vládními předpisy:
+ Datoví vědci potřebují schopnost vysvětlit své modely vedoucím pracovníkům a zúčastněným stranám, aby mohli pochopit hodnotu a přesnost svých zjištění 
+ Podnikatelé s rozhodovací pravomocí potřebují klid schopnosti zajistit transparentnost pro koncové uživatele, aby získali a udrželi si důvěru

Povolení možnosti vysvětlit model strojového učení je důležité ve dvou hlavních fázích vývoje modelu:
+ Během fáze školení cyklu vývoje modelu strojového učení. Návrháři modelů a hodnotitelé mohou použít výstup interpretability modelu k ověření hypotéz a budování důvěry se zúčastněnými stranami. Také používají přehledy do modelu pro ladění, ověřování chování modelu odpovídá jejich cíle a ke kontrole zkreslení nebo nevýznamné funkce.
+ Během fáze odvozování, protože transparentnost kolem nasazených modelů umožňuje vedoucím pracovníkům pochopit", když jsou nasazeni", jak model funguje a jak jeho rozhodnutí zacházejí s lidmi a ovlivňují je v reálném životě. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretabilita s Azure Machine Learning

V tomto článku se dozvíte, jak jsou implementovány koncepty interpretability modelu v sadě SDK.

Pomocí tříd a metod v sadě SDK můžete získat:
+ Hodnoty důležitosti funkce pro nezpracované i inženýrské funkce
+ Interpretovatelnost na reálných datových sadách ve velkém měřítku, během školení a odvození.
+ Interaktivní vizualizace, které vám pomohou při objevování vzorů v datech a vysvětlení v době školení


Ve strojovém učení jsou **funkce** datová pole používaná k předvídání cílového datového bodu. Chcete-li například předpovědět úvěrové riziko, mohou být použita datová pole pro věk, velikost účtu a stáří účtu. V tomto případě jsou **funkce**věk, velikost účtu a stáří účtu . Důležitost funkce vám řekne, jak každé datové pole ovlivnilo předpovědi modelu. Například věk může být hojně používán v predikci, zatímco velikost účtu a stáří nemají významný vliv na přesnost předpovědi. Tento proces umožňuje datovým vědcům vysvětlit výsledné předpovědi, aby zúčastněné strany měly přehled o tom, jaké datové body jsou v modelu nejdůležitější.

Pomocí těchto nástrojů můžete vysvětlit modely strojového učení **globálně na všech datech**nebo **místně na konkrétním datovém bodu** pomocí nejmodernějších technologií snadno použitelným a škálovatelným způsobem.

Interpretovatelnost třídy jsou k dispozici prostřednictvím více balíčků sady SDK. Zjistěte, jak [nainstalovat balíčky sady SDK pro Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* `azureml.interpret`, hlavní balíček, který obsahuje funkce podporované společností Microsoft.

* `azureml.contrib.interpret`, náhled a experimentální funkce, které můžete vyzkoušet.

* `azureml.train.automl.automlexplainer`balíček pro interpretaci automatizovaných modelů strojového učení.

> [!IMPORTANT]
> Obsah v `contrib` oboru názvů není plně podporován. Jak experimentální funkce zrají, budou postupně přesunuty do hlavního oboru názvů.

## <a name="how-to-interpret-your-model"></a>Jak interpretovat model

Můžete použít interpretovatelnost třídy a metody pochopit globální chování modelu nebo konkrétní předpovědi. První se nazývá globální vysvětlení a druhé je nazýváno místním vysvětlením.

Metody lze také kategorizovat na základě toho, zda je metoda agnostik modelu nebo konkrétní model. Některé metody se zaměřují na určitý typ modelů. Například vysvětlivací strom SHAP se vztahuje pouze na modely založené na stromech. Některé metody považují model za černou skříňku, například například napodobeninu explaineru nebo vysvětlivku jádra SHAP. Balíček `interpret` využívá tyto různé přístupy založené na datových sadách, typech modelů a případech použití.

Výstup je sada informací o tom, jak daný model provádí jeho předpověď, například:
* Význam globálního/místního relativního prvku
* Globální/místní vztah funkce a predikce

### <a name="explainers"></a>Vysvětlivky

Tento balíček využívá techniky interpretability vyvinuté v [Interpret-Community](https://github.com/interpretml/interpret-community/), open source python balíček pro školení interpretovatelné modely a pomáhá vysvětlit blackbox AI systémy. [Interpret-Community](https://github.com/interpretml/interpret-community/) slouží jako hostitel pro podporované vysvětlující moduly této sady SDK a v současné době podporuje následující techniky interpretability:

* **SHAP Tree Explainer**: [SHAP](https://github.com/slundberg/shap)'s strom explainer, který se zaměřuje na polynom čas rychlý shap hodnota odhad algoritmus specifický pro stromy a soubory stromů.
* **SHAP Deep Explainer**: Na základě vysvětlení z [SHAP](https://github.com/slundberg/shap), Deep Explainer "je vysokorychlostní aproximace algoritmus pro HODNOTY SHAP v hlubokém učení modelů, které staví na spojení s DeepLIFT popsané v [SHAP NIPS papíru](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). Podporovány jsou modely TensorFlow a modely Keras využívající backend TensorFlow (existuje také předběžná podpora pro PyTorch)".
* **Shap Lineární explainer**: [SHAP](https://github.com/slundberg/shap)'s Lineární explainer vypočítá SHAP hodnoty pro lineární model, volitelně účetnictví pro inter-funkce korelace.

* **Shap Kernel Explainer**: [Shap](https://github.com/slundberg/shap)'s Kernel explainer používá speciálně váženou lokální lineární regresi pro odhad hodnot SHAP pro jakýkoli model.
* **Mimic Explainer**: Mimic explainer je založen na myšlence školení [globálních náhradních modelů](https://christophm.github.io/interpretable-ml-book/global.html) napodobovat blackbox modely. Globální náhradní model je vnitřně interpretovatelný model, který je trénovaný tak, aby co nejpřesněji aproximoval předpovědi modelu černé skříňky. Datový vědec může interpretovat náhradní model k vyvozování závěrů o modelu černé skříňky. Můžete použít jeden z následujících interpretovatelných modelů jako náhradní model: LightGBM (LGBMExplainableModel), Lineární regrese (LinearExplainableModel), Stochastic Gradient Descent vysvětlitelný model (SGDExplainableModel) a Rozhodovací strom ( DecisionTreeExplainableModel).


* **Permutace Funkce Význam Explainer**: Permutace Funkce Význam je technika používaná k vysvětlení klasifikace a regrese modely, která je [inspirována Random Lesy Breiman papír](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (viz oddíl 10). Na vysoké úrovni, způsob, jakým to funguje, je náhodným mícháním dat po jedné funkci pro celou datovou sadu a výpočtem, jak moc se mění metrika výkonu zájmu. Čím větší je změna, tím důležitější je tato funkce.

* **LIME Explainer** (`contrib`): Na základě [LIME](https://github.com/marcotcr/lime), LIME Explainer používá state-of-the-art Místní interpretovatelné model-agnostik vysvětlení (LIME) algoritmus pro vytvoření místních náhradních modelů. Na rozdíl od globálních náhradních modelů se LIME zaměřuje na trénování místních náhradních modelů k vysvětlení jednotlivých předpovědí.
* **Han Text Explainer** (`contrib`): HAN Text Explainer používá hierarchickou síť pozornosti pro získání vysvětlení modelu z textových dat pro daný textový model černého pole. Trénuje náhradní model HAN na předpokládané výstupy daného modelu černé skříňky. Po globálním školení v textovém korpusu přidá krok doladění pro konkrétní dokument, aby se zlepšila přesnost vysvětlení. HAN používá obousměrný RNN se dvěma vrstvami pozornosti, pro větu a slovo pozornost. Jakmile je DNN trénováno na modelu černé skříňky a vyladěno na konkrétním dokumentu, může uživatel extrahovat význam slova z vrstev pozornosti. HAN je prokázáno, že je přesnější než LIME nebo SHAP pro textová data, ale dražší, pokud jde o dobu tréninku stejně. Byla provedena vylepšení, která uživateli poskytují možnost inicializovat síť pomocí vkládání slov GloVe, aby se zkrátila doba školení. Doba školení lze výrazně zlepšit spuštěním HAN na vzdáleném virtuálním počítači GPU Azure. Implementace HAN je popsána v ["Hierarchické sítě pozornosti pro klasifikaci dokumentů (Yang et al., 2016)"](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification).


* **Tabulkový explainer**: `TabularExplainer` používá následující logiku k vyvolání direct [shap](https://github.com/slundberg/shap) explainerů:

    1. Pokud se jedná o stromový `TreeExplainer`model, použijte SHAP , jinak
    2. Pokud se jedná o model `DeepExplainer`DNN, použijte SHAP , jinak
    3. Pokud se jedná o lineární `LinearExplainer`model, použijte SHAP , jinak
    3. Zacházejte s ním jako s modelem černé skříňky a aplikujte SHAP`KernelExplainer`


`TabularExplainer`učinil také významné vylepšení funkcí a výkonu oproti přímým vysvětlivačům SHAP:

* **Sumarizace datové sady inicializace**. V případech, kdy je nejdůležitější rychlost vysvětlení, shrneme datovou sadu inicializace a vygenerujeme malou sadu reprezentativních vzorků, což urychluje globální i místní vysvětlení.
* **Vzorkování souboru hodnotících dat**. Pokud uživatel předá ve velké sadě vzorků hodnocení, ale ve skutečnosti nepotřebuje všechny z nich, které mají být vyhodnoceny, vzorkování parametr lze nastavit na true urychlit globální vysvětlení.

Následující diagram znázorňuje aktuální strukturu přímých a meta explainers.

[![Architektura interpretace strojového učení](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Podporované modely

Všechny modely, které jsou trénované na datové sady v Pythonu `numpy.array` `pandas.DataFrame`, , `iml.datatypes.DenseData`, nebo `scipy.sparse.csr_matrix` formát jsou podporovány interpretovatelnost `explain` balíčku SDK.

Funkce vysvětlení přijmout modely a kanály jako vstup. Pokud je k dispozici model, musí model `predict` `predict_proba` implementovat předpověď funkce nebo který odpovídá scikit konvence. Pokud je k dispozici kanál (název skriptu kanálu), funkce vysvětlení předpokládá, že spuštěný skript kanálu vrátí předpověď. Podporujeme modely vycvičené prostřednictvím rámců hlubokého učení PyTorch, TensorFlow a Keras.

### <a name="local-and-remote-compute-target"></a>Místní a vzdálený výpočetní cíl

Balíček `explain` je určen pro práci s místními i vzdálenými výpočetními cíli. Pokud běží místně, funkce sady SDK nebude kontaktovat žádné služby Azure. Vysvětlení můžete spustit vzdáleně na Azure Machine Learning Compute a protokolovat vysvětlení informace do Služby Historie běhu na strojových učení Azure. Jakmile jsou tyto informace zaznamenány, sestavy a vizualizace z vysvětlení jsou snadno dostupné v pracovním prostoru Azure Machine Learning pro analýzu uživatelů.


## <a name="next-steps"></a>Další kroky

Podívejte [se na návody, jak](how-to-machine-learning-interpretability-aml.md) povolit interpretabilitu pro modely školení místně i na Azure Machine Learning vzdálené výpočetní prostředky. Další scénáře najdete v [ukázkových poznámkových blocích.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
