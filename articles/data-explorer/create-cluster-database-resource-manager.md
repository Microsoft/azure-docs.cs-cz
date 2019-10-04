---
title: Vytvoření clusteru a databáze Azure Průzkumník dat pomocí šablony Azure Resource Manager
description: Naučte se vytvořit cluster a databázi Azure Průzkumník dat pomocí šablony Azure Resource Manager
author: orspod
ms.author: orspodek
ms.reviewer: oflipman
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: b8aa87b871b86a8dab3b1984b638366a42708483
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936899"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Vytvoření clusteru a databáze Azure Průzkumník dat pomocí šablony Azure Resource Manager

> [!div class="op_single_selector"]
> * [Bran](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [Prostředí](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Šablona ARM](create-cluster-database-resource-manager.md)

Azure Průzkumník dat je rychlá a vysoce škálovatelná služba průzkumu dat pro data protokolů a telemetrie. Pokud chcete použít Azure Průzkumník dat, musíte nejdřív vytvořit cluster a v tomto clusteru vytvořit jednu nebo víc databází. Pak data ingestujte do databáze, abyste na ni mohli spouštět dotazy. 

V tomto článku vytvoříte cluster a databázi Azure Průzkumník dat pomocí [šablony Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Tento článek ukazuje, jak definovat, které prostředky jsou nasazeny a jak definovat parametry, které jsou zadány při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji přizpůsobit tak, aby splňovala vaše požadavky. Informace o vytváření šablon najdete v tématu [vytváření šablon Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates). Informace o syntaxi a vlastnostech JSON pro použití v šabloně najdete v tématu [typy prostředků Microsoft. Kusto](/azure/templates/microsoft.kusto/allversions).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>Šablona Azure Resource Manager pro vytváření clusterů a databází

V tomto článku použijete [existující šablonu pro rychlý Start](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json) .

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
              "name": "D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-09-07-preview",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-09-07-preview",
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

Další ukázky šablon najdete v tématu [šablony rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/).

## <a name="deploy-the-template-and-verify-template-deployment"></a>Nasazení šablony a ověření nasazení šablony

Šablonu Azure Resource Manager můžete nasadit [pomocí Azure Portal](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) nebo [pomocí PowerShellu](#use-powershell-to-deploy-the-template-and-verify-template-deployment).

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>Použití Azure Portal k nasazení šablony a ověření nasazení šablony

1. Chcete-li vytvořit cluster a databázi, použijte následující tlačítko ke spuštění nasazení. Klikněte pravým tlačítkem myši a vyberte **otevřít v novém okně**, abyste mohli postupovat podle zbývajících kroků v tomto článku.

    [@no__t – 1Deploy do Azure](media/create-cluster-database-resource-manager/deploybutton.png)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-kusto-cluster-database/azuredeploy.json)

    Tlačítkem **nasadit do Azure** přejdete na Azure Portal a vyplníte formulář nasazení.

    ![Nasazení do Azure](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    Šablonu můžete [v Azure Portal upravit a nasadit](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) pomocí formuláře.

1. Dokončete **základní** oddíly a **Nastavení** . Vyberte jedinečné názvy clusterů a databází.
Vytvoření clusteru a databáze služby Azure Průzkumník dat trvá několik minut.

1. Pokud chcete toto nasazení ověřit, otevřete skupinu prostředků v [Azure Portal](https://portal.azure.com) , abyste našli nový cluster a databázi. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>Nasazení šablony a ověření nasazení šablony pomocí PowerShellu

#### <a name="deploy-the-template-using-powershell"></a>Nasazení šablony pomocí PowerShellu

1. Vyberte **vyzkoušet** z následujícího bloku kódu a pak podle pokynů se přihlaste ke službě Azure Cloud Shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add(“clusters_kustocluster_name”, $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Vyberte **Kopírovat** a zkopírujte skript prostředí PowerShell.
1. Klikněte pravým tlačítkem na konzolu prostředí a pak vyberte **Vložit**.
Vytvoření clusteru a databáze služby Azure Průzkumník dat trvá několik minut.

#### <a name="verify-the-deployment-using-powershell"></a>Ověření nasazení pomocí PowerShellu

Chcete-li ověřit nasazení, použijte následující skript Azure PowerShell.  Pokud je Cloud Shell stále otevřené, nemusíte kopírovat/spouštět první řádek (pro čtení-hostitel). Další informace týkající se správy prostředků Azure Průzkumník dat v prostředí PowerShell najdete v článku [AZ. Kusto](/powershell/module/az.kusto/?view=azps-2.7.0). 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky Azure nepotřebujete, vyčistěte prostředky, které jste nasadili, odstraněním skupiny prostředků. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Vyčištění prostředků pomocí Azure Portal

Odstraňte prostředky v Azure Portal podle kroků v části [vyčištění prostředků](create-cluster-database-portal.md#clean-up-resources).

### <a name="clean-up-resources-using-powershell"></a>Vyčištění prostředků pomocí PowerShellu

Pokud je Cloud Shell stále otevřené, nemusíte kopírovat/spouštět první řádek (pro čtení-hostitel).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Další kroky

[Ingestování dat do clusteru a databáze Azure Průzkumník dat](ingest-data-overview.md)
