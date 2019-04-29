---
title: Vytvoření a správa pracovních prostorů
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak vytvářet, zobrazovat a odstraňovat pracovní prostory služby Azure Machine Learning na webu Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: e65f739a9641181381205c7255d0472325e8055c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819580"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Vytvoření a správa pracovních prostorů služby Azure Machine Learning

V tomto článku budete vytvářet, zobrazení a odstranění [ **pracovních prostorů služby Azure Machine Learning** ](concept-azure-machine-learning-architecture.md#workspace) na webu Azure Portal pro [služby Azure Machine Learning](overview-what-is-azure-ml.md).  Můžete také vytvářet a odstraňovat pracovní prostory [pomocí rozhraní příkazového řádku](reference-azure-machine-learning-cli.md) nebo [pomocí kódu Python](https://aka.ms/aml-sdk).

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru 

Chcete-li vytvořit pracovní prostor, budete potřebovat předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view"></a>Zobrazení pracovního prostoru

1. V levém horním rohu portálu, vyberte **všechny služby**. 

1. V **všechny služby** pole filtru, typu **služby machine learning service**.  

1. Vyberte **pracovních prostorů služby Machine Learning**.

   ![Hledat pracovní prostor služby Azure Machine Learning](media/how-to-manage-workspace/all-services.png)

1. Prohlédněte si seznam pracovních prostorů nalezen. Můžete filtrovat podle předplatného, skupiny prostředků a umístění.  

1. Vyberte pracovní prostor zobrazíte jeho vlastnosti.
   ![Vlastnosti pracovního prostoru](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Odstranění pracovního prostoru

Použijte tlačítko Odstranit v horní části pracovního prostoru, který chcete odstranit.

  ![Tlačítko Odstranit](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další postup

Postupujte podle komplexně kurzu se dozvíte, jak používat pracovní prostor pro vytváření, trénování a nasazujte modely pomocí služby Azure Machine Learning.

> [!div class="nextstepaction"]
> [Kurz: Trénování modelů](tutorial-train-models-with-aml.md)
