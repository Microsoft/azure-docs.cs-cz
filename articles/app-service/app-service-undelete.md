---
title: Obnovit odstraněné aplikace
description: Přečtěte si, jak obnovit odstraněnou aplikaci v Azure App Service. Vyhněte se starostí nechtěné odstraněné aplikace.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: a30ac638422f99134ebe9cc26e4b418f5de079b9
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672153"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Obnovení odstraněných App Service aplikace pomocí PowerShellu

Pokud jste nastali omylem odstranit aplikaci v Azure App Service, můžete ji obnovit pomocí příkazů z [modulu AZ PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

## <a name="re-register-app-service-resource-provider"></a>Znovu zaregistrovat App Service poskytovatel prostředků
Někteří zákazníci můžou narazit na problém, při kterém se nepovede načíst seznam odstraněných aplikací. Chcete-li tento problém vyřešit, spusťte následující příkaz:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Výpis odstraněných aplikací

Pokud chcete získat kolekci odstraněných aplikací, můžete použít `Get-AzDeletedWebApp`.

Podrobnosti o konkrétní odstraněné aplikaci můžete použít:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
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

Ve výchozím nastavení `Restore-AzDeletedWebApp` obnoví konfiguraci aplikace i obsah. Chcete-li obnovit pouze obsah, použijte příznak `-RestoreContentOnly` s tímto rutiny.

> [!NOTE]
> Pokud byla aplikace hostována a poté odstraněna z App Service Environment pak ji lze obnovit pouze v případě, že odpovídající App Service Environment stále existují.
>

Úplný odkaz na rutiny najdete tady: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
