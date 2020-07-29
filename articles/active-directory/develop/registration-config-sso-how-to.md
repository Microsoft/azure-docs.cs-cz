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
ms.openlocfilehash: 9b6451d29a3a874e09d74cbe664662a395b647f9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82890462"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Jak nakonfigurovat jednotné přihlašování pro aplikaci

Povolení federovaného jednotného přihlašování (SSO) ve vaší aplikaci se automaticky povolí při federování prostřednictvím služby Azure AD pro OpenID Connect, SAML 2,0 nebo WS. Pokud se koncoví uživatelé musí přihlašovat, i když už mají existující relaci se službou Azure AD, je možné, že vaše aplikace bude možná nesprávně nakonfigurovaná.

* Pokud používáte ADAL/MSAL, ujistěte se, že máte **PromptBehavior** nastavenou na **Automatické** místo **vždy**.

* Pokud vytváříte mobilní aplikaci, možná budete potřebovat další konfigurace, aby bylo možné povolit zprostředkované nebo nezprostředkované jednotné přihlašování.

Informace pro Android najdete v tématu [povolení jednotného přihlašování mezi aplikacemi v Androidu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

Informace o iOS najdete [v tématu povolení jednotného přihlašování mezi aplikacemi v iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Další kroky

[Jednotné přihlašování Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Povolení jednotného přihlašování mezi aplikacemi v Androidu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Povolení jednotného přihlašování mezi aplikacemi v iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integrace aplikací do AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Oprávnění a souhlas v koncovém bodu Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
