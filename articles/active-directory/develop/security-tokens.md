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
ms.openlocfilehash: 9668d3b0b57e36fb95421f8b502b9b743be8eb31
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756214"
---
# <a name="security-tokens"></a>Tokeny zabezpečení

Centralizovaný poskytovatel identity je zvláště užitečný pro aplikace, které mají uživatele na celém světě, kteří se nemusí nutně přihlašovat ze sítě Enterprise. Platforma Microsoft Identity ověřuje uživatele a poskytuje tokeny zabezpečení, jako je [přístupový token](developer-glossary.md#access-token), [obnovovací token](developer-glossary.md#refresh-token)a [token ID](developer-glossary.md#id-token), které umožňují [klientské aplikaci](developer-glossary.md#client-application) přístup k chráněným prostředkům na [serveru prostředků](developer-glossary.md#resource-server).

**Přístupový token** je token zabezpečení, který je vydaný [autorizačním serverem](developer-glossary.md#authorization-server) jako součást toku [OAuth 2,0](active-directory-v2-protocols.md) . Obsahuje informace o uživateli a aplikaci, pro kterou je token určen. který se dá použít pro přístup k webovým rozhraním API a dalším chráněným prostředkům. Další informace o tom, jak Microsoft Identity Platform vydává přístup k tokenům, najdete v tématu [přístupové tokeny](access-tokens.md).

Přístupové tokeny jsou platné jenom po krátkou dobu, takže autorizační servery někdy vystaví **obnovovací token** ve stejnou dobu, kdy se přístupový token vydá. V případě potřeby může klientská aplikace vyměňovat Tento obnovovací token pro nový přístupový token. Další informace o tom, jak platforma Microsoft Identity Platform používá k odvolání oprávnění obnovovací tokeny, najdete v tématu [odvolání tokenu](access-tokens.md#token-revocation).

**Tokeny ID** se odesílají do klientské aplikace jako součást toku [OpenID Connect](v2-protocols-oidc.md) . Je možné je odeslat podél sebe nebo místo přístupového tokenu a klient je používá k ověření uživatele. Další informace o tom, jak Microsoft Identity Platform vydává tokeny ID, najdete v tématu [tokeny ID](id-tokens.md).

> [!NOTE]
> Tento článek popisuje tokeny zabezpečení používané protokoly OAuth2 a OpenID Connect. Mnoho podnikových aplikací používá SAML k ověřování uživatelů. Informace o kontrolních výrazech SAML najdete v referenčních informacích k [tokenům SAML Azure AD](reference-saml-tokens.md) .

## <a name="validating-security-tokens"></a>Ověřování tokenů zabezpečení

Je to až aplikace, pro kterou se token vygeneroval, webovou aplikaci, která se přihlásila jako uživatel, nebo webové rozhraní API, které se zavolá, aby se token ověřil. Token je podepsaný **serverem tokenu zabezpečení (STS)** pomocí privátního klíče. Služba STS publikuje odpovídající veřejný klíč. K ověření tokenu aplikace ověří podpis pomocí veřejného klíče služby STS a ověří, zda byl podpis vytvořen pomocí privátního klíče.

Tokeny jsou platné pouze po omezené množství času. Služba STS obvykle poskytuje dvojici tokenů:

* Přístupový token pro přístup k aplikaci nebo chráněnému prostředku a
* Obnovovací token, který se používá k aktualizaci přístupového tokenu, když je přístupový token blízko vypršení platnosti.

Přístupové tokeny se předávají webovému rozhraní API jako nosný token v `Authorization` hlavičce. Aplikace může službě STS poskytnout obnovovací token a pokud se uživatel k aplikaci neodvolává, vrátí nový přístupový token a nový obnovovací token. To je způsob, jakým se zpracovává scénář někoho, kdo opouští podnik. Když STS obdrží obnovovací token, nevydá jiný platný přístupový token, pokud už uživatel není autorizovaný.

## <a name="json-web-tokens-jwts-and-claims"></a>Webové tokeny JSON (JWTs) a deklarace identity

Microsoft Identity Platform implementuje tokeny zabezpečení jako **webové tokeny JSON (JWTs)** , které obsahují **deklarace identity**. Vzhledem k tomu, že JWTs se používají jako tokeny zabezpečení, tato forma ověřování se někdy nazývá **ověřování pomocí JWT**.

[Deklarace identity](developer-glossary.md#claim) poskytuje kontrolní výrazy týkající se jedné entity, jako je například klientská aplikace nebo [vlastník prostředku](developer-glossary.md#resource-owner), k jiné entitě, jako je například server prostředků. Deklarace identity může být také označována jako deklarace JWT nebo deklarace JSON Web Token.

Deklarace identity jsou páry název-hodnota, které přenášejí fakta týkající se předmětu tokenu. Deklarace identity může například obsahovat fakta o objektu zabezpečení, který byl ověřen autorizačním serverem. Deklarace identity přítomné v daném tokenu závisí na mnoha věcí, včetně typu tokenu, typu přihlašovacího údaje použitého k ověření předmětu, konfigurace aplikace atd.

Aplikace mohou používat deklarace identity pro různé úlohy, jako například:

* Ověřování tokenu
* Identifikace [tenanta](developer-glossary.md#tenant) předmětu tokenu
* Zobrazení informací o uživateli
* Určení autorizace subjektu

Deklarace identity se skládá z párů klíč-hodnota, které poskytují informace, jako jsou:

* Server tokenu zabezpečení, který vygeneroval token
* Datum, kdy se token vygeneroval
* Předmět (například uživatel – s výjimkou démonů)
* Cílová skupina, což je aplikace, pro kterou se token vygeneroval
* Aplikace (klient), která požádala o token. V případě webových aplikací to může být stejné jako cílová skupina.

Další informace o tom, jak platforma Microsoft Identity implementuje tokeny a informace o deklaraci identity, najdete v tématu [přístupové tokeny](access-tokens.md) a [tokeny ID](id-tokens.md).

## <a name="how-each-flow-emits-tokens-and-codes"></a>Jak každý tok generuje tokeny a kódy

V závislosti na tom, jak je váš klient sestavený, může použít jeden (nebo několik) toků ověřování podporovaných platformou Microsoft identity. Tyto toky můžou vytvářet různé tokeny (tokeny ID, aktualizovat tokeny, přístupové tokeny) a také autorizační kódy a při práci vyžadovat jiné tokeny. Tento graf poskytuje přehled:

|Tok | Nutné | Token ID | přístupový token | aktualizovat token | autorizační kód |
|-----|----------|----------|--------------|---------------|--------------------|
|[Tok autorizačního kódu](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Implicitní tok](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybridní tok OIDC](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Aktualizovat uplatnění tokenu](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | aktualizovat token | x | x | x| |
|[Tok On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md) | přístupový token| x| x| x| |
|[Přihlašovací údaje klienta](v2-oauth2-client-creds-grant-flow.md) | | | x (jenom aplikace)| | |

Tokeny vydané prostřednictvím implicitního režimu mají omezení délky, protože se předává zpátky do prohlížeče přes adresu URL (kde `response_mode` je `query` nebo `fragment` ).  Některé prohlížeče mají omezení velikosti adresy URL, kterou lze umístit do panelu prohlížeče, a selhání, pokud je příliš dlouhé.  Proto tyto tokeny nejsou `groups` ani `wids` deklarace identity.

## <a name="next-steps"></a>Další kroky

Další témata týkající se ověřování a základů autorizace:

* V tématu [ověřování vs. autorizace](authentication-vs-authorization.md) se dozvíte o základních konceptech ověřování a autorizace na platformě Microsoft identity.
* V článku [aplikační model](application-model.md) se dozvíte o procesu registrace aplikace, aby se mohla integrovat s platformou Microsoft identity.
* V tématu [Flow pro přihlášení k aplikaci](app-sign-in-flow.md) se dozvíte o procesu přihlašování webových, desktopových a mobilních aplikací na platformě Microsoft identity.
