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
ms.openlocfilehash: 630be1676d73410f1b0d376b163a8599299ddbe2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80883147"
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

[Souhlas a oprávnění pro sblížené aplikace AzureAD v 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
