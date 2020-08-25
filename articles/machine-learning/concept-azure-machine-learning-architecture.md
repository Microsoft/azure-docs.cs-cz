---
title: Klíčové koncepty & architektury
titleSuffix: Azure Machine Learning
description: Přečtěte si o architektuře, pojmech a konceptech, které tvoří Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: d7bad24510f74a7fadd74328e24ea22855e6fe02
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750855"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Jak Azure Machine Learning funguje: architektura a koncepty

Seznamte se s architekturou a koncepty pro Azure Machine Learning.

> [!NOTE]
> I když tento článek popisuje pojmy a koncepty, které používá Azure Machine Learning, nedefinuje pojmy a koncepty pro platformu Azure. Další informace o terminologii platforem Azure najdete v tématu [Microsoft Azure Glosář](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a><a name="workspace"></a> Stejných

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Architektura Azure Machine Learning":::

[Pracovní prostor Machine Learning](concept-workspace.md) je prostředek nejvyšší úrovně pro Azure Machine Learning.  Pracovní prostor je centralizované místo pro:
* Správa prostředků, které používáte pro školení a nasazení modelů, například [výpočetních](#compute-instance) prostředků
* Prostředky, které vytvoříte, můžete ukládat při použití Azure Machine Learning, včetně:
  * [Prostředí](#environments)
  * [Běží](#runs)
  * [Pipelines](#ml-pipelines)
  * [Datové sady](#datasets-and-datastores)
  * [Modely](#models)
  * [Bod](#endpoints)

Pracovní prostor obsahuje další prostředky Azure, které používá pracovní prostor:

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): registruje kontejnery Docker, které používáte během školení a při nasazení modelu. Pro minimalizaci nákladů je ACR **opožděně načteno** , dokud se nevytvoří image nasazení.
+ [Azure Storage účet](https://azure.microsoft.com/services/storage/): slouží jako výchozí úložiště dat pro pracovní prostor.  Jupyter poznámkové bloky používané s vašimi výpočetními instancemi Azure Machine Learning jsou také uloženy.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): ukládá informace o monitorování vašich modelů.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): ukládá tajné kódy používané výpočetními cíli a dalšími citlivými informacemi, které pracovní prostor potřebuje.

Pracovní prostor můžete sdílet s ostatními.

## <a name="studio"></a>Studio

[Azure Machine Learning Studio](https://ml.azure.com) poskytuje webové zobrazení všech artefaktů ve vašem pracovním prostoru.  Tento portál také umožňuje přístup k interaktivním nástrojům, které jsou součástí Azure Machine Learning:

+ [Azure Machine Learning Designer (Preview)](concept-designer.md) k provedení kroků pracovního postupu bez psaní kódu
+ Webové prostředí pro [automatizované strojové učení](concept-automated-ml.md)
+ Vytváření, Správa a monitorování projektů k označení dat v [projektech](how-to-create-labeling-projects.md)

##  <a name="computes"></a>Vypočítá

<a name="compute-targets"></a>[Výpočetní cíl](concept-compute-target.md) je počítač nebo sada počítačů, ve kterých spouštíte školicí skript nebo hostování nasazení služby. Toto umístění může být vaším místním počítačem nebo vzdáleným výpočetním prostředkem.

Azure Machine Learning zavádí dva plně spravované cloudové výpočetní prostředky, které jsou nakonfigurovány pro úlohy strojového učení:

* <a name="compute-instance"></a>**Instance COMPUTE** ([computeinstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance?view=azure-ml-py)): výpočetní instance je virtuální počítač (VM), který obsahuje několik nástrojů a prostředí nainstalovaných pro strojové učení. K zahájení spouštění ukázkových poznámkových bloků bez nutnosti instalace použijte výpočetní instanci jako svou vývojovou pracovní stanici. Dá se použít taky jako cíl výpočtů pro školení a Inferencing úlohy.
* **Výpočetní clustery** ([Amlcompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py)): cluster virtuálních počítačů s možnostmi škálování na více uzlů. Po odeslání úlohy se automaticky škáluje. Lépe vhodné pro výpočetní cíle pro velké úlohy a produkci. Použijte jako školicí cíl výpočetní služby nebo pro nasazení pro vývoj a testování.

Další informace o školicích cílech výpočtů najdete v tématu [školení výpočetních cílů](concept-compute-target.md#train).  Další informace o výpočetních cílech nasazení najdete v tématu [cíle nasazení](concept-compute-target.md#deploy).

## <a name="datasets-and-datastores"></a>Datové sady a úložiště dat

[**Azure Machine Learning datové sady**](concept-data.md#datasets)  usnadňují přístup k datům a práci s nimi. Datové sady spravují data v různých scénářích, jako jsou například školení modelů a vytváření kanálů. Pomocí sady Azure Machine Learning SDK můžete získat přístup k základnímu úložišti, prozkoumat data a spravovat životní cyklus různých definic datových sad.

Datové sady poskytují metody pro práci s daty v oblíbených formátech, jako je například použití `from_delimited_files()` nebo `to_pandas_dataframe()` .

Další informace najdete v tématu [Vytvoření a registrace Azure Machine Learning datových sad](how-to-create-register-datasets.md).  Další příklady použití datových sad najdete v [ukázkových poznámkových blocích](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial).

[**Úložiště dat**](concept-data.md#datastores) je abstrakce úložiště v rámci účtu úložiště Azure. Každý pracovní prostor má výchozí úložiště dat a můžete zaregistrovat další úložiště dat. K ukládání a načítání souborů z úložiště dat použijte rozhraní Python SDK API nebo Azure Machine Learning CLI. 

## <a name="models"></a>Modely

V nejjednodušším modelu je kód, který přebírá vstup a vytváří výstup. Vytvoření modelu Machine Learning zahrnuje výběr algoritmu a jeho poskytování dat a [ladění parametrů](how-to-tune-hyperparameters.md). Školení je iterativní proces, který vytváří školicí model, který zapouzdřuje model, který byl zjištěn během procesu školení.

Model je vytvořen [spuštěním](#runs) [experimentu](#experiments) v Azure Machine Learning. Můžete také použít model, který je vyškolen mimo Azure Machine Learning. Pak [zaregistrujete model](#register-model) v pracovním prostoru.

Azure Machine Learning je nezávislá Framework. Při vytváření modelu můžete použít jakoukoli oblíbenou architekturu strojového učení, jako je Scikit-Learning, XGBoost, PyTorch, TensorFlow a chainer.

Příklad školení modelu pomocí Scikit-učení najdete v tématu [kurz: výuka modelu klasifikace obrázků pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="model-registry"></a><a name="register-model"></a> Registr modelu
[Pracovní prostor](#workspace)  >  **Registr modelu**

**Registr modelu** umožňuje sledovat všechny modely v pracovním prostoru Azure Machine Learning.

Modely se identifikují podle názvu a verze. Pokaždé, když zaregistrujete model se stejným názvem, jako má stávající, registr předpokládá, že se jedná o novou verzi. Verze se zvýší a nový model se zaregistruje pod stejným názvem.

Při registraci modelu můžete zadat další značky metadat a pak použít značky při hledání modelů.

> [!TIP]
> Registrovaný model je logický kontejner pro jeden nebo více souborů, které tvoří model. Například pokud máte model, který je uložený v několika souborech, můžete je zaregistrovat jako jeden model v pracovním prostoru Azure Machine Learning. Po registraci můžete zaregistrovaný model stáhnout nebo nasadit a získat všechny soubory, které byly zaregistrovány.

Registrovaný model, který je používán aktivním nasazením, nelze odstranit.

Příklad registrace modelu naleznete v tématu [výuka modelu klasifikace obrázku pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).


### <a name="environments"></a>Prostředí

[Pracovní prostor](#workspace)  >  **Prostředí**

[Prostředí](concept-environments.md) je zapouzdření prostředí, ve kterém se provádí školení nebo bodování modelu strojového učení. Prostředí Určuje balíčky Pythonu, proměnné prostředí a nastavení softwaru kolem vašich školicích a vyhodnocovacích skriptů.

Ukázky kódu najdete v části "Správa prostředí" tématu [použití prostředí](how-to-use-environments.md#manage-environments).

### <a name="experiments"></a>Experimenty

[Pracovní prostor](#workspace)  >  **Experimenty**

Experiment je seskupení mnoha běhů ze zadaného skriptu. Vždycky patří do pracovního prostoru. Po odeslání běhu zadáte název experimentu. Informace pro běh jsou uloženy v rámci tohoto experimentu. Pokud název neexistuje při odeslání experimentu, dojde k automatickému vytvoření nového experimentu.

Příklad použití experimentu najdete v tématu [kurz: výuka prvního modelu](tutorial-1st-experiment-sdk-train.md).

### <a name="runs"></a>Běží

[Pracovní prostor](#workspace)  >  [Experimenty](#experiments)  >  **Spustit příkaz**

Spuštění je jediné spuštění školicího skriptu. Experiment bude obvykle obsahovat více spuštění.

Azure Machine Learning zaznamenává všechna spuštění a ukládá do experimentu následující informace:

* Metadata o běhu (časové razítko, doba trvání atd.)
* Metriky, které jsou protokolovány vaším skriptem
* Výstupní soubory, které jsou na základě experimentu shromažďovány nebo výslovně nahrány
* Snímek adresáře, který obsahuje vaše skripty před spuštěním

Spuštění vytvoříte při odeslání skriptu pro výuku modelu. Spuštění může mít nula nebo více podřízených spuštění. Například spuštění na nejvyšší úrovni může mít dvě podřízená spuštění, z nichž každá může mít vlastní podřízený běh.

### <a name="run-configurations"></a>Konfigurace spuštění

[Pracovní prostor](#workspace)  >  [Experimenty](#experiments)  >  [Spustit příkaz](#runs)  >  **Konfigurace spuštění**

Konfigurace spuštění je sada instrukcí, které definují, jak by měl skript běžet v zadaném výpočetním cíli. Tato konfigurace zahrnuje rozsáhlou sadu definic chování, například to, jestli se má použít existující prostředí Pythonu nebo jak používat conda prostředí, které je sestavené ze specifikace.

Konfigurace spuštění může být trvalá do souboru v adresáři, který obsahuje školicí skript.   Nebo může být vytvořen jako objekt v paměti a použit k odeslání běhu.

Například konfigurace spuštění najdete v tématu [Výběr a použití výpočetní cíle ke školení modelu](how-to-set-up-training-targets.md).

### <a name="estimators"></a>Odhady

Pro usnadnění školení modelů s oblíbenými rozhraními vám třída Estimator umožňuje snadno sestavit konfigurace spuštění. Můžete vytvořit a použít obecné [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) k odesílání školicích skriptů, které používají všechny vámi zvolené vzdělávací architektury (například scikit-učení).

Pro úlohy PyTorch, TensorFlow a řetězení Azure Machine Learning poskytuje také příslušné [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)a [Chain](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) odhady pro zjednodušení používání těchto rozhraní.

Další informace najdete v následujících článcích:

* [Modely vlakových ml pomocí odhady](how-to-train-ml-models.md).
* [Pytorch se škálují modely hloubkového učení s](how-to-train-pytorch.md)využitím Azure Machine Learning.
* [TensorFlow modely a zaregistrujte se ve velkém měřítku pomocí Azure Machine Learning](how-to-train-tensorflow.md).
* [Škálujte a Registrujte modely zřetězení ve velkém měřítku pomocí Azure Machine Learning](how-to-train-ml-models.md).

### <a name="snapshots"></a>Snímky

[Pracovní prostor](#workspace)  >  [Experimenty](#experiments)  >  [Spustit příkaz](#runs)  >  **Snímek**

Když odešlete běh, Azure Machine Learning zkomprimuje adresář, který obsahuje skript jako soubor zip, a odešle ho do cíle služby Compute. Pak se soubor zip extrahuje a v něm se spustí skript. Azure Machine Learning také ukládá soubor ZIP jako snímek jako součást záznamu spuštění. Kdokoli s přístupem k pracovnímu prostoru může procházet záznam spuštění a stáhnout snímek.


### <a name="logging"></a>protokolování

Při vývoji řešení použijte sadu Azure Machine Learning Python SDK ve vašem skriptu Pythonu k protokolování libovolných metrik. Po spuštění dotazu na metriky určete, zda běh vytvořil model, který chcete nasadit.


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Sledování a integrace Git

Když spustíte školicí kurz, kde zdrojový adresář je místní úložiště Git, informace o úložišti se ukládají v historii spuštění. Tato funkce funguje s poslanými běhy s použitím kanálu Estimator, ML nebo spuštění skriptu. Funguje taky pro spuštění odeslaná ze sady SDK nebo rozhraní příkazového řádku Machine Learning.

Další informace najdete v tématu [integrace Gitu pro Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="deployment"></a>Nasazení

[Registrovaný model](#register-model) nasadíte jako koncový bod služby. Potřebujete následující komponenty:

* **Prostředí**. Toto prostředí zapouzdřuje závislosti potřebné ke spuštění modelu pro odvození.
* **Kód bodování**. Tento skript přijímá požadavky, zarovnává požadavky pomocí modelu a vrátí výsledky.
* **Konfigurace odvození**. Konfigurace odvození určuje prostředí, vstupní skript a další komponenty potřebné ke spuštění modelu jako služby.

Další informace o těchto součástech najdete v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="endpoints"></a>Koncové body

[Pracovní prostor](#workspace)  >  **Koncové body**

Koncový bod je instance vašeho modelu do webové služby, kterou je možné hostovat v cloudu nebo v modulu IoT pro nasazení integrovaných zařízení.

#### <a name="web-service-endpoint"></a>Koncový bod webové služby

Při nasazení modelu jako webové služby je možné koncový bod nasadit v Azure Container Instances, službě Azure Kubernetes nebo FPGA. Službu vytvoříte z modelu, skriptu a přidružených souborů. Jsou umístěny do základní image kontejneru, která obsahuje spouštěcí prostředí pro model. Image má koncový bod HTTP s vyrovnáváním zatížení, který přijímá požadavky na bodování, které se odesílají do webové služby.

Můžete povolit Application Insights telemetrie nebo telemetrie modelů a monitorovat tak webovou službu. Data telemetrie jsou dostupná jenom pro vás.  Je uložený ve vašich Application Insights a instancích účtu úložiště.

Pokud jste povolili automatické škálování, Azure automaticky škáluje vaše nasazení.

Příklad nasazení modelu jako webové služby najdete [v tématu nasazení modelu klasifikace imagí v Azure Container Instances](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>Koncové body modulu IoT

Nasazený koncový bod modulu IoT je kontejner Docker, který obsahuje váš model a přidružený skript nebo aplikaci a všechny další závislosti. Tyto moduly nasadíte pomocí Azure IoT Edge na hraničních zařízeních.

Pokud jste povolili monitorování, Azure shromáždí data telemetrie z modelu uvnitř modulu Azure IoT Edge. Data telemetrie jsou dostupná jenom pro vás a ukládají se do vaší instance účtu úložiště.

Azure IoT Edge zajistí, že je váš modul spuštěný, a monitoruje zařízení, které ho hostuje.
. 
## <a name="automation"></a>Automatizace

### <a name="azure-machine-learning-cli"></a>Azure Machine Learning CLI 

[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) je rozšíření Azure CLI, rozhraní příkazového řádku pro více platforem pro platformu Azure. Toto rozšíření poskytuje příkazy pro automatizaci aktivit strojového učení.

### <a name="ml-pipelines"></a>Kanály ML

Pomocí [kanálů strojového učení](concept-ml-pipelines.md) můžete vytvářet a spravovat pracovní postupy, které dohromady spojí fáze strojového učení. Kanál může například zahrnovat přípravu dat, školení modelů, nasazení modelu a fáze odvození a bodování. Každá fáze může zahrnovat několik kroků, z nichž každá může běžet bez obsluhy v různých výpočetních cílech. 

Kroky kanálu jsou opakovaně použitelné a je možné je spustit bez nutnosti znovu spustit předchozí kroky, pokud se výstup těchto kroků nezměnil. V případě, že se data nezměnila, můžete například přeškolit model bez nutnosti znovu spustit nákladný postup přípravy dat. Kanály také umožňují pracovníkům dat spolupracovat při práci na samostatných oblastech pracovního postupu Machine Learning.

## <a name="interacting-with-machine-learning"></a>Interakce s Machine Learningem

> [!IMPORTANT]
> Nástroje označené (Preview) jsou momentálně ve verzi Public Preview.
> Verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+  Spolupracovat se službou v jakémkoli prostředí Pythonu s [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Interakci se službou v jakémkoli prostředí R s [Azure Machine Learning SDK pro R](https://azure.github.io/azureml-sdk-for-r/reference/index.html) (Preview).
+ Použijte [Azure Machine Learning Designer (Preview)](concept-designer.md) k provedení kroků pracovního postupu bez psaní kódu. ( [Pracovní prostor organizace](concept-workspace.md#upgrade)) je vyžadován pro použití návrháře.)
+ Pro automatizaci použijte [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) .
+ [Mnohé modely řešení](https://aka.ms/many-models) (Preview) jsou sestavené na Azure Machine Learning a umožňují výuku, provozování a správu stovek nebo dokonce tisíců modelů strojového učení.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s Azure Machine Learning, přečtěte si:

* [Co je Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md)
* [Kurz (část 1): výuka modelu](tutorial-train-models-with-aml.md)
