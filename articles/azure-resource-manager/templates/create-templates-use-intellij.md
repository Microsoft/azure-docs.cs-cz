---
title: Nasazení šablony – IntelliJ nápad
description: Naučte se, jak vytvořit první šablonu Azure Resource Manager s využitím NÁPADu IntelliJ a jak ho nasadit.
ms.devlang: java
ms.date: 08/01/2019
ms.topic: conceptual
ms.openlocfilehash: 1046c942e6bec4ded332027b7856110238b8f2d7
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2020
ms.locfileid: "75680609"
---
# <a name="create-and-deploy-azure-resource-manager-templates-by-using-the-intellij-idea"></a>Vytváření a nasazování šablon Azure Resource Manager s využitím NÁPADu IntelliJ

Naučte se, jak nasadit šablonu Správce prostředků do Azure s využitím NÁPADu IntelliJ a procesu úpravy a aktualizace šablony přímo z integrovaného vývojového prostředí (IDE). Šablony Resource Manageru jsou soubory JSON, které definují, jaké prostředky je pro řešení potřeba nasadit. Informace o konceptech spojených s nasazením a správou řešení Azure najdete v tématu [Přehled nasazení šablon](overview.md).

![Diagram portálu Správce prostředků Template](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Po dokončení kurzu nasadíte účet Azure Storage. Stejný postup je možné použít k nasazení dalších prostředků Azure.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Je nainstalovaná edice [INTELLIJ nápad](https://www.jetbrains.com/idea/download/) Ultimate nebo Community Edition.
* [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) nainstalován, další informace najdete v [příručce pro správu modulů plug-in IntelliJ](https://www.jetbrains.com/help/idea/managing-plugins.html) .
* [Přihlášení](/java/azure/intellij/azure-toolkit-for-intellij-sign-in-instructions) k účtu Azure pro Azure Toolkit for IntelliJ

## <a name="deploy-a-quickstart-template"></a>Nasazení šablony pro rychlý Start

Šablonu nemusíte vytvářet od začátku, ale můžete místo toho otevřít některou [šablonu pro rychlý start Azure](https://azure.microsoft.com/resources/templates/). Šablony pro rychlý Start Azure jsou úložiště pro šablony Správce prostředků. Šablona použitá v tomto článku se nazývá [vytvořit účet úložiště úrovně Standard](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/). Definuje prostředek účtu Azure Storage.

1. Klikněte pravým tlačítkem myši a uložte [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) a [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) do svého místního počítače.

1. Pokud je vaše sada Azure Toolkit správně nainstalovaná a přihlášená, měla by se zobrazit v Průzkumníkovi Azure na postranním panelu IntelliJ NÁPADu. Klikněte pravým tlačítkem na **Správa prostředků** a vyberte **vytvořit nasazení**.

    ![Správce prostředků šablonu kliknutím pravým tlačítkem myši vytvoříte nasazení.](./media/create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Proveďte konfiguraci **názvu nasazení**, **předplatného**, **skupiny prostředků**a **oblasti**. Tady nasadíme šablonu do nové skupiny prostředků `testRG`. Pak vyberte cesta pro **šablonu prostředků** jako `azuredeploy.json` a **parametry prostředku** jako `azuredeploy.parameters.json` jste si stáhli.

    ![Správce prostředků šablonou vyberte soubory pro vytvoření nasazení.](./media/create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Po kliknutí na tlačítko OK se nasazení spustí. Až do dokončení nasazení můžete na konci najít průběh ze **stavového řádku** nápadu IntelliJ.

    ![Stav nasazení šablony Správce prostředků](./media/create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Procházet existující nasazení

1. Po dokončení nasazení uvidíte novou skupinu prostředků `testRG` a vytvoří se nové nasazení. Klikněte pravým tlačítkem na nasazení a uvidíte seznam možných akcí. Nyní vyberte **Zobrazit vlastnosti**.

    ![Nasazení procházení šablonou Správce prostředků](./media/create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Otevře se zobrazení karet, ve kterém se zobrazí některé užitečné vlastnosti, jako je stav nasazení a struktura šablony.

    ![Šablona Správce prostředků – zobrazit vlastnosti nasazení](./media/create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Upravit a aktualizovat existující nasazení

1. V nabídce kliknutím pravým tlačítkem nebo zobrazení vlastností vyberte **Upravit nasazení** . Otevře se jiné zobrazení karty, které zobrazuje šablonu a soubory parametrů pro nasazení v Azure. Chcete-li uložit tyto soubory do místní, můžete kliknout na **Exportovat soubor šablony** nebo **exportovat soubory parametrů**.

    ![Nasazení úprav šablony Správce prostředků](./media/create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Tyto dva soubory můžete upravovat na této stránce a nasazovat změny do Azure. Zde upravujeme hodnotu **storageAccountType** v souborech parametrů, od `Standard_LRS` k `Standard_GRS`. Potom v dolní části klikněte na **aktualizovat nasazení** a potvrďte aktualizaci.

    ![Nasazení úprav šablony Správce prostředků](./media/create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. Po dokončení nasazení aktualizace můžete na portálu ověřit, že se vytvořený účet úložiště změnil na `Standard_GRS`.

## <a name="clean-up-resources"></a>Vyčištění prostředků

1. Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků. Můžete to provést z Azure Portal nebo pomocí Azure CLI. V Azure Exploreru na IntelliJ nápad klikněte pravým tlačítkem na vytvořenou **skupinu prostředků** a vyberte Odstranit.

    ![Odstranění skupiny prostředků v Azure Exploreru z IntelliJ NÁPADu](./media/create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Všimněte si, že odstraněním nasazení nedojde k odstranění prostředků vytvořených nasazením. Odstraňte prosím odpovídající skupinu prostředků nebo konkrétní prostředky, pokud je už nepotřebujete.

## <a name="next-steps"></a>Další kroky

Hlavním cílem tohoto článku je používat IntelliJ nápad k nasazení existující šablony ze šablon Azure pro rychlý Start. Zjistili jste také, jak zobrazit a aktualizovat existující nasazení v Azure. Šablony Azure pro rychlý start možná nenabízí vše, co potřebujete. Další informace o vývoji šablon najdete v naší nové řadě kurzů pro začátečníky:

> [!div class="nextstepaction"]
> [Začátečník – kurzy](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Navštívit Java ve službě Azure Dev Center](https://docs.microsoft.com/azure/java)
