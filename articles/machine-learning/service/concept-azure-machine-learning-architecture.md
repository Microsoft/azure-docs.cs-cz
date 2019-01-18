---
title: 'Strojové učení v cloudu: Podmínky a architektura'
titleSuffix: Azure Machine Learning service
description: Další informace o architektuře, terminologie a koncepty, které tvoří službu Azure Machine Learning. Dozvíte se taky o obecný pracovní postup použití služby a služby Azure, které používá služba Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: larryfr
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: e5f569d5561050c9993ec48b096f60595d44b1b1
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54391175"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Fungování služby Azure Machine Learning: Architektura a koncepty

Tento článek popisuje architekturu a koncepty pro službu Azure Machine Learning. Hlavní součástí služby a obecný pracovní postup pro používání služby jsou uvedeny v následujícím diagramu: 

[![Architektura služby Azure Machine Learning a pracovní postup](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

Pracovní postup probíhá obecně toto pořadí:

1. Vývoj strojového učení trénovací skripty v **Python**.
1. Vytvoření a konfigurace **cílové výpočetní prostředí**.
1. **Odeslat skripty** do cílového výpočetního nakonfigurované prostředí pro spuštění v daném prostředí. Při školení, může číst nebo zapisovat do skriptů **datastore**. A záznamy o spuštění se uložit jako **spustí** v **pracovní prostor**, který se rozdělil přes **experimentovat**.
1. **Dotazování experiment** pro metrikách zaznamenaných do protokolu běhů aktuálního i staršího. Pokud metriky neindikují požadovaného výsledku, smyčka zpátky ke kroku 1 a iterovat své skripty.
1. Po uspokojivé spustit je najít, zaregistrujte model trvalého v **modelu registru**.
1. Vyvíjejte hodnoticí skript.
1. **Vytvoření image** a zaregistrujte ho **registru imagí**. 
1. **Nasazení bitové kopie** jako **webová služba** v Azure.


> [!NOTE]
> Přestože tento článek definuje podmínky a koncepty používané službou Azure Machine Learning, nedefinuje terminologie a koncepty pro platformu Azure. Další informace o terminologii platformy Azure najdete v tématu [Glosář Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a>Pracovní prostor

Pracovní prostor je prostředek nejvyšší úrovně pro službu Azure Machine Learning. Poskytuje centrální místo, kde můžete pracovat s všechny artefakty, které vytvoříte, když používáte službu Azure Machine Learning.

Pracovním prostoru udržuje seznam cílových výpočetních prostředí, které použijete k natrénování modelu. Také udržuje historii tréninkových spuštění, včetně protokolů, metrik, výstupu a snímek vašich skriptů. Tyto informace použít k určení, které školení vznikly nejlepší model.

Zaregistrujte modely s pracovním prostorem. Použijete k vytvoření image registrovanému modelu a vyhodnocení skriptů. Pak můžete nasadit image do služby Azure Container Instances, Azure Kubernetes Service, nebo pole programmable gate array (FPGA) jako koncový bod HTTP založené na protokolu REST. Můžete taky nasadit bitovou kopii pro zařízení Azure IoT Edge jako modul.

Můžete vytvořit víc pracovních prostorů a každý pracovní prostor může být sdílen více lidí. Když sdílíte s pracovním prostorem, můžete řídit přístup k němu tak, že uživatelům přiřadíte následující role:

* Vlastník
* Přispěvatel
* Čtenář

Když vytvoříte nový pracovní prostor, automaticky vytvoří několik prostředků Azure, které se používají v pracovním prostoru:

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Registry kontejnerů dockeru, které použijete během cvičení a nasazení modelu.
* [Účet úložiště Azure](https://azure.microsoft.com/services/storage/): Slouží jako výchozí úložiště pro pracovní prostor.
* [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): Ukládá informace o vašich modelů monitorování.
* [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Tajné klíče úložiště, které jsou používány výpočetní cíle a dalších citlivých údajů, který je nezbytný v pracovním prostoru.

> [!NOTE]
> Kromě vytvoření nové verze, můžete také použít stávající služby Azure. 

Taxonomie pracovního prostoru je znázorněn v následujícím diagramu:

[![Taxonomie pracovního prostoru](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.svg)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

## <a name="model"></a>Model

V nejjednodušším model je část kódu, která přijímá vstup a výstup. Vytvoření modelu strojového učení zahrnuje výběr algoritmus, poskytuje s daty a ladění hyperparameters. Školení je iterativní proces, který vytváří trénovaného modelu, který zapouzdřuje modelu osvojené během procesu trénování.

Model je produkovaný spustit ve službě Azure Machine Learning. Můžete také použít model, který se trénuje mimo Azure Machine Learning. V pracovním prostoru služby Azure Machine Learning můžete zaregistrovat modelu.

Služba Azure Machine Learning je nezávislý na rozhraní framework. Při vytváření modelu, můžete použít jakoukoli architekturu learningu oblíbených počítačů, jako je například Scikit poučení, XGBoost, PyTorch, TensorFlow, Chainer a Microsoft Cognitive Toolkit (dříve označovaná jako CNTK).

Příklad trénování modelu najdete v tématu [rychlý start: Vytvořit pracovní prostor služby Machine Learning](quickstart-get-started.md).

### <a name="model-registry"></a>Model registru

Model registru uchovává informace o všech modelů v pracovním prostoru služby Azure Machine Learning. 

Modely jsou identifikovány názvem a verzí. Pokaždé, když zaregistrujete model se stejným názvem jako existující registru se předpokládá, že se jedná o novou verzi. Verze je zvýšen a nový model je zaregistrovaný pod stejným názvem.

Při registraci modelu můžete zadat další metadata značky a pak použít značky při hledání modely.

Nelze odstranit modely, které se používají v obrázku.

Příklad registrace modelu, naleznete v tématu [trénování modelu klasifikace obrázků s Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="image"></a>Image

Bitové kopie poskytnout způsob, jak spolehlivě nasadit model, spolu s všechny součásti nutné k použití modelu. Bitová kopie obsahuje následující položky:

* Model.
* Hodnoticí skript nebo aplikace. Pomocí skriptu předat vstup do modelu a vrátí výstup modelu.
* Závislosti, které jsou potřeba pro model nebo hodnoticí skript nebo aplikace. Můžete například zahrnout soubor prostředí Conda, který obsahuje závislosti balíčků Python.

Azure Machine Learning můžete vytvořit dva typy obrázků:

* **Obrázek FPGA**: Použít při nasazování do pole programmable gate array v Azure.
* **Image dockeru**: Použít při nasazení do jiné než FPGA cílových výpočetních prostředí. Příklady Azure Container Instances a Azure Kubernetes Service.

Příklad vytvoření image, najdete v části [nasadit model klasifikace obrázků ve službě Azure Container Instances](tutorial-deploy-models-with-aml.md).

### <a name="image-registry"></a>Registru imagí

Bitové kopie, které jsou vytvořeny z vašich modelů uchovává informace o registru imagí. Při vytvoření bitové kopie můžete zadat další metadata značky. Značky metadat jsou uložené v registru image a můžete dát dotaz na nich najít image.

## <a name="deployment"></a>Nasazení

Nasazení je instance svou image do obou webovou službu, která je možné hostovat v cloudu nebo modul pro nasazení integrovaných zařízení služby IoT. 

### <a name="web-service"></a>Webová služba

Azure Container Instances, Azure Kubernetes Service nebo FPGA, můžete použít nasazenou webovou službu. Vytvoříte službu z obrázku, který zapouzdřuje modelu, skripty a přidružené soubory. Bitová kopie je vyrovnáváním zatížení, koncový bod HTTP, která přijímá vyhodnocování požadavků, které se odesílají do webové služby.

Azure pomáhá monitorovat nasazení vaší webové služby pomocí shromažďování telemetrie Application Insights nebo telemetrická data modelu, pokud jste se rozhodli tuto funkci povolil. Telemetrická data jsou přístupná pouze pro vás a je uložena v Application Insights a instance účtu úložiště.

Pokud jste povolili automatické škálování, Azure automaticky změní velikost vašeho nasazení.

Příklad nasazení modelu jako webové služby najdete v tématu [nasadit model klasifikace obrázků ve službě Azure Container Instances](tutorial-deploy-models-with-aml.md).

### <a name="iot-module"></a>Modul IoT

Nasazené modulu IoT je kontejner Dockeru obsahující váš model a přidruženého skriptovacího nebo aplikace a všechny další závislosti. Nasaďte tyto moduly s použitím Azure IoT Edge na hraničních zařízeních. 

Pokud jste povolili monitorování, Azure shromažďuje telemetrická data z modelu uvnitř modulu Azure IoT Edge. Telemetrická data jsou přístupná pouze pro vás a je uložené v instanci účtu úložiště.

Azure IoT Edge zajišťuje, že je spuštěn modul a monitoruje zařízení, který je hostitelem.

## <a name="datastore"></a>Úložiště dat

Úložiště dat je úložiště abstrakce přes účet úložiště Azure. Úložiště můžete použít kontejner objektů blob v Azure nebo sdílené složky Azure jako úložiště back-end. Každý pracovní prostor má výchozí úložiště, a můžete zaregistrovat další úložiště. 

Pomocí rozhraní Python API sady SDK nebo rozhraní příkazového řádku Azure Machine Learning k ukládání a načítání souborů z úložiště. 

## <a name="run"></a>Spusťte

Spuštění je záznam, který obsahuje následující informace:

* Metadata o spuštění (časové razítko, dobu trvání a tak dále)
* Metriky, které jsou zapsané podle vašeho skriptu
* Výstupní soubory, které jsou autocollected podle testu nebo explicitně nahrát sami
* Snímek adresáře, který obsahuje vaše skripty před spuštění

Spuštění na základě když odešlete skript pro trénování modelu. Spuštění může mít nula nebo více podřízených spuštění. Například běh nejvyšší úrovně může mít dvě podřízené spuštění, z nichž každá může mít svůj vlastní podřízený prvek spustit.

Příklad zobrazení spuštění, které vytváří trénování modelu, naleznete v tématu [rychlý start: Začínáme se službou Azure Machine Learning](quickstart-get-started.md).

## <a name="experiment"></a>Experiment

Experiment je seskupení mnoho spuštění ze zadaného skriptu. Vždy patří do pracovního prostoru. Když odešlete spustit, zadejte název experimentu. Informace pro spuštění jsou uloženy v rámci testu. Pokud odešlete spustit a zadejte název experimentu, který neexistuje, je automaticky vytvoří nový experiment s nově zadaným názvem.

Příklad použití experiment, naleznete v tématu [rychlý start: Začínáme se službou Azure Machine Learning](quickstart-get-started.md).

## <a name="pipeline"></a>Kanál

Pomocí machine learning kanály, abyste mohli vytvářet a spravovat pracovní postupy, které spojit dohromady machine learning fází. Kanál může obsahovat například přípravy dat, trénování modelu, model nasazení a odvozování fází. Jednotlivé fáze může zahrnovat několik kroků, z nichž každý lze spustit bezobslužně v různých cílových výpočetních prostředí.

Další informace o machine learning kanály pomocí této služby najdete v tématu [kanály a Azure Machine Learning](concept-ml-pipelines.md).

## <a name="compute-target"></a>Cílové výpočetní prostředí

Cílové výpočetní prostředí je výpočetní prostředek, který používáte ke spuštění trénovací skript nebo hostovat vaše nasazení služby. Cílových podporovaných výpočetních prostředí jsou: 

| Cílové výpočetní prostředí | Školení | Nasazení |
| ---- |:----:|:----:|
| Místního počítače | ✓ | &nbsp; |
| Azure Machine Learning compute | ✓ | &nbsp; |
| Virtuální počítač s Linuxem v Azure</br>(například virtuální počítač pro datové vědy) | ✓ | &nbsp; |
| Azure Databricks | ✓ | &nbsp; | &nbsp; |
| Azure Data Lake Analytics | ✓ | &nbsp; |
| Apache Spark pro HDInsight | ✓ | &nbsp; |
| Azure Container Instances | &nbsp; | ✓ |
| Azure Kubernetes Service | &nbsp; | ✓ |
| Azure IoT Edge | &nbsp; | ✓ |
| Project Brainwave</br>(Field-programmable gate array) | &nbsp; | ✓ |

Cílových výpočetních prostředí jsou připojeny k pracovnímu prostoru. Výpočetní cíle než v místním počítači sdílejí uživatelé pracovního prostoru.

### <a name="managed-and-unmanaged-compute-targets"></a>Cílových výpočetních prostředí spravované a nespravované

* **Spravované**: Cíle, které jsou vytvořeny a spravované službou Azure Machine Learning COMPUTE. Tyto výpočetní cíle jsou optimalizované pro machine learning úlohy. Výpočetní prostředky Azure Machine Learning je jediná cílové výpočetní prostředí spravované od 4. prosince 2018. V budoucnu lze přidat další spravované výpočetní cíle. 

    Můžete vytvořit machine learningu výpočetní instance přímo prostřednictvím pracovního prostoru pomocí webu Azure portal, Azure Machine Learning SDK nebo rozhraní příkazového řádku Azure. Všechny ostatní cílových výpočetních prostředí musí být vytvořená mimo pracovní prostor a pak k němu připojená.

* **Nespravované**: Cílových výpočetních prostředí, které jsou *není* spravované službou Azure Machine Learning. Potřebujete vytvořit mimo Azure Machine Learning a připojte je k vašemu pracovnímu prostoru před použitím. Cílových výpočetních prostředí nespravované může vyžadovat další kroky pro vás k údržbě nebo ke zlepšení výkonu pro úlohy s machine learning.

Informace o výběru cílové výpočetní prostředí pro školení, naleznete v tématu [výběr a použití cílové výpočetní prostředí k natrénování modelu](how-to-set-up-training-targets.md).

Informace o výběru cílové výpočetní prostředí pro nasazení, najdete v článku [nasazujte modely pomocí služby Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="run-configuration"></a>Konfigurace spuštění

Konfigurace spuštění je sada instrukcí, který definuje, jak spustit skript v cílové výpočetní prostředí zadané. Konfigurace zahrnuje celou sadu definic chování, například, zda chcete použít existující prostředí Python nebo používat prostředí Conda, která je vytvořená z specifikace.

Konfigurace spuštění můžete nastavit jako trvalý, do souboru do adresáře, který obsahuje cvičný skript, nebo může být vytvořen jako objekt v paměti a použít k odeslání spuštění.

Příklad konfigurace spuštění, naleznete v tématu [výběr a použití cílové výpočetní prostředí k natrénování modelu](how-to-set-up-training-targets.md).

## <a name="training-script"></a>Cvičný skript

Pro trénování modelu, určíte adresář, který obsahuje skript školení a přidružené soubory. Můžete také zadat název experimentu, který se používá k ukládání informací, která se nashromáždí během cvičení. Při školení, celý adresář se zkopíruje do prostředí pro školení (cílové výpočetní prostředí) a spuštění skriptu, který je určen podle konfigurace spuštění. Snímek adresáře je také uložen v rámci testu v pracovním prostoru.

Příklad najdete v tématu [vytvořit pracovní prostor s Pythonem](quickstart-get-started.md).

## <a name="logging"></a>Protokolování

Při vývoji řešení protokolovat libovolné metriky pomocí sady Python SDK Azure Machine Learning ve svém skriptu Pythonu. Po spuštění dotaz metriky k určení, zda má spustit vytvořený model, který chcete nasadit. 

## <a name="snapshot"></a>Snímek

Když odešlete spuštění, Azure Machine Learning komprimuje adresáře, který obsahuje skript jako soubor zip a odesílá je do cílového výpočetního prostředí. Soubor zip se pak extrahuje a existuje spuštění skriptu. Azure Machine Learning také ukládá soubor zip jako snímek jako součást spuštění záznamu. Každý, kdo má přístup k pracovním prostoru můžete procházet záznam spuštění a stáhnout snímek.

## <a name="activity"></a>Aktivita

Aktivita představuje dlouhotrvající operace. Příklady aktivit jsou následující operace:

* Vytvoření nebo odstranění cílové výpočetní prostředí
* Spuštění skriptu na cílové výpočetní prostředí

Aktivity může poskytnout oznámení prostřednictvím sady SDK nebo ve webovém uživatelském rozhraní, takže můžete snadno sledovat průběh těchto operací.

## <a name="next-steps"></a>Další postup

Abyste mohli začít se službou Azure Machine Learning, naleznete v tématu:

* [Co je služba Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Rychlé zprovoznění: Vytvoření pracovního prostoru s využitím Pythonu](quickstart-get-started.md)
* [Kurz: Trénování modelu](tutorial-train-models-with-aml.md)
