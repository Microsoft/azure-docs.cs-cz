---
title: Přiřazení uživatelů k aplikacím | Microsoft Docs
description: Vysvětlení způsobu přiřazení uživatelů k aplikaci ve vašem tenantovi
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
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "65825977"
---
# <a name="how-to-assign-users-to-applications"></a>Přiřazení uživatelů k aplikacím

Tento článek vám pomůže pochopit, jak se uživatelé přiřadí k aplikaci ve vašem tenantovi.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Jak se uživatelé přiřadí k aplikaci ve službě Azure AD?

Pro uživatele, kteří mají přístup k aplikaci, je nutné je nejprve přiřadit jiným způsobem. Přiřazení může provádět správce, obchodní delegát nebo občas samotný uživatel. Níže popisuje způsoby, kterými se uživatelé můžou přiřazovat k aplikacím:

1.  Správce [přiřadí uživatele](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) k aplikaci přímo.

2.  Správce [přiřadí skupinu](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) , které je uživatel členem aplikace, včetně:

    * Skupina, která byla synchronizovaná z místního prostředí

    * Statická skupina zabezpečení vytvořená v cloudu

    * [Dynamická skupina zabezpečení](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) vytvořená v cloudu

    * Skupina Office 365 vytvořená v cloudu

    * Skupina [Všichni uživatelé](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)

3.  Správce umožňuje [Samoobslužný přístup k aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) povolit **uživatelům přidání aplikace** pomocí [přístupového panelu aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **bez schválení firmy** .

4.  Správce umožňuje [Samoobslužný přístup k aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) povolit uživateli přidání aplikace pomocí [přístupového panelu aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Přidat funkci aplikace** , ale jenom s tím,**že předá od vybrané sady obchodních schvalovatelů předchozí schválení** .

5.  Správce umožňuje [samoobslužné správě skupin](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) dovolit uživateli připojit se ke skupině, ke které je aplikace přiřazená **bez souhlasu s obchodním schválením** .

6.  Správce umožňuje [samoobslužné správě skupin](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) dovolit uživateli připojit se ke skupině, ke které je aplikace přiřazená, ale jenom **s předchozím schválením od vybrané sady obchodních schvalovatelů** .

7.  Správce přiřadí licenci uživateli přímo pro aplikaci první strany, například [systém Microsoft Office 365](https://products.office.com/)

8.  Správce přiřadí licenci skupině, na kterou je uživatel členem, do aplikace první strany, například [systém Microsoft Office 365](https://products.office.com/)

9.  [Správce souhlasí s aplikací](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) , kterou bude používat všichni uživatelé, a pak se uživatel přihlásí k aplikaci.

10. Uživatel [souhlasí s aplikací](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) , která se přihlašuje k aplikaci sami.

## <a name="next-steps"></a>Další kroky
[Správa aplikací pomocí Azure Active Directory](what-is-application-management.md)
