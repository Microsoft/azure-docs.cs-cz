---
title: Jak konfigurovat novou aplikaci s více tenanty | Dokumentace Microsoftu
description: Jak nakonfigurovat jednotné přihlašování pro vlastní aplikace, vývoji a registraci v Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 97c698fa99c22b689b5e10b91c87345f4eadd647
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365222"
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

[StackOverflow pro Azure AD](http://stackoverflow.com/questions/tagged/azure-active-directory)
