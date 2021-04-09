---
title: Interpretace modelu v Azure Machine Learning (Náhled)
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak porozumět &, jak vysvětlovat, jak váš model strojového učení předpovědi během školení & Inferencing pomocí sady Azure Machine Learning Python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 02/25/2021
ms.openlocfilehash: 44ccf6b6d2459b87040fcac7d9cdcd336cc7b82f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102522032"
---
# <a name="model-interpretability-in-azure-machine-learning-preview"></a>Interpretace modelu v Azure Machine Learning (Náhled)


## <a name="model-interpretability-overview"></a>Přehled interpretace modelu

Interpretace modelu je kritická pro odborníky přes data, auditory a pracovníky pro rozhodování, aby zajistili dodržování zásad společnosti, oborových standardů a předpisů pro státní správu:

+ Vědečtí data potřebují možnost vysvětlit jejich modely vedoucím pracovníkům a zúčastněným stranám, aby mohli pochopit hodnotu a přesnost jejich zjištění. Vyžadují také výklad pro ladění modelů a rozhodování o tom, jak je zlepšit. 

+ Zákonné auditory vyžadují nástroje k ověřování modelů v souvislosti s dodržováním předpisů a monitorování způsobu, jakým jsou jejich rozhodnutí ovlivněna lidmi. 

+ Pracovníci s rozhodovacím rozhodnutím potřebují klidně, protože mají možnost poskytnout transparentnost koncovým uživatelům. To jim umožní získat a zachovat důvěryhodnost.

Povolení funkce vysvětlení modelu strojového učení je důležité během dvou hlavních fází vývoje modelu:

+ Ve fázi školení mohou návrháři modelů a hodnotitelé použít výstup interpretace modelu k ověření hypotézy a vztahu důvěryhodnosti se zúčastněnými stranami. Využívají také přehledy modelu pro ladění, ověřování chování modelu odpovídá jejich cílům a ke kontrole nespravedlivých nebo nevýznamných funkcí modelu.

+ V rámci fáze Inferencing, která má transparentnost kolem nasazených modelů, umožňuje vedoucím pochopit, jak je nasazený, jak model funguje a jak se jeho rozhodnutí zpracovávají a mají dopad na lidi v reálném životě. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretace pomocí Azure Machine Learning

Třídy interpretace modelu jsou zpřístupněny prostřednictvím následujícího balíčku sady SDK: (Naučte se [instalovat balíčky sady SDK pro Azure Machine Learning](/python/api/overview/azure/ml/install))

* `azureml.interpret`obsahuje funkce podporované Microsoftem.

Používá se `pip install azureml-interpret` pro obecné použití.

## <a name="how-to-interpret-your-model"></a>Jak interpretovat model

Pomocí tříd a metod v sadě SDK můžete:
+ Vysvětlete předpověď modelu tím, že vygenerujete hodnoty důležitosti funkcí pro celý model a/nebo jednotlivé databody. 
+ Dosáhnete možností interpretace modelu na skutečných datových sadách ve velkém měřítku během školení a odvození.
+ Pomocí interaktivního řídicího panelu vizualizace můžete vyhledat vzory v datech a vysvětlení v době školení.

Ve strojovém učení jsou **funkcemi** datová pole, která slouží k předpovědi cílového datového bodu. Například pro předpověď úvěrového rizika je možné použít datová pole pro stáří, velikost účtu a stáří účtu. V tomto případě jsou **funkce** stáří, velikost účtu a stáří účtu. Důležitost funkcí vysvětluje, jak každé datové pole ovlivnilo předpovědi modelu. Například věk může být v předpovědi silně využíván, zatímco velikost účtu a stáří neovlivňují hodnoty předpovědi významně. Tento proces umožňuje datovým vědcům vysvětlit výsledné předpovědi, aby zúčastněné strany měly přehled o tom, jaké funkce jsou v modelu nejdůležitější.

