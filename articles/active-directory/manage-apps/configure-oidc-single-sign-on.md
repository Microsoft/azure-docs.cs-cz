---
title: Pochopení jednotného přihlašování (SSO) založeného na OIDC pro aplikace v Azure Active Directory
description: Pochopení jednotného přihlašování (SSO) založeného na OIDC pro aplikace v Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.custom: contperf-fy21q2
ms.openlocfilehash: ffaa55d4aa482e8f0eda93b1b32db6310d17e2a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255245"
---
# <a name="understand-oidc-based-single-sign-on"></a>Pochopení jednotného přihlašování založeného na OIDC
V [řadě rychlých startů](view-applications-portal.md) při správě aplikací jste zjistili, jak používat Azure AD jako zprostředkovatele identity (IDP) pro aplikaci. V tomto článku najdete další podrobnosti o aplikacích, které používají standard OpenID Connect k implementaci jednotného přihlašování. 

## <a name="before-you-begin"></a>Než začnete
Proces přidání aplikace do tenanta Azure Active Directory závisí na typu jednotného přihlašování implementované aplikace. Další informace o možnostech jednotného přihlašování dostupných pro aplikace, které můžou používat Azure AD pro správu identit, najdete v tématu [Možnosti jednotného přihlašování](sso-options.md). Tento článek popisuje aplikace založené na OIDC.


## <a name="basic-oidc-configuration"></a>Základní konfigurace OIDC
V [sérii rychlých startech](add-application-portal-setup-oidc-sso.md)je k dispozici článek týkající se konfigurace jednotného přihlašování. V takovém případě se dozvíte, jak přidat aplikaci založenou na OIDC do tenanta Azure.

Je vhodné přidat aplikaci, která používá standard OIDC pro jednotné přihlašování, pokud je konfigurace minimální. Tady je krátké video ukazující, jak do svého tenanta přidat aplikaci založenou na OIDC.

Přidání aplikace založené na OIDC v Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

Další informace o souhlasu uživatele a správce najdete v tématu [vysvětlení souhlasu uživatele a správce](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

## <a name="next-steps"></a>Další kroky

- [Série rychlý Start při správě aplikací](add-application-portal-setup-oidc-sso.md)
- [Možnosti jednotného přihlašování](sso-options.md)
- [Postup: Přihlášení libovolného uživatele služby Azure Active Directory pomocí vzoru aplikace s více tenanty](../develop/howto-convert-app-to-be-multi-tenant.md)
