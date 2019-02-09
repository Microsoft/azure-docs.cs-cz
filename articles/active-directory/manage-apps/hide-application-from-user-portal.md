---
title: Skrytí aplikace prostředí uživatele v Azure Active Directory | Dokumentace Microsoftu
description: Popisuje, jak skrýt aplikaci v prostředí uživatele v Azure Active Directory přístupových panelech nebo Office 365 Spouštěč oken.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: celested
ms.reviewer: kasimpso
ms.openlocfilehash: a1c3bd400e08b83b3cc8733b68f73b7c308bdc44
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964504"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Skrýt aplikace od koncových uživatelů ve službě Azure Active Directory

Pokyny, jak skrýt aplikace z panelu MyApps nebo Spouštěče aplikací Office 365 koncoví uživatelé. Skryté aplikace mít uživatelé dál oprávnění k aplikaci. 

## <a name="prerequisites"></a>Požadavky

Skrýt aplikace z panelu MyApps a spuštění služeb Office 365 jsou nutná oprávnění správce aplikace.

Chcete-li skrýt všechny aplikace Office 365 jsou požadována oprávnění globálního správce.


## <a name="hide-an-application-from-the-end-user"></a>Skrytí aplikace koncového uživatele
Skrytí aplikace MyApps panel a Spouštěče aplikací Office 365 pomocí následujících kroků.

1.  Přihlaste se k [webu Azure portal](https://portal.azure.com) jako globální správce pro svůj adresář.
2.  Vyberte **Azure Active Directory**.
3.  Vyberte **podnikové aplikace**. **Podnikové aplikace – všechny aplikace** se otevře okno.
4.  V části **typ aplikace**vyberte **podnikové aplikace**, pokud ještě není vybraná.
5.  Vyhledejte aplikaci, kterou chcete skrýt a klikněte na aplikaci.  Otevře se okno Přehled vaší aplikace.
6.  Klikněte na **Vlastnosti**. 
7.  Pro **viditelné pro uživatele?** otázku, klikněte na tlačítko **ne**.
8.  Klikněte na **Uložit**.


## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Skrýt aplikace Office 365 z panelu MyApps

Postupujte následovně Chcete-li skrýt všechny aplikace Office 365 z panelu MyApps. Aplikace se stále zobrazená na portálu Office 365.

1.  Přihlaste se k [webu Azure portal](https://portal.azure.com) jako globální správce pro svůj adresář.
2.  Vyberte **Azure Active Directory**.
3.  Vyberte **uživatelská nastavení**.
4.  V části **podnikové aplikace**, klikněte na tlačítko **spravovat jak koncoví uživatelé spouštějí a zobrazují své aplikace.**
5.  Pro **uživatelé vidí pouze aplikace Office 365 na portálu Office 365**, klikněte na tlačítko **Ano**.
6.  Klikněte na **Uložit**.


## <a name="next-steps"></a>Další postup
* [Zobrazit všechny moje skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Přiřadit uživatele nebo skupiny k podnikové aplikace](assign-user-or-group-access-portal.md)
* [Odebrání uživatele nebo skupiny přiřazení podnikové aplikace](remove-user-or-group-access-portal.md)
* [Změnit název nebo loga podnikové aplikace](change-name-or-logo-portal.md)

