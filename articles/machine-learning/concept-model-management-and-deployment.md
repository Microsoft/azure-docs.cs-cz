---
title: 'MLOps: Správa modelů na ML'
titleSuffix: Azure Machine Learning
description: 'Přečtěte si o správě modelů pomocí Azure Machine Learning (MLOps). Nasaďte, spravujte a Monitorujte své modely, abyste je mohli průběžně vylepšit. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: e7c1f3384b88de7b58457251cf72b2815267cb29
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75541696"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: Správa modelů, nasazení a monitorování pomocí Azure Machine Learning

V tomto článku se dozvíte, jak pomocí Azure Machine Learning spravovat životní cyklus vašich modelů. Azure Machine Learning používá přístup k operacím Machine Learning (MLOps). MLOps vylepšuje kvalitu a konzistenci vašich řešení pro strojové učení. 

Azure Machine Learning poskytuje následující možnosti MLOps:

- **Vytvořte reprodukovatelné kanály ml**. Kanály umožňují definovat opakované a opakovaně použitelné kroky pro procesy přípravy dat, školení a bodování.
- **Zaregistrujte, zabalit a nasaďte modely odkudkoli** a sledujte přidružená metadata potřebná k použití modelu.
- **Zaznamenejte data zásad správného řízení, která jsou nutná pro zachycení kompletního životního cyklu ml**, včetně toho, kdo je publikující modely, proč se změny provádějí a kdy se modely nasadily nebo používaly v produkčním prostředí.
- **Upozornění a upozornění na události v životním cyklu ml** , jako je například dokončení experimentů, registrace modelů, nasazení modelu a detekce posunu dat.
- **Monitorovat aplikace na úrovni ml pro problémy související s provozními a ml** Porovnejte modelové vstupy mezi školením a odvozením, prozkoumejte metriky specifické pro model a poskytněte monitorování a výstrahy v infrastruktuře ML.
- **Automatizujte kompletní životní cyklus ml pomocí Azure Machine Learning a Azure DevOps** , abyste často aktualizovali modely, otestovali nové modely a průběžně zavedli nové modely ml společně s ostatními aplikacemi a službami.

## <a name="create-reproducible-ml-pipelines"></a>Vytváření reprodukovatelné kanály ML

Pomocí kanálů ML z Azure Machine Learning spojíte všechny kroky, které se podílejí na procesu školení modelu.

Kanál ML může obsahovat kroky z přípravy dat na extrakci funkcí až po vyladění modelu do hodnocení. Další informace najdete v tématu [ml kanály](service/concept-ml-pipelines.md).

Pokud k vytvoření kanálů ml použijete [Návrháře](service/concept-designer.md) , můžete kdykoli kliknout na tlačítko **"..."** v pravém horním rohu stránky návrháře a pak vybrat **klonovat**. Klonování kanálu vám umožní iterovat návrh kanálu bez ztráty starých verzí.  

## <a name="register-package-and-deploy-models-from-anywhere"></a>Registrace, balení a nasazení modelů odkudkoli

### <a name="register-and-track-ml-models"></a>Registrace a sledování modelů ML

Registrace modelu umožňuje ukládat a modelovat modely v cloudu Azure ve vašem pracovním prostoru. Modelový registr usnadňuje uspořádání a sledování vašich vyškolených modelů.

> [!TIP]
> Registrovaný model je logický kontejner pro jeden nebo více souborů, které tvoří model. Například pokud máte model, který je uložený v několika souborech, můžete je zaregistrovat jako jeden model v pracovním prostoru Azure Machine Learning. Po registraci můžete zaregistrovaný model stáhnout nebo nasadit a získat všechny soubory, které byly zaregistrovány.

Registrované modely se identifikují podle názvu a verze. Pokaždé, když zaregistrujete model se stejným názvem jako existující registr zvýší verzi. Během registrace lze zadat další značky metadat. Tyto značky se pak použijí při hledání modelu. Azure Machine Learning podporuje libovolný model, který lze načíst pomocí Pythonu 3.5.2 nebo vyššího.

