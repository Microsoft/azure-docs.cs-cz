---
title: Vytváření pracovních prostorů Azure ML na portálu
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet, zobrazovat a odstraňovat Azure Machine Learning pracovní prostory v Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 1985c596b9f4b9b78b0055bfe1eab9888c30e201
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489731"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Vytváření a Správa pracovních prostorů Azure Machine Learning v Azure Portal
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku vytvoříte, zobrazíte a odstraníte [**Azure Machine Learning pracovní prostory**](concept-workspace.md) v Azure Portal pro [Azure Machine Learning](overview-what-is-azure-ml.md).  Portál představuje nejjednodušší způsob, jak začít pracovat s pracovními prostory, ale jak se vaše potřeby mění nebo jsou požadavky na automatizaci, můžete také vytvořit a odstranit pracovní prostory [pomocí rozhraní](reference-azure-machine-learning-cli.md)příkazového řádku [s kódem Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) nebo [prostřednictvím rozšíření vs Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

K vytvoření pracovního prostoru potřebujete předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí přihlašovacích údajů předplatného Azure. 

1. V levém horním rohu Azure Portal vyberte **+ vytvořit prostředek**.

      ![Vytvoření nového prostředku](../../../includes/media/aml-create-in-portal/portal-create-resource.png)

1. Pomocí panelu hledání vyhledejte **Machine Learning pracovní prostor služby**.

1. Vyberte **Machine Learning pracovní prostor služby**.

1. V podokně **pracovní prostor služby Machine Learning** vyberte **vytvořit** a začněte.

1. Zadáním následujících informací nakonfigurujte nový pracovní prostor:

   Pole|Popis 
   ---|---
   Název pracovního prostoru |Zadejte jedinečný název, který identifikuje váš pracovní prostor. V tomto příkladu používáme **docs-WS**. Názvy musí být v rámci skupiny prostředků jedinečné. Použijte název, který se dá snadno vyvolat a odlišit z pracovních prostorů vytvořených jinými uživateli.  
   Předplatné |Vyberte předplatné Azure, které chcete použít.
   Skupina prostředků | Ve svém předplatném použijte existující skupinu prostředků nebo zadejte název pro vytvoření nové skupiny prostředků. Skupina prostředků obsahuje související prostředky pro řešení Azure. V tomto příkladu používáme **docs-AML**. 
   Umístění | Vyberte umístění, které je nejblíže vašim uživatelům a datovým prostředkům, abyste mohli vytvořit pracovní prostor.
   Edice pracovního prostoru | Vyberte **Basic** nebo **Enterprise**.  Tato edice pracovního prostoru určuje funkce, ke kterým budete mít přístup a ceny. Přečtěte si další informace o [nabídkách Basic a Enterprise Edition](overview-what-is-azure-ml.md#sku). 

    ![Konfigurace pracovního prostoru](media/how-to-manage-workspace/select-edition.png)

1. Po dokončení konfigurace pracovního prostoru vyberte **vytvořit**. 

   > [!Warning] 
   > Vytvoření pracovního prostoru v cloudu může trvat několik minut.

   Po dokončení procesu se zobrazí zpráva o úspěšném nasazení. 
 
 1. Pokud chcete zobrazit nový pracovní prostor, vyberte **Přejít k prostředku**.

### <a name="download-a-configuration-file"></a>Stažení konfiguračního souboru

1. Pokud budete vytvářet [virtuální počítač poznámkového bloku](tutorial-1st-experiment-sdk-setup.md#azure), tento krok přeskočte.

1. Pokud plánujete použít kód v místním prostředí, které odkazuje na tento pracovní prostor, vyberte **Stáhnout soubor config. JSON** v části **Přehled** pracovního prostoru.  

   ![Stažení souboru config. JSON](./media/how-to-manage-workspace/configure.png)
   
   Soubor umístěte do struktury adresáře pomocí skriptů Pythonu nebo poznámkových bloků Jupyter. Může být ve stejném adresáři, v podadresáři s názvem *. AzureML*nebo v nadřazeném adresáři. Při vytváření virtuálního počítače poznámkového bloku se tento soubor přidá do správného adresáře na VIRTUÁLNÍm počítači za vás.

## <a name="upgrade"></a>Upgrade na Enterprise Edition

Pracovní prostor můžete upgradovat ze sady Basic Edition na Enterprise Edition a využít tak vylepšené funkce, jako je například prostředí s nízkým kódem a rozšířené funkce zabezpečení.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Vyberte pracovní prostor, který chcete upgradovat.

1. Vyberte možnost **upgradovat** v horní části nebo ve zprávě pro upgrade.

    ![Upgrade pracovního prostoru](media/how-to-manage-workspace/upgrade.png)

1. Vyberte **Potvrdit aktualizaci**.


> [!IMPORTANT]
> Pracovní prostor Enterprise Edition nelze downgradovat na pracovní prostor edice Basic. 

## <a name="view"></a>Najít pracovní prostor

1. V levém horním rohu portálu vyberte **všechny služby**.

1. Do pole Filtr **všech služeb** zadejte **Machine Learning**.  

1. Vyberte **Azure Machine Learning**.

   ![Vyhledat Azure Machine Learning pracovní prostor](media/how-to-manage-workspace/all-services.png)

1. Prohlédněte si seznam pracovních prostorů, které se našly. Můžete filtrovat podle předplatného, skupin prostředků a umístění.  

1. Vyberte pracovní prostor, ve kterém chcete zobrazit jeho vlastnosti.
   vlastnosti ![pracovního prostoru](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Odstranění pracovního prostoru

Použijte tlačítko Odstranit v horní části pracovního prostoru, který chcete odstranit.

  ![Tlačítko Odstranit](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

Postupujte podle kurzu pro celou délku a Naučte se používat pracovní prostor k sestavování, vytváření výukových a nasazování modelů pomocí Azure Machine Learning.

> [!div class="nextstepaction"]
> [Kurz: modely vlaků](tutorial-train-models-with-aml.md)
