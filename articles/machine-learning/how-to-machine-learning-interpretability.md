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
ms.reviewer: Luis.Quintanilla
ms.date: 04/02/2020
ms.openlocfilehash: fcb837af85a54102e8c9eafc33249af9dba6b5ce
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631398"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Interpretace modelu v Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Přehled interpretace modelu

Interpretabilita je pro datové vědce, auditory i pracovníky s rozhodovací pravomocí důležitá pro zajištění souladu s firemními politikami, průmyslovými standardy a vládními předpisy:

+ Datoví vědci potřebují schopnost vysvětlovat své modely vedoucím pracovníkům a zúčastněným stranám, aby mohli pochopit hodnotu a přesnost svých zjištění. Vyžadují také interpretovatelnost ladit své modely a činit informovaná rozhodnutí o tom, jak je zlepšit. 

+ Právní auditoři potřebují nástroje k ověřování modelů s ohledem na dodržování právních předpisů a ke sledování toho, jaký dopad mají rozhodnutí modelů na člověka. 

+ Tvůrci obchodních rozhodnutí potřebují klid tím, že mají schopnost poskytovat transparentnost pro koncové uživatele. To jim umožňuje získat a udržet důvěru.


Povolení možnosti vysvětlit model strojového učení je důležité ve dvou hlavních fázích vývoje modelu:
+ Během fáze školení jako návrháři modelu a hodnotitelé můžete použít interpretovatelnost výstup modelu k ověření hypotéz a budovat důvěru se zúčastněnými stranami. Také používají přehledy do modelu pro ladění, ověřování chování modelu odpovídá jejich cíle a ke kontrole nespravedlnosti modelu nebo nevýznamné funkce.

+ Během fáze odvozování, protože transparentnost kolem nasazených modelů umožňuje vedoucím pracovníkům pochopit", když jsou nasazeni", jak model funguje a jak jeho rozhodnutí zacházejí s lidmi a ovlivňují je v reálném životě. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretabilita s Azure Machine Learning

Interpretovatelnost třídy jsou k dispozici prostřednictvím více balíčků sady SDK: (Naučte se, jak [nainstalovat balíčky sady SDK pro Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py))

* `azureml.interpret`, hlavní balíček, který obsahuje funkce podporované společností Microsoft.

* `azureml.contrib.interpret`, náhled a experimentální funkce, které můžete vyzkoušet.

* `azureml.train.automl.automlexplainer`balíček pro interpretaci automatizovaných modelů strojového učení.

Použití `pip install azureml-interpret` `pip install azureml-interpret-contrib` a pro obecné `pip install azureml-interpret-contrib` použití a pro použití AutoML získat interpretovatelné balíčky.


> [!IMPORTANT]
> Obsah v `contrib` oboru názvů není plně podporován. Jak experimentální funkce zrají, budou postupně přesunuty do hlavního oboru názvů.
.



## <a name="how-to-interpret-your-model"></a>Jak interpretovat model

Pomocí tříd a metod v sadě SDK můžete:
+ Vysvětlete predikci modelu generováním hodnot důležitosti funkce pro celý model nebo jednotlivé datové body. 
+ Dosáhněte interpretace modelu na reálných datových sadách ve velkém měřítku, během školení a odvození.
+ Použití interaktivního vizualizačního řídicího panelu ke zjištění vzorců v datech a vysvětlení chrátek v době školení


Ve strojovém učení jsou **funkce** datová pole používaná k předvídání cílového datového bodu. Chcete-li například předpovědět úvěrové riziko, mohou být použita datová pole pro věk, velikost účtu a stáří účtu. V tomto případě jsou **funkce**věk, velikost účtu a stáří účtu . Důležitost funkce vám řekne, jak každé datové pole ovlivnilo předpovědi modelu. Například věk může být silně používán v predikci, zatímco velikost účtu a stáří významně neovlivňují hodnoty predikce. Tento proces umožňuje datovým vědcům vysvětlit výsledné předpovědi, aby zúčastněné strany měly přehled o tom, jaké funkce jsou v modelu nejdůležitější.

Další informace o podporovaných technikách interpretability, podporovaných modelech strojového učení a podporovaných prostředích spuštění zde.


