---
title: Klíčové koncepty & architektury
titleSuffix: Azure Machine Learning
description: Přečtěte si o architektuře, pojmech, konceptech a pracovních postupech, které tvoří Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: 8e1f144fdf94df445fb64b9b489cba1e9998f381
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845614"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Jak Azure Machine Learning funguje: architektura a koncepty

Přečtěte si o architektuře, konceptech a pracovním postupu pro Azure Machine Learning. Hlavní součásti služby a obecný pracovní postup pro používání služby jsou uvedeny v následujícím diagramu:

![Azure Machine Learning architektura a pracovní postup](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Pracovní postupy

Pracovní postup modelu Machine Learning se obvykle řídí tímto pořadím:

1. **Průřez**
    + Vývoj školicích skriptů pro strojové učení v **Pythonu** nebo pomocí vizuálního návrháře.
    + Vytvoření a konfigurace **cílové výpočetní prostředí**.
    + **Odeslat skripty** do cílového výpočetního nakonfigurované prostředí pro spuštění v daném prostředí. Během školení můžou skripty číst nebo zapisovat do **úložiště dat**. A záznamy o spuštění jsou uloženy jako **běhy** v **pracovním prostoru** a seskupeny pod **experimenty**.

1. **Balíček** – po nalezení uspokojivého spuštění Zaregistrujte trvalý model v **registru modelu**.

1. **Ověřte** - **dotazování experimentu** u protokolovaných metrik z aktuálních a minulých spuštění. Pokud metriky nenaznačují požadovaný výsledek, vraťte se ke kroku 1 a Iterujte na svých skriptech.

1. **Nasazení** – vývoj vyhodnocovacího skriptu, který používá model a **nasazení modelu** jako **webové služby** v Azure nebo do **zařízení IoT Edge**.

1. **Monitor** – monitorování pro **Posun dat** mezi školicí datovou sadou a odvozenými daty nasazeného modelu. V případě potřeby se vraťte ke kroku 1 a přeškolujte model s novými školicími daty.

## <a name="tools-for-azure-machine-learning"></a>Nástroje pro Azure Machine Learning

Použijte tyto nástroje pro Azure Machine Learning:

+  Spolupracovat se službou v jakémkoli prostředí Pythonu s [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Interakci se službou v jakémkoli prostředí R s [Azure Machine Learning SDK pro R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Automatizujte své aktivity strojového učení pomocí [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli).
+ Použijte [Azure Machine Learning Designer (Preview)](concept-designer.md) k provedení kroků pracovního postupu bez psaní kódu.


> [!NOTE]
> I když tento článek popisuje pojmy a koncepty, které používá Azure Machine Learning, nedefinuje pojmy a koncepty pro platformu Azure. Další informace o terminologii platforem Azure najdete v tématu [Microsoft Azure Glosář](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Glosář
+ <a href="#activities">Aktivita</a>
+ <a href="#compute-targets">Cíle výpočtů</a>
+ <a href="#datasets-and-datastores">Datová sada & úložiště dat</a>
+ <a href="#endpoints">Bod</a>
+ <a href="#environments">Environment</a>
+ [Odhady](#estimators)
+ <a href="#experiments">Experimenty</a>
+ <a href="#github-tracking-and-integration">Sledování Gitu</a>
+ <a href="#iot-module-endpoints">Moduly IoT</a>
+ <a href="#logging">Protokolování</a>
+ <a href="#ml-pipelines">Kanály ML</a>
+ <a href="#models">Vzor</a>
+ <a href="#runs">Spuštění</a>
+ <a href="#run-configurations">Konfigurace spuštění</a>
+ <a href="#snapshots">Snímek</a>
+ <a href="#training-scripts">Školicí skript</a>
+ <a href="#web-service-endpoint">Webové služby</a>
+ <a href="#workspaces">Stejných</a>

### <a name="activities"></a>Aktivity

Aktivita představuje dlouhotrvající operace. Příklady aktivit jsou následující operace:

* Vytvoření nebo odstranění cílové výpočetní prostředí
* Spuštění skriptu na cílové výpočetní prostředí

Aktivity můžou poskytovat oznámení prostřednictvím sady SDK nebo webového uživatelského rozhraní, abyste mohli snadno monitorovat průběh těchto operací.

### <a name="compute-instance"></a>Instance COMPUTE (Preview)

> [!NOTE]
> Výpočetní instance jsou dostupné jenom pro pracovní prostory s **střed USA – sever**, * * východní USA 2 * *, **Severní Evropa** nebo **Velká Británie – jih**.
>Pokud je váš pracovní prostor v jakékoli jiné oblasti, můžete místo toho vytvořit a použít [virtuální počítač s poznámkovým blokem](concept-compute-instance.md#notebookvm) . 

**Instance služby compute Azure Machine Learning** (dříve virtuální počítač poznámkového bloku) je plně spravovaná cloudová pracovní stanice, která zahrnuje několik nástrojů a prostředí nainstalovaných pro strojové učení. Výpočetní instance se dají použít jako cíl výpočtů pro školení a Inferencing úlohy. V případě rozsáhlých úloh [Azure Machine Learning výpočetní clustery](how-to-set-up-training-targets.md#amlcompute) s možnostmi škálování s více uzly lepší volbou cíle pro výpočty.

Přečtěte si další informace o [výpočetních instancích](concept-compute-instance.md).

### <a name="compute-targets"></a>Cílové výpočetní objekty

[Cílový výpočetní](concept-compute-target.md) výkon vám umožní určit výpočetní prostředek, ve kterém spustíte školicí skript, nebo hostovat nasazení služby. Toto umístění může být váš místní počítač nebo cloudový výpočetní prostředek.

Přečtěte si další informace o [dostupných výpočetních cílech pro školení a nasazení](concept-compute-target.md).

### <a name="datasets-and-datastores"></a>Datové sady a úložiště dat

**Azure Machine Learning datové sady** (Preview) usnadňují přístup k datům a práci s nimi. Datové sady spravují data v různých scénářích, jako jsou například školení modelů a vytváření kanálů. Pomocí sady Azure Machine Learning SDK můžete získat přístup k základnímu úložišti, prozkoumat data a spravovat životní cyklus různých definic datových sad.

Datové sady poskytují metody pro práci s daty v oblíbených formátech, jako je například použití `from_delimited_files()` nebo `to_pandas_dataframe()`.

Další informace najdete v tématu [Vytvoření a registrace Azure Machine Learning datových sad](how-to-create-register-datasets.md).  Další příklady použití datových sad najdete v [ukázkových poznámkových blocích](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets).

**Úložiště dat** je abstrakce úložiště v rámci účtu úložiště Azure. Úložiště dat může jako back-end úložiště použít buď kontejner Azure Blob, nebo sdílenou složku Azure. Každý pracovní prostor má výchozí úložiště dat a můžete zaregistrovat další úložiště dat. K ukládání a načítání souborů z úložiště dat použijte rozhraní Python SDK API nebo Azure Machine Learning CLI.

### <a name="endpoints"></a>Koncové body

Koncový bod je instance vašeho modelu do webové služby, kterou je možné hostovat v cloudu nebo v modulu IoT pro nasazení integrovaných zařízení.

#### <a name="web-service-endpoint"></a>Koncový bod webové služby

Při nasazení modelu jako webové služby je možné koncový bod nasadit v Azure Container Instances, službě Azure Kubernetes nebo FPGA. Službu vytvoříte z modelu, skriptu a přidružených souborů. Jsou umístěny do základní image kontejneru, která obsahuje spouštěcí prostředí pro model. Image má koncový bod HTTP s vyrovnáváním zatížení, který přijímá požadavky na bodování, které se odesílají do webové služby.

Azure vám pomůže monitorovat webovou službu shromažďováním Application Insights telemetrie nebo telemetrie modelů, pokud jste se rozhodli tuto funkci povolit. Data telemetrie jsou dostupná jenom pro vás a ukládají se do vašich Application Insights a instancí účtů úložiště.

Pokud jste povolili automatické škálování, Azure automaticky škáluje vaše nasazení.

Příklad nasazení modelu jako webové služby najdete [v tématu nasazení modelu klasifikace imagí v Azure Container Instances](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>Koncové body modulu IoT

Nasazený koncový bod modulu IoT je kontejner Docker, který obsahuje váš model a přidružený skript nebo aplikaci a všechny další závislosti. Tyto moduly nasadíte pomocí Azure IoT Edge na hraničních zařízeních.

Pokud jste povolili monitorování, Azure shromáždí data telemetrie z modelu uvnitř modulu Azure IoT Edge. Data telemetrie jsou dostupná jenom pro vás a ukládají se do vaší instance účtu úložiště.

Azure IoT Edge zajistí, že je váš modul spuštěný, a monitoruje zařízení, které ho hostuje.

### <a name="environments"></a>Prostředí

Prostředí Azure ML se používají k určení konfigurace (Docker/Python/Spark/atd.), která slouží k vytvoření reprodukovatelného prostředí pro přípravu dat, školení modelů a obsluhu modelů. Jsou spravované a se správou verzí v rámci vašeho pracovního prostoru Azure Machine Learning umožňují reprodukovatelné pracovní postupy, které lze auditovat a přenosné strojové učení napříč různými výpočetními cíli.

K vývoji školicího skriptu můžete použít objekt prostředí v místním výpočetním prostředí, použít stejné prostředí v Azure Machine Learning výpočetním prostředí pro modelově škálovatelné školení a dokonce model nasadit do stejného prostředí. 

Naučte [se vytvářet a spravovat opakovaně použitelné prostředí ml](how-to-use-environments.md) pro školení a odvozování.

### <a name="estimators"></a>Odhady

Pro usnadnění školení modelů s oblíbenými rozhraními vám třída Estimator umožňuje snadno sestavit konfigurace spuštění. Můžete vytvořit a použít obecné [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) k odesílání školicích skriptů, které používají všechny vámi zvolené vzdělávací architektury (například scikit-učení).

Pro úlohy PyTorch, TensorFlow a řetězení Azure Machine Learning poskytuje také příslušné [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)a [Chain](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) odhady pro zjednodušení používání těchto rozhraní.

Další informace najdete v těchto článcích:

* [Modely vlakových ml pomocí odhady](how-to-train-ml-models.md).
* [Pytorch se škálují modely hloubkového učení s](how-to-train-pytorch.md)využitím Azure Machine Learning.
* [TensorFlow modely a zaregistrujte se ve velkém měřítku pomocí Azure Machine Learning](how-to-train-tensorflow.md).
* [Škálujte a Registrujte modely zřetězení ve velkém měřítku pomocí Azure Machine Learning](how-to-train-chainer.md).

### <a name="experiments"></a>Experimenty

Experiment je seskupení mnoha běhů ze zadaného skriptu. Vždy patří do pracovního prostoru. Když odešlete spustit, zadejte název experimentu. Informace pro spuštění jsou uloženy v rámci testu. Pokud odešlete běh a určíte název experimentu, který neexistuje, automaticky se vytvoří nový experiment s tímto nově zadaným názvem.

Příklad použití experimentu najdete v tématu [kurz: výuka prvního modelu](tutorial-1st-experiment-sdk-train.md).


### <a name="github-tracking-and-integration"></a>Sledování a integrace GitHubu

Když spustíte školicí kurz, kde zdrojový adresář je místní úložiště Git, informace o úložišti se ukládají v historii spuštění. Tato funkce funguje s poslanými běhy s použitím kanálu Estimator, ML nebo spuštění skriptu. Funguje taky pro spuštění odeslaná ze sady SDK nebo rozhraní příkazového řádku Machine Learning.

Další informace najdete v tématu [integrace Gitu pro Azure Machine Learning](concept-train-model-git-integration.md).

### <a name="logging"></a>Protokolování

Při vývoji řešení použijte sadu Azure Machine Learning Python SDK ve vašem skriptu Pythonu k protokolování libovolných metrik. Po spuštění dotazu na metriky určete, zda běh vytvořil model, který chcete nasadit.

### <a name="ml-pipelines"></a>Kanály ML

Pomocí kanálů strojového učení můžete vytvářet a spravovat pracovní postupy, které dohromady spojí fáze strojového učení. Kanál může například zahrnovat přípravu dat, školení modelů, nasazení modelu a fáze odvození a bodování. Jednotlivé fáze může zahrnovat několik kroků, z nichž každý lze spustit bezobslužně v různých cílových výpočetních prostředí. 

Kroky kanálu jsou opakovaně použitelné a je možné je spustit bez nutnosti znovu spustit následné kroky, pokud se výstup tohoto kroku nezměnil. V případě, že se data nezměnila, můžete například přeškolit model bez nutnosti znovu spustit nákladný postup přípravy dat. Kanály také umožňují pracovníkům dat spolupracovat při práci na samostatných oblastech pracovního postupu Machine Learning.

Další informace o kanálech strojového učení s touto službou najdete v tématu [kanály a Azure Machine Learning](concept-ml-pipelines.md).

### <a name="models"></a>Modely

V nejjednodušším model je část kódu, která přijímá vstup a výstup. Vytvoření modelu strojového učení zahrnuje výběr algoritmus, poskytuje s daty a ladění hyperparameters. Školení je iterativní proces, který vytváří trénovaného modelu, který zapouzdřuje modelu osvojené během procesu trénování.

Model je produkovaný spustit ve službě Azure Machine Learning. Můžete také použít model, který je vyškolen mimo Azure Machine Learning. Model můžete zaregistrovat v pracovním prostoru Azure Machine Learning.

Azure Machine Learning je nezávislá Framework. Při vytváření modelu můžete použít jakoukoli oblíbenou architekturu strojového učení, jako je Scikit-Learning, XGBoost, PyTorch, TensorFlow a chainer.

Příklad školení modelu pomocí Scikit-učení a Estimator najdete v tématu [kurz: výuka modelu klasifikace obrázků pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).

**Registr modelu** udržuje přehled o všech modelech v pracovním prostoru Azure Machine Learning.

Modely jsou identifikovány názvem a verzí. Pokaždé, když zaregistrujete model se stejným názvem, jako má stávající, registr předpokládá, že se jedná o novou verzi. Verze se zvýší a nový model se zaregistruje pod stejným názvem.

Při registraci modelu můžete zadat další značky metadat a pak použít značky při hledání modelů.

> [!TIP]
> Registrovaný model je logický kontejner pro jeden nebo více souborů, které tvoří model. Například pokud máte model, který je uložený v několika souborech, můžete je zaregistrovat jako jeden model v pracovním prostoru Azure Machine Learning. Po registraci můžete zaregistrovaný model stáhnout nebo nasadit a získat všechny soubory, které byly zaregistrovány.

Registrovaný model, který je používán aktivním nasazením, nelze odstranit.

Příklad registrace modelu naleznete v tématu [výuka modelu klasifikace obrázku pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="runs"></a>Spuštění

Spuštění je jediné spuštění školicího skriptu. Azure Machine Learning zaznamenává všechna spuštění a ukládá následující informace:

* Metadata o běhu (časové razítko, doba trvání atd.)
* Metriky, které jsou protokolovány vaším skriptem
* Výstupní soubory, které jsou na základě experimentu shromažďovány nebo výslovně nahrány
* Snímek adresáře, který obsahuje vaše skripty před spuštění

Spuštění vytvoříte při odeslání skriptu pro výuku modelu. Spuštění může mít nula nebo více podřízených spuštění. Například spuštění na nejvyšší úrovni může mít dvě podřízená spuštění, z nichž každá může mít vlastní podřízený běh.

### <a name="run-configurations"></a>Konfigurace spuštění

Konfigurace spuštění je sada instrukcí, které definují, jak by měl skript běžet v zadaném výpočetním cíli. Tato konfigurace zahrnuje rozsáhlou sadu definic chování, například to, jestli se má použít existující prostředí Pythonu nebo jak používat conda prostředí, které je sestavené ze specifikace.

Konfiguraci spuštění lze zachovat do souboru v adresáři, který obsahuje školicí skript, nebo může být vytvořen jako objekt v paměti a použit k odeslání běhu.

Například konfigurace spuštění najdete v tématu [Výběr a použití výpočetní cíle ke školení modelu](how-to-set-up-training-targets.md).
### <a name="snapshots"></a>Snímky

Když odešlete běh, Azure Machine Learning zkomprimuje adresář, který obsahuje skript jako soubor zip, a odešle ho do cíle služby Compute. Pak se soubor zip extrahuje a v něm se spustí skript. Azure Machine Learning také ukládá soubor zip jako snímek jako součást spuštění záznamu. Každý, kdo má přístup k pracovním prostoru můžete procházet záznam spuštění a stáhnout snímek.

> [!NOTE]
> Aby nedocházelo k zahrnutí zbytečných souborů do snímku, udělejte soubor Ignore (. gitignore nebo. amlignore). Tento soubor umístěte do adresáře snímků a přidejte do něj názvy souborů, které chcete v něm ignorovat. Soubor. amlignore používá stejnou [syntaxi a vzory jako soubor. gitignore](https://git-scm.com/docs/gitignore). Pokud oba soubory existují, má soubor. amlignore přednost.

### <a name="training-scripts"></a>Trénovací skripty

Pro trénování modelu, určíte adresář, který obsahuje skript školení a přidružené soubory. Zadejte také název experimentu, který se používá k ukládání informací shromážděných během školení. Během školení se celý adresář zkopíruje do školicího prostředí (cíl výpočtů) a spustí se skript, který je zadaný v konfiguraci spuštění. Snímek adresáře je také uložen v rámci testu v pracovním prostoru.

Příklad najdete v tématu [kurz: výuka modelu klasifikace obrázků pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="workspaces"></a>Pracovní prostory

[Pracovní prostor](concept-workspace.md) je prostředek nejvyšší úrovně pro Azure Machine Learning. Poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte při použití Azure Machine Learning. Pracovní prostor můžete sdílet s ostatními. Podrobný popis pracovních prostorů najdete v tématu [co je Azure Machine Learning pracovní prostor?](concept-workspace.md).

### <a name="next-steps"></a>Další kroky

Pokud chcete začít s Azure Machine Learning, přečtěte si:

* [Co je Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md)
* [Kurz (část 1): výuka modelu](tutorial-train-models-with-aml.md)
