---
title: Import souborů SQL BACPAC pomocí šablon Azure Resource Manageru | Microsoft Docs
description: Zjistěte, jak s využitím rozšíření služby SQL Database importovat soubory SQL BACPAC pomocí šablon Azure Resource Manageru.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6349f3e6a30ce1b7a3162f54056901313327a101
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612533"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Kurz: Import souborů SQL BACPAC pomocí šablon Azure Resource Manageru

Zjistěte, jak s využitím rozšíření služby Azure SQL Database importovat soubor BACPAC. V tomto kurzu vytvoříte šablonu pro nasazení Azure SQL Serveru, služby SQL Database a souboru BACPAC. Informace o nasazování rozšíření virtuálních počítačů Azure pomocí šablon Azure Resource Manageru najdete v [kurzu nasazování rozšíření virtuálních počítačů pomocí šablon Azure Resource Manageru](./resource-manager-tutorial-deploy-vm-extensions.md).

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

* [Visual Studio Code](https://code.visualstudio.com/) s rozšířením Nástroje Resource Manageru Přečtěte si, [jak toto rozšíření nainstalovat](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Pro zlepšení zabezpečení použijte pro účet správce SQL Serveru vygenerované heslo. Tady ukázka generování hesla:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace najdete v [kurzu integrace služby Azure Key Vault v nasazení šablony Resource Manageru](./resource-manager-tutorial-use-key-vault.md). Zároveň doporučujeme heslo každé tři měsíce aktualizovat.

## <a name="prepare-a-bacpac-file"></a>Příprava souboru BACPAC

Soubor BACPAC je sdílený v [účtu služby Azure Storage s veřejným přístupem](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac). Pokud chcete vytvořit vlastní, přečtěte si téma [Export databáze SQL Azure do souboru BACPAC](../sql-database/sql-database-export.md). Pokud se rozhodnete soubor publikovat do vlastního umístění, v pozdější části kurzu budete muset šablonu aktualizovat.

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

Šablony pro rychlý start Azure slouží jako úložiště šablon Resource Manageru. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona použitá v tomto kurzu má název [Deploy an Azure SQL Server with Threat Detection](https://azure.microsoft.com/resources/templates/201-sql-threat-detection-server-policy-optional-db/) (Nasazení Azure SQL Serveru s detekcí hrozeb).

1. V nástroji Visual Studio Code vyberte **File** (Soubor) >**Open File** (Otevřít soubor).
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-sql-threat-detection-server-policy-optional-db/azuredeploy.json
    ```
3. Výběrem **Open** (Otevřít) soubor otevřete.

    Šablona definuje tři prostředky:

    * `Microsoft.Sql/servers`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
    * `Microsoft.SQL/servers/securityAlertPolicies`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies).
    * `Microsoft.SQL.servers/databases`.  Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
    Než začnete šablonu přizpůsobovat, je vhodné se s ní nejprve trochu seznámit.
4. Vyberte **File** (Soubor) >**Save As** (Uložit jako) a soubor uložte na místní počítač pod názvem **azuredeploy.json**.

## <a name="edit-the-template"></a>Úprava šablony

Do šablony je potřeba přidat další dva prostředky.

* Aby rozšíření služby SQL Database mohlo importovat soubory BACPAC, musíte povolit přístup ke službám Azure. Přidejte k definici serveru SQL následující kód JSON:

    ```json
    {
        "type": "firewallrules",
        "name": "AllowAllAzureIps",
        "location": "[parameters('location')]",
        "apiVersion": "2014-04-01",
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
                "storageUri": "https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac",
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
    * **administratorLoginPassword:** Heslo správce SQL. Doporučuje se použít vygenerované heslo. Viz [Požadavky](#prerequisites).

## <a name="deploy-the-template"></a>Nasazení šablony

Informace o procesu nasazení najdete v části [Nasazení šablony](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template). Místo toho použijte následující skript PowerShellu pro nasazení:

```azurepowershell
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmResourceGroupDeployment -Name $deploymentName `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile azuredeploy.json
```

Doporučuje se použít vygenerované heslo. Viz [Požadavky](#prerequisites).

## <a name="verify-the-deployment"></a>Ověření nasazení

Na portálu vyberte databázi SQL z nově nasazené skupiny prostředků. Vyberte **Editor dotazů (Preview)** a zadejte přihlašovací údaje správce. Měly by se zobrazit dvě tabulky importované do databáze:

![Azure Resource Manager – Soubor BACPAC pro nasazení rozšíření SQL](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nasadili SQL Server a službu SQL Database a importovali jste soubor BACPAC. Informace o nasazování prostředků Azure napříč několika oblastmi a používání postupů bezpečného nasazení najdete tady:

> [!div class="nextstepaction"]
> [Použití Azure Deployment Manageru](./resource-manager-tutorial-deploy-vm-extensions.md)
