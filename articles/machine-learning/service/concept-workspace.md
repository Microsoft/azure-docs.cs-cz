---
title: Co je pracovní prostor
titleSuffix: Azure Machine Learning service
description: Zjistěte, jaké pracovní prostor je a proč potřebujete jeden pro službu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 5785bf8f6538f1d91e7a23178e29487ebee14f29
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989838"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Co je pracovní prostor služby Azure Machine Learning?

Pracovní prostor je prostředek nejvyšší úrovně pro službu Azure Machine Learning. Poskytuje centrální místo, kde můžete pracovat s všechny artefakty, které vytvoříte, když používáte službu Azure Machine Learning.

Pracovní prostor uchovává historii tréninkových spuštění, včetně protokolů, metrik, výstupu a snímek vašich skriptů. Tyto informace použít k určení, které školení vznikly nejlepší model.  

Jakmile budete mít modelu, který rádi používáte, zaregistrujte s pracovním prostorem. Použijete registrovanému modelu a hodnoticí skripty k nasazení do služby Azure Container Instances, Azure Kubernetes Service, nebo pole programmable gate array (FPGA) jako koncový bod HTTP založené na protokolu REST. Můžete taky nasadit model pro zařízení Azure IoT Edge jako modul.

## <a name="taxonomy"></a>Taxonomie 

Taxonomie pracovního prostoru je znázorněn v následujícím diagramu:

[![Taxonomie pracovního prostoru](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Diagram znázorňuje následující součásti pracovního prostoru:

+ Pracovní prostor může obsahovat [virtuálních počítačů Poznámkový blok](quickstart-run-cloud-notebook.md), cloudové prostředky, které nakonfigurují prostředí Pythonu potřebné ke spuštění Azure Machine Learning.
+ [Role uživatelů](how-to-assign-roles.md) umožňují sdílet s ostatními uživateli, týmy nebo projekty pracovního prostoru.
+ [Cílových výpočetních prostředí](concept-azure-machine-learning-architecture.md#compute-target) umožňují spouštět své experimenty.
+ Když vytváříte pracovní prostor, [přidružené prostředky](#resources) se také vytvoří za vás.
+ [Experimenty](concept-azure-machine-learning-architecture.md#experiment) jsou tréninkových spuštění použijete k sestavení modelů.  Můžete vytvářet a spouštět experimenty s
    + [Azure Machine Learning pro Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
    + [Automatizované experimenty machine learning (preview)](how-to-create-portal-experiments.md) části webu Azure Portal.
    + [Vizuální rozhraní (preview)](ui-concept-visual-interface.md).
+ [Kanály](concept-azure-machine-learning-architecture.md#pipeline) jsou opakovaně použitelných pracovních postupů pro trénování a přetrénování modelu.
+ [Datové sady](concept-azure-machine-learning-architecture.md#dataset) podpory v správy dat můžete použít pro vytvoření modelu školení a kanálu.
+ Jakmile budete mít modelu chcete nasadit, můžete vytvořit [registrovanému modelu](concept-azure-machine-learning-architecture.md#model-registry).
+ Vytvoření pomocí registrovaného modelu a hodnoticí skript [nasazení](concept-azure-machine-learning-architecture.md#image-registry).

## <a name="tools-for-workspace-interaction"></a>Nástroje pro interakci pracovního prostoru

Můžete pracovat s pracovním prostorem následujícími způsoby:

+ Na webu:
    + [Azure Portal](https://azure.portal.com)
    + [Vizuální rozhraní (preview)](ui-concept-visual-interface.md)
+ V Pythonu pomocí služby Azure Machine Learning [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ Na příkazovém řádku pomocí Azure Machine Learning [rozšíření rozhraní příkazového řádku](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>Strojové učení s pracovním prostorem

Úlohy strojového učení čtení nebo zápisu artefakty do pracovního prostoru. 

+ Spusťte experiment k natrénování modelu – zápisy experimentovat spuštění výsledky do pracovního prostoru.
+ Použijte automatické ML k natrénování modelu – zapíše školení výsledky do pracovního prostoru.
+ Zaregistrujte model v pracovním prostoru.
+ Nasazení modelu – k vytvoření nasazení používá registrovaného modelu.
+ Vytvářet a spouštět opakovaně použitelných pracovních postupů.
+ Zobrazení strojového učení součásti, jako jsou pokusy, kanály, modely, nasazení.
+ Sledování a monitorování modely.

## <a name="workspace-management"></a>Správa pracovních prostorů

Můžete také provádět následující úlohy správy pracovního prostoru:

| Pracovní prostor Správa úloh   | Portál              | SDK        | CLI        |
|---------------------------|------------------|------------|------------|
| Vytvoření pracovního prostoru        | **&check;**     | **&check;** | **&check;** |
| Vytváření a správě výpočetních prostředků    | **&check;**   | **&check;** |  **&check;**   |
| Spravovat přístup k pracovnímu prostoru    | **&check;**   | |  **&check;**    |
| Vytvoření poznámkového bloku virtuálního počítače | **&check;**   | |     |

Začínáme se službou tak [vytvoření pracovního prostoru](setup-create-workspace.md).

## <a name="resources"></a> Přidružené prostředky

Když vytvoříte nový pracovní prostor, automaticky vytvoří několik prostředků Azure, které se používají v pracovním prostoru:

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Registry kontejnerů dockeru, které použijete během cvičení a nasazení modelu. Chcete-li minimalizovat náklady, je služba ACR **opožděné načtení** dokud se nevytvoří nasazení bitové kopie.
+ [Účet služby Azure Storage](https://azure.microsoft.com/services/storage/): Slouží jako výchozí úložiště pro pracovní prostor.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): Ukládá informace o vašich modelů monitorování.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Tajné klíče úložiště, které jsou používány výpočetní cíle a dalších citlivých údajů, který je nezbytný v pracovním prostoru.

> [!NOTE]
> Kromě vytvoření nové verze, můžete také použít stávající služby Azure.

## <a name="next-steps"></a>Další postup

Abyste mohli začít se službou Azure Machine Learning, naleznete v tématu:

+ [Přehled služby Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Vytvoření pracovního prostoru](setup-create-workspace.md)
+ [Správa pracovního prostoru](how-to-manage-workspace.md)
+ [Kurz: Trénování modelu](tutorial-train-models-with-aml.md)
