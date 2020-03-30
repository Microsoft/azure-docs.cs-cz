---
title: Co je pracovní prostor
titleSuffix: Azure Machine Learning
description: Pracovní prostor je prostředek nejvyšší úrovně pro Azure Machine Learning. Udržuje historii všech spuštění školení, včetně protokolů, metriky, výstup a snímek skriptů. Tyto informace slouží k určení, které školení spustit vytváří nejlepší model
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.openlocfilehash: 03bc49c24e3c2d32e97f3e5e03bd39da63428a6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505575"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Co je pracovní prostor Azure Machine Learning?

Pracovní prostor je prostředek nejvyšší úrovně pro Azure Machine Learning, který poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte při použití Azure Machine Learning.  Pracovní prostor uchovává historii všech spuštění školení, včetně protokolů, metriky, výstup a snímek skriptů. Tyto informace slouží k určení, které trénovací běh vytvoří nejlepší model.  

Jakmile máte model, který se vám líbí, zaregistrujete jej do pracovního prostoru. Potom použijete registrované model a vyhodnocovací skripty k nasazení do azure container instance, azure kubernetes service nebo pole programovatelné gate array (FPGA) jako koncový bod HTTP založené na REST. Model můžete také nasadit do zařízení Azure IoT Edge jako modul.

