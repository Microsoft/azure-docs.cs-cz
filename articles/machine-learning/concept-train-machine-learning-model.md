---
title: Sestavování &ch výukových modelů
titleSuffix: Azure Machine Learning
description: Naučte se naučit modely pomocí Azure Machine Learning. Prozkoumejte různé metody školení a vyberte pro svůj projekt tu správnou.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 05/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: de3d9aa60322cc3e6e189f6f16c35d6f42c0cf61
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500406"
---
# <a name="train-models-with-azure-machine-learning"></a>Výuka modelů pomocí Azure Machine Learning

Azure Machine Learning poskytuje několik způsobů, jak proškolit vaše modely, od řešení z prvního kódu pomocí sady SDK až po řešení s nízkým kódem, jako je například automatizované Machine Learning a vizuální Návrhář. Pomocí následujícího seznamu určete, která metoda školení je pro vás nejvhodnější:

+ [Azure Machine Learning SDK pro Python](#python-sdk): sada Python SDK nabízí několik způsobů výuky modelů, z nichž každá má různé možnosti.

    | Způsob školení | Description |
    | ----- | ----- |
    | [Konfigurace spuštění](#run-configuration) | **Typický způsob, jak naučit modely** , je použít školicí skript a spustit konfiguraci. Konfigurace spuštění poskytuje informace potřebné ke konfiguraci školicího prostředí používaného pro výuku modelu. V konfiguraci spuštění můžete zadat školicí skript, cíl výpočtů a prostředí Azure ML a spustit školicí úlohu. |
    | [Automatizované strojové učení](#automated-machine-learning) | Automatizované Machine Learning umožňuje **výukové modely bez rozsáhlých vědeckých znalostí nebo programování**. Pro lidi s datovou vědy a programováním na pozadí poskytuje způsob, jak ušetřit čas a prostředky díky automatizaci výběru algoritmu a ladění parametrů. Při použití automatizovaného strojového učení se nemusíte starat o definování konfigurace spuštění. |
    | [Kanál strojového učení](#machine-learning-pipeline) | Kanály nejsou jinou výukovou metodou, ale **způsob definování pracovního postupu pomocí modulárních a opakovaně použitelných kroků**, které můžou zahrnovat školení jako součást pracovního postupu. Kanály strojového učení podporují pomocí automatizovaného strojového učení a spouštějí konfiguraci k výuce modelů. Vzhledem k tomu, že se kanály nezaměřují konkrétně na školení, jsou důvody pro použití kanálu různorodější než jiné metody školení. Obecně platí, že můžete použít kanál v těchto případech:<br>* Chcete **naplánovat bezobslužné procesy** , jako je například dlouho běžící školicí úlohy nebo Příprava dat.<br>* Použijte **více kroků** , které jsou koordinovány napříč heterogenními výpočetními prostředky a umístěními úložiště.<br>* Kanál použijte jako **opakovaně použitelnou šablonu** pro konkrétní scénáře, jako je například přeškolení nebo dávkové vyhodnocování.<br>* **Sledování a verze zdrojů dat, vstupů a výstupů** pro váš pracovní postup.<br>* Pracovní postup je **implementován různými týmy, které pracují podle konkrétních kroků nezávisle**. Kroky je pak možné spojit společně v kanálu pro implementaci pracovního postupu. |

+ Sada [sdk Azure Machine Learning pro r (Preview)](#r-sdk-preview): sada SDK pro jazyk r používá balíček reticulate k vytvoření vazby na sadu Azure Machine Learning SDK pro Python. To umožňuje přístup k základním objektům a metodám implementovaným v sadě Python SDK z jakéhokoli prostředí jazyka R.

+ **Návrhář**: Azure Machine Learning Designer poskytuje snadnou vstupní bod do strojového učení pro vytváření důkazů konceptů nebo pro uživatele s malým prostředím kódování. Umožňuje naučit modely pomocí webového uživatelského rozhraní přetažení. V rámci návrhu můžete použít kód Pythonu nebo modely výuky bez psaní kódu.

+ **CLI**: rozhraní příkazového řádku Machine Learning poskytuje příkazy pro běžné úlohy s Azure Machine Learning a často se používá pro **skriptování a automatizaci úloh**. Když jste například vytvořili školicí skript nebo kanál, můžete použít rozhraní příkazového řádku ke spuštění školicího programu podle plánu nebo při aktualizaci datových souborů použitých pro školení. Pro školicí modely poskytuje příkazy, které odesílají školicí úlohy. Může odesílat úlohy pomocí konfigurací spuštění nebo kanálů.

Každá z těchto metod školení může pro školení použít různé typy výpočetních prostředků. Souhrnně se tyto prostředky označují jako [__výpočetní cíle__](concept-azure-machine-learning-architecture.md#compute-targets). Cílem výpočetní služby může být místní počítač nebo cloudový prostředek, jako je Azure Machine Learning COMPUTE, Azure HDInsight nebo vzdálený virtuální počítač.

## <a name="python-sdk"></a>Python SDK

Sada SDK Azure Machine Learning pro Python umožňuje sestavovat a spouštět pracovní postupy strojového učení s Azure Machine Learning. Můžete komunikovat se službou z interaktivní relace Pythonu, poznámkových bloků Jupyter, Visual Studio Code nebo jiného integrovaného vývojového prostředí (IDE).

* [Co je sada SDK Azure Machine Learning pro Python](/python/api/overview/azure/ml/intro)
* [Instalace/aktualizace sady SDK](/python/api/overview/azure/ml/install)
* [Konfigurace vývojového prostředí pro Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Konfigurace spuštění

Úlohu obecného školení s Azure Machine Learning lze definovat pomocí [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig). Pak se použije konfigurace spuštění skriptu spolu se školicími skripty k výuce modelu na výpočetním cíli.

Můžete začít s konfigurací spuštění pro místní počítač a pak v případě potřeby přejít na jeden pro cloudový cíl výpočtů. Když měníte cíl výpočtů, změníte jenom konfiguraci spuštění, kterou použijete. Spuštění také zaznamená informace o úloze školení, jako jsou vstupy, výstupy a protokoly.

* [Co je konfigurace spuštění?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Kurz: analýza prvního modelu ML](tutorial-1st-experiment-sdk-train.md)
* [Příklady: příklady školicích modelů Jupyter Notebook a Pythonu](https://github.com/Azure/azureml-examples)
* [Postupy: konfigurace spuštění školení](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Automatizované strojové učení

Definujte iterace, nastavení parametrů, featurization a další nastavení. Během školení Azure Machine Learning pokusy o různé algoritmy a parametry paralelně. Školení se zastaví, jakmile narazí na kritéria ukončení, která jste definovali.

> [!TIP]
> Kromě sady Python SDK můžete také používat automatizované ML prostřednictvím [Azure Machine Learning studia](https://ml.azure.com).

* [Co je automatizované strojové učení?](concept-automated-ml.md)
* [Kurz: Vytvoření prvního modelu klasifikace pomocí automatizovaného strojového učení](tutorial-first-experiment-automated-ml.md)
* [Kurz: Využití automatizovaného strojového učení k predikci ceny jízdy taxíkem](tutorial-auto-train-models.md)
* [Příklady: příklady Jupyter Notebook pro automatizované strojové učení](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Postupy: Konfigurace automatizovaných experimentů ML v Pythonu](how-to-configure-auto-train.md)
* [Postupy: autovýuka modelu prognózy časových řad](how-to-auto-train-forecast.md)
* [Postupy: vytváření, zkoumání a nasazení automatických experimentů strojového učení pomocí nástroje Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md)

### <a name="machine-learning-pipeline"></a>Kanál strojového učení

Kanály strojového učení můžou používat dřív uvedené metody školení. Kanály jsou více o vytváření pracovního postupu, takže zahrnují více než jenom školení modelů. V kanálu můžete model přeškolit pomocí automatizovaného strojového učení nebo konfigurací spouštění.

* [Co jsou kanály ML v Azure Machine Learning?](concept-ml-pipelines.md)
* [Vytvoření a spuštění kanálů strojového učení s Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md)
* [Kurz: použití kanálů Azure Machine Learning pro dávkové vyhodnocování](tutorial-pipeline-batch-scoring-classification.md)
* [Příklady: příklady Jupyter Notebook pro kanály strojového učení](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Příklady: kanál s automatickým strojovým učením](https://aka.ms/pl-automl)

### <a name="understand-what-happens-when-you-submit-a-training-job"></a>Informace o tom, co se stane, když odešlete školicí úlohu

Životní cyklus školení Azure se skládá z těchto součástí:

1. Zipování soubory ve složce projektu, které budou ignorovány, které jsou zadány v souboru _. amlignore_ nebo _. gitignore_
1. Škálování výpočetního clusteru 
1. Sestavování nebo stahování souboru Dockerfile do výpočetního uzlu 
    1. Systém vypočítá hodnotu hash: 
        - Základní obrázek 
        - Vlastní kroky Docker (viz [nasazení modelu pomocí vlastního Docker Base image](./how-to-deploy-custom-docker-image.md))
        - Definice conda YAML (viz téma [vytvoření & použití softwarových prostředí v Azure Machine Learning](./how-to-use-environments.md))
    1. Systém používá tuto hodnotu hash jako klíč při vyhledávání Azure Container Registry pracovního prostoru (ACR).
    1. Pokud se nenajde, vyhledá shodu v globálním ACR
    1. Pokud není nalezen, systém vytvoří novou bitovou kopii (která bude uložena do mezipaměti a bude registrována v pracovním prostoru ACR).
1. Stažení souboru projektu zip do dočasného úložiště na výpočetním uzlu
1. Rozzipovává soubor projektu
1. Prováděný výpočetní uzel `python <entry script> <arguments>`
1. Ukládání protokolů, souborů modelů a dalších souborů zapsaných do `./outputs` účtu úložiště přidruženého k pracovnímu prostoru
1. Snížení kapacity výpočetních prostředků, včetně odebrání dočasného úložiště 

Pokud se rozhodnete, že budete na svém místním počítači ("konfigurovat jako místní běh"), nemusíte používat Docker. Pokud si zvolíte (příklad najdete v části [Konfigurace kanálu ml](./how-to-debug-pipelines.md) ), můžete použít Docker lokálně.

## <a name="r-sdk-preview"></a>R SDK (Preview)

Sada R SDK umožňuje používat jazyk R s Azure Machine Learning. Sada SDK používá balíček reticulate k navázání Azure Machine Learning Python SDK. Díky tomu máte přístup k základním objektům a metodám implementovaným v sadě Python SDK z jakéhokoli prostředí jazyka R.

Další informace najdete v následujících článcích:

* [Referenční informace o Azure Machine Learning SDK pro R](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Návrhář služby Azure Machine Learning

Návrhář umožňuje výukové modely pomocí rozhraní přetažení ve webovém prohlížeči.

+ [Co je Návrhář?](concept-designer.md)
+ [Kurz: předpověď ceny automobilu](tutorial-designer-automobile-price-train-score.md)

## <a name="many-models-solution-accelerator"></a>Řada akcelerátorů řešení modelů

[Mnohé modely řešení](https://aka.ms/many-models) (Preview) jsou sestavené na Azure Machine Learning a umožňují výuku, provozování a správu stovek nebo dokonce tisíců modelů strojového učení.

Například vytvoření modelu __pro každou instanci nebo jednotlivce__ v následujících scénářích může vést k lepším výsledkům:

* Předpověď prodeje pro každé individuální úložiště
* Prediktivní údržba stovek ropných zásobníků
* Přizpůsobení prostředí pro jednotlivé uživatele.

Další informace najdete v tématu o [mnoha modelech řešení modelů](https://aka.ms/many-models) na GitHubu.

## <a name="cli"></a>Rozhraní příkazového řádku

Machine Learning CLI je rozšířením rozhraní příkazového řádku Azure CLI. Poskytuje příkazy rozhraní příkazového řádku pro různé platformy pro práci s Azure Machine Learning. Typicky k automatizaci úloh, jako je například školení modelu strojového učení, použijete rozhraní příkazového řádku.

* [Pro Azure Machine Learning použít rozšíření CLI](reference-azure-machine-learning-cli.md)
* [MLOps v Azure](https://github.com/microsoft/MLOps)

## <a name="vs-code"></a>VS Code

Ke spuštění a správě školicích úloh můžete použít rozšíření VS Code. Další informace najdete v tématu [Průvodce správou prostředků vs Code](how-to-manage-resources-vscode.md#experiments) .

## <a name="next-steps"></a>Další kroky

Naučte se [Konfigurovat školicí běh](how-to-set-up-training-targets.md).