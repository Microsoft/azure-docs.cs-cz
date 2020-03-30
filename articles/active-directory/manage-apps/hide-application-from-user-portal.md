---
title: Skrytí aplikace před uživatelským prostředím ve službě Azure AD
description: Jak skrýt aplikaci z uživatelského prostředí v panelech přístupu Azure Active Directory nebo office 365 spouštěče.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5718adf4fd76e2fbd0ff793dd2fa33ee08f7c0fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295054"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Skrytí aplikací před koncovými uživateli ve službě Azure Active Directory

Pokyny, jak skrýt aplikace z panelu MyApps koncových uživatelů nebo před spouštěčem Office 365. Pokud je aplikace skrytá, uživatelé mají stále oprávnění k aplikaci. 

## <a name="prerequisites"></a>Požadavky

Oprávnění správce aplikací jsou nutná ke skrytí aplikace z panelu MyApps a spouštěče Office 365.

Oprávnění globálního správce jsou nutná ke skrytí všech aplikací Office 365.


## <a name="hide-an-application-from-the-end-user"></a>Skrytí aplikace před koncovým uživatelem
Pomocí následujících kroků skryjte aplikaci z panelu MyApps a spouštěče aplikací Office 365.

1.  Přihlaste se k [portálu Azure](https://portal.azure.com) jako globální správce pro váš adresář.
2.  Vyberte **Azure Active Directory**.
3.  Vyberte **podnikové aplikace**. Enterprise aplikace – otevře se okno **všechny aplikace.**
4.  V části **Typ aplikace**vyberte **Podnikové aplikace**, pokud ještě není vybraná.
5.  Vyhledejte aplikaci, kterou chcete skrýt, a klepněte na ni.  Otevře se přehled aplikace.
6.  Klikněte na **Vlastnosti**. 
7.  Otázka **Viditelné pro uživatele?** **No**
8.  Klikněte na **Uložit**.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Použití Prostředí Azure AD PowerShell ke skrytí aplikace

Chcete-li skrýt aplikaci z panelu MyApps, můžete ručně přidat značku HideApp do instančního objektu aplikace. Spusťte následující příkazy [AzureAD PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) ustavte vlastnost **Viditelné pro uživatele** aplikace na **ne.** 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Skrytí aplikací Office 365 v panelu MyApps

Pomocí následujících kroků skryjte všechny aplikace Office 365 z panelu MyApps. Aplikace jsou pořád viditelné na portálu Office 365.

1.  Přihlaste se k [portálu Azure](https://portal.azure.com) jako globální správce pro váš adresář.
2.  Vyberte **Azure Active Directory**.
3.  Vyberte **Uživatelská nastavení**.
4.  V části **Podnikové aplikace**klikněte na **Spravovat způsob, jakým koncoví uživatelé spouštějí a zobrazují své aplikace.**
5.  Pro **uživatele se můžou zobrazit jenom aplikace Office 365 na portálu Office 365**, klikněte na **Ano**.
6.  Klikněte na **Uložit**.

## <a name="next-steps"></a>Další kroky
* [Zobrazit všechny mé skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](assign-user-or-group-access-portal.md)
* [Odebrání přiřazení uživatele nebo skupiny z podnikové aplikace](remove-user-or-group-access-portal.md)
* [Změna názvu nebo loga podnikové aplikace](change-name-or-logo-portal.md)

