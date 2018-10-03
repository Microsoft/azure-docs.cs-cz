---
title: Jak funguje služba Azure Machine Learning?
description: Další informace o architektuře, terminologie a koncepty, které tvoří službu Azure Machine Learning. Dozvíte se taky o obecný pracovní postup použití služby a služby Azure, které používá služba Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: 64104fc70c7be1589c9332905f243a2e1e692eee
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237972"
---
# <a name="architecture-and-concepts-how-does-azure-machine-learning-service-work"></a>Architektura a koncepty: Jak funguje služba Azure Machine Learning? 

Tento dokument popisuje architekturu a koncepty pro službu Azure Machine Learning. Následující diagram znázorňuje hlavní součásti služby a ilustruje obecný pracovní postup, při použití služby: 

[![Architektura služby Machine Learning a pracovní postup pro Azure](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

Pracovní postup obvykle zahrnuje následující kroky:

1. Vývoj strojového učení trénovací skripty v __Python__.
1. Vytvoření a konfigurace __cílové výpočetní prostředí__.
1. __Odeslat skripty__ do cílového výpočetního nakonfigurované prostředí pro spuštění v daném prostředí. Během cvičení, ukládá cílové výpočetní prostředí spouštění záznamy __datastore__. Zde se záznamy ukládají do __experimentovat__.
1. __Dotazování experiment__ pro metrikách zaznamenaných do protokolu běhů aktuálního i staršího. Pokud metriky nevyplývá požadovaného výsledku, smyčka zpátky ke kroku 1 a iterovat své skripty.
1. Po nalezení uspokojivé spustit zaregistrujte model trvalého v __modelu registru__.
1. Vyvíjejte hodnoticí skript.
1. __Vytvoření Image__ a zaregistrujte ho __registru imagí__. 
1. __Nasazení bitové kopie__ jako __webová služba__ v Azure.


[!INCLUDE [aml-preview-note](../../../includes/aml-preview-note.md)]

> [!NOTE]
> Když tento dokument definuje podmínky a koncepty používané službou Azure Machine Learning, nedefinuje terminologie a koncepty pro platformu Azure. Další informace o terminologii platformy Azure najdete v článku [Glosář Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a>Pracovní prostor

Pracovní prostor je prostředek nejvyšší úrovně pro službu Azure Machine Learning. Poskytuje centrální místo, kde můžete pracovat s všechny artefakty, které vytvoříte, při použití služby Azure Machine Learning.

Pracovním prostoru udržuje seznam cílových výpočetních prostředí, které lze použít k natrénování modelu. Také udržuje historii tréninkových spuštění, včetně protokolů, metrik, výstupu a snímek vašich skriptů. Tyto informace slouží k určení, které školení vznikly nejlepší model.

Modely jsou registrované s pracovním prostorem. Registrovanému modelu a hodnoticí skripty slouží k vytvoření image. Na obrázku je pak možné nasadit do Azure Container Instances, Azure Kubernetes Service nebo do pole programmable gate array (FPGA) jako koncový bod HTTP založené na protokolu REST. Se můžete také nasadit do zařízení s Azure IoT Edge jako modul.

Můžete vytvořit víc pracovních prostorů a každý pracovní prostor může být sdílen více lidí. Při sdílení pracovního prostoru, řízení přístupu k pracovnímu prostoru tak, že uživatelům přiřadíte následující role:

* Vlastník
* Přispěvatel
* Čtenář

Když vytvoříte nový pracovní prostor, automaticky vytvoří několik prostředků Azure, které se používají v pracovním prostoru:

* [Služba Azure Container Registry](https://azure.microsoft.com/services/container-registry/) -registry kontejnerů dockeru, které se používají během cvičení a při nasazení modelu.
* [Azure Storage](https://azure.microsoft.com/services/storage/) – používá se jako výchozí úložiště dat pro pracovní prostor.
* [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) – ukládá informace o vašich modelů monitorování.
* [Služba Azure Key Vault](https://azure.microsoft.com/services/key-vault/) – úložiště tajných kódů používaných cílových výpočetních prostředí a dalších citlivých údajů vyžadované pracovního prostoru.

> [!NOTE]
> Místo vytváření nové verze, můžete také použít stávající služby Azure. 

Následující diagram není moc taxonomie pracovního prostoru:

[![Taxonomie pracovního prostoru](./media/concept-azure-machine-learning-architecture/taxonomy.png)](./media/concept-azure-machine-learning-architecture/taxonomy.png#lightbox)

## <a name="model"></a>Model

V nejjednodušším model je část kódu, která přijímá vstup a výstup. Vytvoření modelu strojového učení zahrnuje výběr algoritmus, poskytuje s daty a ladění hyperparameters. Školení je iterativní proces, který vytváří trénovaného modelu, který zapouzdřuje modelu osvojené během procesu trénování.

Model je produkovaný spustit ve službě Azure Machine Learning. Můžete také použít modelů trénovaných mimo Azure Machine Learning. Model lze zaregistrovat v části pracovní prostor služby Azure Machine Learning service.

Služba Azure Machine Learning je nezávislý na rozhraní framework. Můžete použít jakoukoli architekturu learningu oblíbených počítač při vytváření modelu, jako je například scikit-informace xgboost PyTorch, TensorFlow, Chainer a CNTK.

Příklad trénování modelu, najdete v článku [rychlý start: vytvoření služby machine learning pracovního prostoru služby](quickstart-get-started.md) dokumentu.

### <a name="model-registry"></a>Model registru

Model registru uchovává informace o všech modelů v pracovním prostoru služby Azure Machine Learning. 

Modely jsou identifikovány názvem a verzí. Pokaždé, když zaregistrujete model se stejným názvem jako existující registru se předpokládá, že se jedná o novou verzi. Verze je zvýšen a nový model je zaregistrovaný pod názvem.

Při registraci modelu a poté použít tyto značky při hledání modely můžete zadat další metadata značky.

Nelze odstranit modely, které se používají v obrázku.

Příklad registrace modelu, najdete v článku [trénování modelu klasifikace obrázků s Azure Machine Learning](tutorial-train-models-with-aml.md) dokumentu.

## <a name="image"></a>Image

Bitové kopie poskytnout způsob, jak spolehlivě nasadit model, spolu s všechny součásti potřebné k použití modelu. Bitová kopie obsahuje následující položky:

* Model.
* Hodnoticí skript nebo aplikace. Tento skript slouží k předání vstupu do modelu a vrátí výstup modelu.
* Potřebné závislosti pro model nebo hodnoticího skriptu a aplikace.  Můžete například zahrnout soubor prostředí Conda, který obsahuje závislosti balíčků Python.

Existují dva typy imagí, které je možné vytvořit pomocí Azure Machine Learning:

* Obrázek FPGA: použít při nasazování do pole programmable gate array v cloudu Azure.
* Image dockeru: slouží k výpočtu cíle než FPGA při nasazování. Například Azure Container Instances a Azure Kubernetes Service.

Příklad vytvoření image, najdete v článku [nasadit model klasifikace image v Azure Container Instance](tutorial-deploy-models-with-aml.md) dokumentu.

### <a name="image-registry"></a>Registru imagí

Obrázky vytvořené z vašich modelů uchovává informace o registru imagí. Při vytváření bitové kopie můžete zadat další metadata značky. Jsou uložené v registru image a značky metadat může být dotazována k vyhledání bitové kopie.

## <a name="deployment"></a>Nasazení

Nasazení je instance svou image do obou webovou službu, která může být hostovaná v cloudu nebo modul pro nasazení integrovaných zařízení služby IoT. 

### <a name="web-service"></a>Webová služba

Nasazenou webovou službu můžete použít Azure Container Instances, Azure Kubernetes Service nebo pole programmable gate Array (FPGA).
Vytvoření služby z obrázku, který zapouzdřuje modelu, skripty a přidružené soubory. Bitová kopie je vyrovnáváním zatížení, koncový bod HTTP, která přijímá bodování požadavky odeslané na webové službě.

Azure pomáhá monitorovat nasazení vaší webové služby pomocí shromažďování telemetrie Application Insights a/nebo telemetrická data modelu, pokud jste vybrali k povolení této funkce. Telemetrická data přístupná jenom pro vás a uložená v Application Insights a instance účtu úložiště.

Pokud jste povolili automatické škálování, Azure automaticky škálovat vaše nasazení.

Příklad nasazení modelu jako webové služby, najdete v článku [nasadit model klasifikace image v Azure Container Instance](tutorial-deploy-models-with-aml.md) dokumentu.

### <a name="iot-module"></a>Modul IoT

Nasazené modulu IoT je kontejner Dockeru obsahující váš model a přidruženého skriptovacího nebo aplikace a všechny další závislosti. Tyto moduly jsou nasazeny pomocí Azure IoT Edge na hraničních zařízeních. 

Pokud povolíte monitorování Azure shromažďuje telemetrická data z modelu uvnitř modulu Azure IoT Edge. Telemetrická data přístupná jenom pro vás a uložené v instanci účtu úložiště.

Azure IoT Edge se ujistěte, že modul běží a monitorovat zařízení, který je hostitelem.

## <a name="datastore"></a>Úložiště dat

Úložiště dat je úložiště abstrakce přes účet úložiště Azure. Úložiště můžete použít ke kontejneru objektů blob v Azure nebo sdílené složky Azure jako back-endového úložiště. Každý pracovní prostor má výchozí úložiště dat a si můžete zaregistrovat další úložiště. 

Použití rozhraní API Python SDK nebo rozhraní příkazového řádku pro Azure Machine Learning k ukládání a načítání souborů z úložiště. 

## <a name="run"></a>Spusťte

Spuštění je záznam, který obsahuje následující informace:

* Metadata o spuštění (časové razítko, dobu trvání atd.)
* Metriky, které jsou zapsané podle vašeho skriptu
* Výstupní soubory automaticky shromažďovaných experiment, nebo explicitně nahrát sami.
* Snímek adresáře, který obsahuje vaše skripty před spuštění

Spuštění je vytvořen, když odešlete skript pro trénování modelu. Spuštění může mít nula nebo více podřízených spuštění. Takže nejvyšší úrovně spuštění může mít dvě podřízené spuštění, z nichž každá může mít vlastní podřízená úloha poběží.

Příklad zobrazení spouští vyprodukované díky trénování modelu, najdete v článku [rychlý start: Začínáme se službou Azure Machine Learning](quickstart-get-started.md) dokumentu.

## <a name="experiment"></a>Experiment

Experiment je seskupení mnoho spuštění z daného skriptu. Vždy patří do pracovního prostoru. Když odešlete spustit, zadejte název experimentu. Informace pro spuštění jsou uloženy v rámci testu. Pokud odešlete spustit a zadejte název experimentu, který neexistuje, je automaticky vytvoří nový experiment s tímto názvem.

Příklad použití experiment, najdete v článku [rychlý start: Začínáme se službou Azure Machine Learning](quickstart-get-started.md) dokumentu.

## <a name="compute-target"></a>Cílové výpočetní prostředí

Cílové výpočetní prostředí je výpočetní prostředek, který používá ke spuštění trénovací skript nebo hostovat vaše nasazení webové služby. Cílových podporovaných výpočetních prostředí jsou: 

* Místního počítače
* Virtuální počítač s Linuxem v Azure (například virtuální počítač pro datové vědy)
* Cluster služby Azure Batch AI
* Apache Spark pro HDInsight
* Instance kontejneru Azure
* Azure Kubernetes Service

Cílových výpočetních prostředí jsou připojeny k pracovnímu prostoru. Výpočetní cíle než v místním počítači sdílejí uživatelé pracovního prostoru.

Většina výpočetní cíle je možné vytvářet přímo přes pracovní prostor pomocí webu Azure portal, Azure Machine Learning SDK nebo rozhraní příkazového řádku Azure. Pokud máte cílových výpočetních prostředí, které byly vytvořeny jiným procesem (například webu Azure portal nebo rozhraní příkazového řádku Azure), můžete přidat (připojit) je do pracovního prostoru. Některé výpočetní cíle musí být vytvořená mimo pracovní prostor a potom připojit.

Další informace o výběru cílové výpočetní prostředí pro školení, najdete v článku [výběr a použití cílové výpočetní prostředí k natrénování modelu](how-to-set-up-training-targets.md) dokumentu.

Další informace o výběru cílové výpočetní prostředí pro nasazení, najdete v článku [nasazujte modely pomocí služby Azure Machine Learning](how-to-deploy-and-where.md) dokumentu.

## <a name="run-configuration"></a>Konfigurace spuštění

Konfigurace spuštění je sada instrukcí, který definuje, jak spustit skript v cílové výpočetní prostředí daného. Zahrnuje celou sadu definic chování, například, jestli se má použít existující prostředí Python nebo prostředí Conda od specifikace.

Konfigurace spuštění můžete nastavit jako trvalý, do souboru v adresáři, který obsahuje skript školení, nebo vytvořen jako objekt v paměti a použít k odeslání spuštění.

Příklad konfigurace spuštění, najdete v článku [výběr a použití cílové výpočetní prostředí k natrénování modelu](how-to-set-up-training-targets.md) dokumentu.

## <a name="training-script"></a>Cvičný skript

Pro trénování modelu, určíte adresář, který obsahuje skript školení a přidružené soubory. Můžete také zadat název experimentu, který se používá k uložení informace shromážděné během cvičení. Při školení, celý adresář se zkopíruje do prostředí pro školení (cílové výpočetní prostředí) a spuštění skriptu. určené konfigurace spuštění. Snímek adresáře je také uložen v rámci testu v pracovním prostoru.

Příklad použití skriptů k natrénování modelu, naleznete v tématu [vytvořit pracovní prostor s využitím Pythonu](quickstart-get-started.md)

## <a name="logging"></a>Protokolování

Při vývoji řešení, protokolovat libovolné metriky pomocí sady Python SDK Azure Machine Learning ve svém skriptu Pythonu. Po spuštění dotaz metriky k určení, pokud spuštění vytváří model, který chcete nasadit. 

## <a name="snapshot"></a>Snímek

Při odesílání spuštění, Azure Machine Learning komprimuje adresáře, který obsahuje skript jako soubor zip a odesílá je do cílového výpočetního prostředí. Je pak rozšířen souboru zip a existuje spuštění skriptu. Azure Machine Learning také ukládá soubor zip jako snímek jako součást spuštění záznamu. Každý, kdo má přístup k pracovním prostoru můžete procházet záznam spuštění a stáhnout snímek.

## <a name="activity"></a>Aktivita

Aktivita představuje dlouhotrvající operace. Příklady aktivit jsou následující operace:

* Vytvoření nebo odstranění cílové výpočetní prostředí
* Spuštění skriptu na cílové výpočetní prostředí

Aktivity může poskytovat oznámení prostřednictvím sady SDK nebo webového uživatelského rozhraní, tak můžou snadno monitorovat průběh těchto operací.

## <a name="next-steps"></a>Další postup

Abyste mohli začít používat Azure Machine Learning pomocí následujících odkazů:

* [Co je služba Azure Machine Learning](overview-what-is-azure-ml.md)
* [Rychlý start: Vytvoření pracovního prostoru s využitím Pythonu](quickstart-get-started.md)
* [Kurz: Trénování modelu](tutorial-train-models-with-aml.md)
