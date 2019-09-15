---
title: Co jsou kanály ML
titleSuffix: Azure Machine Learning service
description: V tomto článku najdete informace o machine learningu kanály, které můžete sestavit s využitím Azure Machine Learning SDK for Python a výhody použití kanály. Machine learning (ML) kanály využívají odborníci přes data sestavení, optimalizovat a spravovat jejich strojového učení pracovních postupů.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 2547ca3af3039af53cb5fd5b244669b9152c21b3
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993285"
---
# <a name="what-are-ml-pipelines-in-azure-machine-learning-service"></a>Co jsou kanály ML ve službě Azure Machine Learning Service?

Přečtěte si, jak můžete vytvářet a spravovat kanály strojového učení pomocí služby Azure Machine Learning. 

Pomocí machine learning (ML) kanály, odborníci přes data, datovými architekty a odborníky v oblasti můžete spolupracovat na kroky:
+ Příprava dat, jako je například normalizations a transformace
+ Cvičení modelu
+ Vyhodnocení modelu
+ Nasazení

Zjistěte, jak [vytvořit svůj první kanál](how-to-create-your-first-pipeline.md).

![Kanály strojového učení ve službě Azure Machine Learning Service](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Kterou technologii kanálů Azure mám použít?

Cloud Azure nabízí několik dalších kanálů, z nichž každý má jiný účel. V následující tabulce jsou uvedeny různé kanály a jejich použití pro:

| Kanál | Co dělá | Kanonický kanál |
| ---- | ---- | ---- |
| Azure Machine Learning kanály | Definuje opakovaně použitelné pracovní postupy strojového učení, které se dají použít jako šablona pro scénáře vašeho strojového učení. | Model dat > |
| [Kanály Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Seskupuje, transformuje a řídí aktivity, které jsou potřebné k provedení úkolu.  | Data-> data |
| [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Průběžná integrace a doručování vaší aplikace na libovolnou platformu/libovolný Cloud  | > App/Service pro kód |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Proč vytvářet kanály pomocí služby Azure Machine Learning?

Kanály strojového učení optimalizují pracovní postup s využitím rychlosti, přenositelnosti a opakovaného použití, abyste se mohli soustředit na vaše odbornosti, strojové učení, nikoli na infrastrukturu a automatizaci.

Kanály jsou vytvořené z více **kroků**, které jsou v kanálu odlišné výpočetní jednotky. Každý krok může běžet nezávisle a používat izolované výpočetní prostředky.
Nezávislé kroky umožňují, aby více pracovníků dat pracovalo na stejném kanálu současně bez navýšení výpočetních prostředků a také usnadňuje používání různých výpočetních typů/velikostí pro každý krok.

Až bude kanál navržený, často se vyladí cyklus školení kanálu. Při opakovaném spuštění kanálu přejde na konkrétní kroky, které je třeba znovu spustit, například aktualizovaný školicí skript, a přeskočí, co se nezměnilo. Stejné paradigma se vztahuje na beze změny skripty používané pro provedení kroku. Tato funkce opětovného použití pomáhá zabránit spuštění náročného a časově náročného postupu, jako je přijímání a transformace dat, pokud se podkladová data nezmění.

Pomocí Azure Machine Learning můžete pro každý krok v kanálu použít různé sady nástrojů a architektury, jako je například PyTorch nebo TensorFlow. Azure koordinuje různé [výpočetní cíle](concept-azure-machine-learning-architecture.md) , které používáte, aby bylo možné snadno sdílet mezilehlá data se stejnými cíli výpočtů.

[Metriky pro testy kanálu můžete sledovat](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) přímo v Azure Portal nebo na [cílové stránce pracovního prostoru (Preview)](https://ml.azure.com). Po publikování kanálu můžete nakonfigurovat koncový bod REST, který umožňuje znovu spustit kanál z jakékoli platformy nebo zásobníku.

## <a name="key-advantages"></a>Klíčové výhody

Mezi klíčové výhody použití kanálů pro pracovní postupy machine learningu patří:

|Hlavní výhodou|Popis|
|:-------:|-----------|
|**Bezobslužné&nbsp;běží**|Naplánujte kroky paralelně nebo v rámci spolehlivého a bezobslužného zpracování. Příprava a modelování dat může mít poslední dny nebo týdny a kanály vám umožní soustředit se na další úlohy, když je proces spuštěný. |
|**Heterogenní COMPUTE**|Používejte více kanálů, které jsou spolehlivě koordinované napříč heterogenními a škálovatelnými výpočetními prostředky a umístěními úložiště. Využijte k efektivnímu využití dostupných výpočetních prostředků spuštěním jednotlivých kroků kanálu na různých výpočetních cílech, jako je HDInsight, GPU pro datové vědy GPU a datacihly.|
|**Opětovné použití**|Vytvořte šablony kanálu pro konkrétní scénáře, jako je například přeškolení a dávkové vyhodnocování. Triggery publikovaných kanálů z externích systémů prostřednictvím jednoduchých volání REST.|
|**Sledování a správy verzí**|Namísto ručního sledování dat a cest výsledků při iteraci můžete použít sadu SDK pro kanály k explicitnímu pojmenování a používání datových zdrojů, vstupů a výstupů. Skripty a data můžete spravovat i samostatně pro zvýšení produktivity.|
|**Prostřednictvím**|Kanály umožňují odborníkům přes data spolupracovat ve všech oblastech procesu návrhu strojového učení, přičemž můžou souběžně fungovat na postupech kanálu.|

## <a name="the-python-sdk-for-pipelines"></a>Python SDK pro kanály

[Pomocí sady Python SDK](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) můžete vytvářet kanály ml v upřednostňovaném integrovaném vývojovém prostředí (IDE) nebo v poznámkových blocích Jupyter. Sada SDK Azure Machine Learning nabízí imperativní konstrukce pro pořadí úloh a paralelní provádění kroků ve vašich kanálů, když je k dispozici žádná data závislost. 

Použití závislostí deklarativní data, můžete optimalizovat vaše úkoly. Sada SDK obsahuje architekturu předem připravených modulů pro běžné úlohy, jako je například přenos dat a publikování modelu. Rozhraní můžete roztáhnout tak, aby modeloval vlastní konvence implementací vlastních kroků opakovaně použitelných napříč kanály. Výpočetní cíle a prostředky úložiště můžete spravovat také přímo ze sady SDK.

Uložte kanály jako šablony a nasaďte je do koncového bodu REST pro úlohy dávkového vyhodnocování nebo přeškolení.

K dispozici jsou dva balíčky Pythonu pro kanály s Azure Machine Learning: [AzureML-Pipelines – Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) a [AzureML-Pipeline-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Pokud chcete rychle začít, použijte jeden z předem připravených modulů, jako je:

* Spuštění skriptu Pythonu v kroku s [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep)
* Přenos dat mezi možnostmi úložiště pomocí [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep)
* Vytvoření kroku kanálu AutoML pomocí [AutoMLStep](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlstep)

## <a name="next-steps"></a>Další postup

+ Zjistěte, jak [vytvořit svůj první kanál](how-to-create-your-first-pipeline.md).

+ Naučte se [spouštět Batch předpovědi pro velké objemy dat](tutorial-pipeline-batch-scoring-classification.md).

+ Prohlédněte si [Referenční dokumentace k sadě SDK pro kanály](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py).

+ Vyzkoušejte si příklady Jupyter poznámkových bloků předvádí [Azure Machine Learning kanálů](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Přečtěte si, jak [Spustit poznámkové bloky a prozkoumat tuto službu](samples-notebooks.md).
