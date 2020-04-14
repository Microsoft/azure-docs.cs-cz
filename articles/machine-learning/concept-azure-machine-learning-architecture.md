---
title: Architektura & klíčové pojmy
titleSuffix: Azure Machine Learning
description: Seznamte se s architekturou, termíny, koncepty a pracovními postupy, které tvoří Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/17/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: 9f1d23f11cf73680a8861c9f1ac6cbd40ad497a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257330"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Jak Azure Machine Learning funguje: Architektura a koncepty

Seznamte se s architekturou, koncepty a pracovními postupy pro Azure Machine Learning. Hlavní součásti služby a obecný pracovní postup pro používání služby jsou uvedeny v následujícím diagramu:

![Architektura a pracovní postup Azure Machine Learning](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Pracovní postup

Pracovní postup modelu strojového učení obecně následuje podle této sekvenci:

1. **Trénování**
    + Vývoj školicích skriptů strojového učení v **Pythonu**, **R**nebo s vizuálním designérem.
    + Vytvořte a nakonfigurujte **výpočetní cíl**.
    + **Odešlete skripty** na konfigurovaný výpočetní cíl, který se spustí v tomto prostředí. Během trénování mohou skripty číst nebo zapisovat do **datových úložišť**. Protokoly a výstupy vytvořené během školení jsou uloženy jako **schované** v **pracovním prostoru** a seskupeny podle **experimentů**.

1. **Balíček** - Po nalezení uspokojivého spuštění zaregistrujte trvalý model v **registru modelu**.

1. **Ověřte** - **dotaz experiment** pro protokolované metriky z aktuálního a minulého spuštění. Pokud metriky neoznačují požadovaný výsledek, smyčku zpět ke kroku 1 a iterate na skripty.

1. **Nasazení** – vývoj vyhodnocovacískript, který používá model a **nasadit model** jako **webovou službu** v Azure nebo na **zařízení IoT Edge**.

1. **Monitor** – monitorování **posunu dat** mezi trénovací datovou sadou a odvozená data nasazeného modelu. V případě potřeby smyčku zpět ke kroku 1 přeškolit model s novými trénovacími daty.

## <a name="tools-for-azure-machine-learning"></a>Nástroje pro Azure Machine Learning

Pro Azure Machine Learning použijte tyto nástroje:

+  Pracujte se službou v libovolném prostředí Pythonu pomocí [sady Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Interakci se službou v jakémkoli prostředí R s [Azure Machine Learning SDK pro R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Automatizujte své aktivity strojového učení pomocí [cli Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli).
+ Pomocí [návrháře Azure Machine Learning (preview)](concept-designer.md) můžete provést kroky pracovního postupu bez psaní kódu.


> [!NOTE]
> I když tento článek definuje termíny a koncepty používané Azure Machine Learning, nedefinuje termíny a koncepty pro platformu Azure. Další informace o terminologii platformy Azure najdete v [glosáři Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Slovníček

* [Činnosti](#activities)
* [Pracovní prostor](#workspaces)
    * [Experimenty](#experiments)
        * [Spustit](#runs) 
            * [Spustit konfiguraci](#run-configurations)
            * [Snímek](#snapshots)
            * [Git sledování](#github-tracking-and-integration)
            * [Protokolování](#logging)
    * [Kanály ML](#ml-pipelines)
    * [Modely](#models)
        * [Prostředí](#environments)
        * [Školicí skript](#training-scripts)
        * [Odhady](#estimators)
    * [Koncové body](#endpoints)
        * [Webová služba](#web-service-endpoint)
        * [IoT moduly](#iot-module-endpoints)
    * [Datová sada & úložišť dat](#datasets-and-datastores)
    * [Cílové výpočetní objekty](#compute-targets)

### <a name="activities"></a>Aktivity

Aktivita představuje dlouho běžící operace. Následující operace jsou příklady aktivit:

* Vytvoření nebo odstranění výpočetního cíle
* Spuštění skriptu na výpočetní matný cíl

Aktivity můžete poskytovat oznámení prostřednictvím sady SDK nebo webové uživatelské ho uživatelského nastavení, takže můžete snadno sledovat průběh těchto operací.

### <a name="workspaces"></a>Pracovní prostory

[Pracovní prostor](concept-workspace.md) je prostředek nejvyšší úrovně pro Azure Machine Learning. Poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte při použití Azure Machine Learning. Pracovní prostor můžete sdílet s ostatními. Podrobný popis pracovních prostorů najdete [v tématu Co je pracovní prostor Azure Machine Learning?](concept-workspace.md).

### <a name="experiments"></a>Experimenty

Experiment je seskupení mnoha spuštění ze zadaného skriptu. Vždy patří do pracovního prostoru. Při odeslání spuštění zadáte název experimentu. Informace pro spuštění jsou uloženy v rámci tohoto experimentu. Pokud odešlete spustit a zadáte název experimentu, který neexistuje, automaticky se vytvoří nový experiment s nově zadaným názvem.

Příklad použití experimentu najdete v [tématu Výuka: Trénování prvního modelu](tutorial-1st-experiment-sdk-train.md).

### <a name="runs"></a>Běží

Spuštění je jedno spuštění školicího skriptu. Experiment bude obvykle obsahovat více spuštění.

Azure Machine Learning zaznamenává všechny spuštění a ukládá následující informace v experimentu:

* Metadata o spuštění (časové razítko, doba trvání a tak dále)
* Metriky protokolované skriptem
* Výstupní soubory, které jsou automaticky shromážděny experimentem nebo explicitně nahrané vámi
* Snímek adresáře, který obsahuje skripty, před spuštěním

Spuštění při odeslání skriptu trénovat model. Spuštění může mít nula nebo více podřízených spuštění. Například spuštění nejvyšší úrovně může mít dva podřízené spuštění, z nichž každý může mít své vlastní podřízené spuštění.

### <a name="run-configurations"></a>Spustit konfigurace

Konfigurace spuštění je sada pokynů, která definuje, jak by měl být skript spuštěn v zadaném výpočetním cíli. Konfigurace obsahuje širokou sadu definic chování, například zda použít existující prostředí Pythonu nebo použít prostředí Conda, které je sestaveno ze specifikace.

Konfigurace spuštění může být zachována do souboru uvnitř adresáře, který obsahuje trénovací skript, nebo může být vytvořena jako objekt v paměti a použita k odeslání spuštění.

Například spustit konfigurace, najdete v [tématu Výběr a použití výpočetní cíl trénovat váš model](how-to-set-up-training-targets.md).

### <a name="snapshots"></a>Snímky

Při odeslání spustit Azure Machine Learning komprimuje adresář, který obsahuje skript jako zip soubor a odešle jej do výpočetního cíle. Soubor zip je pak extrahován a skript je spuštěn tam. Azure Machine Learning také ukládá soubor zip jako snímek jako součást záznamu spuštění. Kdokoli s přístupem k pracovnímu prostoru může procházet spustit záznam a stáhnout snímek.

> [!NOTE]
> Chcete-li zabránit zahrnutí nepotřebných souborů do snímku, vytvořte soubor s ignorátorem (.gitignore nebo .amlignore). Umístěte tento soubor do adresáře Snímek a přidejte názvy souborů, které chcete ignorovat. Soubor Amlignore používá stejnou [syntaxi a vzorky jako soubor .gitignore](https://git-scm.com/docs/gitignore). Pokud oba soubory existují, má přednost soubor Amlignore.

### <a name="github-tracking-and-integration"></a>GitHub sledování a integrace

Když spustíte trénovací běh, kde zdrojový adresář je místní úložiště Git, informace o úložišti jsou uloženy v historii spuštění. To funguje s spuštění odeslanými pomocí odhadu, kanálu ML nebo spuštění skriptu. Funguje také pro spuštění odeslaná z cli sady SDK nebo Machine Learning.

Další informace najdete v [tématu Integrace Gitu pro Azure Machine Learning](concept-train-model-git-integration.md).

### <a name="logging"></a>Protokolování

Při vývoji řešení použijte Azure Machine Learning Python SDK ve skriptu Pythonu k protokolování libovolných metrik. Po spuštění dotaz metriky k určení, zda spuštění vytvořil model, který chcete nasadit.

### <a name="ml-pipelines"></a>Kanály ML

Kanály strojového učení se používají k vytváření a správě pracovních postupů, které spojují fáze strojového učení. Kanál může například zahrnovat přípravu dat, trénování modelu, nasazení modelu a fáze odvození/vyhodnocování. Každá fáze může zahrnovat více kroků, z nichž každý může běžet bez obslužné v různých výpočetních cílů. 

Kroky kanálu jsou opakovaně použitelné a lze spustit bez opětovného spuštění předchozíkroky, pokud se nezměnil výstup těchto kroků. Můžete například přeškolit model bez opětovného spuštění nákladných kroků přípravy dat, pokud se data nezměnila. Kanály také umožňují datovým vědcům spolupracovat při práci na samostatných oblastech pracovního postupu strojového učení.

Další informace o kanálech strojového učení s touto službou najdete v [tématu Pipelines and Azure Machine Learning](concept-ml-pipelines.md).

### <a name="models"></a>Modely

V nejjednodušším případě je model část kódu, která přebírá vstup a vytváří výstup. Vytvoření modelu strojového učení zahrnuje výběr algoritmu, poskytování dat a ladění hyperparametrů. Školení je iterativní proces, který vytváří trénovaný model, který zapouzdřuje, co se model naučil během procesu školení.

Model se vyrábí spuštění v Azure Machine Learning. Můžete taky použít model, který je trénovaný mimo Azure Machine Learning. Model můžete zaregistrovat v pracovním prostoru Azure Machine Learning.

Azure Machine Learning je framework agnostik. Při vytváření modelu můžete použít libovolný populární rámec strojového učení, například Scikit-learn, XGBoost, PyTorch, TensorFlow a Chainer.

Příklad školení modelu pomocí Scikit-learn a odhad, najdete [v tématu Kurz: Trénování modelu klasifikace obrázků s Azure Machine Learning](tutorial-train-models-with-aml.md).

**Registr modelů** sleduje všechny modely v pracovním prostoru Azure Machine Learning.

Modely jsou označeny názvem a verzí. Pokaždé, když zaregistrujete model se stejným názvem jako existující, registr předpokládá, že se jedná o novou verzi. Verze se zvětšuje a nový model je registrován pod stejným názvem.

Při registraci modelu můžete zadat další značky metadat a pak je použít při hledání modelů.

> [!TIP]
> Registrovaný model je logický kontejner pro jeden nebo více souborů, které tvoří model. Například pokud máte model, který je uložen ý ve více souborech, můžete je zaregistrovat jako jeden model v pracovním prostoru Azure Machine Learning. Po registraci pak můžete stáhnout nebo nasadit registrovaný model a přijímat všechny soubory, které byly zaregistrovány.

Registrovaný model, který používá aktivní nasazení, nelze odstranit.

Příklad registrace modelu najdete v [tématu Trénování modelu klasifikace obrázků pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="environments"></a>Prostředí

Prostředí Azure ML se používají k určení konfigurace (Docker / Python / Spark / atd.) slouží k vytvoření reprodukovatelné prostředí pro přípravu dat, model školení a model slouží. Jsou to spravované entity s verzí v rámci pracovního prostoru Azure Machine Learning, které umožňují reprodukovatelné, auditovatelné a přenosné pracovní postupy strojového učení napříč různými výpočetními cíli.

Objekt prostředí na místní výpočetní prostředky můžete použít k vývoji trénovacího skriptu, opětovnému použití stejného prostředí v Azure Machine Learning Compute pro školení modelu ve velkém měřítku a dokonce i nasazení modelu se stejným prostředím. 

Naučte [se vytvářet a spravovat opakovaně použitelné prostředí ML](how-to-use-environments.md) pro školení a odvození.

### <a name="training-scripts"></a>Školicí skripty

Chcete-li trénovat model, zadejte adresář, který obsahuje školicí skript a přidružené soubory. Zadáte také název experimentu, který se používá k ukládání informací shromážděných během školení. Během školení je celý adresář zkopírován do tréninkového prostředí (cíl výpočetní ho disponia) a spustí se skript určený konfigurací spuštění. Snímek adresáře je také uložen v rámci experimentu v pracovním prostoru.

Příklad najdete v [tématu Kurz: Trénování modelu klasifikace obrázků pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="estimators"></a>Odhady

Chcete-li usnadnit model školení s oblíbenými rámci, třída odhadu umožňuje snadno vytvářet konfigurace spuštění. Můžete vytvořit a použít obecný [Odhad](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) k odeslání školicích skriptů, které používají libovolný rámec učení, který zvolíte (například scikit-learn).

Pro úlohy PyTorch, TensorFlow a Chainer poskytuje Azure Machine Learning také příslušné odhady [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)a [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) pro zjednodušení používání těchto architektur.

Další informace najdete v těchto článcích:

* [Trénovat modely ML s odhady](how-to-train-ml-models.md).
* [Trénujte modely hlubokého učení Pytorch ve velkém měřítku pomocí Azure Machine Learning](how-to-train-pytorch.md).
* [Trénování a registrace modelů TensorFlow ve velkém měřítku pomocí Azure Machine Learning](how-to-train-tensorflow.md).
* [Trénování a registrace chainermodely ve velkém měřítku s Azure Machine Learning](how-to-train-ml-models.md).

### <a name="endpoints"></a>Koncové body

Koncový bod je vytvoření instance vašeho modelu do webové služby, která může být hostovaná v cloudu, nebo modulu IoT pro nasazení integrovaných zařízení.

#### <a name="web-service-endpoint"></a>Koncový bod webové služby

Při nasazování modelu jako webové služby lze koncový bod nasadit na instance kontejnerů Azure, služby Azure Kubernetes nebo FPGA. Službu vytvoříte z modelu, skriptu a přidružených souborů. Ty jsou umístěny do image základní kontejner, který obsahuje prostředí spuštění pro model. Obrázek má vyrovnávání zatížení, http koncový bod, který přijímá požadavky na vyhodnocování, které jsou odesílány do webové služby.

Azure vám pomůže sledovat vaši webovou službu shromažďováním telemetrie Application Insights nebo telemetrie modelu, pokud jste se rozhodli tuto funkci povolit. Telemetrická data jsou přístupná jenom pro vás a jsou uložena v instancích účtů aplikací a účtu úložiště.

Pokud jste povolili automatické škálování, Azure automaticky škáluje vaše nasazení.

Příklad nasazení modelu jako webové služby najdete v [tématu Nasazení modelu klasifikace bitových obrázků v instanci kontejneru Azure](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>Koncové body modulu IoT

Nasaditý koncový bod modulu IoT je kontejner Dockeru, který obsahuje váš model a přidružený skript nebo aplikaci a všechny další závislosti. Tyto moduly nasadíte pomocí Azure IoT Edge na hraničních zařízeních.

Pokud jste povolili monitorování, Azure shromažďuje telemetrická data z modelu uvnitř modulu Azure IoT Edge. Telemetrická data jsou přístupná jenom pro vás a jsou uložena v instanci účtu úložiště.

Azure IoT Edge zajišťuje, že váš modul běží a monitoruje zařízení, které je hostitelem.


### <a name="compute-instance-preview"></a><a name="compute-instance"></a>Výpočetní instance (náhled)

**Výpočetní instance Azure Machine Learning** (dříve notebook v počítači) je plně spravovaná cloudová pracovní stanice, která zahrnuje více nástrojů a prostředí nainstalovaných pro strojové učení. Výpočetní instance se dá použít jako výpočetní cíl pro úlohy školení a odvození. Pro velké úkoly výpočetní [clustery Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) s možnostmi škálování s více uzlovými oblastmi jsou lepší volbou výpočetního cíle.

Přečtěte si další informace o [výpočetních instancích](concept-compute-instance.md).

### <a name="datasets-and-datastores"></a>Datové sady a úložiště dat

**Datové sady (preview) Azure Machine Learning** usnadňují přístup k vašim datům a práci s ním. Datové sady spravují data v různých scénářích, jako je například trénování modelu a vytváření kanálu. Pomocí sady Azure Machine Learning SDK můžete přistupovat k podkladovému úložišti, zkoumat data a spravovat životní cyklus různých definic datových sad.

Datové sady poskytují metody pro práci s daty `from_delimited_files()` `to_pandas_dataframe()`v oblíbených formátech, například pomocí nebo .

Další informace najdete v [tématu Vytvoření a registrace datových sad Azure Machine Learning](how-to-create-register-datasets.md).  Další příklady pomocí datových sad naleznete v [ukázkových poznámkových blocích](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets).

**Úložiště dat** je abstrakce úložiště přes účet úložiště Azure. Úložiště dat můžete použít kontejner objektů blob Azure nebo sdílené složky Azure jako úložiště back-end. Každý pracovní prostor má výchozí úložiště dat a můžete zaregistrovat další úložiště dat. Pomocí rozhraní API Sady Pythonsdka nebo rozhraní PŘÍKAZOVÉHO PŘÍKAZU Azure Machine Learning můžete ukládat a načítat soubory z úložiště dat.

### <a name="compute-targets"></a>Cílové výpočetní objekty

Cíl [výpočetních prostředků](concept-compute-target.md) umožňuje určit výpočetní prostředek, ve kterém spustíte školicí skript nebo hostujete nasazení služby. Toto umístění může být místní počítač nebo cloudový výpočetní prostředek.

Přečtěte si další informace o [dostupných výpočetních cílech pro školení a nasazení](concept-compute-target.md).

### <a name="next-steps"></a>Další kroky

Pokud chcete začít s Azure Machine Learning, přečtěte si hledět:

* [Co je Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md)
* [Výuka (část 1): Vycvičit model](tutorial-train-models-with-aml.md)