> [!TIP]
> Můžete také registrovat modely školení mimo Azure Machine Learning.

Registrovaný model, který se používá v aktivním nasazení, se nedá odstranit.
Další informace naleznete v části model registru v tématu [nasazení modelů](how-to-deploy-and-where.md#registermodel).

### <a name="package-and-debug-models"></a>Modely balíčků a ladění

Než model nasadíte do produkčního prostředí, je zabalen do image Docker. Ve většině případů probíhá vytváření obrázků automaticky na pozadí během nasazování. Bitovou kopii můžete zadat ručně.

Pokud narazíte na problémy s nasazením, můžete nasadit v místním vývojovém prostředí pro řešení potíží a ladění.

Další informace najdete v tématu [nasazení modelů](how-to-deploy-and-where.md#registermodel) a [řešení potíží s nasazeními](how-to-troubleshoot-deployment.md).

### <a name="validate-and-profile-models"></a>Ověřování modelů a profilace

Azure Machine Learning dá profilaci použít k určení ideálního nastavení procesoru a paměti, které se má použít při nasazení modelu. Ověřování modelu probíhá jako součást tohoto procesu pomocí dat, která zadáte pro proces profilace.

### <a name="convert-and-optimize-models"></a>Převod a optimalizace modelů

Převod modelu na [otevřený neuronové Network Exchange](https://onnx.ai) (ONNX) může zlepšit výkon. V průměru může převod na ONNX přinést dvojnásobnou hodnotu zvýšení výkonu.

Další informace o ONNX s Azure Machine Learning najdete v článku o [Vytvoření a zrychlení ml modelů](concept-onnx.md) .

### <a name="use-models"></a>Použití modelů

Školené modely strojového učení se nasazují jako webové služby v cloudu nebo místně. Můžete také nasadit modely pro Azure IoT Edge zařízení. Nasazení používají pro Inferencing PROCESORy, GPU nebo pole s programovatelnými poli brány (FPGA). Můžete také použít modely z Power BI.

Při použití modelu jako webové služby nebo zařízení IoT Edge zadejte následující položky:

* Model (y), které se používají k určení skóre dat odeslaných do služby nebo zařízení.
* Vstupní skript. Tento skript přijímá požadavky, používá model (y) k vyhodnocení dat a vrací odpověď.
* Soubor prostředí Conda, který popisuje závislosti vyžadované modelem (y) a vstupním skriptem.
* Jakékoli další prostředky, například text, data atd., které jsou vyžadovány modelem (y) a vstupním skriptem.

Také zadáte konfiguraci cílové platformy nasazení. Například typ rodiny virtuálních počítačů, dostupnou paměť a počet jader při nasazení do služby Azure Kubernetes.

Při vytvoření bitové kopie jsou přidány také součásti požadované Azure Machine Learning. Například prostředky potřebné ke spuštění webové služby a interakci s IoT Edge.

#### <a name="batch-scoring"></a>Dávkové vyhodnocování
Dávkové vyhodnocování je podporováno prostřednictvím kanálů ML. Další informace najdete v tématu [Dávková předpovědi pro velké](how-to-run-batch-predictions.md)objemy dat.

#### <a name="real-time-web-services"></a>Webové služby v reálném čase

Můžete použít vaše modely ve **webových službách** s následujícími výpočetními cíli:

* Instance kontejneru Azure
* Azure Kubernetes Service
* Místní vývojové prostředí

Chcete-li model nasadit jako webovou službu, je nutné zadat následující položky:

* Model nebo komplet modelů.
* Závislosti vyžadované pro použití modelu Například skript, který přijímá požadavky a vyvolá model, závislosti conda atd.
* Konfigurace nasazení, která popisuje, jak a kde nasadit model.

Další informace najdete v tématu [nasazení modelů](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>IoT Edge zařízení

Modely se zařízeními IoT můžete používat prostřednictvím **Azure IoT Edgech modulů**. Moduly IoT Edge jsou nasazeny do hardwarového zařízení, které umožňuje odvození modelu nebo vyhodnocování modelů na zařízení.

Další informace najdete v tématu [nasazení modelů](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analýza

Microsoft Power BI podporuje používání modelů strojového učení pro analýzu dat. Další informace najdete v tématu [integrace Azure Machine Learning v Power BI (Preview)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Zaznamenání dat zásad správného řízení potřebných pro zachycení kompletního životního cyklu ML

Azure ML poskytuje možnost sledovat komplexní záznam auditu všech vašich prostředků ML. Zejména:

- Azure ML [se integruje s Git](how-to-set-up-training-targets.md#gitintegration) a sleduje informace o tom, ze kterého úložiště, větvení a potvrzení kódu pochází.
- Datové [sady Azure ml](how-to-create-register-datasets.md) vám pomůžou sledovat, profilovat a data verze. 
- Historie spuštění Azure ML ukládá snímek kódu, dat a výpočetní služby, které se používají k učení modelu.
- Registr modelu Azure ML zachycuje všechna metadata přidružená k vašemu modelu (které experiment vyškole, pokud je nasazený, pokud je jeho nasazení v pořádku).

## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Upozornění, automatizace a upozornění na události v životním cyklu ML
Azure ML publikuje klíčové události do Azure EventGrid, které se dají použít k oznamování a automatizaci událostí v životním cyklu ML. Další informace najdete v [tomto dokumentu](how-to-use-event-grid.md).


## <a name="monitor-for-operational--ml-issues"></a>Monitorování problémů s provozní & ML

Monitorování vám umožní pochopit, jaká data jsou odesílána do modelu, a předpovědi, který vrátí.

Tyto informace vám pomohou pochopit, jak je model používán. Shromážděná vstupní data mohou být užitečná také při výuce budoucích verzí modelu.

Další informace najdete v tématu [Jak povolit shromažďování dat modelu](how-to-enable-data-collection.md).

## <a name="automate-the-ml-lifecycle"></a>Automatizace životního cyklu ML 

Pomocí GitHubu a Azure Pipelines můžete vytvořit proces průběžné integrace, který bude navlakovat do modelu. Když se v typickém scénáři zkontroluje data v úložišti Git pro projekt, kanál Azure spustí školicí běh. Výsledky spuštění je pak možné zkontrolovat, aby se zobrazily výkonnostní charakteristiky vyučeného modelu. Můžete také vytvořit kanál, který nasadí model jako webovou službu.

[Rozšíření Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) usnadňuje práci s Azure Pipelines. Poskytuje následující vylepšení pro Azure Pipelines:

* Umožňuje výběr pracovního prostoru při definování připojení služby.
* Umožňuje aktivované kanály vydání vyškolené modely vytvořené v školicím kanálu.

Další informace o použití Azure Pipelines s Azure Machine Learning najdete v článku [průběžná integrace a nasazování modelů ml s Azure Pipelinesm](/azure/devops/pipelines/targets/azure-machine-learning) článkem a úložištěm [Azure Machine Learning MLOps](https://aka.ms/mlops) .

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu o tom, jak číst a prozkoumat následující zdroje:

+ [Postup & nasazení modelů](how-to-deploy-and-where.md) pomocí Azure Machine Learning

+ [Kurz: nasazení modelu klasifikace image v ACI](tutorial-deploy-models-with-aml.md).

+ [Ucelené příklady MLOps úložiště](https://github.com/microsoft/MLOps)

+ [CI/CD modelů ML s Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)

+ Vytváření klientů využívajících [nasazený model](how-to-consume-web-service.md)

+ [Strojové učení ve velkém měřítku](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Referenční architektury Azure AI & zástupce osvědčených postupů](https://github.com/microsoft/AI)
