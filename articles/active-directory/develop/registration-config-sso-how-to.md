---
title: Jak konfigurovat novou aplikaci s více tenanty | Dokumentace Microsoftu
description: Jak nakonfigurovat jednotné přihlašování pro vlastní aplikace, vývoji a registraci v Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: cbd0849399b2f1b1bb4df240b6d696266bbd84cc
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52423394"
---
# <a name="how-to-configure-a-new-multi-tenant-application"></a>Jak konfigurovat novou aplikaci s více tenanty

Povolení federovaného jednotného přihlašování (SSO) v aplikaci je automaticky povolen při federaci pro OpenID Connect, SAML 2.0 nebo WS-Fed prostřednictvím služby Azure AD. Pokud vaši koncoví uživatelé se museli přihlásit bez ohledu na již má existující relaci s Azure AD, je pravděpodobné, že vaše aplikace může být chybně nakonfigurovaná.

* Pokud používáte knihovnu ADAL/MSAL, ujistěte se, že máte **PromptBehavior** nastavena na **automaticky** spíše než **vždy**.

* Pokud vytváříte mobilní aplikace, budete potřebovat další konfigurace, které umožňují zprostředkované nebo zprostředkované jednotné přihlašování.

Pro Android, najdete v článku [povolení pro různé aplikace jednotné přihlašování v Androidu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

Pro iOS, najdete v článku [povolení pro různé aplikace jednotné přihlašování v iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Další postup

[Jednotné přihlašování služby Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Povolení křížové jednotného přihlašování aplikace v Androidu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Povolení pro různé aplikace jednotné přihlašování v iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integrace aplikací do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Videí k Azure AD v2.0 a vyjádření souhlasu konvergované aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[StackOverflow pro Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
