---
title: Rutiny prostředí Azure AD PowerShell pro vytváření sestav | Dokumenty společnosti Microsoft
description: Odkaz na rutiny prostředí Azure AD PowerShell pro vytváření sestav.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495302"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Rutiny Azure AD PowerShellu pro vytváření sestav

> [!NOTE] 
> Tyto rutiny prostředí Powershell aktuálně fungovat pouze s modulem [Azure AD Preview.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) Vezměte prosím na vědomí, že modul náhledu není navržen pro produkční použití. 

Chcete-li nainstalovat verzi Public Preview, použijte následující. 

```powershell
Install-module AzureADPreview
```
Další informace o tom, jak se připojit k Azure AD pomocí powershellu , najdete v článku [Azure AD Powershell pro graf](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).  

Pomocí sestav Azure Active Directory (Azure AD) můžete získat podrobnosti o aktivitách kolem všech operací zápisu ve vašem směru (protokoly auditu) a ověřovacích dat (protokoly přihlášení). Přestože informace jsou k dispozici pomocí rozhraní MS Graph API, teď můžete načíst stejná data pomocí rutin prostředí Azure AD PowerShell pro vytváření sestav.

Tento článek poskytuje přehled rutin prostředí PowerShell, které se mají použít pro protokoly auditování a protokoly přihlášení.

## <a name="audit-logs"></a>Protokoly auditu

[Protokoly auditu](concept-audit-logs.md) poskytují sledovatelnost prostřednictvím protokolů pro všechny změny provedené různými funkcemi v rámci azure ad. Příklady protokolů auditu zahrnují změny provedené ve všech prostředcích v rámci Azure AD, jako je přidání nebo odebrání uživatelů, aplikací, skupin, rolí a zásad.

Získáte přístup k protokolům auditu pomocí rutiny Get-AzureDAuditDirectoryLogs.


| Scénář                      | Příkaz PowerShellu |
| :--                           | :--                |
| Zobrazovaný název aplikace      | Get-AzureADAuditDirectoryLogs -Filtr "initiatedBy/app/displayName eq 'Azure AD Cloud Sync'" |
| Kategorie                      | Get-AzureADAuditDirectoryLogs -Filtrovat "kategorie eq 'Správa aplikací'" |
| Čas data aktivity            | Get-AzureADAuditDirectoryLogs -Filtr "activityDateTime gt 2019-04-18" |
| Všechny výš uvedené položky              | Get-AzureADAuditDirectoryLogs -Filtr "initiatedBy/app/displayName eq 'Azure AD Cloud Sync' a kategorie eq 'Správa aplikací' a activityDateTime gt 2019-04-18"|


Následující obrázek ukazuje příklad pro tento příkaz. 

![Tlačítko "Souhrn dat"](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Protokoly přihlašování

Protokoly [přihlášení](concept-sign-ins.md) poskytují informace o využití spravovaných aplikací a aktivity přihlášení uživatele.

Získáte přístup k protokolům přihlášení pomocí rutiny Get-AzureADAuditInInLogs.


| Scénář                      | Příkaz PowerShellu |
| :--                           | :--                |
| Zobrazované jméno uživatele             | Get-AzureADAuditSignInLogs -Filtr "userDisplayName eq 'Timothy Perkins'" |
| Vytvořit čas data              | Get-AzureADAuditSignInLogs -Filtr "createdDateTime gt 2019-04-18T17:30:00.0Z" (Vše od 17:30 dne 4/18) |
| Status                        | Get-AzureADAuditSignInLogs -Filtr "status/errorCode eq 50105" |
| Zobrazovaný název aplikace      | Get-AzureADAuditSignInLogs -Filtr "appDisplayName eq 'StoreFrontStudio [wsfed povoleno]"" |
| Všechny výš uvedené položky              | Get-AzureADAuditSignInLogs -Filtr "userDisplayName eq 'Timothy Perkins' a status/errorCode ne 0 a appDisplayName eq 'StoreFrontStudio [wsfed enabled]'" |


Následující obrázek ukazuje příklad pro tento příkaz. 

![Tlačítko "Souhrn dat"](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Další kroky

- [Přehled sestav Azure AD](overview-reports.md).
- [Sestava protokolů auditu](concept-audit-logs.md). 
- [Programový přístup k sestavám Azure AD](concept-reporting-api.md)
