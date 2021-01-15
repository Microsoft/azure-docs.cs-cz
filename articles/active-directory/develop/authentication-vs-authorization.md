---
title: Ověřování oproti autorizaci | Azure
titleSuffix: Microsoft identity platform
description: Seznamte se se základy ověřování a autorizace na platformě Microsoft identity (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: d4abc9e1bba151f46adf71dd0185ddddad916a38
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216457"
---
# <a name="authentication-vs-authorization"></a>Ověřování vs. autorizace

Tento článek definuje ověřování a autorizaci. Také stručně popisuje, jak můžete pomocí platformy Microsoft Identity ověřovat a autorizovat uživatele ve webových aplikacích, webových rozhraních API nebo aplikacích, které volají chráněná webová rozhraní API. Pokud se vám zobrazí termín, který neznáte, vyzkoušejte si náš [Glosář](developer-glossary.md) nebo naše [videa k platformě Microsoft Identity](identity-videos.md), která se týkají základních konceptů.

## <a name="authentication"></a>Authentication

*Ověřování* je proces, který prokáže, že jste sami. Někdy se zkracuje na *Authn*. Platforma Microsoft Identity Platform používá ke zpracování ověřování protokol [OpenID Connect](https://openid.net/connect/) .

## <a name="authorization"></a>Autorizace

*Autorizace* je způsob, jakým se uděluje oprávnění ověřené strany k tomu, aby něco projednalo. Určuje, jaká data máte povolený přístup, a co s nimi můžete dělat. Autorizace se někdy zkracuje na *Authz*. Platforma Microsoft identity používá protokol [OAuth 2,0](https://oauth.net/2/) ke zpracování autorizace.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Ověřování a autorizace pomocí platformy Microsoft identity

Vytváření aplikací, které mají za starosti vlastní uživatelské jméno a heslo, má za následek vysokou administrativní zátěž, když potřebujete přidat nebo odebrat uživatele napříč více aplikacemi. Místo toho můžou vaše aplikace delegovat tuto odpovědnost na centralizovaného zprostředkovatele identity.

Azure Active Directory (Azure AD) je centralizovaný poskytovatel identity v cloudu. Delegování ověřování a autorizace do IT umožňuje scénáře, jako například:

- Zásady podmíněného přístupu, které vyžadují, aby uživatel byl v určitém umístění.
- Použití [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md), který se někdy nazývá dvojúrovňové ověřování nebo 2FA.
- Povolením, aby se uživatel přihlásil jednou a pak se automaticky přihlásil ke všem webovým aplikacím, které sdílejí stejný centralizovaný adresář. Tato funkce se nazývá *jednotné přihlašování (SSO)*.

Platforma Microsoftu identity usnadňuje autorizaci a ověřování pro vývojáře aplikací tím, že poskytuje identitu jako službu. Podporuje standardní protokoly a open source knihovny pro různé platformy, které vám pomůžou rychle začít vytvářet kódování. Umožňuje vývojářům vytvářet aplikace, které přihlásí všechny identity Microsoftu, získávají tokeny pro volání [Microsoft Graph](https://developer.microsoft.com/graph/), přístup k rozhraním Microsoft API nebo přístup k jiným rozhraním API, která vývojáři vytvořili.

Toto video vysvětluje platformu Microsoft identity a základy moderního ověřování: 

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

Tady je porovnání protokolů, které používá platforma Microsoft Identity Platform:

* **OAuth proti OpenID připojení**: platforma pro ověřování používá OAuth pro autorizaci a OpenID Connect (OIDC). OpenID Connect je postavená na OAuth 2,0, takže terminologie a tok jsou mezi nimi podobné. Můžete dokonce ověřit uživatele (prostřednictvím OpenID Connect) a získat autorizaci pro přístup k chráněnému prostředku, který vlastní uživatel (prostřednictvím OAuth 2,0) v jednom požadavku. Další informace najdete v tématu [protokoly OAuth 2,0 a OpenID Connect](active-directory-v2-protocols.md) a [protokol OpenID Connect](v2-protocols-oidc.md).
* **OAuth proti SAML**: platforma používá OAuth 2,0 k autorizaci a SAML pro ověřování. Další informace o tom, jak tyto protokoly používat při ověřování uživatele a jak získat autorizaci pro přístup k chráněnému prostředku, najdete v tématu [tok kontrolního výrazu SAML pro Microsoft Identity Platform a OAuth 2,0](v2-saml-bearer-assertion.md).
* **OpenID Connect versus SAML**: platforma používá OpenID Connect i SAML k ověření uživatele a povoluje jednotné přihlašování. Ověřování SAML se běžně používá u zprostředkovatelů identity, jako je Active Directory Federation Services (AD FS) (AD FS) federované do Azure AD, takže se často používá v podnikových aplikacích. OpenID Connect se běžně používá pro aplikace, které jsou čistě v cloudu, jako jsou například mobilní aplikace, weby a webová rozhraní API.

## <a name="next-steps"></a>Další kroky

Další témata, která se týkají základy ověřování a autorizace:

* Další informace o tom, jak se v autorizaci a ověřování používají přístupové tokeny, aktualizovat tokeny a tokeny ID, najdete v tématu [tokeny zabezpečení](security-tokens.md).
* Další informace o procesu registrace aplikace, aby ji bylo možné integrovat s platformou Microsoft identity, najdete v tématu [aplikační model](application-model.md).
