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
ms.date: 08/06/2019
ms.openlocfilehash: 677a52572658f80d9cde4de28dd02d6e957407d2
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269210"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Co je Azure Machine Learning pracovní prostor?

Pracovní prostor je prostředek nejvyšší úrovně pro Azure Machine Learning, který poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte při použití Azure Machine Learning.  Pracovní prostor uchovává historii všech školicích běhů, včetně protokolů, metrik, výstupu a snímků vašich skriptů. Pomocí těchto informací určíte, který školicí běh vytváří nejlepší model.  

Jakmile máte model, který chcete, zaregistrujete ho v pracovním prostoru. Pak použijete zaregistrovaný model a skripty bodování k nasazení do Azure Container Instances, službě Azure Kubernetes nebo k poli FPGA (Array-programovatelné pole brány) jako koncový bod HTTP na bázi REST. Model můžete také nasadit do zařízení Azure IoT Edge jako modul.

## <a name="taxonomy"></a>Taxonomie 

Taxonomie pracovního prostoru je znázorněna v následujícím diagramu:

[![Taxonomie pracovního prostoru](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Diagram znázorňuje následující komponenty pracovního prostoru:

+ Pracovní prostor může obsahovat [virtuální počítače s poznámkovým blokem](tutorial-1st-experiment-sdk-setup.md), cloudové prostředky nakonfigurované s prostředím Python, které je nutné ke Azure Machine Learning spuštění
+ [Role uživatelů](how-to-assign-roles.md) umožňují sdílet pracovní prostor s ostatními uživateli, týmy nebo projekty.
+ [Cíle výpočetní](concept-azure-machine-learning-architecture.md#compute-targets) služby se používají ke spouštění experimentů.
+ Při vytváření pracovního prostoru jsou pro vás také vytvořeny [přidružené prostředky](#resources) .
+ [Experimenty](concept-azure-machine-learning-architecture.md#experiments) jsou školicími testy, které používáte k sestavení vašich modelů.  Experimenty můžete vytvářet a spouštět pomocí
    + [Sada SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
    + Část [automatizované experimenty strojového učení (Preview)](how-to-create-portal-experiments.md) v Azure Portal nebo na cílové stránce pracovního prostoru (Preview).
    + [Rozhraní jazyka Visual (Preview)](ui-concept-visual-interface.md).
+ [Kanály](concept-azure-machine-learning-architecture.md#ml-pipelines) jsou opakovaně použitelné pracovní postupy pro školení a přeškolení modelu.
+ Datové [sady](concept-azure-machine-learning-architecture.md#datasets-and-datastores) pomáhají při správě dat, která používáte pro školení modelů a vytváření kanálů.
+ Jakmile budete mít model, který chcete nasadit, vytvoříte registrovaný model.
+ Pomocí registrovaného modelu a vyhodnocovacího skriptu vytvořte [nasazení](concept-azure-machine-learning-architecture.md#deployment).

## <a name="tools-for-workspace-interaction"></a>Nástroje pro interakci pracovního prostoru

S vaším pracovním prostorem můžete pracovat následujícími způsoby:

+ Na webu:
    + [Azure Portal](https://portal.azure.com)
    + [Cílová stránka pracovního prostoru (Preview)](https://ml.azure.com)
    + [Vizuální rozhraní (Preview)](ui-concept-visual-interface.md)
+ V Pythonu s využitím Azure Machine Learning [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ Na příkazovém řádku pomocí [rozšíření Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)

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

| Úloha správy pracovního prostoru   | Portál              | Sada SDK        | Rozhraní příkazového řádku        |
|---------------------------|------------------|------------|------------|
| Vytvoření pracovního prostoru        | **&check;**     | **&check;** | **&check;** |
| Vytváření a Správa výpočetních prostředků    | **&check;**   | **&check;** |  **&check;**   |
| Správa přístupu k pracovnímu prostoru    | **&check;**   | |  **&check;**    |
| Vytvoření virtuálního počítače s poznámkovým blokem | **&check;**   | |     |

### <a name='create-workspace'></a>Vytvořit pracovní prostor

Existuje několik způsobů, jak vytvořit pracovní prostor.

* Pomocí [Azure Portal](how-to-manage-workspace.md) pro rozhraní Point-to-Click vás provede jednotlivé kroky.
* Použití [sady SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) k vytvoření pracovního prostoru ze skriptů Pythonu nebo poznámkových bloků Jupiter
* Pokud potřebujete automatizovat nebo přizpůsobovat vytváření pomocí standardů podnikových zabezpečení, použijte [šablonu Azure Resource Manager](how-to-create-workspace-template.md) nebo [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) .
* Pokud pracujete v Visual Studio Code, použijte [rozšíření vs Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="resources"></a>Přidružené prostředky

Když vytvoříte nový pracovní prostor, automaticky vytvoří několik prostředků Azure, které se používají v pracovním prostoru:

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Registruje kontejnery Docker, které používáte během školení a při nasazení modelu. Pro minimalizaci nákladů je ACR **opožděně načteno** , dokud se nevytvoří image nasazení.
+ [Účet Azure Storage](https://azure.microsoft.com/services/storage/): Slouží jako výchozí úložiště dat pro pracovní prostor.  Poznámkové bloky Jupyter, které se používají s vašimi virtuálními počítači poznámkových bloků, se také ukládají.
+ [Application Insights Azure](https://azure.microsoft.com/services/application-insights/): Ukládá informace o monitorování vašich modelů.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Ukládá tajné kódy používané výpočetními cíli a dalšími citlivými informacemi, které pracovní prostor potřebuje.

> [!NOTE]
> Kromě vytváření nových verzí můžete používat i existující služby Azure.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s Azure Machine Learning, přečtěte si:

+ [Přehled Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Vytvoření pracovního prostoru](how-to-manage-workspace.md)
+ [Správa pracovního prostoru](how-to-manage-workspace.md)
+ [Kurz: Výuka modelu](tutorial-train-models-with-aml.md)
