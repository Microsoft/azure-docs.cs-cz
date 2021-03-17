---
title: Účty a přihlašovací údaje pro sdílení – Azure Active Directory | Microsoft Docs
description: Popisuje, jak Azure Active Directory umožňuje organizacím bezpečně sdílet účty pro místní aplikace a cloudové služby pro spotřebitele.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/03/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb088d56879ebdf5d439c913ac47a701db5c4a60
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576243"
---
# <a name="sharing-accounts-with-azure-ad"></a>Sdílení účtů s Azure AD

## <a name="overview"></a>Přehled

Někdy organizace potřebují použít jedno uživatelské jméno a heslo pro více lidí, což se obvykle stává ve dvou případech:

* Při přístupu k aplikacím, které vyžadují jedinečné přihlášení a heslo pro každého uživatele, bez ohledu na to, jestli jsou místní aplikace nebo spotřebitelské cloudové služby (například firemní účty sociálních médií).
* Při vytváření prostředí s více uživateli. Je možné, že máte jeden místní účet, který má zvýšená oprávnění, a používá se pro základní aktivity nastavení, správy a obnovení. Například místní účet "globální správce" pro Microsoft 365 nebo účet root v Salesforce.

Tradičně jsou tyto účty sdíleny distribucí přihlašovacích údajů (uživatelské jméno a heslo) pravým jednotlivcům nebo jejich uložením do sdíleného umístění, kde k nim může přistupovat více důvěryhodných agentů.

Tradiční model sdílení má několik nevýhod:

* Povolení přístupu k novým aplikacím vyžaduje distribuci přihlašovacích údajů všem, kteří potřebují přístup.
* Každá sdílená aplikace může vyžadovat vlastní jedinečnou sadu sdílených přihlašovacích údajů, aby si uživatelé museli pamatovat několik sad přihlašovacích údajů. Když si uživatelé musí pamatovat spoustu přihlašovacích údajů, riziko se zvýší, že se budou rozmyslet na rizikové postupy. (například zápis hesel do hesla).
* Nemůžete říct, kdo má přístup k aplikaci.
* Nemůžete zjistit, kdo získal *k aplikaci pøístup* .
* Pokud chcete odebrat přístup k aplikaci, musíte aktualizovat přihlašovací údaje a znovu je distribuovat všem, kteří potřebují k této aplikaci přístup.

## <a name="azure-active-directory-account-sharing"></a>Sdílení účtu Azure Active Directory

Azure AD poskytuje nový přístup k používání sdílených účtů, které eliminují tyto nevýhody.

Správce Azure AD nakonfiguruje, které aplikace může uživatel přistupovat, pomocí přístupového panelu a výběrem typu jednotného přihlašování, které se pro tuto aplikaci nejlépe hodí. Jeden z těchto typů: *jednotné přihlašování založené na heslech* umožňuje službě Azure AD fungovat jako typ Broker během procesu přihlašování pro tuto aplikaci.

Uživatelé se přihlásí pomocí účtu organizace. Tento účet je stejný jako ten, který pravidelně používá pro přístup ke svému desktopu nebo e-mailu. Můžou zjišťovat a přistupovat jenom k aplikacím, ke kterým jsou přiřazené. U sdílených účtů může tento seznam aplikací obsahovat libovolný počet sdílených přihlašovacích údajů. Koncový uživatel nemusí pamatovat ani zapisovat různé účty, které můžou používat.

Sdílené účty nezvyšují jenom zlepšení a zlepšují použitelnost, zvyšují taky vaše zabezpečení. Uživatelé s oprávněními k použití přihlašovacích údajů nevidí sdílené heslo, ale místo toho získají oprávnění používat heslo jako součást procesu orchestrace ověřování. Některé aplikace jednotného přihlašování k heslům navíc umožňují použití Azure AD k pravidelné výměně hesel (aktualizace). Systém používá velká, složitá hesla, která zvyšují zabezpečení účtů. Správce může snadno udělit nebo odvolat přístup k aplikaci, ví, kdo má přístup k účtu a kdo k němu získal přístup v minulosti.

Azure AD podporuje sdílené účty pro všechny sady Enterprise Mobility Suite (EMS) nebo Azure AD Premium licenčního plánu napříč všemi typy aplikací pro jednotné přihlašování s hesly. V galerii aplikací můžete sdílet účty pro všechny tisíce předem integrovaných aplikací a můžou přidat vlastní aplikaci ověřování hesla s [vlastními aplikacemi jednotného přihlašování](../manage-apps/what-is-single-sign-on.md).

Funkce Azure AD, které umožňují sdílení účtů, zahrnují:

* [Jednotné přihlašování k heslu](../manage-apps/sso-options.md#password-based-sso)
* Agent jednotného přihlašování k heslům
* [Přiřazení skupiny](groups-self-service-management.md)
* Vlastní aplikace pro hesla
* [Řídicí panel a sestavy využití aplikací](../authentication/howto-sspr-reporting.md)
* Portál pro přístup koncových uživatelů
* [Proxy aplikace](../manage-apps/application-proxy.md)
* [Tržiště služby Active Directory](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory)

## <a name="sharing-an-account"></a>Sdílení účtu

Pokud chcete ke sdílení účtu použít Azure AD, musíte:

* Přidání [Galerie](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) aplikačních aplikací nebo [vlastní aplikace](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)
* Konfigurace aplikace pro jednotné Sign-On hesla (SSO)
* Použijte [přiřazení na základě skupin](groups-saasapps.md) a vyberte možnost zadání sdíleného přihlašovacího údaje.

Sdílený účet můžete také lépe zabezpečit pomocí Multi-Factor Authentication (MFA) (Další informace o [zabezpečení aplikací pomocí služby Azure AD](../authentication/concept-mfa-howitworks.md)) a můžete delegovat schopnost spravovat, kdo má k aplikaci přístup pomocí [samoobslužné správy skupin Azure AD](groups-self-service-management.md) .

## <a name="next-steps"></a>Další kroky

* [Správa aplikací ve službě Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Ochrana aplikací pomocí podmíněného přístupu](../../active-directory-b2c/overview.md)
* [Samoobslužná správa skupin/SSAA](groups-self-service-management.md)