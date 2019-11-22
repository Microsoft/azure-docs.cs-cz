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
ms.date: 11/12/2018
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e01c79c5cc9391922333af4e9a60ba44a6a6b13
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274010"
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
8.  Klikněte na možnost **Uložit**.


## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Skrýt aplikace Office 365 z panelu MyApp

Pomocí následujících kroků skryjte všechny aplikace Office 365 z panelu MyApp. Aplikace jsou pořád viditelné na portálu Office 365.

1.  Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce vašeho adresáře.
2.  Vyberte **Azure Active Directory**.
3.  Vyberte **nastavení uživatele**.
4.  V části **podnikové aplikace**klikněte na možnost **spravovat způsob, jak koncoví uživatelé spouštějí a zobrazují své aplikace.**
5.  **Uživatelé můžou na portálu office 365 zobrazit jenom aplikace Office 365**, klikněte na **Ano**.
6.  Klikněte na možnost **Uložit**.


## <a name="next-steps"></a>Další kroky
* [Zobrazit všechny moje skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](assign-user-or-group-access-portal.md)
* [Odebrání přiřazení uživatele nebo skupiny z podnikové aplikace](remove-user-or-group-access-portal.md)
* [Změna názvu nebo loga podnikové aplikace](change-name-or-logo-portal.md)

