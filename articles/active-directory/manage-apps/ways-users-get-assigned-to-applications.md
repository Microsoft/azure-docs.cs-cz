---
title: Pochopení způsobu přiřazování uživatelů k aplikacím v Azure Active Directory
description: Seznamte se s tím, jak se uživatelé přiřadí k aplikaci, která používá Azure Active Directory pro správu identit.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 243e5ece0d0a14cb7e3ade409ee68510cef64a9c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89397096"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Pochopení způsobu přiřazování uživatelů k aplikacím v Azure Active Directory
Tento článek vám pomůže pochopit, jak se uživatelé přiřadí k aplikaci ve vašem tenantovi.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Jak se uživatelé přiřadí k aplikaci ve službě Azure AD?
Pro uživatele, kteří mají přístup k aplikaci, je nutné je nejprve přiřadit jiným způsobem. Přiřazení může provádět správce, obchodní delegát nebo občas samotný uživatel. Níže popisuje způsoby, kterými se uživatelé můžou přiřazovat k aplikacím:

*  Správce [přiřadí uživatele](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) k aplikaci přímo.
*  Správce [přiřadí skupinu](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) , které je uživatel členem aplikace, včetně:
    * Skupina, která byla synchronizovaná z místního prostředí
    * Statická skupina zabezpečení vytvořená v cloudu
    * [Dynamická skupina zabezpečení](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) vytvořená v cloudu
    * Skupina Office 365 vytvořená v cloudu
    * Skupina [Všichni uživatelé](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)
*  Správce umožňuje [Samoobslužný přístup k aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) povolit uživateli přidání aplikace pomocí funkce [Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Přidat funkci aplikace** **bez schválení firmy** .
*  Správce umožňuje [Samoobslužný přístup k aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) povolit uživateli přidání aplikace pomocí funkce [Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Přidat** aplikaci, ale jenom s tím,**že předá od vybrané sady obchodních schvalovatelů dřívější schválení** .
*  Správce umožňuje [samoobslužné správě skupin](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) dovolit uživateli připojit se ke skupině, ke které je aplikace přiřazená **bez souhlasu s obchodním schválením** .
*  Správce umožňuje [samoobslužné správě skupin](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) dovolit uživateli připojit se ke skupině, ke které je aplikace přiřazená, ale jenom **s předchozím schválením od vybrané sady obchodních schvalovatelů** .
*  Správce přiřadí licenci uživateli přímo pro aplikaci první strany, například [systém Microsoft Office 365](https://products.office.com/)
*  Správce přiřadí licenci skupině, na kterou je uživatel členem, do aplikace první strany, například [systém Microsoft Office 365](https://products.office.com/)
*  [Správce souhlasí s aplikací](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) , kterou bude používat všichni uživatelé, a pak se uživatel přihlásí k aplikaci.
* Uživatel [souhlasí s aplikací](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) , která se přihlašuje k aplikaci sami.

## <a name="next-steps"></a>Další kroky
* [Série rychlý Start při správě aplikací](view-applications-portal.md)
* [Co je správa aplikací?](what-is-application-management.md)
* [Co je jednotné přihlašování?](what-is-single-sign-on.md)
