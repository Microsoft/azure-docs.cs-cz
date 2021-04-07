---
title: Skrytí podnikové aplikace z uživatelského prostředí v Azure AD
description: Jak skrýt podnikovou aplikaci před uživatelským prostředím v Azure Active Directory panelů přístupu nebo spouštěčích Microsoft 365.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8469b48b92f3f9a645a0c05441e6c1943b02e16f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258876"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Skrýt podnikové aplikace od koncových uživatelů v Azure Active Directory

Pokyny, jak skrýt aplikace z panelu MyApp a Microsoft 365 spouštěče koncových uživatelů. Když je aplikace skrytá, uživatelé mají stále oprávnění k aplikaci. 

## <a name="prerequisites"></a>Požadavky

Pro skrytí aplikace z panelu MyApp a Microsoft 365 spouštěče se vyžadují oprávnění správce aplikace.

Pro skrytí všech Microsoft 365 aplikací jsou vyžadována oprávnění globálního správce.


## <a name="hide-an-application-from-the-end-user"></a>Skrýt aplikaci od koncového uživatele
Pomocí následujících kroků skryjte aplikaci z panelu MyApp a Microsoft 365 spouštěče aplikací.

1.  Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce vašeho adresáře.
2.  Vyberte **Azure Active Directory**.
3.  Vyberte **podnikové aplikace**. Otevře se okno **podnikové aplikace – všechny aplikace** .
4.  V části **Typ aplikace** vyberte **podnikové aplikace**, pokud ještě není vybraná.
5.  Vyhledejte aplikaci, kterou chcete skrýt, a klikněte na aplikaci.  Otevře se Přehled aplikace.
6.  Klikněte na **Vlastnosti**. 
7.  Aby se **uživatelé mohli zviditelnit?** klikněte na **ne**.
8.  Klikněte na **Uložit**.

> [!NOTE]
> Tyto pokyny platí pouze pro podnikové aplikace.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Použití Azure AD PowerShellu ke skrytí aplikace

Chcete-li skrýt aplikaci z panelu MyApp, můžete ručně přidat značku HideApp do instančního objektu aplikace. Spusťte následující příkazy [prostředí PowerShell AzureAD](/powershell/module/azuread/#service_principals) a nastavte vlastnost **Visible pro uživatele** na hodnotu **ne**. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-myapps-panel"></a>Skrýt Microsoft 365 aplikace z panelu MyApp

Pomocí následujících kroků skryjte všechny Microsoft 365 aplikace z panelu MyApp. Aplikace jsou pořád viditelné na portálu Office 365.

1.  Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce vašeho adresáře.
2.  Vyberte **Azure Active Directory**.
3.  Vyberte **Uživatelé**.
4.  Vyberte **Uživatelská nastavení**.
5.  V části **podnikové aplikace** klikněte na možnost **spravovat způsob, jak koncoví uživatelé spouštějí a zobrazují své aplikace.**
6.  **Uživatelé můžou na portálu office 365 zobrazit jenom aplikace Office 365**, klikněte na **Ano**.
7.  Klikněte na **Uložit**.

## <a name="next-steps"></a>Další kroky
* [Zobrazit všechny moje skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](assign-user-or-group-access-portal.md)
* [Odebrání přiřazení uživatele nebo skupiny z podnikové aplikace](./assign-user-or-group-access-portal.md)
* [Změna názvu nebo loga podnikové aplikace](./add-application-portal-configure.md)
