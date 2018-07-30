---
title: Vytvoření šablony Resource Manageru pomocí nástroje Visual Studio Code | Microsoft Docs
description: Při práci na šablonách Resource Manageru můžete použít rozšíření Nástroje Azure Resource Manageru.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/17/2018
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: bb0e3ee685d5a52a9f372e99d97f3608f67c17c0
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185393"
---
# <a name="quickstart-create-azure-resource-manager-templates-by-using-visual-studio-code"></a>Rychlý start: Vytvoření šablon Azure Resource Manageru pomocí nástroje Visual Studio Code

Naučte se vytvářet šablony pomocí nástroje Visual Studio Code a rozšíření Nástroje Azure Resource Manageru. Šablony Resource Manageru můžete v nástroji Visual Studio Code vytvářet i bez tohoto rozšíření, poskytuje však možnosti automatického dokončování, které vývoj šablon zjednodušují. Abyste porozuměli konceptům spojeným s nasazením a správou řešení Azure, podívejte se na téma [Přehled Azure Resource Manageru](resource-group-overview.md).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

- [Visual Studio Code](https://code.visualstudio.com/).
- Rozšíření Nástroje Resource Manageru. Instalaci provedete následujícím postupem:

    1. Otevřete Visual Studio Code.
    2. Stisknutím **CTRL+SHIFT+X** otevřete podokno Rozšíření.
    3. Vyhledejte **Nástroje Azure Resource Manageru** a vyberte **Nainstalovat**.
    4. Instalaci rozšíření dokončíte výběrem **Znovu načíst**.

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

Šablonu nemusíte vytvářet od začátku, ale můžete místo toho otevřít některou [šablonu pro rychlý start Azure](https://azure.microsoft.com/resources/templates/). Šablony pro rychlý start Azure slouží jako úložiště šablon Resource Manageru.

Šablona, kterou jsme použili v tomto rychlém startu, se nazývá [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Vytvoření standardního účtu úložiště). Šablona definuje prostředek účtu služby Azure Storage.

1. V nástroji Visual Studio Code vyberte **File** (Soubor) >**Open File** (Otevřít soubor).
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Výběrem **Open** (Otevřít) soubor otevřete.
4. Vyberte **File** (Soubor) >**Save As** (Uložit jako) a soubor uložte jako **azuredeploy.json** na místní počítač.

## <a name="edit-the-template"></a>Úprava šablony

Abyste se naučili upravit šablonu v nástroji Visual Studio Code, přidáte další element do části výstupů.

1. V nástroji Visual Studio Code přidejte jeden další výstup do exportované šablony:

    ```json
    "storageUri": {
      "type": "string",
      "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
    }
    ```

    Až to bude mít, bude část výstupů vypadat takto:

    ```json
    "outputs": {
      "storageAccountName": {
        "type": "string",
        "value": "[variables('storageAccountName')]"
      },
      "storageUri": {
        "type": "string",
        "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

    Pokud jste zkopírovali a vložili kód v nástroji Visual Studio Code, zkuste znovu zadat element **value**, abyste si vyzkoušeli možnosti IntelliSense v rozšíření Nástroje Resource Manageru.

    ![IntelliSense v nástroji Visual Studio Code v šabloně Resource Manageru](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. Soubor uložte tak, že vyberete **File** (Soubor) >**Save** (Uložit).

## <a name="deploy-the-template"></a>Nasazení šablony

Šablony můžete nasadit mnoha způsoby.  V tomto rychlém startu použijete Cloud Shell na portálu Azure Portal. Cloud Shell podporuje jak Azure CLI, tak i Azure PowerShell. V pokynech v tomto článku se používá CLI.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. V pravém horním rohu vyberte **Cloud Shell**, jak je znázorněno na tomto obrázku:

    ![Cloud Shell na portálu Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell.png)

3. Vyberte šipku dolů a potom výběrem položky **Bash** přepněte z PowerShellu na CLI.

    ![CLI v Cloud Shellu na portálu Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)
4. Vyberte **Restartovat** a prostředí restartujte.
5. Vyberte **Nahrát nebo stáhnout soubory** a potom vyberte **Nahrát**.

    ![Nahrání souboru v Cloud Shellu na portálu Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)
4. Vyberte soubor, který jste si uložili dříve v tomto rychlém startu. Výchozí název je **azuredeploy.json**.
5. V Cloud Shellu spusťte příkaz **ls** a ověřte, že se soubor nahrál úspěšně. Obsah šablony můžete ověřit také pomocí příkazu **cat**.

    ![Zobrazení souboru v Cloud Shellu na portálu Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)
6. V Cloud Shellu spusťte následující příkazy:

    ```cli
    az group create --name <ResourceGroupName> --location <AzureLocation>

    az group deployment create --name <DeploymentName> --resource-group <ResourceGroupName> --template-file <TemplateFileName>
    ```
    Tady je snímek obrazovky s ukázkovým nasazením:

    ![Šablona nasazení v Cloud Shellu na portálu Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)

    Na snímku obrazovky jsou použity tyto hodnoty:

    - **&lt;ResourceGroupName>**: myresourcegroup0709. Tento parametr je použit na dvou místech.  Nezapomeňte použít stejnou hodnotu.
    - **&lt;AzureLocation>**: eastus2
    - **&lt;DeployName>**: mydeployment0709
    - **&lt;TemplateFile>**: azuredeploy.json

    Ve výstupu na snímku obrazovky má účet úložiště název *3tqebj3slyfyestandardsa*. 

7. Spuštěním následujícího powershellového příkazu zobrazíte nově vytvořený účet úložiště:

    ```cli
    az storage account show --resource-group <ResourceGroupName> --name <StorageAccountName>
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste naučili, jak vytvořit šablonu v nástroji Visual Studio Code a jak ji nasadit pomocí Cloud Shellu na portálu Azure Portal. V dalším kurzu se dozvíte více o tom, jak vyvinout šablonu a jak používat referenční informace k šablonám.

> [!div class="nextstepaction"]
> [Vytvoření šifrovaného účtu úložiště](./resource-manager-tutorial-create-encrypted-storage-accounts.md)
