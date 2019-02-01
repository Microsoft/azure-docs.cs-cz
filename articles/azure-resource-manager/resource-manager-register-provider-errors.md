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
ms.openlocfilehash: e87ff83470ac8d375b4eee3b69f8793e3e11c0f5
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497413"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Vyřešit chyby registrace poskytovatele prostředků

Tento článek popisuje, chyby, které může dojít při používání poskytovatele prostředků, který jste dříve nepoužili ve vašem předplatném.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

Pokud používáte PowerShell, použijte **Get-AzResourceProvider** zobrazíte stav registrace.

```powershell
Get-AzResourceProvider -ListAvailable
```

K registraci poskytovatele použijte **Register-AzResourceProvider** a zadejte název poskytovatele prostředků, kterou chcete zaregistrovat.

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
