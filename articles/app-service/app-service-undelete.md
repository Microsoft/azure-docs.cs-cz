---
title: Obnovení odstraněných App Service aplikací – Azure App Service
description: Naučte se, jak obnovit odstraněnou App Service aplikaci pomocí PowerShellu.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: a766f7ed7a82874df8ef7506226de0d6f38a5847
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219535"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Obnovení odstraněných App Service aplikace pomocí PowerShellu

Pokud jste nastali omylem odstranit aplikaci v Azure App Service, můžete ji obnovit pomocí příkazů z [modulu AZ PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

## <a name="list-deleted-apps"></a>Výpis odstraněných aplikací

Pokud chcete získat kolekci odstraněných aplikací, můžete použít `Get-AzDeletedWebApp`.

Podrobnosti o konkrétní odstraněné aplikaci můžete použít:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app>
```

Podrobné informace zahrnují:

- **DeletedSiteId**: Jedinečný identifikátor pro aplikaci, který se používá pro scénáře, ve kterých se odstranilo víc aplikací se stejným názvem.
- **SubscriptionId**: Předplatné obsahující odstraněný prostředek
- **Umístění**: Umístění původní aplikace
- **ResourceGroupName**: Název původní skupiny prostředků
- **Název**: Název původní aplikace
- **Slot**: název slotu.
- **Čas odstranění**: Kdy byla aplikace odstraněna  

## <a name="restore-deleted-app"></a>Obnovit odstraněnou aplikaci

Po identifikaci aplikace, kterou chcete obnovit, ji můžete obnovit pomocí `Restore-AzDeletedWebApp`.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

Vstupy pro příkaz jsou:

- **Skupina prostředků**: Cílová skupina prostředků, do které se aplikace obnoví
- **Název**: Název aplikace by měl být globálně jedinečný.
- **TargetAppServicePlanName**: App Service plán propojený s aplikací

Ve výchozím `Restore-AzDeletedWebApp` nastavení se obnoví i konfigurace aplikace i obsah. Chcete-li obnovit pouze obsah, použijte `-RestoreContentOnly` příznak s tímto rutiny.
