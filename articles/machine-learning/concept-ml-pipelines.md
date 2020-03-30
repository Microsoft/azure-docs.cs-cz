---
title: Co jsou ml potrubí
titleSuffix: Azure Machine Learning
description: V tomto článku se dozvíte výhody kanálu strojového učení (ML), které můžete sestavit pomocí sady Azure Machine Learning SDK pro Python. Kanály strojového učení používají datoví vědci k vytváření, optimalizaci a správě svých pracovních postupů strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/06/2019
ms.openlocfilehash: da45c0db027dffc89bd058b70331a4bd6d093b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336965"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Co jsou kanály Azure Machine Learning?

Kanály Azure Machine Learning umožňují vytvářet pracovní postupy ve vašich projektech strojového učení. Tyto pracovní postupy mají řadu výhod: 

+ Jednoduchost
+ Rychlost
+ Opakovatelnost
+ Flexibilita
+ Správa verzí a sledování
+ Modularita 
+ Kontrola kvality
+ Kontrola nákladů

Tyto výhody se stanou významnými, jakmile se váš projekt strojového učení přesune za čistý průzkum a do iterace. Cenné mohou být i jednoduché jednostupňové kanály. Projekty strojového učení jsou často ve složitém stavu a může být úlevou učinit z přesného dokončení jednoho pracovního postupu triviální proces.

Přečtěte si, jak [vytvořit první kanál](how-to-create-your-first-pipeline.md).

