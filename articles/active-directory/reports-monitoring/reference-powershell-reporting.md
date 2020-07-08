---
title: Rutiny Azure AD PowerShellu pro vytváření sestav | Microsoft Docs
description: Reference k rutinám Azure AD PowerShellu pro vytváření sestav.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/12/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2192c472e00d123780ec6bc5574e7b9fe326258b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "75495302"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Rutiny Azure AD PowerShellu pro vytváření sestav

> [!NOTE] 
> Tyto rutiny PowerShellu momentálně fungují jenom s modulem [Azure AD ve verzi Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) . Upozorňujeme, že modul Preview není navržený pro použití v produkčním prostředí. 

K instalaci verze Public Preview použijte následující příkaz. 

```powershell
Install-module AzureADPreview
```
Další informace o tom, jak se připojit ke službě Azure AD pomocí PowerShellu, najdete v článku [Azure AD PowerShell pro graf](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).  

Pomocí sestav Azure Active Directory (Azure AD) můžete získat podrobnosti o aktivitách kolem všech operací zápisu ve směru (protokoly auditu) a ověřovacích dat (protokoly přihlášení). I když jsou informace k dispozici pomocí Graph APIu, teď můžete načíst stejná data pomocí rutin Azure AD PowerShell pro vytváření sestav.

Tento článek poskytuje přehled rutin PowerShellu, které se použijí pro protokoly auditu a protokoly přihlášení.

## <a name="audit-logs"></a>Protokoly auditu

[Protokoly auditu](concept-audit-logs.md) poskytují sledovatelnost prostřednictvím protokolů pro všechny změny prováděné různými funkcemi v rámci služby Azure AD. Mezi příklady protokolů auditu patří změny provedené u všech prostředků v rámci Azure AD, jako je přidávání nebo odebírání uživatelů, aplikací, skupin, rolí a zásad.

Přístup k protokolům auditu získáte pomocí rutiny Get-AzureADAuditDirectoryLogs.


| Scénář                      | Příkaz PowerShellu |
| :--                           | :--                |
| Zobrazovaný název aplikace      | Get-AzureADAuditDirectoryLogs-Filter "initiatedBy/App/DisplayName" Azure AD Cloud Sync "" |
| Kategorie                      | Get-AzureADAuditDirectoryLogs-Filter "kategorie EQ" Správa aplikací "" |
| Datum a čas aktivity            | Get-AzureADAuditDirectoryLogs-Filter "activityDateTime gt 2019-04-18" |
| Všechny výš uvedené položky              | Get-AzureADAuditDirectoryLogs-Filter "initiatedBy/App/DisplayName EQ" Azure AD Cloud Sync "and Category EQ" Správa aplikací "and activityDateTime gt 2019-04-18"|


Příklad tohoto příkazu je znázorněn na následujícím obrázku. 

![Tlačítko Souhrn dat](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Protokoly přihlašování

Protokoly [přihlášení](concept-sign-ins.md) poskytují informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.

Přístup k protokolům přihlášení získáte pomocí rutiny Get-AzureADAuditSignInLogs.


| Scénář                      | Příkaz PowerShellu |
| :--                           | :--                |
| Zobrazované jméno uživatele             | Get-AzureADAuditSignInLogs-Filter "userDisplayName EQ" Alexander Perkins "" |
| Vytvořit datum a čas              | Get-AzureADAuditSignInLogs-Filter "createdDateTime gt 2019-04-18T17:30:00.0 Z" (vše od 5:30 pm v 4/18) |
| Status                        | Get-AzureADAuditSignInLogs-Filter "status/errorCode EQ 50105" |
| Zobrazovaný název aplikace      | Get-AzureADAuditSignInLogs-Filter "appDisplayName EQ" StoreFrontStudio [wsfed Enabled] "" |
| Všechny výš uvedené položky              | Get-AzureADAuditSignInLogs-Filter "userDisplayName EQ" Alexander Perkins "a status/errorCode New 0 and appDisplayName EQ" StoreFrontStudio [wsfed Enabled] "" |


Příklad tohoto příkazu je znázorněn na následujícím obrázku. 

![Tlačítko Souhrn dat](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Další kroky

- [Přehled sestav Azure AD](overview-reports.md).
- [Sestava protokolů auditu](concept-audit-logs.md). 
- [Programový přístup k sestavám Azure AD](concept-reporting-api.md)
