---
title: Nasadit šablonu - IntelliJ IDEA
description: Zjistěte, jak vytvořit svou první šablonu Azure Resource Manager pomocí IntelliJ IDEA a jak ji nasadit.
ms.devlang: java
ms.date: 08/01/2019
ms.topic: conceptual
ms.openlocfilehash: a5015a018f905b4353afd6bf25a48a2d942b3b97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153348"
---
# <a name="create-and-deploy-arm-templates-by-using-the-intellij-idea"></a>Vytvoření a nasazení šablon ARM pomocí intelliJ IDEA

Zjistěte, jak nasadit šablonu Azure Resource Manager (ARM) do Azure pomocí IntelliJ IDEA a proces úprav a aktualizace šablony přímo z ide. Arm šablony jsou soubory JSON, které definují prostředky, které potřebujete k nasazení pro vaše řešení. Informace o konceptech spojených s nasazením a správou řešení Azure najdete v [tématu přehled nasazení šablon](overview.md).

![Diagram portálu šablony Správce prostředků](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Po dokončení kurzu nasadíte účet Azure Storage. Stejný proces lze použít k nasazení jiných prostředků Azure.

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Nainstalované prostředí [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition nebo Community Edition
* Nainstalovaná sada [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053). Další informace najdete v [průvodci správou modulů plug-in pro IntelliJ](https://www.jetbrains.com/help/idea/managing-plugins.html).
* V sadě Azure Toolkit for IntelliJ musíte být [přihlášeni](/java/azure/intellij/azure-toolkit-for-intellij-sign-in-instructions) ke svému účtu Azure.

## <a name="deploy-a-quickstart-template"></a>Nasazení šablony rychlého startu

Šablonu nemusíte vytvářet od začátku, ale můžete místo toho otevřít některou [šablonu pro rychlý start Azure](https://azure.microsoft.com/resources/templates/). Azure Quickstart Templates je úložiště pro šablony ARM. Šablona použitá v tomto článku se nazývá [Vytvořit účet standardního úložiště](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/). Definuje prostředek účtu úložiště Azure.

1. Klepněte pravým [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) tlačítkem [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) myši a uložte a do místního počítače.

1. Pokud vaše Azure Toolkit jsou správně nainstalované a přihlášené, měli byste vidět Azure Explorer v postranním panelu IntelliJ IDEA. Klikněte pravým tlačítkem myši na **položku Správa prostředků** a vyberte **příkaz Vytvořit nasazení**.

    ![Kliknutím pravým tlačítkem myši na šablonu Správce prostředků klikněte na tlačítko pro vytvoření nasazení.](./media/create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Konfigurace **názvu nasazení**, **předplatného**, **skupiny prostředků**a **oblasti**. Zde nasadíme šablonu `testRG`do nové skupiny prostředků . Potom vyberte cestu pro `azuredeploy.json` **šablonu prostředku** as as a **parametry prostředků** `azuredeploy.parameters.json` při stahování.

    ![Šablona Správce prostředků vybírá soubory, které mají být k vytvoření nasazení](./media/create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Po klepnutí na tlačítko OK je nasazení spuštěno. Až do dokončení nasazení, můžete najít pokrok ze **stavového řádku** IntelliJ IDEA na dně.

    ![Stav nasazení šablony Správce prostředků](./media/create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Procházení existujícího nasazení

1. Po dokončení nasazení se zobrazí nová skupina `testRG` prostředků a nové nasazení vytvořené. Klikněte pravým tlačítkem myši na nasazení a zobrazí se seznam možných akcí. Nyní vyberte **zobrazit vlastnosti**.

    ![Nasazení procházení šablony Správce prostředků](./media/create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Zobrazení tabulátoru bude otevřeno a zobrazí některé užitečné vlastnosti, jako je stav nasazení a struktura šablony.

    ![Šablona Správce prostředků zobrazuje vlastnosti nasazení](./media/create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Úprava a aktualizace existujícího nasazení

1. Vyberte **Upravit nasazení** z nabídky pravým tlačítkem myši nebo zobrazení vlastností zobrazení. Otevře se další zobrazení karty, které zobrazí soubory šablon a parametrů pro nasazení v Azure. Chcete-li tyto soubory uložit do místního, můžete klepnout na **položku Exportovat soubor šablony** nebo **Exportovat soubory parametrů**.

    ![Nasazení šablony Správce prostředků](./media/create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Dva soubory na této stránce můžete upravit a nasadit změny do Azure. Zde změníme hodnotu **storageAccountType** v `Standard_LRS` souborech parametrů, z do `Standard_GRS`. Potom klikněte na **aktualizovat nasazení** na dně a potvrďte aktualizaci.

    ![Nasazení šablony Správce prostředků](./media/create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. Po dokončení nasazení aktualizace můžete na portálu ověřit, že `Standard_GRS`vytvořený účet úložiště byl změněn na .

## <a name="clean-up-resources"></a>Vyčištění prostředků

1. Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků. Můžete to udělat z webu Azure Portal nebo Azure CLI. V Průzkumníkovi Azure z IntelliJ IDEA klikněte pravým tlačítkem myši na **vytvořenou skupinu prostředků** a vyberte odstranit.

    ![Odstranění skupiny prostředků v Průzkumníkovi Azure z IntelliJ IDEA](./media/create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Všimněte si, že odstranění nasazení neodstraní prostředky vytvořené nasazením. Pokud je již nepotřebujete, odstraňte odpovídající skupinu prostředků nebo konkrétní prostředky.

## <a name="next-steps"></a>Další kroky

Hlavním zaměřením tohoto článku je použití IntelliJ IDEA k nasazení existující šablony ze šablon Azure Quickstart. Také jste se naučili, jak zobrazit a aktualizovat existující nasazení v Azure. Šablony Azure pro rychlý start možná nenabízí vše, co potřebujete. Další informace o vývoji šablon najdete v našem novém výukovém programu pro začátečníky:

> [!div class="nextstepaction"]
> [Kurzy pro začátečníky](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Navštivte Java v centru Azure Dev](https://docs.microsoft.com/azure/java)
