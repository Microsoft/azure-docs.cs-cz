---
title: Pomocí Visual Studio Code vytvořit šablonu Azure Resource Manager | Microsoft Docs
description: Pro práci na šablonách Správce prostředků použijte Visual Studio Code a rozšíření Azure Resource Manager Tools.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: b62d4f8b43c62ff401b4d74932b131c1bf8fd63f
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169563"
---
# <a name="quickstart-create-azure-resource-manager-templates-by-using-visual-studio-code"></a>Rychlý Start: vytváření Azure Resource Manager šablon pomocí Visual Studio Code

Naučte se používat Visual Studio Code a rozšíření Azure Resource Manager Tools k vytváření a úpravám šablon Azure Resource Manager. Můžete vytvořit šablony Správce prostředků v Visual Studio Code bez rozšíření, ale rozšíření poskytuje možnosti automatického dokončování, které zjednodušují vývoj šablon. Informace o konceptech spojených s nasazením a správou řešení Azure najdete v tématu [přehled Azure Resource Manager](resource-group-overview.md).

V tomto rychlém startu nasadíte účet úložiště:

![Šablona Resource Manageru rychlý Start pro Visual Studio Code diagram](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/resource-manager-template-quickstart-vscode-diagram.png)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

- [Visual Studio Code](https://code.visualstudio.com/).
- Rozšíření nástrojů Správce prostředků. K instalaci použijte tento postup:

    1. Otevřete Visual Studio Code.
    2. Stisknutím **kombinace kláves CTRL + SHIFT + X** otevřete podokno rozšíření.
    3. Vyhledejte **Azure Resource Manager nástroje**a pak vyberte **nainstalovat**.
    4. Chcete-li dokončit instalaci rozšíření, vyberte možnost **znovu načíst** .

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý Start

Místo vytvoření zcela nové šablony otevřete šablonu ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/). Šablony pro rychlý Start Azure jsou úložiště pro šablony Správce prostředků.

