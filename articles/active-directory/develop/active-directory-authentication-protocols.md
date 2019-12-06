---
title: Azure Active Directory ověřovací protokoly | Microsoft Docs
description: Přehled ověřovacích protokolů podporovaných službou Azure Active Directory (AD)
author: rwike77
services: active-directory
manager: CelesteDG
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/27/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: b370736a5e4994651499716e3a923cf59465ca96
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74845412"
---
# <a name="azure-active-directory-authentication-protocols"></a>Protokoly Azure Active Directory ověřování

Azure Active Directory (Azure AD) podporuje několik nejčastěji používaných ověřovacích a autorizačních protokolů. Témata v této části popisují podporované protokoly a jejich implementaci ve službě Azure AD. Témata obsahovaly kontrolu podporovaných typů deklarací identity, Úvod k použití federačních metadat, podrobných OAuth 2,0. a referenční dokumentace k protokolům SAML 2,0 a část řešení potíží.

## <a name="authentication-protocols-articles-and-reference"></a>Články a referenční informace k ověřovacím protokolům

* [Důležité informace o změně podpisového klíče ve službě Azure AD](active-directory-signing-key-rollover.md) – Přečtěte si informace o výměně tempo podpisových klíčů služby Azure AD, změnách, které můžete provést k automatické aktualizaci klíče, a diskuzi o tom, jak aktualizovat nejběžnější scénáře aplikací.
* [Podporované typy tokenů a deklarací](v1-id-and-access-tokens.md) – Seznamte se s deklaracemi v tokenech, které Azure AD vystavuje.
* [Federační metadata](azure-ad-federation-metadata.md) – Přečtěte si, jak najít a interpretovat dokumenty metadat vygenerované službou Azure AD.
* [OAuth 2,0 ve službě Azure AD](v1-protocols-oauth-code.md) – Přečtěte si o implementaci OAuth 2,0 ve službě Azure AD.
* [OpenID Connect 1,0](v1-protocols-openid-connect-code.md) – Naučte se používat ověřovací protokol OAuth 2,0 pro ověřování.
* [Volání služeb s přihlašovacími údaji klienta](v1-oauth2-client-creds-grant-flow.md) – Zjistěte, jak pomocí přihlašovacích údajů klienta OAuth 2,0 udělit tok pro volání služeb.
* Obsluha [volání služby s](v1-oauth2-on-behalf-of-flow.md) využitím toku – Naučte se, jak používat službu OAuth 2,0 za běhu za účelem obsluhy volání služeb.
* [Odkaz na protokol SAML](active-directory-saml-protocol-reference.md) – informace o jednotném přihlašování a jednom z profilů SAML v Azure AD.

## <a name="see-also"></a>Další informace najdete v tématech

* [Azure Active Directory příručka pro vývojáře](v1-overview.md)
* [Ukázky kódu služby Active Directory](sample-v1-code.md)
