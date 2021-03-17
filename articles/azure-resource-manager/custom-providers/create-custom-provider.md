---
title: Vytvořit poskytovatele prostředků
description: Popisuje, jak vytvořit poskytovatele prostředků a nasadit jeho vlastní typy prostředků.
author: MSEvanhi
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: evanhi
ms.openlocfilehash: 23ae69f49d37a210dd9fa1bfeedeca6c1b461e20
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888857"
---
# <a name="quickstart-create-a-custom-provider-and-deploy-custom-resources"></a>Rychlý Start: Vytvoření vlastního zprostředkovatele a nasazení vlastních prostředků

V tomto rychlém startu vytvoříte vlastního poskytovatele prostředků a nasadíte vlastní typy prostředků pro tohoto poskytovatele prostředků. Další informace o vlastních poskytovatelích najdete v tématu [Přehled vlastních zprostředkovatelů Azure ve verzi Preview](overview.md).

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- K dokončení kroků v tomto rychlém startu je potřeba volat `REST` operace. Existují [různé způsoby, jak odesílat žádosti REST](/rest/api/azure/).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Připravte prostředí pro rozhraní příkazového řádku Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Příklady použití Azure CLI `az rest` pro `REST` požadavky. Další informace najdete v tématu [AZ REST](/cli/azure/reference-index#az-rest).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Příkazy PowerShellu se spouští místně pomocí PowerShellu 7 nebo novějšího a Azure PowerShellch modulů. Další informace najdete v tématu [instalace Azure PowerShell](/powershell/azure/install-az-ps).
- Pokud ještě nemáte Nástroj pro `REST` operace, nainstalujte [ARMClient](https://github.com/projectkudu/ARMClient). Je to open source nástroj příkazového řádku, který zjednodušuje vyvolání rozhraní Azure Resource Manager API.
- Po instalaci **ARMClient** můžete zobrazit informace o použití z příkazového řádku prostředí PowerShell zadáním příkazu: `armclient.exe` . Nebo si přečtěte na [wikiwebu ARMClient](https://github.com/projectkudu/ARMClient/wiki).

---

## <a name="deploy-custom-provider"></a>Nasazení vlastního zprostředkovatele

Pokud chcete nastavit vlastního zprostředkovatele, nasaďte do svého předplatného Azure [ukázkovou šablonu](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) .

Po nasazení šablony má vaše předplatné následující prostředky:

- Function App s operacemi pro prostředky a akce.
- Účet úložiště pro ukládání uživatelů vytvořených prostřednictvím vlastního zprostředkovatele.
- Vlastní zprostředkovatel, který definuje vlastní typy prostředků a akce. Pro odesílání požadavků používá koncový bod aplikace Function App.
- Vlastní prostředek z vlastního zprostředkovatele.

Pokud chcete nasadit vlastního zprostředkovatele, použijte Azure CLI, PowerShell nebo Azure Portal:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Tento příklad vás vyzve k zadání názvu skupiny prostředků, umístění a názvu aplikace Function App poskytovatele. Názvy jsou uloženy v proměnných, které se používají v jiných příkazech. K nasazení prostředků nasaďte příkazy [AZ Group Create](/cli/azure/group#az-group-create) a [AZ Deployment Group Create](/cli/azure/deployment/group#az-deployment-group-create) .

```azurecli-interactive
read -p "Enter a resource group name:" rgName &&
read -p "Enter the location (i.e. eastus):" location &&
read -p "Enter the provider's function app name:" funcName &&
templateUri="https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json" &&
az group create --name $rgName --location "$location" &&
az deployment group create --resource-group $rgName --template-uri $templateUri --parameters funcName=$funcName &&
echo "Press [ENTER] to continue ..." &&
read
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Tento příklad vás vyzve k zadání názvu skupiny prostředků, umístění a názvu aplikace Function App poskytovatele. Názvy jsou uloženy v proměnných, které se používají v jiných příkazech. Příkazy [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) nasazují prostředky.

```powershell
$rgName = Read-Host -Prompt "Enter a resource group name"
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$funcName = Read-Host -Prompt "Enter the provider's function app name"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json"
New-AzResourceGroup -Name $rgName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $rgName -TemplateUri $templateUri -funcName $funcName
Read-Host -Prompt "Press [ENTER] to continue ..."
```

---

Řešení můžete nasadit také z Azure Portal. Kliknutím na tlačítko **nasadit do Azure** otevřete šablonu v Azure Portal.

[![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json)

## <a name="view-custom-provider-and-resource"></a>Zobrazit vlastního zprostředkovatele a prostředku

V portálu je vlastním poskytovatelem skrytý typ prostředku. Pokud chcete potvrdit, že se poskytovatel prostředků nasadil, přejděte do skupiny prostředků. Vyberte možnost pro **zobrazení skrytých typů**.

![Zobrazit skryté typy prostředků](./media/create-custom-provider/show-hidden.png)

Pokud chcete zobrazit vlastní typ prostředku, který jste nasadili, použijte `GET` operaci u svého typu prostředku.

```http
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
subID=$(az account show --query id --output tsv)
requestURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"
az rest --method get --uri $requestURI
```

Dostanete odpověď:

```json
{
  "value": [
    {
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole",
        "provisioningState": "Succeeded"
      },
      "resourceGroup": "<rg-name>",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

Dostanete odpověď:

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

---

## <a name="call-action"></a>Akce volání

Vlastní zprostředkovatel má také akci s názvem `ping` . Kód, který zpracovává požadavek, je implementován ve Function App. `ping`Akce reaguje na pozdrav.

Pokud chcete odeslat `ping` žádost, použijte `POST` operaci na vlastním poskytovateli.

```http
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
pingURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"
az rest --method post --uri $pingURI
```

Dostanete odpověď:

```json
{
  "message": "hello <function-name>.azurewebsites.net",
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  }
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

Dostanete odpověď:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

---

## <a name="create-a-resource-type"></a>Vytvořit typ prostředku

Chcete-li vytvořit vlastní typ prostředku, můžete prostředek nasadit do šablony. Tento přístup se zobrazí v šabloně, kterou jste nasadili v rámci tohoto rychlého startu. Můžete také odeslat žádost o `PUT` typ prostředku.

```http
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
addURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
az rest --method put --uri $addURI --body "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"
```

Dostanete odpověď:

```json
{
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "properties": {
    "FullName": "Test User",
    "Location": "Earth",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "<rg-name>",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

Dostanete odpověď:

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

---

## <a name="custom-resource-provider-commands"></a>Příkazy vlastního poskytovatele prostředků

K práci s vaším vlastním poskytovatelem prostředků použijte příkazy [Custom-Providers](/cli/azure/ext/custom-providers/custom-providers/resource-provider) .

### <a name="list-custom-resource-providers"></a>Vypsat vlastní poskytovatele prostředků

Pomocí `list` příkazu zobrazte všechny vlastní poskytovatele prostředků v rámci předplatného. Ve výchozím nastavení jsou vypsáni vlastní poskytovatelé prostředků aktuálního předplatného nebo můžete zadat `--subscription` parametr. Chcete-li zobrazit seznam pro skupinu prostředků, použijte `--resource-group` parametr.

```azurecli-interactive
az custom-providers resource-provider list --subscription $subID
```

```json
[
  {
    "actions": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "ping",
        "routingType": "Proxy"
      }
    ],
    "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceproviders/<provider-name>",
    "location": "eastus",
    "name": "<provider-name>",
    "provisioningState": "Succeeded",
    "resourceGroup": "<rg-name>",
    "resourceTypes": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "users",
        "routingType": "Proxy, Cache"
      }
    ],
    "tags": {},
    "type": "Microsoft.CustomProviders/resourceproviders",
    "validations": null
  }
]
```

### <a name="show-the-properties"></a>Zobrazit vlastnosti

Pomocí `show` příkazu můžete zobrazit vlastnosti vlastního poskytovatele prostředků. Výstupní formát se podobá `list` výstupu.

```azurecli-interactive
az custom-providers resource-provider show --resource-group $rgName --name $funcName
```

### <a name="create-a-new-resource"></a>Vytvoření nového prostředku

Pomocí `create` příkazu vytvořte nebo aktualizujte vlastního poskytovatele prostředků. Tento příklad aktualizuje `actions` a `resourceTypes` .

```azurecli-interactive
az custom-providers resource-provider create --resource-group $rgName --name $funcName \
--action name=ping endpoint=https://myTestSite.azurewebsites.net/api/{requestPath} routing_type=Proxy \
--resource-type name=users endpoint=https://myTestSite.azurewebsites.net/api{requestPath} routing_type="Proxy, Cache"
```

```json
"actions": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api/{requestPath}",
    "name": "ping",
    "routingType": "Proxy"
  }
],

"resourceTypes": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api{requestPath}",
    "name": "users",
    "routingType": "Proxy, Cache"
  }
],
```

### <a name="update-the-providers-tags"></a>Aktualizace značek poskytovatele

`update`Příkaz aktualizuje pouze značky pro vlastního poskytovatele prostředků. V Azure Portal zobrazuje služba App Service poskytovatele vlastního prostředku značku.

```azurecli-interactive
az custom-providers resource-provider update --resource-group $rgName --name $funcName --tags new=tag
```

```json
"tags": {
  "new": "tag"
},
```

### <a name="delete-a-custom-resource-provider"></a>Odstranění vlastního poskytovatele prostředků

`delete`Příkaz vás vyzve a odstraní pouze vlastního poskytovatele prostředků. Účet úložiště, App Service a plán služby App Service se neodstraní. Po odstranění poskytovatele se vrátíte do příkazového řádku.

```azurecli-interactive
az custom-providers resource-provider delete --resource-group $rgName --name $funcName
```

## <a name="next-steps"></a>Další kroky

Úvod k vlastním poskytovatelům najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Přehled vlastních zprostředkovatelů Azure Custom Providers Preview](overview.md)
