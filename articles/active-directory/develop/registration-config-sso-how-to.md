---
title: Jak nakonfigurovat jednotné přihlašování | Dokumentace Microsoftu
description: Jak nakonfigurovat jednotné přihlašování pro vlastní aplikace, vývoji a registraci v Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: d151d4d7f32c479f2cfb4d71a8ed667f42e03b49
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465674"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Jak nakonfigurovat jednotné přihlašování pro aplikaci

Povolení federovaného jednotného přihlašování (SSO) v aplikaci je automaticky povolen při federaci pro OpenID Connect, SAML 2.0 nebo WS-Fed prostřednictvím služby Azure AD. Pokud vaši koncoví uživatelé se museli přihlásit bez ohledu na již má existující relaci s Azure AD, je pravděpodobné, že vaše aplikace může být chybně nakonfigurovaná.

* Pokud používáte knihovnu ADAL/MSAL, ujistěte se, že máte **PromptBehavior** nastavena na **automaticky** spíše než **vždy**.

* Pokud vytváříte mobilní aplikace, budete potřebovat další konfigurace, které umožňují zprostředkované nebo zprostředkované jednotné přihlašování.

Pro Android, najdete v článku [povolení pro různé aplikace jednotné přihlašování v Androidu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

Pro iOS, najdete v článku [povolení pro různé aplikace jednotné přihlašování v iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Další postup

[Azure AD SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Povolení křížové jednotného přihlašování aplikace v Androidu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Povolení pro různé aplikace jednotné přihlašování v iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integrace aplikací do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Videí k Azure AD v2.0 a vyjádření souhlasu konvergované aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[StackOverflow pro Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
