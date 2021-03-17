---
title: Ověřování SAML s Azure Active Directory
description: Pokyny pro architekturu při dosahování ověřování SAML pomocí Azure Active Directory
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
ms.openlocfilehash: 7cd2aa5e9ff8cbaeead69f11d2e3de7f760b53ec
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168640"
---
# <a name="saml-authentication-with-azure-active-directory"></a>Ověřování SAML s Azure Active Directory

Security Assertion Markup Language (SAML) je otevřený standard pro výměnu ověřovacích a autorizačních dat mezi zprostředkovatelem identity a poskytovatelem služeb. SAML je značkovací jazyk založený na jazyce XML pro kontrolní výrazy zabezpečení, které jsou příkazy, které poskytovatelé služeb používají pro rozhodování o řízení přístupu. 

Specifikace SAML definuje tři role:

* Objekt zabezpečení, obecně uživatel
* Zprostředkovatel identity (IdP)
* Poskytovatel služeb (SP)


## <a name="use-when"></a>Kdy použít

Pro podnikovou aplikaci SAML je potřeba zadat jednotné přihlašování (SSO).

I když je jedním z nejdůležitějších případů použití jednotné přihlašování, zejména rozšířením jednotného přihlašování napříč doménami zabezpečení, existují i jiné případy použití (nazývané profily). 

![Diagram architektury pro SAML](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>Součásti systému

* **Uživatel**: vyžádá od aplikace službu.

* **Webový prohlížeč**: součást, se kterou uživatel pracuje.

* **Webová** aplikace: podniková aplikace, která podporuje SAML a používá Azure AD jako IDP.

* **Token**: kontrolní výraz SAML (označovaný také jako tokeny SAML), který má sady deklarací identity provedené IDP o principu (uživatele). Obsahuje informace o ověřování, atributy a příkazy autorizace rozhodnutí.

* **Azure AD**: podnikový cloud IDP, který poskytuje jednotné přihlašování a vícefaktorové ověřování pro aplikace SAML. Synchronizuje, uchovává a spravuje informace o identitě pro uživatele a zároveň poskytuje ověřovací služby pro spoléhající se na aplikace. 

## <a name="implement-saml-authentication-with-azure-ad"></a>Implementace ověřování SAML pomocí Azure AD

* [Kurzy pro integraci SaaSch aplikací pomocí Azure Active Directory](../saas-apps/tutorial-list.md) 

* [Konfigurace jednotného přihlašování založeného na SAML pro aplikace mimo galerii](../manage-apps/add-application-portal.md) 

* [Jak Azure AD využívá protokol SAML](../develop/active-directory-saml-protocol-reference.md)