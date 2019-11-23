---
title: Import SQL BACPAC files with templates
description: Learn how to use SQL Database extension to import SQL BACPAC files with Azure Resource Manager templates.
author: mumian
ms.date: 04/08/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6291daecb578ba20f854dd2e6023890b158b8ebe
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326456"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Kurz: Import souborů SQL BACPAC pomocí šablon Azure Resource Manageru

Learn how to use Azure SQL Database extensions to import a BACPAC file with Azure Resource Manager templates. Deployment artifacts are any files, in addition to the main template file that are needed to complete a deployment. The BACPAC file is an artifact. In this tutorial, you create a template to deploy an Azure SQL Server, a SQL Database, and import a BACPAC file. Informace o nasazování rozšíření virtuálních počítačů Azure pomocí šablon Azure Resource Manageru najdete v [kurzu nasazování rozšíření virtuálních počítačů pomocí šablon Azure Resource Manageru](./resource-manager-tutorial-deploy-vm-extensions.md).

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Příprava souboru BACPAC
> * Otevření šablony pro rychlý start
> * Úprava šablony
> * Nasazení šablony
> * Ověření nasazení

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto článku potřebujete:

* Visual Studio Code with Resource Manager Tools extension. See [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md).
* Pro zlepšení zabezpečení použijte pro účet správce SQL Serveru vygenerované heslo. Tady ukázka generování hesla:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace najdete v [kurzu integrace služby Azure Key Vault v nasazení šablony Resource Manageru](./resource-manager-tutorial-use-key-vault.md). Zároveň doporučujeme heslo každé tři měsíce aktualizovat.

## <a name="prepare-a-bacpac-file"></a>Příprava souboru BACPAC

A BACPAC file is shared in [Github](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Pokud chcete vytvořit vlastní, přečtěte si téma [Export databáze Azure SQL do souboru BACPAC](../sql-database/sql-database-export.md). Pokud se rozhodnete soubor publikovat do vlastního umístění, v pozdější části kurzu budete muset šablonu aktualizovat.

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

The template used in this tutorial is stored in [Github](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. V nástroji Visual Studio Code vyberte **File** (Soubor) >**Open File** (Otevřít soubor).
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```
3. Výběrem **Open** (Otevřít) soubor otevřete.

    Šablona definuje tři prostředky:

   * `Microsoft.Sql/servers`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies).
   * `Microsoft.SQL.servers/databases`.  Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

     Než začnete šablonu přizpůsobovat, je vhodné se s ní nejprve trochu seznámit.
4. Vyberte **File** (Soubor) >**Save As** (Uložit jako) a soubor uložte na místní počítač pod názvem **azuredeploy.json**.

## <a name="edit-the-template"></a>Úprava šablony

Add two additional resources to the template.

* Aby rozšíření služby SQL Database mohlo importovat soubory BACPAC, musíte povolit přístup ke službám Azure. Přidejte k definici serveru SQL následující kód JSON:

    ```json
    {
        "type": "firewallrules",
        "name": "AllowAllAzureIps",
        "location": "[parameters('location')]",
        "apiVersion": "2015-05-01-preview",
        "dependsOn": [
            "[variables('databaseServerName')]"
        ],
        "properties": {
            "startIpAddress": "0.0.0.0",
            "endIpAddress": "0.0.0.0"
        }
    }
    ```

    Šablona by měla vypadat takto:

    ![Azure Resource Manager – Soubor BACPAC pro nasazení rozšíření SQL](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

* Pomocí následujícího kódu JSON přidejte k definici databáze prostředek rozšíření služby SQL Database:

    ```json
    "resources": [
        {
            "name": "Import",
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/databases', variables('databaseServerName'), variables('databaseName'))]"
            ],
            "properties": {
                "storageKeyType": "SharedAccessKey",
                "storageKey": "?",
                "storageUri": "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac",
                "administratorLogin": "[variables('databaseServerAdminLogin')]",
                "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
                "operationMode": "Import",
            }
        }
    ]
    ```

    Šablona by měla vypadat takto:

    ![Azure Resource Manager – Soubor BACPAC pro nasazení rozšíření SQL](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

    Vysvětlení definice prostředku najdete v [referenčních informacích k rozšíření služby SQL Database](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Tady je několik důležitých elementů:

    * **dependsOn:** Prostředek rozšíření se musí vytvořit po vytvoření databáze SQL.
    * **storageKeyType:** Typ klíče úložiště, který se má použít. Hodnota může být `StorageAccessKey` nebo `SharedAccessKey`. Vzhledem k tomu, že uvedený soubor BACPAC je sdílený v účtu služby Azure Storage s veřejným přístupem, používá se zde typ SharedAccessKey.
    * **storageKey:** Klíč úložiště, který se má použít. Pokud je typ klíče úložiště SharedAccessKey, musí tato hodnota začínat na znak „?“.
    * **storageUri:** Identifikátor URI úložiště, které se má použít. Pokud se rozhodnete nepoužít uvedený soubor BACPAC, musíte hodnoty aktualizovat.
    * **administratorLoginPassword:** Heslo správce SQL. Use a generated password. Viz [Požadavky](#prerequisites).

## <a name="deploy-the-template"></a>Nasazení šablony

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Informace o procesu nasazení najdete v části [Nasazení šablony](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Místo toho použijte následující skript PowerShellu pro nasazení:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json"
```

Use a generated password. Viz [Požadavky](#prerequisites).

## <a name="verify-the-deployment"></a>Ověření nasazení

Na portálu vyberte databázi SQL z nově nasazené skupiny prostředků. Vyberte **Editor dotazů (Preview)** a zadejte přihlašovací údaje správce. Měly by se zobrazit dvě tabulky importované do databáze:

![Azure Resource Manager – Soubor BACPAC pro nasazení rozšíření SQL](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili SQL Server a službu SQL Database a importovali jste soubor BACPAC. The BACPAC file is stored in an Azure storage account. Anybody with the URL can access the file. To learn how to secure the BACPAC file (artifact), see

> [!div class="nextstepaction"]
> [Secure the artifacts](./resource-manager-tutorial-secure-artifacts.md)
