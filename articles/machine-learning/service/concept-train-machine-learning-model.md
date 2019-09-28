---
title: Metody školení modelu
titleSuffix: Azure Machine Learning
description: Seznamte se s různými metodami, které můžete použít k proškolování modelu pomocí Azure Machine Learning. Odhady poskytují snadný způsob, jak pracovat s oblíbenými rozhraními, jako jsou Scikit-učení, TensorFlow, Keras, PyTorch a chainer. Machine Learning kanály usnadňují plánování bezobslužného spuštění, použití výpočetních prostředí heterogenní a používání částí pracovního postupu. A konfigurace spuštění poskytují podrobnou kontrolu nad výpočetními cíli, na kterých běží školicí proces.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: a755fe1607e581cb0a25eb9bd90c2ba223829a46
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350596"
---
# <a name="train-models-with-azure-machine-learning"></a>Trénování modelů Azure Machine Learning

Azure Machine Learning poskytuje několik způsobů, jak proškolit vaše modely, od řešení Code First pomocí sady SDK přes řešení s nízkým kódem, jako je například automatizované Machine Learning a vizuální rozhraní. Pomocí následujícího seznamu určete, která metoda školení je pro vás nejvhodnější:

+ [Azure Machine Learning SDK pro Python](#python-sdk): Sada Python SDK nabízí několik způsobů výuky modelů, z nichž každá má různé možnosti.

    | Způsob školení | Popis |
    | ----- | ----- |
    | [Konfigurace spuštění](#run-configuration) | **Obecným způsobem, jak naučit modely** , je použít školicí skript a spustit konfiguraci. Konfigurace spuštění poskytuje informace potřebné ke konfiguraci školicího prostředí používaného pro výuku modelu. Můžete provést konfiguraci spuštění, školicí skript a cíl služby COMPUTE (školicí prostředí) a spustit školicí úlohu. |
    | [Automatizované Machine Learning](#automated-machine-learning) | Automatizované Machine Learning umožňuje **výukové modely bez rozsáhlých vědeckých znalostí nebo programování**. Pro lidi s datovou vědy a programováním na pozadí poskytuje způsob, jak ušetřit čas a prostředky díky automatizaci výběru algoritmu a ladění parametrů. Při použití automatizovaného strojového učení se nemusíte starat o definování konfigurace spuštění. |
    | [Odhady](#estimators) | Třídy Estimator usnadňují **výukové modely založené na oblíbených architekturách strojového učení**. K dispozici jsou třídy Estimator pro **Scikit-učit**, **PyTorch**, **TensorFlow**a **chainer**. K dispozici je také obecná Estimator, která může být použita s architekturami, které ještě nemají vyhrazenou třídu Estimator. Při použití odhady se nemusíte starat o definování konfigurace spuštění. |
    | [Kanál strojového učení](#machine-learning-pipeline) | Kanály nejsou jinou výukovou metodou, ale **způsob definování pracovního postupu pomocí modulárních a opakovaně použitelných kroků**, které můžou zahrnovat školení jako součást pracovního postupu. Kanály strojového učení podporují pomocí automatizovaného strojového učení, odhady a konfigurace spouštění pro výukové modely. Vzhledem k tomu, že se kanály nezaměřují konkrétně na školení, jsou důvody pro použití kanálu různorodější než jiné metody školení. Obecně platí, že můžete použít kanál v těchto případech:<br>* Chcete **naplánovat bezobslužné procesy** , jako je například dlouho běžící školicí úlohy nebo Příprava dat.<br>* Použijte **více kroků** , které jsou koordinovány napříč heterogenními výpočetními prostředky a umístěními úložiště.<br>* Kanál použijte jako **opakovaně použitelnou šablonu** pro konkrétní scénáře, jako je například přeškolení nebo dávkové vyhodnocování.<br>* :**sledování a verze zdrojů dat, vstupů a výstupů** pro váš pracovní postup.<br>* Pracovní postup je **implementován různými týmy, které pracují podle konkrétních kroků nezávisle**. Kroky je pak možné spojit společně v kanálu pro implementaci pracovního postupu. |

+ **Vizuální rozhraní**: Azure Machine Learning __vizuální rozhraní__ poskytuje strojové učení snadno se vstupním bodem pro vytváření důkazů konceptů nebo pro uživatele s malým prostředím kódování. Umožňuje naučit modely pomocí webového uživatelského rozhraní přetažení. V rámci návrhu můžete použít kód Pythonu nebo modely výuky bez psaní kódu.

+ **CLI**: Machine Learning CLI nabízí příkazy pro běžné úlohy s Azure Machine Learning a často se používá pro **skriptování a automatizaci úloh**. Když jste například vytvořili školicí skript nebo kanál, můžete použít rozhraní příkazového řádku ke spuštění školicího programu podle plánu nebo při aktualizaci datových souborů použitých pro školení. Pro školicí modely poskytuje příkazy, které odesílají školicí úlohy. Může odesílat úlohy pomocí konfigurací spuštění nebo kanálů.

Každá z těchto metod školení může pro školení použít různé typy výpočetních prostředků. Souhrnně se tyto prostředky označují jako [__výpočetní cíle__](concept-azure-machine-learning-architecture.md#compute-targets). Cílem výpočetní služby může být místní počítač nebo cloudový prostředek, jako je Azure Machine Learning COMPUTE, Azure HDInsight nebo vzdálený virtuální počítač.

## <a name="python-sdk"></a>Python SDK

Sada SDK Azure Machine Learning pro Python umožňuje sestavovat a spouštět pracovní postupy strojového učení s Azure Machine Learning. Můžete komunikovat se službou z interaktivní relace Pythonu, poznámkových bloků Jupyter, Visual Studio Code nebo jiného integrovaného vývojového prostředí (IDE).

* [Co je sada SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Nainstalovat/aktualizovat sadu SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Konfigurace vývojového prostředí pro Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Konfigurace spuštění

Úlohu obecného školení s Azure Machine Learning lze definovat pomocí [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). Pak se použije konfigurace spuštění společně se školicími skripty pro výuku modelu na výpočetním cíli.

Můžete začít s konfigurací spuštění pro místní počítač a pak v případě potřeby přejít na jeden pro cloudový cíl výpočtů. Když měníte cíl výpočtů, změníte jenom konfiguraci spuštění, kterou použijete. Spuštění také zaznamená informace o úloze školení, jako jsou vstupy, výstupy a protokoly.

* [Co je konfigurace spuštění?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Kurz: Výuka prvního modelu ML @ no__t-0
* @no__t – 0Examples: Jupyter Notebook příklady modelů školení @ no__t-0
* [Postup: Nastavení a použití výpočetních cílů pro školení modelů @ no__t-0

### <a name="automated-machine-learning"></a>Automatizované Machine Learning

Definujte iterace, nastavení parametrů, featurization a další nastavení. Během školení Azure Machine Learning pokusy o různé algoritmy a parametry paralelně. Školení se zastaví, jakmile narazí na kritéria ukončení, která jste definovali. Při použití odhady se nemusíte starat o definování konfigurace spuštění.

> [!TIP]
> V kromě k sadě Python SDK můžete pomocí [úvodní stránky pracovního prostoru (Preview)](https://ml.azure.com)použít také automatizované ml.

* [Co je automatizované strojové učení?](concept-automated-ml.md)
* [Kurz: Vytvoření prvního modelu klasifikace pomocí automatizovaného strojového učení @ no__t-0
* [Kurz: Předpověď taxislužby tarifs @ no__t-0 pomocí automatizovaného strojového učení
* @no__t – 0Examples: Příklady Jupyter Notebook automatizovaného strojového učení @ no__t-0
* [Postup: Konfigurace automatizovaných experimentů ML v Pythonu @ no__t-0
* [Postup: Autovýukový model prognózy časových řad @ no__t-0
* [Postup: Vytvářejte, Zkoumejte a nasaďte automatizované experimenty strojového učení s využitím úvodní stránky pracovního prostoru Azure Machine Learning (Preview) ](how-to-create-portal-experiments.md)

### <a name="estimators"></a>Odhady

Odhady usnadňuje výukové modely pomocí oblíbených ML architektur. Pokud používáte **Scikit-učení**, **PyTorch**, **TensorFlow**nebo **chainer**, měli byste zvážit použití Estimator pro školení. K dispozici je také obecná Estimator, která může být použita s architekturami, které ještě nemají vyhrazenou třídu Estimator. Při použití odhady se nemusíte starat o definování konfigurace spuštění.

* [Co jsou odhady?](concept-azure-machine-learning-architecture.md#estimators)
* [Kurz: Analýza modelů klasifikace obrázků pomocí MNIST ručně zapsaných dat a scikit – Naučte se pomocí Azure Machine Learning @ no__t-0
* @no__t – 0Examples: Jupyter Notebook příklady použití odhady @ no__t-0
* [Postup: Vytváření odhady ve školeních @ no__t-0

### <a name="machine-learning-pipeline"></a>Kanál strojového učení

Kanály strojového učení můžou používat dřív zmíněné metody školení (spuštění konfigurace, odhady a automatizovaného strojového učení). Kanály jsou více o vytváření pracovního postupu, takže zahrnují více než jenom školení modelů. V kanálu můžete model vytvořit pomocí automatizovaného strojového učení, odhady nebo konfigurací spouštění.

* [Co jsou kanály ML v Azure Machine Learning?](concept-ml-pipelines.md)
* [Vytvoření a spuštění kanálů strojového učení s Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
* [Kurz: Použití Azure Machine Learningch kanálů pro dávkové vyhodnocování @ no__t-0
* @no__t – 0Examples: Příklady Jupyter Notebook pro kanály strojového učení @ no__t-0
* @no__t – 0Examples: Kanál pomocí automatizovaného strojového učení @ no__t-0
* @no__t – 0Examples: Kanál s odhady @ no__t-0

## <a name="visual-interface"></a>Vizuální rozhraní

Vizuální rozhraní (Preview) umožňuje výukové modely pomocí rozhraní přetažení ve webovém prohlížeči.

+ [Co je to vizuální rozhraní?](ui-concept-visual-interface.md)
+ @no__t – 0Tutorial: Předpověď ceny automobilu @ no__t-0
+ @no__t – 0Regression: Předpověď ceny](how-to-ui-sample-regression-predict-automobile-price-basic.md)
+ @no__t – 0Classification: Předpověď úvěrového rizika](how-to-ui-sample-classification-predict-credit-risk-basic.md)
+ @no__t – 0Classification: Předpověď změn, appetencyí a prodejů](how-to-ui-sample-classification-predict-churn.md)

## <a name="cli"></a>Rozhraní příkazového řádku

Machine Learning CLI je rozšířením rozhraní příkazového řádku Azure CLI. Poskytuje příkazy rozhraní příkazového řádku pro různé platformy pro práci s Azure Machine Learning. Typicky k automatizaci úloh, jako je například školení modelu strojového učení, použijete rozhraní příkazového řádku.

* [Pro Azure Machine Learning použít rozšíření CLI](reference-azure-machine-learning-cli.md)
* [MLOps v Azure](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [nastavit školicí prostředí](how-to-set-up-training-targets.md).
