---
title: Protokoly pro ověřování Azure Active Directory | Microsoft Docs
description: Přehled ověřovací protokoly podporované pomocí Azure Active Directory (AD)
documentationcenter: dev-center-name
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 71b12b848b1be309823bac46a219c0bb25e26092
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="azure-active-directory-authentication-protocols"></a>Protokoly pro ověřování Azure Active Directory
Azure Active Directory (Azure AD) podporuje několik nejčastěji používané protokoly ověřování a autorizace. Témata v této části popisují podporovaných protokolů a jejich provádění ve službě Azure AD. Témata zahrnuté kontrolu podporované typy deklarací identity, úvod do používání federačních metadat, podrobného OAuth 2.0. a referenční dokumentace protokolu SAML 2.0 a část s řešením potíží.

## <a name="authentication-protocols-articles-and-reference"></a>Protokoly ověřování články a referenční informace
* [Důležité informace o podpisový klíč výměny ve službě Azure AD](active-directory-signing-key-rollover.md) – Další informace o Azure AD podepisování cadence výměny klíčů, můžete provést automaticky aktualizovat klíč změny a diskusi k tom, jak aktualizovat nejběžnějších scénářů aplikace.
* [Podporované typy deklarací identity a Token](active-directory-token-and-claims.md) -Další informace o deklaracích identity v tokenech, které Azure AD vydá.
* [Federační Metadata](active-directory-federation-metadata.md) – zjistěte, jak najít a interpretovat dokumenty metadat, které generuje Azure AD.
* [OAuth 2.0 ve službě Azure AD](active-directory-protocols-oauth-code.md) -Další informace o provádění OAuth 2.0 ve službě Azure AD.
* [OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) -Další informace o použití OAuth 2.0, ověřovací protokol, pro ověřování.
* [Volání služeb s pověření klienta](active-directory-protocols-oauth-service-to-service.md) – Naučte se používat pro volání služeb tok udělení pověření klienta OAuth 2.0.
* [Volání služeb s tokem On-Behalf-Of](active-directory-protocols-oauth-on-behalf-of.md) -Další informace o použití toku OAuth 2.0 On-Behalf-Of pro volání služeb.
* [Referenční informace o protokolu SAML](active-directory-saml-protocol-reference.md) -Další informace o profilech jednotné přihlašování a jeden Sign-out SAML Azure AD.

## <a name="see-also"></a>Viz také
[Příručka pro vývojáře Azure Active Directory](active-directory-developers-guide.md)

[Ukázky kódu služby Active Directory](active-directory-code-samples.md)