## <a name="supported-interpretability-techniques"></a>Podporované techniky interpretace

 `azureml-interpret`používá techniky interpretability vyvinuté v [Interpret-Community](https://github.com/interpretml/interpret-community/), open source python balíček pro školení interpretovatelné modely a pomáhá vysvětlit blackbox AI systémy. [Interpret-Community](https://github.com/interpretml/interpret-community/) slouží jako hostitel pro podporované vysvětlující moduly této sady SDK a v současné době podporuje následující techniky interpretability:

|Technika interpretability|Popis|Typ|
|--|--|--------------------|
|1. SHAP Strom Explainer| Sptovek stromů [SHAP,](https://github.com/slundberg/shap)který se zaměřuje na polynomčasový algoritmus časového odhadu SHAP specifického pro **stromy a soubory stromů**.|Specifické pro model|
|2. SHAP Deep Explainer| Na základě vysvětlení [shap](https://github.com/slundberg/shap), Deep Explainer "je vysokorychlostní aproximace algoritmus pro HODNOTY SHAP v hlubokém učení modelů, které staví na spojení s DeepLIFT popsané v [shap NIPS papíru](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). Podporovány jsou modely **TensorFlow** a **modely Keras** využívající backend TensorFlow (existuje také předběžná podpora pro PyTorch)".|Specifické pro model|
|3. SHAP Lineární vysvětlivka| Lineární vysvětlivka [SHAP](https://github.com/slundberg/shap)vypočítá hodnoty SHAP pro **lineární model**, případně zohledňuje korelace mezi funkcemi.|Specifické pro model|
|4. SHAP jádra Explainer| Vysvětlivka jádra [SHAP](https://github.com/slundberg/shap)používá speciálně váženou lokální lineární regresi k odhadu hodnot SHAP pro **libovolný model**.|Model-agnostik|
|5. Mimita Explainer (Globální náhradník)| Mimické vysvětlivky je založena na myšlence školení [globálních náhradních modelů](https://christophm.github.io/interpretable-ml-book/global.html) napodobovat blackbox modely. Globální náhradní model je vnitřně interpretovatelný model, který je trénovaný tak, aby co nejpřesněji aproximoval předpovědi **libovolného modelu černé skříňky.** Datoví vědci mohou interpretovat náhradní model a vyvodit závěry o modelu černé skříňky. Můžete použít jeden z následujících interpretovatelných modelů jako náhradní model: LightGBM (LGBMExplainableModel), Lineární regrese (LinearExplainableModel), Stochastic Gradient Descent vysvětlitelný model (SGDExplainableModel) a Rozhodovací strom (DecisionTreeExplainableModel).|Model-agnostik|
|6. Permutation Feature Význam Explainer (PFI)| Význam permutace je technika používaná k vysvětlení klasifikačních a regresních modelů, která je [inspirována Breimanovým papírem Random Forests](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (viz oddíl 10). Na vysoké úrovni, způsob, jakým to funguje, je náhodným mícháním dat po jedné funkci pro celou datovou sadu a výpočtem, jak moc se mění metrika výkonu zájmu. Čím větší je změna, tím důležitější je tato funkce. PFI můžete vysvětlit celkové chování **jakéhokoli základního modelu,** ale nevysvětluje jednotlivé předpovědi. |Model-agnostik|




Kromě výše popsaných technik interpretability podporujeme další [vysvětlující techniku založenou na SHAP](https://github.com/slundberg/shap), nazvanou `TabularExplainer`. V závislosti na `TabularExplainer` modelu používá jeden z podporovaných shap explainers:

* TreeExplainer pro všechny modely na bázi stromů
* DeepExplainer pro modely DNN
* LinearExplainer pro lineární modely
* KernelExplainer pro všechny ostatní modely

`TabularExplainer`učinil také významné vylepšení funkcí a výkonu oproti přímým vysvětlivačům SHAP:

* **Sumarizace datové sady inicializace**. V případech, kdy je nejdůležitější rychlost vysvětlení, shrnujeme inicializační datovou sadu a generujeme malou sadu reprezentativních vzorků, což urychluje generování celkových a individuálních hodnot důležitosti funkce.
* **Vzorkování souboru hodnotících dat**. Pokud uživatel předá ve velké sadě vzorků hodnocení, ale ve skutečnosti nepotřebuje všechny z nich, které mají být vyhodnoceny, vzorkování parametr lze nastavit na true urychlit výpočet celkového modelu vysvětlení.

Následující diagram znázorňuje aktuální strukturu podporovaných explainerů.

[![Architektura interpretace strojového učení](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Podporované modely strojového učení

Balíček `azureml.interpret` sady SDK podporuje modely trénované v následujících formátech datové sady:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

Funkce vysvětlení přijmout modely a kanály jako vstup. Pokud je k dispozici model, musí model `predict` `predict_proba` implementovat předpověď funkce nebo který odpovídá scikit konvence. Pokud váš model nepodporuje toto, můžete zabalit model ve funkci, která generuje stejný výsledek jako `predict` nebo `predict_proba` v Scikit a použít tuto funkci obálky s vybraným explainer. Pokud je k dispozici kanál, vysvětlení funkce předpokládá, že spuštěný skript kanálu vrátí předpověď. Pomocí této techniky `azureml.interpret` balení může podporovat modely vycvičené prostřednictvím rámců hlubokého učení PyTorch, TensorFlow a Keras, stejně jako klasické modely strojového učení.

## <a name="local-and-remote-compute-target"></a>Místní a vzdálený výpočetní cíl

Balíček `azureml.interpret` je určen pro práci s místními i vzdálenými výpočetními cíli. Pokud běží místně, funkce sady SDK nebude kontaktovat žádné služby Azure. 

Vysvětlení můžete spustit vzdáleně na Azure Machine Learning Compute a protokolovat vysvětlení informace do služby Azure Machine Learning Run History Service. Jakmile jsou tyto informace zaznamenány, sestavy a vizualizace z vysvětlení jsou snadno dostupné ve službě Azure Machine Learning studio pro analýzu uživatelů.


## <a name="next-steps"></a>Další kroky

Podívejte [se na návody, jak](how-to-machine-learning-interpretability-aml.md) povolit interpretabilitu pro modely školení místně i na Azure Machine Learning vzdálené výpočetní prostředky. Další scénáře najdete v [ukázkových poznámkových blocích.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
