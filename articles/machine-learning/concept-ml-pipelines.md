---
title: Co jsou kanály ML
titleSuffix: Azure Machine Learning
description: V tomto článku se dozvíte o výhodách kanálů pro strojové učení (ML), které můžete vytvořit pomocí sady Azure Machine Learning SDK pro Python. Kanály pro strojové učení jsou používány odborníky přes data k sestavování, optimalizaci a správě pracovních postupů strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/06/2019
ms.openlocfilehash: c93c936664f65e7846f6c4ad82d9aead973fa129
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772597"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Co jsou kanály Azure Machine Learning?

Azure Machine Learning kanály umožňují vytvářet pracovní postupy v projektech machine learningu. Tyto pracovní postupy mají několik výhod: 

+ Jednoduchost
+ Rychlost
+ Opakovatelnost
+ Flexibilita
+ Správa verzí a sledování
+ Modularitu 
+ Kontrola kvality
+ Řízení nákladů

Tyto výhody se stanou významnou, jakmile se váš projekt Machine Learning pohybuje mimo rámec čistého průzkumu a iterace. Můžou být užitečné i jednoduché kanály s jedním krokem. Projekty strojového učení jsou často ve složitém stavu a může být pro zajištění přesného splnění jednoho pracovního postupu jednoduchý proces.

Zjistěte, jak [vytvořit svůj první kanál](how-to-create-your-first-pipeline.md).

