---
title: Pochopení jednotného přihlašování (SSO) založeného na OIDC pro aplikace v Azure Active Directory
description: Pochopení jednotného přihlašování (SSO) založeného na OIDC pro aplikace v Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.openlocfilehash: 825f79b0a1c132fb7a15d643c3487dfb7d6a9abd
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209873"
---
# <a name="understand-oidc-based-single-sign-on"></a>Pochopení jednotného přihlašování založeného na OIDC
V [řadě rychlých startů](view-applications-portal.md) při správě aplikací jste zjistili, jak používat Azure AD jako zprostředkovatele identity (IDP) pro aplikaci. V tomto článku najdete další podrobnosti o aplikacích, které používají standard OpenID Connect k implementaci jednotného přihlašování. 

## <a name="before-you-begin"></a>Než začnete
Proces přidání aplikace do tenanta Azure Active Directory závisí na typu jednotného přihlašování implementované aplikace. Další informace o možnostech jednotného přihlašování dostupných pro aplikace, které můžou používat Azure AD pro správu identit, najdete v tématu [Možnosti jednotného přihlašování](sso-options.md). Tento článek popisuje aplikace založené na OIDC.


## <a name="basic-oidc-configuration"></a>Základní konfigurace OIDC
V [sérii rychlých startech](add-application-portal-setup-oidc-sso.md)je k dispozici článek týkající se konfigurace jednotného přihlašování. V takovém případě se dozvíte, jak přidat aplikaci založenou na OIDC do tenanta Azure.

Je vhodné přidat aplikaci, která používá standard OIDC pro jednotné přihlašování, pokud je konfigurace minimální. Tady je krátké video ukazující, jak do svého tenanta přidat aplikaci založenou na OIDC.

Přidání aplikace založené na OIDC v Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/4kv-upsZCI0]

## <a name="next-steps"></a>Další kroky

- [Série rychlý Start při správě aplikací](add-application-portal-setup-oidc-sso.md)
- [Možnosti jednotného přihlašování](sso-options.md)
- [Postup: Přihlášení libovolného uživatele služby Azure Active Directory pomocí vzoru aplikace s více tenanty](../develop/howto-convert-app-to-be-multi-tenant.md)