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
ms.date: 09/22/2020
ms.openlocfilehash: 7185adf559f429feb0ada60fef65e1edb106da66
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907628"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Co je Azure Machine Learning pracovní prostor?

Pracovní prostor je prostředek nejvyšší úrovně pro Azure Machine Learning, který poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte při použití Azure Machine Learning.  Pracovní prostor uchovává historii všech školicích běhů, včetně protokolů, metrik, výstupu a snímků vašich skriptů. Pomocí těchto informací určíte, který školicí běh vytváří nejlepší model.  

Jakmile máte model, který chcete, zaregistrujete ho v pracovním prostoru. Pak použijete zaregistrovaný model a skripty bodování k nasazení do Azure Container Instances, službě Azure Kubernetes nebo k poli FPGA (Array-programovatelné pole brány) jako koncový bod HTTP na bázi REST. Model můžete také nasadit do zařízení Azure IoT Edge jako modul.

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
    + [Návrhář služby Azure Machine Learning](concept-designer.md) 
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
| Vytváření a Správa výpočetních prostředků    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Vytvoření virtuálního počítače s poznámkovým blokem |   | **&check;** | |     ||

> [!WARNING]
> Přesunutím pracovního prostoru Azure Machine Learning do jiného předplatného nebo přesunutím vlastnícího předplatného na nového tenanta se nepodporuje. V takovém případě může dojít k chybám.

## <a name="create-a-workspace"></a><a name='create-workspace'></a> Vytvořit pracovní prostor

Existuje několik způsobů, jak vytvořit pracovní prostor:  

* Pomocí [Azure Portal](how-to-manage-workspace.md) pro rozhraní Point-to-Click vás provede jednotlivé kroky.
* Použití [sady SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#&preserve-view=trueworkspace) k vytvoření pracovního prostoru ze skriptů Pythonu nebo poznámkových bloků Jupiter
* Pokud potřebujete automatizovat nebo přizpůsobovat vytváření pomocí standardů podnikových zabezpečení, použijte [šablonu Azure Resource Manager](how-to-create-workspace-template.md) nebo [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) .
* Pokud pracujete v Visual Studio Code, použijte [rozšíření vs Code](how-to-manage-resources-vscode.md#create-a-workspace).

> [!NOTE]
> V názvu pracovního prostoru se nerozlišují malá a velká písmena.

## <a name="associated-resources"></a><a name="resources"></a> Přidružené prostředky

Když vytvoříte nový pracovní prostor, automaticky se vytvoří několik prostředků Azure, které pracovní prostor používá:

+ [Azure Storage účet](https://azure.microsoft.com/services/storage/): slouží jako výchozí úložiště dat pro pracovní prostor.  Jupyter poznámkové bloky používané s vašimi výpočetními instancemi Azure Machine Learning jsou také uloženy. 
  
  > [!IMPORTANT]
  > Ve výchozím nastavení je účet úložiště účet pro obecné účely v1. Po vytvoření pracovního prostoru můžete [tuto verzi upgradovat na obecné účely verze 2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) . Po upgradu na obecné účely v2 nepovolujte v účtu úložiště hierarchický obor názvů.

  Pokud chcete použít existující účet Azure Storage, nemůže to být účet Premium (Premium_LRS a Premium_GRS). Nemůže mít také hierarchický obor názvů (používá se s Azure Data Lake Storage Gen2). V rámci _výchozího_ účtu úložiště pracovního prostoru nejsou podporovány ani úrovně Premium Storage ani hierarchické obory názvů. Můžete použít Storage úrovně Premium nebo hierarchický obor názvů s účty úložiště, _které nejsou výchozí_ .
  
+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): registruje kontejnery Docker, které používáte během školení a při nasazení modelu. Pro minimalizaci nákladů je ACR **opožděně načteno** , dokud se nevytvoří image nasazení.

+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): ukládá informace o monitorování vašich modelů.

+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): ukládá tajné kódy používané výpočetními cíli a dalšími citlivými informacemi, které pracovní prostor potřebuje.

> [!NOTE]
> Kromě vytváření nových verzí můžete používat i existující služby Azure.

<a name="wheres-enterprise"></a>

## <a name="what-happened-to-enterprise-edition"></a>Co se stalo se standardem Enterprise Edition

Od září 2020 jsou teď všechny funkce dostupné v pracovních prostorech Enterprise Edition dostupné i v pracovních prostorech Basic Edition. Nové pracovní prostory v podniku již nelze vytvořit.  Všechna volání sady SDK, rozhraní příkazového řádku nebo Azure Resource Manager, která používají `sku` parametr, budou i nadále fungovat, ale bude zřízen základní pracovní prostor.

Od 21. prosince budou všechny pracovní prostory edice Enterprise Edition automaticky nastaveny na základní edici, která má stejné možnosti. Během tohoto procesu nedojde k žádnému výpadku. Od 1. ledna 2021 bude edice Enterprise vyřazená z provozu. 

V obou edicích jsou zákazníci odpovědni za náklady na spotřebované prostředky Azure a nebudou muset platit žádné další poplatky za Azure Machine Learning. Další podrobnosti najdete na [stránce s cenami Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/) .

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s Azure Machine Learning, přečtěte si:

+ [Přehled Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Vytvoření pracovního prostoru](how-to-manage-workspace.md)
+ [Správa pracovního prostoru](how-to-manage-workspace.md)
+ [Kurz: Začínáme s vytvářením prvního experimentu ML pomocí sady Python SDK](tutorial-1st-experiment-sdk-setup.md)
+ [Kurz: Začínáme s Azure Machine Learning s využitím sady R SDK](tutorial-1st-r-experiment.md)
+ [Kurz: vytvoření prvního modelu klasifikace pomocí automatizovaného strojového učení](tutorial-first-experiment-automated-ml.md) 
+ [Kurz: předpověď ceny automobilu pomocí návrháře](tutorial-designer-automobile-price-train-score.md)
