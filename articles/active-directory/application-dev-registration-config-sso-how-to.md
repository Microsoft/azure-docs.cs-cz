---
title: Postup konfigurace novou aplikaci víceklientské | Microsoft Docs
description: Postup konfigurace jednotného přihlašování pro vlastní aplikaci vývoji a Probíhá registrace ve službě Azure AD.
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: c9654a9d03769bbf0e9d0e2c477ad26cefc05150
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
ms.locfileid: "26612911"
---
# <a name="how-to-configure-a-new-multi-tenant-application"></a>Postup konfigurace nové víceklientské aplikace

Povolení federované jednotné přihlašování (SSO) v aplikaci je automaticky povolen při federaci prostřednictvím služby Azure AD pro OpenID Connect SAML 2.0 a WS-Fed. Pokud vaši koncoví uživatelé se musejí přihlásit přes již existující relaci s Azure AD, je pravděpodobné, že vaše aplikace může být nesprávné.

* Pokud používáte ADAL/MSAL, ujistěte se, máte **PromptBehavior** nastavena na **automaticky** místo **vždy**.

* Pokud vytváříte mobilní aplikace, budete potřebovat další konfigurace, které umožňují zprostředkované nebo jiných zprostředkované jednotné přihlašování.

Pro Android, najdete v části [povolení mezi aplikace jednotné přihlašování v Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

Pro iOS, najdete v části [povolení mezi aplikace jednotné přihlašování v iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Další kroky

[Jednotné přihlašování Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Povolení křížové jednotného přihlašování k aplikaci v Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Povolení křížové aplikace jednotné přihlašování v iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integrace aplikace AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Souhlasu a systému oprávnění rolích pro AzureAD v2.0 konvergované aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