![Kanály strojového učení v Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Kterou technologii kanálů Azure mám použít?

Cloud Azure nabízí několik dalších kanálů, z nichž každý má jiný účel. V následující tabulce jsou uvedeny různé kanály a jejich použití pro:

| Kanál | Výsledek | Kanonický kanál |
| ---- | ---- | ---- |
| Azure Machine Learning kanály | Definuje opakovaně použitelné pracovní postupy strojového učení, které se dají použít jako šablona pro scénáře vašeho strojového učení. | Model dat > |
| [Kanály Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Seskupuje, transformuje a řídí aktivity, které jsou potřebné k provedení úkolu.  | Data-> data |
| [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Průběžná integrace a doručování vaší aplikace na libovolnou platformu/libovolný Cloud  | > App/Service pro kód |

## <a name="what-can-azure-ml-pipelines-do"></a>Co můžou kanály Azure ML dělat?

Kanál Azure Machine Learning je nezávisle spustitelný pracovní postup dokončené úlohy strojového učení. Dílčí úkoly jsou zapouzdřené jako série kroků v rámci kanálu. Kanál Azure Machine Learning může být jednoduchý jako ten, který volá skript Pythonu, takže _může_ dělat prakticky cokoli. Kanály _by se měly_ soustředit na úlohy strojového učení, jako jsou:

+ Příprava dat zahrnující import, ověřování a čištění, munging a transformaci, normalizaci a přípravu
+ Konfigurace školení, včetně argumentů Parametrizace, cest k umístěním a konfigurací protokolování/vytváření sestav
+ Efektivní a opakované školení, které může zahrnovat určení specifických podmnožin dat, různých hardwarových výpočetních prostředků, distribuovaného zpracování a sledování průběhu
+ Nasazení, včetně správy verzí, škálování, zřizování a řízení přístupu 

Nezávislé kroky umožňují, aby více pracovníků dat pracovalo na stejném kanálu současně bez překročení výpočetních prostředků. Jednotlivé kroky také usnadňují používání různých výpočetních typů/velikostí pro každý krok.

Až bude kanál navržený, často se vyladí cyklus školení kanálu. Při opakovaném spuštění kanálu přejde na kroky, které je třeba znovu spustit, například aktualizovaný školicí skript. Kroky, které není nutné znovu spustit, se přeskočí. Stejná analýza platí pro nezměněné skripty používané pro splnění tohoto kroku. Tato funkce opětovného použití pomáhá zabránit spuštění náročného a časově náročného postupu, jako je přijímání a transformace dat, pokud se podkladová data nezmění.

Pomocí Azure Machine Learning můžete pro každý krok v kanálu použít různé sady nástrojů a architektury, jako je například PyTorch nebo TensorFlow. Azure koordinuje různé [výpočetní cíle](concept-azure-machine-learning-architecture.md) , které používáte, takže vaše mezilehlá data se dají sdílet s cílovými výpočetními cíli.

[Metriky pro testy kanálu můžete sledovat](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments) přímo v Azure Portal nebo na [cílové stránce pracovního prostoru (Preview)](https://ml.azure.com). Po publikování kanálu můžete nakonfigurovat koncový bod REST, který umožňuje znovu spustit kanál z jakékoli platformy nebo zásobníku.

V krátkém případě je možné s kanály považovat všechny komplexní úkoly životního cyklu strojového učení. Jiné technologie kanálu Azure mají vlastní sílu, například [Azure Data Factory kanály](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) pro práci s daty a [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) pro průběžnou integraci a nasazování. Pokud se ale zaměřujete na strojové učení, Azure Machine Learning kanály budou pravděpodobně nejlepší volbou pro potřeby pracovního postupu. 

## <a name="what-are-azure-ml-pipelines"></a>Co jsou kanály Azure ML?

Kanál Azure ML provádí kompletní logický pracovní postup s uspořádaným pořadím kroků. Každý krok je diskrétní akce zpracování. Kanály běží v kontextu Azure Machine Learning [experimentu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py).

V zcela rané fázi projektu ML je dobré mít jeden Jupyter Poznámkový blok nebo skript Pythonu, který provádí veškerou práci v pracovním prostoru a konfiguraci prostředků Azure, přípravu dat, konfiguraci, školení a ověřování. Ale stejně jako funkce a třídy se rychle přestanou preferovat na jeden imperativný blok kódu, pracovní postupy ML rychle budou vhodnější pro monolitické Poznámkový blok nebo skript. 

Na základě úloh modularizing ML podporují kanály pro počítačovou vědu, že by měla komponenta dělat (jenom jednu věc). Modularita je jasně zásadní pro úspěch projektu při programování v týmech, ale i při samotné práci, a to i v případě, že se jedná o samostatný projekt, který zahrnuje samostatné úkoly, z nichž každý má dobrou složitost Mezi úlohy patří: Konfigurace pracovního prostoru a přístup k datům, příprava dat, definice modelu a konfigurace a nasazení. Zatímco výstupy jednoho nebo více úloh tvoří vstupy do jiné, jsou přesné podrobnosti o implementaci jakékoli úlohy v nejbližším případě nepodstatných odčítání v dalším. V nejhorším případě výpočetní stav jedné úlohy může způsobit chybu v jiném. 

### <a name="analyzing-dependencies"></a>Analýza závislostí

Mnoho programovacích ekosystémů má nástroje, které orchestrují závislosti prostředků, knihoven nebo kompilace. Obecně tyto nástroje používají k výpočtu závislostí časová razítka souborů. Když dojde ke změně souboru, budou aktualizovány pouze IT a jeho závislé položky (stažení, překompilování nebo zabalení). Kanály Azure ML zvyšují tento koncept výrazně. Stejně jako tradiční nástroje pro sestavení vypočítávají kanály závislosti mezi jednotlivými kroky a provádějí pouze nezbytné přepočty. 

Analýza závislostí v kanálech Azure ML je složitější než u jednoduchých časových razítek. Každý krok může běžet v jiném hardwarovém a softwarovém prostředí. Příprava dat může být časově náročný proces, ale nemusí běžet na hardwaru s výkonným grafickým procesorem, některé kroky můžou vyžadovat software pro konkrétní operační systém, možná budete chtít používat distribuované školení a tak dále. I když úspory nákladů pro optimalizaci prostředků může být významná, může být zahlcení ručně juggle všechny různé varianty hardwarových a softwarových prostředků. To vše je ještě obtížnější, aniž by došlo k omylům v datech, která přenášíte mezi jednotlivými kroky. 

Kanály tento problém vyřeší. Azure Machine Learning automaticky orchestruje všechny závislosti mezi jednotlivými kroky kanálu. Tato orchestrace může zahrnovat rotující a vypnuté image Docker, připojení a odpojení výpočetních prostředků a přesouvání dat mezi jednotlivými kroky konzistentním a automatickým způsobem.

### <a name="reusing-results"></a>Znovu se používají výsledky.

Kromě toho může výstup kroku v případě, že zvolíte, být znovu použit. Pokud jako možnost zadáte možnost znovu použít a neexistují žádné nadřazené závislosti, které aktivují přepočítání, služba kanálu bude používat verzi výsledků kroku v mezipaměti. Takové opakované použití může výrazně zkrátit dobu vývoje. Pokud máte komplexní úlohu přípravy dat, pravděpodobně ji znovu spustíte častěji než je nezbytně nutné. Kanály k nim zbavují tyto obavy: v případě potřeby se krok spustí, pokud ne, nebude.

Všechny tyto analýzy, Orchestrace a aktivace jsou zpracovávány Azure Machine Learning při vytváření instance objektu [kanálu](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) , předání do `Experiment`a volání `submit()`. 

### <a name="coordinating-the-steps-involved"></a>Koordinace kroků, které jsou součástí

Když vytvoříte a spustíte objekt `Pipeline`, dojde k následujícím krokům vysoké úrovně:

+ Pro každý krok vypočítá služba požadavky pro:
    + Výpočetní prostředky hardwaru
    + Prostředky operačního systému (Image Docker)
    + Softwarové prostředky (závislosti conda/virtualenv)
    + Vstupy dat 
+ Služba určuje závislosti mezi kroky, což vede k dynamickému spuštění grafu.
+ Když se každý uzel v grafu provádění spustí:
    + Služba konfiguruje potřebné hardwarové a softwarové prostředí (možná znovu používá stávající prostředky).
    + Spustí se krok, který poskytuje informace o protokolování a monitorování objektu obsahujícího `Experiment`.
    + Po dokončení kroku se jeho výstupy připravují jako vstupy k dalšímu kroku a/nebo se zapsaly do úložiště.
    + Prostředky, které už nejsou potřeba, se dokončují a odpojí.

![Postup kanálu](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Vytváření kanálů pomocí sady Python SDK

V sadě [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)je kanál objektem Python definovaným v modulu `azureml.pipeline.core`. Objekt [kanálu](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) obsahuje uspořádanou sekvenci jednoho nebo více objektů [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) . Třída `PipelineStep` je abstraktní a skutečný postup bude podtřídou, jako je například [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py), [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)nebo [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). Třída [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) obsahuje opakovaně použitelnou sekvenci kroků, které je možné sdílet mezi kanály. `Pipeline` spouští jako součást `Experiment`.

Kanál Azure ML je přidružený k pracovnímu prostoru Azure Machine Learning a krok kanálu je přidružený k cílovému výpočetnímu prostředí, které je dostupné v daném pracovním prostoru. Další informace najdete v tématu [Vytvoření a Správa pracovních prostorů Azure Machine Learning v Azure Portal](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) nebo [Jaké jsou výpočetní cíle v Azure Machine Learning?](https://docs.microsoft.com/azure/machine-learning/concept-compute-target).

V Azure Machine Learning výpočetní cíl je prostředí, ve kterém nastane fáze ML. Softwarové prostředí může být vzdáleným virtuálním počítačem, Azure Machine Learning výpočetní prostředky, Azure Databricks, Azure Batch a tak dále. Hardwarová prostředí se také můžou výrazně lišit v závislosti na podpoře GPU, paměti, úložišti a tak dále. Pro každý krok můžete zadat výpočetní cíl, který vám poskytne jemně odstupňovanou kontrolu nad náklady. Můžete použít více nebo méně výkonné prostředky pro konkrétní akci, objem dat a potřeby výkonu vašeho projektu. 

## <a name="building-pipelines-with-the-designer"></a>Vytváření kanálů pomocí návrháře

Vývojáři, kteří preferují vizuální návrhovou plochu, mohou pomocí návrháře Azure Machine Learning vytvářet kanály. K tomuto nástroji můžete přistupovat z výběru **Návrháře** na domovské stránce pracovního prostoru.  Návrhář umožňuje přetáhnout kroky na návrhovou plochu. Pro rychlý vývoj můžete používat existující moduly v rámci spektra úloh ML. stávající moduly pokrývají vše od transformace dat až po výběr algoritmu až po školení k nasazení. Nebo můžete vytvořit plně vlastní kanál kombinováním vlastních kroků definovaných ve skriptech Pythonu.

Při vizuálním návrhu kanálů jsou vstupy a výstupy kroku zobrazeny viditelně. Můžete přetahovat datová připojení, což vám umožní rychle pochopit a upravovat tok dat vašeho kanálu.
 
![Příklad návrháře Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>Princip grafu spuštění

Kroky v kanálu mohou mít závislosti na jiných krocích. Služba kanálu Azure ML pracuje na analýze a orchestraci těchto závislostí. Uzly ve výsledném grafu provádění jsou kroky zpracování. Každý krok může zahrnovat vytvoření nebo opětovné použití konkrétní kombinace hardwaru a softwaru, opětovného použití uložených výsledků v mezipaměti atd. Orchestrace a optimalizace služby tohoto grafu provádění mohou významně zrychlit fázi ML a snížit náklady. 

Vzhledem k tomu, že kroky jsou spouštěny nezávisle, objekty k uložení vstupních a výstupních dat, která toků mezi kroky musí být definována externě. Toto je role [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)a přidružených tříd. Tyto datové objekty jsou přidruženy k objektu [úložiště](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) dat, který zapouzdřuje konfiguraci úložiště. Základní třída `PipelineStep` je vždy vytvořena řetězcem `name`, seznamem `inputs`a seznamem `outputs`. Většinou má také seznam `arguments` a často se seznam `resource_inputs`. Podtřídy budou mít obvykle i další argumenty (například `PythonScriptStep` vyžaduje název souboru a cestu ke skriptu, který chcete spustit). 

Spuštění grafu je acyklického, ale kanály je možné spouštět podle opakovaného plánu a můžou spouštět skripty Pythonu, které můžou zapisovat stavové informace do systému souborů, což umožňuje vytvářet komplexní profily. Pokud provedete návrh kanálu tak, aby se některé kroky mohly spustit paralelně nebo asynchronně, Azure Machine Learning transparentně zpracovává analýzu závislostí a koordinaci ventilátorů a ventilátorů. Obecně se nemusíte řešit s podrobnostmi o grafu provádění, ale jsou k dispozici prostřednictvím atributu [Pipeline. Graph](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) . 


### <a name="a-simple-python-pipeline"></a>Jednoduchý kanál Pythonu

Tento fragment kódu ukazuje objekty a volání potřebné k vytvoření a spuštění základní `Pipeline`:

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = DataReference(
    datastore=Datastore(ws, blob_store),
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")

output_data = PipelineData(
    "output_data",
    datastore=blob_store,
    output_name="output_data1")

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_data, "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

Fragment kódu začíná běžnými Azure Machine Learningmi objekty, `Workspace`, `Datastore`, [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)a `Experiment`. Potom kód vytvoří objekty pro uchování `input_data` a `output_data`. Pole `steps` obsahuje jeden prvek, `PythonScriptStep`, která bude používat datové objekty a běží na `compute_target`. Kód potom vytvoří instanci objektu `Pipeline` sám sebe a předává ho do pole pracovní prostor a kroky. Volání `experiment.submit(pipeline)` zahájí běh kanálu Azure ML. Volání `wait_for_completion()` blokuje až do dokončení kanálu. 

Další informace o připojení kanálu k vašim datům najdete v článcích [o tom, jak získat přístup k datům](how-to-access-data.md) a [jak registrovat datové sady](how-to-create-register-datasets.md). 

## <a name="best-practices-when-using-pipelines"></a>Osvědčené postupy při použití kanálů

Jak vidíte, vytvoření kanálu Azure ML je trochu složitější než spuštění skriptu. Kanály vyžadují, aby bylo nakonfigurováno a vytvořeno několik objektů Pythonu. 

Některé situace, které navrhují použití kanálu:

* V týmovém prostředí: rozdělte úlohy ML na více nezávislých kroků, aby vývojáři mohli pracovat a vyvíjet své programy nezávisle. 

* Při nasazení v rámci nebo blízko: nail konfiguraci a používejte plánované operace a operace řízené událostmi, aby zůstaly nad měnícími se daty.

* V počátečních fázích projektu ML nebo samostatně pracující: k automatizaci sestavení použijte kanály. Pokud jste se seznámili o opětovném vytvoření konfigurace a výpočetního stavu před implementací nového nápadu, je to signál, který vám může zvážit použití kanálu k automatizaci pracovního postupu. 

Můžete se snadno dostáváme o opětovném použití uložených v mezipaměti, jemně odstupňované kontrole nad náklady na výpočetní prostředky a izolaci procesů, ale kanály mají náklady. Mezi antipatterny patří:

* Použití kanálů jako jediného prostředku k oddělení obav. Integrované funkce, objekty a moduly Pythonu přecházejí dlouhou cestou, abyste se vyhnuli nematoucímu programovému stavu. Krok kanálu je mnohem dražší než volání funkce.

* Těžké párování mezi kroky kanálu. Pokud refaktoring závislého kroku často vyžaduje změnu výstupů předchozího kroku, je pravděpodobný, že samostatné kroky jsou aktuálně větší než výhoda. Další potvrzení, že kroky jsou u sebe vzájemně spojeny, jsou argumenty pro krok, který není data, ale příznaky pro řízení zpracování. 

* Předčasně Optimalizujte výpočetní prostředky. Například je často několik fází přípravy dat a druhý se často zobrazuje "Oh", zde je místo, kde můžu použít `MpiStep` pro paralelní programování, ale tady je místo, kde můžu použít `PythonScriptStep` s méně výkonnými výpočetními cíli, a tak dále. A v dlouhodobém běhu to může vést k výraznému provedení jemných kroků, které by se mohly ukázat jako vhodné, zejména v případě, že existuje možnost použít výsledky v mezipaměti, nikoli vždy přepočítat. Kanály ale neslouží jako náhrada za modul `multiprocessing`. 

Dokud projekt nezíská velké nebo téměř nasazování, kanály by se měly vymezit spíše než jemně odstupňované. Pokud si myslíte, že projekt ML se týká _fází_ a kanálu, který poskytuje kompletní pracovní postup k přesunutí do konkrétní fáze, jste na pravé cestě. 

## <a name="key-advantages"></a>Klíčové výhody

Mezi klíčové výhody použití kanálů pro pracovní postupy machine learningu patří:

|Hlavní výhodou|Popis|
|:-------:|-----------|
|**Bezobslužné&nbsp;běží**|Naplánujte kroky paralelně nebo v rámci spolehlivého a bezobslužného zpracování. Příprava a modelování dat může mít poslední dny nebo týdny a kanály vám umožní soustředit se na další úlohy, když je proces spuštěný. |
|**Heterogenní COMPUTE**|Používejte více kanálů, které jsou spolehlivě koordinované napříč heterogenními a škálovatelnými výpočetními prostředky a umístěními úložiště. Využijte k efektivnímu využití dostupných výpočetních prostředků spuštěním jednotlivých kroků kanálu na různých výpočetních cílech, jako je HDInsight, GPU pro datové vědy GPU a datacihly.|
|**Opětovné použití**|Vytvořte šablony kanálu pro konkrétní scénáře, jako je například přeškolení a dávkové vyhodnocování. Triggery publikovaných kanálů z externích systémů prostřednictvím jednoduchých volání REST.|
|**Sledování a správy verzí**|Namísto ručního sledování dat a cest výsledků při iteraci můžete použít sadu SDK pro kanály k explicitnímu pojmenování a používání datových zdrojů, vstupů a výstupů. Skripty a data můžete spravovat i samostatně pro zvýšení produktivity.|
| **Modularitu** | Oddělení otázek a izolace změn umožňuje softwaru vyvíjet se rychleji s vyšší kvalitou. | 
|**Spolupráce**|Kanály umožňují odborníkům přes data spolupracovat ve všech oblastech procesu návrhu strojového učení, přičemž můžou souběžně fungovat na postupech kanálu.|

## <a name="next-steps"></a>Další kroky

Kanály Azure ML jsou výkonné zařízení, které začíná dodávat hodnoty ve fázích předčasného vývoje. Hodnota se zvyšuje při zvětšování týmu a projektu. Tento článek vysvětluje, jak se zadává kanály s Azure Machine Learning Python SDK a orchestruje se v Azure. Viděli jste nějaký základní zdrojový kód a zavedli jsme několik `PipelineStep` tříd, které jsou k dispozici. Měli byste mít představu o tom, kdy používat kanály Azure ML a jak je Azure spouští. 


+ Zjistěte, jak [vytvořit svůj první kanál](how-to-create-your-first-pipeline.md).

+ Naučte se [spouštět Batch předpovědi pro velké objemy dat](tutorial-pipeline-batch-scoring-classification.md ).

+ V referenční dokumentaci k sadě SDK najdete [základní kroky kanálu](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) a [kanály](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

+ Vyzkoušejte si příklady Jupyter poznámkových bloků předvádí [Azure Machine Learning kanálů](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Přečtěte si, jak [Spustit poznámkové bloky a prozkoumat tuto službu](samples-notebooks.md).
