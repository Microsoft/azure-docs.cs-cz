---
title: Co jsou kanály strojového učení?
titleSuffix: Azure Machine Learning
description: Naučte se, jak kanály strojového učení pomůžou vytvářet, optimalizovat a spravovat pracovní postupy machine learningu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 01/12/2021
ms.custom: devx-track-python
ms.openlocfilehash: e3f92f445068b98c12069577ddf61a71568e403b
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98871549"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Co jsou kanály Azure Machine Learning?

V tomto článku se dozvíte, jak kanál strojového učení pomáhá sestavovat, optimalizovat a spravovat pracovní postup služby Machine Learning. 

<a name="compare"></a>
## <a name="which-azure-pipeline-technology-should-i-use"></a>Kterou technologii kanálů Azure mám použít?

Cloud Azure nabízí několik typů kanálů, z nichž každý má jiný účel. V následující tabulce jsou uvedeny různé kanály a jejich použití pro:

| Scenario | Primární osoba | Nabídka Azure | Nabídka OSS | Kanonický kanál | Silné stránky | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Orchestrace modelů (Machine Learning) | Datový vědec | Azure Machine Learning kanály | Kanály Kubeflow | Model dat > | Distribuce, ukládání do mezipaměti, kód – první, opakované použití | 
| Orchestrace dat (PREP pro data) | Datový inženýr | [Kanály Azure Data Factory](../data-factory/concepts-pipelines-activities.md) | Postup Apache | Data-> data | Pohyb silného typu, aktivity zaměřené na data |
| Kód & orchestrace aplikace (CI/CD) | Vývojář aplikace/OPS | [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Code + Model – > App/Service | Nejvyšší podpora otevřených a flexibilních aktivit, fronty schvalování, fáze s pořízením | 

## <a name="what-can-machine-learning-pipelines-do"></a>Co můžou kanály strojového učení dělat?

Kanál Azure Machine Learning je nezávisle spustitelný pracovní postup dokončené úlohy strojového učení. Dílčí úlohy jsou v rámci kanálu zapouzdřené do série kroků. Kanál Azure Machine Learning může být jednoduchý jako ten, který volá skript Pythonu, takže _může_ dělat prakticky cokoli. Kanály _by se měly_ soustředit na úlohy strojového učení, jako jsou:

+ Přípravu dat, včetně importu, ověřování a čištění, transformace a nedefinované transformace, normalizace a fázování
+ Konfiguraci trénování, včetně parametrizace argumentů, cest k souborům a konfigurací protokolování a generování sestav
+ Školení a ověřování efektivně a opakovaně. Efektivita může pocházet z určení specifických podmnožin dat, různých výpočetních prostředků hardwaru, distribuovaného zpracování a sledování průběhu.
+ Nasazení, včetně správy verzí, škálování, zřizování a řízení přístupu

Nezávislé kroky umožňují, aby více pracovníků dat pracovalo na stejném kanálu současně bez překročení výpočetních prostředků. Jednotlivé kroky také usnadňují používání různých výpočetních typů/velikostí pro každý krok.

Až bude kanál navržený, často se vyladí cyklus školení kanálu. Při opakovaném spuštění kanálu přejde na kroky, které je třeba znovu spustit, například aktualizovaný školicí skript. Kroky, které není nutné znovu spustit, se přeskočí. 

S kanály se můžete rozhodnout použít jiný hardware pro různé úlohy. Azure koordinuje různé [výpočetní cíle](concept-azure-machine-learning-architecture.md) , které používáte, takže mezilehlá data hladce plynule natéká do výpočetních cílů pro příjem dat.

[Metriky pro testy kanálu můžete sledovat](./how-to-track-experiments.md) přímo v Azure Portal nebo na [cílové stránce pracovního prostoru (Preview)](https://ml.azure.com). Po publikování kanálu můžete nakonfigurovat koncový bod REST, který umožňuje znovu spustit kanál z jakékoli platformy nebo zásobníku.

V krátkém případě je možné s kanály považovat všechny komplexní úkoly životního cyklu strojového učení. Jiné technologie kanálu Azure mají své vlastní síly. [Azure Data Factory kanálů](../data-factory/concepts-pipelines-activities.md) v práci s daty a [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) je pravý Nástroj pro průběžnou integraci a nasazování. Pokud se ale zaměřujete na strojové učení, Azure Machine Learning kanály budou pravděpodobně nejlepší volbou pro potřeby pracovního postupu. 

### <a name="analyzing-dependencies"></a>Analýza závislostí

Mnoho programovacích ekosystémů má nástroje, které orchestrují závislosti prostředků, knihoven nebo kompilace. Obecně tyto nástroje používají k výpočtu závislostí časová razítka souborů. Když dojde ke změně souboru, budou aktualizovány pouze IT a jeho závislé položky (stažení, překompilování nebo zabalení). Kanál Azure Machine Learning rozšiřuje tento koncept. Stejně jako tradiční nástroje pro sestavení vypočítávají kanály závislosti mezi jednotlivými kroky a provádějí pouze nezbytné přepočty. 

Analýza závislostí v Azure Machine Learning kanálech je složitější než u jednoduchých časových razítek. Každý krok může běžet v jiném hardwarovém a softwarovém prostředí. Příprava dat může být časově náročný proces, ale nemusí běžet na hardwaru s výkonným grafickým procesorem, některé kroky můžou vyžadovat software pro konkrétní operační systém, možná budete chtít používat distribuované školení a tak dále. 

Azure Machine Learning automaticky orchestruje všechny závislosti mezi jednotlivými kroky kanálu. Tato orchestrace může zahrnovat rotující a vypnuté image Docker, připojení a odpojení výpočetních prostředků a přesouvání dat mezi jednotlivými kroky konzistentním a automatickým způsobem.

### <a name="coordinating-the-steps-involved"></a>Koordinace kroků, které jsou součástí

Při vytváření a spouštění `Pipeline` objektu dojde k následujícím krokům vysoké úrovně:

+ Pro každý krok vypočítá služba požadavky pro:
    + Výpočetní prostředky hardwaru
    + Prostředky operačního systému (Image Docker)
    + Softwarové prostředky (závislosti conda/virtualenv)
    + Vstupy dat 
+ Služba určuje závislosti mezi kroky, což vede k dynamickému spuštění grafu.
+ Když se každý uzel v grafu provádění spustí:
    + Služba konfiguruje potřebné hardwarové a softwarové prostředí (možná znovu používá stávající prostředky).
    + Spustí se krok, který poskytuje informace o protokolování a monitorování objektu, který ho obsahuje. `Experiment`
    + Po dokončení kroku se jeho výstupy připravují jako vstupy k dalšímu kroku a/nebo se zapsaly do úložiště.
    + Prostředky, které už nejsou potřeba, se dokončují a odpojí.

![Postup kanálu](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Vytváření kanálů pomocí sady Python SDK

V sadě [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)je kanál objektem Python definovaným v `azureml.pipeline.core` modulu. Objekt [kanálu](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?preserve-view=true&view=azure-ml-py) obsahuje uspořádanou sekvenci jednoho nebo více objektů [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?preserve-view=true&view=azure-ml-py) . `PipelineStep`Třída je abstraktní a vlastní kroky budou podtřídou, jako je například [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?preserve-view=true&view=azure-ml-py), [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?preserve-view=true&view=azure-ml-py)nebo [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?preserve-view=true&view=azure-ml-py). Třída [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?preserve-view=true&view=azure-ml-py) obsahuje opakovaně použitelnou sekvenci kroků, které je možné sdílet mezi kanály. `Pipeline`Spustí se jako součást `Experiment` .

Kanál služby Azure Machine Learning je přidružený k pracovnímu prostoru Azure Machine Learning a krok kanálu je přidružený k cílovému výpočetnímu prostředí, které je dostupné v daném pracovním prostoru. Další informace najdete v tématu [Vytvoření a Správa pracovních prostorů Azure Machine Learning v Azure Portal](./how-to-manage-workspace.md) nebo [Jaké jsou výpočetní cíle v Azure Machine Learning?](./concept-compute-target.md).

### <a name="a-simple-python-pipeline"></a>Jednoduchý kanál Pythonu

Tento fragment kódu ukazuje objekty a volání potřebné k vytvoření a spuštění `Pipeline` :

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
output_data = OutputFileDatasetConfig()
input_named = input_data.as_named_input('input')

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_named.as_download(), "--output", output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

Fragment kódu začíná běžnými Azure Machine Learning objekty, a `Workspace` , a `Datastore` , [ComputeTarget](/python/api/azureml-core/azureml.core.computetarget?preserve-view=true&view=azure-ml-py)a `Experiment` . Kód potom vytvoří objekty, které mají být uloženy `input_data` a `output_data` . `input_data`Je instancí třídy typu [DataSet](/python/api/azureml-core/azureml.data.filedataset?preserve-view=true&view=azure-ml-py) a `output_data` je instancí třídy [OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py). `OutputFileDatasetConfig`Výchozí chování je zkopírovat výstup do `workspaceblobstore` úložiště dat pod cestou `/dataset/{run-id}/{output-name}` , kde `run-id` je ID běhu a `output-name` je automaticky generovaná hodnota, pokud vývojář neurčí.

Pole `steps` obsahuje jeden prvek, `PythonScriptStep` který bude používat datové objekty a běží na `compute_target` . Kód potom vytvoří instanci objektu, který předává `Pipeline` do pole pracovní prostor a kroky. Volání `experiment.submit(pipeline)` zahájí běh kanálu Azure ml. Volání do `wait_for_completion()` bloků až do dokončení kanálu. 

Další informace o připojení kanálu k vašim datům najdete v článcích [přístup k datům v Azure Machine Learning](concept-data.md) a [přesouvání dat do a mezi kroky kanálu ml (Python)](how-to-move-data-in-out-of-pipelines.md). 

## <a name="building-pipelines-with-the-designer"></a>Vytváření kanálů pomocí návrháře

Vývojáři, kteří preferují vizuální návrhovou plochu, mohou pomocí návrháře Azure Machine Learning vytvářet kanály. K tomuto nástroji můžete přistupovat z výběru **Návrháře** na domovské stránce pracovního prostoru.  Návrhář umožňuje přetáhnout kroky na návrhovou plochu. 

Při vizuálním návrhu kanálů jsou vstupy a výstupy kroku zobrazeny viditelně. Můžete přetahovat datová připojení, což vám umožní rychle pochopit a upravovat tok dat vašeho kanálu.

![Příklad návrháře Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>Klíčové výhody

Mezi klíčové výhody použití kanálů pro pracovní postupy machine learningu patří:

|Výhoda klíče|Popis|
|:-------:|-----------|
|**Bezobslužné &nbsp; běhy**|Naplánujte kroky paralelně nebo v rámci spolehlivého a bezobslužného zpracování. Příprava a modelování dat může mít poslední dny nebo týdny a kanály vám umožní soustředit se na další úlohy, když je proces spuštěný. |
|**Heterogenní COMPUTE**|Používejte více kanálů, které jsou spolehlivě koordinované napříč heterogenními a škálovatelnými výpočetními prostředky a umístěními úložiště. Využijte k efektivnímu využití dostupných výpočetních prostředků spuštěním jednotlivých kroků kanálu na různých výpočetních cílech, jako je HDInsight, GPU pro datové vědy GPU a datacihly.|
|**Možnost opakovaného využití**|Vytvořte šablony kanálu pro konkrétní scénáře, jako je například přeškolení a dávkové vyhodnocování. Triggery publikovaných kanálů z externích systémů prostřednictvím jednoduchých volání REST.|
|**Sledování a správa verzí**|Namísto ručního sledování dat a cest výsledků při iteraci můžete použít sadu SDK pro kanály k explicitnímu pojmenování a používání datových zdrojů, vstupů a výstupů. Skripty a data můžete spravovat i samostatně pro zvýšení produktivity.|
| **Modularitu** | Oddělení otázek a izolace změn umožňuje softwaru vyvíjet se rychleji s vyšší kvalitou. | 
|**Spolupráce**|Kanály umožňují odborníkům přes data spolupracovat ve všech oblastech procesu návrhu strojového učení, přičemž můžou souběžně fungovat na postupech kanálu.|

## <a name="next-steps"></a>Další kroky

Azure Machine Learning kanály jsou výkonné zařízení, které začíná dodávat hodnoty ve fázích předčasného vývoje. Hodnota se zvyšuje při zvětšování týmu a projektu. Tento článek vysvětluje, jak se zadává kanály s Azure Machine Learning Python SDK a orchestruje se v Azure. Viděli jste nějaký jednoduchý zdrojový kód a zavedli jsme několik `PipelineStep` dostupných tříd. Měli byste mít představu o tom, kdy používat kanály Azure Machine Learning a jak je Azure spouští. 

+ Naučte se, jak [vytvořit první kanál](./how-to-create-machine-learning-pipelines.md).

+ Naučte se [spouštět Batch předpovědi pro velké objemy dat](tutorial-pipeline-batch-scoring-classification.md ).

+ V referenční dokumentaci k sadě SDK najdete [základní kroky kanálu](/python/api/azureml-pipeline-core/?preserve-view=true&view=azure-ml-py) a [kanály](/python/api/azureml-pipeline-steps/?preserve-view=true&view=azure-ml-py).

+ Vyzkoušejte si příklady Jupyter poznámkových bloků předvádí [Azure Machine Learning kanálů](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Přečtěte si, jak [Spustit poznámkové bloky a prozkoumat tuto službu](samples-notebooks.md).