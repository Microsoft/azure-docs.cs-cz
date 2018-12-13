---
title: Vytvoření a správa pracovních prostorů služby Azure Machine Learning
description: Zjistěte, jak vytvářet, zobrazovat a odstraňovat pracovní prostory služby Azure Machine Learning na webu Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 647af189319a1fa7b80b10410ad96f3297c18192
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53083763"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Vytvoření a správa pracovních prostorů služby Azure Machine Learning

V tomto článku budete vytvářet, zobrazení a odstranění [ **pracovních prostorů služby Azure Machine Learning** ](concept-azure-machine-learning-architecture.md#workspace) na webu Azure Portal pro [služby Azure Machine Learning](overview-what-is-azure-ml.md).  Můžete také vytvářet a odstraňovat pracovní prostory [pomocí rozhraní příkazového řádku](reference-azure-machine-learning-cli.md) nebo [pomocí kódu Python](https://aka.ms/aml-sdk).

Chcete-li vytvořit pracovní prostor, budete potřebovat předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLfree) před tím, než začnete.

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view-a-workspace"></a>Zobrazení pracovního prostoru

1. V levém horním rohu portálu, vyberte **všechny služby**. 

1. V **všechny služby** pole filtru, typu **pracovní prostor služby Machine Learning**.  

   ![Hledat pracovní prostor služby Azure Machine Learning](media/how-to-manage-workspace/allservices-search1.png)

1. Ve výsledcích filtru vybrat **pracovní prostor služby Machine Learning** zobrazíte seznam vašich pracovních prostorů. 

   ![Seznam pracovních prostorů služby Azure Machine Learning](media/how-to-manage-workspace/allservices-search.PNG)

1. Prohlédněte si seznam pracovních prostorů nalezen. Můžete filtrovat podle předplatného, skupiny prostředků a umístění.  

   ![Zobrazit pracovní prostory](media/how-to-manage-workspace/allservices_view_workspace.PNG)

1. Vyberte pracovní prostor, který jste právě vytvořili, zobrazíte její vlastnosti.

   ![PNG](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Odstranění pracovního prostoru

Použijte tlačítko Odstranit v horní části pracovního prostoru, který chcete odstranit.

  ![Tlačítko Odstranit](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další postup

Postupujte podle komplexně kurzu se dozvíte, jak používat pracovní prostor pro vytváření, trénování a nasazujte modely pomocí služby Azure Machine Learning.

> [!div class="nextstepaction"]
> [Kurz: Trénování modelů](tutorial-train-models-with-aml.md)