## <a name="supported-interpretability-techniques"></a>Podporované techniky interpretace

 `azureml-interpret` používá techniky interpretace vyvinuté v [interpretství – Community](https://github.com/interpretml/interpret-community/), open source balíček Pythonu pro účely školení uživatelsky interpretované modely a pomáhá vysvětlit Blackbox systémy AI. [Interpretace – komunita](https://github.com/interpretml/interpret-community/) slouží jako hostitel pro vysvětlení podporovaná v sadě SDK a aktuálně podporuje následující techniky interpretace:

|Technika interpretace|Popis|Typ|
|--|--|--------------------|
|SHAP stromové struktury| [SHAP](https://github.com/slundberg/shap)na stromové struktuře, který se zaměřuje na polynomická rychlá hodnota SHAP algoritmu odhadu, která je specifická pro **stromy a komplety stromů**.|Specifické pro model|
|SHAP hluboký vysvětlující| Na základě vysvětlení z SHAP je hluboko vysvětlující algoritmus pro hodnoty SHAP v modelech hloubkového učení s vysokou rychlostí, který se vytváří na základě připojení s DeepLIFT popsané v [dokumentu SHAP nips](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). **TensorFlow** modely a modely **Keras** používající back-end TensorFlow jsou podporované (k dispozici je také předběžná podpora pro PyTorch).|Specifické pro model|
|SHAP lineární vysvětlující| Lineární SHAPý vysvětlení vypočítá SHAP hodnoty pro **lineární model**, volitelně také účetní pro korelace mezi funkcemi.|Specifické pro model|
|Vysvětlující SHAP jádra| SHAP vysvětlení jádra používá speciálně váženou místní lineární regresi k odhadování hodnot SHAP pro **libovolný model**.|Model – nezávislá|
|Napodobit vysvětlení (globální náhrada)| Nevhodnější je vycházet z nápadu pro školení [globálních náhradních modelů](https://christophm.github.io/interpretable-ml-book/global.html) pro napodobování Blackbox modelů. Globální náhradní model je vnitřně interpretováný model, který je vyškolen na to, aby co nejpřesněji předpovědi **model černého pole** . Vědečtí data mohou interpretovat náhradní model, aby vykreslili závěry o modelu černého pole. Jako svůj náhradní model můžete použít jeden z následujících způsobů, které lze interpretovat: LightGBM (LGBMExplainableModel), lineární regrese (LinearExplainableModel), stochastického Gradientový model klesání na základě standardu (SGDExplainableModel) a rozhodovací strom (DecisionTreeExplainableModel).|Model – nezávislá|
|Vysvětlení důležitosti funkce permutace (PFI)| Funkce permutace je důležitou metodou pro vysvětlení modelů klasifikace a regrese, které jsou nechte inspirovat pomocí [náhodných strukturních struktur Breiman](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (viz oddíl 10). Na nejvyšší úrovni je způsob, jakým funguje, náhodným pohybem dat pro celou datovou sadu a výpočtem množství metriky výkonu pro důležité změny. Čím větší je tato změna, tím důležitější je funkce. PFI může vysvětlit celkové chování **jakéhokoli podkladového modelu** , ale nevysvětluje jednotlivé předpovědi. |Model – nezávislá|

Kromě metod interpretace popsaných výše podporujeme jiný vysvětlující SHAP, který se nazývá `TabularExplainer` . V závislosti na modelu `TabularExplainer` používá jeden z podporovaných SHAP vysvětlení:

* TreeExplainer pro všechny modely založené na stromové struktuře
* DeepExplainer pro modely DNN
* LinearExplainer pro lineární modely
* KernelExplainer pro všechny ostatní modely

`TabularExplainer` má také významná vylepšení funkcí a výkonu prostřednictvím přímých SHAPch vysvětlení:

* **Sumarizace inicializační sady dat**. V případech, kdy je rychlost vysvětlení nejdůležitější, shrnujeme inicializační datovou sadu a vygenerujeme malou sadu reprezentativních vzorků, které urychlují generování celkových a individuálních hodnot důležitosti funkcí.
* **Vzorkování sady dat pro vyhodnocení**. Pokud uživatel projde velkou sadou zkušebních vzorů, ale nepotřebuje vyhodnotit všechny z nich, je možné nastavit parametr vzorkování na hodnotu true, aby se urychlil výpočet celkového vysvětlení modelu.

Následující diagram znázorňuje aktuální strukturu podporovaných vysvětlení.

[![Machine Learning architektura pro interpretaci](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Podporované modely strojového učení

`azureml.interpret`Balíček sady SDK podporuje modely vyškolené pomocí následujících formátů datových sad:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

Funkce vysvětlení přijímají jako vstup oba modely i kanály. Pokud je k dispozici model, model musí implementovat funkci předpovědi `predict` nebo `predict_proba` , která odpovídá Scikit konvenci. Pokud váš model tento model nepodporuje, můžete model zabalit do funkce, která generuje stejný výsledek jako `predict` nebo `predict_proba` v Scikit a použije tuto obálkovou funkci s vybraným doplňkem. Pokud je k dispozici kanál, funkce vysvětlení předpokládá, že spuštěný skript kanálu vrátí předpověď. Pomocí této techniky balení `azureml.interpret` může podporovat modely vyškolené prostřednictvím PyTorch, TensorFlow a Keras architektury hloubkového učení a také klasických modelů strojového učení.

## <a name="local-and-remote-compute-target"></a>Cíl pro místní a vzdálené výpočty

`azureml.interpret`Balíček je navržený tak, aby fungoval s místními i vzdálenými výpočetními cíli. Pokud spustíte místně, funkce SDK nebudou kontaktovat žádné služby Azure. 

Vysvětlení můžete spustit vzdáleně na Azure Machine Learning COMPUTE a protokolovat informace o vysvětlení do služby historie spouštění Azure Machine Learning. Po zaznamenání těchto informací jsou sestavy a vizualizace z vysvětlení snadno dostupné na Azure Machine Learning Studiu pro analýzu uživatelů.


## <a name="next-steps"></a>Další kroky

- Přečtěte si téma [postup](how-to-machine-learning-interpretability-aml.md) pro povolení interpretace u modelů v místním prostředí i na Azure Machine Learning vzdálených výpočetních prostředků. 
- Další scénáře najdete v [ukázkových poznámkových blocích](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) . 
- Pokud se zajímáte o výklad pro textové scénáře, přečtěte si téma [interpretace textu](https://github.com/interpretml/interpret-text), související Open Source úložiště pro [interpretaci komunit](https://github.com/interpretml/interpret-community/)pro techniky interpretace pro NLP. `azureml.interpret` balíček v současné době nepodporuje tyto techniky, ale můžete začít s [ukázkovým poznámkovým blokem při klasifikaci textu](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb).