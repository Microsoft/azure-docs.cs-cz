---
title: Sdílení účtů a přihlašovacích údajů – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Popisuje, jak Služba Azure Active Directory umožňuje organizacím bezpečně sdílet účty pro místní aplikace a cloudové služby pro spotřebitele.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 987c3ee7c90eb0bb793b96eb2771efbb258f16a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565498"
---
# <a name="sharing-accounts-with-azure-ad"></a>Sdílení účtů pomocí Azure AD

## <a name="overview"></a>Přehled

Někdy organizace potřebují používat jedno uživatelské jméno a heslo pro více osob, což se obvykle děje ve dvou případech:

* Při přístupu k aplikacím, které vyžadují jedinečné přihlášení a heslo pro každého uživatele, ať už místní aplikace nebo cloudové služby spotřebitelů (například firemní účty sociálních médií).
* Při vytváření prostředí pro více uživatelů. Můžete mít jeden místní účet, který má zvýšená oprávnění a slouží k základním ustavičům, správě a obnovení. Například místní účet globálního správce pro Office 365 nebo kořenový účet v Salesforce.

Tyto účty jsou tradičně sdíleny distribucí přihlašovacích údajů (uživatelského jména a hesla) správným osobám nebo jejich uložením ve sdíleném umístění, kde k nim může přistupovat více důvěryhodných agentů.

Tradiční model sdílení má několik nevýhod:

* Povolení přístupu k novým aplikacím vyžaduje distribuci přihlašovacích údajů všem uživatelům, kteří potřebují přístup.
* Každá sdílená aplikace může vyžadovat vlastní jedinečnou sadu sdílených přihlašovacích údajů, která vyžaduje, aby si uživatelé pamatovali více sad pověření. Když si uživatelé musí pamatovat mnoho přihlašovacích údajů, zvyšuje se riziko, že se uchylují k rizikovým praktikám. (například zapisování hesel).
* Nemůžete říct, kdo má přístup k aplikaci.
* Nemůžete říct, kdo *má přístup k aplikaci.*
* Pokud chcete odebrat přístup k aplikaci, budete muset aktualizovat pověření a redistribuovat je všem uživatelům, kteří potřebují přístup k této aplikaci.

## <a name="azure-active-directory-account-sharing"></a>Sdílení účtů služby Azure Active Directory

Azure AD poskytuje nový přístup k používání sdílených účtů, který eliminuje tyto nevýhody.

Správce Azure AD konfiguruje, ke kterým aplikacím má uživatel přístup pomocí přístupového panelu a výběru typu jednotného přihlašování, které je pro tuto aplikaci nejvhodnější. Jeden z těchto typů, *na základě hesla jednotného přihlášení*, umožňuje Azure AD fungovat jako druh "zprostředkovatele" během procesu přihlašování pro tuto aplikaci.

Uživatelé se přihlašují jednou pomocí svého účtu organizace. Tento účet je stejný, který pravidelně používají pro přístup ke své ploše nebo e-mailu. Mohou zjistit a přistupovat pouze k aplikacím, které jsou přiřazeny. U sdílených účtů může tento seznam aplikací obsahovat libovolný počet sdílených pověření. Koncový uživatel si nemusí pamatovat ani zapisovat různé účty, které může používat.

Sdílené účty nejen zvyšují dohled a zlepšují použitelnost, ale také zvyšují vaši bezpečnost. Uživatelé s oprávněními k použití pověření nezobrazují sdílené heslo, ale spíše získat oprávnění k použití hesla jako součást řízeného toku ověřování. Dále některé aplikace s přihlašování hesla vám možnost pomocí Azure AD pravidelně rollover (aktualizace) hesla. Systém používá velká, složitá hesla, což zvyšuje zabezpečení účtu. Správce může snadno udělit nebo odvolat přístup k aplikaci, ví, kdo má přístup k účtu a kdo k němu v minulosti přistupoval.

Azure AD podporuje sdílené účty pro všechny Enterprise Mobility Suite (EMS) nebo Azure AD Premium licenční plán, napříč všemi typy hesel jednotného přihlášení aplikací. Můžete sdílet účty pro některé z tisíců předintegrovaných aplikací v galerii aplikací a můžete přidat vlastní heslo ověřování aplikace s [vlastní aplikace přihlašování .](../manage-apps/configure-single-sign-on-non-gallery-applications.md)

Mezi funkce Azure AD, které umožňují sdílení účtů, patří:

* [Jednotné přihlašování heslem](../manage-apps/what-is-single-sign-on.md#password-based-sso)
* Agent jednotného přihlašování k heslu
* [Přiřazení skupiny](groups-self-service-management.md)
* Aplikace s vlastním heslem
* [Řídicí panel/sestavy využití aplikací](../active-directory-passwords-get-insights.md)
* Portály pro přístup koncových uživatelů
* [Proxy aplikace](../manage-apps/application-proxy.md)
* [Tržiště služby Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Sdílení účtu

Pokud chcete ke sdílení účtu použít Azure AD, musíte:

* Přidání [galerie aplikací](https://azure.microsoft.com/marketplace/active-directory/) nebo [vlastní aplikace](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)
* Konfigurace aplikace pro jednotné přihlašování pomocí hesla (SSO)
* Použití [přiřazení založeného na skupině](groups-saasapps.md) a výběr možnosti pro zadání sdíleného pověření

Můžete také zajistit zabezpečení sdíleného účtu pomocí vícefaktorového ověřování (MFA) (další informace o [zabezpečení aplikací pomocí Azure AD)](../authentication/concept-mfa-whichversion.md)a můžete delegovat možnost spravovat, kdo má přístup k aplikaci pomocí [samoobslužné](groups-self-service-management.md) správy skupin Azure AD.

## <a name="next-steps"></a>Další kroky

* [Správa aplikací ve službě Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Ochrana aplikací pomocí podmíněného přístupu](../active-directory-conditional-access-azure-portal.md)
* [Samoobslužné řízení skupin/SSAA](groups-self-service-management.md)
