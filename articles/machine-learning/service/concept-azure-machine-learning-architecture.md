---
title: Architektura a klíčové koncepty
titleSuffix: Azure Machine Learning service
description: Další informace o architektuře, podmínky, koncepty a pracovní postup, který tvoří služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/15/2019
ms.custom: seodec18
ms.openlocfilehash: f369f899d4a383205ad124e4fcd8dabf9f92f63f
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753189"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Fungování služby Azure Machine Learning: Architektura a koncepty

Další informace o architektuře, koncepty a pracovních postupů pro službu Azure Machine Learning. Hlavní součástí služby a obecný pracovní postup pro používání služby jsou uvedeny v následujícím diagramu:

[![Architektura služby Azure Machine Learning a pracovní postup](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

## <a name="workflow"></a>Pracovní postup

Informace o pracovním postupu počítače následující obecně toto pořadí:

1. Vývoj strojového učení trénovací skripty v **Python** nebo vizuální rozhraní.
1. Vytvoření a konfigurace **cílové výpočetní prostředí**.
1. **Odeslat skripty** do cílového výpočetního nakonfigurované prostředí pro spuštění v daném prostředí. Při školení, může číst nebo zapisovat do skriptů **datastore**. A záznamy o spuštění se uloží jako **spustí** v **pracovní prostor** seskupené pod **experimenty**.
1. **Dotazování experiment** pro metrikách zaznamenaných do protokolu běhů aktuálního i staršího. Pokud metriky neindikují požadovaného výsledku, smyčka zpátky ke kroku 1 a iterovat své skripty.
1. Po uspokojivé spustit je najít, zaregistrujte model trvalého v **modelu registru**.
1. Vývoj hodnoticí skript, který používá model a **model nasadit** jako **webovou službu** v Azure nebo do **zařízení IoT Edge**.

Provedením těchto kroků s žádným z následujících akcí:
+ [Azure Machine Learning sady SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)
+ [Rozšíření Azure Machine Learning VS Code](how-to-vscode-tools.md)
+  [Vizuální rozhraní pro službu Azure Machine Learning (preview)](ui-concept-visual-interface.md)

> [!NOTE]
> Přestože tento článek definuje podmínky a koncepty používané službou Azure Machine Learning, nedefinuje terminologie a koncepty pro platformu Azure. Další informace o terminologii platformy Azure najdete v tématu [Glosář Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a>Pracovní prostor

[Pracovní prostor](concept-workspace.md) je prostředek nejvyšší úrovně pro službu Azure Machine Learning. Poskytuje centrální místo, kde můžete pracovat s všechny artefakty, které vytvoříte, když používáte službu Azure Machine Learning.

Taxonomie pracovního prostoru je znázorněn v následujícím diagramu:

[![Taxonomie pracovního prostoru](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Další informace o pracovních prostorech naleznete v tématu [co je pracovní prostor služby Azure Machine Learning?](concept-workspace.md).

## <a name="experiment"></a>Experiment

Experiment je seskupení mnoho spuštění ze zadaného skriptu. Vždy patří do pracovního prostoru. Když odešlete spustit, zadejte název experimentu. Informace pro spuštění jsou uloženy v rámci testu. Pokud odešlete spustit a zadejte název experimentu, který neexistuje, je automaticky vytvoří nový experiment s nově zadaným názvem.

Příklad použití experiment, naleznete v tématu [rychlý start: Začínáme se službou Azure Machine Learning](quickstart-run-cloud-notebook.md).

## <a name="model"></a>Model

V nejjednodušším model je část kódu, která přijímá vstup a výstup. Vytvoření modelu strojového učení zahrnuje výběr algoritmus, poskytuje s daty a ladění hyperparameters. Školení je iterativní proces, který vytváří trénovaného modelu, který zapouzdřuje modelu osvojené během procesu trénování.

Model je produkovaný spustit ve službě Azure Machine Learning. Můžete také použít model, který se trénuje mimo Azure Machine Learning. V pracovním prostoru služby Azure Machine Learning můžete zaregistrovat modelu.

Služba Azure Machine Learning je nezávislý na rozhraní framework. Při vytváření modelu, můžete použít jakoukoli architekturu learningu oblíbených počítačů, jako je například Scikit poučení, XGBoost, PyTorch, TensorFlow a Chainer.

Příklad trénování modelu najdete v tématu [kurzu: Trénování modelu klasifikace obrázků pomocí služby Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="model-registry"></a>Model registru

Model registru uchovává informace o všech modelů v pracovním prostoru služby Azure Machine Learning.

Modely jsou identifikovány názvem a verzí. Pokaždé, když zaregistrujete model se stejným názvem jako existující registru se předpokládá, že se jedná o novou verzi. Verze je zvýšen a nový model je zaregistrovaný pod stejným názvem.

Při registraci modelu můžete zadat další metadata značky a pak použít značky při hledání modely.

Nelze odstranit modely, které jsou používány aktivní nasazení.

Příklad registrace modelu, naleznete v tématu [trénování modelu klasifikace obrázků s Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="run-configuration"></a>Konfigurace spuštění

Konfigurace spuštění je sada instrukcí, který definuje, jak spustit skript v cílové výpočetní prostředí zadané. Konfigurace zahrnuje celou sadu definic chování, například, zda chcete použít existující prostředí Python nebo používat prostředí Conda, která je vytvořená z specifikace.

Konfigurace spuštění můžete nastavit jako trvalý, do souboru do adresáře, který obsahuje cvičný skript, nebo může být vytvořen jako objekt v paměti a použít k odeslání spuštění.

Příklad konfigurace spuštění, naleznete v tématu [výběr a použití cílové výpočetní prostředí k natrénování modelu](how-to-set-up-training-targets.md).

## <a name="dataset"></a>Datová sada

Sady Azure Machine Learning dat (preview) usnadňují přístup a práce s daty. Datové sady spravovat data v různých scénářích, jako je například trénování modelu a vytváření kanálu. Pomocí sady SDK služby Azure Machine Learning, můžete přístup k podkladové úložiště, prozkoumejte a připravit data, spravovat životní cyklus různých definicích datových sad a porovnání mezi datové sady použité v školení a v produkčním prostředí.

Datové sady, poskytuje metody pro práci s daty v oblíbených formátů, jako je třeba použití `from_delimited_files()` nebo `to_pandas_dataframe()`.

Další informace najdete v tématu [vytvořit a zaregistrovat datové sady Azure Machine Learning](how-to-create-register-datasets.md).

Příklad použití datových sad, najdete v článku [ukázkové poznámkové bloky](https://aka.ms/dataset-tutorial).

## <a name="datastore"></a>Úložiště dat

Úložiště dat je úložiště abstrakce přes účet úložiště Azure. Úložiště můžete použít kontejner objektů blob v Azure nebo sdílené složky Azure jako úložiště back-end. Každý pracovní prostor má výchozí úložiště, a můžete zaregistrovat další úložiště.

Pomocí rozhraní Python API sady SDK nebo rozhraní příkazového řádku Azure Machine Learning k ukládání a načítání souborů z úložiště.

## <a name="compute-target"></a>Cílové výpočetní prostředí

A [cílové výpočetní prostředí](concept-compute-target.md) vám umožňují určit výpočetních prostředků, ve kterém jste spustili cvičný skript nebo hostitele vašeho nasazení služby. Toto umístění může být místního počítače nebo cloudové výpočetní prostředek. Změna výpočetní prostředí bez úpravy kódu usnadňují cílových výpočetních prostředí. 

## <a name="training-script"></a>Cvičný skript

Pro trénování modelu, určíte adresář, který obsahuje skript školení a přidružené soubory. Můžete také zadat název experimentu, který se používá k ukládání informací, která se nashromáždí během cvičení. Při školení, celý adresář se zkopíruje do prostředí pro školení (cílové výpočetní prostředí) a spuštění skriptu, který je určen podle konfigurace spuštění. Snímek adresáře je také uložen v rámci testu v pracovním prostoru.

Příklad najdete v tématu [kurzu: Trénování modelu klasifikace obrázků pomocí služby Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="run"></a>Spusťte

Spuštění je záznam, který obsahuje následující informace:

* Metadata o spuštění (časové razítko, dobu trvání a tak dále)
* Metriky, které jsou zapsané podle vašeho skriptu
* Výstupní soubory, které jsou autocollected podle testu nebo explicitně nahrát sami
* Snímek adresáře, který obsahuje vaše skripty před spuštění

Spuštění na základě když odešlete skript pro trénování modelu. Spuštění může mít nula nebo více podřízených spuštění. Například běh nejvyšší úrovně může mít dvě podřízené spuštění, z nichž každá může mít svůj vlastní podřízený prvek spustit.

Příklad zobrazení spuštění, které vytváří trénování modelu, naleznete v tématu [rychlý start: Začínáme se službou Azure Machine Learning](quickstart-run-cloud-notebook.md).

## <a name="github-tracking-and-integration"></a>GitHub sledování a integrace

Při spuštění školení spustit, pokud je zdrojový adresář místního úložiště Git, informace o úložišti jsou uloženy v historii spuštění. Například aktuální ID potvrzení pro úložiště se zaznamená jako část historie. Tento postup funguje s spuštění odeslána pomocí odhad, ML kanálu nebo spustit skript. Funguje i pro spuštění z SDK nebo rozhraní příkazového řádku Machine Learning.

## <a name="snapshot"></a>Snímek

Když odešlete spuštění, Azure Machine Learning komprimuje adresáře, který obsahuje skript jako soubor zip a odesílá je do cílového výpočetního prostředí. Soubor zip se pak extrahuje a existuje spuštění skriptu. Azure Machine Learning také ukládá soubor zip jako snímek jako součást spuštění záznamu. Každý, kdo má přístup k pracovním prostoru můžete procházet záznam spuštění a stáhnout snímek.

> [!NOTE]
> Abyste zabránili zbytečné soubory nebudou zahrnuty do snímku, zkontrolujte soubor ignorovat (.gitignore nebo .amlignore). Umístit tento soubor v adresáři snímku a přidejte názvy souborů v ho. Soubor .amlignore používá stejný [syntaxi a vzory jako soubor .gitignore](https://git-scm.com/docs/gitignore). Pokud existují oba soubory, soubor .amlignore přednost.

## <a name="activity"></a>Aktivita

Aktivita představuje dlouhotrvající operace. Příklady aktivit jsou následující operace:

* Vytvoření nebo odstranění cílové výpočetní prostředí
* Spuštění skriptu na cílové výpočetní prostředí

Aktivity může poskytnout oznámení prostřednictvím sady SDK nebo ve webovém uživatelském rozhraní, takže můžete snadno sledovat průběh těchto operací.

## <a name="image"></a>Image

Bitové kopie poskytnout způsob, jak spolehlivě nasadit model, spolu s všechny součásti nutné k použití modelu. Bitová kopie obsahuje následující položky:

* Model.
* Hodnoticí skript nebo aplikace. Pomocí skriptu předat vstup do modelu a vrátí výstup modelu.
* Závislosti, které jsou potřeba pro model nebo hodnoticí skript nebo aplikace. Můžete například zahrnout soubor prostředí Conda, který obsahuje závislosti balíčků Python.

Azure Machine Learning můžete vytvořit dva typy obrázků:

* **Obrázek FPGA**: Použít při nasazování do pole programmable gate array v Azure.
* **Image dockeru**: Použít při nasazení do jiné než FPGA cílových výpočetních prostředí. Příklady Azure Container Instances a Azure Kubernetes Service.

Služba Azure Machine Learning poskytuje základní image, který se používá ve výchozím nastavení. Můžete taky zadat vlastní Image.

Příklad vytvoření image, najdete v části [nasadit model klasifikace obrázků ve službě Azure Container Instances](tutorial-deploy-models-with-aml.md).

### <a name="image-registry"></a>Registru imagí

Bitové kopie, které jsou vytvořeny z vašich modelů uchovává informace o registru imagí. Při vytvoření bitové kopie můžete zadat další metadata značky. Značky metadat jsou uložené v registru image a můžete dát dotaz na nich najít image.

## <a name="deployment"></a>Nasazení

Nasazení je instance modelu do buď webové služby, který je možné hostovat v cloudu nebo modul pro nasazení integrovaných zařízení služby IoT.

### <a name="web-service"></a>Webová služba

Azure Container Instances, Azure Kubernetes Service nebo FPGA, můžete použít nasazenou webovou službu. Vytvoříte službu z modelu, skripty a přidružené soubory. Tyto jsou zapouzdřeny v obrázku, který poskytuje běhu prostředí pro webovou službu. Bitová kopie je vyrovnáváním zatížení, koncový bod HTTP, která přijímá vyhodnocování požadavků, které se odesílají do webové služby.

Azure pomáhá monitorovat nasazení vaší webové služby pomocí shromažďování telemetrie Application Insights nebo telemetrická data modelu, pokud jste se rozhodli tuto funkci povolil. Telemetrická data jsou přístupná pouze pro vás a je uložena v Application Insights a instance účtu úložiště.

Pokud jste povolili automatické škálování, Azure automaticky změní velikost vašeho nasazení.

Příklad nasazení modelu jako webové služby najdete v tématu [nasadit model klasifikace obrázků ve službě Azure Container Instances](tutorial-deploy-models-with-aml.md).

### <a name="iot-module"></a>Modul IoT

Nasazené modulu IoT je kontejner Dockeru obsahující váš model a přidruženého skriptovacího nebo aplikace a všechny další závislosti. Nasaďte tyto moduly s použitím Azure IoT Edge na hraničních zařízeních.

Pokud jste povolili monitorování, Azure shromažďuje telemetrická data z modelu uvnitř modulu Azure IoT Edge. Telemetrická data jsou přístupná pouze pro vás a je uložené v instanci účtu úložiště.

Azure IoT Edge zajišťuje, že je spuštěn modul a monitoruje zařízení, který je hostitelem.

## <a name="pipeline"></a>Kanál

Pomocí machine learning kanály, abyste mohli vytvářet a spravovat pracovní postupy, které spojit dohromady machine learning fází. Kanál může obsahovat například přípravy dat, trénování modelu, model nasazení a fáze vyhodnocování/odvození. Jednotlivé fáze může zahrnovat několik kroků, z nichž každý lze spustit bezobslužně v různých cílových výpočetních prostředí.

Další informace o machine learning kanály pomocí této služby najdete v tématu [kanály a Azure Machine Learning](concept-ml-pipelines.md).

## <a name="logging"></a>Protokolování

Při vývoji řešení protokolovat libovolné metriky pomocí sady Python SDK Azure Machine Learning ve svém skriptu Pythonu. Po spuštění dotaz metriky k určení, zda má spustit vytvořený model, který chcete nasadit.

## <a name="next-steps"></a>Další postup

Abyste mohli začít se službou Azure Machine Learning, naleznete v tématu:

* [Co je služba Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Vytvořit pracovní prostor služby Azure Machine Learning](setup-create-workspace.md)
* [Kurz: Trénování modelu](tutorial-train-models-with-aml.md)
* [Vytvoření pracovního prostoru pomocí šablony Resource Manageru](how-to-create-workspace-template.md)
