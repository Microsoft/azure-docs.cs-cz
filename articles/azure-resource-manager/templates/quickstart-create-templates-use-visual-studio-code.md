---
title: Vytvořit šablonu – kód Visual Studia
description: Při práci na šablonách Resource Manageru můžete použít Visual Studio Code a rozšíření Nástroje Azure Resource Manageru.
author: mumian
ms.date: 03/04/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: a0c80f18e9cd09b765804aaddbd178b4b3e32a9d
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984448"
---
# <a name="quickstart-create-arm-templates-by-using-visual-studio-code"></a>Úvodní příručka: Vytvoření šablon ARM pomocí kódu sady Visual Studio

Zjistěte, jak pomocí kódu Visual Studia a rozšíření Nástrojů Správce prostředků Azure vytvářet a upravovat šablony Azure Resource Manager (ARM). Můžete vytvořit ARM šablony v kódu Sady Visual Studio bez rozšíření, ale rozšíření poskytuje možnosti automatického dokončování, které zjednodušují vývoj šablon. Informace o konceptech spojených s nasazením a správou řešení Azure najdete v [tématu přehled nasazení šablon](overview.md).

V tomto rychlém startu nasadíte účet úložiště:

![diagram kódu aplikace Visual Studio pro rychlé spuštění šablony správce prostředků](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-template-quickstart-vscode-diagram.png)

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

- [Visual Studio kód](https://code.visualstudio.com/).
- Rozšíření Nástroje Resource Manageru. Instalaci provedete následujícím postupem:

    1. Otevřete Visual Studio Code.
    2. Stisknutím **CTRL+SHIFT+X** otevřete podokno Rozšíření.
    3. Vyhledejte **Nástroje Azure Resource Manageru** a vyberte **Nainstalovat**.
    4. Instalaci rozšíření dokončíte výběrem **Znovu načíst**.

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

Šablonu nemusíte vytvářet od začátku, ale můžete místo toho otevřít některou [šablonu pro rychlý start Azure](https://azure.microsoft.com/resources/templates/). Azure Quickstart Templates je úložiště pro šablony ARM.

Šablona, kterou jsme použili v tomto rychlém startu, se nazývá [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Vytvoření standardního účtu úložiště). Šablona definuje prostředek účtu služby Azure Storage.

1. V kódu sady Visual Studio vyberte **Soubor**>**otevřít soubor**.
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Výběrem **Open** (Otevřít) soubor otevřete.
4. Vyberte **Soubor**>**Uložit jako,** chcete-li soubor uložit jako **azuredeploy.json** do místního počítače.

## <a name="edit-the-template"></a>Úprava šablony

Chcete-li získat možnost upravit šablonu pomocí kódu sady `outputs` Visual Studio, přidejte do oddílu ještě jeden prvek, který zobrazí identifikátor URI úložiště.

1. Přidejte do exportované šablony jeden další výstup:

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

    Pokud jste zkopírovali a vložili kód do kódu sady Visual Studio, zkuste znovu zadat element **hodnoty,** aby se zotřil funkce Technologie IntelliSense rozšíření Nástroje Správce prostředků.

    ![IntelliSense ve Visual Studio Code v šabloně Resource Manageru](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. Chcete-li soubor uložit, vyberte **Uložit**>**Save** soubor.

## <a name="deploy-the-template"></a>Nasazení šablony

Šablony můžete nasadit mnoha způsoby. Prostředí Azure Cloud se používá v tomto rychlém startu. Prostředí cloudpodporuje azure cli a Azure PowerShell. Pomocí voliče karet si můžete vybrat mezi příkazem příkazového příkazu k příkazu k příkazu PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Přihlaste se do služby [Azure Cloud Shell](https://shell.azure.com).

2. V levém horním rohu vyberte preferované prostředí výběrem **prostředí PowerShell** nebo **Bash**(CLI).  Po přepnutí se vyžaduje restartování prostředí.

    # <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

    ![CLI v Cloud Shellu na portálu Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Prostředí Cloud shellu Azure portal PowerShell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-powershell.png)

    ---

3. Vyberte **Nahrát nebo stáhnout soubory** a potom vyberte **Nahrát**.

    # <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

    ![Nahrání souboru v Cloud Shellu na portálu Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Nahrání souboru v Cloud Shellu na portálu Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file-powershell.png)

    ---

    Vyberte soubor, který jste uložili v předchozí části. Výchozí název je **azuredeploy.json**. Soubor šablony musí být přístupný z prostředí.

    Volitelně můžete použít příkaz **ls** a příkaz **cat** k ověření úspěšného nahrání souboru.

    # <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

    ![Zobrazení souboru v Cloud Shellu na portálu Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Zobrazení souboru v Cloud Shellu na portálu Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file-powershell.png)

    ---
4. Ve službě Cloud Shell spusťte následující příkazy. Výběrem odpovídající karty zobrazíte kód PowerShellu nebo kód rozhraní příkazového řádku.

    # <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    ---

    Pokud jste soubor uložili s jiným názvem než **azuredeploy.json**, aktualizujte název souboru šablony.

    Následující snímek obrazovky ukazuje ukázkové nasazení:

    # <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

    ![Šablona nasazení v Cloud Shellu na portálu Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Šablona nasazení v Cloud Shellu na portálu Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template-powershell.png)

    ---

    Na snímku obrazovky je zvýrazněný název účtu úložiště a adresa URL úložiště v části outputs (Výstupy). Název účtu úložiště budete potřebovat v dalším kroku.

5. Spuštěním následujícího příkazu rozhraní příkazového řádku nebo PowerShellu zobrazíte nově vytvořený účet úložiště:

    # <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the Storage Account name:" &&
    read storageAccountName &&
    az storage account show --resource-group $resourceGroupName --name $storageAccountName
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $storageAccountName = Read-Host -Prompt "Enter the Storage Account name"
    Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
    ```

    ---

Další informace o používání účtů úložiště Azure najdete v tématu [Rychlý start: Nahrávání, stahování a výpis objektů blob pomocí webu Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

Hlavním cílem tohoto rychlého zprovoznění byla úprava existující šablony z šablon Azure pro rychlý start pomocí editoru Visual Studio Code. Také jste se naučili, jak nasadit šablonu pomocí cli nebo PowerShell z prostředí Azure Cloud. Šablony Azure pro rychlý start možná nenabízí vše, co potřebujete. Další informace o vývoji šablon najdete v našem novém výukovém programu pro začátečníky:

> [!div class="nextstepaction"]
> [Kurzy pro začátečníky](./template-tutorial-create-first-template.md)
