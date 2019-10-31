---
title: Volba správného federačního protokolu pro vaši víceklientské aplikaci
description: Pokyny pro nezávislé výrobce softwaru při integraci s Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: ede458e7d4c1cb1a8d7e3f2e2c9df54d5925d6d8
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175984"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Volba správného federačního protokolu pro vaši víceklientské aplikaci

Při vývoji aplikace SaaS (software jako služba) musíte vybrat federační protokol, který nejlépe vyhovuje vašim potřebám a vašim zákazníkům. Toto rozhodnutí je založené na vaší vývojové platformě a vaše přání o integraci s daty dostupnými v rámci vašich zákazníků v ekosystému Office 365 a Azure AD.

Podívejte se na úplný seznam [protokolů, které jsou k dispozici pro integraci jednotného přihlašování](what-is-single-sign-on.md) s Azure Active Directory.
Následující tabulka porovnává 
* Otevřete ověřování 2,0 (OAuth 2,0).
* Otevřít ID připojení (OIDC)
* Security Assertion Markup Language (SAML)
* Specifikace Web Services Federation (WSFed)

| Schopnost| OAuth/OIDC| SAML/WSFed |
| - |-|-|
| Webové jednotné přihlašování| √| √ |
| Jednotné odhlašování na základě webu| √| √ |
| Jednotné přihlašování prostřednictvím mobilního telefonu| √| √ |
| Jednotné odhlašování pomocí mobilních zařízení| √| √ |
| Zásady podmíněného přístupu pro mobilní aplikace| √| × |
| Bezproblémové možnosti vícefaktorového ověřování pro mobilní aplikace| √| × |
| Přístup Microsoft Graph| √| × |

*, Ale Microsoft neposkytuje ukázky ani doprovodné materiály.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2,0 a otevřené ID připojit

OAuth 2,0 je [standardní](https://oauth.net/2/) protokol pro autorizaci. OIDC (OpenID Connect) je [standardní](https://openid.net/connect/) vrstva ověřování identity postavená na úrovni protokolu Oath 2,0.

### <a name="benefits"></a>Výhody

Microsoft doporučuje používat OIDC/OAuth 2,0, protože má k dispozici ověřování a autorizaci, které jsou integrované v těchto protokolech. V případě SAML je nutné implementovat také autorizaci.

Autorizace, která je na základě těchto protokolů, umožňuje vaší aplikaci přístup k datům uživatelů a organizací s bohatou a integrovanou datovou sadou prostřednictvím rozhraní Microsoft Graph API.

Použití OAuth 2,0 a OIDC zjednodušuje prostředí koncového uživatele vašich zákazníků při přijímání jednotného přihlašování pro vaši aplikaci. Je možné snadno definovat potřebné sady oprávnění, které se pak automaticky reprezentují pro správce nebo koncového uživatele, který souhlasí.

Navíc pomocí těchto protokolů můžou vaši zákazníci používat zásady podmíněného přístupu a vícefaktorového ověřování k řízení přístupu k aplikacím. Společnost Microsoft poskytuje knihovny a [ukázky kódu napříč různými technologickými platformami](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) , které pomáhají při vývoji.  

### <a name="implementation"></a>Implementace

Svou aplikaci zaregistrujete pomocí Microsoft identity, což je poskytovatel OAuth 2,0. Pak můžete zaregistrovat aplikaci založenou na protokolu OAuth 2,0 s jakýmkoli jiným poskytovatelem identity, se kterým chcete integrovat. 

Informace o registraci aplikace a implementaci těchto protokolů pro jednotné přihlašování k webovým aplikacím najdete v tématu [autorizace přístupu k webovým aplikacím pomocí OpenID Connect a Azure Active Directory](../develop/sample-v2-code.md).  Informace o tom, jak implementovat tyto protokoly pro jednotné přihlašování v mobilních aplikacích, najdete v následujících tématech: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Univerzální platforma Windows](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2,0 a WSFed

Security Assertion Markup Language (SAML) se obvykle používá pro webové aplikace. Podívejte se [, jak Azure používá pro přehled protokol SAML](../develop/active-directory-saml-protocol-reference.md) . 

Specifikace Web Services Federation (WSFed) je [Standardní obor](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) , který se běžně používá pro webové aplikace vyvinuté pomocí platformy .NET.

### <a name="benefits"></a>Výhody

SAML 2,0 je vyspělá Standard a většina technologických platforem podporuje open source knihovny pro SAML 2,0. Zákazníkům můžete poskytnout rozhraní pro správu pro konfiguraci jednotného přihlašování SAML. Můžou nakonfigurovat jednotné přihlašování SAML pro Microsoft Azure AD a libovolného jiného poskytovatele identity, který podporuje SAML 2.

### <a name="trade-offs"></a>Kompromisy

Při použití protokolů SAML 2,0 nebo WSFed pro mobilní aplikace budou mít určité zásady podmíněného přístupu, včetně služby Multi-Factor Authentication (MFA), omezené prostředí. Pokud navíc chcete získat přístup k Microsoft Graph, bude nutné implementovat autorizaci prostřednictvím OAuth 2,0, abyste vygenerovali potřebné tokeny. 

### <a name="implementation"></a>Implementace

Microsoft neposkytuje knihovny pro implementaci SAML ani nedoporučuje konkrétní knihovny. K dispozici je celá řada Open Source knihoven.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>Jednotné přihlašování a používání rozhraní Microsoft Graph REST API 

Microsoft Graph jsou datové prostředky napříč všemi Microsoft 365mi, včetně Office 365, Windows 10 a Enterprise mobility a zabezpečení, a dalších produktů, jako je Dynamics 365. To zahrnuje základní schémata entit, jako jsou uživatelé, skupiny, kalendář, pošta, soubory a další, které zvyšují produktivitu uživatelů. Microsoft Graph nabízí tři rozhraní pro vývojáře rozhraní API založených na REST, Microsoft Graph připojení dat a konektorů, které vývojářům umožňují přidat do Microsoft Graph vlastní data.  

Použití kteréhokoli z výše uvedených protokolů pro jednotné přihlašování umožňuje vaší aplikaci přístup k bohatě dostupným datům prostřednictvím Microsoft Graph REST API. Díky tomu můžou vaši zákazníci získat větší hodnotu z investice do Microsoft 365. Například vaše aplikace může volat rozhraní Microsoft Graph API pro integraci s instancí Office 365 vašich zákazníků a systém Microsoft Office a SharePointových položek v rámci vaší aplikace. 

Pokud používáte k ověřování otevřené ID připojit, bude prostředí pro vývoj bezproblémové, protože budete používat OAuth2, základ otevřeného ID připojit k získání tokenů se dá použít k vyvolání Microsoft Graph rozhraní API. Pokud vaše aplikace používá SAML nebo WSFed, musíte do své aplikace přidat další kód, abyste získali tyto OAuth2 k získání tokenů potřebných k vyvolání rozhraní Microsoft Graph API. 

## <a name="next-steps"></a>Další kroky

[Povolení jednotného přihlašování pro aplikaci s více klienty](isv-sso-content.md)

[Vytvoření dokumentace pro vaši víceklientské aplikace](isv-create-sso-documentation.md)
