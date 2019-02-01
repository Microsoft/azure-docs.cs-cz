---
title: Poskytovatelé a typy prostředků Azure prostředků | Dokumentace Microsoftu
description: Popisuje poskytovatele prostředků, které podporují Resource Manageru, jejich schémata a k dispozici verze rozhraní API a oblastí, které můžete hostovat prostředky.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2018
ms.author: tomfitz
ms.openlocfilehash: 109c740ee92e82b6d18879da6839ce6341353cba
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495509"
---
# <a name="resource-providers-and-types"></a>Poskytovatelé a typy prostředků

Při nasazování prostředků, můžete často potřebují k načtení informací o prostředku poskytovatelé a typy. V tomto článku se naučíte:

* Zobrazit všechny poskytovatele prostředků v Azure
* Zkontrolovat stav registrace poskytovatele prostředků
* Registrace poskytovatele prostředků
* Zobrazit typy prostředků pro poskytovatele prostředků.
* Zobrazit platné umístění pro typ prostředku
* Zobrazit platná verze rozhraní API pro typ prostředku

Tento postup na portálu, Powershellu nebo rozhraní příkazového řádku Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="powershell"></a>PowerShell

Pokud chcete zobrazit všechny poskytovatele prostředků v Azure a stav registrace pro vaše předplatné, použijte:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Který vrátí výsledky podobné:

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Registrace poskytovatele prostředků nakonfiguruje vaše předplatné pro práci s poskytovatelem prostředků. Obor pro registraci je vždy předplatné. Ve výchozím nastavení jsou automaticky registrované řada poskytovatelů prostředků. Ale budete muset ručně zaregistrovat někteří poskytovatelé prostředků. Zaregistrovat poskytovatele prostředků, musíte mít oprávnění k provedení `/register/action` operace pro poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Který vrátí výsledky podobné:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Pokud stále máte typy prostředků od tohoto poskytovatele prostředků ve vašem předplatném, nelze zrušit registraci poskytovatele prostředků.

Pokud chcete zobrazit informace pro určitý prostředek zprostředkovatele, použijte:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Který vrátí výsledky podobné:

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Chcete-li zobrazit typy prostředků pro poskytovatele prostředků, použijte:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Který vrátí:

```powershell
batchAccounts
operations
locations
locations/quotas
```

Rozhraní API verze odpovídá verzi operace REST API, které jsou vydány přes poskytovatele prostředků. Jako poskytovatel prostředků umožňuje nové funkce, vydání nové verze rozhraní REST API.

Pokud chcete získat k dispozici verze rozhraní API pro typ prostředku, použijte:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Který vrátí:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Resource Manager je podporováno ve všech oblastech, ale nemusí být podporován prostředky, které nasadíte ve všech oblastech. Kromě toho může být omezení v rámci předplatného, které brání použití některých oblastí, které podporují prostředku.

Podporovaná umístění pro typ prostředku, použijte.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Který vrátí:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

Pokud chcete zobrazit všechny poskytovatele prostředků v Azure a stav registrace pro vaše předplatné, použijte:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Který vrátí výsledky podobné:

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Registrace poskytovatele prostředků nakonfiguruje vaše předplatné pro práci s poskytovatelem prostředků. Obor pro registraci je vždy předplatné. Ve výchozím nastavení jsou automaticky registrované řada poskytovatelů prostředků. Ale budete muset ručně zaregistrovat někteří poskytovatelé prostředků. Zaregistrovat poskytovatele prostředků, musíte mít oprávnění k provedení `/register/action` operace pro poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník.

```azurecli
az provider register --namespace Microsoft.Batch
```

Který vrátí zprávu registrace je na přechod.

Pokud stále máte typy prostředků od tohoto poskytovatele prostředků ve vašem předplatném, nelze zrušit registraci poskytovatele prostředků.

Pokud chcete zobrazit informace pro určitý prostředek zprostředkovatele, použijte:

```azurecli
az provider show --namespace Microsoft.Batch
```

