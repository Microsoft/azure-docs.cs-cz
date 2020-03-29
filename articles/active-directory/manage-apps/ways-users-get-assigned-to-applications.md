---
title: Jak přiřadit uživatele k aplikacím | Dokumenty společnosti Microsoft
description: Zjistěte, jak se uživatelé přiřazují k aplikaci ve vašem tenantovi.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b818fe1d8b6bbc9d2d8c5b460b4d71dccdd39366
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825977"
---
# <a name="how-to-assign-users-to-applications"></a>Jak přiřadit uživatele k aplikacím

Tento článek vám pomůže pochopit, jak se uživatelé přiřazují k aplikaci ve vašem tenantovi.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Jak se uživatelé přiřadí k aplikaci ve službě Azure AD?

Aby měl uživatel přístup k aplikaci, musí k ní být nejprve nějakým způsobem přiřazen. Přiřazení může provádět správce, obchodní delegát nebo někdy i samotný uživatel. Níže je popsáno, jak mohou být uživatelé přiřazeni k aplikacím:

1.  Správce [přiřadí uživatele](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) k aplikaci přímo

2.  Správce [přiřadí do aplikace skupinu,](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) jejíž je uživatel členem, včetně:

    * Skupina synchronizovaná z místního prostředí

    * Skupina statického zabezpečení vytvořená v cloudu

    * [Dynamická skupina zabezpečení](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) vytvořená v cloudu

    * Skupina Office 365 vytvořená v cloudu

    * Skupina [Všichni uživatelé](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)

3.  Správce povolí [samoobslužný přístup k aplikacím,](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) aby uživatel mohl přidat aplikaci pomocí funkce Přidat aplikaci pomocí **panelu** Application Access App **bez souhlasu** [firmy.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

4.  Správce povolí [samoobslužný přístup k aplikacím,](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) aby uživatelmohl přidat aplikaci pomocí funkce Přidat aplikaci pomocí panelu Application Access [Panel,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Add App** ale pouze s**předchozím schválením od vybrané sady obchodních schvalovatelů**

5.  Správce umožňuje [samoobslužné správě skupiny](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) umožnit uživateli připojit se ke skupině, ke které je aplikace přiřazena **bez souhlasu firmy.**

6.  Správce umožňuje [samoobslužné správě skupiny](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) umožnit uživateli připojit se ke skupině, ke které je aplikace přiřazena, ale pouze **s předchozím souhlasem vybrané skupiny obchodních schvalovatelů.**

7.  Správce přiřadí uživateli licenci přímo k aplikaci první strany, například [Microsoft Office 365](https://products.office.com/)

8.  Správce přiřadí licenci skupině, jejíž je uživatel členem, k aplikaci první strany, například [Microsoft Office 365.](https://products.office.com/)

9.  [Správce souhlasí s tím, aby aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) byla používána všemi uživateli, a poté se uživatel přihlásí k aplikaci.

10. Uživatel [souhlasí s aplikací](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) sám přihlášením k aplikaci

## <a name="next-steps"></a>Další kroky
[Správa aplikací pomocí služby Azure Active Directory](what-is-application-management.md)
