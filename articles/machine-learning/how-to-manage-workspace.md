---
title: Vytvoření pracovních prostorů Azure Machine Learning na portálu
titleSuffix: Azure Machine Learning
description: Zjistěte, jak vytvářet, zobrazovat a odstraňovat pracovní prostory Azure Machine Learning na webu Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: f38b0895b0d6eddcf63c082d3df205f4d9de9d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297066"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Vytváření a správa pracovních prostorů Azure Machine Learning na webu Azure Portal
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku vytvoříte, zobrazíte a odstraníte [**pracovní prostory Azure Machine Learning**](concept-workspace.md) na portálu Azure pro Azure Machine [Learning](overview-what-is-azure-ml.md).  Portál je nejjednodušší způsob, jak začít s pracovními prostory, ale jak se vaše potřeby mění nebo požadavky na zvýšení automatizace můžete také vytvářet a odstraňovat pracovní prostory [pomocí CLI](reference-azure-machine-learning-cli.md), [s kódem Pythonu](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) nebo [pomocí rozšíření VS Code](tutorial-setup-vscode-extension.md).

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

K vytvoření pracovního prostoru potřebujete předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí přihlašovacích údajů pro vaše předplatné Azure. 

1. V levém horním rohu portálu Azure vyberte **+ Vytvořit prostředek**.

      ![Vytvoření nového prostředku](./media/how-to-manage-workspace/create-workspace.gif)

1. Pomocí vyhledávacího panelu vyhledejte **machine learning**.

1. Vyberte **možnost Strojové učení**.

1. V podokně **Strojového učení** vyberte **Vytvořit** a začněte.

1. Zadejte následující informace pro konfiguraci nového pracovního prostoru:

   Pole|Popis 
   ---|---
   Název pracovního prostoru |Zadejte jedinečný název, který identifikuje váš pracovní prostor. V tomto příkladu používáme **docs-ws**. Názvy musí být jedinečné v celé skupině prostředků. Použijte název, který se snadno pamatuje a odlišuje od pracovních prostorů vytvořených jinými uživateli. Název pracovního prostoru nerozlišuje malá a velká písmena.
   Předplatné |Vyberte předplatné Azure, které chcete použít.
   Skupina prostředků | Použijte stávající skupinu prostředků, kterou máte v předplatném, nebo zadejte název a vytvořte novou skupinu prostředků. Skupina prostředků obsahuje související prostředky pro řešení Azure. V tomto příkladu používáme **docs-aml**. 
   Umístění | Vyberte umístění, které je nejblíže uživatelům, a datové prostředky k vytvoření pracovního prostoru.
   Edice pracovního prostoru | Vyberte **základní** nebo **organizace**.  Tato edice pracovního prostoru určuje funkce, ke kterým budete mít přístup a ceny. Přečtěte si další informace o [nabídkách základní a podnikové edice](overview-what-is-azure-ml.md#sku). 

    ![Konfigurace pracovního prostoru](./media/how-to-manage-workspace/select-edition.png)

1. Po dokončení konfigurace pracovního prostoru vyberte **Zkontrolovat + Vytvořit**.
2. Zkontrolujte nastavení a proveďte další změny nebo opravy. Až budete satisified s nastavením, vyberte **Vytvořit**.

   > [!Warning] 
   > Vytvoření pracovního prostoru v cloudu může trvat několik minut.

   Po dokončení procesu se zobrazí zpráva o úspěchu nasazení. 
 
 1. Chcete-li zobrazit nový pracovní prostor, vyberte **přejít na zdroj**.

### <a name="download-a-configuration-file"></a>Stažení konfiguračního souboru

1. Pokud budete vytvářet [výpočetní instanci](tutorial-1st-experiment-sdk-setup.md#azure), přeskočte tento krok.

1. Pokud plánujete použít kód v místním prostředí, které odkazuje na tento pracovní prostor, vyberte **stáhnout config.json** z části **Přehled** pracovního prostoru.  

   ![Stáhnout config.json](./media/how-to-manage-workspace/configure.png)
   
   Umístěte soubor do struktury adresářů pomocí skriptů Pythonu nebo poznámkových bloků Jupyter. Může být ve stejném adresáři, podadresáři s názvem *.azureml*nebo v nadřazeném adresáři. Když vytvoříte výpočetní instanci, tento soubor se přidá do správného adresáře na virtuálním počítači za vás.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Upgrade na edici Enterprise

Pracovní prostor můžete upgradovat ze základní edice na edici Enterprise a využívat tak rozšířené funkce, jako jsou prostředí s nízkým kódem a vylepšené funkce zabezpečení.

1. Přihlaste se do [studia Azure Machine Learning Studio](https://ml.azure.com).

1. Vyberte pracovní prostor, který chcete upgradovat.

1. V pravém horním rohu stránky vyberte **Další možnost Další.**

   [![Upgrade pracovního](./media/how-to-manage-workspace/upgrade.png) prostoru](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. V okně, které se zobrazí, vyberte **Upgradovat.**


> [!IMPORTANT]
> Pracovní prostor edice Enterprise nelze přejít na pracovní prostor Basic Edition. 

## <a name="find-a-workspace"></a><a name="view"></a>Vyhledání pracovního prostoru

1. V horním vyhledávacím poli zadejte **Machine Learning**.  

1. Vyberte **možnost Strojové učení**.

   ![Hledání pracovního prostoru Azure Machine Learning](./media/how-to-manage-workspace/find-workspaces.png)

1. Prohlédněte si seznam nalezených pracovních prostorů. Můžete filtrovat na základě předplatného, skupin prostředků a umístění.  

1. Vyberte pracovní prostor, chcete-li zobrazit jeho vlastnosti.

## <a name="delete-a-workspace"></a>Odstranění pracovního prostoru

Použijte tlačítko Odstranit v horní části pracovního prostoru, který chcete odstranit.

  ![Tlačítko Odstranit](./media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Řešení potíží

### <a name="resource-provider-errors"></a>Chyby zprostředkovatele prostředků

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Přesunutí pracovního prostoru

> [!WARNING]
> Přesunutí pracovního prostoru Azure Machine Learning do jiného předplatného nebo přesunutí vlastního předplatného do nového tenanta není podporováno. To může způsobit chyby.

### <a name="deleting-the-azure-container-registry"></a>Odstranění registru kontejnerů Azure

Pracovní prostor Azure Machine Learning používá azure kontejnerregistru (ACR) pro některé operace. Automaticky vytvoří instanci ACR, když ji poprvé potřebuje.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Další kroky

Postupujte podle celé délky kurzu se dozvíte, jak používat pracovní prostor k vytváření, trénování a nasazování modelů s Azure Machine Learning.

> [!div class="nextstepaction"]
> [Výuka: Modely vlaků](tutorial-train-models-with-aml.md)
