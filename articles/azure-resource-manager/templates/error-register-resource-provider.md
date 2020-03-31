---
title: Chyby registrace zprostředkovatele prostředků
description: Popisuje, jak vyřešit chyby registrace zprostředkovatele prostředků Azure při nasazování prostředků pomocí Správce prostředků Azure.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.openlocfilehash: a9182be53cc91240a62ab201efc53d674f7cf427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273771"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Řešení chyb s registrací poskytovatele prostředků

Tento článek popisuje chyby, se kterými se může dojít při použití zprostředkovatele prostředků, který jste dříve nepoužili v předplatném.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Příznak

Při nasazování prostředků se může zobrazit následující kód chyby a zpráva:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Nebo se může zobrazit podobná zpráva, která uvádí:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Chybová zpráva by měla poskytnout návrhy pro podporovaná umístění a verze rozhraní API. Šablonu můžete změnit na jednu z navržených hodnot. Většina poskytovatelů se automaticky zaeviduje portálem Azure nebo rozhraním příkazového řádku, které používáte, ale ne všechny. Pokud jste dříve nepoužívali konkrétního poskytovatele prostředků, budete možná muset tohoto zprostředkovatele zaregistrovat.

Nebo při zakázání automatického vypnutí pro virtuální počítače se může zobrazit chybová zpráva podobná:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Příčina

Tyto chyby se zobrazí z jednoho z těchto důvodů:

* Požadovaný poskytovatel prostředků nebyl pro vaše předplatné zaregistrován.
* Verze rozhraní API není podporována pro typ prostředku
* Umístění není podporováno pro typ prostředku
* Pro automatické vypnutí virtuálních počítače musí být zaregistrován poskytovatel prostředků Microsoft.DevTestLab.

## <a name="solution-1---powershell"></a>Řešení 1 - PowerShell

V prostředí PowerShell uvidíte stav registrace pomocí **get-azResourceProvider.**

```powershell
Get-AzResourceProvider -ListAvailable
```

Chcete-li zaregistrovat zprostředkovatele, použijte **Register-AzResourceProvider** a zadejte název poskytovatele prostředků, který chcete zaregistrovat.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Chcete-li získat podporovaná umístění pro určitý typ prostředku, použijte:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Chcete-li získat podporované verze rozhraní API pro určitý typ prostředku, použijte:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Řešení 2 – Azure CLI

Chcete-li zjistit, zda je `az provider list` poskytovatel registrován, použijte příkaz.

```azurecli-interactive
az provider list
```

Chcete-li zaregistrovat zprostředkovatele prostředků, použijte `az provider register` příkaz a zadejte obor *názvů,* který chcete zaregistrovat.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Chcete-li zobrazit podporovaná umístění a verze rozhraní API pro typ prostředku, použijte:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Řešení 3 – portál Azure

Můžete zobrazit stav registrace a zaregistrovat obor názvů poskytovatele prostředků prostřednictvím portálu.

1. Na portálu vyberte **Všechny služby**.

   ![Vybrat všechny služby](./media/error-register-resource-provider/select-all-services.png)

1. Vyberte **Předplatná**.

   ![Vybrat předplatná](./media/error-register-resource-provider/select-subscriptions.png)

1. Ze seznamu předplatných vyberte předplatné, které chcete použít pro registraci poskytovatele prostředků.

   ![Vyberte předplatné pro registraci zprostředkovatele prostředků.](./media/error-register-resource-provider/select-subscription-to-register.png)

1. Pro vaše předplatné vyberte **zprostředkovatele prostředků**.

   ![Vybrat poskytovatele prostředků](./media/error-register-resource-provider/select-resource-provider.png)

1. Podívejte se na seznam poskytovatelů prostředků a v případě potřeby vyberte odkaz **Registrovat** a zaregistrujte poskytovatele prostředků typu, který se pokoušíte nasadit.

   ![Seznam poskytovatelů prostředků](./media/error-register-resource-provider/list-resource-providers.png)
