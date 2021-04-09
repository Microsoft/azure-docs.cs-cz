---
title: Rychlý Start – vytvoření úlohy Azure Stream Analytics pomocí šablony Azure Resource Manager
description: V tomto rychlém startu se dozvíte, jak pomocí šablony Azure Resource Manager vytvořit úlohu Azure Stream Analytics.
services: stream-analytics
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, subject-armqs
ms.date: 05/28/2020
ms.openlocfilehash: 2e8e7890adcb6b91d79bbf4e119488e365410d3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020073"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-by-using-an-arm-template"></a>Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí šablony ARM

V tomto rychlém startu použijete šablonu Azure Resource Manager (šablona ARM) k vytvoření úlohy Azure Stream Analytics. Po vytvoření úlohy se nasazení ověří.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-streamanalytics-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Máte předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-streamanalytics-create/).

:::code language="json" source="~/quickstart-templates/101-streamanalytics-create/azuredeploy.json":::

Prostředek Azure definovaný v šabloně je [Microsoft. StreamAnalytics/StreamingJobs](/azure/templates/microsoft.streamanalytics/streamingjobs): vytvořte úlohu Azure Stream Analytics.

## <a name="deploy-the-template"></a>Nasazení šablony

V této části vytvoříte úlohu Azure Stream Analytics pomocí šablony ARM.

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří úlohu Azure Stream Analytics.

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-streamanalytics-create%2Fazuredeploy.json)

2. Zadejte požadované hodnoty pro vytvoření úlohy Azure Stream Analytics.

   ![Vytvoření úlohy Azure Stream Analytics pomocí šablony Azure Resource Manager](./media/quick-create-azure-resource-manager/create-stream-analytics-job-resource-manager-template.png "Vytvoření úlohy Azure Stream Analytics pomocí šablony Azure Resource Manager")

   Zadejte následující hodnoty:

   |Vlastnost  |Popis  |
   |---------|---------|
   |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
   |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který uchovává související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../azure-resource-manager/management/overview.md). |
   |**Oblast**     | Vyberte **USA – východ**. Další dostupné oblasti najdete v tématu [Dostupné služby Azure podle oblastí](https://azure.microsoft.com/regions/services/).        |
   |**Název úlohy Stream Analytics**     | Zadejte název úlohy Stream Analytics.      |
   |**Počet jednotek streamování**     |  Vyberte počet jednotek streamování, které potřebujete. Další informace najdete v tématu [pochopení a úprava jednotek streamování](stream-analytics-streaming-unit-consumption.md).       |

3. Vyberte **Zkontrolovat a vytvořit** a potom **Vytvořit**.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Můžete použít Azure Portal ke kontrole úlohy Azure Stream Analytics nebo k vypsání prostředku pomocí následujícího skriptu Azure CLI nebo Azure PowerShell.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
echo "Enter your Azure Stream Analytics job name:" &&
read streamAnalyticsJobName &&
echo "Enter the resource group where the Azure Stream Analytics job exists:" &&
read resourcegroupName &&
az stream-analytics job show -g $resourcegroupName -n $streamAnalyticsJobName
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Stream Analytics job exists"
(Get-AzResource -ResourceType "Microsoft.StreamAnalytics/StreamingJobs" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat k dalším kurzům, možná budete chtít tyto prostředky ponechat na místě. Pokud už je nepotřebujete, odstraňte skupinu prostředků, která odstraní úlohu Azure Stream Analytics. Odstranění skupiny prostředků pomocí rozhraní příkazového řádku Azure nebo Azure PowerShell:

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili úlohu Azure Stream Analytics pomocí šablony ARM a ověřili nasazení. V dalším článku se dozvíte, jak exportovat šablonu ARM pro existující úlohu pomocí VS Code.

> [!div class="nextstepaction"]
> [Exportovat šablonu Azure Stream Analytics úlohy ARM](resource-manager-export.md)
