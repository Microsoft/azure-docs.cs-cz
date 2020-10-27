---
title: 'Rychlý Start: Vytvoření šablony Azure DB pro PostgresSQL Flexible Server – ARM'
description: V tomto rychlém startu se dozvíte, jak vytvořit server Azure Database for PostgresSQL flexibilního serveru pomocí šablony ARM.
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sumuth
ms.date: 10/23/2020
ms.openlocfilehash: 3eccb3fb4f4c65896f3956e265509258525c1ac9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92535752"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-postgresql---flexible-server"></a>Rychlý Start: použití šablony ARM k vytvoření Azure Database for PostgreSQLho flexibilního serveru

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Flexibilní Server je spravovaná služba, pomocí které můžete spouštět, spravovat a škálovat vysoce dostupné databáze PostgreSQL v cloudu. Šablonu Azure Resource Manager (šablonu ARM) můžete použít k zřízení PostgreSQL flexibilního serveru pro nasazení více serverů nebo více databází na serveru.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Azure Resource Manager je služba nasazování a správy pro Azure. Poskytuje úroveň správy, která vám umožňuje vytvářet, aktualizovat a odstraňovat prostředky v účtu Azure. Pomocí funkcí správy, jako jsou řízení přístupu, zámky a značky, můžete zabezpečit a organizovat prostředky po nasazení. Další informace o šablonách Azure Resource Manager najdete v tématu [template Deployment Overview](../../azure-resource-manager/templates/overview.md).

## <a name="prerequisites"></a>Požadavky

Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Kontrola šablony

Server Azure Database for PostgresSQL je nadřazeným prostředkem pro jednu nebo více databází v rámci oblasti. Poskytuje obor pro zásady správy, které se vztahují na jeho databáze: přihlašovací údaje, brána firewall, uživatelé, role a konfigurace.

Vytvořte _postgres-flexible-server-template.jsna_ soubor a zkopírujte do něj následující skript JSON.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "administratorLogin": {
      "type": "String"
    },
    "administratorLoginPassword": {
      "type": "SecureString"
    },
    "location": {
      "type": "String"
    },
    "serverName": {
      "type": "String"
    },
    "serverEdition": {
      "type": "String"
    },
    "vCores": {
      "type": "Int"
    },
    "storageSizeMB": {
      "type": "Int"
    },
    "standbyCount": {
      "type": "Int"
    },
    "availabilityZone": {
      "type": "String"
    },
    "version": {
      "type": "String"
    },
    "tags": {
      "defaultValue": {},
      "type": "Object"
    },
    "firewallRules": {
      "defaultValue": {},
      "type": "Object"
    },
    "vnetData": {
      "defaultValue": {},
      "type": "Object"
    },
    "backupRetentionDays": {
      "type": "Int"
    }
  },
  "variables": {
    "api": "2020-02-14-privatepreview",
    "firewallRules": "[parameters('firewallRules').rules]",
    "publicNetworkAccess": "[if(empty(parameters('vnetData')), 'Enabled', 'Disabled')]",
    "vnetDataSet": "[if(empty(parameters('vnetData')), json('{ \"vnetId\": \"\", \"vnetName\": \"\", \"vnetResourceGroup\": \"\", \"subnetName\": \"\" }'), parameters('vnetData'))]",
    "finalVnetData": "[json(concat('{ \"DelegatedVnetID\": \"', variables('vnetDataSet').vnetId, '\", \"DelegatedVnetName\": \"', variables('vnetDataSet').vnetName, '\", \"DelegatedVnetResourceGroup\": \"', variables('vnetDataSet').vnetResourceGroup, '\", \"DelegatedSubnetName\": \"', variables('vnetDataSet').subnetName, '\"}'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/flexibleServers",
      "apiVersion": "[variables('api')]",
      "name": "[parameters('serverName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "GP_D4s_v3",
        "tier": "[parameters('serverEdition')]",
        "capacity": "[parameters('vCores')]"
      },
      "tags": "[parameters('tags')]",
      "properties": {
        "version": "[parameters('version')]",
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "publicNetworkAccess": "[variables('publicNetworkAccess')]",
        "VnetInjArgs": "[if(empty(parameters('vnetData')), json('null'), variables('finalVnetData'))]",
        "standbyCount": "[parameters('standbyCount')]",
        "storageProfile": {
          "storageMB": "[parameters('storageSizeMB')]",
          "backupRetentionDays": "[parameters('backupRetentionDays')]"
        },
        "availabilityZone": "[parameters('availabilityZone')]"
      }
    },
    {
      "condition": "[greater(length(variables('firewallRules')), 0)]",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-08-01",
      "name": "[concat('firewallRules-', copyIndex())]",
      "copy": {
        "name": "firewallRulesIterator",
        "count": "[if(greater(length(variables('firewallRules')), 0), length(variables('firewallRules')), 1)]",
        "mode": "Serial"
      },
      "dependsOn": [
        "[concat('Microsoft.DBforPostgreSQL/flexibleServers/', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.DBforPostgreSQL/flexibleServers/firewallRules",
              "name": "[concat(parameters('serverName'),'/',variables('firewallRules')[copyIndex()].name)]",
              "apiVersion": "[variables('api')]",
              "properties": {
                "StartIpAddress": "[variables('firewallRules')[copyIndex()].startIPAddress]",
                "EndIpAddress": "[variables('firewallRules')[copyIndex()].endIPAddress]"
              }
            }
          ]
        }
      }
    }
  ]
}
```

Tyto prostředky jsou definované v šabloně:

- Microsoft. DBforPostgreSQL/flexibleServers

## <a name="deploy-the-template"></a>Nasazení šablony

Vyberte příkaz **vyzkoušet** z následujícího bloku kódu prostředí PowerShell a otevřete Azure Cloud Shell.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for PostgreSQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for PostgreSQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateFile "D:\Azure\Templates\EngineeringSite.json
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Postupujte podle těchto kroků a ověřte, zda byl server vytvořen v Azure.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **Azure Database for PostgreSQL flexibilní servery (Preview)** .
1. V seznamu databáze vyberte nový server, abyste zobrazili stránku **Přehled** pro správu serveru.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Pokud chcete zobrazit podrobnosti o Azure Database for PostgreSQL flexibilním serveru, budete muset zadat název nového serveru.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for PostgreSQL server"
Get-AzResource -ResourceType "Microsoft.DBforPostgreSQL/flexibleServers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

Pokud chcete zobrazit podrobnosti o Azure Database for PostgreSQL flexibilním serveru, budete muset zadat název a skupinu prostředků nového serveru.

```azurecli-interactive
echo "Enter your Azure Database for PostgreSQL Flexible Server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for PostgreSQL Flexible Server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DBforPostgreSQL/flexibleServers"
```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete přejít k [dalším krokům](#next-steps), zachovejte tuto skupinu prostředků, server a samostatnou databázi. Následující kroky ukazují, jak se připojit k databázi a dotazovat se na ni pomocí různých metod.

Odstranění skupiny prostředků:

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Na [portálu](https://portal.azure.com)vyberte skupinu prostředků, kterou chcete odstranit.

1. Vyberte **Odstranit skupinu prostředků** .
1. Odstranění potvrďte tak, že zadáte název skupiny prostředků.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```
----

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Migrace databáze pomocí výpisu a obnovení](../howto-migrate-using-dump-and-restore.md)