Který vrátí výsledky podobné:

```azurecli
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Chcete-li zobrazit typy prostředků pro poskytovatele prostředků, použijte:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Který vrátí:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

Rozhraní API verze odpovídá verzi operace REST API, které jsou vydány přes poskytovatele prostředků. Jako poskytovatel prostředků umožňuje nové funkce, vydání nové verze rozhraní REST API.

Pokud chcete získat k dispozici verze rozhraní API pro typ prostředku, použijte:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Který vrátí:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Resource Manager je podporováno ve všech oblastech, ale nemusí být podporován prostředky, které nasadíte ve všech oblastech. Kromě toho může být omezení v rámci předplatného, které brání použití některých oblastí, které podporují prostředku.

Podporovaná umístění pro typ prostředku, použijte.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Který vrátí:

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="portal"></a>Portál

Pokud chcete zobrazit všechny poskytovatele prostředků v Azure a stav registrace pro vaše předplatné, vyberte **předplatná**.

![Vyberte předplatné.](./media/resource-manager-supported-services/select-subscriptions.png)

Vyberte předplatné, chcete-li zobrazit.

![Zadejte předplatné](./media/resource-manager-supported-services/subscription.png)

Vyberte **poskytovatelů prostředků** a zobrazí se seznam dostupných poskytovatelů prostředků.

![Zobrazení poskytovatelů prostředků](./media/resource-manager-supported-services/show-resource-providers.png)

Registrace poskytovatele prostředků nakonfiguruje vaše předplatné pro práci s poskytovatelem prostředků. Obor pro registraci je vždy předplatné. Ve výchozím nastavení jsou automaticky registrované řada poskytovatelů prostředků. Ale budete muset ručně zaregistrovat někteří poskytovatelé prostředků. Zaregistrovat poskytovatele prostředků, musíte mít oprávnění k provedení `/register/action` operace pro poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník. Chcete-li zaregistrovat poskytovatele prostředků, vyberte **zaregistrovat**.

![Registrace poskytovatele prostředků](./media/resource-manager-supported-services/register-provider.png)

Pokud stále máte typy prostředků od tohoto poskytovatele prostředků ve vašem předplatném, nelze zrušit registraci poskytovatele prostředků.

Chcete-li zobrazit informace o zprostředkovateli určitého prostředku, vyberte **všechny služby**.

![Vyberte všechny služby](./media/resource-manager-supported-services/more-services.png)

Vyhledejte **Průzkumníka prostředků** a vyberte ho z dostupných možností.

![Vyberte Průzkumník prostředků](./media/resource-manager-supported-services/select-resource-explorer.png)

Vyberte **Poskytovatelé**.

![Vybrat zprostředkovatele](./media/resource-manager-supported-services/select-providers.png)

Vyberte poskytovatele prostředků a typ prostředku, který chcete zobrazit.

![Vyberte typ prostředku](./media/resource-manager-supported-services/select-resource-type.png)

Resource Manager je podporováno ve všech oblastech, ale nemusí být podporován prostředky, které nasadíte ve všech oblastech. Kromě toho může být omezení v rámci předplatného, které brání použití některých oblastí, které podporují prostředku. Průzkumník prostředků zobrazí platné umístění pro typ prostředku.

![Zobrazit umístění](./media/resource-manager-supported-services/show-locations.png)

Rozhraní API verze odpovídá verzi operace REST API, které jsou vydány přes poskytovatele prostředků. Jako poskytovatel prostředků umožňuje nové funkce, vydání nové verze rozhraní REST API. Průzkumník prostředků zobrazí platná verze rozhraní API pro typ prostředku.

![Zobrazit verze rozhraní API](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="next-steps"></a>Další postup

* Další informace o vytváření šablon Resource Manageru najdete v tématu [šablon pro vytváření Azure Resource Manageru](resource-group-authoring-templates.md).
* Další informace o nasazení prostředků najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).
* Operace pro poskytovatele prostředků najdete v tématu [rozhraní Azure REST API](/rest/api/).
