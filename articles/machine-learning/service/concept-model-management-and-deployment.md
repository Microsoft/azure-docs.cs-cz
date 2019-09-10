---
title: 'MLOps: Správa, nasazení a & monitorování modelů ML'
titleSuffix: Azure Machine Learning service
description: 'Naučte se používat službu Azure Machine Learning pro MLOps: nasazení, Správa a monitorování vašich modelů pro jejich nepřetržité zlepšení. Můžete nasadit modely, které natrénovaný pomocí služby Azure Machine Learning, na místním počítači nebo z jiných zdrojů.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 2f3c3532637bef041ad1983b7573837dd0f29211
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860607"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Spravovat, nasazovat a sledovat modely pomocí služby Azure Machine Learning

V tomto článku se dozvíte, jak používat službu Azure Machine Learning ke správě životního cyklu vašich modelů. Azure Machine Learning využívá přístup k operacím s Machine Learning (MLOps), což zlepšuje kvalitu a konzistenci vašich řešení pro strojové učení. 

Služba Azure Machine Learning poskytuje následující možnosti MLOps:

- **Nasazení projektů ML odkudkoli**
- **Monitorovat aplikace na úrovni ml pro problémy související s provozními a ml** – porovnávají vstupy modelů mezi školením a odvozem, prozkoumejte metriky specifické pro model a poskytněte monitorování a výstrahy v infrastruktuře ml.
- **Zachyťte data potřebná k vytvoření koncového záznamu v rámci životního cyklu ml**, včetně toho, kdo je publikuje, proč se změny provádějí a kdy se modely nasadily nebo používaly v produkčním prostředí.
- **Automatizujte kompletní životní cyklus ml s Azure Machine Learning a Azure DevOps** , abyste často aktualizovali modely, otestovali nové modely a průběžně zavedli nové modely ml společně s ostatními aplikacemi a službami.

Pokud chcete další informace o konceptech na MLOps a o tom, jak se vztahují na službu Azure Machine Learning, podívejte se na následující video.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="deploy-ml-projects-from-anywhere"></a>Nasazení projektů ML odkudkoli

### <a name="turn-your-training-process-into-a-reproducible-pipeline"></a>Zapněte svůj školicí proces do reprodukovatelného kanálu.
Použijte kanály ML z Azure Machine Learning ke spojování všech kroků zapojených do procesu školicích procesů, od přípravy dat až po extrakci parametrů až po vyladění modelu.

Další informace najdete v tématu [ml kanály](concept-ml-pipelines.md).

### <a name="register-and-track-ml-models"></a>Registrace a sledování modelů ML

Registrace modelu umožňuje ukládat a modelovat modely v cloudu Azure ve vašem pracovním prostoru. Modelový registr usnadňuje uspořádání a sledování vašich vyškolených modelů.

> [!TIP]
> Registrovaný model je logický kontejner pro jeden nebo více souborů, které tvoří model. Například pokud máte model, který je uložený v několika souborech, můžete je zaregistrovat jako jeden model v pracovním prostoru Azure Machine Learning. Po registraci můžete zaregistrovaný model stáhnout nebo nasadit a získat všechny soubory, které byly zaregistrovány.
 
Registrované modely se identifikují podle názvu a verze. Pokaždé, když zaregistrujete model se stejným názvem jako existující registr zvýší verzi. Během registrace, který se dá použít při vyhledávání pro modely můžete zadat také další metadata značky. Služba Azure Machine Learning podporuje jakýkoliv model, který se dá načíst pomocí Pythonu 3.5.2 nebo vyššího.

> [!TIP]
> Můžete také registrovat modely školení mimo Azure Machine Learning službu.

