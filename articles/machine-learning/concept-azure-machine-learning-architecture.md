---
title: Klíčové koncepty & architektury
titleSuffix: Azure Machine Learning
description: Tento článek poskytuje podrobný přehled architektury, podmínek a konceptů, které tvoří Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: dc1954c97da0d7f40deaf0f4efa7ca99793107bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102503687"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Jak Azure Machine Learning funguje: architektura a koncepty

Seznamte se s architekturou a koncepty pro [Azure Machine Learning](overview-what-is-azure-ml.md).  Tento článek vám poskytne základní informace o součástech a o tom, jak společně pomáhají při sestavování, nasazování a údržbě modelů strojového učení.

## <a name="workspace"></a><a name="workspace"></a> Stejných

[Pracovní prostor Machine Learning](concept-workspace.md) je prostředek nejvyšší úrovně pro Azure Machine Learning.

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Diagram: Azure Machine Learning architektury pracovního prostoru a jeho součástí":::

Pracovní prostor je centralizované místo pro:

* Správa prostředků, které používáte pro školení a nasazení modelů, například [výpočetních](#compute-instance) prostředků
* Prostředky, které vytvoříte, můžete ukládat při použití Azure Machine Learning, včetně:
  * [Prostředí](#environments)
  * [Experimenty](#experiments)
  * [Pipelines](#ml-pipelines)
  * [Datové sady](#datasets-and-datastores)
  * [Modely](#models)
  * [Koncové body](#endpoints)

Pracovní prostor obsahuje další prostředky Azure, které používá pracovní prostor:

+ [Azure Container Registry (ACR)](https://azure.microsoft.com/services/container-registry/): registruje kontejnery Docker, které používáte během školení a při nasazení modelu. Pro minimalizaci nákladů je ACR vytvořena pouze při vytváření imagí nasazení.
+ [Azure Storage účet](https://azure.microsoft.com/services/storage/): slouží jako výchozí úložiště dat pro pracovní prostor.  Jupyter poznámkové bloky používané s vašimi výpočetními instancemi Azure Machine Learning jsou také uloženy.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): ukládá informace o monitorování vašich modelů.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): ukládá tajné kódy používané výpočetními cíli a dalšími citlivými informacemi, které pracovní prostor potřebuje.

Pracovní prostor můžete sdílet s ostatními.

## <a name="computes"></a>Vypočítá

<a name="compute-targets"></a>[Výpočetní cíl](concept-compute-target.md) je libovolný počítač nebo sada počítačů, které používáte ke spuštění školicího skriptu nebo hostování nasazení služby. Jako cíl výpočetní služby můžete použít místní počítač nebo vzdálený výpočetní prostředek.  Díky výpočetním cílům můžete začít školení na místním počítači a pak škálovat do cloudu beze změny školicího skriptu.

Azure Machine Learning zavádí dva plně spravované cloudové virtuální počítače, které jsou nakonfigurované pro úlohy strojového učení:

* <a name="compute-instance"></a>**Instance COMPUTE**: výpočetní instance je virtuální počítač, který obsahuje několik nástrojů a prostředí nainstalovaných pro strojové učení. Primární použití výpočetní instance je pro vaši vývojovou pracovní stanici.  Můžete začít používat ukázkové poznámkové bloky bez nutnosti instalace. Výpočetní instanci lze také použít jako cíl výpočtů pro školení a Inferencing úlohy.

* **Výpočetní clustery**: výpočetní clustery jsou cluster virtuálních počítačů s možnostmi škálování na více uzlů. Výpočetní clustery jsou lépe vhodné pro výpočetní cíle pro velké úlohy a produkci.  Při odeslání úlohy se cluster automaticky škáluje.  Použijte jako školicí cíl výpočetní služby nebo pro nasazení pro vývoj a testování.

Další informace o školicích cílech výpočtů najdete v tématu [školení výpočetních cílů](concept-compute-target.md#train).  Další informace o výpočetních cílech nasazení najdete v tématu [cíle nasazení](concept-compute-target.md#deploy).

## <a name="datasets-and-datastores"></a>Datové sady a úložiště dat

[**Azure Machine Learning datové sady**](concept-data.md#datasets)  usnadňují přístup k datům a práci s nimi. Vytvořením datové sady vytvoříte odkaz na umístění zdroje dat společně s kopií jeho metadat. Vzhledem k tomu, že data zůstanou ve svém stávajícím umístění, nebudete mít žádné dodatečné náklady na úložiště a nebudete mít na paměti integritu vašich zdrojů dat.

Další informace najdete v tématu [Vytvoření a registrace Azure Machine Learning datových sad](how-to-create-register-datasets.md).  Další příklady použití datových sad najdete v [ukázkových poznámkových blocích](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial).

Datové sady používají [úložiště dat](concept-data.md#datastores) pro zabezpečené připojení ke službám Azure Storage. Úložiště dat ukládá informace o připojení bez vložení přihlašovacích údajů pro ověřování a integrity původního zdroje dat. Ukládají informace o připojení, například ID předplatného a autorizaci tokenů ve vašem Key Vault přidružené k pracovnímu prostoru, takže můžete bezpečně přistupovat k úložišti, aniž byste je museli zakódovat do svého skriptu.

## <a name="environments"></a>Prostředí

[Pracovní prostor](#workspace)  >  **Prostředí**

[Prostředí](concept-environments.md) je zapouzdření prostředí, ve kterém se provádí školení nebo bodování modelu strojového učení. Prostředí Určuje balíčky Pythonu, proměnné prostředí a nastavení softwaru kolem vašich školicích a vyhodnocovacích skriptů.  

Ukázky kódu najdete v části "Správa prostředí" tématu [použití prostředí](how-to-use-environments.md#manage-environments).

## <a name="experiments"></a>Experimenty

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

Konfigurace spuštění definuje, jak by se měl skript spustit v zadaném výpočetním cíli. Pomocí konfigurace můžete zadat skript, cíl výpočtů a prostředí Azure ML, na kterém budou spouštěny všechny distribuované konfigurace specifické pro danou úlohu a některé další vlastnosti. Další informace o kompletní sadě konfigurovatelných možností pro spuštění najdete v tématu [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig).

Konfigurace spuštění může být trvalá do souboru v adresáři, který obsahuje školicí skript.   Nebo může být vytvořen jako objekt v paměti a použit k odeslání běhu.

Například konfigurace spuštění najdete v tématu [Konfigurace školicího běhu](how-to-set-up-training-targets.md).

### <a name="snapshots"></a>Snímky

[Pracovní prostor](#workspace)  >  [Experimenty](#experiments)  >  [Spustit příkaz](#runs)  >  **Snímek**

Když odešlete běh, Azure Machine Learning zkomprimuje adresář, který obsahuje skript jako soubor zip, a odešle ho do cíle služby Compute. Pak se soubor zip extrahuje a v něm se spustí skript. Azure Machine Learning také ukládá soubor ZIP jako snímek jako součást záznamu spuštění. Kdokoli s přístupem k pracovnímu prostoru může procházet záznam spuštění a stáhnout snímek.

### <a name="logging"></a>protokolování

Azure Machine Learning automaticky zaznamená standardní metriky běhu za vás. Můžete ale také [použít sadu Python SDK k protokolování libovolných metrik](how-to-track-experiments.md).

Existují různé způsoby zobrazení protokolů: sledování stavu spuštění v reálném čase nebo zobrazení výsledků po dokončení. Další informace najdete v tématu [monitorování a zobrazování protokolů spuštění ml](how-to-monitor-view-training-logs.md).


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Sledování a integrace Git

Když spustíte školicí kurz, kde zdrojový adresář je místní úložiště Git, informace o úložišti se ukládají v historii spuštění. Tato funkce funguje s poslanými běhy na základě konfigurace běhu skriptu nebo kanálu ML. Funguje taky pro spuštění odeslaná ze sady SDK nebo rozhraní příkazového řádku Machine Learning.

Další informace najdete v tématu [integrace Gitu pro Azure Machine Learning](concept-train-model-git-integration.md).

### <a name="training-workflow"></a>Pracovní postup školení

Když spustíte experiment pro výuku modelu, dojde k následujícím krokům. Ty jsou znázorněné v diagramu pracovního postupu školení níže:

* Azure Machine Learning se volá s ID snímku pro snímek kódu uložený v předchozí části.
* Azure Machine Learning vytvoří ID spuštění (volitelné) a token služby Machine Learning, který je později využíván výpočetními cíli, jako je Výpočetní prostředky služby Machine Learning/virtuální počítače ke komunikaci se službou Machine Learning.
* Ke spuštění školicích úloh můžete vybrat buď spravovaný cíl služby COMPUTE (například Výpočetní prostředky služby Machine Learning), nebo nespravovaný cíl služby COMPUTE (například virtuální počítače). Zde jsou datové toky pro oba scénáře:
   * Virtuální počítače/HDInsight, ke kterým mají přístup přihlašovací údaje SSH v trezoru klíčů v předplatném Microsoftu. Azure Machine Learning spouští kód pro správu na výpočetním cíli, který:

   1. Připraví prostředí. (Docker je možnost pro virtuální počítače a místní počítače. Pokud chcete zjistit, jak fungují experimenty na kontejnerech Docker, Projděte si následující postup Výpočetní prostředky služby Machine Learning.)
   1. Stáhne kód.
   1. Nastaví proměnné prostředí a konfigurace.
   1. Spustí uživatelské skripty (snímek kódu uvedený v předchozí části).

   * K Výpočetní prostředky služby Machine Learning přistupované prostřednictvím identity spravované v pracovním prostoru.
Vzhledem k tomu, že Výpočetní prostředky služby Machine Learning je spravovaný výpočetní cíl (to znamená, že ho spravuje Microsoft), běží pod vaším předplatným Microsoft.

   1. V případě potřeby se spustí konstrukce vzdáleného Docker.
   1. Kód správy je zapsán do sdílené složky souborů Azure uživatele.
   1. Kontejner je spuštěn s počátečním příkazem. To znamená kód správy, jak je popsáno v předchozím kroku.

* Po dokončení běhu se můžete dotazovat na běhy a metriky. V níže uvedeném diagramu se tento krok stane, když výpočetní cíl školení zapíše metriky Run zpátky do Azure Machine Learning z úložiště v databázi Cosmos DB. Klienti můžou volat Azure Machine Learning. Machine Learning bude z databáze Cosmos DB znovu aktivovat metriky a vracet je zpět klientovi.

[![Pracovní postup školení](media/concept-azure-machine-learning-architecture/training-and-metrics.png)](media/concept-azure-machine-learning-architecture/training-and-metrics.png#lightbox)

## <a name="models"></a>Modely

V nejjednodušším modelu je kód, který přebírá vstup a vytváří výstup. Vytvoření modelu Machine Learning zahrnuje výběr algoritmu a jeho poskytování dat a [ladění parametrů](how-to-tune-hyperparameters.md). Školení je iterativní proces, který vytváří školicí model, který zapouzdřuje model, který byl zjištěn během procesu školení.

Můžete uvést model, který byl vyškolený mimo Azure Machine Learning. Nebo můžete model vytvořit tak, že odešlete [spuštění](#runs) [experimentu](#experiments) do [cíle výpočetní](#compute-targets) služby v Azure Machine Learning. Po vytvoření modelu [zaregistrujete model](#register-model) v pracovním prostoru.

Azure Machine Learning je nezávislá Framework. Při vytváření modelu můžete použít jakoukoli oblíbenou architekturu strojového učení, jako je Scikit-Learning, XGBoost, PyTorch, TensorFlow a chainer.

Příklad školení modelu pomocí Scikit-učení najdete v tématu [kurz: výuka modelu klasifikace obrázků pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).


### <a name="model-registry"></a><a name="register-model"></a> Registr modelu

[Pracovní prostor](#workspace)  >  **Modely**

**Registr modelu** umožňuje sledovat všechny modely v pracovním prostoru Azure Machine Learning.

Modely se identifikují podle názvu a verze. Pokaždé, když zaregistrujete model se stejným názvem, jako má stávající, registr předpokládá, že se jedná o novou verzi. Verze se zvýší a nový model se zaregistruje pod stejným názvem.

Při registraci modelu můžete zadat další značky metadat a pak použít značky při hledání modelů.

> [!TIP]
> Registrovaný model je logický kontejner pro jeden nebo více souborů, které tvoří model. Například pokud máte model, který je uložený v několika souborech, můžete je zaregistrovat jako jeden model v pracovním prostoru Azure Machine Learning. Po registraci můžete zaregistrovaný model stáhnout nebo nasadit a získat všechny soubory, které byly zaregistrovány.

Registrovaný model, který je používán aktivním nasazením, nelze odstranit.

Příklad registrace modelu naleznete v tématu [výuka modelu klasifikace obrázku pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).

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

Při nasazování modelu jako webové služby je možné koncový bod nasadit v Azure Container Instances, službě Azure Kubernetes nebo FPGA. Službu vytvoříte z modelu, skriptu a přidružených souborů. Jsou umístěny do základní image kontejneru, která obsahuje spouštěcí prostředí pro model. Image má koncový bod HTTP s vyrovnáváním zatížení, který přijímá požadavky na bodování, které se odesílají do webové služby.

Můžete povolit Application Insights telemetrie nebo telemetrie modelů a monitorovat tak webovou službu. Data telemetrie jsou dostupná jenom pro vás.  Je uložený ve vašich Application Insights a instancích účtu úložiště. Pokud jste povolili automatické škálování, Azure automaticky škáluje vaše nasazení.

Následující diagram znázorňuje odvozený pracovní postup pro model nasazený jako koncový bod webové služby:

Podrobnosti najdete tady:

* Uživatel zaregistruje model pomocí klienta, jako je Azure Machine Learning SDK.
* Uživatel vytvoří obrázek pomocí modelu, souboru skóre a dalších závislostí modelu.
* Image Docker se vytvoří a uloží v Azure Container Registry.
* Webová služba je nasazena do cíle služby COMPUTE (Container Instances/AKS) pomocí image vytvořené v předchozím kroku.
* Podrobnosti žádosti o vyhodnocování jsou uložené v Application Insights, který je v předplatném uživatele.
* Telemetrii se taky vloží do předplatného Microsoft/Azure.

[![Pracovní postup odvození](media/concept-azure-machine-learning-architecture/inferencing.png)](media/concept-azure-machine-learning-architecture/inferencing.png#lightbox)


Příklad nasazení modelu jako webové služby najdete [v tématu nasazení modelu klasifikace imagí v Azure Container Instances](tutorial-deploy-models-with-aml.md).

#### <a name="real-time-endpoints"></a>Koncové body v reálném čase

Když nasadíte školený model v návrháři, můžete [model nasadit jako koncový bod v reálném čase](tutorial-designer-automobile-price-deploy.md). Koncový bod v reálném čase obvykle přijímá jednu žádost prostřednictvím koncového bodu REST a vrací předpovědi v reálném čase. To je na rozdíl od dávkového zpracování, který zpracovává více hodnot najednou a ukládá výsledky po dokončení do úložiště dat.

#### <a name="pipeline-endpoints"></a>Koncové body kanálu

Koncové body kanálu umožňují volat [kanály ml](#ml-pipelines) programově prostřednictvím koncového bodu REST. Koncové body kanálu umožňují automatizovat vaše pracovní postupy kanálu.

Koncový bod kanálu je kolekcí publikovaných kanálů. Tato logická organizace vám umožní spravovat a volat víc kanálů pomocí stejného koncového bodu. Každý publikovaný kanál v koncovém bodu kanálu je ve verzi. Můžete vybrat výchozí kanál pro koncový bod nebo zadat verzi ve volání REST.
 

#### <a name="iot-module-endpoints"></a>Koncové body modulu IoT

Nasazený koncový bod modulu IoT je kontejner Docker, který obsahuje váš model a přidružený skript nebo aplikaci a všechny další závislosti. Tyto moduly nasadíte pomocí Azure IoT Edge na hraničních zařízeních.

Pokud jste povolili monitorování, Azure shromáždí data telemetrie z modelu uvnitř modulu Azure IoT Edge. Data telemetrie jsou dostupná jenom pro vás a ukládají se do vaší instance účtu úložiště.

Azure IoT Edge zajistí, že je váš modul spuštěný, a monitoruje zařízení, které ho hostuje. 
## <a name="automation"></a>Automation

### <a name="azure-machine-learning-cli"></a>Rozhraní CLI služby Azure Machine Learning 

[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) je rozšíření Azure CLI, rozhraní příkazového řádku pro více platforem pro platformu Azure. Toto rozšíření poskytuje příkazy pro automatizaci aktivit strojového učení.

### <a name="ml-pipelines"></a>Kanály ML

Pomocí [kanálů strojového učení](concept-ml-pipelines.md) můžete vytvářet a spravovat pracovní postupy, které dohromady spojí fáze strojového učení. Kanál může například zahrnovat přípravu dat, školení modelů, nasazení modelu a fáze odvození a bodování. Každá fáze může zahrnovat několik kroků, z nichž každá může běžet bez obsluhy v různých výpočetních cílech. 

Kroky kanálu jsou opakovaně použitelné a je možné je spustit bez nutnosti znovu spustit předchozí kroky, pokud se výstup těchto kroků nezměnil. V případě, že se data nezměnila, můžete například přeškolit model bez nutnosti znovu spustit nákladný postup přípravy dat. Kanály také umožňují pracovníkům dat spolupracovat při práci na samostatných oblastech pracovního postupu Machine Learning.

## <a name="monitoring-and-logging"></a>Monitorování a protokolování

Azure Machine Learning poskytuje následující možnosti monitorování a protokolování:

* Pro odborníky přes __data__ můžete sledovat experimenty a informace protokolu z vašich školicích běhů. Další informace najdete v následujících článcích:
   * [Spuštění, monitorování a zrušení školicích běhů](how-to-manage-runs.md)
   * [Protokolování metrik pro tréninková spuštění](how-to-track-experiments.md)
   * [Sledování experimentů s využitím MLflow](how-to-use-mlflow.md)
   * [Vizualizace spuštění s využitím TensorBoardu](how-to-monitor-tensorboard.md)
* Pro __správce__ můžete monitorovat informace o pracovním prostoru, souvisejících prostředcích Azure a událostech, jako je vytváření a odstraňování prostředků, pomocí Azure monitor. Další informace najdete v tématu [monitorování Azure Machine Learning](monitor-azure-machine-learning.md).
* Pro __DevOps__ nebo __MLOps__ můžete monitorovat informace vygenerované modely nasazenými jako webové služby nebo moduly IoT Edge a identifikovat tak problémy s nasazeními a shromažďovat data odeslaná do služby. Další informace najdete v tématu [shromáždění dat modelu](how-to-enable-data-collection.md) a [monitorování pomocí Application Insights](how-to-enable-app-insights.md).

## <a name="interacting-with-your-workspace"></a>Interakce s vaším pracovním prostorem

### <a name="studio"></a>Studio

[Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) poskytuje webové zobrazení všech artefaktů ve vašem pracovním prostoru.  Můžete zobrazit výsledky a podrobnosti vašich datových sad, experimentů, kanálů, modelů a koncových bodů.  V studiu můžete také spravovat výpočetní prostředky a úložiště dat.

Nástroj Studio také umožňuje přístup k interaktivním nástrojům, které jsou součástí Azure Machine Learning:

+ [Azure Machine Learning Designer](concept-designer.md) k provedení kroků pracovního postupu bez psaní kódu
+ Webové prostředí pro [automatizované strojové učení](concept-automated-ml.md)
+ [Azure Machine Learning poznámkových blocích](how-to-run-jupyter-notebooks.md) pro psaní a spouštění vlastního kódu na integrovaných serverech Jupyter notebook.
+ Vytváření, Správa a monitorování projektů k označování dat v [projektech](how-to-create-labeling-projects.md)

### <a name="programming-tools"></a>Programovací nástroje

> [!IMPORTANT]
> Nástroje označené (Preview) jsou momentálně ve verzi Public Preview.
> Verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+  Spolupracovat se službou v jakémkoli prostředí Pythonu s [Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro).
+ Interakci se službou v jakémkoli prostředí R s [Azure Machine Learning SDK pro R](https://azure.github.io/azureml-sdk-for-r/reference/index.html) (Preview).
+ Použijte [návrháře Azure Machine Learning](concept-designer.md) k provedení kroků pracovního postupu bez psaní kódu. 
+ Pro automatizaci použijte [Azure Machine Learning CLI](./reference-azure-machine-learning-cli.md) .
+ [Mnohé modely řešení](https://aka.ms/many-models) (Preview) jsou sestavené na Azure Machine Learning a umožňují výuku, provozování a správu stovek nebo dokonce tisíců modelů strojového učení.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s Azure Machine Learning, přečtěte si:

* [Co je Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md)
* [Kurz (část 1): výuka modelu](tutorial-train-models-with-aml.md)