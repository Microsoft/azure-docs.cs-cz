---
title: Vyberte protokol správné federation pro vaše aplikace s více tenanty
description: Pokyny pro nezávislé dodavatele softwaru na integraci se službou Azure Active Directory
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
ms.openlocfilehash: 53b315b87200b37cda215a29a65be9babaf54f43
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795200"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Vyberte protokol správné federation pro vaše aplikace s více tenanty

Při vývoji vaší aplikace software jako služba (SaaS), musíte vybrat protokol federace, která nejlépe vyhovuje potřebám vaší zákazníků. Toto rozhodnutí je založen na vývojářské platformy a vaše požadavky na integraci s daty, které jsou k dispozici v ekosystému vaši zákazníci Office 365 a Azure AD.

Zobrazit úplný seznam [protokoly, které jsou k dispozici pro jednotné přihlašování integrace](what-is-single-sign-on.md) službou Azure Active Directory.
Následující tabulka porovnává 
* Otevřete ověřování 2.0 (OAuth 2.0)
* Open ID Connect (OIDC)
* Security Assertion Markup Language (SAML)
* Web Services Federation (WSFed)

| Funkce| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| Webové jednotné přihlašování| √| √ |
| Webové jedním odhlášení| √| √ |
| Na základě Mobile jednotného přihlašování| √| √* |
| Na základě Mobile jedním odhlášení| √| √* |
| Zásady podmíněného přístupu pro mobilní aplikace| √| X |
| Bezproblémové prostředí vícefaktorové ověřování pro mobilní aplikace| √| X |
| Přístup k Microsoft Graphu| √| X |

\* Možná, ale Microsoft neposkytuje ukázky nebo pokyny.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 a Open ID Connect

OAuth 2.0 je [standardní](https://oauth.net/2/) protokol pro ověřování. OIDC (OpenID Connect) je [standardní](https://openid.net/connect/) vrstvu ověřování identit postavené na protokol Oath 2.0.

### <a name="benefits"></a>Výhody

Microsoft doporučuje používat OIDC/OAuth 2.0 mají ověřování a autorizace součástí protokolů. Pomocí SAML musí také implementovat autorizace.

Povolení vyplývajících z těchto protokolů umožňuje vaší aplikaci přístup a integrace s rozsáhlým uživatelským datům a organizace prostřednictvím rozhraní Microsoft Graph API.

Pomocí OAuth 2.0 a OIDC zjednodušuje zkušenosti vašich koncových uživatelů při přijímání jednotného přihlašování pro vaši aplikaci. Můžete snadno definovat nezbytné sady oprávnění, která jsou pak automaticky reprezentovány do správce či koncového uživatele souhlas.

Kromě toho pomocí těchto protokolů umožňuje zákazníkům používat podmíněný přístup a vícefaktorového ověřování zásad řízení přístupu k aplikacím. Společnost Microsoft poskytuje knihovny a [ukázky kódu napříč různými platformami technologie](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) pro podporu vývoje.  

### <a name="implementation"></a>Implementace

Registrace aplikace pomocí Microsoft Identity, která je poskytovatel OAuth 2.0. Potom může také registrace aplikace na základě OAuth 2.0 pomocí žádného dalšího zprostředkovatele Identity, kterou chcete integrovat. 

Informace o tom, jak zaregistrovat aplikaci a implementovat tyto protokoly pro jednotné přihlašování do služby web apps, najdete v tématu [autorizaci přístupu k webovým aplikacím pomocí OpenID Connect a službou Azure Active Directory](../develop/sample-v2-code.md).  Informace o tom, jak implementovat tyto protokoly pro jednotné přihlašování v mobilních aplikacích naleznete v následujících tématech: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Univerzální platforma Windows](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>Protokol SAML 2.0 a WSFed

Zabezpečení kontrolního výrazu SAML (Markup Language) se obvykle používá pro webové aplikace. Zobrazit [Azure používá protokol SAML](../develop/active-directory-saml-protocol-reference.md) Přehled. 

Web Services Federation (WSFed) je [standardní](http://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) obvykle používá pro webové aplikace, které jsou vyvíjeny pomocí platformy .net.

### <a name="benefits"></a>Výhody

Protokol SAML 2.0 je vyspělá standard a většinu technologie platformy podporu open source knihoven pro protokol SAML 2.0. Rozhraní pro správu konfigurace jednotného přihlašování SAML, můžete zadat vaše zákazníky. Konfigurace jednotného přihlašování SAML pro Microsoft Azure AD a žádného dalšího zprostředkovatele identity, který podporuje SAML 2

### <a name="trade-offs"></a>Nevýhody

Při použití přes protokol SAML 2.0 nebo WSFed pro mobilní aplikace, bude mít některé zásady podmíněného přístupu, včetně služby Multi-Factor Authentication (MFA) sníženými možnostmi. Kromě toho pokud chcete přístup k Microsoft Graphu, je potřeba implementovat autorizace OAuth 2.0 ke generování tokenů nezbytné. 

### <a name="implementation"></a>Implementace

Microsoft neposkytuje knihovny pro implementaci SAML ani doporučit konkrétní knihovny. Nejsou k dispozici řada open source knihoven.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>Jednotné přihlašování a Microsoft Graph Rest pomocí rozhraní API 

Microsoft Graph je fabric dat napříč všemi Microsoft 365, včetně Office 365, Windows 10 a Enterprise Mobility a Security a další produkty, jako je Dynamics 365. To zahrnuje základní schémata entit jako uživatelé, skupiny, kalendáře, e-mailu, souborů a další, tento zvýšení produktivity uživatelů. Microsoft Graph nabízí tři rozhraní pro vývojáře REST API založené na datech Microsoft Graphu připojení a konektory, které umožňují vývojářům pro přidání vlastních dat do Microsoft Graphu.  

Pomocí kteréhokoli z výše uvedených protokolů pro jednotné přihlašování umožňuje vaší aplikaci přístup k bohaté data k dispozici prostřednictvím rozhraní Microsoft Graph REST API. To umožňuje zákazníkům, aby vytěžit více z jejich investic do Microsoftu 365. Vaše aplikace například můžete volat rozhraní Microsoft Graph API k integraci s instance vaši zákazníci Office 365 a surface uživatelům Microsoft Office a SharePoint položky v rámci vaší aplikace. 

Pokud používáte Open ID Connect k ověření, pak vaše zkušenosti s vývojem je bezproblémové, protože ho použijete OAuth2, základem Open ID Connect, získat tokeny lze použít pro volání rozhraní Microsoft Graph API. Pokud vaše aplikace používá SAML nebo WSFed, je nutné přidat další kód v rámci vaší aplikace, abyste získali tyto OAuth2 k získání tokenů požadované k vyvolání rozhraní Microsoft Graph API. 

## <a name="next-steps"></a>Další kroky

[Povolení jednotného přihlašování pro vaše aplikace s více tenanty](isv-sso-content.md)

[Vytváření dokumentace pro vaši aplikaci s více tenanty](isv-create-sso-documentation.md)