![Kanály strojového učení v Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Kterou technologii kanálu Azure mám použít?

Cloud Azure poskytuje několik dalších kanálů, každý s jiným účelem. V následující tabulce jsou uvedeny různé kanály a jejich použití:

| Scénář | Primární persona | Nabídka Azure | Nabídka OSS | Kanonické potrubí | Silné | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Orchestrace modelu (Strojové učení) | Odborník přes data | Kanály Azure Machine Learning | Potrubí Kubeflow | Model > dat | Distribuce, ukládání do mezipaměti, první kód, opakované použití | 
| Orchestrace dat (příprava dat) | Datový inženýr | [Kanály Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Apache proudění vzduchu | Data -> Data | Silně napsaná pohyb. Aktivity zaměřené na data. |
| Kód & aplikace orchestrace (CI/CD) | Vývojář aplikací / Ops | [Kanály Azure DevOps](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Kód + Model -> App/Service | Většina otevřené a flexibilní podpory aktivit, schvalovacích front, fází s gatingem | 


## <a name="what-can-azure-ml-pipelines-do"></a>Co můžou kanály Azure ML dělat?

Kanál Azure Machine Learning je nezávisle spustitelný pracovní postup dokončení úlohy strojového učení. Dílčí úlohy jsou v rámci kanálu zapouzdřené do série kroků. Kanál Azure Machine Learning může být stejně jednoduchý jako ten, který volá skript Pythonu, takže _může_ dělat cokoliv. Kanály by se _měly_ zaměřit na úlohy strojového učení, jako jsou:

+ Přípravu dat, včetně importu, ověřování a čištění, transformace a nedefinované transformace, normalizace a fázování
+ Konfiguraci trénování, včetně parametrizace argumentů, cest k souborům a konfigurací protokolování a generování sestav
+ Efektivní a opakované školení a ověřování. Efektivita může pocházet z určení konkrétních podmnožin dat, různých hardwarových výpočetních prostředků, distribuovaného zpracování a monitorování průběhu.
+ Nasazení, včetně správy verzí, škálování, zřizování a řízení přístupu 

Nezávislé kroky umožňují více datových vědců pracovat na stejném kanálu ve stejnou dobu bez nadměrného zdanění výpočetních prostředků. Samostatné kroky také usnadňují použití různých typů výpočetních prostředků nebo velikostí pro každý krok.

Po navržení kanálu je často více doladění kolem tréninkové smyčky kanálu. Při opětovném spuštění kanálu spuštění přejde na kroky, které je třeba znovu spustit, jako je například aktualizovaný školicí skript. Kroky, které není nutné znovu spustit, jsou přeskočeny. Stejná analýza platí pro nezměněné skripty použité k dokončení kroku. Tato funkce opakovaného použití pomáhá vyhnout se spuštění nákladných a časově náročných kroků, jako je ingestování a transformace dat, pokud se nezměnila základní data.

S Azure Machine Learning můžete pro každý krok ve vašem kanálu používat různé sady nástrojů a architektury, jako je PyTorch nebo TensorFlow. Azure koordinuje různé [výpočetní cíle, které](concept-azure-machine-learning-architecture.md) používáte, takže vaše zprostředkující data můžete sdílet s navazující výpočetní cíle.

[Metriky experimentů s kanály](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments) můžete sledovat přímo na webu Azure Portal nebo [na vstupní stránce pracovního prostoru (preview).](https://ml.azure.com) Po publikování kanálu můžete nakonfigurovat koncový bod REST, který umožňuje znovu spustit kanál z libovolné platformy nebo zásobníku.

Stručně řečeno, všechny složité úkoly životního cyklu strojového učení lze pomoci s kanály. Jiné technologie kanálu Azure mají své vlastní silné stránky, jako jsou [kanály Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) pro práci s daty a Azure [Pipelines](https://azure.microsoft.com/services/devops/pipelines/) pro průběžnou integraci a nasazení. Ale pokud se zaměřujete na strojové učení, kanály Azure Machine Learning budou pravděpodobně nejlepší volbou pro vaše potřeby pracovního postupu. 

## <a name="what-are-azure-ml-pipelines"></a>Co jsou kanály Azure ML?

Kanál Azure ML provádí kompletní logický pracovní postup s seřazenou posloupnost kroků. Každý krok je samostatná akce zpracování. Kanály spustit v kontextu [experimentu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)Azure Machine Learning .

V raných fázích projektu ML je v pořádku mít jeden poznámkový blok Jupyter nebo skript Pythonu, který provádí veškerou práci pracovního prostoru Azure a konfigurace prostředků, přípravy dat, konfigurace spuštění, školení a ověřování. Ale stejně jako funkce a třídy rychle stanou vhodnější mikiny bloku kódu, ML pracovní postupy rychle stanou vhodnější než monolitický notebook nebo skript. 

Modularizací úkolů ML podporují kanály imperativ počítačové vědy, že komponenta by měla "dělat (pouze) jednu věc dobře." Modularita je jednoznačně zásadní pro úspěch projektu při programování v týmech, ale i při práci sám, i malý projekt ML zahrnuje samostatné úkoly, z nichž každý má dobrou složitost. Mezi úkoly patří: konfigurace pracovního prostoru a přístup k datům, příprava dat, definice a konfigurace modelu a nasazení. Zatímco výstupy jednoho nebo více úkolů tvoří vstupy do jiného, přesné podrobnosti implementace jednoho úkolu jsou v nejlepším případě irelevantní rozptýlení v dalším. V nejhorším případě výpočetní stav jednoho úkolu může způsobit chybu v jiném. 

### <a name="analyzing-dependencies"></a>Analýza závislostí

Mnoho programovacích ekosystémů má nástroje, které orchestrují závislosti prostředků, knihovny nebo kompilace. Obecně platí, že tyto nástroje používají časová razítka souboru k výpočtu závislostí. Při změně souboru jsou aktualizovány pouze jeho a jeho závislé položky (staženy, překompilovány nebo zabaleny). Azure ML kanály rozšířit tento koncept dramaticky. Stejně jako tradiční nástroje sestavení, kanály vypočítat závislosti mezi kroky a provádět pouze nezbytné přepočty. 

Analýza závislostí v kanálech Azure ML je složitější než jednoduchá časová razítka. Každý krok může běžet v jiném hardwarovém a softwarovém prostředí. Příprava dat může být časově náročný proces, ale není nutné spustit na hardwaru s výkonnými grafickými procesory, některé kroky mohou vyžadovat software specifický pro operační systém, můžete chtít použít distribuované školení a tak dále. Zatímco úspory nákladů na optimalizaci zdrojů mohou být významné, může být ohromující ručně žonglovat se všemi různými variantami hardwarových a softwarových zdrojů. Je to ještě těžší udělat všechno, aniž by někdy dělat chybu v datech, které přenášejí mezi kroky. 

Kanály vyřešit tento problém. Azure Machine Learning automaticky orchestruje všechny závislosti mezi kroky kanálu. Tato orchestrace může zahrnovat předení nahoru a dolů image Dockeru, připojení a odpojení výpočetních prostředků a přesunutí dat mezi kroky konzistentním a automatickým způsobem.

### <a name="reusing-results"></a>Opakované použití výsledků

Kromě toho výstup kroku může být, pokud zvolíte, znovu použít. Pokud zadáte opakované použití jako možnost a neexistují žádné upstream závislosti aktivační přepočet, služba kanálu použije verzi výsledků kroku uloženou v mezipaměti. Takové opakované použití může výrazně snížit dobu vývoje. Pokud máte komplexní úlohu přípravy dat, pravděpodobně jej znovu spustíte častěji, než je nezbytně nutné. Potrubí vás zbaví těchto obav: pokud je to nutné, krok poběží, pokud ne, nebude.

Všechny tyto analýzy závislostí, orchestrace a aktivace jsou zpracovány Azure Machine Learning při vytváření `Experiment`instancí [objektu Pipeline,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) předat jej a volání `submit()`. 

### <a name="coordinating-the-steps-involved"></a>Koordinace kroků, které jsou s tím spojeny

Při vytváření a `Pipeline` spuštění objektu dojde k následujícím krokům vysoké úrovně:

+ Pro každý krok služba vypočítá požadavky pro:
    + Výpočetní prostředky hardwaru
    + Prostředky operačního systému (image Dockeru)
    + Softwarové zdroje (conda / virtualenv závislosti)
    + Datové vstupy 
+ Služba určuje závislosti mezi kroky, výsledkem je graf dynamického spuštění
+ Při spuštění každého uzlu v grafu spuštění:
    + Služba konfiguruje potřebné hardwarové a softwarové prostředí (možná opětovné použití stávajících prostředků)
    + Krok běží, poskytuje protokolování a sledování `Experiment` informací o jeho obsahující objekt
    + Po dokončení kroku jsou jeho výstupy připraveny jako vstupy k dalšímu kroku a/nebo zapsány do úložiště
    + Prostředky, které již nejsou potřeba, jsou dokončeny a odpojeny

![Kroky kanálu](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Vytváření kanálů pomocí sady Python SDK

V [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), kanál je `azureml.pipeline.core` objekt Pythondefinované v modulu. Objekt [Pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) obsahuje uspořádanou posloupnost jednoho nebo více objektů [PipelineStep.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) Třída `PipelineStep` je abstraktní a skutečné kroky budou podtřídy jako [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py), [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)nebo [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). Třída [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) obsahuje opakovaně použitelnou posloupnost kroků, které lze sdílet mezi kanály. A `Pipeline` běží jako `Experiment`součást .

Kanál Azure ML je přidružený k pracovnímu prostoru Azure Machine Learning a krok kanálu je přidružený k výpočetnímu cíli, který je k dispozici v tomto pracovním prostoru. Další informace najdete [v tématu Vytvoření a správa pracovních prostorů Azure Machine Learning na webu Azure Portal](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) nebo Co jsou výpočetní cíle v Azure Machine [Learning?](https://docs.microsoft.com/azure/machine-learning/concept-compute-target).

V Azure Machine Learning je výpočetní cíl prostředí, ve kterém dochází k fázi ML. Softwarové prostředí může být vzdálený virtuální počítač, Azure Machine Learning Compute, Azure Databricks, Azure Batch a tak dále. Hardwarové prostředí se také může značně lišit v závislosti na podpoře GPU, paměti, úložišti atd. Můžete zadat cíl výpočetních prostředků pro každý krok, který vám dává jemně odstupňovanou kontrolu nad náklady. Můžete použít více nebo méně výkonné prostředky pro konkrétní akce, objem dat a požadavky na výkon projektu. 

## <a name="building-pipelines-with-the-designer"></a>Výstavba potrubí s projektantem

Vývojáři, kteří dávají přednost vizuální návrh povrchu můžete použít Návrhář Azure Machine Learning k vytvoření kanály. K tomuto nástroji můžete přistupovat z výběru **návrháře** na domovské stránce pracovního prostoru.  Návrhář umožňuje přetahovat kroky na návrhovou plochu. Pro rychlý vývoj můžete použít stávající moduly napříč spektrem úloh ML; stávající moduly pokrývají vše od transformace dat přes výběr algoritmů až po školení až po nasazení. Nebo můžete vytvořit plně vlastní kanál kombinací vlastních kroků definovaných ve skriptech Pythonu.

Při vizuálnínávrh potrubí, vstupy a výstupy kroku jsou zobrazeny viditelně. Datové připojení můžete přetáhnout a můžete je rychle pochopit a upravit.
 
![Příklad návrháře Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>Principy grafu spuštění

Kroky v rámci kanálu může mít závislosti na jiné kroky. Služba kanálu Azure ML provádí analýzu a orchestraci těchto závislostí. Uzly ve výsledném "spuštění grafu" jsou kroky zpracování. Každý krok může zahrnovat vytvoření nebo opětovné použití určité kombinace hardwaru a softwaru, opakované použití výsledků uložených v mezipaměti a tak dále. Orchestrace a optimalizace tohoto grafu spuštění služby může výrazně urychlit fázi ML a snížit náklady. 

Vzhledem k tomu, že kroky běží nezávisle, musí být objekty pro uložení vstupních a výstupních dat, která toky mezi kroky, definovány externě. Toto je role [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)a přidružené třídy. Tyto datové objekty jsou přidruženy k objektu [Datastore,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) který zapouzdřuje jejich konfiguraci úložiště. Základní `PipelineStep` třída je vždy `name` vytvořena pomocí řetězce, seznamu `inputs`a `outputs`seznamu . Obvykle má také seznam `arguments` a často bude mít `resource_inputs`seznam . Podtřídy budou mít obecně také další `PythonScriptStep` argumenty (například vyžaduje spuštění názvu souboru a cesty skriptu). 

Graf spuštění je acyklický, ale kanály lze spustit podle opakovaného plánu a lze spustit skripty Pythonu, které mohou zapisovat informace o stavu do systému souborů, což umožňuje vytvářet složité profily. Pokud navrhnete kanál tak, aby určité kroky mohou běžet paralelně nebo asynchronně, Azure Machine Learning transparentně zpracovává analýzu závislostí a koordinaci fan-out a fan-in. Obecně se nemusíte zabývat podrobnostmi grafu spuštění, ale je k dispozici prostřednictvím atributu [Pipeline.graph.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) 


### <a name="a-simple-python-pipeline"></a>Jednoduchý kanál Pythonu

Tento úryvek zobrazuje objekty a volání potřebná k vytvoření a spuštění základního `Pipeline`:

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

Úryvek začíná běžnými objekty Azure `Workspace`Machine `Datastore`Learning, a , `Experiment`a , [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)a . Potom kód vytvoří objekty, `input_data` `output_data`které mají být uchovány a . Pole `steps` obsahuje jeden prvek, `PythonScriptStep` a který bude používat datové `compute_target`objekty a spustit na . Potom kód konkretizovat `Pipeline` samotný objekt, předávání v pracovním prostoru a kroky pole. Volání zahájí `experiment.submit(pipeline)` spuštění kanálu Azure ML. Volání `wait_for_completion()` bloky, dokud není dokončena potrubí. 

Další informace o připojení kanálu k datům najdete v článcích [Jak získat přístup k datům](how-to-access-data.md) a Jak [zaregistrovat datové sady](how-to-create-register-datasets.md). 

## <a name="best-practices-when-using-pipelines"></a>Doporučené postupy při používání kanálů

Jak můžete vidět, vytvoření kanálu Azure ML je trochu složitější než spuštění skriptu. Kanály vyžadují několik objektů Pythonu nakonfigurovat a vytvořit. 

Některé situace, které naznačují použití kanálu:

* V týmovém prostředí: rozdělte úlohy ML do několika nezávislých kroků, aby vývojáři mohli pracovat a vyvíjet své programy nezávisle. 

* Při nasazení nebo v jeho blízkosti: přiznejte konfiguraci a použijte naplánované operace řízené událostmi, abyste měli přehled o měnících se datech.

* V raných fázích projektu ML nebo samostatně pracovat: pomocí kanálů automatizovat sestavení. Pokud jste se začali starat o opětovné vytvoření konfigurace a výpočetního stavu před implementací nového nápadu, je to signál, který můžete zvážit použití kanálu k automatizaci pracovního postupu. 

Je snadné se stát nadšeným z opakovaného použití výsledků uložených v mezipaměti, jemně odstupňované kontroly nad výpočetními náklady a izolace procesů, ale kanály mají náklady. Některé anti-vzory patří:

* Použití potrubí jako jediný prostředek k oddělení obav. Vestavěné funkce, objekty a moduly Pythonu jdou dlouhou cestu, aby se zabránilo matoucímu programovému stavu! Krok kanálu je mnohem dražší než volání funkce.

* Těžká spojka mezi potrubními kroky. Pokud refaktoring závislého kroku často vyžaduje úpravu výstupů předchozího kroku, je pravděpodobné, že samostatné kroky jsou aktuálně spíše náklady než výhodou. Další vodítko, že kroky jsou příliš spojeny, je argumenty kroku, které nejsou daty, ale příznaky pro řízení zpracování. 

* Předčasná optimalizace výpočetních prostředků. Například, tam jsou často několik fází přípravy dat a jeden může často vidět `MpiStep` "Oh, tady je místo, kde bych `PythonScriptStep` mohl použít pro paralelní programování, ale tady je místo, kde bych mohl použít s méně-výkonný výpočetní cíl," a tak dále. A možná, v dlouhodobém horizontu, vytváření jemnozrnné kroky, jako že by se mohlo ukázat jako užitečné, zvláště pokud existuje možnost používat výsledky uložené v mezipaměti, spíše než vždy přepočítávání. Ale kanály nejsou určeny jako náhrada `multiprocessing` za nativní modul Pythonu. 

Dokud projekt získá velké nebo se blíží nasazení, vaše kanály by měly být hrubší než jemně odstupňované. Pokud si myslíte, že projekt ML zahrnuje _fáze_ a kanál jako poskytující kompletní pracovní postup, který vás přesune přes určitou fázi, jste na správné cestě. 

## <a name="key-advantages"></a>Klíčové výhody

Hlavní výhody použití kanálů pro vaše pracovní postupy strojového učení jsou:

|Klíčová výhoda|Popis|
|:-------:|-----------|
|**Bezobslužné běhy&nbsp;**|Naplánujte kroky, které mají být spuštěny paralelně nebo postupně spolehlivým a bezobslužným způsobem. Příprava a modelování dat může trvat dny nebo týdny a kanály umožňují zaměřit se na jiné úkoly, zatímco proces běží. |
|**Heterogenní výpočetní výkon**|Používejte více kanálů, které jsou spolehlivě koordinovány napříč heterogenními a škálovatelnými výpočetními prostředky a umístěními úložiště. Efektivně využijte dostupné výpočetní prostředky spuštěním jednotlivých kroků kanálu na různých výpočetních cílech, jako jsou HDInsight, virtuální počítače gpu data science a datové cihly.|
|**Opětovná použitelnost**|Vytvořte šablony kanálu pro konkrétní scénáře, jako je například rekvalifikace a dávkové vyhodnocování. Spouštějte publikované kanály z externích systémů prostřednictvím jednoduchých volání REST.|
|**Sledování a správa verzí**|Místo ručního sledování dat a cest výsledků při itetování použijte sady SDK kanálů k explicitnímu pojmenování a verzi zdrojů dat, vstupů a výstupů. Skripty a data můžete také spravovat samostatně, abyste zvýšili produktivitu.|
| **Modularita** | Oddělení oblastí obav a izolace změn umožňuje rychlejšívývoj softwaru s vyšší kvalitou. | 
|**Spolupráce**|Kanály umožňují datovým vědcům spolupracovat ve všech oblastech procesu návrhu strojového učení a zároveň mohou souběžně pracovat na krocích kanálu.|

### <a name="choosing-the-proper-pipelinestep-subclass"></a>Výběr správné podtřídy PipelineStep

Je `PythonScriptStep` nejflexibilnější podtřída `PipelineStep`abstraktní . Jiné podtřídy, `EstimatorStep` například `DataTransferStep` podtřídy a mohou provádět určité úkoly s menším kódem. Například `EstimatorStep` lze vytvořit jednoduše předáním názvu `Estimator`kroku, a výpočetní cíl. Nebo můžete přepsat vstupy a výstupy, parametry kanálu a argumenty. Další informace najdete v tématu [Trénování modelů s Azure Machine Learning pomocí odhadu](how-to-train-ml-models.md). 

Usnadňuje `DataTransferStep` přesun dat mezi zdroji dat a jímky. Kód k tomu ručně je jednoduché, ale opakující se. Místo toho můžete vytvořit `DataTransferStep` pouze s názvem, odkazy na zdroj dat a jímky dat a výpočetní cíl. Kanál strojového učení Azure v poznámkovém bloku [s datemTransferStep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-data-transfer.ipynb) ukazuje tuto flexibilitu.

## <a name="modules"></a>Moduly

Zatímco kroky kanálu umožňují opakované použití výsledků předchozího spuštění, v mnoha případech konstrukce kroku předpokládá, že požadované skripty a závislé soubory musí být místně k dispozici. Pokud datový vědec chce stavět na existující kód, skripty a závislosti často musí být klonovány ze samostatného úložiště.

Moduly jsou podobné v použití kroků kanálu, ale poskytují správu verzí usnadněnou prostřednictvím pracovního prostoru, který umožňuje spolupráci a opětovnou použitelnost ve velkém měřítku. Moduly jsou navrženy pro opakované použití ve více kanálech a mohou se vyvíjet tak, aby přizpůsobily konkrétní výpočty různým případům použití. Uživatelé mohou prostřednictvím pracovního prostoru dělat následující úkoly bez použití externích úložišť:

* Vytváření nových modulů a publikování nových verzí stávajících modulů
* Zavržení existujících verzí
* Označení verzí jako zakázaných, aby spotřebitelé tuto verzi nepoužívali.
* Určení výchozích verzí
* Načtení modulů podle verze z pracovního prostoru, aby bylo zajištěno, že týmy používají stejný kód

Najdete [v poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb) příklady kódu o tom, jak vytvořit, připojit a používat moduly v kanálech Azure Machine Learning.

## <a name="next-steps"></a>Další kroky

Kanály Azure ML jsou výkonné zařízení, které začíná dodávat hodnotu v raných fázích vývoje. Hodnota se zvyšuje s růstem týmu a projektu. Tento článek vysvětluje, jak jsou kanály určené pomocí Azure Machine Learning Python SDK a orchestrované v Azure. Viděli jste některé základní zdrojový kód a byl zaveden `PipelineStep` do několika tříd, které jsou k dispozici. Měli byste mít představu o tom, kdy používat kanály Azure ML a jak je Azure spouští. 


+ Přečtěte si, jak [vytvořit první kanál](how-to-create-your-first-pipeline.md).

+ Zjistěte, jak [spustit predikce dávek na velkých datech](tutorial-pipeline-batch-scoring-classification.md ).

+ Kroky jádra [kanálu](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) a kroků kanálu naleznete v referenčních dokumentech sady [SDK.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)

+ Vyzkoušejte například poznámkové bloky Jupyter, které zobrazují [kanály Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Přečtěte si, jak [spustit poznámkové bloky a prozkoumat tuto službu](samples-notebooks.md).
