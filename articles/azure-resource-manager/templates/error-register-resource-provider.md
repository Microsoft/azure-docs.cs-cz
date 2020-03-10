---
title: Chyby registrace poskytovatele prostředků
description: Popisuje, jak vyřešit chyby registrace poskytovatele prostředků Azure při nasazování prostředků pomocí Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.openlocfilehash: a9182be53cc91240a62ab201efc53d674f7cf427
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390055"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Vyřešit chyby registrace poskytovatele prostředků

Tento článek popisuje, chyby, které může dojít při používání poskytovatele prostředků, který jste dříve nepoužili ve vašem předplatném.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptom

Při nasazování prostředků, může se zobrazit následující kód chyby a zprávy:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Nebo můžete obdržet podobná zpráva s oznámením:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Chybová zpráva by vám měl dát návrhy pro podporované umístění a verze rozhraní API. Šablony můžete změnit na jednu z navrhovaných hodnot. Většina poskytovatelů jsou registrované automaticky pomocí webu Azure portal nebo rozhraní příkazového řádku, které používáte, ale ne všechny. Pokud jste ještě nepoužívali poskytovatele určitého prostředku před, budete muset zaregistrovat tohoto poskytovatele.

Nebo pokud zakážete automatické vypnutí virtuálních počítačů, může se zobrazit chybová zpráva podobná této:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Příčina

Tyto chyby se zobrazí v jednom z následujících důvodů:

* Požadovaný poskytovatel prostředků nebyl zaregistrován pro vaše předplatné.
* Verze rozhraní API není podporována pro typ prostředku
* Umístění není podporována pro typ prostředku
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

Podporovaná umístění pro konkrétní typ prostředku, použijte:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Pokud chcete získat podporované verze rozhraní API pro konkrétní typ prostředku, použijte:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Řešení 2 – rozhraní příkazového řádku Azure

Chcete-li zjistit, zda je poskytovatel zaregistrován, použijte příkaz `az provider list`.

```azurecli-interactive
az provider list
```

Chcete-li zaregistrovat poskytovatele prostředků, použijte příkaz `az provider register` a zadejte *obor názvů* , který chcete zaregistrovat.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Pokud chcete zobrazit podporovaná místa a verze rozhraní API pro typ prostředku, použijte:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Řešení 3 – Azure portal

Můžete zobrazit stav registrace a obor názvů zprostředkovatele prostředků prostřednictvím portálu pro registraci.

1. Na portálu vyberte **všechny služby**.

   ![Vyberte všechny služby](./media/error-register-resource-provider/select-all-services.png)

1. Vyberte **Předplatná**.

   ![Vybrat předplatná](./media/error-register-resource-provider/select-subscriptions.png)

1. Seznam předplatných vyberte předplatné, které chcete použít pro registrace poskytovatele prostředků.

   ![Vyberte předplatné, zaregistrujte poskytovatele prostředků](./media/error-register-resource-provider/select-subscription-to-register.png)

1. U svého předplatného vyberte **poskytovatelé prostředků**.

   ![Vyberte poskytovatele prostředků](./media/error-register-resource-provider/select-resource-provider.png)

1. Podívejte se na seznam poskytovatelů prostředků a v případě potřeby vyberte odkaz **zaregistrovat** a zaregistrujte poskytovatele prostředků typu, který se pokoušíte nasadit.

   ![Seznam poskytovatelů prostředků](./media/error-register-resource-provider/list-resource-providers.png)
