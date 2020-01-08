---
title: Vytvořit poskytovatele prostředků
description: Popisuje, jak vytvořit poskytovatele prostředků a nasadit jeho vlastní typy prostředků.
author: MSEvanhi
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 393993a44c860525b9bd9a540ed7afff78e5b93c
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75649866"
---
# <a name="quickstart-create-custom-provider-and-deploy-custom-resources"></a>Rychlý Start: Vytvoření vlastního zprostředkovatele a nasazení vlastních prostředků

V tomto rychlém startu vytvoříte vlastního poskytovatele prostředků a nasadíte vlastní typy prostředků pro tohoto poskytovatele prostředků. Další informace o vlastních poskytovatelích najdete v tématu [Přehled vlastních zprostředkovatelů Azure ve verzi Preview](overview.md).

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto rychlém startu je potřeba volat operace REST. Existují [různé způsoby, jak odesílat žádosti REST](/rest/api/azure/). Pokud ještě nemáte Nástroj pro operace REST, nainstalujte [ARMClient](https://github.com/projectkudu/ARMClient). Je to open source nástroj příkazového řádku, který zjednodušuje vyvolání rozhraní Azure Resource Manager API.

## <a name="deploy-custom-provider"></a>Nasazení vlastního zprostředkovatele

Pokud chcete nastavit vlastního zprostředkovatele, nasaďte do svého předplatného Azure [ukázkovou šablonu](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) .

Po nasazení šablony má vaše předplatné následující prostředky:

* Function App s operacemi pro prostředky a akce.
* Účet úložiště pro ukládání uživatelů vytvořených prostřednictvím vlastního zprostředkovatele.
* Vlastní zprostředkovatel, který definuje vlastní typy prostředků a akce. Pro odesílání požadavků používá koncový bod aplikace Function App.
* Vlastní prostředek z vlastního zprostředkovatele.

Pokud chcete vlastního zprostředkovatele nasadit pomocí PowerShellu, použijte:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

Nebo můžete řešení nasadit pomocí následujícího tlačítka:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Zobrazit vlastního zprostředkovatele a prostředku

V portálu je vlastním poskytovatelem skrytý typ prostředku. Pokud chcete potvrdit, že poskytovatel prostředků je nasazený, přejděte do skupiny prostředků. Vyberte možnost pro **zobrazení skrytých typů**.

![Zobrazit skryté typy prostředků](./media/create-custom-provider/show-hidden.png)

Pokud chcete zobrazit vlastní typ prostředku, který jste nasadili, použijte operaci získat u typu prostředku.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

Pomocí ARMClient použijte:

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

## <a name="call-action"></a>Akce volání

Vlastní zprostředkovatel má také akci s názvem " **testovat**". Kód, který zpracovává požadavek, je implementován ve Function App. Akce s odpovědí k odeslání odpoví na pozdrav.

Pokud chcete odeslat žádost o zadání požadavku na příkaz, použijte operaci POST u vlastního poskytovatele.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

Pomocí ARMClient použijte:

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

## <a name="create-resource-type"></a>Vytvořit typ prostředku

Chcete-li vytvořit vlastní typ prostředku, můžete prostředek nasadit do šablony. Tento přístup se zobrazí v šabloně, kterou jste nasadili v rámci tohoto rychlého startu. Můžete také odeslat požadavek PUT pro typ prostředku.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

Pomocí ARMClient použijte:

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

## <a name="next-steps"></a>Další kroky

Úvod k vlastním poskytovatelům najdete v tématu [Přehled vlastních zprostředkovatelů Azure ve verzi Preview](overview.md).
