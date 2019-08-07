---
title: Co je pracovní prostor
titleSuffix: Azure Machine Learning service
description: Pracovní prostor je prostředek nejvyšší úrovně pro službu Azure Machine Learning. Udržuje historii všech školicích běhů, včetně protokolů, metrik, výstupu a snímků vašich skriptů. Pomocí těchto informací určíte, který školicí běh vytváří nejlepší model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: fc3f88e627e0ce19195f6df947d4f11f5f8a73ae
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2019
ms.locfileid: "68772757"
---
# <a name="what-is-an-azure-machine-learning-service-workspace"></a>Co je pracovní prostor služby Azure Machine Learning?

Pracovní prostor je prostředek nejvyšší úrovně pro Azure Machine Learning službu a poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte při používání služby Azure Machine Learning.  Pracovní prostor uchovává historii všech školicích běhů, včetně protokolů, metrik, výstupu a snímků vašich skriptů. Pomocí těchto informací určíte, který školicí běh vytváří nejlepší model.  

Jakmile máte model, který chcete, zaregistrujete ho v pracovním prostoru. Pak použijete zaregistrovaný model a skripty bodování k nasazení do Azure Container Instances, službě Azure Kubernetes nebo k poli FPGA (Array-programovatelné pole brány) jako koncový bod HTTP na bázi REST. Model můžete také nasadit do zařízení Azure IoT Edge jako modul.

## <a name="taxonomy"></a>Taxonomie 

Taxonomie pracovního prostoru je znázorněna v následujícím diagramu:

[![Taxonomie pracovního prostoru](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Diagram znázorňuje následující komponenty pracovního prostoru:

+ Pracovní prostor může obsahovat [virtuální počítače](tutorial-1st-experiment-sdk-setup.md)s poznámkovým blokem, cloudové prostředky nakonfigurované s prostředím Python, které je nutné ke Azure Machine Learning spuštění
+ [Role uživatelů](how-to-assign-roles.md) umožňují sdílet pracovní prostor s ostatními uživateli, týmy nebo projekty.
+ [Cíle výpočetní](concept-azure-machine-learning-architecture.md#compute-targets) služby se používají ke spouštění experimentů.
+ Při vytváření pracovního prostoru jsou pro vás také vytvořeny [přidružené prostředky](#resources) .
+ Experimenty jsou školicími [testy](concept-azure-machine-learning-architecture.md#experiments) , které používáte k sestavení vašich modelů.  Experimenty můžete vytvářet a spouštět pomocí
    + [Sada SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
    + V Azure Portal část [automatizované experimenty strojového učení (Preview)](how-to-create-portal-experiments.md) .
    + [Rozhraní jazyka Visual (Preview)](ui-concept-visual-interface.md).
+ [Kanály](concept-azure-machine-learning-architecture.md#ml-pipelines) jsou opakovaně použitelné pracovní postupy pro školení a přeškolení modelu.
+ Datové [sady](concept-azure-machine-learning-architecture.md#datasets-and-datastores) pomáhají při správě dat, která používáte pro školení modelů a vytváření kanálů.
+ Jakmile budete mít model, který chcete nasadit, vytvoříte registrovaný model.
+ Pomocí registrovaného modelu a vyhodnocovacího skriptu vytvořte [nasazení](concept-azure-machine-learning-architecture.md#deployment).

## <a name="tools-for-workspace-interaction"></a>Nástroje pro interakci pracovního prostoru

S vaším pracovním prostorem můžete pracovat následujícími způsoby:

+ Na webu:
    + [Azure Portal](https://portal.azure.com)
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

Začněte pracovat se službou vytvořením [pracovního prostoru](setup-create-workspace.md).

## <a name="resources"></a>Přidružené prostředky

Když vytvoříte nový pracovní prostor, automaticky vytvoří několik prostředků Azure, které se používají v pracovním prostoru:

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Registruje kontejnery Docker, které používáte během školení a při nasazení modelu. Pro minimalizaci nákladů je ACR **opožděně načteno** , dokud se nevytvoří image nasazení.
+ [Účet Azure Storage](https://azure.microsoft.com/services/storage/): Slouží jako výchozí úložiště dat pro pracovní prostor.  Poznámkové bloky Jupyter, které se používají s vašimi virtuálními počítači poznámkových bloků, se také ukládají.
+ [Application Insights Azure](https://azure.microsoft.com/services/application-insights/): Ukládá informace o monitorování vašich modelů.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Ukládá tajné kódy používané výpočetními cíli a dalšími citlivými informacemi, které pracovní prostor potřebuje.

> [!NOTE]
> Kromě vytváření nových verzí můžete používat i existující služby Azure.

## <a name="next-steps"></a>Další postup

Pokud chcete začít používat službu Azure Machine Learning, přečtěte si:

+ [Přehled služby Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Vytvoření pracovního prostoru](setup-create-workspace.md)
+ [Správa pracovního prostoru](how-to-manage-workspace.md)
+ [Kurz: Výuka modelu](tutorial-train-models-with-aml.md)
