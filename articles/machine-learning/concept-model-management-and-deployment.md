---
title: 'MLOps: Správa modelů ML'
titleSuffix: Azure Machine Learning
description: 'Další informace o správě modelů pomocí Azure Machine Learning (MLOps). Nasazujte, spravujte a monitorujte své modely, abyste je neustále vylepšovali. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 7857d11c625911cd1b49dfcf0e0d612fc6a3871e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314297"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: Správa modelů, nasazení a monitorování pomocí Azure Machine Learning

V tomto článku se dozvíte, jak pomocí Azure Machine Learning spravovat životní cyklus vašich modelů. Azure Machine Learning používá přístup operace strojového učení (MLOps). MLOps zlepšuje kvalitu a konzistenci vašich řešení strojového učení. 

## <a name="what-is-mlops"></a>Co je MLOps?

Operace strojového učení (MLOps) jsou založeny na principech a postupech [DevOps,](https://azure.microsoft.com/overview/what-is-devops/) které zvyšují efektivitu pracovních postupů. Například průběžná integrace, doručování a nasazení. MLOps aplikuje tyto zásady na proces strojového učení s cílem:

* Rychlejší experimentování a vývoj modelů
* Rychlejší nasazení modelů do produkčního prostředí
* Kontrola kvality

Azure Machine Learning poskytuje následující funkce MLOps:

- **Vytvořte reprodukovatelné kanály ML**. Kanály machine learningu umožňují definovat opakovatelné a opakovaně použitelné kroky pro procesy přípravy, školení a vyhodnocování dat.
- **Vytvořte opakovaně použitelná softwarová prostředí** pro školení a nasazování modelů.
- **Zaregistrujte, zabalte a nasaďte modely odkudkoli**. Můžete také sledovat přidružená metadata potřebná k použití modelu.
- **Zachyťte data zásad správného řízení pro celý životní cyklus ML**. Protokolované informace mohou zahrnovat, kdo publikuje modely, proč byly provedeny změny a kdy byly modely nasazeny nebo použity v produkčním prostředí.
- **Upozorněte a upozorněte na události v životním cyklu ml**. Například dokončení experimentu, registrace modelu, nasazení modelu a detekce posunu dat.
- **Sledujte aplikace ML z provozních důvodů a problémů souvisejících s ml**. Porovnejte vstupy modelu mezi školenía odvození, prozkoumejte metriky specifické pro model a poskytněte monitorování a výstrahy na infrastruktuře ML.
- **Automatizujte celý životní cyklus ML pomocí Azure Machine Learning a Azure Pipelines**. Pomocí kanálů můžete často aktualizovat modely, testovat nové modely a průběžně zavádět nové modely ML společně s dalšími aplikacemi a službami.

## <a name="create-reproducible-ml-pipelines"></a>Vytvoření reprodukovatelných kanálů ML

Pomocí kanálů ML z Azure Machine Learning sešít všechny kroky, které jsou součástí procesu školení modelu.

Kanál ML může obsahovat kroky od přípravy dat až po extrakci funkce až po hyperparametrické ladění do vyhodnocení modelu. Další informace naleznete v [tématu ML potrubí](concept-ml-pipelines.md).

Pokud k vytvoření kanálů ML použijete [návrháře,](concept-designer.md) můžete kdykoli kliknout na **tlačítko "..."** v pravém horním rohu stránky Návrháře a potom vybrat **možnost Klonovat**. Klonování kanálu umožňuje iterat návrh kanálu bez ztráty starých verzí.  

## <a name="create-reusable-software-environments"></a>Vytvoření opakovaně použitelných softwarových prostředí

Prostředí Azure Machine Learning umožňují sledovat a reprodukovat závislosti softwaru vašich projektů ve vývoji. Prostředí umožňují zajistit, aby sestavení byla reprodukovatelná bez ruční konfigurace softwaru.

Prostředí popisují pip a Conda závislosti pro vaše projekty a lze použít pro školení a nasazení modelů. Další informace najdete v tématu [Co jsou prostředí Azure Machine Learning](concept-environments.md).

## <a name="register-package-and-deploy-models-from-anywhere"></a>Registrace, balení a nasazení modelů odkudkoli

### <a name="register-and-track-ml-models"></a>Registrace a sledování modelů ML

Registrace modelu umožňuje ukládat a uvolňovat modely v cloudu Azure ve vašem pracovním prostoru. Registr modelů usnadňuje uspořádání a sledování trénovaných modelů.

> [!TIP]
> Registrovaný model je logický kontejner pro jeden nebo více souborů, které tvoří model. Například pokud máte model, který je uložen ý ve více souborech, můžete je zaregistrovat jako jeden model v pracovním prostoru Azure Machine Learning. Po registraci pak můžete stáhnout nebo nasadit registrovaný model a přijímat všechny soubory, které byly zaregistrovány.

Registrované modely se identifikují prostřednictvím názvu a verze. Vždy, když zaregistrujete model se stejným názvem, který již používá existující model, v registru se zvýší jeho verze. Další značky metadat mohou být poskytnuty při registraci. Tyto značky se pak používají při hledání modelu. Azure Machine Learning podporuje jakýkoli model, který se dá načíst pomocí Pythonu 3.5.2 nebo vyššího.

> [!TIP]
> Můžete také zaregistrovat modely trénované mimo Azure Machine Learning.

Registrovaný model, který se používá v aktivním nasazení, nelze odstranit.
Další informace naleznete v části model registru [nasazení modelů](how-to-deploy-and-where.md#registermodel).

### <a name="profile-models"></a>Profilové modely

Azure Machine Learning vám může pomoci pochopit požadavky na procesor a paměť služby, která se vytvoří při nasazení modelu. Profilování testuje službu, která spouští váš model a vrací informace, jako je využití procesoru, využití paměti a latence odezvy. Poskytuje také doporučení procesoru a paměti na základě využití prostředků.
Další informace naleznete v části profilování [nasazení modelů](how-to-deploy-and-where.md#profilemodel).

### <a name="package-and-debug-models"></a>Modely balíčků a ladění

Před nasazením modelu do produkčního prostředí je zabalen do image Dockeru. Ve většině případů se vytváření bitové kopie automaticky během nasazení. Obrázek můžete zadat ručně.

Pokud narazíte na problémy s nasazením, můžete nasadit v místním vývojovém prostředí pro řešení potíží a ladění.

Další informace naleznete v [tématu Nasazení modelů](how-to-deploy-and-where.md#registermodel) a [nasazení řešení potíží](how-to-troubleshoot-deployment.md).

### <a name="convert-and-optimize-models"></a>Převod a optimalizace modelů

Převod modelu na [Open Neural Network Exchange](https://onnx.ai) (ONNX) může zlepšit výkon. V průměru může převod na ONNX přinést 2x zvýšení výkonu.

Další informace o ONNX s Azure Machine Learning najdete v článku [Vytvoření a urychlení modelů ML.](concept-onnx.md)

### <a name="use-models"></a>Použití modelů

Trénované modely strojového učení se nasazují jako webové služby v cloudu nebo místně. Modely můžete také nasadit do zařízení Azure IoT Edge. Nasazení používají pro odvození procesor, GPU nebo pole programovatelná pole brány (FPGA). Můžete taky použít modely z Power BI.

Při použití modelu jako webové služby nebo zařízení IoT Edge zadejte následující položky:

* Model (y), které se používají k skóre data odeslaná do služby/zařízení.
* Vstupní skript. Tento skript přijímá požadavky, používá model (y) k skóre dat a vrátit odpověď.
* Prostředí Azure Machine Learning, které popisuje závislosti pip a Conda vyžadované modelem a vstupním skriptem.
* Všechny další prostředky, jako je text, data atd., které jsou vyžadovány modelem (modely) a vstupním skriptem.

Můžete také zadat konfiguraci cílové platformy nasazení. Například typ rodiny virtuálních počítače, dostupná paměť a počet jader při nasazování do služby Azure Kubernetes.

Při vytvoření image jsou přidány také součásti vyžadované Azure Machine Learning. Například prostředky potřebné ke spuštění webové služby a interakci s IoT Edge.

#### <a name="batch-scoring"></a>Dávkové vyhodnocování
Dávkové vyhodnocování je podporováno prostřednictvím kanálů ML. Další informace naleznete [v tématu Batch predictions on big data](how-to-use-parallel-run-step.md).

#### <a name="real-time-web-services"></a>Webové služby v reálném čase

Modely můžete používat ve **webových službách** s následujícími výpočetními cíli:

* Instance kontejneru Azure
* Azure Kubernetes Service
* Prostředí místního rozvoje

Chcete-li nasadit model jako webovou službu, musíte zadat následující položky:

* Model nebo soubor modelů.
* Závislosti potřebné k použití modelu. Například skript, který přijímá požadavky a vyvolá model, conda závislosti atd.
* Konfigurace nasazení, která popisuje, jak a kde nasadit model.

Další informace naleznete v [tématu Nasazení modelů](how-to-deploy-and-where.md).

#### <a name="controlled-rollout"></a>Řízené zavádění

Při nasazování do služby Azure Kubernetes service můžete pomocí řízeného zavádění povolit následující scénáře:

* Vytvoření více verzí koncového bodu pro nasazení
* Proveďte testování A/B směrováním provozu do různých verzí koncového bodu.
* Přepínejte mezi verzemi koncových bodů aktualizací procenta provozu v konfiguraci koncového bodu.

Další informace naleznete [v tématu Řízené zavádění modelů ML](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview).

#### <a name="iot-edge-devices"></a>Zařízení IoT Edge

Modely se zařízeními IoT můžete používat prostřednictvím **modulů Azure IoT Edge**. Moduly IoT Edge se nasazují do hardwarového zařízení, které umožňuje odvození nebo vyhodnocování modelu na zařízení.

Další informace naleznete v [tématu Nasazení modelů](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analýza

Microsoft Power BI podporuje použití modelů strojového učení pro analýzu dat. Další informace najdete [v tématu Integrace Azure Machine Learning v Power BI (preview).](https://docs.microsoft.com/power-bi/service-machine-learning-integration)

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Zachyťte data zásad správného řízení potřebná pro zachycení životního cyklu ML od konce

Azure ML umožňuje sledovat stopu auditu od konce na konci všech vašich prostředků ML pomocí metadat.

- Azure ML [se integruje s Gitem](how-to-set-up-training-targets.md#gitintegration) a sleduje informace o tom, ze kterého úložiště nebo větvení nebo potvrzení kódu pochází.
- [Datové sady Azure ML](how-to-create-register-datasets.md) vám pomůžou sledovat data, profil a verze.
- [Interpretabilita](how-to-machine-learning-interpretability.md) umožňuje vysvětlit vaše modely, splnit dodržování předpisů a pochopit, jak modely přicházejí k výsledku pro daný vstup.
- Historie Azure ML Run ukládá snímek kódu, dat a výpočetních prostředků používaných k trénování modelu.
- Registr modelu Azure ML zachycuje všechna metadata přidružená k vašemu modelu (který experiment trénoval, kde se nasazuje, pokud jsou jeho nasazení v pořádku).
- [Integrace s Azure Event Grid](concept-event-grid-integration.md) umožňuje jednat na události v životním cyklu ML. Například registrace modelu, nasazení, posun dat a trénovací (spustit) události.

> [!TIP]
> Zatímco některé informace o modelech a datových sadách jsou automaticky zachyceny, můžete přidat další informace pomocí __značek__. Při hledání registrovaných modelů a datových sad v pracovním prostoru můžete značky použít jako filtr.
>
> Připojování datové sady s registrovaným modelem je volitelný krok. Informace o odkazování na datovou sadu při registraci modelu naleznete v odkazu na třídu [modelu.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py)


## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Upozorňovat, automatizovat a upozorňovat na události v životním cyklu ml
Azure ML publikuje klíčové události do Azure EventGrid, které se dají upozornit a automatizovat na událostech v životním cyklu ML. Další informace naleznete v [tomto dokumentu](how-to-use-event-grid.md).


## <a name="monitor-for-operational--ml-issues"></a>Sledování provozních & problémů s ML

Monitorování umožňuje pochopit, jaká data jsou odesílána do modelu a předpovědi, které vrátí.

Tyto informace vám pomohou pochopit, jak je model používán. Shromážděná vstupní data mohou být také užitečná při trénování budoucích verzí modelu.

Další informace naleznete v tématu [Jak povolit shromažďování dat modelu](how-to-enable-data-collection.md).

## <a name="retrain-your-model-on-new-data"></a>Přeškolení modelu na nová data

Často budete chtít ověřit model, aktualizovat, nebo dokonce přeškolit od začátku, jak budete dostávat nové informace. V některých případě je příjem nových dat očekávanou součástí domény. Jindy, jak je popsáno v [tématu Detekce posunu dat (náhled) na datové sady](how-to-monitor-datasets.md), může výkon modelu snížit tváří v tvář takovým věcem, jako jsou změny konkrétního senzoru, změny přirozených dat, jako jsou sezónní efekty, nebo funkce, které se ve vztahu k ostatním funkcím mění. 

Neexistuje univerzální odpověď na otázku "Jak mám vědět, jestli mám přeškolit?" ale Azure ML události a monitorování nástroje dříve diskutované jsou dobrým výchozím bodem pro automatizaci. Jakmile se rozhodnete přeškolit, měli byste: 

- Předběžně zpracujte data pomocí opakovatelného automatizovaného procesu
- Trénování nového modelu
- Porovnání výstupů nového modelu s výstupy vašeho starého modelu
- Pomocí předdefinovaných kritérií zvolte, zda chcete starý model nahradit. 

Tématem výše uvedených kroků je, že vaše rekvalifikace by měla být automatizovaná, nikoli ad hoc. [Kanály Azure Machine Learning](concept-ml-pipelines.md) jsou dobrou odpovědí pro vytváření pracovních postupů týkajících se přípravy dat, školení, ověřování a nasazení. Přečtěte si [retrain modely s Azure Machine Learning designer (preview)](how-to-retrain-designer.md) a zjistěte, jak kanály a Návrhář Azure Machine Learning zapadají do scénáře retraining. 

## <a name="automate-the-ml-lifecycle"></a>Automatizace životního cyklu ML 

GitHub a Azure Pipelines můžete použít k vytvoření procesu průběžné integrace, který trénuje model. V typickém scénáři, když data scientist zkontroluje změnu do úložiště Git pro projekt, Azure Pipeline spustí trénovací běh. Výsledky spuštění pak mohou být zkontrolovány, aby se zjistily výkonnostní charakteristiky trénovaného modelu. Můžete také vytvořit kanál, který nasadí model jako webovou službu.

Rozšíření [Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) usnadňuje práci s Azure Pipelines. Poskytuje následující vylepšení Azure Pipelines:

* Povolí výběr pracovního prostoru při definování připojení služby.
* Umožňuje kanály vydání, které mají být spuštěny trénované modely vytvořené v kanálu školení.

Další informace o používání Azure Pipelines s Azure Machine Learning najdete v následujících odkazech:

* [Průběžná integrace a nasazování modelů ML s Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Úložiště MLOps Azure Machine Learning.](https://aka.ms/mlops)
* [Azure Machine Learning MLOpsPython](https://github.com/Microsoft/MLOpspython) úložiště.

Azure Data Factory můžete také použít k vytvoření kanálu pro ingestování dat, který připraví data pro použití s trénováním. Další informace naleznete [v tématu Kanál pro přijím dat](how-to-cicd-data-ingestion.md).

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících zdrojích informací:

+ [Jak & kam nasadit modely](how-to-deploy-and-where.md) pomocí Azure Machine Learning

+ [Kurz: Nasazení modelu klasifikace bitových obrázků v ACI](tutorial-deploy-models-with-aml.md).

+ [Příklady od konce MLOps repo](https://github.com/microsoft/MLOps)

+ [CI/CD modelů ML s Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)

+ Vytvoření klientů, kteří [spotřebovávají nasazený model](how-to-consume-web-service.md)

+ [Strojové učení ve velkém měřítku](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Referenční architektury Azure AI & zástupce osvědčených postupů](https://github.com/microsoft/AI)
