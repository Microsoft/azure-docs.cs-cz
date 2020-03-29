---
title: Ověřovací protokoly platformy Microsoft pro identity | Dokumenty společnosti Microsoft
description: Přehled ověřovacích protokolů podporovaných platformou microsoft identity
author: rwike77
services: active-directory
manager: CelesteDG
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 43168ec7217d8f016857ba6dc54ca30bce2dd594
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76699287"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Ověřovací protokoly platformy microsoftu pro identity

Platforma identit společnosti Microsoft podporuje několik nejpoužívanějších ověřovacích a autorizačních protokolů. Témata v této části popisují podporované protokoly a jejich implementaci v platformě identit microsoftu. Témata zahrnovala přehled podporovaných typů deklarací, úvod do používání metadat federace, podrobný OAuth 2.0. a referenční dokumentace protokolu SAML 2.0 a část pro řešení potíží.

## <a name="authentication-protocols-articles-and-reference"></a>Články a odkazy na ověřovací protokoly

* [Důležité informace o podepisování klíče rollover v platformě identit Microsoft](active-directory-signing-key-rollover.md) – Další informace o microsoft identity platformy podpisový klíč rollover kadence, změny, které můžete provést aktualizovat klíč automaticky a diskuse o tom, jak aktualizovat nejběžnější scénáře aplikací.
* [Podporované typy tokenů a deklarací](id-tokens.md) – informace o deklaracích v tokenech, které má platforma identity Microsoftu problémy.
* [OAuth 2.0 v platformě microsoft identity](v2-oauth2-auth-code-flow.md) – Další informace o implementaci OAuth 2.0 v platformě identit Microsoftu.
* [OpenID Connect 1.0](v2-protocols-oidc.md) - Naučte se používat OAuth 2.0, autorizační protokol, pro ověřování.
* [Volání služby s pověřeními klienta](v2-oauth2-client-creds-grant-flow.md) – zjistěte, jak používat pověření klienta OAuth 2.0 udělovat tok pro volání služby.
* [Služba servisníhovory s On-Behalf-Of Flow](v2-oauth2-on-behalf-of-flow.md) - Přečtěte si, jak používat OAuth 2.0 On-Behalf-Of toku pro služby servisní hovory.
* [Odkaz na protokol SAML](active-directory-saml-protocol-reference.md) – informace o profilech SAML jednotného přihlášení a jednotného přihlášení na platformě identit microsoftu.

## <a name="see-also"></a>Viz také

* [Přehled platformy identit Microsoftu](v2-overview.md)
* [Ukázky kódu služby Active Directory](sample-v2-code.md)
