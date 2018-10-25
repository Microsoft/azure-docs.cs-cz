---
title: Vytvoření šablony Resource Manageru pomocí nástroje Visual Studio Code | Microsoft Docs
description: Při práci na šablonách Resource Manageru můžete použít Visual Studio Code a rozšíření Nástroje Azure Resource Manageru.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/18/2018
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: e8ca232f3c5664f69db800648b46abaf0822d6f1
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458135"
---
# <a name="quickstart-create-azure-resource-manager-templates-by-using-visual-studio-code"></a>Rychlé zprovoznění: Vytvoření šablon Azure Resource Manageru pomocí nástroje Visual Studio Code

Zjistěte, jak pomocí Visual Studio Code a rozšíření Nástroje Azure Resource Manageru vytvářet a upravovat šablony Azure Resource Manageru. Šablony Resource Manageru můžete v nástroji Visual Studio Code vytvářet i bez tohoto rozšíření, poskytuje však možnosti automatického dokončování, které vývoj šablon zjednodušují. Abyste porozuměli konceptům spojeným s nasazením a správou řešení Azure, podívejte se na téma [Přehled Azure Resource Manageru](resource-group-overview.md).

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

Abyste se naučili upravit šablonu pomocí Visual Studio Code, přidáte další element do části `outputs`.

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

    Pokud jste zkopírovali a vložili kód v nástroji Visual Studio Code, zkuste znovu zadat element **value**, abyste si vyzkoušeli možnosti IntelliSense v rozšíření Nástroje Resource Manageru.

    ![IntelliSense v nástroji Visual Studio Code v šabloně Resource Manageru](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. Soubor uložte tak, že vyberete **File** (Soubor) >**Save** (Uložit).

## <a name="deploy-the-template"></a>Nasazení šablony

Šablony můžete nasadit mnoha způsoby.  V tomto rychlém startu použijete Azure Cloud Shell. Cloud Shell podporuje jak Azure CLI, tak i Azure PowerShell. 

1. Přihlaste se do služby [Azure Cloud Shell](https://shell.azure.com).

    ![CLI v Cloud Shellu na portálu Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)
2. V levém horním rohu služby Cloud Shell se zobrazí buď **PowerShell**, nebo **Bash**. Pokud chcete použít rozhraní příkazového řádku, musíte otevřít relaci Bash. Pokud chcete spustit PowerShell, musíte otevřít relaci PowerShellu. Chcete-li přepnout mezi Bash a PowerShell, vyberte šipku dolů. Viz předchozí snímek obrazovky. Po přepnutí se vyžaduje restartování prostředí.
3. Vyberte **Nahrát nebo stáhnout soubory** a potom vyberte **Nahrát**.

    # <a name="clitabcli"></a>[Rozhraní příkazového řádku](#tab/CLI)

    ![Nahrání souboru v Cloud Shellu na portálu Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ![Nahrání souboru v Cloud Shellu na portálu Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file-powershell.png)
    
    ---

    Soubor šablony musíte nejprve nahrát, a až pak ho můžete nasadit z prostředí.
5. Vyberte soubor, který jste uložili v předchozí části. Výchozí název je **azuredeploy.json**.
6. V Cloud Shellu spusťte příkaz **ls** a ověřte, že se soubor nahrál úspěšně. Obsah šablony můžete ověřit také pomocí příkazu **cat**. Následující obrázek ukazuje spuštění příkazu v prostředí Bash.  Stejné příkazy se používají i v relaci PowerShellu.

    # <a name="clitabcli"></a>[Rozhraní příkazového řádku](#tab/CLI)

    ![Zobrazení souboru v Cloud Shellu na portálu Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ![Zobrazení souboru v Cloud Shellu na portálu Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file-powershell.png)
    
    ---
7. Ve službě Cloud Shell spusťte následující příkazy. Výběrem odpovídající karty zobrazíte kód PowerShellu nebo kód rozhraní příkazového řádku.

    # <a name="clitabcli"></a>[Rozhraní příkazového řádku](#tab/CLI)
    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the name for this deployment:" &&
    read deploymentName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    az group create --name $resourceGroupName --location $location &&
    az group deployment create --name $deploymentName --resource-group $resourceGroupName --template-file "azuredeploy.json"
    ```
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $deploymentName = Read-Host -Prompt "Enter the name for this deployment"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $resourceGroupName -TemplateFile "azuredeploy.json"
    ```
    
    ---

    Pokud jste soubor uložili s jiným názvem než **azuredeploy.json**, aktualizujte název souboru šablony.

    Následující snímek obrazovky ukazuje ukázkové nasazení:

    # <a name="clitabcli"></a>[Rozhraní příkazového řádku](#tab/CLI)

    ![Šablona nasazení v Cloud Shellu na portálu Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ![Šablona nasazení v Cloud Shellu na portálu Azure Portal](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template-powershell.png)
    
    ---

    Na snímku obrazovky je zvýrazněný název účtu úložiště a adresa URL úložiště v části outputs (Výstupy). Název účtu úložiště budete potřebovat v dalším kroku.

7. Spuštěním následujícího příkazu rozhraní příkazového řádku nebo PowerShellu zobrazíte nově vytvořený účet úložiště:

    # <a name="clitabcli"></a>[Rozhraní příkazového řádku](#tab/CLI)
    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the Storage Account name:" &&
    read storageAccountName &&
    az storage account show --resource-group $resourceGroupName --name $storageAccountName
    ```
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $storageAccountName = Read-Host -Prompt "Enter the Storage Account name"
    Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
    ```
    
    ---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

Hlavním cílem tohoto rychlého zprovoznění byla úprava existující šablony z šablon Azure pro rychlý start pomocí editoru Visual Studio Code. Také jste zjistili, jak šablonu nasadit pomocí rozhraní příkazového řádku nebo PowerShellu ve službě Azure Cloud Shell. Šablony Azure pro rychlý start možná nenabízí vše, co potřebujete. V dalším kurzu se dozvíte, jak v referenčních informacích k šablonám vyhledat potřebné informace, abyste mohli vytvořit šifrovaný účet služby Azure Storage.

> [!div class="nextstepaction"]
> [Vytvoření šifrovaného účtu úložiště](./resource-manager-tutorial-create-encrypted-storage-accounts.md)
