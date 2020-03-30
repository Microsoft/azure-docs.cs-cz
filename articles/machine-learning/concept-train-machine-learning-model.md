---
title: Modelovacích metod
titleSuffix: Azure Machine Learning
description: Seznamte se s různými metodami, které můžete použít k trénování modelu pomocí Azure Machine Learning. Odhady poskytují snadný způsob práce s populárními frameworky, jako jsou Scikit-learn, TensorFlow, Keras, PyTorch a Chainer. Kanály machine learningu usnadňují plánování bezobslužných spuštění, použití heterogenních výpočetních prostředí a opakované použití částí pracovního postupu. A spouštění konfigurací poskytuje podrobnou kontrolu nad výpočetními cíli, na kterých je spuštěn proces školení.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 68ad9cc47d68f7bc3ae952f7e458781cdc1c4ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129773"
---
# <a name="train-models-with-azure-machine-learning"></a>Trénování modelů pomocí Azure Machine Learning

Azure Machine Learning poskytuje několik způsobů, jak trénovat vaše modely, od prvních řešení kódu pomocí sady SDK až po řešení s nízkým kódem, jako je automatizované strojové učení a vizuální návrhář. Pomocí následujícího seznamu určete, která metoda školení je pro vás vhodná:

+ [Azure Machine Learning SDK pro Python](#python-sdk): Python SDK poskytuje několik způsobů, jak trénovat modely, každý s různými možnostmi.

    | Metoda školení | Popis |
    | ----- | ----- |
    | [Spustit konfiguraci](#run-configuration) | Obecný **způsob, jak trénovat modely** je použít školicí skript a spustit konfiguraci. Konfigurace spuštění poskytuje informace potřebné ke konfiguraci tréninkového prostředí používaného k trénování modelu. Můžete provést konfiguraci spuštění, tréninkový skript a výpočetní cíl (tréninkové prostředí) a spustit trénovací úlohu. |
    | [Automatizované strojové učení](#automated-machine-learning) | Automatizované strojové učení umožňuje **trénovat modely bez rozsáhlých znalostí o datové vědě nebo programování**. Pro lidi s datovou vědou a programovacím pozadím poskytuje způsob, jak ušetřit čas a prostředky automatizací výběru algoritmů a hyperparametrikulací. Při použití automatizovaného strojového učení se nemusíte starat o definování konfigurace spuštění. |
    | [Odhady](#estimators) | Třídy odhadu **usnadňují trénování modelů založených na populárních architekturách strojového učení**. Existují třídy odhadu pro **Scikit-learn**, **PyTorch**, **TensorFlow**a **Chainer**. K dispozici je také obecný odhad, který lze použít s rámci, které ještě nemají vyhrazené třídy odhadu. Při použití odhadů se nemusíte starat o definování konfigurace spuštění. |
    | [Kanál strojového učení](#machine-learning-pipeline) | Kanály nejsou různé metody školení, ale **způsob definování pracovního postupu pomocí modulární, opakovaně použitelné kroky**, které mohou zahrnovat školení jako součást pracovního postupu. Kanály strojového učení podporují pomocí automatizovaného strojového učení, odhady a spouštění konfigurace pro trénování modelů. Vzhledem k tomu, že kanály nejsou zaměřeny konkrétně na školení, důvody pro použití kanálu jsou pestřejší než jiné metody školení. Obecně můžete použít kanál, když:<br>* Chcete **naplánovat bezobslužné procesy,** jako jsou dlouhodobé úlohy školení nebo příprava dat.<br>* Použijte **více kroků,** které jsou koordinovány napříč heterogenní výpočetní prostředky a umístění úložiště.<br>* Použijte kanál jako **opakovaně použitelnou šablonu** pro konkrétní scénáře, jako je například rekvalifikace nebo dávkové vyhodnocování.<br>* **Sledování a verze zdrojů dat, vstupů a výstupů** pro váš pracovní postup.<br>* Váš pracovní postup je **implementován různými týmy, které pracují na konkrétních krocích nezávisle**. Kroky pak lze spojit v kanálu k implementaci pracovního postupu. |

+ [Azure Machine Learning SDK pro Python](#r-sdk): Sada SDK používá balíček reticulate k vytvoření vazby na pythonovou sadku Azure Machine Learning. To umožňuje přístup k jádrovým objektům a metodám implementovaným v pythonu SDK z libovolného prostředí R.

+ **Návrhář:** Návrhář Azure Machine Learning (preview) poskytuje snadný vstupní bod do strojového učení pro vytváření proof konceptů nebo pro uživatele s malým kódováním. Umožňuje trénování modelů pomocí webového uživatelského uživatelského uživatelského uživatelského uživatelského uživatelského systému drag and drop. Kód Pythonu můžete použít jako součást návrhu nebo trénování modelů bez psaní kódu.

+ **CLI**: Funkce příkazu cli strojového učení poskytuje příkazy pro běžné úkoly s Azure Machine Learning a často se používá pro **skriptování a automatizaci úloh**. Například po vytvoření trénovacího skriptu nebo kanálu můžete pomocí funkce vpořádku spustit trénovací spuštění podle plánu nebo při aktualizaci datových souborů použitých pro trénování. Pro modely školení poskytuje příkazy, které odesílají úlohy školení. Může odesílat úlohy pomocí konfigurace spuštění nebo kanály.

Každá z těchto metod školení můžete použít různé typy výpočetních prostředků pro školení. Společně tyto prostředky jsou označovány jako [__výpočetní cíle__](concept-azure-machine-learning-architecture.md#compute-targets). Výpočetní cíl může být místní počítač nebo cloudový prostředek, jako je Azure Machine Learning Compute, Azure HDInsight nebo vzdálený virtuální počítač.

## <a name="python-sdk"></a>Python SDK

Sada Azure Machine Learning SDK pro Python umožňuje vytvářet a spouštět pracovní postupy strojového učení pomocí Azure Machine Learning. Se službou můžete pracovat z interaktivní relace Pythonu, poznámkových bloků Jupyter, kódu Sady Visual Studio nebo jiného prostředí IDE.

* [Co je sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Instalace/aktualizace sady SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Konfigurace vývojového prostředí pro Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Spustit konfiguraci

Obecnou tréninkovou úlohu s Azure Machine Learning lze definovat pomocí [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). Konfigurace spuštění se pak používá spolu s tréninkovými skripty k trénování modelu na výpočetním cíli.

Můžete začít s konfigurací spuštění pro místní počítač a pak přepnout na jeden pro cloudový výpočetní cíl podle potřeby. Při změně výpočetního cíle můžete pouze změnit konfiguraci spuštění, kterou používáte. Spuštění také protokoluje informace o úlohy školení, jako jsou vstupy, výstupy a protokoly.

* [Co je konfigurace spuštění?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Výuka: Trénujte svůj první model ML](tutorial-1st-experiment-sdk-train.md)
* [Příklady: Jupyter Notebook příklady tréninkových modelů](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [Postup: Nastavení a použití výpočetních cílů pro trénování modelu](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Automatizované strojové učení

Definujte iterace, nastavení hyperparametrů, featurization a další nastavení. Během školení Azure Machine Learning se snaží různé algoritmy a parametry paralelně. Školení se zastaví, jakmile narazí na kritéria ukončení, která jste definovali. Při použití odhadů se nemusíte starat o definování konfigurace spuštění.

> [!TIP]
> Kromě sady Python SDK můžete také používat automatizovanou ml prostřednictvím [studia Azure Machine Learning studio](https://ml.azure.com).

* [Co je automatizované strojové učení?](concept-automated-ml.md)
* [Kurz: Vytvoření prvního klasifikačního modelu pomocí automatizovaného strojového učení](tutorial-first-experiment-automated-ml.md)
* [Kurz: Pomocí automatizovaného strojového učení předvídejte tarify taxislužby](tutorial-auto-train-models.md)
* [Příklady: Příklady jupyterového notebooku pro automatizované strojové učení](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Postup: Konfigurace automatizovaných experimentů ML v Pythonu](how-to-configure-auto-train.md)
* [Postup: Autotrain model prognózy časových řad](how-to-auto-train-forecast.md)
* [Postup: Vytváření, zkoumání a nasazování automatizovaných experimentů strojového učení pomocí studia Azure Machine Learning studio](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>Odhady

Odhady usnadňují trénování modelů pomocí populárních rámců ML. Pokud používáte **Scikit-learn**, **PyTorch**, **TensorFlow**nebo **Chainer**, měli byste zvážit použití odhadu pro školení. K dispozici je také obecný odhad, který lze použít s rámci, které ještě nemají vyhrazené třídy odhadu. Při použití odhadů se nemusíte starat o definování konfigurace spuštění.

* [Co jsou odhady?](concept-azure-machine-learning-architecture.md#estimators)
* [Kurz: Trénování modelů klasifikace obrázků s daty MNIST a scikit-learn pomocí Azure Machine Learning](tutorial-train-models-with-aml.md)
* [Příklady: Příklady jupyterového notebooku používající odhady](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [Postup: Vytvoření odhadů v tréninku](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>Kanál strojového učení

Kanály strojového učení můžou používat výše uvedené metody školení (konfigurace spuštění, odhady a automatizované strojové učení). Kanály jsou více o vytvoření pracovního postupu, takže zahrnují více než jen školení modelů. V kanálu můžete trénovat model pomocí automatizovaného strojového učení, odhady nebo spustit konfigurace.

* [Co jsou kanály ML v Azure Machine Learning?](concept-ml-pipelines.md)
* [Vytváření a spouštění kanálů strojového učení pomocí Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
* [Kurz: Použití azure machine learning potrubí pro dávkové vyhodnocování](tutorial-pipeline-batch-scoring-classification.md)
* [Příklady: Příklady poznámkového bloku Jupyter pro kanály strojového učení](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Příklady: Potrubí s automatizovaným strojovým učením](https://aka.ms/pl-automl)
* [Příklady: Potrubí s odhady](https://aka.ms/pl-estimator)

## <a name="r-sdk"></a>R SDK

Sada R SDK umožňuje používat jazyk R s Azure Machine Learning. Sada SDK používá balíček reticulate k vytvoření vazby na sadu Python SDK Azure Machine Learning. To umožňuje přístup k jádrovým objektům a metodám implementovaným v pythonu SDK z libovolného prostředí R.

Další informace najdete v těchto článcích:

* [Kurz: Vytvoření logistického regresního modelu](tutorial-1st-r-experiment.md)
* [Azure Machine Learning SDK pro referenční číslo R](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Návrhář Azure Machine Learning

Návrhář umožňuje trénování modelů pomocí rozhraní drag and drop ve webovém prohlížeči.

+ [Co je to návrhář?](concept-designer.md)
+ [Výuka : Předpověď ceny automobilů](tutorial-designer-automobile-price-train-score.md)
+ [Regrese: Předpověď ceny](how-to-designer-sample-regression-automobile-price-basic.md)
+ [Klasifikace: Předvídání příjmů](how-to-designer-sample-classification-predict-income.md)
+ [Klasifikace: Předvídejte konve, apetence a up-selling](how-to-designer-sample-classification-churn.md)
+ [Klasifikace s vlastním skriptem R: Předvídejte zpoždění letu](how-to-designer-sample-classification-flight-delay.md)
+ [Klasifikace textu: Dataset Wikipedie SP 500](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>Rozhraní příkazového řádku

Cli strojového učení je rozšíření pro Azure CLI. Poskytuje příkazy příkazu příkazu příkazu příkazu příkazu pro různé platformy pro práci s Azure Machine Learning. Obvykle používáte cli k automatizaci úkolů, jako je například školení modelu strojového učení.

* [Použití rozšíření CLI pro Azure Machine Learning](reference-azure-machine-learning-cli.md)
* [MLOps v Azure](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [nastavit tréninková prostředí](how-to-set-up-training-targets.md).
