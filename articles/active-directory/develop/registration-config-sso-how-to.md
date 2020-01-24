---
title: Konfigurace jednotného přihlašování aplikace | Microsoft Docs
description: Jak nakonfigurovat jednotné přihlašování pro vlastní aplikaci, kterou vyvíjíte a zaregistrujete ve službě Azure AD.
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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702637"
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
