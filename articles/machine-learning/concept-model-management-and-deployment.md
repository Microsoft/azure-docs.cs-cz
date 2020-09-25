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
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 6f03a1e44fdb62570b693753f5e01c7ab0f53e78
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91302413"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: Správa modelů, nasazení a monitorování pomocí Azure Machine Learning

V tomto článku se dozvíte, jak pomocí Azure Machine Learning spravovat životní cyklus vašich modelů. Azure Machine Learning používá přístup k operacím Machine Learning (MLOps). MLOps vylepšuje kvalitu a konzistenci vašich řešení pro strojové učení. 

## <a name="what-is-mlops"></a>Co je MLOps?

Operace Machine Learning (MLOps) jsou založené na [DevOps](https://azure.microsoft.com/overview/what-is-devops/) principech a postupech, které zvyšují efektivitu pracovních postupů. Například průběžná integrace, doručování a nasazování. MLOps tyto zásady aplikuje na proces strojového učení s cílem:

* Rychlejší experimentování a vývoj modelů
* Rychlejší nasazení modelů do produkčního prostředí
* Kontrola kvality

Azure Machine Learning poskytuje následující možnosti MLOps:

- **Vytvořte reprodukovatelné kanály ml**. Kanály Machine Learning umožňují definovat opakované a opakovaně použitelné kroky pro procesy přípravy, školení a bodování vašich dat.
- **Vytvořte opakovaně použitelná softwarová prostředí** pro školení a nasazení modelů.
- **Zaregistrujte, zabalit a nasaďte modely odkudkoli**. Můžete také sledovat přidružená metadata potřebná k použití modelu.
- **Zaznamenejte si data zásad správného řízení pro kompletní životní cyklus ml**. Protokolované informace mohou zahrnovat to, kdo je publikující, proč byly provedeny změny a kdy se modely nasadily nebo používaly v produkčním prostředí.
- **Upozornění a upozornění na události v průběhu životního cyklu ml**. Například dokončování experimentů, registrace modelů, nasazení modelu a detekce posunu dat.
- **Monitorovat aplikace na úrovni ml pro problémy související s provozními a ml** Porovnejte modelové vstupy mezi školením a odvozením, prozkoumejte metriky specifické pro model a poskytněte monitorování a výstrahy v infrastruktuře ML.
- **Automatizujte kompletní životní cyklus ml pomocí Azure Machine Learning a Azure Pipelines**. Použití kanálů umožňuje často aktualizovat modely, testovat nové modely a průběžně zavádět nové modely ML společně s ostatními aplikacemi a službami.

## <a name="create-reproducible-ml-pipelines"></a>Vytváření reprodukovatelné kanály ML

Pomocí kanálů ML z Azure Machine Learning spojíte všechny kroky, které se podílejí na procesu školení modelu.

Kanál ML může obsahovat kroky z přípravy dat na extrakci funkcí až po vyladění modelu do hodnocení. Další informace najdete v tématu [ml kanály](concept-ml-pipelines.md).

Pokud k vytvoření kanálů ml použijete [Návrháře](concept-designer.md) , můžete kdykoli kliknout na tlačítko **"..."** v pravém horním rohu stránky návrháře a pak vybrat **klonovat**. Klonování kanálu vám umožní iterovat návrh kanálu bez ztráty starých verzí.  

## <a name="create-reusable-software-environments"></a>Vytváření opakovaně použitelných softwarových prostředí

Azure Machine Learning prostředí umožňují sledovat a reprodukuje závislosti softwaru projektů při jejich vývojech. Prostředí vám umožní zajistit, aby sestavení byla reprodukovatelná bez ručních konfigurací softwaru.

Prostředí popisují závislosti PIP a conda pro vaše projekty a dají se použít jak pro účely školení, tak pro nasazení modelů. Další informace najdete v tématu [co jsou Azure Machine Learning prostředí](concept-environments.md).

## <a name="register-package-and-deploy-models-from-anywhere"></a>Registrace, balení a nasazení modelů odkudkoli

### <a name="register-and-track-ml-models"></a>Registrace a sledování modelů ML

Registrace modelu umožňuje ukládat a modelovat modely v cloudu Azure ve vašem pracovním prostoru. Modelový registr usnadňuje uspořádání a sledování vašich vyškolených modelů.

> [!TIP]
> Registrovaný model je logický kontejner pro jeden nebo více souborů, které tvoří model. Například pokud máte model, který je uložený v několika souborech, můžete je zaregistrovat jako jeden model v pracovním prostoru Azure Machine Learning. Po registraci můžete zaregistrovaný model stáhnout nebo nasadit a získat všechny soubory, které byly zaregistrovány.

Registrované modely se identifikují prostřednictvím názvu a verze. Vždy, když zaregistrujete model se stejným názvem, který již používá existující model, v registru se zvýší jeho verze. Během registrace lze zadat další značky metadat. Tyto značky se pak použijí při hledání modelu. Azure Machine Learning podporuje libovolný model, který lze načíst pomocí Pythonu 3.5.2 nebo vyššího.

> [!TIP]
> Můžete také registrovat modely školení mimo Azure Machine Learning.

Registrovaný model, který se používá v aktivním nasazení, se nedá odstranit.
Další informace naleznete v části model registru v tématu [nasazení modelů](how-to-deploy-and-where.md#registermodel).

### <a name="profile-models"></a>Modely profilů

Azure Machine Learning vám může pomáhat pochopit požadavky na procesor a paměť služby, která se vytvoří při nasazení modelu. Profilace testuje službu, která spouští váš model, a vrací informace, jako je využití CPU, využití paměti a latence odezvy. Poskytuje taky doporučení pro procesor a paměť na základě využití prostředků.
Další informace najdete v části profilace v tématu [nasazení modelů](how-to-deploy-profile-model.md).

### <a name="package-and-debug-models"></a>Modely balíčků a ladění

Než model nasadíte do produkčního prostředí, je zabalen do image Docker. Ve většině případů probíhá vytváření obrázků automaticky na pozadí během nasazování. Bitovou kopii můžete zadat ručně.

Pokud narazíte na problémy s nasazením, můžete nasadit v místním vývojovém prostředí pro řešení potíží a ladění.

Další informace najdete v tématu [nasazení modelů](how-to-deploy-and-where.md#registermodel) a [řešení potíží s nasazeními](how-to-troubleshoot-deployment.md).

### <a name="convert-and-optimize-models"></a>Převod a optimalizace modelů

Převod modelu na [otevřený neuronové Network Exchange](https://onnx.ai) (ONNX) může zlepšit výkon. V průměru může převod na ONNX přinést dvojnásobnou hodnotu zvýšení výkonu.

Další informace o ONNX s Azure Machine Learning najdete v článku o [Vytvoření a zrychlení ml modelů](concept-onnx.md) .

### <a name="use-models"></a>Použití modelů

Školené modely strojového učení se nasazují jako webové služby v cloudu nebo místně. Můžete také nasadit modely pro Azure IoT Edge zařízení. Nasazení používají pro Inferencing PROCESORy, GPU nebo pole s programovatelnými poli brány (FPGA). Můžete také použít modely z Power BI.

V případě použití modelu jako webové služby nebo pro zařízení IoT Edge poskytujete následující položky:

* Model (y), které se používají k určení skóre dat odeslaných do služby nebo zařízení.
* Vstupní skript. Tento skript přijímá požadavky, používá model (y) k vyhodnocení dat a vrací odpověď.
* Azure Machine Learning prostředí popisující závislosti PIP a Conda, které vyžadují model (y) a skript vstupu.
* Jakékoli další prostředky, například text, data atd., které jsou vyžadovány modelem (y) a vstupním skriptem.

Poskytujete také konfiguraci cílové platformy, na kterou model nasazujete. Například typ rodiny virtuálních počítačů, dostupnou paměť a počet jader při nasazení do služby Azure Kubernetes.

Do vytvořené image se dále přidají komponenty, které vyžaduje Azure Machine Learning. Může jít třeba o prostředky potřebné ke spuštění webové služby a interakci s IoT Edge.

#### <a name="batch-scoring"></a>Dávkové vyhodnocování
Dávkové vyhodnocování je podporováno prostřednictvím kanálů ML. Další informace najdete v tématu [Dávková předpovědi pro velké](how-to-use-parallel-run-step.md)objemy dat.

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

#### <a name="controlled-rollout"></a>Řízené zavedení

Při nasazování do služby Azure Kubernetes můžete použít řízené zavedení k povolení následujících scénářů:

* Vytvoření více verzí koncového bodu pro nasazení
* Proveďte testování A/B směrováním provozu do různých verzí koncového bodu.
* Přepněte mezi verzemi koncových bodů tak, že aktualizujete procento provozu v konfiguraci koncového bodu.

Další informace najdete v tématu [řízená zavedení modelů ml](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview).

#### <a name="iot-edge-devices"></a>IoT Edge zařízení

Modely se zařízeními IoT můžete používat prostřednictvím **Azure IoT Edgech modulů**. Moduly IoT Edge jsou nasazeny do hardwarového zařízení, které umožňuje odvození modelu nebo vyhodnocování modelů na zařízení.

Další informace najdete v tématu [nasazení modelů](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analýzy

Microsoft Power BI podporuje používání modelů strojového učení pro analýzu dat. Další informace najdete v tématu [integrace Azure Machine Learning v Power BI (Preview)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Zaznamenání dat zásad správného řízení potřebných pro zachycení kompletního životního cyklu ML

Služba Azure ML poskytuje možnost sledovat kompletní záznam auditu všech prostředků ML pomocí metadat.

- Azure ML [se integruje s Git](how-to-set-up-training-targets.md#gitintegration) a sleduje informace o tom, ze kterého úložiště, větvení a potvrzení kódu pochází.
- Datové [sady Azure ml](how-to-create-register-datasets.md) vám pomůžou sledovat, profilovat a data verze.
- Možnost [Interpretace](how-to-machine-learning-interpretability.md) vám umožní vysvětlit vaše modely, dodržovat dodržování předpisů a pochopit, jak modely dorazí ve výsledku pro daný vstup.
- Historie spuštění Azure ML ukládá snímek kódu, dat a výpočtů používaných pro výuku modelu.
- Registr modelu Azure ML zachycuje všechna metadata přidružená k vašemu modelu (které experiment vyškole, pokud je nasazený, pokud je jeho nasazení v pořádku).
- [Integrace s Azure](how-to-use-event-grid.md)  umožňuje působit na události v průběhu životního cyklu ml. Například události registrace modelu, nasazení, posunu dat a školení (spustit).

> [!TIP]
> I když jsou některé informace o modelech a datových sadách automaticky zachyceny, můžete přidat další informace pomocí __značek__. Při hledání registrovaných modelů a datových sad v pracovním prostoru můžete použít značky jako filtr.
>
> Přidružení datové sady k registrovanému modelu je volitelný krok. Informace o odkazování na datovou sadu při registraci modelu naleznete v tématu [model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true) Class reference.


## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Upozornění, automatizace a upozornění na události v životním cyklu ML
Azure ML publikuje klíčové události do Azure EventGrid, které se dají použít k oznamování a automatizaci událostí v životním cyklu ML. Další informace najdete v [tomto dokumentu](how-to-use-event-grid.md).


## <a name="monitor-for-operational--ml-issues"></a>Monitorování problémů s provozní & ML

Monitorování vám umožní pochopit, jaká data jsou odesílána do modelu, a předpovědi, který vrátí.

Tyto informace vám pomohou pochopit, jak je model používán. Shromážděná vstupní data mohou být užitečná také při výuce budoucích verzí modelu.

Další informace najdete v tématu [Jak povolit shromažďování dat modelu](how-to-enable-data-collection.md).

## <a name="retrain-your-model-on-new-data"></a>Přeučení modelu o nových datech

Často budete chtít model ověřit, aktualizovat nebo dokonce od začátku začít znovu, protože obdržíte nové informace. V některých případech je příjem nových dat očekávanou součástí domény. Jindy, jak je popsáno v tématu [detekce posunu dat (Preview) u datových sad](how-to-monitor-datasets.md), může výkon modelu zhoršit na tvář takových věcí jako změny konkrétního snímače, přirozené změny dat, jako jsou sezónní efekty, nebo funkce, které se v jejich vztahu k ostatním funkcím posunují. 

Neexistuje žádná univerzální odpověď na "Návody vědět, jestli by se měl přeškolit?" ale výše popsané nástroje pro události a monitorování Azure ML jsou dobré počáteční body pro automatizaci. Jakmile se rozhodnete přeškolit, měli byste: 

- Předzpracování dat pomocí opakovaného, automatizovaného procesu
- Výuka nového modelu
- Porovnejte výstupy nového modelu s původními modely.
- Pomocí předdefinovaných kritérií vyberte, jestli se má původní model nahradit. 

Motivem výše uvedených kroků je, že vaše rekurze by měla být automatizovaná, ne ad hoc. [Kanály Azure Machine Learning](concept-ml-pipelines.md) jsou vhodnou odpovědí k vytváření pracovních postupů souvisejících s přípravou, školením, ověřováním a nasazením dat. Přečtěte si [modely přeučení pomocí návrháře Azure Machine Learning](how-to-retrain-designer.md) , abyste zjistili, jak kanály a návrháře Azure Machine Learning se vejdou do scénáře přeškolení. 

## <a name="automate-the-ml-lifecycle"></a>Automatizace životního cyklu ML 

Pomocí GitHubu a Azure Pipelines můžete vytvořit proces průběžné integrace, který bude navlakovat do modelu. Když se v typickém scénáři zkontroluje data v úložišti Git pro projekt, kanál Azure spustí školicí běh. Výsledky spuštění je pak možné zkontrolovat, aby se zobrazily výkonnostní charakteristiky vyučeného modelu. Můžete také vytvořit kanál, který nasadí model jako webovou službu.

[Rozšíření Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) usnadňuje práci s Azure Pipelines. Poskytuje následující vylepšení pro Azure Pipelines:

* Umožňuje výběr pracovního prostoru při definování připojení služby.
* Umožňuje aktivované kanály vydání vyškolené modely vytvořené v školicím kanálu.

Další informace o použití Azure Pipelines s Azure Machine Learning najdete na následujících odkazech:

* [Průběžná integrace a nasazování modelů ML pomocí Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Azure Machine Learning úložiště MLOps](https://aka.ms/mlops) .
* [Azure Machine Learning úložiště MLOpsPython](https://github.com/Microsoft/MLOpspython) .

Pomocí Azure Data Factory můžete také vytvořit kanál pro příjem dat, který připraví data pro použití s školeními. Další informace najdete v tématu [kanál](how-to-cicd-data-ingestion.md)pro příjem dat.

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu o tom, jak číst a prozkoumat následující zdroje:

+ [Postup & nasazení modelů](how-to-deploy-and-where.md) pomocí Azure Machine Learning

+ [Kurz: nasazení modelu klasifikace image v ACI](tutorial-deploy-models-with-aml.md).

+ [Ucelené příklady MLOps úložiště](https://github.com/microsoft/MLOps)

+ [CI/CD modelů ML s Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)

+ Vytváření klientů využívajících [nasazený model](how-to-consume-web-service.md)

+ [Strojové učení ve velkém měřítku](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Referenční architektury Azure AI & zástupce osvědčených postupů](https://github.com/microsoft/AI)
