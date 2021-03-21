---
title: Tokeny zabezpečení | Azure
titleSuffix: Microsoft identity platform
description: Seznamte se se základy tokenů zabezpečení na platformě Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 6d9f5538d377be1414089e591559344bde4f381a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98795648"
---
# <a name="security-tokens"></a>Tokeny zabezpečení

Centralizovaný poskytovatel identity je zvláště užitečný pro aplikace, které mají uživatele na celém světě, kteří se nemusí nutně přihlašovat ze sítě Enterprise. Platforma Microsoft Identity ověřuje uživatele a poskytuje tokeny zabezpečení, jako jsou [přístupové tokeny](developer-glossary.md#access-token), [aktualizační tokeny](developer-glossary.md#refresh-token)a [tokeny ID](developer-glossary.md#id-token). Tokeny zabezpečení umožňují [klientské aplikaci](developer-glossary.md#client-application) získat přístup k chráněným prostředkům na [serveru prostředků](developer-glossary.md#resource-server).

**Přístupový token**: přístupový token je token zabezpečení, který je vydaný [autorizačním serverem](developer-glossary.md#authorization-server) jako součást toku [OAuth 2,0](active-directory-v2-protocols.md) . Obsahuje informace o uživateli a prostředku, pro který je token určen. Tyto informace se dají použít pro přístup k webovým rozhraním API a dalším chráněným prostředkům. Přístupové tokeny jsou ověřeny prostředky pro udělení přístupu klientské aplikaci. Další informace o tom, jak Microsoft Identity Platform vydává přístup k tokenům, najdete v tématu [přístupové tokeny](access-tokens.md).

**Aktualizovat token**: vzhledem k tomu, že přístupové tokeny jsou platné jenom pro krátkou dobu, autorizační servery někdy vystaví obnovovací token současně s vydáním přístupového tokenu. V případě potřeby může klientská aplikace vyměňovat Tento obnovovací token pro nový přístupový token. Další informace o tom, jak platforma Microsoft Identity Platform používá k odvolání oprávnění obnovovací tokeny, najdete v tématu [odvolání tokenu](access-tokens.md#token-revocation).

**Token ID**: tokeny ID se odesílají do klientské aplikace jako součást toku [OpenID Connect](v2-protocols-oidc.md) . Je možné je odeslat společně nebo místo přístupového tokenu. Tokeny ID, které klient používá k ověření uživatele. Další informace o tom, jak Microsoft Identity Platform vydává tokeny ID, najdete v tématu [tokeny ID](id-tokens.md).

> [!NOTE]
> Tento článek popisuje tokeny zabezpečení používané protokoly OAuth2 a OpenID Connect. Mnoho podnikových aplikací používá SAML k ověřování uživatelů. Informace o kontrolních výrazech SAML naleznete v tématu [Azure Active Directory Reference k tokenu SAML](reference-saml-tokens.md).

## <a name="validate-security-tokens"></a>Ověřit tokeny zabezpečení

Je to až aplikace, pro kterou se token vygeneroval, webovou aplikaci, která se přihlásila uživateli, nebo webové rozhraní API, které se zavolá k ověření tokenu. Token je podepsán autorizačním serverem pomocí privátního klíče. Autorizační server publikuje odpovídající veřejný klíč. K ověření tokenu aplikace ověří podpis pomocí veřejného klíče autorizačního serveru a ověří, že byl podpis vytvořen pomocí privátního klíče.

Tokeny jsou platné pouze po omezené množství času. Autorizační Server obvykle poskytuje dvojici tokenů, například:

* Přístupový token, který přistupuje k aplikaci nebo chráněnému prostředku.
* Obnovovací token, který se používá k aktualizaci přístupového tokenu, když je přístupový token blízko vypršení platnosti.

Přístupové tokeny se předávají webovému rozhraní API jako nosný token v `Authorization` hlavičce. Aplikace může na autorizačním serveru poskytnout obnovovací token. Pokud uživatel nezrušil přístup k aplikaci, vrátí se nový přístupový token a nový obnovovací token. To je způsob, jakým se zpracovává scénář někoho, kdo opouští podnik. Když autorizační server obdrží obnovovací token, nevydá jiný platný přístupový token, pokud už uživatel není autorizovaný.

## <a name="json-web-tokens-and-claims"></a>Webové tokeny JSON a deklarace identity

Microsoft Identity Platform implementuje tokeny zabezpečení jako webové tokeny JSON (JWTs), které obsahují *deklarace identity*. Vzhledem k tomu, že JWTs se používají jako tokeny zabezpečení, tato forma ověřování se někdy nazývá *ověřování pomocí JWT*.

[Deklarace identity](developer-glossary.md#claim) poskytuje kontrolní výrazy týkající se jedné entity, jako je například klientská aplikace nebo [vlastník prostředku](developer-glossary.md#resource-owner), k jiné entitě, jako je například server prostředků. Deklarace identity může být také označována jako deklarace identity JWT nebo deklarace JSON Web Token.

Deklarace identity jsou páry název nebo hodnota, které přenášejí fakta týkající se předmětu tokenu. Deklarace identity může například obsahovat fakta o objektu zabezpečení, který byl ověřen autorizačním serverem. Deklarace identity přítomné v konkrétním tokenu závisí na mnoha věcí, jako je typ tokenu, typ přihlašovacích údajů použitých k ověření předmětu a konfigurace aplikace.

Aplikace můžou používat deklarace identity pro různé úlohy, jako jsou:

* Ověřte token.
* Identifikujte [tenanta](developer-glossary.md#tenant)předmětu tokenu.
* Zobrazí informace o uživateli.
* Určete autorizaci předmětu.

Deklarace identity se skládá z párů klíč-hodnota, které poskytují informace, jako jsou:

* Server tokenu zabezpečení, který vygeneroval token.
* Datum, kdy se token vygeneroval.
* Předmět (například uživatel – s výjimkou démonů).
* Cílová skupina, což je aplikace, pro kterou se token vygeneroval.
* Aplikace (klient), která požádala o token. V případě webových aplikací může být tato aplikace stejná jako cílová skupina.

Další informace o tom, jak platforma Microsoft Identity implementuje tokeny a informace o deklaraci identity, najdete v tématu [přístupové tokeny](access-tokens.md) a [tokeny ID](id-tokens.md).

## <a name="how-each-flow-emits-tokens-and-codes"></a>Jak každý tok generuje tokeny a kódy

V závislosti na tom, jak je váš klient sestavený, může použít jeden (nebo několik) toků ověřování podporovaných platformou Microsoft identity. Tyto toky můžou vydávat různé tokeny (tokeny ID, aktualizovat tokeny, přístupové tokeny) a autorizační kódy. Vyžadují různé tokeny, aby je bylo možné použít. Tato tabulka poskytuje přehled.

|Tok | Nutné | Token ID | Přístupový token | Aktualizovat token | Autorizační kód |
|-----|----------|----------|--------------|---------------|--------------------|
|[Tok autorizačního kódu](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Implicitní tok](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybridní tok OIDC](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Aktualizovat uplatnění tokenu](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | Aktualizovat token | x | x | x| |
|[Tok On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md) | Přístupový token| x| x| x| |
|[Přihlašovací údaje klienta](v2-oauth2-client-creds-grant-flow.md) | | | x (jenom aplikace)| | |

Tokeny vydané prostřednictvím implicitního režimu mají omezení délky, protože jsou předávány zpět do prohlížeče přes adresu URL, kde `response_mode` je `query` nebo `fragment` . Některé prohlížeče mají omezení velikosti adresy URL, kterou lze umístit do panelu prohlížeče, a selhání, pokud je příliš dlouhé. V důsledku toho tyto tokeny neobsahují `groups` ani `wids` nedeklarují deklarace identity.

## <a name="next-steps"></a>Další kroky

Další informace o ověřování a autorizaci na platformě Microsoft identity najdete v následujících článcích:

* Další informace o základních principech ověřování a autorizace najdete v tématu [ověřování vs. Authorization](authentication-vs-authorization.md).
* Další informace o registraci aplikace pro integraci najdete v tématu [aplikační model](application-model.md).
* Další informace o procesu přihlašování webových, desktopových a mobilních aplikací najdete v tématu [Flow přihlašování k aplikacím](app-sign-in-flow.md).
