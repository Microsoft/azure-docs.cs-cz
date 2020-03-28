---
title: Import souborů SQL BACPAC pomocí šablon
description: Zjistěte, jak pomocí rozšíření Azure SQL Database importovat soubory SQL BACPAC pomocí šablon Azure Resource Manageru.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8e65ebbfa0971bf2156165b55ca18eee3cc74bc9
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239282"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>Kurz: Import souborů SQL BACPAC se šablonami ARM

Zjistěte, jak pomocí rozšíření Azure SQL Database importovat soubor BACPAC pomocí šablon Azure Resource Manager (ARM). Artefakty nasazení jsou všechny soubory, kromě hlavní soubory šablony, které jsou potřebné k dokončení nasazení. Soubor BACPAC je artefakt. 

V tomto kurzu vytvoříte šablonu pro nasazení serveru Azure SQL a databáze SQL a importovat soubor BACPAC. Informace o tom, jak nasadit rozšíření virtuálních strojů Azure pomocí šablon ARM, najdete [v tématu Kurz: Nasazení rozšíření virtuálních strojů se šablonami ARM](./template-tutorial-deploy-vm-extensions.md).

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Připravte soubor BACPAC.
> * Otevřete šablonu rychlého startu.
> * Upravte šablonu.
> * Nasaďte šablonu.
> * Ověřte nasazení.

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Code s rozšířením Nástroje Resource Manageru Viz [Použití kódu Visual Studia k vytvoření šablon ARM](./use-vs-code-to-create-template.md).
* Chcete-li zvýšit zabezpečení, použijte vygenerované heslo pro účet správce Azure SQL Server. Zde je ukázka, kterou můžete použít ke generování hesla:

    ```console
    openssl rand -base64 32
    ```

    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace naleznete [v tématu: Integrace trezoru klíčů Azure v nasazení šablony ARM](./template-tutorial-use-key-vault.md). Zároveň doporučujeme heslo každé tři měsíce aktualizovat.

## <a name="prepare-a-bacpac-file"></a>Příprava souboru BACPAC

Soubor BACPAC je sdílen v [GitHubu](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Pokud chcete vytvořit vlastní, přečtěte si téma [Export databáze Azure SQL do souboru BACPAC](../../sql-database/sql-database-export.md). Pokud se rozhodnete soubor publikovat do vlastního umístění, v pozdější části kurzu budete muset šablonu aktualizovat.

Soubor BACPAC musí být uloženy v účtu Azure Storage před jeho importpomocí šablony ARM. Následující skript prostředí PowerShell připraví soubor BACPAC pomocí následujících kroků:

* Stáhněte si soubor BACPAC.
* Vytvořit účet služby Azure Storage
* Vytvořte kontejner objektů blob účtu úložiště.
* Nahrajte soubor BACPAC do kontejneru.
* Zobrazte klíč účtu úložiště a adresu URL objektu blob.

1. Vyberte **Zkuste to** otevřít prostředí cloudu. Pak vložte následující skript Prostředí PowerShell do okna prostředí.

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

1. Poznamenejte si klíč účtu úložiště a adresu URL souboru BACPAC. Tyto hodnoty potřebujete při nasazení šablony.

## <a name="open-a-quickstart-template"></a>Otevření šablony rychlého startu

Šablona použitá v tomto kurzu je uložena v [GitHubu](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. V kódu sady Visual Studio vyberte **Soubor** > **otevřít soubor**.
1. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. Výběrem **Open** (Otevřít) soubor otevřete.

    V šabloně jsou definovány dva prostředky:

   * `Microsoft.Sql/servers`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL.servers/databases`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

        Je užitečné získat některé základní znalosti šablony před přizpůsobením.
1. Vyberte **Soubor** > **Uložit jako,** chcete-li uložit kopii souboru do místního počítače s názvem *azuredeploy.json*.

## <a name="edit-the-template"></a>Úprava šablony

1. Přidejte další dva parametry na konci části **parametry** pro nastavení klíče účtu úložiště a adresy URL BACPAC.

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

    Přidejte čárku za **adminPassword**. Chcete-li formátovat soubor JSON z kódu sady Visual Studio, vyberte Shift+Alt+F.

    Chcete-li získat tyto dvě hodnoty, naleznete [v tématu Příprava souboru BACPAC](#prepare-a-bacpac-file).

1. Přidejte do šablony dva další prostředky.

    * Chcete-li povolit rozšíření databáze SQL k importu souborů BACPAC, musíte povolit provoz ze služeb Azure. Pod definici serveru SQL přidejte následující definici pravidla brány firewall:

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

        ![Šablona s rozšířením databáze SQL](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Vysvětlení definice prostředku najdete v [referenčních informacích k rozšíření služby SQL Database](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Tady je několik důležitých elementů:

        * **dependsOn:** Prostředek rozšíření se musí vytvořit po vytvoření databáze SQL.
        * **storageKeyType**: Zadejte typ klíče úložiště, který chcete použít. Hodnota může být `StorageAccessKey` nebo `SharedAccessKey`. Použití `StorageAccessKey` v tomto kurzu.
        * **storageKey**: Zadejte klíč pro účet úložiště, kde je uložen soubor BACPAC. Pokud je `SharedAccessKey`typ klíče úložiště , musí předcházet "?".
        * **storageUri**: Zadejte adresu URL souboru BACPAC uloženého v účtu úložiště.
        * **administratorLoginPassword:** Heslo správce SQL. Použijte vygenerované heslo. Viz [Požadavky](#prerequisites).

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

Zvažte použití stejného názvu projektu, který jste použili při přípravě souboru BACPAC tak, aby všechny prostředky jsou uloženy ve stejné skupině prostředků. Tímto způsobem je snazší spravovat úkoly zdrojů, jako je například vyčištění prostředků. Pokud používáte stejný název projektu, můžete `New-AzResourceGroup` příkaz odebrat ze skriptu nebo odpovědět ano (y) nebo ne (n) na dotaz, zda chcete aktualizovat existující skupinu prostředků.

Použijte vygenerované heslo. Viz [Požadavky](#prerequisites).

## <a name="verify-the-deployment"></a>Ověření nasazení

Chcete-li získat přístup k serveru SQL z klientského počítače, je třeba přidat další pravidlo brány firewall. Další informace naleznete v [tématu Vytvoření a správa pravidel brány firewall IP](../../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules).

Na webu Azure Portal vyberte databázi SQL z nově nasazené skupiny prostředků. Vyberte **Editor dotazů (Preview)** a zadejte přihlašovací údaje správce. Zobrazí se dvě tabulky importované do databáze.

![Editor dotazů (náhled)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na webu Azure Portal vyberte **skupinu prostředků** v levé nabídce.
1. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
1. Vyberte název skupiny prostředků. Ve skupině prostředků se zobrazí celkem šest prostředků.
1. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili sql server a databázi SQL a importovali soubor BACPAC. Soubor BACPAC je uložený v účtu Azure Storage. Kdokoli s adresou URL má přístup k souboru. Informace o zabezpečení souboru BACPAC (artefaktu) najdete v následujících tématech:

> [!div class="nextstepaction"]
> [Zabezpečte artefakty](./template-tutorial-secure-artifacts.md)
