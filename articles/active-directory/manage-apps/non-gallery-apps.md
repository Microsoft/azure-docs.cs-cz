---
title: Používání Azure AD pro aplikace, které nejsou uvedené v galerii aplikací
description: Naučte se integrovat aplikace, které nejsou uvedené v galerii Azure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/27/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 3483701d34b8fef5b40a411e22b33a73db2de44b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658736"
---
# <a name="using-azure-ad-for-applications-not-listed-in-the-app-gallery"></a>Používání Azure AD pro aplikace, které nejsou uvedené v galerii aplikací

V rychlém startu [Přidat aplikaci](add-application-portal.md) se dozvíte, jak přidat aplikaci do tenanta služby Azure AD.

Kromě možností v [galerii aplikací Azure AD](../saas-apps/tutorial-list.md)máte možnost Přidat **aplikaci mimo galerii**. 

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Možnosti pro aplikace, které nejsou uvedené v galerii Azure AD

Můžete přidat libovolnou aplikaci, která již existuje ve vaší organizaci, nebo jakoukoli aplikaci třetí strany od dodavatele, který ještě není součástí Galerie Azure AD. V závislosti na vaší [licenční smlouvě](https://azure.microsoft.com/pricing/details/active-directory/)jsou k dispozici tyto možnosti:

- Samoobslužná integrace všech aplikací, které podporují zprostředkovatele identity [Security Assertion Markup Language (SAML) 2,0](https://wikipedia.org/wiki/SAML_2.0) (SP-inicied nebo IDP)
- Samoobslužná integrace jakékoli webové aplikace, která má přihlašovací stránku založenou na jazyce HTML pomocí [jednotného přihlašování založeného na heslech](sso-options.md#password-based-sso)
- Samoobslužné připojení aplikací, které používají [systém pro protokol SCIM (Domain Identity Management) pro zřizování uživatelů](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Možnost Přidat odkazy na libovolnou aplikaci ve [Spouštěči aplikací Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) nebo ve [složkách moje aplikace](sso-options.md#linked-sign-on)

Pokud hledáte pokyny pro vývojáře, jak integrovat vlastní aplikace do služby Azure AD, přečtěte si téma [scénáře ověřování pro Azure AD](../develop/authentication-vs-authorization.md). Když vyvíjíte aplikaci, která používá moderní protokol, jako je [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) k ověřování uživatelů, můžete ji zaregistrovat na platformě Microsoft identity pomocí prostředí [Registrace aplikací](../develop/quickstart-register-app.md) v Azure Portal.

## <a name="next-steps"></a>Další kroky

- [Série rychlý Start při správě aplikací](view-applications-portal.md)