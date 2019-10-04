---
title: Obnovení odstraněných App Service aplikací – Azure App Service
description: Naučte se, jak obnovit odstraněnou App Service aplikaci pomocí PowerShellu.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: 7b3a21f3cfee806dc94353e0bc6c11e88641ea34
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827525"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Obnovení odstraněných App Service aplikace pomocí PowerShellu

Pokud jste nastali omylem odstranit aplikaci v Azure App Service, můžete ji obnovit pomocí příkazů z [modulu AZ PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

## <a name="list-deleted-apps"></a>Vypsat odstraněné aplikace

Pokud chcete získat kolekci odstraněných aplikací, můžete použít `Get-AzDeletedWebApp`.

Podrobnosti o konkrétní odstraněné aplikaci můžete použít:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app>
```

Podrobné informace zahrnují:

- **DeletedSiteId**: jedinečný identifikátor pro aplikaci, který se používá pro scénáře, ve kterých se odstranilo víc aplikací se stejným názvem.
- **SubscriptionId**: předplatné obsahující odstraněný prostředek
- **Umístění**: umístění původní aplikace
- **ResourceGroupName**: název původní skupiny prostředků
- **Name**: název původní aplikace.
- **Slot**: název slotu.
- **Čas odstranění**: kdy se aplikace odstranila  

## <a name="restore-deleted-app"></a>Obnovit odstraněnou aplikaci

Po identifikaci aplikace, kterou chcete obnovit, ji můžete obnovit pomocí `Restore-AzDeletedWebApp`.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

Vstupy pro příkaz jsou:

- **Skupina prostředků**: cílová skupina prostředků, do které se aplikace obnoví
- **Název**: název aplikace by měl být globálně jedinečný.
- **TargetAppServicePlanName**: App Service plán propojený s aplikací

Ve výchozím nastavení je `Restore-AzDeletedWebApp` obnovit jak konfiguraci aplikace, tak i obsah. Chcete-li obnovit pouze obsah, použijte příznak `-RestoreContentOnly` s tímto rutiny.

Úplný odkaz na rutiny najdete tady: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
