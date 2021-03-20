---
title: OpenID připojit ověřování pomocí Azure Active Directory
description: Pokyny pro architekturu týkající se ověřování OpenID připojení pomocí Azure Active Directory.
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
ms.openlocfilehash: 0e5bf7e51de38d42e64f6737e687c5946a464160
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96168657"
---
# <a name="openid-connect-authentication-with-azure-active-directory"></a>OpenID připojit ověřování pomocí Azure Active Directory

OpenID Connect (OIDC) je ověřovací protokol založený na protokolu OAuth2 (který se používá pro autorizaci). OIDC používá standardizované toky zpráv z OAuth2 k poskytování služeb identit. 

Cílem OIDC je "zjednodušit jednoduché a složitější možné věci". OIDC umožňuje vývojářům ověřovat své uživatele napříč weby a aplikacemi, aniž by museli vlastnit a spravovat soubory hesel. To poskytuje tvůrci aplikací zabezpečený způsob, jak ověřit identitu osoby, která aktuálně používá prohlížeč nebo nativní aplikaci, která je připojená k aplikaci.

Ověřování uživatele musí probíhat u poskytovatele identity, kde se zkontrolují relace nebo přihlašovací údaje uživatele. K tomu potřebujete důvěryhodný agent. Nativní aplikace obvykle pro tento účel spouštějí prohlížeč systému. Vložená zobrazení se považují za důvěryhodná, protože neexistují žádná data, která by bylo možné zabránit tomu, aby se aplikace nepovažovala na heslo uživatele. 

Kromě ověřování může být uživatel požádán o souhlas. Souhlas je explicitní oprávnění uživatele, aby aplikace mohla získat přístup k chráněným prostředkům. Souhlas se liší od ověřování, protože u prostředku je třeba zadat pouze jeden souhlas. Souhlas zůstává platný, dokud uživatel nebo správce ručně nezruší udělení oprávnění. 

## <a name="use-when"></a>Kdy použít

Vyžaduje se souhlas uživatele a přihlášení k webu.

![Diagram architektury](./media/authentication-patterns/oidc-auth.png)

## <a name="components-of-system"></a>Součásti systému

* **Uživatel**: vyžádá od aplikace službu.

* **Důvěryhodný agent**: součást, se kterou uživatel pracuje. Tento důvěryhodný Agent je obvykle webový prohlížeč.

* **Aplikace**: aplikace nebo server prostředků je místo, kde se nachází prostředek nebo data. Důvěřuje poskytovateli identity pro bezpečné ověření a autorizaci důvěryhodného agenta. 

* **Azure AD**: poskytovatel OIDC, označovaný také jako poskytovatel identity, bezpečně spravuje cokoli k tomu, aby se informace o uživateli, jejich přístup a vztahy důvěryhodnosti mezi stranami v toku. Ověřuje identitu uživatele, uděluje a odvolává přístup k prostředkům a vydává tokeny. 

## <a name="implement-oidc-with-azure-ad"></a>Implementace OIDC s využitím Azure AD

* [Integrace aplikací s Azure AD](../saas-apps/tutorial-list.md) 

* [Protokoly OAuth 2,0 a OpenID Connect na platformě Microsoft Identity Platform](../develop/active-directory-v2-protocols.md) 

* [Microsoft Identity Platform a OpenID Connect Protocol](../develop/v2-protocols-oidc.md) 

* [Webové přihlašování pomocí OpenID Connect v Azure Active Directory B2C](../../active-directory-b2c/openid-connect.md) 

* [Zabezpečení aplikace pomocí OpenID Connect a Azure AD](/learn/modules/secure-app-with-oidc-and-azure-ad/) 

