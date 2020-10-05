---
title: Rychlý Start – vytvoření a nasazení pracovního postupu aplikace logiky pomocí Azure Resource Manager šablon
description: Postup vytvoření a nasazení aplikace logiky pomocí šablon Azure Resource Manager
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, subject-armqs
ms.date: 06/30/2020
ms.openlocfilehash: 10cc89d1a0cc975df4384e551dddde32be0a4a72
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87078122"
---
# <a name="quickstart-create-and-deploy-a-logic-app-workflow-by-using-an-arm-template"></a>Rychlý Start: vytvoření a nasazení pracovního postupu aplikace logiky pomocí šablony ARM

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) je cloudová služba, která pomáhá vytvářet a spouštět automatizované pracovní postupy, které integrují data, aplikace, cloudové služby a místní systémy, a to výběrem ze [stovek konektorů](/connectors/connector-reference/connector-reference-logicapps-connectors). Tento rychlý Start se zaměřuje na proces nasazení šablony Azure Resource Manager (šablona ARM) pro vytvoření základní aplikace logiky, která zkontroluje stav Azure podle hodinového plánu. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-logic-app-create%2fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

V tomto rychlém startu se používá šablona [**vytvořit aplikaci logiky**](https://azure.microsoft.com/resources/templates/101-logic-app-create/) , kterou najdete v [galerii šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates) , ale je moc dlouhá, takže ji tady můžete zobrazit. Místo toho můžete zkontrolovat ["azuredeploy.jsna soubor](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) šablony pro rychlý Start" v galerii šablon.

Šablona pro rychlý Start vytvoří pracovní postup aplikace logiky, který používá aktivační událost opakování, která je nastavená na spouštění každou hodinu, a [ *integrovanou* akci](../connectors/apis-list.md#connector-types)http, která volá adresu URL, která vrací stav pro Azure. Integrovaná akce je nativní pro Azure Logic Apps platformu.

Tato šablona vytvoří následující prostředek Azure:

* [**Microsoft. Logic/Workflows**](/azure/templates/microsoft.logic/workflows), které vytváří pracovní postup pro aplikaci logiky.

Chcete-li najít další šablony pro rychlý Start pro Azure Logic Apps, přečtěte si šablony [Microsoft. Logic](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Logic) v galerii.

<a name="deploy-template"></a>

## <a name="deploy-the-template"></a>Nasazení šablony

Použijte možnost, kterou chcete použít k nasazení šablony pro rychlý Start:

| Možnost | Popis |
|--------|-------------|
| [Azure Portal](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-portal#deploy-template) | Pokud vaše prostředí Azure splňuje požadavky a jste obeznámeni s používáním šablon ARM, tyto kroky vám pomůžou se přihlašovat přímo do Azure a otevřít šablonu pro rychlý Start v Azure Portal. Další informace najdete v tématu [nasazení prostředků pomocí šablon ARM a Azure Portal](../azure-resource-manager/templates/deploy-portal.md). |
| [Azure CLI](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-cli#deploy-template) | Rozhraní příkazového řádku Azure (Azure CLI) je sada příkazů pro vytváření a správu prostředků Azure. Pokud chcete spustit tyto příkazy, potřebujete Azure CLI verze 2,6 nebo novější. Chcete-li zjistit verzi rozhraní příkazového řádku, zadejte `az --version` . Další informace najdete v těchto tématech: <p><p>- [Co je Azure CLI](/cli/azure/what-is-azure-cli?view=azure-cli-latest) <br>- [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) |
| [Azure PowerShell](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-powershell#deploy-template) | Prostředí Azure PowerShell poskytuje sadu rutin, které ke správě vašich prostředků Azure využívají model Azure Resource Manager. Další informace najdete v těchto tématech: <p><p>- [Přehled Azure PowerShell](/powershell/azure/azurerm/overview) <br>- [Představení Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az) <br>- [Začínáme s Azure PowerShell](/powershell/azure/get-started-azureps) |
| [REST API správy prostředků Azure](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=rest-api#deploy-template) | Azure poskytuje rozhraní REST API pro reprezentaci, což jsou koncové body služby, které podporují operace HTTP (metody), které slouží k vytváření, načítání, aktualizaci nebo odstraňování přístupu k prostředkům služby. Další informace najdete v tématu [Začínáme s Azure REST API](/rest/api/azure/). |
|||

<a name="deploy-azure-portal"></a>

#### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Vyberte následující obrázek pro přihlášení pomocí účtu Azure a otevřete šablonu pro rychlý Start v Azure Portal:

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-logic-app-create%2fazuredeploy.json)

1. Na portálu na stránce **vytvořit aplikaci logiky pomocí šablony** zadejte nebo vyberte tyto hodnoty:

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Předplatné** | <*Azure – předplatné – název*> | Název předplatného Azure, které se má použít |
   | **Skupina prostředků** | <*Azure-Resource-Group-Name*> | Název nové nebo existující skupiny prostředků Azure. Tento příklad používá `Check-Azure-Status-RG` . |
   | **Oblast** | <*Oblast Azure*> | Oblast datacenter Azure pro použití vaší aplikace logiky Tento příklad používá `West US` . |
   | **Název aplikace logiky** | <*Logic-App-Name*> | Název, který se má použít pro vaši aplikaci logiky. Tento příklad používá `Check-Azure-Status-LA` . |
   | **Identifikátor URI testu** | <*identifikátor URI testu*> | Identifikátor URI pro službu, která má být volána podle konkrétního plánu. Tento příklad používá `https://status.azure.com/en-us/status/` , což je stránka stavu Azure. |
   | **Umístění** |  <*Azure-region-All-Resources*> | Oblast Azure, která se má použít pro všechny prostředky, pokud se liší od výchozí hodnoty. V tomto příkladu se používá výchozí hodnota, `[resourceGroup().location]` , která je umístěním skupiny prostředků. |
   ||||

   Tady je vzhled stránky s hodnotami použitými v tomto příkladu:

   ![Zadání informací pro šablonu pro rychlé zprovoznění](./media/quickstart-create-deploy-azure-resource-manager-template/create-logic-app-template-portal.png)

1. Až budete hotovi, vyberte **zkontrolovat + vytvořit**.

1. Pokračujte postupem v části [Kontrola nasazených prostředků](#review-deployed-resources).

#### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name name to use for generating resource names:" projectName &&
read -p "Enter the location, such as 'westus':" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

Další informace najdete v těchto tématech:

* [Azure CLI: AZ Deployment Group](/cli/azure/deployment/group)
* [Nasazení prostředků pomocí šablon ARM a Azure CLI](../azure-resource-manager/templates/deploy-cli.md)

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name to use for generating resource names"
$location = Read-Host -Prompt "Enter the location, such as 'westus'"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

Další informace najdete v těchto tématech:

* [Azure PowerShell: New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)
* [Azure PowerShell: New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)
* [Nasazení prostředků pomocí šablon ARM a Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

#### <a name="rest-api"></a>[REST API](#tab/rest-api)

1. Pokud nechcete použít existující skupinu prostředků Azure, vytvořte novou skupinu prostředků pomocí této syntaxe pro požadavek, který odešlete do REST API správy prostředků:

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2019-10-01
   ```

   | Hodnota | Popis |
   |-------|-------------|
   | `subscriptionId`| Identifikátor GUID předplatného Azure, který chcete použít |
   | `resourceGroupName` | Název skupiny prostředků Azure, která se má vytvořit. Tento příklad používá `Check-Azure-Status-RG` . |
   |||

   Příklad:

   ```http
   PUT https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourcegroups/Check-Azure-Status-RG?api-version=2019-10-01
   ```

   Další informace najdete v těchto tématech:

   * [Referenční informace k Azure REST API – jak volat rozhraní Azure REST API](/rest/api/azure/)
   * [Správa prostředků REST API: skupiny prostředků – vytvořit nebo aktualizovat](/rest/api/resources/resourcegroups/createorupdate).

1. K nasazení šablony pro rychlé zprovoznění do skupiny prostředků použijte tuto syntaxi pro požadavek, který odešlete do správy prostředků REST API:

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
   ```

   | Hodnota | Popis |
   |-------|-------------|
   | `subscriptionId`| Identifikátor GUID předplatného Azure, který chcete použít |
   | `resourceGroupName` | Název skupiny prostředků Azure, která se má použít. Tento příklad používá `Check-Azure-Status-RG` . |
   | `deploymentName` | Název, který se má použít pro vaše nasazení. Tento příklad používá `Check-Azure-Status-LA` . |
   |||

   Příklad:

   ```http
   PUT https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourcegroups/Check-Azure-Status-RG/providers/Microsoft.Resources/deployments/Check-Azure-Status-LA?api-version=2019-10-01
   ```

   Další informace najdete v tématu [Správa prostředků REST API: nasazení – vytvořit nebo aktualizovat](/rest/api/resources/deployments/createorupdate).

1. Pokud chcete poskytnout hodnoty, které se mají použít pro nasazení, jako je například oblast Azure a odkazy na šablonu a [soubor parametrů](../azure-resource-manager/templates/template-parameters.md)pro rychlý Start, který obsahuje hodnoty pro šablonu rychlého startu, která se má použít při nasazení, použijte tuto syntaxi pro text žádosti, který odešlete do REST API správy prostředků:

   ```json
   {
      "location": "{Azure-region}",
      "properties": {
         "templateLink": {
            "uri": "{quickstart-template-URL}",
            "contentVersion": "1.0.0.0"
         },
         "parametersLink": {
            "uri": "{quickstart-template-parameter-file-URL}",
            "contentVersion": "1.0.0.0"
         },
         "mode": "Incremental"
      }
   }
   ```

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | `location`| <*Oblast Azure*> | Oblast Azure, která se má použít pro nasazení Tento příklad používá `West US` . |
   | `templateLink` : `uri` | <*rychlý Start – šablona – adresa URL*> | Umístění adresy URL šablony pro rychlý Start, která se má použít pro nasazení: <p><p>`https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json`. |
   | `parametersLink` : `uri` | <*rychlý Start – šablona-parametr-soubor-URL*> | Umístění adresy URL souboru parametrů šablony pro rychlý Start, který se má použít pro nasazení: <p><p>`https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.parameters.json` <p><p>Další informace o souboru parametrů Správce prostředků najdete v těchto tématech: <p><p>- [Vytvořit soubor Správce prostředků parametrů](../azure-resource-manager/templates/parameter-files.md) <br>- [Kurz: použití souborů parametrů k nasazení šablony ARM](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md) |
   | `mode` | <*režim nasazení*> | Spusťte buď přírůstkovou aktualizaci, nebo úplnou aktualizaci. V tomto příkladu `Incremental` je použita výchozí hodnota. Další informace najdete v tématu [režimy nasazení Azure Resource Manager](../azure-resource-manager/templates/deployment-modes.md). |
   |||

   Příklad:

   ```json
   {
      "location": "West US",
      "properties": {
         "templateLink": {
            "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json",
            "contentVersion": "1.0.0.0"
         },
         "parametersLink": {
            "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.parameters.json",
            "contentVersion": "1.0.0.0"
         },
         "mode": "Incremental"
      }
   }
   ```

Další informace najdete v těchto tématech:

* [REST API správy prostředků](/rest/api/resources/)
* [Nasazení prostředků pomocí šablon ARM a Správce prostředků REST API](../azure-resource-manager/templates/deploy-rest.md)

---

<a name="review-deployed-resources"></a>

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

K zobrazení aplikace logiky můžete použít Azure Portal, spustit skript, který vytvoříte pomocí Azure CLI nebo Azure PowerShell, nebo použít aplikaci logiky REST API.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Do vyhledávacího pole Azure Portal zadejte název vaší aplikace logiky, který je `Check-Azure-Status-LA` v tomto příkladu. V seznamu výsledků vyberte svou aplikaci logiky.

1. V Azure Portal vyhledejte a vyberte svou aplikaci logiky, která je `Check-Azure-Status-RG` v tomto příkladu.

1. Po otevření návrháře aplikace logiky si prohlédněte aplikaci logiky vytvořenou šablonou pro rychlý Start.

1. Aplikaci logiky otestujete tak, že na panelu nástrojů návrháře vyberete **Spustit**.

### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

```azurecli-interactive
echo "Enter your logic app name:" &&
read logicAppName &&
az logic workflow show --name $logicAppName &&
echo "Press [ENTER] to continue ..."
```

Další informace najdete v tématu [Azure CLI: AZ Logic Workflow show](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-show).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$logicAppName = Read-Host -Prompt "Enter your logic app name"
Get-AzLogicApp -Name $logicAppName
Write-Host "Press [ENTER] to continue..."
```

Další informace najdete v tématu [Azure PowerShell: Get-AzLogicApp](/powershell/module/az.logicapp/get-azlogicapp).

### <a name="rest-api"></a>[REST API](#tab/rest-api)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}?api-version=2016-06-01
```

| Hodnota | Popis |
|-------|-------------|
| `subscriptionId`| Identifikátor GUID předplatného Azure, kde jste nasadili šablonu pro rychlý Start. |
| `resourceGroupName` | Název skupiny prostředků Azure, do které jste nasadili šablonu pro rychlý Start. Tento příklad používá `Check-Azure-Status-RG` . |
| `workflowName` | Název aplikace logiky, kterou jste nasadili. Tento příklad používá `Check-Azure-Status-LA` . |
|||

Příklad:

```http
GET https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourceGroups/Check-Azure-Status-RG/providers/Microsoft.Logic/workflows/Check-Azure-Status-LA?api-version=2016-06-01
```

Další informace najdete v tématu [Logic Apps REST API: pracovní postupy – získat](/rest/api/logic/workflows/get).

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat. Když už nepotřebujete aplikaci logiky, odstraňte skupinu prostředků pomocí Azure Portal, rozhraní příkazového řádku Azure CLI, Azure PowerShell nebo REST API správy prostředků.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V Azure Portal vyhledejte a vyberte skupinu prostředků, kterou chcete odstranit, což je `Check-Azure-Status-RG` v tomto příkladu.

1. V nabídce skupina prostředků vyberte **Přehled** , pokud ještě není vybraný. Na stránce Přehled vyberte **Odstranit skupinu prostředků**.

1. Pro potvrzení zadejte název skupiny prostředků.

Další informace najdete v tématu věnovaném [odstranění skupiny prostředků](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

```azurecli-interactive
echo "Enter your resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

Další informace najdete v tématu [Azure CLI: AZ Group Delete](/cli/azure/group?view=azure-cli-latest#az-group-delete).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

Další informace najdete v tématu [Azure PowerShell: Remove-AzResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

### <a name="rest-api"></a>[REST API](#tab/rest-api)

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2019-10-01
```

| Hodnota | Popis |
|-------|-------------|
| `subscriptionId`| Identifikátor GUID předplatného Azure, kde jste nasadili šablonu pro rychlý Start. |
| `resourceGroupName` | Název skupiny prostředků Azure, do které jste nasadili šablonu pro rychlý Start. Tento příklad používá `Check-Azure-Status-RG` . |
|||

Příklad:

```http
GET https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourceGroups/Check-Azure-Status-RG?api-version=2019-10-01
```

Další informace najdete v tématu [Správa prostředků REST API: skupiny prostředků-odstranit](/rest/api/resources/resourcegroups/delete).

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: vytvoření a nasazení první šablony ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
