---
title: Import souborů SQL BACPAC pomocí šablon
description: Naučte se používat rozšíření SQL Database k importu souborů SQL BACPAC pomocí šablon Azure Resource Manager.
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

Naučte se používat rozšíření Azure SQL Database k importu souboru BACPAC pomocí šablon Azure Resource Manager. Artefakty nasazení jsou kromě hlavních souborů šablon, které jsou potřeba k dokončení nasazení, také všechny soubory. Soubor BACPAC je artefakt. V tomto kurzu vytvoříte šablonu pro nasazení SQL Server Azure, SQL Database a importu souboru BACPAC. Informace o nasazování rozšíření virtuálních počítačů Azure pomocí šablon Azure Resource Manageru najdete v [kurzu nasazování rozšíření virtuálních počítačů pomocí šablon Azure Resource Manageru](./resource-manager-tutorial-deploy-vm-extensions.md).

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Příprava souboru BACPAC
> * Otevření šablony pro rychlý start
> * Úprava šablony
> * Nasazení šablony
> * Ověření nasazení

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Code s rozšířením nástrojů Správce prostředků Tools. Pokud [chcete vytvořit Azure Resource Manager šablony](./resource-manager-tools-vs-code.md), přečtěte si téma použití Visual Studio Code.
* Pro zlepšení zabezpečení použijte pro účet správce SQL Serveru vygenerované heslo. Tady ukázka generování hesla:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace najdete v [kurzu integrace služby Azure Key Vault v nasazení šablony Resource Manageru](./resource-manager-tutorial-use-key-vault.md). Zároveň doporučujeme heslo každé tři měsíce aktualizovat.

## <a name="prepare-a-bacpac-file"></a>Příprava souboru BACPAC

Soubor BACPAC se sdílí na [GitHubu](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Pokud chcete vytvořit vlastní, přečtěte si téma [Export databáze Azure SQL do souboru BACPAC](../sql-database/sql-database-export.md). Pokud se rozhodnete soubor publikovat do vlastního umístění, v pozdější části kurzu budete muset šablonu aktualizovat.

Soubor BACPAC musí být uložený v účtu Azure Storage, aby se mohl importovat pomocí šablony Správce prostředků.

1. Otevřete [Cloud Shell](https://shell.azure.com).
1. Vyberte **Odeslat/stáhnout soubory**a pak vyberte **nahrát**.
1. Zadejte následující adresu URL a pak vyberte **otevřít**.

    ```url
    https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac
    ```

1. Zkopírujte následující skript prostředí PowerShell a vložte ho do okna prostředí.

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

1. Zapište klíč účtu úložiště a adresu URL souboru BACPAC. Tyto hodnoty budete potřebovat při nasazení šablony.

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

Šablona použitá v tomto kurzu je uložená na [GitHubu](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

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

1. Pokud chcete nastavit klíč účtu úložiště a adresu URL BACPAC, přidejte na konec oddílu **Parameters** další dva parametry:

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

    Za **adminPassword**vložte čárku. K naformátování souboru JSON z VS Code stiskněte **[SHIFT] + [ALT] + F**.

    Viz [Příprava souboru BacPac](#prepare-a-bacpac-file) o získání těchto dvou hodnot.

1. Přidejte do šablony dva další prostředky.

    * Pokud chcete, aby rozšíření SQL Database naimportovalo soubory BACPAC, je potřeba, abyste povolili provoz ze služeb Azure. Do definice SQL serveru přidejte následující definici pravidla brány firewall:

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
        * **storageKeyType**: zadejte typ klíče úložiště, který se má použít. Hodnota může být `StorageAccessKey` nebo `SharedAccessKey`. V tomto kurzu použijte `StorageAccessKey`.
        * **storageKey**: Zadejte klíč pro účet úložiště, ve kterém je uložený soubor BacPac. Pokud je typ klíče úložiště SharedAccessKey, musí předcházet "?".
        * **storageUri**: zadejte adresu URL souboru BacPac uloženého v účtu úložiště.
        * **administratorLoginPassword:** Heslo správce SQL. Použijte vygenerované heslo. Viz [Požadavky](#prerequisites).

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

Zvažte použití stejného názvu projektu, jako jste použili při přípravě souboru BacPac, takže všechny prostředky budou uloženy ve stejné skupině prostředků.  Správa prostředků je jednodušší, jako je třeba vyčištění prostředků. Pokud použijete stejný název projektu, můžete buď ze skriptu odebrat příkaz **New-AzResourceGroup** , nebo odpovědět na y nebo n, když se zobrazí dotaz, zda chcete aktualizovat existující skupinu prostředků.

Použijte vygenerované heslo. Viz [Požadavky](#prerequisites).

## <a name="verify-the-deployment"></a>Ověření nasazení

Chcete-li získat přístup k SQL serveru z klientského počítače, je nutné přidat další pravidlo brány firewall. Další informace najdete v tématu [Vytvoření a Správa pravidel brány firewall protokolu IP](../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules).

Na portálu vyberte databázi SQL z nově nasazené skupiny prostředků. Vyberte **Editor dotazů (Preview)** a zadejte přihlašovací údaje správce. Měly by se zobrazit dvě tabulky importované do databáze:

![Azure Resource Manager – Soubor BACPAC pro nasazení rozšíření SQL](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili SQL Server a službu SQL Database a importovali jste soubor BACPAC. Soubor BACPAC je uložený v účtu úložiště Azure. Kdokoli s adresou URL může získat přístup k souboru. Informace o tom, jak zabezpečit soubor BACPAC (artefakt), najdete v tématu.

> [!div class="nextstepaction"]
> [Zabezpečit artefakty](./resource-manager-tutorial-secure-artifacts.md)
