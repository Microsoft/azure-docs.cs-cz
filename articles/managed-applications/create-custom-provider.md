---
title: Vytvoření poskytovatele prostředků pomocí Azure Vlastní zprostředkovatelé ve verzi Preview
description: Popisuje, jak vytvořit poskytovatele prostředků a nasadit jeho vlastní typy prostředků.
services: managed-applications
author: MSEvanhi
ms.service: managed-applications
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: e4196cf59537be5194ceb510a1b7b066c97de19a
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410227"
---
# <a name="tutorial-create-custom-provider-and-deploy-custom-resources"></a>Kurz: Vytvoření vlastního zprostředkovatele a nasazení vlastních prostředků

V tomto kurzu vytvoříte poskytovatele prostředků a nasazovat vlastní typy prostředků pro daného poskytovatele prostředků. Další informace o vlastních poskytovatelů najdete v tématu [přehled Azure Vlastní zprostředkovatelé Preview](custom-providers-overview.md).

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto kurzu, budete muset volání operací REST. Existují [různé způsoby odesílání požadavků REST](/rest/api/azure/). Pokud ještě nemáte nástroj pro operace REST, nainstalujte [ARMClient](https://github.com/projectkudu/ARMClient). Je nástroj příkazového řádku open source, který zjednodušuje volání rozhraní API Azure Resource Manageru.

## <a name="deploy-custom-provider"></a>Nasazení vlastního zprostředkovatele

Nastavení vlastního zprostředkovatele, nasazení [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) ke svému předplatnému Azure.

Po nasazení šablony, vaše předplatné má následující prostředky:

* Aplikace Function App s operacemi pro prostředkům a akcím.
* Účet úložiště pro ukládání uživatelů, které jsou vytvořené prostřednictvím vlastního zprostředkovatele.
* Vlastní zprostředkovatel, který definuje vlastní typy prostředků a akcí. Koncový bod aplikace funkce používá pro odesílání požadavků.
* Vlastní prostředek z vlastního zprostředkovatele.

K nasazení vlastního zprostředkovatele pomocí Powershellu, použijte:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

Nebo můžete nasadit řešení se na následující tlačítko:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Zobrazení vlastního zprostředkovatele a prostředků

Na portálu je vlastního zprostředkovatele typu skryté prostředku. Potvrďte, že byla nasazena poskytovatele prostředků, přejděte do skupiny prostředků. Vyberte možnost **zobrazit skryté typy**.

![Zobrazit skryté typy prostředků](./media/create-custom-providers/show-hidden.png)

Pokud chcete zobrazit vlastní typ prostředku, který jste nasadili, použijte operaci GET u typu prostředku.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

S ARMClient použijte:

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

Obdržíte odpovědi:

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

## <a name="call-action"></a>Volání akce

Vlastní zprostředkovatel také obsahuje akci s názvem **ping**. Kód, který zpracuje žádost je implementována do aplikace function App. Akci ping odpoví odpovědí s pozdrav.

K odesílání žádosti příkazu ping, použijte operaci POST u vlastního poskytovatele.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

S ARMClient použijte:

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

Obdržíte odpovědi:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

## <a name="create-resource-type"></a>Vytvořit typ prostředku

Pokud chcete vytvořit vlastní prostředek typu, můžete nasadit prostředků v šabloně. Tento přístup je zobrazena v šabloně, kterou jste nasadili v tomto kurzu. Můžete také odeslat požadavek PUT pro typ prostředku.

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

Obdržíte odpovědi:

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

## <a name="next-steps"></a>Další postup

Úvod do vlastních poskytovatelů najdete v tématu [přehled Azure Vlastní zprostředkovatelé Preview](custom-providers-overview.md).
