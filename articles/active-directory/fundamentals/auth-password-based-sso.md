---
title: Ověřování na základě hesla pomocí Azure Active Directory
description: Pokyny pro architekturu týkající se ověřování na základě hesla pomocí Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74815976036abafc56f97e622351a1018fe30504
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576928"
---
# <a name="password-based-authentication-with-azure-active-directory"></a>Ověřování na základě hesla pomocí Azure Active Directory

Sign-On jednotné přihlašování na základě hesla používá pro aplikaci existující proces ověřování. Pokud povolíte jednotné přihlašování založené na heslech, Azure Active Directory (Azure AD) shromažďuje, šifruje a bezpečně ukládá přihlašovací údaje uživatelů v adresáři. Azure AD poskytuje aplikaci uživatelské jméno a heslo, když se uživatel pokusí přihlásit.

Pokud se aplikace ověřuje pomocí uživatelského jména a hesla místo přístupových tokenů a hlaviček, vyberte jednotné přihlašování založené na heslech. Jednotné přihlašování založené na heslech podporuje jakékoli cloudové aplikace, které mají přihlašovací stránku založenou na jazyce HTML. 

## <a name="use-when"></a>Kdy použít

Je nutné chránit pomocí předběžného ověřování a poskytovat jednotné přihlašování prostřednictvím trezoru hesel k webovým aplikacím.

![Diagram architektury](./media/authentication-patterns/password-based-sso-auth.png)


## <a name="components-of-system"></a>Součásti systému

* **Uživatel** : přistupuje k aplikaci vytvořené pomocí aplikace z mých aplikací nebo přímo na web. 

* **Webový prohlížeč** : komponenta, s jakou uživatel pracuje, aby mohla přistupovat k externí adrese URL aplikace. Uživatel přistupuje k aplikaci založené na formulářích prostřednictvím rozšíření MyApp. 

* **Rozšíření MyApp** : identifikuje nakonfigurovanou aplikaci jednotného přihlašování založené na heslech a zadá přihlašovací údaje do formuláře pro přihlášení. Rozšíření MyApps je nainstalováno ve webovém prohlížeči. 

* **Azure AD** : ověřuje uživatele.

## <a name="implement-password-based-sso-with-azure-ad"></a>Implementace jednotného přihlašování založeného na heslech pomocí Azure AD

* [Co je jednotné přihlašování založené na heslech](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) 

* [Konfigurace jednotného přihlašování založeného na heslech pro cloudové aplikace ](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications)

* [Konfigurace jednotného přihlašování založeného na heslech pro místní aplikace s proxy aplikací](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting)

 
