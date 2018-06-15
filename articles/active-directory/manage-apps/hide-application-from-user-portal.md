---
title: Skrýt aplikace z možnosti pro uživatele v Azure Active Directory | Microsoft Docs
description: Jak skrýt aplikace z možnosti pro uživatele v Azure Active Directory přístup panelů nebo Spouštěč oken Office 365.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: b60384663d79294531225612a767663e0d71723f
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303292"
---
# <a name="hide-an-application-from-users-experience-in-azure-active-directory"></a>Skrýt aplikace z možnosti pro uživatele v Azure Active Directory

Pokud máte aplikaci, která nechcete zobrazit na panelů přístup uživatelů nebo Spouštěč oken Office 365, existuje možnost Skrýt tuto dlaždici aplikace.  Následující dvě možnosti jsou k dispozici pro skrytí aplikací z Spouštěč oken aplikace uživatele.

- Skrýt aplikace třetích stran z panelů přístup uživatelé a aplikace Office 365 Spouštěč oken
- Skrýt všechny aplikace Office 365 z panelů přístupu uživatelů

Skrytím uživatelům aplikací stále mít oprávnění k aplikaci, ale nezobrazí je zobrazit na jejich Spouštěč aplikace oken. Musí mít příslušná oprávnění ke správě firemní aplikace a musí být globální správce adresáře.


## <a name="hiding-an-application-from-users-end-user-experiences"></a>Skrytí aplikace z prostředí koncového uživatele uživatele
Následujících kroků v závislosti na vaší situaci, můžete použít ke skrytí aplikace na přístupovém panelu.

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>Jak skrýt aplikace třetích stran z přístupového panelu a Spouštěč oken aplikace O365 uživatele?
Pomocí následujících kroků skrýt aplikace do přístupového panelu a Spouštěč oken aplikace Office 365 uživatele.

1.  Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu, který je globální správce adresáře.
2.  Vyberte **všechny služby**, zadejte **Azure Active Directory** v textovém poli a potom vyberte **Enter**.
3.  Na **Azure Active Directory - *directoryname***  obrazovky (to znamená, Azure AD obrazovky pro adresář spravujete), vyberte **podnikové aplikace, které**.
![Podnikové aplikace](./media/hide-application-from-user-portal/app1.png)
4.  Na **podnikové aplikace, které** obrazovku, vyberte **všechny aplikace**. Zobrazí seznam aplikací, které můžete spravovat.
5.  Na **podnikové aplikace – všechny aplikace** obrazovky, vyberte aplikaci.</br>
![Podnikové aplikace](./media/hide-application-from-user-portal/app2.png)
6.  Na ***appname*** obrazovky (to znamená, že na obrazovce s názvem vybranou aplikaci v názvu) vyberte vlastnosti.
7.  Na  ***appname* -vlastnosti** obrazovku, vyberte **Ano** pro **viditelný pro uživatele?**.
![Podnikové aplikace](./media/hide-application-from-user-portal/app3.png)
8.  Vyberte **Uložit** příkaz.

### <a name="how-do-i-hide-office-365-applications-from-users-access-panel"></a>Jak skrýt aplikací Office 365 z panel přístupu uživatele?

Pomocí následujících kroků skrýt všechny aplikace Office 365 na přístupovém panelu. Tyto aplikace se stále nebude zobrazovat na portálu Office 365.

1.  Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu, který je globální správce adresáře.
2.  Vyberte **všechny služby**, zadejte **Azure Active Directory** v textovém poli a potom vyberte **Enter**.
3.  Na **Azure Active Directory - *directoryname***  obrazovky (to znamená, Azure AD obrazovky pro adresář spravujete), vyberte **uživatelská nastavení**.
4.  Na **uživatelská nastavení** obrazovce, v části **podnikové aplikace, které** vyberte **Ano** pro **uživatelé vidí pouze aplikace Office 365 na portálu Office 365**.

![Podnikové aplikace](./media/hide-application-from-user-portal/apps4.png)

## <a name="next-steps"></a>Další postup
* [Zobrazení všech Moje skupin](../active-directory-groups-view-azure-portal.md)
* [Přiřazení uživatele nebo skupiny do aplikace enterprise](assign-user-or-group-access-portal.md)
* [Odebrat uživatele nebo skupinu přiřazení z podnikové aplikace.](remove-user-or-group-access-portal.md)
* [Změna názvu nebo logo aplikace enterprise](change-name-or-logo-portal.md)

