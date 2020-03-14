---
title: Vytvořit šablonu – Visual Studio Code
description: Při práci na šablonách Resource Manageru můžete použít Visual Studio Code a rozšíření Nástroje Azure Resource Manageru.
author: mumian
ms.date: 03/04/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 9a829b0c84c397f297539cdb04b3ad027a18c834
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240307"
---
# <a name="quickstart-create-azure-resource-manager-templates-by-using-visual-studio-code"></a>Rychlé zprovoznění: Vytvoření šablon Azure Resource Manageru pomocí nástroje Visual Studio Code

Zjistěte, jak pomocí Visual Studio Code a rozšíření Nástroje Azure Resource Manageru vytvářet a upravovat šablony Azure Resource Manageru. Šablony Resource Manageru můžete v nástroji Visual Studio Code vytvářet i bez tohoto rozšíření, poskytuje však možnosti automatického dokončování, které vývoj šablon zjednodušují. Informace o konceptech spojených s nasazením a správou řešení Azure najdete v tématu [Přehled nasazení šablon](overview.md).

V tomto rychlém startu nasadíte účet úložiště:

![Šablona Resource Manageru rychlý Start pro Visual Studio Code diagram](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-template-quickstart-vscode-diagram.png)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

- [Visual Studio Code](https://code.visualstudio.com/).
- Rozšíření Nástroje Resource Manageru. Instalaci provedete následujícím postupem:

    1. Otevřete Visual Studio Code.
    2. Stisknutím **CTRL+SHIFT+X** otevřete podokno Rozšíření.
    3. Vyhledejte **Nástroje Azure Resource Manageru** a vyberte **Nainstalovat**.
    4. Instalaci rozšíření dokončíte výběrem **Znovu načíst**.

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

Šablonu nemusíte vytvářet od začátku, ale můžete místo toho otevřít některou [šablonu pro rychlý start Azure](https://azure.microsoft.com/resources/templates/). Šablony pro rychlý Start Azure jsou úložiště pro šablony Správce prostředků.

Šablona, kterou jsme použili v tomto rychlém startu, se nazývá [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Vytvoření standardního účtu úložiště). Šablona definuje prostředek účtu služby Azure Storage.

1. V nástroji Visual Studio Code vyberte **File** (Soubor) >**Open File** (Otevřít soubor).
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Výběrem **Open** (Otevřít) soubor otevřete.
4. Vyberte **File** (Soubor) >**Save As** (Uložit jako) a soubor uložte jako **azuredeploy.json** na místní počítač.

## <a name="edit-the-template"></a>Úprava šablony

Chcete-li se setkat s tím, jak šablonu upravit pomocí Visual Studio Code, přidejte do části `outputs` další prvek, abyste zobrazili identifikátor URI úložiště.

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

    Pokud jste zkopírovali a vložili kód ve Visual Studio Code, zkuste znovu zadat element **value**, abyste si vyzkoušeli možnosti IntelliSense v rozšíření Nástroje Resource Manageru.

    ![IntelliSense ve Visual Studio Code v šabloně Resource Manageru](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. Soubor uložte tak, že vyberete **File** (Soubor) >**Save** (Uložit).

## <a name="deploy-the-template"></a>Nasazení šablony

Šablony můžete nasadit mnoha způsoby. V tomto rychlém startu se používá Azure Cloud Shell. Cloud Shell podporuje rozhraní příkazového řádku Azure CLI i Azure PowerShell. Pomocí voliče karet můžete vybrat mezi rozhraním příkazového řádku a prostředím PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Přihlaste se do služby [Azure Cloud Shell](https://shell.azure.com).

2. Vyberte prostředí **PowerShell** nebo **bash**(CLI) v levém horním rohu a zvolte preferované prostředí.  Po přepnutí se vyžaduje restartování prostředí.

    # <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

    ![CLI v Cloud Shellu na portálu Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Prostředí PowerShell pro Azure Portal Cloud Shell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-powershell.png)

    ---

3. Vyberte **Nahrát nebo stáhnout soubory** a potom vyberte **Nahrát**.

    # <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

    ![Nahrání souboru v Cloud Shellu na portálu Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Nahrání souboru v Cloud Shellu na portálu Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file-powershell.png)

    ---

    Vyberte soubor, který jste uložili v předchozí části. Výchozí název je **azuredeploy.json**. Soubor šablony musí být přístupný z prostředí.

    Volitelně můžete pomocí příkazu **ls** a příkazu **Cat** ověřit, jestli se soubor úspěšně nahrál.

    # <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

    ![Zobrazení souboru v Cloud Shellu na portálu Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Zobrazení souboru v Cloud Shellu na portálu Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file-powershell.png)

    ---
4. Ve službě Cloud Shell spusťte následující příkazy. Výběrem odpovídající karty zobrazíte kód PowerShellu nebo kód rozhraní příkazového řádku.

    # <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)
    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    az group create --name $resourceGroupName --location "$location" &&
    az group deployment create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

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

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

Hlavním cílem tohoto rychlého zprovoznění byla úprava existující šablony z šablon Azure pro rychlý start pomocí editoru Visual Studio Code. Zjistili jste taky, jak šablonu nasadit pomocí rozhraní příkazového řádku nebo PowerShellu z Azure Cloud shellu. Šablony Azure pro rychlý start možná nenabízí vše, co potřebujete. Další informace o vývoji šablon najdete v naší nové řadě kurzů pro začátečníky:

> [!div class="nextstepaction"]
> [Začátečník – kurzy](./template-tutorial-create-first-template.md)
