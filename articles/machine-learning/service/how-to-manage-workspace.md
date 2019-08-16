---
title: Vytváření a Správa pracovních prostorů
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak vytvářet, zobrazovat a odstraňovat pracovní prostory služby Azure Machine Learning na webu Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: 81e1104d71706194ba1c54e42722b4508df09091
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534848"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Vytvoření a správa pracovních prostorů služby Azure Machine Learning

V tomto článku budete vytvářet, zobrazení a odstranění [ **pracovních prostorů služby Azure Machine Learning** ](concept-workspace.md) na webu Azure Portal pro [služby Azure Machine Learning](overview-what-is-azure-ml.md).  Portál představuje nejjednodušší způsob, jak začít pracovat s pracovními prostory, ale jak se vaše potřeby mění nebo jsou požadavky na automatizaci, můžete také vytvořit a odstranit pracovní prostory [pomocí rozhraní](reference-azure-machine-learning-cli.md)příkazového řádku [s kódem Python](https://aka.ms/aml-sdk) nebo [prostřednictvím rozšíření vs Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning).

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Chcete-li vytvořit pracovní prostor, budete potřebovat předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze služby Azure Machine Learning](https://aka.ms/AMLFree) dnes

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="download-a-configuration-file"></a>Stažení konfiguračního souboru

1. Pokud budete vytvářet [virtuální počítač poznámkového bloku](tutorial-1st-experiment-sdk-setup.md#azure), tento krok přeskočte.

1. Pokud plánujete použít kód v místním prostředí, které odkazuje na tento pracovní prostor, vyberte **Stáhnout soubor config. JSON** v části **Přehled** pracovního prostoru.  

   ![Stažení souboru config. JSON](./media/how-to-manage-workspace/configure.png)
   
   Soubor umístěte do struktury adresáře pomocí skriptů Pythonu nebo poznámkových bloků Jupyter. Může být ve stejném adresáři, v podadresáři s názvem *. AzureML*nebo v nadřazeném adresáři. Při vytváření virtuálního počítače poznámkového bloku se tento soubor přidá do správného adresáře na VIRTUÁLNÍm počítači za vás.


## <a name="view"></a>Zobrazení pracovního prostoru

1. V levém horním rohu portálu, vyberte **všechny služby**.

1. Do pole Filtr **všechny služby** zadejte **Služba Machine Learning**.  

1. Vyberte **Machine Learning pracovní prostory služby**.

   ![Hledat pracovní prostor služby Azure Machine Learning](media/how-to-manage-workspace/all-services.png)

1. Prohlédněte si seznam pracovních prostorů nalezen. Můžete filtrovat podle předplatného, skupiny prostředků a umístění.  

1. Vyberte pracovní prostor, ve kterém chcete zobrazit jeho vlastnosti.
   ![Vlastnosti pracovního prostoru](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Odstranění pracovního prostoru

Použijte tlačítko Odstranit v horní části pracovního prostoru, který chcete odstranit.

  ![Tlačítko Odstranit](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další postup

Postupujte podle komplexně kurzu se dozvíte, jak používat pracovní prostor pro vytváření, trénování a nasazujte modely pomocí služby Azure Machine Learning.

> [!div class="nextstepaction"]
> [Kurz: Modely vlaků](tutorial-train-models-with-aml.md)
