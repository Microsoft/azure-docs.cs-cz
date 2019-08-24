---
title: Klíčové koncepty & architektury
titleSuffix: Azure Machine Learning service
description: Přečtěte si o architektuře, pojmech, konceptech a pracovních postupech, které tvoří službu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 07/12/2019
ms.custom: seodec18
ms.openlocfilehash: b7cb4f1a106d8caa2c43f5f17c5efa16a3ee6df2
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "70011645"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Jak služba Azure Machine Learning funguje: Architektura a koncepty

Přečtěte si o architektuře, konceptech a pracovních postupech pro službu Azure Machine Learning. Hlavní součásti služby a obecný pracovní postup pro používání služby jsou uvedeny v následujícím diagramu:

![Architektura a pracovní postup služby Azure Machine Learning](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Pracovní postup

Pracovní postup modelu Machine Learning se obvykle řídí tímto pořadím:

1. **Průřez**
    + Vývoj školicích skriptů pro strojové učení v **Pythonu** nebo pomocí vizuálního rozhraní.
    + Vytvoření a konfigurace **cílové výpočetní prostředí**.
    + **Odeslat skripty** do cílového výpočetního nakonfigurované prostředí pro spuštění v daném prostředí. Během školení můžou skripty číst nebo zapisovat do **úložiště dat**. A záznamy o spuštění jsou uloženy jako **běhy** v **pracovním prostoru** a seskupeny pod **experimenty**.

1. **Balíček** – po nalezení uspokojivého spuštění Zaregistrujte trvalý model v **registru modelu**.

1. Ověřte - **dotaz na experiment** u protokolovaných metrik z aktuálních a minulých spuštění. Pokud metriky nenaznačují požadovaný výsledek, vraťte se ke kroku 1 a Iterujte na svých skriptech.

1. **Nasazení** – vývoj vyhodnocovacího skriptu, který používá model a **nasazení modelu** jako **webové služby** v Azure nebo do **zařízení IoT Edge**.

1. **Monitor** – monitorování pro **Posun dat** mezi školicí datovou sadou a odvozenými daty nasazeného modelu. V případě potřeby se vraťte ke kroku 1 a přeškolujte model s novými školicími daty.

## <a name="tools-for-azure-machine-learning"></a>Nástroje pro Azure Machine Learning

Použijte tyto nástroje pro Azure Machine Learning:

+  Spolupracovat se službou v jakémkoli prostředí Pythonu s [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Automatizujte své aktivity strojového učení pomocí [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli).
+ Psaní kódu v Visual Studio Code s [rozšířením Azure Machine Learning vs Code](how-to-vscode-tools.md)
+ Použijte [rozhraní Visual Interface (Preview) pro službu Azure Machine Learning](ui-concept-visual-interface.md) k provedení kroků pracovního postupu bez psaní kódu.

> [!NOTE]
> I když tento článek popisuje pojmy a koncepty, které používá služba Azure Machine Learning, nedefinuje pro platformu Azure pojmy a koncepty. Další informace o terminologii platforem Azure najdete v tématu [Microsoft Azure Glosář](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Glosář

+ <a href="#workspaces">Stejných</a>
+ <a href="#experiments">Experimenty</a>
+ <a href="#models">Vzor</a>
+ <a href="#run-configurations">Konfigurace spuštění</a>
+ [Odhady](#estimators)
+ <a href="#datasets-and-datastores">Datová sada & úložiště dat</a>
+ <a href="#compute-targets">Cíle výpočtů</a>
+ <a href="#training-scripts">Školicí skript</a>
+ <a href="#runs">Spuštění</a>
+ <a href="#github-tracking-and-integration">Sledování Gitu</a>
+ <a href="#snapshots">Snímek</a>
+ <a href="#activities">Aktivita</a>
+ <a href="#images">Obrázek</a>
+ <a href="#deployment">Nasazení</a>
+ <a href="#web-service-deployments">Webové služby</a>
+ <a href="#iot-module-deployments">Moduly IoT</a>
+ <a href="#ml-pipelines">Kanály ML</a>
+ <a href="#logging">Logging</a>

### <a name="workspaces"></a>Pracovní prostory

[Pracovní prostor](concept-workspace.md) je prostředek nejvyšší úrovně pro službu Azure Machine Learning. Poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte při použití Azure Machine Learning služby. Pracovní prostor můžete sdílet s ostatními. Podrobný popis pracovních prostorů najdete v tématu [co je Azure Machine Learning pracovní prostor?](concept-workspace.md).

### <a name="experiments"></a>Experimenty

Experiment je seskupení mnoha běhů ze zadaného skriptu. Vždy patří do pracovního prostoru. Když odešlete spustit, zadejte název experimentu. Informace pro spuštění jsou uloženy v rámci testu. Pokud odešlete běh a určíte název experimentu, který neexistuje, automaticky se vytvoří nový experiment s tímto nově zadaným názvem.

Příklad použití experimentu najdete v tématu [kurz: Nahlaste svůj](tutorial-1st-experiment-sdk-train.md)první model.

### <a name="models"></a>Modely

V nejjednodušším model je část kódu, která přijímá vstup a výstup. Vytvoření modelu strojového učení zahrnuje výběr algoritmus, poskytuje s daty a ladění hyperparameters. Školení je iterativní proces, který vytváří trénovaného modelu, který zapouzdřuje modelu osvojené během procesu trénování.

Model je produkovaný spustit ve službě Azure Machine Learning. Můžete také použít model, který je vyškolen mimo Azure Machine Learning. Model můžete zaregistrovat v pracovním prostoru služby Azure Machine Learning.

Služba Azure Machine Learning je nezávislá Framework. Při vytváření modelu můžete použít jakoukoli oblíbenou architekturu strojového učení, jako je Scikit-Learning, XGBoost, PyTorch, TensorFlow a chainer.

Příklad školení modelu pomocí Scikit-učení a Estimator najdete v [kurzu: Školení modelu klasifikace obrázků pomocí služby](tutorial-train-models-with-aml.md)Azure Machine Learning.

**Registr modelu** udržuje přehled o všech modelech v pracovním prostoru služby Azure Machine Learning.

Modely jsou identifikovány názvem a verzí. Pokaždé, když zaregistrujete model se stejným názvem, jako má stávající, registr předpokládá, že se jedná o novou verzi. Verze se zvýší a nový model se zaregistruje pod stejným názvem.

Při registraci modelu můžete zadat další značky metadat a pak použít značky při hledání modelů.

> [!TIP]
> Registrovaný model je logický kontejner pro jeden nebo více souborů, které tvoří model. Například pokud máte model, který je uložený v několika souborech, můžete je zaregistrovat jako jeden model v pracovním prostoru Azure Machine Learning. Po registraci můžete zaregistrovaný model stáhnout nebo nasadit a získat všechny soubory, které byly zaregistrovány.

Registrovaný model, který je používán aktivním nasazením, nelze odstranit.

Příklad registrace modelu naleznete v tématu [výuka modelu klasifikace obrázku pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="run-configurations"></a>Konfigurace spuštění

Konfigurace spuštění je sada instrukcí, které definují, jak by měl skript běžet v zadaném výpočetním cíli. Tato konfigurace zahrnuje rozsáhlou sadu definic chování, například to, jestli se má použít existující prostředí Pythonu nebo jak používat conda prostředí, které je sestavené ze specifikace.

Konfiguraci spuštění lze zachovat do souboru v adresáři, který obsahuje školicí skript, nebo může být vytvořen jako objekt v paměti a použit k odeslání běhu.

Například konfigurace spuštění najdete v tématu [Výběr a použití výpočetní cíle ke školení modelu](how-to-set-up-training-targets.md).

### <a name="estimators"></a>Odhady

Pro usnadnění školení modelů s oblíbenými rozhraními vám třída Estimator umožňuje snadno sestavit konfigurace spuštění. Můžete vytvořit a použít obecné [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) k odesílání školicích skriptů, které používají všechny vámi zvolené vzdělávací architektury (například scikit-učení).

Pro úlohy PyTorch, TensorFlow a řetězení Azure Machine Learning poskytuje také příslušné [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)a [Chain](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) odhady pro zjednodušení používání těchto rozhraní.

Další informace najdete v následujících článcích:

* [Modely vlakových ml pomocí odhady](how-to-train-ml-models.md).
* [Pytorch se škálují modely hloubkového učení s](how-to-train-pytorch.md)využitím Azure Machine Learning.
* [Pomocí Azure Machine Learning služby je škálovatelná a zaregistrujte modely TensorFlow ve](how-to-train-tensorflow.md)velkém měřítku.
* [Škálujte a Registrujte modely zřetězení ve velkém měřítku pomocí Azure Machine Learning služby](how-to-train-chainer.md).

### <a name="datasets-and-datastores"></a>Datové sady a úložiště dat

**Azure Machine Learning datové sady** (Preview) usnadňuje přístup k datům a práci s nimi. Datové sady spravují data v různých scénářích, jako jsou například školení modelů a vytváření kanálů. Pomocí sady Azure Machine Learning SDK můžete získat přístup k základnímu úložišti, prozkoumat a připravit data, spravovat životní cyklus různých definic datových sad a srovnávat mezi datovými sadami používanými při výuce a v produkčním prostředí.

Datové sady poskytují metody pro práci s daty v oblíbených formátech, jako je například `from_delimited_files()` použití `to_pandas_dataframe()`nebo.

Další informace najdete v tématu [Vytvoření a registrace Azure Machine Learning datových sad](how-to-create-register-datasets.md).  Další příklady použití datových sad najdete v ukázkových [poznámkových blocích](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets).

**Úložiště dat** je abstrakce úložiště v rámci účtu úložiště Azure. Úložiště dat může jako back-end úložiště použít buď kontejner Azure Blob, nebo sdílenou složku Azure. Každý pracovní prostor má výchozí úložiště dat a můžete zaregistrovat další úložiště dat. K ukládání a načítání souborů z úložiště dat použijte rozhraní Python SDK API nebo Azure Machine Learning CLI.

### <a name="compute-targets"></a>Cílových výpočetních prostředí

[Cílový výpočetní](concept-compute-target.md) výkon vám umožní určit výpočetní prostředek, ve kterém spustíte školicí skript, nebo hostovat nasazení služby. Toto umístění může být váš místní počítač nebo cloudový výpočetní prostředek. Cíle výpočtů usnadňují změnu prostředí COMPUTE beze změny kódu.

Přečtěte si další informace o [dostupných výpočetních cílech pro školení a nasazení](concept-compute-target.md).

### <a name="training-scripts"></a>Trénovací skripty

Pro trénování modelu, určíte adresář, který obsahuje skript školení a přidružené soubory. Zadejte také název experimentu, který se používá k ukládání informací shromážděných během školení. Během školení se celý adresář zkopíruje do školicího prostředí (cíl výpočtů) a spustí se skript, který je zadaný v konfiguraci spuštění. Snímek adresáře je také uložen v rámci testu v pracovním prostoru.

Příklad najdete v tématu [kurz: Školení modelu klasifikace obrázků pomocí služby](tutorial-train-models-with-aml.md)Azure Machine Learning.

### <a name="runs"></a>Spuštění

Spuštění je jediné spuštění školicího skriptu. Azure Machine Learning zaznamenává všechna spuštění a ukládá následující informace:

* Metadata o běhu (časové razítko, doba trvání atd.)
* Metriky, které jsou protokolovány vaším skriptem
* Výstupní soubory, které jsou na základě experimentu shromažďovány nebo výslovně nahrány
* Snímek adresáře, který obsahuje vaše skripty před spuštění

Spuštění vytvoříte při odeslání skriptu pro výuku modelu. Spuštění může mít nula nebo více podřízených spuštění. Například spuštění na nejvyšší úrovni může mít dvě podřízená spuštění, z nichž každá může mít vlastní podřízený běh.

### <a name="github-tracking-and-integration"></a>Sledování a integrace GitHubu

Když spustíte školicí kurz, kde zdrojový adresář je místní úložiště Git, informace o úložišti se ukládají v historii spuštění. Například aktuální ID potvrzení pro úložiště je protokolováno jako součást historie. Tato funkce funguje s poslanými běhy s použitím kanálu Estimator, ML nebo spuštění skriptu. Funguje taky pro spuštění odeslaná ze sady SDK nebo rozhraní příkazového řádku Machine Learning.

### <a name="snapshots"></a>Snímky

Když odešlete běh, Azure Machine Learning zkomprimuje adresář, který obsahuje skript jako soubor zip, a odešle ho do cíle služby Compute. Pak se soubor zip extrahuje a v něm se spustí skript. Azure Machine Learning také ukládá soubor zip jako snímek jako součást spuštění záznamu. Každý, kdo má přístup k pracovním prostoru můžete procházet záznam spuštění a stáhnout snímek.

> [!NOTE]
> Aby nedocházelo k zahrnutí zbytečných souborů do snímku, udělejte soubor Ignore (. gitignore nebo. amlignore). Tento soubor umístěte do adresáře snímků a přidejte do něj názvy souborů, které chcete v něm ignorovat. Soubor. amlignore používá stejnou [syntaxi a vzory jako soubor. gitignore](https://git-scm.com/docs/gitignore). Pokud oba soubory existují, má soubor. amlignore přednost.

### <a name="activities"></a>Aktivity

Aktivita představuje dlouhotrvající operace. Příklady aktivit jsou následující operace:

* Vytvoření nebo odstranění cílové výpočetní prostředí
* Spuštění skriptu na cílové výpočetní prostředí

Aktivity můžou poskytovat oznámení prostřednictvím sady SDK nebo webového uživatelského rozhraní, abyste mohli snadno monitorovat průběh těchto operací.

### <a name="images"></a>Obrázky

Image poskytují způsob, jak spolehlivě nasadit model spolu se všemi komponentami, které potřebujete k použití modelu. Bitová kopie obsahuje následující položky:

* Model.
* Hodnoticí skript nebo aplikace. Pomocí skriptu předáte vstup do modelu a vrátíte výstup modelu.
* Závislosti, které jsou vyžadovány modelem nebo skriptem bodování nebo aplikací. Můžete například zahrnout soubor prostředí Conda, který obsahuje závislosti balíčků Python.

Azure Machine Learning může vytvořit dva typy imagí:

* **Obrázek FPGA**: Používá se při nasazení do pole brány v Azure, které je programovatelné polem.
* **Obrázek Docker**: Používá se při nasazení na jiné výpočetní cíle než FPGA. Příklady jsou Azure Container Instances a Azure Kubernetes Service.

Služba Azure Machine Learning poskytuje základní image, která se používá ve výchozím nastavení. Můžete také zadat vlastní image.

### <a name="image-registry"></a>Registru imagí

Bitové kopie jsou zařazené do katalogu v **registru imagí** v pracovním prostoru. Když vytvoříte obrázek, můžete zadat další značky metadat, abyste se mohli později dotazovat, aby si image našli.

Příklad vytvoření bitové kopie najdete [v tématu nasazení modelu klasifikace imagí v Azure Container Instances](tutorial-deploy-models-with-aml.md).

Příklad nasazení modelu pomocí vlastní image najdete v tématu [nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md).

### <a name="deployment"></a>Nasazení

Nasazení je vytvoření instance modelu do webové služby, kterou lze hostovat v cloudu nebo modulu IoT pro nasazení integrovaných zařízení.

#### <a name="web-service-deployments"></a>Nasazení webových služeb

Nasazená webová služba může používat Azure Container Instances, službu Azure Kubernetes nebo FPGA. Službu vytvoříte z modelu, skriptu a přidružených souborů. Tyto jsou zapouzdřeny v imagi, což poskytuje prostředí pro dobu běhu webové služby. Image má koncový bod HTTP s vyrovnáváním zatížení, který přijímá požadavky na bodování, které se odesílají do webové služby.

Azure vám pomůže monitorovat nasazení webové služby shromažďováním Application Insights telemetrie nebo telemetrie modelů, pokud jste se rozhodli tuto funkci povolit. Data telemetrie jsou dostupná jenom pro vás a ukládají se do vašich Application Insights a instancí účtů úložiště.

Pokud jste povolili automatické škálování, Azure automaticky škáluje vaše nasazení.

Příklad nasazení modelu jako webové služby najdete [v tématu nasazení modelu klasifikace imagí v Azure Container Instances](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-deployments"></a>Nasazení modulu IoT

Nasazený modul IoT je kontejner Docker, který obsahuje váš model a přidružený skript nebo aplikaci a všechny další závislosti. Tyto moduly nasadíte pomocí Azure IoT Edge na hraničních zařízeních.

Pokud jste povolili monitorování, Azure shromáždí data telemetrie z modelu uvnitř modulu Azure IoT Edge. Data telemetrie jsou dostupná jenom pro vás a ukládají se do vaší instance účtu úložiště.

Azure IoT Edge zajistí, že je váš modul spuštěný, a monitoruje zařízení, které ho hostuje.

### <a name="ml-pipelines"></a>Kanály ML

Pomocí kanálů strojového učení můžete vytvářet a spravovat pracovní postupy, které dohromady spojí fáze strojového učení. Kanál může například zahrnovat přípravu dat, školení modelů, nasazení modelu a fáze odvození a bodování. Jednotlivé fáze může zahrnovat několik kroků, z nichž každý lze spustit bezobslužně v různých cílových výpočetních prostředí. 

Kroky kanálu jsou opakovaně použitelné a je možné je spustit bez nutnosti znovu spustit následné kroky, pokud se výstup tohoto kroku nezměnil. V případě, že se data nezměnila, můžete například přeškolit model bez nutnosti znovu spustit nákladný postup přípravy dat. Kanály také umožňují pracovníkům dat spolupracovat při práci na samostatných oblastech pracovního postupu Machine Learning.

Další informace o kanálech strojového učení s touto službou najdete v tématu [kanály a Azure Machine Learning](concept-ml-pipelines.md).

### <a name="logging"></a>Protokolování

Při vývoji řešení použijte sadu Azure Machine Learning Python SDK ve vašem skriptu Pythonu k protokolování libovolných metrik. Po spuštění dotazu na metriky určete, zda běh vytvořil model, který chcete nasadit.

### <a name="next-steps"></a>Další kroky

Pokud chcete začít používat službu Azure Machine Learning, přečtěte si:

* [Co je služba Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Vytvořit pracovní prostor služby Azure Machine Learning](how-to-manage-workspace.md)
* [Kurz (část 1): Výuka modelu](tutorial-train-models-with-aml.md)
