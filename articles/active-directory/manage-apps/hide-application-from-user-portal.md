---
title: Skrytí aplikace z uživatelského prostředí v Azure AD
description: Postup skrytí aplikace z uživatelského prostředí v Azure Active Directory panelů pro přístup nebo na spouštěčech Office 365.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295054"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Skrýt aplikace od koncových uživatelů v Azure Active Directory

Pokyny, jak skrýt aplikace z panelu MyApp a ze Spouštěče sady Office 365 koncových uživatelů. Když je aplikace skrytá, uživatelé mají stále oprávnění k aplikaci. 

## <a name="prerequisites"></a>Požadavky

Aby bylo možné skrýt aplikaci z panelu MyApp a spouštěče sady Office 365, jsou vyžadována oprávnění správce aplikací.

Pro skrytí všech aplikací Office 365 jsou nutná oprávnění globálního správce.


## <a name="hide-an-application-from-the-end-user"></a>Skrýt aplikaci od koncového uživatele
Pomocí následujících kroků skryjte aplikaci z panelu MyApp a spouštěče aplikací Office 365.

1.  Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce vašeho adresáře.
2.  Vyberte **Azure Active Directory**.
3.  Vyberte **podnikové aplikace**. Otevře se okno **podnikové aplikace – všechny aplikace** .
4.  V části **Typ aplikace**vyberte **podnikové aplikace**, pokud ještě není vybraná.
5.  Vyhledejte aplikaci, kterou chcete skrýt, a klikněte na aplikaci.  Otevře se Přehled aplikace.
6.  Klikněte na **Vlastnosti**. 
7.  Aby se **uživatelé mohli zviditelnit?** klikněte na **ne**.
8.  Klikněte na **Uložit**.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Použití Azure AD PowerShellu ke skrytí aplikace

Chcete-li skrýt aplikaci z panelu MyApp, můžete ručně přidat značku HideApp do instančního objektu aplikace. Spusťte následující příkazy [prostředí PowerShell AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) a nastavte vlastnost **Visible pro uživatele** na hodnotu **ne**. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Skrýt aplikace Office 365 z panelu MyApp

Pomocí následujících kroků skryjte všechny aplikace Office 365 z panelu MyApp. Aplikace jsou pořád viditelné na portálu Office 365.

1.  Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce vašeho adresáře.
2.  Vyberte **Azure Active Directory**.
3.  Vyberte **Uživatelská nastavení**.
4.  V části **podnikové aplikace**klikněte na možnost **spravovat způsob, jak koncoví uživatelé spouštějí a zobrazují své aplikace.**
5.  **Uživatelé můžou na portálu office 365 zobrazit jenom aplikace Office 365**, klikněte na **Ano**.
6.  Klikněte na **Uložit**.

## <a name="next-steps"></a>Další kroky
* [Zobrazit všechny moje skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](assign-user-or-group-access-portal.md)
* [Odebrání přiřazení uživatele nebo skupiny z podnikové aplikace](remove-user-or-group-access-portal.md)
* [Změna názvu nebo loga podnikové aplikace](change-name-or-logo-portal.md)

