---
title: Import SQL BACPAC files with templates
description: Learn how to use SQL Database extension to import SQL BACPAC files with Azure Resource Manager templates.
author: mumian
ms.date: 11/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 741521551335712400e5f61822d7dda31199d3df
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422161"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Kurz: Import souborů SQL BACPAC pomocí šablon Azure Resource Manageru

Learn how to use Azure SQL Database extensions to import a BACPAC file with Azure Resource Manager templates. Deployment artifacts are any files, in addition to the main template files that are needed to complete a deployment. The BACPAC file is an artifact. In this tutorial, you create a template to deploy an Azure SQL Server, a SQL Database, and import a BACPAC file. Informace o nasazování rozšíření virtuálních počítačů Azure pomocí šablon Azure Resource Manageru najdete v [kurzu nasazování rozšíření virtuálních počítačů pomocí šablon Azure Resource Manageru](./resource-manager-tutorial-deploy-vm-extensions.md).

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

A BACPAC file is shared in [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Pokud chcete vytvořit vlastní, přečtěte si téma [Export databáze Azure SQL do souboru BACPAC](../sql-database/sql-database-export.md). Pokud se rozhodnete soubor publikovat do vlastního umístění, v pozdější části kurzu budete muset šablonu aktualizovat.

The BACPAC file must be stored in an Azure Storage account before it can be imported using Resource Manager template.

1. Open the [Cloud shell](https://shell.azure.com).
1. Select **Upload/Download files**, and then select **Upload**.
1. Specify the following URL and then select **Open**.

    ```url
    https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac
    ```

1. Copy and paste the following PowerShell script into the shell window.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFile = "$HOME/SQLDatabaseExtension.bacpac"
    $blobName = "SQLDatabaseExtension.bacpac"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    Set-AzStorageBlobContent -File $bacpacFile `
                             -Container $containerName `
                             -Blob $blobName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$blobName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Write down storage account key and the BACPAC file URL. You need these values when you deploy the template.

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

The template used in this tutorial is stored in [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

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

1. Add two more parameters at the end of the **parameters** section to set the storage account key and the BACPAC URL:

    ```json
    "storageAccountKey": {
      "type":"string",
      "metadata":{
        "description": "Specifies the key of the storage account where the BACPAC file is stored."
      }
    },
    "bacpacUrl": {
      "type":"string",
      "metadata":{
        "description": "Specifies the URL of the BACPAC file."
      }
    }
    ```

    Add a comma after **adminPassword**. To format the JSON file from VS Code, press **[SHIFT]+[ALT]+F**.

    See [Prepare a BACPAC file](#prepare-a-bacpac-file) about getting these two values.

1. Add two additional resources to the template.

    * To allow the SQL database extension to import BACPAC files, you need to allow traffic from Azure services. Add the following firewall rule definition under the SQL server definition:

        ```json
        {
          "type": "firewallrules",
          "apiVersion": "2015-05-01-preview",
          "name": "AllowAllAzureIps",
          "location": "[parameters('location')]",
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
              "type": "extensions",
              "apiVersion": "2014-04-01",
              "name": "Import",
              "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/databases', variables('databaseServerName'), variables('databaseName'))]"
              ],
              "properties": {
                "storageKeyType": "StorageAccessKey",
                "storageKey": "[parameters('storageAccountKey')]",
                "storageUri": "[parameters('bacpacUrl')]",
                "administratorLogin": "[variables('databaseServerAdminLogin')]",
                "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
                "operationMode": "Import"
              }
            }
        ]
        ```

        Šablona by měla vypadat takto:

        ![Azure Resource Manager – Soubor BACPAC pro nasazení rozšíření SQL](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Vysvětlení definice prostředku najdete v [referenčních informacích k rozšíření služby SQL Database](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Tady je několik důležitých elementů:

        * **dependsOn:** Prostředek rozšíření se musí vytvořit po vytvoření databáze SQL.
        * **storageKeyType**: Specify the type of the storage key to use. Hodnota může být `StorageAccessKey` nebo `SharedAccessKey`. Use `StorageAccessKey` in this tutorial.
        * **storageKey**: Specify the key for the storage account where the BACPAC file is stored. If storage key type is SharedAccessKey, it must be preceded with a "?"
        * **storageUri**: Specify the URL of the BACPAC file stored in a storage account.
        * **administratorLoginPassword:** Heslo správce SQL. Use a generated password. Viz [Požadavky](#prerequisites).

## <a name="deploy-the-template"></a>Nasazení šablony

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Informace o procesu nasazení najdete v části [Nasazení šablony](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Místo toho použijte následující skript PowerShellu pro nasazení:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

Consider using the same project name as you used when you prepared the bacpac file, so that all the resources are stored within the same resource group.  It is easier for managing resource, such as cleaning up the resources. If you use the same project name, you can either remove the **New-AzResourceGroup** command from the script, or answer y or n when you are asked whether you want to update the existing resource group.

Use a generated password. Viz [Požadavky](#prerequisites).

## <a name="verify-the-deployment"></a>Ověření nasazení

To access the SQL server from your client computer, you need to add an additional firewall rule. For more information, see [Create and manage IP firewall rules](../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules).

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
