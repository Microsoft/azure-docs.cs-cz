---
title: Co je pracovní prostor
titleSuffix: Azure Machine Learning
description: Pracovní prostor je prostředek nejvyšší úrovně pro Azure Machine Learning. Udržuje historii všech školicích běhů, včetně protokolů, metrik, výstupu a snímků vašich skriptů. Pomocí těchto informací určíte, který školicí běh vytváří nejlepší model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/08/2020
ms.openlocfilehash: e765422ebfce1a4328bac9a17edb8b581f87e6f7
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661709"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Co je Azure Machine Learning pracovní prostor?

Pracovní prostor je prostředek nejvyšší úrovně pro Azure Machine Learning, který poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte při použití Azure Machine Learning.  Pracovní prostor uchovává historii všech školicích běhů, včetně protokolů, metrik, výstupu a snímků vašich skriptů. Pomocí těchto informací určíte, který školicí běh vytváří nejlepší model.  

Jakmile máte model, který chcete, zaregistrujete ho v pracovním prostoru. Pak použijete zaregistrovaný model a skripty bodování k nasazení do Azure Container Instances, službě Azure Kubernetes nebo k poli FPGA (Array-programovatelné pole brány) jako koncový bod HTTP na bázi REST. Model můžete také nasadit do zařízení Azure IoT Edge jako modul.

