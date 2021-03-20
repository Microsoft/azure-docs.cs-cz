---
title: Chyby registrace poskytovatele prostředků
description: Popisuje, jak vyřešit chyby registrace poskytovatele prostředků Azure při nasazování prostředků pomocí Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 143cf03a33739f43a29af94fc2f8a336fb3aef8f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89076663"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Řešení chyb s registrací poskytovatele prostředků

Tento článek popisuje chyby, se kterými se můžete setkat při používání poskytovatele prostředků, kterého jste předtím ve svém předplatném použili.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Příznak

Při nasazování prostředku se může zobrazit následující kód chyby a zpráva:

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

Chybová zpráva by vám měla poskytnout návrhy na podporovaná umístění a verze rozhraní API. Šablonu můžete změnit na jednu z navrhovaných hodnot. Většina poskytovatelů je zaregistrována automaticky Azure Portal nebo rozhraní příkazového řádku, které používáte, ale ne všechny. Pokud jste konkrétního poskytovatele prostředků nepoužili dřív, možná budete muset tohoto poskytovatele zaregistrovat.

Nebo pokud zakážete automatické vypnutí virtuálních počítačů, může se zobrazit chybová zpráva podobná této:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Příčina

Tyto chyby se zobrazí v jednom z následujících důvodů:

* Požadovaný poskytovatel prostředků nebyl zaregistrován pro vaše předplatné.
* Verze rozhraní API není pro typ prostředku podporovaná.
* Umístění není pro daný typ prostředku podporované.
* Pro automatické vypnutí virtuálních počítačů musí být zaregistrovaný poskytovatel prostředků Microsoft. DevTestLab.

## <a name="solution-1---powershell"></a>Řešení 1 – PowerShell

Pro prostředí PowerShell použijte **příkaz Get-AzResourceProvider** , abyste viděli stav registrace.

```powershell
Get-AzResourceProvider -ListAvailable
```

Chcete-li zaregistrovat poskytovatele, použijte **Register-AzResourceProvider** a zadejte název poskytovatele prostředků, který chcete zaregistrovat.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Pokud chcete získat podporovaná umístění pro určitý typ prostředku, použijte:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Pokud chcete získat podporované verze rozhraní API pro konkrétní typ prostředku, použijte:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Řešení 2 – Azure CLI

Chcete-li zjistit, zda je poskytovatel zaregistrován, použijte `az provider list` příkaz.

```azurecli-interactive
az provider list
```

Chcete-li zaregistrovat poskytovatele prostředků, použijte `az provider register` příkaz a zadejte *obor názvů* , který chcete zaregistrovat.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Pokud chcete zobrazit podporovaná umístění a verze rozhraní API pro typ prostředku, použijte:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Řešení 3 – Azure Portal

Pomocí portálu můžete zobrazit stav registrace a zaregistrovat obor názvů poskytovatele prostředků.

1. Na portálu vyberte **všechny služby**.

   ![Vybrat všechny služby](./media/error-register-resource-provider/select-all-services.png)

1. Vyberte **Předplatná**.

   ![Vybrat odběry](./media/error-register-resource-provider/select-subscriptions.png)

1. V seznamu odběrů vyberte předplatné, které chcete použít k registraci poskytovatele prostředků.

   ![Vyberte předplatné pro registraci poskytovatele prostředků.](./media/error-register-resource-provider/select-subscription-to-register.png)

1. U svého předplatného vyberte **poskytovatelé prostředků**.

   ![Vybrat poskytovatele prostředků](./media/error-register-resource-provider/select-resource-provider.png)

1. Podívejte se na seznam poskytovatelů prostředků a v případě potřeby vyberte odkaz **zaregistrovat** a zaregistrujte poskytovatele prostředků typu, který se pokoušíte nasadit.

   ![Vypsat poskytovatele prostředků](./media/error-register-resource-provider/list-resource-providers.png)
