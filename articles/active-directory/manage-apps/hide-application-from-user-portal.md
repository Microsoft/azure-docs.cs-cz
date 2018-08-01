---
title: Skrytí aplikace prostředí uživatele v Azure Active Directory | Dokumentace Microsoftu
description: Popisuje, jak skrýt aplikaci v prostředí uživatele v Azure Active Directory přístupových panelech nebo Office 365 Spouštěč oken.
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
ms.topic: conceptual
ms.date: 01/04/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 55f80396df4cbfe7d0a16a6a5066b68aadc0bdd3
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39369342"
---
# <a name="hide-an-application-from-users-experience-in-azure-active-directory"></a>Skrytí aplikace prostředí uživatele v Azure Active Directory

Pokud máte aplikaci, která nechcete zobrazit na přístupových panelech nebo Office 365 Spouštěč oken uživatelů, existují možnosti Skrýt tuto dlaždici aplikace.  Následující dvě možnosti jsou k dispozici pro skrytí aplikací z Spouštěč oken aplikace uživatele.

- Skrytí aplikace třetí strany z přístupových panelech uživatelů a Spouštěč oken aplikace Office 365
- Skrýt všechny aplikace Office 365 z přístupových panelech uživatelů

Tím, že skryjete uživatelé aplikace stále máte oprávnění k aplikaci, ale neuvidíte na jejich Spouštěč oken aplikace zobrazí. Musí mít příslušná oprávnění ke správě podnikové aplikace a musíte být globálním správcem adresáře.


## <a name="hiding-an-application-from-users-end-user-experiences"></a>Skrytí aplikace z prostředí koncového uživatele pro uživatele
Následující kroky, v závislosti na vaší situaci můžete skrýt aplikace na přístupovém panelu.

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>Jak se skrytí aplikace třetí strany z přístupového panelu a Spouštěč oken aplikace O365 uživatele?
Následujícím postupem skrytí aplikace uživatele přístupovém panelu a Spouštěč oken aplikace Office 365.

1.  Přihlaste se k portálu [Azure Portal](https://portal.azure.com) prostřednictvím účtu, který má k adresáři oprávnění globálního správce.
2.  Vyberte **všechny služby**, zadejte **Azure Active Directory** v textovém poli a pak vyberte **Enter**.
3.  Na **Azure Active Directory – *NazevAdresare***  obrazovky (to znamená, Azure AD obrazovky pro directory spravujete), vyberte **podnikové aplikace**.
![Podnikové aplikace](./media/hide-application-from-user-portal/app1.png)
4.  Na **podnikové aplikace** obrazovky, vyberte **všechny aplikace**. Zobrazí seznam aplikací, které můžete spravovat.
5.  Na **podnikové aplikace – všechny aplikace** obrazovky, vyberte aplikaci.</br>
![Podnikové aplikace](./media/hide-application-from-user-portal/app2.png)
6.  Na ***appname*** obrazovky (to znamená, obrazovky s názvem vybranou aplikaci v názvu), vyberte vlastnosti.
7.  Na  ***appname* – vlastnosti** obrazovky, vyberte **Ano** pro **viditelné pro uživatele?**.
![Podnikové aplikace](./media/hide-application-from-user-portal/app3.png)
8.  Vyberte **Uložit** příkazu.

### <a name="how-do-i-hide-office-365-applications-from-users-access-panel"></a>Jak se skrýt aplikací Office 365 z přístupového panelu uživatele?

Chcete-li skrýt všechny aplikace Office 365 na přístupovém panelu, postupujte následovně. Tyto aplikace budou se dál zobrazovat na portálu Office 365.

1.  Přihlaste se k portálu [Azure Portal](https://portal.azure.com) prostřednictvím účtu, který má k adresáři oprávnění globálního správce.
2.  Vyberte **všechny služby**, zadejte **Azure Active Directory** v textovém poli a pak vyberte **Enter**.
3.  Na **Azure Active Directory – *NazevAdresare***  obrazovky (to znamená, Azure AD obrazovky pro directory spravujete), vyberte **uživatelská nastavení**.
4.  Na **uživatelská nastavení** obrazovky, v části **podnikové aplikace** vyberte **Ano** pro **uživatelé vidí pouze aplikace Office 365 na portálu Office 365**.

![Podnikové aplikace](./media/hide-application-from-user-portal/apps4.png)

## <a name="next-steps"></a>Další postup
* [Zobrazit všechny moje skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Přiřadit uživatele nebo skupiny k podnikové aplikace](assign-user-or-group-access-portal.md)
* [Odebrání uživatele nebo skupiny přiřazení podnikové aplikace](remove-user-or-group-access-portal.md)
* [Změnit název nebo loga podnikové aplikace](change-name-or-logo-portal.md)