Šablona použitá v tomto rychlém startu se nazývá [vytvořit účet úložiště úrovně Standard](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Šablona definuje prostředek účtu Azure Storage.

1. Z Visual Studio Code vyberte **soubor**>**otevřít soubor**.
2. Do pole **název souboru**vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Vyberte **otevřít** a otevřete soubor.
4. Vyberte **soubor**>**Uložit jako** a uložte soubor jako **azuredeploy. JSON** do místního počítače.

## <a name="edit-the-template"></a>Úprava šablony

Chcete-li se naučit, jak šablonu upravit pomocí Visual Studio Code, přidejte do části `outputs` další prvek, abyste zobrazili identifikátor URI úložiště.

1. Přidejte do exportované šablony další výstup:

    ```json
    "storageUri": {
      "type": "string",
      "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
    }
    ```

    Až skončíte, bude oddíl výstupy vypadat takto:

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

    Pokud jste zkopírovali a vložili kód uvnitř Visual Studio Code, zkuste znovu zadat element **Value** pro prostředí funkce IntelliSense rozšíření Správce prostředků Tools.

    ![Správce prostředků šablon pro Visual Studio Code IntelliSense](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. Vyberte **soubor**>**Uložit** a uložte soubor.

## <a name="deploy-the-template"></a>Nasazení šablony

Existuje mnoho způsobů, jak nasadit šablony. V tomto rychlém startu se používá Azure Cloud Shell. Cloud Shell podporuje rozhraní příkazového řádku Azure CLI i Azure PowerShell. Pomocí voliče karet můžete vybrat mezi rozhraním příkazového řádku a prostředím PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Přihlaste se ke [službě Azure Cloud Shell](https://shell.azure.com) .

2. Vyberte prostředí **PowerShell** nebo **bash**(CLI) v levém horním rohu a zvolte preferované prostředí.  Při přepnutí se vyžaduje restartování prostředí.

    # <a name="clitabcli"></a>[CLI](#tab/CLI)

    ![Azure Portal rozhraní příkazového řádku Cloud Shell](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)

    # <a name="powershelltabpowershell"></a>[Prostředí](#tab/PowerShell)

    ![Prostředí PowerShell pro Azure Portal Cloud Shell](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-powershell.png)

    ---

3. Vyberte **Odeslat/stáhnout soubory**a pak vyberte **nahrát**.

    # <a name="clitabcli"></a>[CLI](#tab/CLI)

    ![Azure Portal nahrání souboru do Cloud shellu](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)

    # <a name="powershelltabpowershell"></a>[Prostředí](#tab/PowerShell)

    ![Azure Portal nahrání souboru do Cloud shellu](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file-powershell.png)

    ---

    Vyberte soubor, který jste uložili v předchozí části. Výchozí název je **azuredeploy. JSON**. Soubor šablony musí být přístupný z prostředí.

    Volitelně můžete pomocí příkazu **ls** a příkazu **Cat** ověřit, jestli se soubor úspěšně nahrál.

    # <a name="clitabcli"></a>[CLI](#tab/CLI)

    ![Soubor seznamu Azure Portal Cloud Shell](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)

    # <a name="powershelltabpowershell"></a>[Prostředí](#tab/PowerShell)

    ![Soubor seznamu Azure Portal Cloud Shell](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file-powershell.png)

    ---
4. Ve službě cloud Shell spusťte následující příkazy. Vyberte kartu pro zobrazení kódu PowerShellu nebo kódu CLI.

    # <a name="clitabcli"></a>[CLI](#tab/CLI)
    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    az group create --name $resourceGroupName --location "$location" &&
    az group deployment create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershelltabpowershell"></a>[Prostředí](#tab/PowerShell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    ---

    Aktualizujte název souboru šablony, pokud soubor uložíte do jiného názvu než **azuredeploy. JSON**.

    Následující snímek obrazovky ukazuje ukázkové nasazení:

    # <a name="clitabcli"></a>[CLI](#tab/CLI)

    ![Šablona nasazení Azure Portal Cloud Shell](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)

    # <a name="powershelltabpowershell"></a>[Prostředí](#tab/PowerShell)

    ![Šablona nasazení Azure Portal Cloud Shell](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template-powershell.png)

    ---

    Název účtu úložiště a adresa URL úložiště v oddílu Outputs (výstupy) jsou na snímku obrazovky zvýrazněné. V dalším kroku budete potřebovat název účtu úložiště.

5. Pro vytvoření seznamu nově vytvořeného účtu úložiště spusťte následující příkaz CLI nebo PowerShell:

    # <a name="clitabcli"></a>[CLI](#tab/CLI)
    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the Storage Account name:" &&
    read storageAccountName &&
    az storage account show --resource-group $resourceGroupName --name $storageAccountName
    ```

    # <a name="powershelltabpowershell"></a>[Prostředí](#tab/PowerShell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $storageAccountName = Read-Host -Prompt "Enter the Storage Account name"
    Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
    ```

    ---

Další informace o používání účtů úložiště Azure najdete v tématu [rychlý Start: nahrání, stažení a výpis objektů BLOB pomocí Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky Azure nepotřebujete, vyčistěte prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  V rámci skupiny prostředků se zobrazí celkem šest prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

Hlavním cílem tohoto rychlého startu je použít Visual Studio Code k úpravám existující šablony ze šablon Azure pro rychlý Start. Zjistili jste taky, jak šablonu nasadit pomocí rozhraní příkazového řádku nebo PowerShellu z Azure Cloud shellu. Šablony ze šablon Azure pro rychlý Start vám nemusí poskytnout všechno, co potřebujete. Další informace o vývoji šablon najdete v naší nové řadě kurzů pro začátečníky:

> [!div class="nextstepaction"]
> [Začátečník – kurzy](./template-tutorial-create-first-template.md)