Dostupné ceny a funkce závisí na tom, jestli je pro pracovní prostor vybraná [základní nebo podniková edice.](overview-what-is-azure-ml.md#sku) Při [vytváření pracovního prostoru](#create-workspace)vyberete edici .  Můžete také [upgradovat](#upgrade) ze základní edice na verzi Enterprise.

## <a name="taxonomy"></a>Taxonomie 

Taxonomie pracovního prostoru je znázorněna v následujícím diagramu:

[![Taxonomie pracovního prostoru](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

Diagram znázorňuje následující součásti pracovního prostoru:

+ Pracovní prostor může obsahovat [výpočetní instance Azure Machine Learning](concept-compute-instance.md), cloudové prostředky nakonfigurované s prostředím Pythonu, které je nezbytné ke spuštění Azure Machine Learning.

+ [Uživatelské role](how-to-assign-roles.md) umožňují sdílet pracovní prostor s ostatními uživateli, týmy nebo projekty.
+ [Výpočetní cíle](concept-azure-machine-learning-architecture.md#compute-targets) se používají ke spuštění experimentů.
+ Při vytváření pracovního prostoru jsou vytvořeny také [přidružené zdroje](#resources) pro vás.
+ [Experimenty](concept-azure-machine-learning-architecture.md#experiments) jsou tréninkové běhy, které používáte k sestavení modelů.  
+ [Kanály](concept-azure-machine-learning-architecture.md#ml-pipelines) jsou opakovaně použitelné pracovní postupy pro školení a rekvalifikaci modelu.
+ [Datové sady](concept-azure-machine-learning-architecture.md#datasets-and-datastores) pomáhají při správě dat, která používáte pro trénování modelu a vytváření kanálu.
+ Jakmile máte model, který chcete nasadit, vytvořte registrovaný model.
+ K vytvoření [koncového bodu nasazení](concept-azure-machine-learning-architecture.md#endpoints)použijte registrovaný model a skript hodnocení .

## <a name="tools-for-workspace-interaction"></a>Nástroje pro interakci s pracovním prostorem

S pracovním prostorem můžete pracovat následujícími způsoby:

+ Na webu:
    + [Azure Machine Learning studio](https://ml.azure.com) 
    + [Návrhář Azure Machine Learning (preview)](concept-designer.md) – k dispozici jenom v pracovních prostorech [edice Enterprise.](overview-what-is-azure-ml.md#sku)
+ V jakémkoli prostředí Pythonu s [Sadou Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ V jakémkoli prostředí R s [Sadou Azure Machine Learning SDK pro R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Na příkazovém řádku pomocí [rozšíření cli Azure](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) Machine Learning

## <a name="machine-learning-with-a-workspace"></a>Strojové učení s pracovním prostorem

Úlohy strojového učení číst nebo zapisovat artefakty do pracovního prostoru.

+ Spusťte experiment pro trénování modelu - zapíše výsledky experimentu do pracovního prostoru.
+ Pomocí automatického ml trénovat model – zapisuje výsledky školení do pracovního prostoru.
+ Zaregistrujte model v pracovním prostoru.
+ Nasazení modelu – používá registrovaný model k vytvoření nasazení.
+ Vytvořte a spusťte opakovaně použitelné pracovní postupy.
+ Zobrazení artefaktů strojového učení, jako jsou experimenty, kanály, modely, nasazení.
+ Sledujte a monitorujte modely.

## <a name="workspace-management"></a>Správa pracovního prostoru

Můžete také provádět následující úlohy správy pracovního prostoru:

| Úloha správy pracovního prostoru   | Portál              | Studio | Python SDK / R SDK       | Rozhraní příkazového řádku        |
|---------------------------|---------|---------|------------|------------|
| Vytvoření pracovního prostoru        | **&check;**     | | **&check;** | **&check;** |
| Správa přístupu k pracovnímu prostoru    | **&check;**   || |  **&check;**    |
| Upgrade na edici Enterprise    | **&check;** | **&check;**  | |     |
| Vytváření a správa výpočetních prostředků    | **&check;**   | **&check;** | **&check;** |  **&check;**   |
| Vytvoření virtuálního počítače s poznámkovým blokem |   | **&check;** | |     |

> [!WARNING]
> Přesunutí pracovního prostoru Azure Machine Learning do jiného předplatného nebo přesunutí vlastního předplatného do nového tenanta není podporováno. To může způsobit chyby.

## <a name="create-a-workspace"></a><a name='create-workspace'></a>Vytvoření pracovního prostoru

Při vytváření pracovního prostoru se rozhodnete, zda jej chcete vytvořit pomocí [základní nebo podnikové edice](overview-what-is-azure-ml.md#sku). Edice určuje funkce dostupné v pracovním prostoru. Edice Enterprise vám mimo jiné poskytuje přístup k [návrháři Azure Machine Learning](concept-designer.md) a studiové verzi vytváření [experimentů automatizovaného strojového učení](tutorial-first-experiment-automated-ml.md).  Další podrobnosti a informace o cenách najdete v [tématu Azure Machine Learning pricing](https://azure.microsoft.com/pricing/details/machine-learning/).

Pracovní prostor lze vytvořit několika způsoby:  

* Použijte [portál Azure](how-to-manage-workspace.md) pro rozhraní point-and-click, které vás provede jednotlivými kroky.
* Použití [sady Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) k vytvoření pracovního prostoru za chodu ze skriptů Pythonu nebo poznámkových bloků Jupiteru
* Když potřebujete automatizovat nebo přizpůsobit vytváření pomocí podnikových standardů zabezpečení, použijte [šablonu Azure Resource Manager](how-to-create-workspace-template.md) nebo [cli Azure Machine Learning.](reference-azure-machine-learning-cli.md)
* Pokud pracujete v kódu Visual Studio, použijte [rozšíření VS Code](tutorial-setup-vscode-extension.md).

> [!NOTE]
> Název pracovního prostoru nerozlišuje malá a velká písmena.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Upgrade na edici Enterprise

Pracovní prostor můžete [upgradovat ze základní edice na edici Enterprise](how-to-manage-workspace.md#upgrade) pomocí portálu Azure Portal. Pracovní prostor edice Enterprise nelze přejít na pracovní prostor Basic Edition. 

## <a name="associated-resources"></a><a name="resources"></a>Přidružené zdroje

Když vytvoříte nový pracovní prostor, automaticky vytvoří několik prostředků Azure, které pracovní prostor používá:

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Registruje kontejnery dockeru, které používáte během školení a při nasazení modelu. Chcete-li minimalizovat náklady, ACR je **opožděné načtení,** dokud nejsou vytvořeny bitové kopie nasazení.
+ [Účet Azure Storage](https://azure.microsoft.com/services/storage/): Používá se jako výchozí úložiště dat pro pracovní prostor.  Poznámkové bloky Jupyter, které se používají s výpočetními instancemi Azure Machine Learning, se také ukládají zde.
+ [Azure Application Insights:](https://azure.microsoft.com/services/application-insights/)Ukládá informace o monitorování o vašich modelech.
+ [Azure Key Vault:](https://azure.microsoft.com/services/key-vault/)Ukládá tajné klíče, které používají výpočetní cíle a další citlivé informace, které je potřeba v pracovním prostoru.

> [!NOTE]
> Kromě vytváření nových verzí můžete také použít stávající služby Azure.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s Azure Machine Learning, přečtěte si hledět:

+ [Přehled Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Vytvoření pracovního prostoru](how-to-manage-workspace.md)
+ [Správa pracovního prostoru](how-to-manage-workspace.md)
+ [Kurz: Začínáme vytvářet první experiment ML pomocí sady Python SDK](tutorial-1st-experiment-sdk-setup.md)
+ [Kurz: Začínáme s Azure Machine Learning s R SDK](tutorial-1st-r-experiment.md)
+ [Kurz: Vytvoření prvního klasifikačního modelu s automatizovaným strojovým učením](tutorial-first-experiment-automated-ml.md) (k dispozici pouze v pracovních prostorech [edice Enterprise)](overview-what-is-azure-ml.md#sku)
+ [Kurz: Předvídání ceny automobilu s návrhářem](tutorial-designer-automobile-price-train-score.md) (k dispozici pouze v pracovních prostorech [edice Enterprise)](overview-what-is-azure-ml.md#sku)