Dostupné ceny a funkce závisí na tom, jestli je pro tento pracovní prostor vybraná možnost [Basic nebo Enterprise Edition](overview-what-is-azure-ml.md#sku) . Edici vyberete při [vytváření pracovního prostoru](#create-workspace).  Můžete také [upgradovat](#upgrade) z edice Basic na Enterprise.

## <a name="taxonomy"></a>Taxonomie 

Taxonomie pracovního prostoru je znázorněna v následujícím diagramu:

[![Taxonomie pracovního prostoru](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

Diagram znázorňuje následující komponenty pracovního prostoru:

+ Pracovní prostor může obsahovat [Azure Machine Learning výpočetních instancí](concept-compute-instance.md), cloudových prostředků nakonfigurovaných pomocí prostředí Pythonu, které je nezbytné ke spuštění Azure Machine Learning.

+ [Role uživatelů](how-to-assign-roles.md) umožňují sdílet pracovní prostor s ostatními uživateli, týmy nebo projekty.
+ [Cíle výpočetní](concept-azure-machine-learning-architecture.md#compute-targets) služby se používají ke spouštění experimentů.
+ Při vytváření pracovního prostoru jsou pro vás také vytvořeny [přidružené prostředky](#resources) .
+ [Experimenty](concept-azure-machine-learning-architecture.md#experiments) jsou školicími testy, které používáte k sestavení vašich modelů.  
+ [Kanály](concept-azure-machine-learning-architecture.md#ml-pipelines) jsou opakovaně použitelné pracovní postupy pro školení a přeškolení modelu.
+ Datové [sady](concept-azure-machine-learning-architecture.md#datasets-and-datastores) pomáhají při správě dat, která používáte pro školení modelů a vytváření kanálů.
+ Jakmile budete mít model, který chcete nasadit, vytvoříte registrovaný model.
+ Pomocí registrovaného modelu a vyhodnocovacího skriptu vytvořte [koncový bod nasazení](concept-azure-machine-learning-architecture.md#endpoints).

## <a name="tools-for-workspace-interaction"></a>Nástroje pro interakci pracovního prostoru

S vaším pracovním prostorem můžete pracovat následujícími způsoby:

> [!IMPORTANT]
> Nástroje označené (Preview) jsou momentálně ve verzi Public Preview.
> Verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+ Na webu:
    + [Azure Machine Learning Studio ](https://ml.azure.com) 
    + [Azure Machine Learning Designer (Preview)](concept-designer.md) – dostupné jenom v pracovních prostorech [Enterprise Edition](overview-what-is-azure-ml.md#sku) .
+ V jakémkoli prostředí Pythonu s [Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true).
+ V jakémkoli prostředí R s [Azure Machine Learning SDK pro R (Preview)](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Na příkazovém řádku pomocí [rozšíření Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli)
+ [Rozšíření Azure Machine Learning VS Code](how-to-manage-resources-vscode.md#workspaces)


## <a name="machine-learning-with-a-workspace"></a>Strojové učení s pracovním prostorem

Úlohy strojového učení čtou a zapisují artefakty do vašeho pracovního prostoru.

+ Spusťte experiment pro výuku modelu – zapisuje výsledky experimentálního spuštění do pracovního prostoru.
+ Použití automatizovaného ML k výuce modelu – zapisuje výsledky školení do pracovního prostoru.
+ Zaregistrujte model v pracovním prostoru.
+ Nasazení modelu – pomocí registrovaného modelu vytvoří nasazení.
+ Vytvoření a spuštění opakovaně použitelných pracovních postupů.
+ Zobrazit artefakty strojového učení, jako jsou experimenty, kanály, modely a nasazení.
+ Sledujte a monitorujte modely.

## <a name="workspace-management"></a>Správa pracovního prostoru

Můžete také provádět následující úlohy správy pracovního prostoru:

| Úloha správy pracovního prostoru   | Portál              | Studio | Python SDK/R SDK       | Rozhraní příkazového řádku        | VS Code
|---------------------------|---------|---------|------------|------------|------------|
| Vytvoření pracovního prostoru        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| Správa přístupu k pracovnímu prostoru    | **&check;**   || |  **&check;**    ||
| Upgrade na Enterprise Edition    | **&check;** | **&check;**  | |     ||
| Vytváření a Správa výpočetních prostředků    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Vytvoření virtuálního počítače s poznámkovým blokem |   | **&check;** | |     ||

> [!WARNING]
> Přesunutím pracovního prostoru Azure Machine Learning do jiného předplatného nebo přesunutím vlastnícího předplatného na nového tenanta se nepodporuje. V takovém případě může dojít k chybám.

## <a name="create-a-workspace"></a><a name='create-workspace'></a> Vytvořit pracovní prostor

Když vytváříte pracovní prostor, rozhodujete se, jestli se má vytvořit se systémem [Basic nebo Enterprise Edition](overview-what-is-azure-ml.md#sku). Edice určuje funkce, které jsou k dispozici v pracovním prostoru. Kromě dalších funkcí vám edice Enterprise dává přístup k [Azure Machine Learning designeru](concept-designer.md) a verzi studia pro vytváření [automatizovaných experimentů strojového učení](tutorial-first-experiment-automated-ml.md).  Další informace a informace o cenách najdete v článku [Azure Machine Learning ceny](https://azure.microsoft.com/pricing/details/machine-learning/).

Existuje několik způsobů, jak vytvořit pracovní prostor:  

* Pomocí [Azure Portal](how-to-manage-workspace.md) pro rozhraní Point-to-Click vás provede jednotlivé kroky.
* Použití [sady SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#&preserve-view=trueworkspace) k vytvoření pracovního prostoru ze skriptů Pythonu nebo poznámkových bloků Jupiter
* Pokud potřebujete automatizovat nebo přizpůsobovat vytváření pomocí standardů podnikových zabezpečení, použijte [šablonu Azure Resource Manager](how-to-create-workspace-template.md) nebo [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) .
* Pokud pracujete v Visual Studio Code, použijte [rozšíření vs Code](how-to-manage-resources-vscode.md#create-a-workspace).

> [!NOTE]
> V názvu pracovního prostoru se nerozlišují malá a velká písmena.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a> Upgrade na Enterprise Edition

[Pracovní prostor můžete upgradovat ze sady Basic na Enterprise Edition](how-to-manage-workspace.md#upgrade) pomocí Azure Portal. Pracovní prostor Enterprise Edition nelze downgradovat na pracovní prostor edice Basic. 

## <a name="associated-resources"></a><a name="resources"></a> Přidružené prostředky

Když vytvoříte nový pracovní prostor, automaticky se vytvoří několik prostředků Azure, které pracovní prostor používá:

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): registruje kontejnery Docker, které používáte během školení a při nasazení modelu. Pro minimalizaci nákladů je ACR **opožděně načteno** , dokud se nevytvoří image nasazení.
+ [Azure Storage účet](https://azure.microsoft.com/services/storage/): slouží jako výchozí úložiště dat pro pracovní prostor.  Jupyter poznámkové bloky používané s vašimi výpočetními instancemi Azure Machine Learning jsou také uloženy.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): ukládá informace o monitorování vašich modelů.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): ukládá tajné kódy používané výpočetními cíli a dalšími citlivými informacemi, které pracovní prostor potřebuje.

> [!NOTE]
> Kromě vytváření nových verzí můžete používat i existující služby Azure.

### <a name="azure-storage-account"></a>Účet služby Azure Storage

Účet Azure Storage vytvořený ve výchozím nastavení v pracovním prostoru je účet pro obecné účely v1. Po vytvoření pracovního prostoru podle kroků v článku [upgrade na účet úložiště pro obecné účely v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) můžete tento krok upgradovat na obecné účely verze 2.

> [!IMPORTANT]
> Po upgradu na obecné účely v2 nepovolujte v účtu úložiště hierarchický obor názvů.

Pokud chcete použít existující účet Azure Storage, nemůže to být účet Premium (Premium_LRS a Premium_GRS). Nemůže mít také hierarchický obor názvů (používá se s Azure Data Lake Storage Gen2). V rámci _výchozího_ účtu úložiště pracovního prostoru nejsou podporovány ani úrovně Premium Storage ani hierarchické obory názvů. Můžete použít Storage úrovně Premium nebo hierarchický obor názvů s účty úložiště, _které nejsou výchozí_ .



## <a name="next-steps"></a>Další kroky

Pokud chcete začít s Azure Machine Learning, přečtěte si:

+ [Přehled Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Vytvoření pracovního prostoru](how-to-manage-workspace.md)
+ [Správa pracovního prostoru](how-to-manage-workspace.md)
+ [Kurz: Začínáme s vytvářením prvního experimentu ML pomocí sady Python SDK](tutorial-1st-experiment-sdk-setup.md)
+ [Kurz: Začínáme s Azure Machine Learning s využitím sady R SDK](tutorial-1st-r-experiment.md)
+ [Kurz: vytvoření prvního modelu klasifikace pomocí automatizovaného strojového učení](tutorial-first-experiment-automated-ml.md) (dostupné jenom v pracovních prostorech [Enterprise Edition](overview-what-is-azure-ml.md#sku) )
+ [Kurz: předpověď ceny automobilu pomocí návrháře](tutorial-designer-automobile-price-train-score.md) (dostupné jenom v pracovních prostorech [Enterprise Edition](overview-what-is-azure-ml.md#sku) )
