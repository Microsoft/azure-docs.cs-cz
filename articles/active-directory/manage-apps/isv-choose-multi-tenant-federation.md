---
title: Výběr správného federačního protokolu pro víceklientské aplikace
description: Pokyny pro nezávislé dodavatele softwaru pro integraci s Azure Active Directory
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
ms.openlocfilehash: 3b3edbbe037c3874d639476e516b3732b7573d9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443381"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Výběr správného federačního protokolu pro víceklientské aplikace

Při vývoji softwaru jako aplikace služby (SaaS) je nutné vybrat federační protokol, který nejlépe vyhovuje potřebám vašich zákazníků. Toto rozhodnutí je založeno na vaší vývojové platformě a vaší touze integrovat s daty dostupnými v ekosystému Office 365 a Azure AD vašich zákazníků.

Podívejte se na úplný seznam [protokolů, které jsou k dispozici pro integrace služby SSO](what-is-single-sign-on.md) s Azure Active Directory.
Následující tabulka porovnává 
* Ověřování s otevřeným i 2,0 (OAuth 2.0)
* Otevřít ID připojení (OIDC)
* Jazyk značek kontrolního výrazu zabezpečení (SAML)
* Federace webových služeb (WSFed)

| Schopnost| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| Jednotné přihlašování z webu| √| √ |
| Webové jednotné odhlášení| √| √ |
| Jednotné přihlašování na mobilní zařízení| √| √* |
| Jednotné odhlášení na mobilních zařízeních| √| √* |
| Zásady podmíněného přístupu pro mobilní aplikace| √| × |
| Bezproblémové prostředí vícefaktorové žádosti o finanční služby pro mobilní aplikace| √| × |
| Access Microsoft Graph| √| × |

*Možné, ale společnost Microsoft neposkytuje vzorky ani pokyny.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 a Open ID Connect

OAuth 2.0 je [standardní](https://oauth.net/2/) protokol pro autorizaci. OIDC (OpenID Connect) je [standardní](https://openid.net/connect/) vrstva ověřování identity postavená na protokolu OAuth 2.0.

### <a name="benefits"></a>Výhody

Společnost Microsoft doporučuje používat OIDC/OAuth 2.0, protože mají ověřování a autorizaci integrované do protokolů. Pomocí saml je nutné dodatečně implementovat autorizaci.

Autorizace vlastní těmto protokolům umožňuje aplikaci přístup a integraci s bohatými uživatelskými a organizačními daty prostřednictvím rozhraní Microsoft Graph API.

Použití OAuth 2.0 a OIDC zjednodušuje prostředí koncových uživatelů vašich zákazníků při přijímání vlastního přihlašování pro vaši aplikaci. Můžete snadno definovat potřebné sady oprávnění, které jsou pak automaticky reprezentovány správci nebo koncovému uživateli souhlas.

Kromě toho pomocí těchto protokolů umožňuje zákazníkům používat podmíněný přístup a mfa zásady řídit přístup k aplikacím. Společnost Microsoft poskytuje knihovny a [ukázky kódu na různých technologických platformách,](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) které pomáhají vašemu vývoji.  

### <a name="implementation"></a>Implementace

Aplikaci zaregistrujete u microsoft identity, která je poskytovatelem OAuth 2.0. Pak můžete také zaregistrovat aplikaci založenou na OAuth 2.0 u jakéhokoli jiného poskytovatele identity, se kterým chcete integrovat. 

Informace o tom, jak zaregistrovat aplikaci a implementovat tyto protokoly pro stesado do webových aplikací, najdete [v tématu Autorizace přístupu k webovým aplikacím pomocí OpenID Connect a Azure Active Directory](../develop/sample-v2-code.md).  Informace o tom, jak implementovat tyto protokoly pro přisazožené služby v mobilních aplikacích, naleznete v následujících tématech: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Univerzální platforma Windows](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 a WSFed

Jazyk značek kontrolního výrazu zabezpečení (SAML) se obvykle používá pro webové aplikace. Přehled najdete [v článku Jak Azure používá protokol SAML.](../develop/active-directory-saml-protocol-reference.md) 

Web Services Federation (WSFed) je [oborový standard](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) obecně používaný pro webové aplikace, které jsou vyvíjeny pomocí platformy .Net.

### <a name="benefits"></a>Výhody

SAML 2.0 je vyspělý standard a většina technologických platforem podporuje open-source knihovny pro SAML 2.0. Zákazníkům můžete poskytnout rozhraní pro správu pro konfiguraci objektu spřibívek SAML. Můžou nakonfigurovat samonárodní přihrádky pro Microsoft Azure AD a všechny ostatní poskytovatele identit, který podporuje SAML 2.

### <a name="trade-offs"></a>Kompromisy

Při použití protokolů SAML 2.0 nebo WSFed pro mobilní aplikace budou mít některé zásady podmíněného přístupu včetně vícefaktorového ověřování (MFA) zhoršené prostředí. Navíc pokud chcete získat přístup k aplikaci Microsoft Graph, budete muset implementovat autorizaci prostřednictvím OAuth 2.0 generovat potřebné tokeny. 

### <a name="implementation"></a>Implementace

Společnost Microsoft neposkytuje knihovny pro implementaci SAML ani nedoporučuje konkrétní knihovny. K dispozici je mnoho knihoven s otevřeným zdrojovým kódem.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>Přiřazování a přibíjení a používání rozhraní Microsoft Graph Rest API 

Microsoft Graph je datový nástroj ve všech microsoft365, včetně Office 365, Windows 10 a Enterprise Mobility and Security a dalších produktů, jako je Dynamics 365. To zahrnuje základní schémata entit, jako jsou uživatelé, skupiny, kalendář, pošta, soubory a další, které zvyšují produktivitu uživatelů. Microsoft Graph nabízí tři rozhraní pro vývojáře rest založené rozhraní API, Microsoft Graph data connect a konektory, které umožňují vývojářům přidávat vlastní data do Microsoft Graphu.  

Použití některého z výše uvedených protokolů pro spřibíka umožňuje vaší aplikaci přístup k bohatým datům, které jsou k dispozici prostřednictvím rozhraní API MICROSOFT Graph REST. To umožňuje zákazníkům získat větší hodnotu z jejich investice do Microsoft365. Vaše aplikace může například volat rozhraní Microsoft Graph API pro integraci s instancemi office 365 vašich zákazníků a položkami Microsoft Office a SharePoint uživatelů povrchu v rámci vaší aplikace. 

Pokud používáte Open ID Connect k ověření, pak vaše vývojové prostředí je bezproblémové, protože budete používat OAuth2, základ Open ID Connect, získat tokeny lze použít pro vyvolání rozhraní API Microsoft Graph. Pokud vaše aplikace používá SAML nebo WSFed, musíte přidat další kód v rámci aplikace získat tyto OAuth2 získat tokeny potřebné k vyvolání rozhraní API Aplikace Microsoft Graph. 

## <a name="next-steps"></a>Další kroky

[Povolení jednotného přihlašování pro aplikaci s více tenanty](isv-sso-content.md)

[Vytvoření dokumentace pro víceklientské aplikace](isv-create-sso-documentation.md)
