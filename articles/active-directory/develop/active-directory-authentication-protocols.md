---
title: Protokoly ověřování platformy Microsoft identity
description: Přehled ověřovacích protokolů podporovaných platformou Microsoft identity
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 41ea41b4d7c181dad9246653a68c329387ac5381
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80884677"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Protokoly ověřování platformy Microsoft identity

Platforma Microsoft Identity Platform podporuje několik nejčastěji používaných ověřovacích a autorizačních protokolů. Témata v této části popisují podporované protokoly a jejich implementaci na platformě Microsoft identity. Témata obsahovaly kontrolu podporovaných typů deklarací identity, Úvod k použití federačních metadat, podrobných OAuth 2,0. a referenční dokumentace k protokolům SAML 2,0 a část řešení potíží.

## <a name="authentication-protocols-articles-and-reference"></a>Články a referenční informace k ověřovacím protokolům

* [Důležité informace o výměně klíčů při výměně na platformě Microsoft Identity](active-directory-signing-key-rollover.md) – Přečtěte si informace o změně podpisového klíče platformy Microsoft Identity Platform tempo, změnách, které můžete provést při automatickém aktualizování klíče, a diskuzi o tom, jak aktualizovat nejběžnější scénáře aplikací.
* [Podporované typy tokenů a deklarací](id-tokens.md) – Přečtěte si o deklaracích v tokenech, které Microsoft Identity Platform řeší.
* [Oauth 2,0 na platformě Microsoft Identity](v2-oauth2-auth-code-flow.md) – Přečtěte si o implementaci OAuth 2,0 na platformě Microsoft identity.
* [OpenID Connect 1,0](v2-protocols-oidc.md) – Naučte se používat ověřovací protokol OAuth 2,0 pro ověřování.
* [Volání služeb s přihlašovacími údaji klienta](v2-oauth2-client-creds-grant-flow.md) – Zjistěte, jak pomocí přihlašovacích údajů klienta OAuth 2,0 udělit tok pro volání služeb.
* Obsluha [volání služby s](v2-oauth2-on-behalf-of-flow.md) využitím toku – Naučte se, jak používat službu OAuth 2,0 za běhu za účelem obsluhy volání služeb.
* [Odkaz na protokol SAML](active-directory-saml-protocol-reference.md) – informace o jednotném přihlašování a jednom z profilů SAML platformy Microsoft Identity Platform

## <a name="see-also"></a>Viz také

* [Přehled platformy Microsoft identity](v2-overview.md)
* [Ukázky kódu služby Active Directory](sample-v2-code.md)
