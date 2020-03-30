---
title: Vytvoření clusteru a databáze Azure Data Explorer pomocí šablony Azure Resource Manageru
description: Zjistěte, jak vytvořit cluster a databázi Azure Data Explorer pomocí šablony Azure Resource Manageru
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 56639d8a29ad8eac465845c8d354d04b31ba6093
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911969"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Vytvoření clusteru a databáze Azure Data Explorer pomocí šablony Azure Resource Manageru

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [Cli](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Šablona Azure Resource Manageru](create-cluster-database-resource-manager.md)

Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Pokud chcete použít Azure Data Explorer, musíte nejdříve vytvořit cluster a v něm vytvořit jednu nebo více databází. Do databáze potom ingestujete (načtete) data, abyste se na ně mohli dotazovat spouštěním dotazů. 

V tomto článku vytvoříte cluster a databázi Azure Data Explorer pomocí [šablony Azure Resource Manager](../azure-resource-manager/management/overview.md). Článek ukazuje, jak definovat, které prostředky jsou nasazeny a jak definovat parametry, které jsou určeny při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky. Informace o vytváření šablon najdete v [tématu vytváření šablon Azure Resource Manager .](/azure/azure-resource-manager/resource-group-authoring-templates) Syntaxe json a vlastnosti pro použití v šabloně naleznete v tématu [Microsoft.Kusto typy prostředků](/azure/templates/microsoft.kusto/allversions).

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>Šablona Azure Resource Manager pro vytváření clusterů a databází

V tomto článku použijete [existující šablonu rychlého startu](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "clusters_kustocluster_name": {
          "type": "string",
          "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
          "metadata": {
            "description": "Name of the cluster to create"
          }
      },
      "databases_kustodb_name": {
          "type": "string",
          "defaultValue": "kustodb",
          "metadata": {
            "description": "Name of the database to create"
          }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for all resources."
        }
      }
  },
  "variables": {},
  "resources": [
      {
          "name": "[parameters('clusters_kustocluster_name')]",
          "type": "Microsoft.Kusto/clusters",
          "sku": {
              "name": "Standard_D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[resourceId('Microsoft.Kusto/clusters', parameters('clusters_kustocluster_name'))]"
          ],
          "properties": {
              "softDeletePeriodInDays": 365,
              "hotCachePeriodInDays": 31
          }
      }
  ]
}
```

Další ukázky šablon najdete v [tématu Šablony rychlého spuštění Azure](https://azure.microsoft.com/resources/templates/).

## <a name="deploy-the-template-and-verify-template-deployment"></a>Nasazení šablony a ověření nasazení šablony

Šablonu Azure Resource Manageru můžete nasadit [pomocí portálu Azure nebo](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) [powershellu](#use-powershell-to-deploy-the-template-and-verify-template-deployment).

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>Nasazení šablony a ověření nasazení šablony pomocí portálu Azure Portal

1. Chcete-li vytvořit cluster a databázi, spusťte nasazení pomocí následujícího tlačítka. Klikněte pravým tlačítkem myši a **vyberte Otevřít v novém okně**, abyste mohli postupovat podle zbývajících kroků v tomto článku.

    [![Nasazení do Azure](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    Výběrem tlačítka **Deploy to Azure** (Nasadit do Azure) přejdete na web Azure Portal, kde vyplníte formulář nasazení.

    ![Nasazení do Azure](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    Šablonu můžete [upravit a nasadit na webu Azure Portal](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) pomocí formuláře.

1. Kompletní **základní a** **nastavení** sekce. Vyberte jedinečné názvy clusterů a databází.
Vytvoření clusteru a databáze Azure Data Explorer trvá několik minut.

1. Chcete-li ověřit nasazení, otevřete skupinu prostředků na [webu Azure Portal](https://portal.azure.com) a najděte nový cluster a databázi. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>Nasazení šablony a ověření nasazení šablony pomocí powershellu

#### <a name="deploy-the-template-using-powershell"></a>Nasazení šablony pomocí powershellu

1. Vyberte **Vyzkoušet** z následujícího bloku kódu a postupujte podle pokynů pro přihlášení do prostředí Azure Cloud.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add("clusters_kustocluster_name", $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Vyberte **Kopírovat,** chcete-li zkopírovat skript PowerShellu.
1. Klepněte pravým tlačítkem myši na konzolu prostředí a vyberte příkaz **Vložit**.
Vytvoření clusteru a databáze Azure Data Explorer trvá několik minut.

#### <a name="verify-the-deployment-using-powershell"></a>Ověření nasazení pomocí PowerShellu

Chcete-li ověřit nasazení, použijte následující skript Azure PowerShell.  Pokud cloudové prostředí je stále otevřené, nemusíte kopírovat/spouštět první řádek (Read-Host). Další informace o správě prostředků Azure Data Exploreru v PowerShellu našlápne na článek [Az.Kusto](/powershell/module/az.kusto/?view=azps-2.7.0). 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

[!INCLUDE [data-explorer-clean-resources](../../includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

[Ingestování dat do clusteru a databáze Azure Data Exploreru](ingest-data-overview.md)
