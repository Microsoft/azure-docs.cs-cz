---
title: Konfigurace jednotného přihlášení aplikace | Dokumenty společnosti Microsoft
description: Jak nakonfigurovat jednotné přihlašování pro vlastní aplikaci, kterou vyvíjíte a registrujete pomocí služby Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: bb77f376e22428e9259ff3efc84cf6f1cb3491fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702637"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Konfigurace jednotného přihlášení pro aplikaci

Povolení federovaného jednotného přihlašování (SSO) ve vaší aplikaci se automaticky aktivuje při federování prostřednictvím Azure AD pro OpenID Connect, SAML 2.0 nebo WS-Fed. Pokud vaši koncoví uživatelé se musí přihlásit, přestože už mají existující relaci s Azure AD, je pravděpodobné, že vaše aplikace může být nesprávně nakonfigurovaná.

* Pokud používáte ADAL/MSAL, ujistěte se, že máte **PromptBehavior** nastavena na **auto,** nikoli **vždy**.

* Pokud vytváříte mobilní aplikaci, budete možná potřebovat další konfigurace, abyste povolili zprostředkované nebo nezprostředkované automatické přihlašované osobní zabezpečení.

Pro Android, [najdete v tématu povolení cross App Přisuzuje v Androidu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

Pro iOS, [najdete v tématu povolení křížové ho správě aplikací v iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Další kroky

[Přiosít k virtuálnímu zabezpečení služby Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Povolení přiřazovacího systému aplikace Cross V Systému Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Povolení přiřazovacího a so aplikace pro křížovou aplikaci v iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integrace aplikací do AzureADu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Souhlas a povolení pro konvergované aplikace AzureAD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Přetečení zásobníku AzureAD](https://stackoverflow.com/questions/tagged/azure-active-directory)