Registrovaný model, který se používá v aktivním nasazení, se nedá odstranit.
Další informace naleznete v části model registru v tématu [nasazení modelů](how-to-deploy-and-where.md#registermodel).

### <a name="package-and-debug-models"></a>Modely balíčků a ladění

Než model nasadíte do produkčního prostředí, je zabalen do image Docker. Ve většině případů probíhá vytváření obrázků automaticky na pozadí během nasazování. V případě pokročilých scénářů můžete bitovou kopii zadat ručně.

Pokud narazíte na problémy s nasazením, můžete nasadit v místním vývojovém prostředí pro řešení potíží a ladění.

Další informace najdete v tématu [nasazení modelů](how-to-deploy-and-where.md#registermodel) a [řešení potíží s nasazeními](how-to-troubleshoot-deployment.md).

### <a name="validate-and-profile-models"></a>Ověřování modelů a profilace

Služba Azure Machine Learning umožňuje profilaci použít k určení ideálního nastavení procesoru a paměti, které se má použít při nasazení modelu. Ověřování modelu probíhá jako součást tohoto procesu pomocí dat, která zadáte pro proces profilace.

### <a name="convert-and-optimize-models"></a>Převod a optimalizace modelů

Převod modelu na [otevřený neuronové Network Exchange](https://onnx.ai) (ONNX) může zlepšit výkon. V průměru může převod na ONNX přinést dvojnásobnou hodnotu zvýšení výkonu.

Další informace o ONNX pomocí služby Azure Machine Learning najdete v článku [Vytvoření a zrychlení ml modelů](concept-onnx.md) .

### <a name="use-models"></a>Použití modelů

Školicí modely strojového učení se dají nasadit jako webové služby v cloudu nebo lokálně na svém vývojovém prostředí. Můžete také nasadit modely pro Azure IoT Edge zařízení. Nasazení můžou pro Inferencing používat PROCESORová, GPU nebo pole FPGA (Array-programovatelná). Můžete také použít modely z Power BI.

Při použití modelu jako webové služby nebo zařízení IoT Edge zadejte následující položky:

* Model (y), které se používají k určení skóre dat odeslaných do služby nebo zařízení.
* Vstupní skript. Tento skript přijímá požadavky, používá model (y) k vyhodnocení dat a vrací odpověď.
* Soubor prostředí Conda, který popisuje závislosti vyžadované modelem (y) a vstupním skriptem.
* Jakékoli další prostředky, například text, data atd., které jsou vyžadovány modelem (y) a vstupním skriptem.

Tyto prostředky jsou zabaleny do bitové kopie Docker a nasazeny jako webová služba nebo modul IoT Edge.

Volitelně můžete k dalšímu vyladění nasazení použít následující parametry:

* Povolit GPU: Slouží k povolení podpory GPU v imagi Docker. Image se musí používat pro Microsoft Azure služby, jako je Azure Container Instances, Azure Kubernetes Service, Azure Machine Learning COMPUTE nebo Azure Virtual Machines.
* Další kroky k souboru Docker: Soubor, který obsahuje další kroky Docker, které se mají spustit při vytváření image Docker.
* Základní bitová kopie: Vlastní image, která se použije jako základní image. Pokud nepoužíváte vlastní image, poskytuje služba Azure Machine Learning základní image.

Také zadáte konfiguraci cílové platformy nasazení. Například typ rodiny virtuálních počítačů, dostupnou paměť a počet jader při nasazení do služby Azure Kubernetes.

Po vytvoření bitové kopie jsou přidány také součásti požadované službou Azure Machine Learning. Například prostředky potřebné ke spuštění webové služby a interakci s IoT Edge.

> [!NOTE]
> Webový server nebo součásti IoT Edge používané v imagi Docker nemůžete upravovat ani měnit. Služba Azure Machine Learning používá konfiguraci webového serveru a IoT Edge součásti, které jsou testovány a podporovány společností Microsoft.

#### <a name="web-service"></a>Webová služba

Můžete použít vaše modely ve **webových službách** s následujícími výpočetními cíli:

* Instance kontejneru Azure
* Azure Kubernetes Service
* Místní vývojové prostředí

Chcete-li model nasadit jako webovou službu, je nutné zadat následující položky:

* Model nebo komplet modelů.
* Závislosti vyžadované pro použití modelu Například skript, který přijímá požadavky a vyvolá model, závislosti conda atd.
* Konfigurace nasazení, která popisuje, jak a kde nasadit model.

Další informace najdete v tématu [nasazení modelů](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>Zařízení IoT Edge

Modely se zařízeními IoT můžete používat prostřednictvím **Azure IoT Edgech modulů**. Moduly IoT Edge jsou nasazeny do hardwarového zařízení, které umožňuje odvození modelu nebo vyhodnocování modelů na zařízení.

Další informace najdete v tématu [nasazení modelů](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analýzy

Microsoft Power BI podporuje používání modelů strojového učení pro analýzu dat. Další informace najdete v tématu [integrace Azure Machine Learning v Power BI (Preview)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).


## <a name="monitor-ml-applications-for-operational-and-ml-related-issues"></a>Monitorování aplikací ML na problémy související s provozními a ML

Monitorování vám umožní pochopit, jaká data jsou odesílána do modelu, a předpovědi, který vrátí.

Tyto informace vám pomohou pochopit, jak je model používán. Shromážděná vstupní data mohou být užitečná také při výuce budoucích verzí modelu.

Další informace najdete v tématu [Jak povolit shromažďování dat modelu](how-to-enable-data-collection.md).


## <a name="capture-an-end-to-end-audit-trail-of-the-ml-lifecycle"></a>Zachytit záznam koncového auditu v průběhu životního cyklu ML

Azure ML vám nabízí možnost sledovat záznam o všech svých prostředcích ML na konci auditu. Zejména:

- Azure ML [se integruje s Git](how-to-set-up-training-targets.md#gitintegration) , aby mohla sledovat informace o tom, ze kterého úložiště, větvení a potvrzení kódu pochází.
- Datové [sady Azure ml](how-to-create-register-datasets.md) vám pomůžou sledovat a data verze.
- Historie spuštění Azure ML ukládá snímek kódu, data a výpočetní prostředky, které se používají pro výuku modelu.
- Registr modelu Azure ML zachycuje všechna metadata přidružená k vašemu modelu (které experiment vyškole, pokud je nasazený, pokud je jeho nasazení v pořádku).

## <a name="automate-the-end-to-end-ml-lifecycle"></a>Automatizace životního cyklu celého ML na konci 

Pomocí GitHubu a Azure Pipelines můžete vytvořit proces průběžné integrace, který bude navlakovat do modelu. Když se v typickém scénáři zkontroluje data v úložišti Git pro projekt, kanál Azure spustí školicí běh. Výsledky spuštění je pak možné zkontrolovat, aby se zobrazily výkonnostní charakteristiky vyučeného modelu. Můžete také vytvořit kanál, který nasadí model jako webovou službu.

[Rozšíření Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) usnadňuje práci s Azure Pipelines. Poskytuje následující vylepšení pro Azure Pipelines:

* Umožňuje výběr pracovního prostoru při definování připojení služby.
* Umožňuje aktivované kanály vydání vyškolené modely vytvořené v školicím kanálu.

Další informace o použití Azure Pipelines s Azure Machine Learning najdete v článku [průběžná integrace a nasazování modelů ml s Azure Pipelinesm](/azure/devops/pipelines/targets/azure-machine-learning) článkem a úložištěm [MLOps služby Azure Machine Learning](https://aka.ms/mlops) .

## <a name="next-steps"></a>Další postup

Další informace o [jak a kde můžete nasadit modely](how-to-deploy-and-where.md) ve službě Azure Machine Learning. Příklad nasazení najdete v tématu [kurz: Nasaďte model klasifikace imagí v Azure Container Instances](tutorial-deploy-models-with-aml.md).

Naučte se vytvářet [průběžnou integraci a nasazování modelů ml pomocí Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning). 

Naučte se vytvářet klientské aplikace a služby, které [využívají model nasazený jako webovou službu](how-to-consume-web-service.md).
