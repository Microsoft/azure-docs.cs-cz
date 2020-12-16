---
title: Import souborů SQL BACPAC pomocí šablon
description: Naučte se používat rozšíření Azure SQL Database k importu souborů SQL BACPAC pomocí šablon Azure Resource Manager (šablony ARM).
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 2d50903f464c03157ee393787af6ddfdad975aed
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588025"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>Kurz: Import souborů SQL BACPAC pomocí šablon ARM

Naučte se používat rozšíření Azure SQL Database k importu souboru [BacPac](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) pomocí šablon Azure Resource Manager (šablony ARM). Artefakty nasazení jsou kromě hlavních souborů šablon, které jsou potřeba k dokončení nasazení, také všechny soubory. Soubor BACPAC je artefaktem.

V tomto kurzu vytvoříte šablonu pro nasazení [logického SQL serveru](../../azure-sql/database/logical-servers.md) a jediné databáze a naimportujete soubor BacPac. Informace o tom, jak nasadit rozšíření virtuálních počítačů Azure pomocí šablon ARM, najdete v tématu [kurz: nasazení rozšíření virtuálních počítačů pomocí šablon ARM](./template-tutorial-deploy-vm-extensions.md).

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
>
> * Připravte soubor BACPAC.
> * Otevřete šablonu pro rychlý Start.
> * Upravte šablonu.
> * Nasazení šablony
> * Ověřte nasazení.

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto článku potřebujete:

* Visual Studio Code s rozšířením Nástroje Resource Manageru Další informace najdete v tématu [rychlý Start: vytvoření šablon ARM pomocí Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md).
* Chcete-li zvýšit zabezpečení, použijte vygenerované heslo pro účet správce serveru. Tady je ukázka, kterou můžete použít k vygenerování hesla:

    ```console
    openssl rand -base64 32
    ```

    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace najdete v tématu [kurz: integrace Azure Key Vault v nasazení šablony ARM](./template-tutorial-use-key-vault.md). Zároveň doporučujeme heslo každé tři měsíce aktualizovat.

## <a name="prepare-a-bacpac-file"></a>Příprava souboru BACPAC

