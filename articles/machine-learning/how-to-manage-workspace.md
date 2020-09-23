---
title: Vytváření pracovních prostorů na portálu
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet, zobrazovat a odstraňovat Azure Machine Learning pracovní prostory v Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 38784b006acac4c3ff70b2aa3c38648e939eddeb
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90889921"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Vytváření a Správa pracovních prostorů Azure Machine Learning v Azure Portal


V tomto článku vytvoříte, zobrazíte a odstraníte [**Azure Machine Learning pracovní prostory**](concept-workspace.md) v Azure Portal pro [Azure Machine Learning](overview-what-is-azure-ml.md).  Portál představuje nejjednodušší způsob, jak začít pracovat s pracovními prostory, ale jak se vaše potřeby mění nebo jsou požadavky na automatizaci, můžete také vytvořit a odstranit pracovní prostory [pomocí rozhraní](reference-azure-machine-learning-cli.md)příkazového řádku [s kódem Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) nebo [prostřednictvím rozšíření vs Code](tutorial-setup-vscode-extension.md).

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

K vytvoření pracovního prostoru potřebujete předplatné Azure. Pokud ještě předplatné Azure nemáte, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí přihlašovacích údajů předplatného Azure. 

1. V levém horním rohu Azure Portal vyberte **+ vytvořit prostředek**.

      ![Vytvoření nového prostředku](./media/how-to-manage-workspace/create-workspace.gif)

1. K vyhledání **Machine Learning**použijte panel hledání.

1. Vyberte **Machine Learning**.

1. V podokně **Machine Learning** vyberte **vytvořit** a začněte.

1. Zadáním následujících informací nakonfigurujte nový pracovní prostor:

   Pole|Description 
   ---|---
   Název pracovního prostoru |Zadejte jedinečný název, který identifikuje váš pracovní prostor. V tomto příkladu používáme **docs-WS**. Názvy musí být v rámci skupiny prostředků jedinečné. Použijte název, který se dá snadno vyvolat a odlišit z pracovních prostorů vytvořených jinými uživateli. V názvu pracovního prostoru se nerozlišují malá a velká písmena.
   Předplatné |Vyberte předplatné Azure, které chcete použít.
   Skupina prostředků | Použijte stávající skupinu prostředků, kterou máte v předplatném, nebo zadejte název a vytvořte novou skupinu prostředků. Skupina prostředků obsahuje související prostředky pro řešení Azure. V tomto příkladu používáme **docs-AML**. Chcete-li použít existující skupinu prostředků, potřebujete roli *Přispěvatel* nebo *vlastník* .  Další informace o přístupu najdete v tématu [Správa přístupu k pracovnímu prostoru Azure Machine Learning](how-to-assign-roles.md).
   Umístění | Vyberte umístění, které je nejblíže vašim uživatelům a datovým prostředkům, abyste mohli vytvořit pracovní prostor.
   Umístění | Vyberte umístění, které je nejblíže vašim uživatelům a datovým prostředkům, abyste mohli vytvořit pracovní prostor.

    ![Konfigurace pracovního prostoru](./media/how-to-manage-workspace/select-edition.png)

1. Po dokončení konfigurace pracovního prostoru vyberte **zkontrolovat + vytvořit**.
2. Zkontrolujte nastavení a proveďte další změny nebo opravy. Až budete s nastavením spokojeni, vyberte **vytvořit**.

   > [!Warning] 
   > Vytvoření pracovního prostoru v cloudu může trvat několik minut.

   Po dokončení procesu se zobrazí zpráva o úspěšném nasazení. 
 
 1. Pokud chcete zobrazit nový pracovní prostor, vyberte **Přejít k prostředku**.

### <a name="download-a-configuration-file"></a>Stažení konfiguračního souboru

1. Pokud budete vytvářet [výpočetní instanci](tutorial-1st-experiment-sdk-setup.md#azure), tento krok přeskočte.

1. Pokud máte v úmyslu používat v místním prostředí kód, který odkazuje na tento pracovní prostor, vyberte  **stáhnout config.jsv** části **Přehled** v pracovním prostoru.  

   ![Stáhnout config.js](./media/how-to-manage-workspace/configure.png)
   
   Soubor umístěte do struktury adresáře pomocí skriptů Pythonu nebo poznámkových bloků Jupyter. Může být ve stejném adresáři, v podadresáři s názvem *. AzureML*nebo v nadřazeném adresáři. Při vytváření výpočetní instance se tento soubor přidá do správného adresáře na virtuálním počítači za vás.
## <a name="find-a-workspace"></a><a name="view"></a>Najít pracovní prostor

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Do pole nejvyšší hledání zadejte **Machine Learning**.  

1. Vyberte **Machine Learning**.

   ![Vyhledat Azure Machine Learning pracovní prostor](./media/how-to-manage-workspace/find-workspaces.png)

1. Prohlédněte si seznam pracovních prostorů, které se našly. Můžete filtrovat podle předplatného, skupin prostředků a umístění.  

1. Vyberte pracovní prostor, ve kterém chcete zobrazit jeho vlastnosti.

## <a name="delete-a-workspace"></a>Odstranění pracovního prostoru

V [Azure Portal](https://portal.azure.com/)v horní části pracovního prostoru, který chcete odstranit, vyberte **Odstranit**  .

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Odstranit pracovní prostor":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Poradce při potížích

### <a name="resource-provider-errors"></a>Chyby poskytovatele prostředků

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Přesun pracovního prostoru

> [!WARNING]
> Přesunutím pracovního prostoru Azure Machine Learning do jiného předplatného nebo přesunutím vlastnícího předplatného na nového tenanta se nepodporuje. V takovém případě může dojít k chybám.

### <a name="deleting-the-azure-container-registry"></a>Odstranění Azure Container Registry

Azure Machine Learning pracovní prostor používá pro některé operace Azure Container Registry (ACR). Automaticky vytvoří instanci ACR, když ji poprvé potřebuje.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Další kroky

Postupujte podle kurzu pro celou délku a Naučte se používat pracovní prostor k sestavování, vytváření výukových a nasazování modelů pomocí Azure Machine Learning.

> [!div class="nextstepaction"]
> [Kurz: modely vlaků](tutorial-train-models-with-aml.md)
