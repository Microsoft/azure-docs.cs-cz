---
title: Vytváření pracovních prostorů Azure ML na portálu
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet, zobrazovat a odstraňovat Azure Machine Learning pracovní prostory v Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: 511c737e160c0f0753e570314c9b29346972cb04
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "71269251"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Vytváření a Správa pracovních prostorů Azure Machine Learning v Azure Portal

V tomto článku vytvoříte, zobrazíte a odstraníte [**Azure Machine Learning pracovní prostory**](concept-workspace.md) v Azure Portal pro [Azure Machine Learning](overview-what-is-azure-ml.md).  Portál představuje nejjednodušší způsob, jak začít pracovat s pracovními prostory, ale jak se vaše potřeby mění nebo jsou požadavky na automatizaci, můžete také vytvořit a odstranit pracovní prostory [pomocí rozhraní](reference-azure-machine-learning-cli.md)příkazového řádku [s kódem Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) nebo [prostřednictvím rozšíření vs Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

K vytvoření pracovního prostoru potřebujete předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="download-a-configuration-file"></a>Stažení konfiguračního souboru

1. Pokud budete vytvářet [virtuální počítač poznámkového bloku](tutorial-1st-experiment-sdk-setup.md#azure), tento krok přeskočte.

1. Pokud plánujete použít kód v místním prostředí, které odkazuje na tento pracovní prostor, vyberte **Stáhnout soubor config. JSON** v části **Přehled** pracovního prostoru.  

   ![Stažení souboru config. JSON](./media/how-to-manage-workspace/configure.png)
   
   Soubor umístěte do struktury adresáře pomocí skriptů Pythonu nebo poznámkových bloků Jupyter. Může být ve stejném adresáři, v podadresáři s názvem *. AzureML*nebo v nadřazeném adresáři. Při vytváření virtuálního počítače poznámkového bloku se tento soubor přidá do správného adresáře na VIRTUÁLNÍm počítači za vás.


## <a name="view"></a>Zobrazení pracovního prostoru

1. V levém horním rohu portálu vyberte **všechny služby**.

1. Do pole Filtr **všechny služby** zadejte **Služba Machine Learning**.  

1. Vyberte **Machine Learning pracovní prostory služby**.

   ![Vyhledat Azure Machine Learning pracovní prostor](media/how-to-manage-workspace/all-services.png)

1. Prohlédněte si seznam pracovních prostorů, které se našly. Můžete filtrovat podle předplatného, skupin prostředků a umístění.  

1. Vyberte pracovní prostor, ve kterém chcete zobrazit jeho vlastnosti.
   vlastnosti ![Workspace ](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Odstranění pracovního prostoru

Použijte tlačítko Odstranit v horní části pracovního prostoru, který chcete odstranit.

  ![Tlačítko Odstranit](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

Postupujte podle kurzu pro celou délku a Naučte se používat pracovní prostor k sestavování, vytváření výukových a nasazování modelů pomocí Azure Machine Learning.

> [!div class="nextstepaction"]
> [Kurz: modely vlaků](tutorial-train-models-with-aml.md)