Soubor BACPAC se sdílí na [GitHubu](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Pokud si chcete vytvořit vlastní, přečtěte si téma [Export databáze z Azure SQL Database do souboru BacPac](../../azure-sql/database/database-export.md). Pokud se rozhodnete soubor publikovat do vlastního umístění, v pozdější části kurzu budete muset šablonu aktualizovat.

Soubor BACPAC musí být uložený v účtu Azure Storage, aby se mohl importovat pomocí šablony ARM. Následující skript prostředí PowerShell připraví soubor BACPAC pomocí těchto kroků:

* Stáhněte si soubor BACPAC.
* Vytvořit účet služby Azure Storage
* Vytvořte kontejner objektů BLOB účtu úložiště.
* Nahrajte soubor BACPAC do kontejneru.
* Zobrazte klíč účtu úložiště a adresu URL objektu BLOB.

1. Vyberte **zkusit** pro otevření prostředí. Pak vložte následující skript PowerShellu do okna prostředí.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Zapište klíč účtu úložiště a adresu URL souboru BACPAC. Tyto hodnoty budete potřebovat při nasazení šablony.

## <a name="open-a-quickstart-template"></a>Otevření šablony rychlého startu

Šablona použitá v tomto kurzu je uložená na [GitHubu](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. Z Visual Studio Code vyberte **soubor**  >  **otevřít soubor**.
1. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. Výběrem **Open** (Otevřít) soubor otevřete.

    V šabloně jsou definované dva prostředky:

   * `Microsoft.Sql/servers`. Viz [referenční informace k šablonám](/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL.servers/databases`. Viz [referenční informace k šablonám](/azure/templates/microsoft.sql/servers/databases).

        Před přizpůsobením šablony je užitečné získat základní informace o této šabloně.
1. Vyberte **soubor**  >  **Uložit jako** a uložte kopii souboru do místního počítače s názvem *azuredeploy.js*.

## <a name="edit-the-template"></a>Úprava šablony

1. Přidejte další dva parametry na konci `parameters` oddílu a nastavte klíč účtu úložiště a adresu URL BacPac.

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

    Za uzavírací složenou `adminPassword` závorku vlastnosti přidejte čárku ( `}` ). Pokud chcete soubor JSON naformátovat z Visual Studio Code, vyberte SHIFT + ALT + F.

    Chcete-li získat tyto dvě hodnoty, přečtěte si téma [Příprava souboru BacPac](#prepare-a-bacpac-file).

1. Přidejte do šablony dva další prostředky.

    * Aby rozšíření SQL Database mohlo importovat soubory BACPAC, je potřeba, abyste povolili provoz ze služeb Azure. Do definice serveru přidejte následující definici pravidla brány firewall:

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2015-05-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        Šablona vypadá takto:

        ![Šablona s definicí pravidla brány firewall](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Pomocí následujícího kódu JSON přidejte k definici databáze prostředek rozšíření služby SQL Database:

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        Šablona vypadá takto:

        ![Šablona s rozšířením SQL Database](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Vysvětlení definice prostředku najdete v [referenčních informacích k rozšíření služby SQL Database](/azure/templates/microsoft.sql/servers/databases/extensions). Tady je několik důležitých elementů:

        * `dependsOn`: Prostředek rozšíření musí být vytvořen po vytvoření databáze.
        * `storageKeyType`: Zadejte typ klíče úložiště, který se má použít. Hodnota může být `StorageAccessKey` nebo `SharedAccessKey`. Použijte `StorageAccessKey` v tomto kurzu.
        * `storageKey`: Zadejte klíč pro účet úložiště, ve kterém je uložený soubor BACPAC. Pokud je typ klíče úložiště `SharedAccessKey` , musí předcházet "?".
        * `storageUri`: Zadejte adresu URL souboru BACPAC uloženého v účtu úložiště.
        * `administratorLoginPassword`: Heslo správce SQL. Použijte vygenerované heslo. Viz [Požadavky](#prerequisites).

Hotová šablona vypadá takto:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>Nasazení šablony

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Informace o procesu nasazení najdete v části [Nasazení šablony](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Místo toho použijte následující skript PowerShellu pro nasazení:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

Zvažte použití stejného názvu projektu, který jste použili při přípravě souboru BACPAC tak, aby všechny prostředky byly uloženy ve stejné skupině prostředků. Tímto způsobem je snazší spravovat úlohy prostředků, jako je třeba vyčištění prostředků. Pokud použijete stejný název projektu, můžete buď odebrat `New-AzResourceGroup` příkaz ze skriptu, nebo odpovědět ano (y) nebo ne (n), když se zobrazí dotaz, zda chcete aktualizovat existující skupinu prostředků.

Použijte vygenerované heslo. Viz [Požadavky](#prerequisites).

## <a name="verify-the-deployment"></a>Ověření nasazení

Chcete-li získat přístup k serveru z klientského počítače, je nutné přidat další pravidlo brány firewall. Další informace najdete v tématu [Vytvoření a Správa pravidel brány firewall protokolu IP](../../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules).

V Azure Portal vyberte databázi z nově nasazené skupiny prostředků. Vyberte **Editor dotazů (Preview)** a zadejte přihlašovací údaje správce. Uvidíte, že se do databáze naimportovaly dvě tabulky.

![Editor dotazů (Preview)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. V Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
1. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
1. Vyberte název skupiny prostředků. V rámci skupiny prostředků se zobrazí celkem šest prostředků.
1. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili server a databázi a naimportovali BACPAC soubor. Další informace o řešení potíží s nasazením šablony najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Řešení potíží s nasazováním šablon ARM](./template-tutorial-troubleshoot.md)
