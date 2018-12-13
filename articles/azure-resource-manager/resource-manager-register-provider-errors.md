---
title: Chyby registrace poskytovatele prostředků Azure | Dokumentace Microsoftu
description: Popisuje, jak vyřešit chyby registrace poskytovatele prostředků Azure.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/10/2018
ms.author: tomfitz
ms.openlocfilehash: 704aa488d40a18d7be0b64c9fc9a1bd33f8a3d96
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184538"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Vyřešit chyby registrace poskytovatele prostředků

Tento článek popisuje, chyby, které může dojít při používání poskytovatele prostředků, který jste dříve nepoužili ve vašem předplatném.

## <a name="symptom"></a>Příznak

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

## <a name="cause"></a>Příčina

Tyto chyby se zobrazí na jednu ze tří důvodů:

* Pro vaše předplatné není zaregistrovaný poskytovatel prostředků
* Verze rozhraní API není podporována pro typ prostředku
* Umístění není podporována pro typ prostředku

## <a name="solution-1---powershell"></a>Řešení 1 – PowerShell

Pokud používáte PowerShell, použijte **Get-AzureRmResourceProvider** zobrazíte stav registrace.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

K registraci poskytovatele použijte **Register-AzureRmResourceProvider** a zadejte název poskytovatele prostředků, kterou chcete zaregistrovat.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

Podporovaná umístění pro konkrétní typ prostředku, použijte:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Pokud chcete získat podporované verze rozhraní API pro konkrétní typ prostředku, použijte:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Řešení 2 – rozhraní příkazového řádku Azure

Pokud chcete zobrazit, zda poskytovatel je zaregistrovaný, použijte `az provider list` příkazu.

```azurecli-interactive
az provider list
```

Zaregistrovat poskytovatele prostředků, použijte `az provider register` příkaz a zadejte *obor názvů* k registraci.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Pokud chcete zobrazit podporovaná místa a verze rozhraní API pro typ prostředku, použijte:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Řešení 3 – Azure portal

Můžete zobrazit stav registrace a obor názvů zprostředkovatele prostředků prostřednictvím portálu pro registraci.

1. Z portálu, vyberte **všechny služby**.

   ![Vyberte všechny služby](./media/resource-manager-register-provider-errors/select-all-services.png)

1. Vyberte **Předplatná**.

   ![Vybrat předplatná](./media/resource-manager-register-provider-errors/select-subscriptions.png)

1. Seznam předplatných vyberte předplatné, které chcete použít pro registrace poskytovatele prostředků.

   ![Vyberte předplatné, zaregistrujte poskytovatele prostředků](./media/resource-manager-register-provider-errors/select-subscription-to-register.png)

1. Pro vaše předplatné, vyberte **poskytovatelů prostředků**.

   ![Vyberte poskytovatele prostředků](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. Podívejte se na seznam poskytovatelů prostředků a v případě potřeby vyberte **zaregistrovat** odkaz se zaregistrovat poskytovatele prostředků typu, který zkoušíte nasadit.

   ![Seznam poskytovatelů prostředků](./media/resource-manager-register-provider-errors/list-resource-providers.png)
