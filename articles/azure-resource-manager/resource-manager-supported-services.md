---
title: Poskytovatelé prostředků Azure a typy prostředků | Microsoft Docs
description: Popisuje zprostředkovatele prostředků, které podporují Resource Manager, jejich schémata a dostupné verze rozhraní API a oblastí, které může být hostitelem prostředky.
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
ms.date: 07/25/2017
ms.author: tomfitz
ms.openlocfilehash: 811bb40816339dbe7097e429722625a3ae5c95c0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="resource-providers-and-types"></a>Poskytovatelé prostředků a typy

Při nasazení prostředků, můžete často potřebují k načtení informací o zprostředkovatelé prostředků a typy. V tomto článku se dozvíte na:

* Zobrazení všech poskytovatelů prostředků v Azure
* Zkontrolovat stav registrace poskytovatele prostředků
* Registrace poskytovatele prostředků
* Zobrazit typy prostředků pro poskytovatele prostředků
* Zobrazení platná umístění pro typ prostředku
* Zobrazit platná verze rozhraní API pro typ prostředku

Abyste mohli provést tyto kroky prostřednictvím portálu, prostředí PowerShell nebo rozhraní příkazového řádku Azure.

## <a name="powershell"></a>PowerShell

Pokud chcete zobrazit všech poskytovatelů prostředků v Azure a stav registrace pro vaše předplatné, použijte:

```powershell
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Které vrátí výsledky podobné:

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Registruje se poskytovatel prostředků nakonfiguruje vaše předplatné pro práci s poskytovatelem prostředků. Rozsah pro registraci je vždy předplatné. Ve výchozím nastavení se automaticky registruje mnoho poskytovatelů prostředků. Můžete však ručně zaregistrovat někteří poskytovatelé prostředků. Registrace poskytovatele prostředků, musíte mít oprávnění k provedení `/register/action` operace pro poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

Které vrátí výsledky podobné:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Pokud máte pořád typy prostředků z tohoto zprostředkovatele prostředků v rámci vašeho předplatného, nelze zrušit registraci poskytovatele prostředků.

Pokud chcete zobrazit informace pro určitý prostředek zprostředkovatele, použijte:

```powershell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

Které vrátí výsledky podobné:

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Chcete-li zobrazit typy prostředků pro poskytovatele prostředků, použijte:

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Která vrací:

```powershell
batchAccounts
operations
locations
locations/quotas
```

Verze rozhraní API odpovídá verzi operace REST API, které vydávají poskytovatelem prostředků. Zprostředkovatel prostředků umožňuje nové funkce, uvolní novou verzi rozhraní REST API. 

K dispozici verze rozhraní API pro typ prostředku, použijte:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Která vrací:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Správce prostředků se podporuje ve všech oblastech, ale nemusí být podporován prostředky, které můžete nasadit ve všech oblastech. Kromě toho může být omezení na vaše předplatné, které zabránit vám v použití některé oblasti, které podporují prostředku. 

Podporovaná umístění pro typ prostředku, použijte.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Která vrací:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI
Pokud chcete zobrazit všech poskytovatelů prostředků v Azure a stav registrace pro vaše předplatné, použijte:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Které vrátí výsledky podobné:

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Registruje se poskytovatel prostředků nakonfiguruje vaše předplatné pro práci s poskytovatelem prostředků. Rozsah pro registraci je vždy předplatné. Ve výchozím nastavení se automaticky registruje mnoho poskytovatelů prostředků. Můžete však ručně zaregistrovat někteří poskytovatelé prostředků. Registrace poskytovatele prostředků, musíte mít oprávnění k provedení `/register/action` operace pro poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník.

```azurecli
az provider register --namespace Microsoft.Batch
```

Které vrátí zprávu, že registrace se průběžné.

Pokud máte pořád typy prostředků z tohoto zprostředkovatele prostředků v rámci vašeho předplatného, nelze zrušit registraci poskytovatele prostředků.

Pokud chcete zobrazit informace pro určitý prostředek zprostředkovatele, použijte:

