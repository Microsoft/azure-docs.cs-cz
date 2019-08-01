---
title: Vytvoření a nasazení šablony Azure Resource Manager pomocí NÁPADu IntelliJ | Microsoft Docs
description: Naučte se, jak vytvořit první šablonu Azure Resource Manager s využitím NÁPADu IntelliJ a jak ho nasadit.
services: azure-resource-manager
documentationcenter: ''
author: yucwan
manager: ''
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: java
ms.date: 08/01/2019
ms.topic: quickstart
ms.author: yucwan
ms.openlocfilehash: cbeaccf4cdea87d6f34d5ee77e6a08b32abd76b5
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708344"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-intellij-idea"></a>Rychlý start: Vytváření a nasazování šablon Azure Resource Manager s využitím NÁPADu IntelliJ

Naučte se, jak nasadit šablonu Správce prostředků do Azure s využitím NÁPADu IntelliJ a procesu úpravy a aktualizace šablony přímo z integrovaného vývojového prostředí (IDE). Šablony Resource Manageru jsou soubory JSON, které definují, jaké prostředky je pro řešení potřeba nasadit. Abyste porozuměli konceptům spojeným s nasazením a správou řešení Azure, podívejte se na téma [Přehled Azure Resource Manageru](resource-group-overview.md).

![Diagram portálu pro rychlé zprovoznění šablon Správce prostředků](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Po dokončení kurzu nasadíte účet Azure Storage. Stejný postup je možné použít k nasazení dalších prostředků Azure.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Je nainstalovaná edice [INTELLIJ nápad](https://www.jetbrains.com/idea/download/) Ultimate nebo Community Edition.
* [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) nainstalován, další informace najdete v [příručce pro správu modulů plug-in IntelliJ](https://www.jetbrains.com/help/idea/managing-plugins.html) .
* [Přihlášení](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-sign-in-instructions) k účtu Azure pro Azure Toolkit for IntelliJ

## <a name="deploy-a-quickstart-template"></a>Nasazení šablony pro rychlý Start

Šablonu nemusíte vytvářet od začátku, ale můžete místo toho otevřít některou [šablonu pro rychlý start Azure](https://azure.microsoft.com/resources/templates/). Šablony pro rychlý start Azure slouží jako úložiště šablon Resource Manageru. Šablona, kterou jsme použili v tomto rychlém startu, se nazývá [Create a standard storage account](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/) (Vytvoření standardního účtu úložiště). Definuje prostředek účtu Azure Storage. 

1. Klikněte pravým tlačítkem myši a [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) uložte [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) a do místního počítače.

1. Pokud je vaše sada Azure Toolkit správně nainstalovaná a přihlášená, měla by se zobrazit v Průzkumníkovi Azure na postranním panelu IntelliJ NÁPADu. Klikněte pravým tlačítkem na **Správa prostředků** a vyberte **vytvořit nasazení**.

    ![Správce prostředků šablonu kliknutím pravým tlačítkem myši vytvoříte nasazení.](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Proveďte konfiguraci **názvu nasazení**,předplatného, **skupiny prostředků**a **oblasti**. Tady nasadíme šablonu do nové skupiny `testRG`prostředků. Pak vyberte možnost cesta pro **šablonu prostředků** jako `azuredeploy.json` `azuredeploy.parameters.json` a **parametry prostředku** při stažení.

    ![Správce prostředků šablonou vyberte soubory pro vytvoření nasazení.](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Po kliknutí na tlačítko OK se nasazení spustí. Až do dokončení nasazení můžete na konci najít průběh ze **stavového řádku** nápadu IntelliJ.

    ![Stav nasazení šablony Správce prostředků](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Procházet existující nasazení

1. Po dokončení nasazení uvidíte novou skupinu `testRG` prostředků a nové nasazení se vytvořilo. Klikněte pravým tlačítkem na nasazení a uvidíte seznam možných akcí. Nyní vyberte **Zobrazit vlastnosti**.

    ![Nasazení procházení šablonou Správce prostředků](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Otevře se zobrazení karet, ve kterém se zobrazí některé užitečné vlastnosti, jako je stav nasazení a struktura šablony.

    ![Šablona Správce prostředků – zobrazit vlastnosti nasazení](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Upravit a aktualizovat existující nasazení

1. V nabídce kliknutím pravým tlačítkem nebo zobrazení vlastností vyberte **Upravit nasazení** . Otevře se jiné zobrazení karty, které zobrazuje šablonu a soubory parametrů pro nasazení v Azure. Chcete-li uložit tyto soubory do místní, můžete kliknout na **Exportovat soubor šablony** nebo **exportovat soubory parametrů**.

    ![Nasazení úprav šablony Správce prostředků](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Tyto dva soubory můžete upravovat na této stránce a nasazovat změny do Azure. Zde upravujeme hodnotu **storageAccountType** v souborech parametrů z `Standard_LRS` na. `Standard_GRS` Potom v dolní části klikněte na **aktualizovat nasazení** a potvrďte aktualizaci.

    ![Nasazení úprav šablony Správce prostředků](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. Po dokončení nasazení aktualizace můžete na portálu ověřit, jestli je vytvořený účet úložiště změněný `Standard_GRS`.

## <a name="clean-up-resources"></a>Vyčištění prostředků

1. Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků. Můžete to provést z Azure Portal nebo pomocí Azure CLI. V Azure Exploreru na IntelliJ nápad klikněte pravým tlačítkem na vytvořenou **skupinu prostředků** a vyberte Odstranit.

    ![Odstranění skupiny prostředků v Azure Exploreru z IntelliJ NÁPADu](./media/resource-manager-quickstart-create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Všimněte si, že odstraněním nasazení nedojde k odstranění prostředků vytvořených nasazením. Odstraňte prosím odpovídající skupinu prostředků nebo konkrétní prostředky, pokud je už nepotřebujete.

## <a name="next-steps"></a>Další postup

Hlavním cílem tohoto rychlého startu je použít IntelliJ nápad k nasazení existující šablony ze šablon Azure pro rychlý Start. Zjistili jste také, jak zobrazit a aktualizovat existující nasazení v Azure. Šablony Azure pro rychlý start možná nenabízí vše, co potřebujete. V dalším kurzu se dozvíte, jak v referenčních informacích k šablonám vyhledat potřebné informace, abyste mohli vytvořit šifrovaný účet služby Azure Storage.

> [!div class="nextstepaction"]
> [Vytvoření šifrovaného účtu úložiště](./resource-manager-tutorial-create-encrypted-storage-accounts.md)

> [!div class="nextstepaction"]
> [Navštívit Java ve službě Azure Dev Center](https://docs.microsoft.com/azure/java)