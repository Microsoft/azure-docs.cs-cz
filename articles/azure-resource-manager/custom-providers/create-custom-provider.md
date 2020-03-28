---
title: Vytvořit zprostředkovatele prostředků
description: Popisuje, jak vytvořit zprostředkovatele prostředků a nasadit jeho vlastní typy prostředků.
author: MSEvanhi
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 393993a44c860525b9bd9a540ed7afff78e5b93c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649866"
---
# <a name="quickstart-create-custom-provider-and-deploy-custom-resources"></a>Úvodní příručka: Vytvoření vlastního zprostředkovatele a nasazení vlastních prostředků

V tomto rychlém startu vytvoříte vlastního poskytovatele prostředků a nasadíte vlastní typy prostředků pro tohoto poskytovatele prostředků. Další informace o vlastních zprostředkovatelích najdete [v tématu Přehled náhledu vlastních zprostředkovatelů Azure](overview.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky v tomto rychlém startu, je třeba volat operace REST. Existují [různé způsoby odesílání požadavků REST](/rest/api/azure/). Pokud ještě nemáte nástroj pro operace REST, nainstalujte [ARMClient](https://github.com/projectkudu/ARMClient). Je to nástroj příkazového řádku s otevřeným zdrojovým kódem, který zjednodušuje vyvolání rozhraní API Azure Resource Manager.

## <a name="deploy-custom-provider"></a>Nasazení vlastního zprostředkovatele

Pokud chcete nastavit vlastního zprostředkovatele, nasaďte do předplatného Azure [ukázkovou šablonu.](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json)

Po nasazení šablony má vaše předplatné následující prostředky:

* Funkce App s operacemi pro prostředky a akce.
* Účet úložiště pro ukládání uživatelů, které jsou vytvořeny prostřednictvím vlastního zprostředkovatele.
* Vlastní zprostředkovatel, který definuje vlastní typy prostředků a akce. Používá koncový bod aplikace funkce pro odesílání požadavků.
* Vlastní prostředek od vlastního zprostředkovatele.

Pokud chcete nasadit vlastního zprostředkovatele pomocí PowerShellu, použijte:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

Nebo můžete nasadit řešení pomocí následujícího tlačítka:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Zobrazit vlastního zprostředkovatele a prostředku

Na portálu je vlastní zprostředkovatel skrytý typ prostředku. Chcete-li ověřit, zda byl poskytovatel prostředků nasazen, přejděte do skupiny prostředků. Vyberte možnost **Zobrazit skryté typy**.

![Zobrazit skryté typy prostředků](./media/create-custom-provider/show-hidden.png)

Chcete-li zobrazit vlastní typ prostředku, který jste nasadili, použijte operaci GET u typu prostředku.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

S ARMClient použijte:

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

Obdržíte odpověď:

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

Váš vlastní zprostředkovatel má také akci s názvem **ping**. Kód, který zpracovává požadavek je implementován v aplikaci funkce. Akce příkazu ping odpoví pozdravem.

Chcete-li odeslat požadavek ping, použijte operaci POST u vlastního zprostředkovatele.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

S ARMClient použijte:

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

Obdržíte odpověď:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

## <a name="create-resource-type"></a>Vytvořit typ prostředku

Chcete-li vytvořit vlastní typ prostředku, můžete jej nasadit v šabloně. Tento přístup je uveden v šabloně, kterou jste nasadili v tomto rychlém startu. Můžete také odeslat požadavek PUT pro typ prostředku.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

S ARMClient použijte:

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

Obdržíte odpověď:

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

Úvod k vlastním zprostředkovatelům najdete v [tématu Přehled náhledu vlastních zprostředkovatelů Azure](overview.md).
