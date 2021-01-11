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
ROBOTS: NOINDEX
ms.openlocfilehash: ffd9b415d3b2a8e1aa716caa0e8289c6ae127b71
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065078"
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
* [Referenční informace o protokolu SAML](active-directory-saml-protocol-reference.md) – Přečtěte si o jednom Sign-On a jednom z profilů SAML pro jednotné přihlašování na platformě Microsoft Identity Platform.

## <a name="see-also"></a>Viz také

* [Přehled platformy Microsoft identity](v2-overview.md)
* [Ukázky kódu služby Active Directory](sample-v2-code.md)
