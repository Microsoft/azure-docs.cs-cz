---
title: Obnovení odstraněných aplikací
description: Přečtěte si, jak obnovit odstraněnou aplikaci ve službě Azure App Service. Vyhněte se bolesti hlavy náhodně smazané aplikace.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: c7d778a0afca4b3552976526d58a2cb2efe12161
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75689622"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Obnovení odstraněné aplikace App Service pomocí PowerShellu

Pokud jste omylem odstranili aplikaci ve službě Azure App Service, můžete ji obnovit pomocí příkazů z [modulu Az PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

> [!NOTE]
> Odstraněné aplikace jsou ze systému vymazány 30 dní po počátečním odstranění. Jakmile je aplikace vymazána, nelze ji obnovit.
>

## <a name="re-register-app-service-resource-provider"></a>Znovu zaregistrovat poskytovatele prostředků služby App Service
Někteří zákazníci se mohou sejít s problémem, kdy se nezdaří načtení seznamu odstraněných aplikací. Chcete-li tento problém vyřešit, spusťte následující příkaz:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Seznam odstraněných aplikací

Chcete-li získat kolekci odstraněných `Get-AzDeletedWebApp`aplikací, můžete použít .

Podrobnosti o konkrétní odstraněné aplikaci můžete použít:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

Podrobné informace zahrnují:

- **DeletedSiteId**: Jedinečný identifikátor aplikace, který se používá pro scénáře, kde bylo odstraněno více aplikací se stejným názvem
- **SubscriptionID**: Odběr obsahující odstraněný prostředek
- **Umístění:** Umístění původní aplikace
- **ResourceGroupName**: Název původní skupiny prostředků
- **Název**: Název původní aplikace.
- **Slot**: název slotu.
- **Doba odstranění**: Kdy byla aplikace odstraněna  

## <a name="restore-deleted-app"></a>Obnovení odstraněné aplikace

Jakmile byla aplikace, kterou chcete obnovit, identifikována, můžete ji obnovit pomocí `Restore-AzDeletedWebApp`aplikace .

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

Vstupy pro příkaz jsou:

- **Skupina prostředků:** Cílová skupina prostředků, kde bude aplikace obnovena
- **Název**: Název aplikace by měl být globálně jedinečný.
- **TargetAppServicePlanName:** Plán služby App Service propojený s aplikací

Ve `Restore-AzDeletedWebApp` výchozím nastavení obnoví jak konfiguraci aplikace, tak i obsah. Pokud chcete pouze obnovit obsah, `-RestoreContentOnly` použijte příznak s tímto příkazem.

> [!NOTE]
> Pokud byla aplikace hostovaná a pak odstraněna z prostředí služby App Service, pak ji lze obnovit pouze v případě, že odpovídající prostředí služby App Service stále existuje.
>

Úplný odkaz na příkaz najdete zde: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
