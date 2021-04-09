---
title: Konfigurace jednotného přihlašování aplikací
description: Jak nakonfigurovat jednotné přihlašování pro vlastní aplikaci, kterou vyvíjíte a zaregistrujete ve službě Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 60a07145e38c19f577ad9864efb599e7b244aa83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653707"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Jak nakonfigurovat jednotné přihlašování pro aplikaci

Povolení federovaného jednotného přihlašování (SSO) ve vaší aplikaci se automaticky povolí při federování prostřednictvím služby Azure AD pro OpenID Connect, SAML 2,0 nebo WS. Pokud se koncoví uživatelé musí přihlašovat, i když už mají existující relaci se službou Azure AD, je možné, že vaše aplikace bude možná nesprávně nakonfigurovaná.

* Pokud používáte ADAL/MSAL, ujistěte se, že máte **PromptBehavior** nastavenou na **Automatické** místo **vždy**.

* Pokud vytváříte mobilní aplikaci, možná budete potřebovat další konfigurace, aby bylo možné povolit zprostředkované nebo nezprostředkované jednotné přihlašování.

Informace pro Android najdete v tématu [povolení jednotného přihlašování mezi aplikacemi v Androidu](../azuread-dev/howto-v1-enable-sso-android.md).<br>

Informace o iOS najdete [v tématu povolení jednotného přihlašování mezi aplikacemi v iOS](../azuread-dev/howto-v1-enable-sso-ios.md).

## <a name="next-steps"></a>Další kroky

[Jednotné přihlašování Azure AD](../manage-apps/what-is-single-sign-on.md)<br>

[Povolení jednotného přihlašování mezi aplikacemi v Androidu](../azuread-dev/howto-v1-enable-sso-android.md)<br>

[Povolení jednotného přihlašování mezi aplikacemi v iOS](../azuread-dev/howto-v1-enable-sso-ios.md)<br>

[Integrace aplikací do AzureAD](./quickstart-register-app.md)<br>

[Oprávnění a souhlas na platformě Microsoft identity](./v2-permissions-and-consent.md)<br>

[AzureAD Microsoft Q&A](/answers/topics/azure-active-directory.html)