```azurecli
az provider show --namespace Microsoft.Batch
```

Které vrátí výsledky podobné:

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

Která vrací:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

Verze rozhraní API odpovídá verzi operace REST API, které vydávají poskytovatelem prostředků. Zprostředkovatel prostředků umožňuje nové funkce, uvolní novou verzi rozhraní REST API. 

K dispozici verze rozhraní API pro typ prostředku, použijte:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Která vrací:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Správce prostředků se podporuje ve všech oblastech, ale nemusí být podporován prostředky, které můžete nasadit ve všech oblastech. Kromě toho může být omezení na vaše předplatné, které zabránit vám v použití některé oblasti, které podporují prostředku. 

Podporovaná umístění pro typ prostředku, použijte.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Která vrací:

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

Pokud chcete zobrazit všech poskytovatelů prostředků v Azure a stav registrace pro vaše předplatné, vyberte **odběry**.

![Vyberte předplatná](./media/resource-manager-supported-services/select-subscriptions.png)

Zvolte předplatné, které chcete zobrazit.

![Zadejte předplatné](./media/resource-manager-supported-services/subscription.png)

Vyberte **zprostředkovatelé prostředků** a zobrazit seznam dostupných poskytovatelů prostředků.

![Zobrazit zprostředkovatelé prostředků](./media/resource-manager-supported-services/show-resource-providers.png)

Registruje se poskytovatel prostředků nakonfiguruje vaše předplatné pro práci s poskytovatelem prostředků. Rozsah pro registraci je vždy předplatné. Ve výchozím nastavení se automaticky registruje mnoho poskytovatelů prostředků. Můžete však ručně zaregistrovat někteří poskytovatelé prostředků. Registrace poskytovatele prostředků, musíte mít oprávnění k provedení `/register/action` operace pro poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník. Registrace poskytovatele prostředků, vyberte **zaregistrovat**.

![registrace poskytovatele prostředků](./media/resource-manager-supported-services/register-provider.png)

Pokud máte pořád typy prostředků z tohoto zprostředkovatele prostředků v rámci vašeho předplatného, nelze zrušit registraci poskytovatele prostředků.

Chcete-li zobrazit informace pro určitý prostředek poskytovatele, vyberte **všechny služby**.

![Vyberte všechny služby](./media/resource-manager-supported-services/more-services.png)

Vyhledejte **Průzkumníka prostředků** a vyberte z dostupných možností.

![Vyberte Průzkumník prostředků](./media/resource-manager-supported-services/select-resource-explorer.png)

Vyberte **Poskytovatelé**.

![Vyberte zprostředkovatele](./media/resource-manager-supported-services/select-providers.png)

Vyberte zprostředkovatele prostředků a typ prostředku, který chcete zobrazit.

![Vyberte typ prostředku](./media/resource-manager-supported-services/select-resource-type.png)

Správce prostředků se podporuje ve všech oblastech, ale nemusí být podporován prostředky, které můžete nasadit ve všech oblastech. Kromě toho může být omezení na vaše předplatné, které zabránit vám v použití některé oblasti, které podporují prostředku. Průzkumník prostředků zobrazí platná umístění pro typ prostředku.

![Zobrazit umístění](./media/resource-manager-supported-services/show-locations.png)

Verze rozhraní API odpovídá verzi operace REST API, které vydávají poskytovatelem prostředků. Zprostředkovatel prostředků umožňuje nové funkce, uvolní novou verzi rozhraní REST API. Průzkumník prostředků zobrazí platná verze rozhraní API pro typ prostředku.

![Zobrazit verze rozhraní API](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="next-steps"></a>Další postup
* Další informace o vytváření šablon Resource Manageru, najdete v části [šablon pro tvorbu Azure Resource Manageru](resource-group-authoring-templates.md).
* Další informace o nasazení prostředků najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).
* Operace pro poskytovatele prostředků najdete v tématu [rozhraní REST API Azure](/rest/api/).

