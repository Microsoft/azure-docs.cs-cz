---
title: Sdílení účtů a přihlašovací údaje – Azure Active Directory | Dokumentace Microsoftu
description: Popisuje, jak Azure Active Directory umožňuje organizacím bezpečně sdílet účty pro místní aplikace i spotřebitelské cloudové služby.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 5e9e5844d80e9f3fce0edb90dae210d3e16fee61
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55509486"
---
# <a name="sharing-accounts-with-azure-ad"></a>Sdílení účtů pomocí Azure AD
## <a name="overview"></a>Přehled
Někdy organizace budou muset pomocí jednoho uživatelského jména a hesla pro více osob, které obvykle dojde ve dvou případech:

* Při přístupu k aplikací, které vyžadují jedinečné přihlášení a hesla pro každého uživatele, zda místní aplikace nebo příjemce cloudové služby (například podniková sociální účty).
* Při vytváření prostředí pro více uživatelů. Může mít jeden místní účet, který se zvýšenými oprávněními a slouží k základní instalaci, správu a obnovení aktivity. Například místní "globální správce" účtu Office 365 nebo pomocí kořenového účtu v Salesforce.

Tyto účty jsou tradičně sdílí distribuci pověření (uživatelské jméno a heslo) vpravo jednotlivcům nebo jejich ukládáním do sdíleného umístění, kde více důvěryhodných agentů k nim přistupovat.

Tradiční sdílení model obsahuje několik nevýhody:

* Povolení přístupu k nové aplikace, musíte distribuovat přihlašovací údaje pro všechny uživatele, které potřebuje přístup.
* Každé sdílené aplikace může vyžadovat, aby svou vlastní jedinečnou sadu sdílené přihlašovací údaje by uživatelé museli pamatovat více sad přihlašovacích údajů. Uživatelé, kteří mají zapamatovat si přihlašovací údaje mnoho, zvyšuje riziko, se uchýlíte k rizikové postupy. (například zápis dolů hesla).
* Nelze zjistit, kdo má přístup k aplikaci.
* Nelze zjistit, kdo má *přistupovat* aplikace.
* Pokud chcete odebrat přístup k aplikaci, budete muset aktualizovat přihlašovací údaje a jejich distribuci pro všechny uživatele, který potřebuje přístup k dané aplikaci.

## <a name="azure-active-directory-account-sharing"></a>Sdílení účtů v Azure Active Directory
Azure AD nabízí nový přístup k použití sdílené účty, které předchází tyto nevýhody.

Správce Azure AD nakonfiguruje vhodné aplikace, které má uživatel přístup pomocí na přístupovém panelu a výběrem typu nejlepší jednotné přihlašování pro tuto aplikaci. Jeden z těchto typů *založené na heslech jednotného přihlašování*, umožňuje Azure AD fungovat jako typ "zprostředkovatele" během procesu přihlašování pro tuto aplikaci.

Uživatelé přihlásit jednou pro účet své organizace. Tento účet je stejný jako ten, který pravidelně používají pro přístup k ploše nebo v e-mailu. Mohou zjišťovat a přístup pouze aplikace, které jsou přiřazeny. S sdílené účty tento seznam aplikací může obsahovat libovolný počet sdílených přihlašovacích údajů. Koncový uživatel nemusí si zapamatujte nebo poznamenejte různé účty, které by mohly používat.

Sdílené účty nejen zvýšit dohledu a zlepšit použitelnost, jsou také posílit zabezpečení. Uživatelé s oprávněními k použití pověření nevidíte sdílené heslo, ale spíše získání oprávnění používat heslo jako součást tok, který iniciovat organizovaně, což ověřování. Dále některé heslem jednotného přihlašování aplikace poskytují možnost používat Azure AD, aby pravidelně hesla výměny (update). Systém používá velkých a složitých hesel, která zvyšuje zabezpečení účtu. Správce může snadno udělit nebo odvolat přístup k aplikaci, která ví, kdo má přístup k účtu, a kdo použil v minulosti.

Azure AD podporuje sdílené účty u Basic, Premium nebo Enterprise Mobility Suite (EMS) uživatele, s licencí napříč všemi typy heslo jednotné přihlašování aplikací. Můžete sdílet účty pro některý z tisíce předem integrovaných aplikací v galerii aplikací a můžete přidat vlastní ověření hesla aplikací s [vlastní jednotného přihlašování aplikace](../manage-apps/configure-single-sign-on-portal.md).

Azure AD k funkcím, které umožňují sdílení účet patří:

* [Heslo jednotného přihlašování](../manage-apps/what-is-single-sign-on.md#password-based-sso)
* Heslo jednotné přihlašování agenta
* [Přiřazení skupiny](groups-self-service-management.md)
* Aplikace vlastního hesla
* [Sestavy řídicího panelu využití aplikace](../active-directory-passwords-get-insights.md)
* Portálech pro koncové uživatele přístup
* [Proxy aplikací](../manage-apps/application-proxy.md)
* [Tržiště Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Účet pro sdílení obsahu
Používání Azure AD pro sdílet účet, budete muset:

* Přidat aplikaci [Galerie aplikací](https://azure.microsoft.com/marketplace/active-directory/) nebo [vlastní aplikace](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)
* Konfigurace aplikace pro heslo jednotné přihlašování (SSO)
* Použití [přiřazování na základě skupiny](groups-saasapps.md) a vyberte možnost a zadejte sdílené přihlašovací údaje
* Volitelné: v některých aplikacích, jako je Facebook, Twitter a LinkedIn, můžete povolit možnost [automatizované převrácení hesla Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/)

Můžete také vytvořit sdílené účet lépe zabezpečit pomocí ověřování služby Multi-Factor Authentication (MFA) (Další informace o [zabezpečení aplikací s Azure AD](../authentication/concept-mfa-whichversion.md)) a schopnost spravovat, kdo má přístup k aplikaci pomocí můžetedelegovat[ Azure AD samoobslužný](groups-self-service-management.md) správu skupin.

## <a name="related-articles"></a>Související články
* [Správa aplikací ve službě Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Ochrana aplikací s podmíněným přístupem](../active-directory-conditional-access-azure-portal.md)
* [Správa/SSAA samoobslužné skupiny](groups-self-service-management.md)
