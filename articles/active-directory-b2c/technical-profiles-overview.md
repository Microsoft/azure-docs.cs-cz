---
title: Přehled technických profilů ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Přečtěte si, jak se v Azure Active Directory B2C používají technické profily ve vlastních zásadách.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d61471e07dda8fcf0c715dcffe85ad3e39ed8ac3
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840368"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Informace o technických profilech v Azure Active Directory B2C vlastních zásadách

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Technický profil poskytuje rozhraní s integrovaným mechanismem pro komunikaci s různými typy stran pomocí vlastních zásad v Azure Active Directory B2C (Azure AD B2C). Technické profily se používají ke komunikaci s vaším klientem Azure AD B2C, k vytvoření uživatele nebo čtení profilu uživatele. Technický profil může být vlastním uplatněním, aby bylo možné interakci s uživatelem. Například Shromážděte přihlašovací údaje uživatele pro přihlášení a pak vykreslete stránku pro registraci nebo resetování hesla.

## <a name="type-of-technical-profiles"></a>Typ technických profilů

Technický profil umožňuje tyto typy scénářů:

- [Application Insights](application-insights-technical-profile.md) – odesílání dat události do [Application Insights](../azure-monitor/app/app-insights-overview.md).
- [Azure Active Directory](active-directory-technical-profile.md) – poskytuje podporu pro správu uživatelů Azure Active Directory B2C.
- [Azure ad Multi-Factor Authentication](multi-factor-auth-technical-profile.md) – poskytuje podporu pro ověření telefonního čísla pomocí Azure AD Multi-Factor Authentication (MFA). 
- [Transformace deklarací identity](claims-transformation-technical-profile.md) – volá výstupní transformace deklarací identity za účelem manipulace s hodnotami deklarací identity, ověřování deklarací identity nebo nastavení výchozích hodnot pro sadu výstupních deklarací.
- [Pomocný parametr tokenu ID](id-token-hint.md) – ověřuje `id_token_hint` signaturu tokenu JWT, název vystavitele a cílovou skupinu tokenů a extrahuje deklaraci identity z příchozího tokenu.
- [Vystavitel tokenu JWT](jwt-issuer-technical-profile.md) – vygeneruje token JWT, který se vrátí zpět do aplikace předávající strany.
- [OAuth1](oauth1-technical-profile.md) – federace s jakýmkoli poskytovatelem identity protokolu OAuth 1,0.
- [OAuth2](oauth2-technical-profile.md) – federace s jakýmkoli poskytovatelem identity protokolu OAuth 2,0.
- [Jednorázové heslo](one-time-password-technical-profile.md) – poskytuje podporu pro správu generování a ověřování jednorázového hesla.
- [OpenID Connect](openid-connect-technical-profile.md) -Federation s libovolným poskytovatelem identity protokolu OpenID Connect.
- [Phone Factor](phone-factor-technical-profile.md) – podpora registrace a ověření telefonních čísel.
- [Poskytovatel RESTful](restful-technical-profile.md) – volání služeb REST API Services, například ověření vstupu uživatele, rozšíření uživatelských dat nebo integrace s podnikovými aplikacemi.
- [Zprostředkovatel identity SAML](saml-identity-provider-technical-profile.md) – federace s jakýmkoli poskytovatelem identity protokolu SAML
- [Vystavitel tokenu SAML](saml-issuer-technical-profile.md) – vygeneruje token SAML, který se vrátí zpátky do aplikace předávající strany.
- Uživatel s [vlastním uplatněním](self-asserted-technical-profile.md) : interakce s uživatelem. Například Shromážděte přihlašovací údaje uživatele pro přihlášení, vykreslete stránku pro registraci nebo resetování hesla.
- [Správa relací](custom-policy-reference-sso.md) – zpracovává různé typy relací.

## <a name="technical-profile-flow"></a>Tok technického profilu

Všechny typy technických profilů sdílejí stejný koncept. Můžete posílat vstupní deklarace identity, spouštět transformaci deklarací identity a komunikovat s nakonfigurovanou stranou, jako je zprostředkovatel identity, REST API nebo adresářové služby Azure AD. Po dokončení procesu bude technický profil vracet výstupní deklarace identity a může spustit transformaci deklarací identity. Následující diagram znázorňuje, jak se zpracovávají transformace a mapování, na které se odkazuje v technickém profilu. Bez ohledu na to, v jaké straně technický profil spolupracuje, se po provedení jakékoli transformace deklarací identity z technického profilu hned uloží i deklarace identity z technického profilu do kontejneru deklarací identity.

![Diagram znázorňující tok technického profilu](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)

1. **Správa relací jednotného přihlašování (SSO)** – obnoví stav relace technického profilu pomocí [správy relace jednotného](custom-policy-reference-sso.md)přihlašování.
1. **Transformace vstupních deklarací** – vstupní deklarace identity pro každou vstupní [transformaci](claimstransformations.md) deklarací se vybírají z kontejneru deklarací identity.  Deklarace výstupů vstupní deklarace identity můžou být vstupními deklaracemi za následné transformace vstupních deklarací.
1. **Vstupní deklarace identity** – deklarace se vybírají z kontejneru deklarací identity a používají se pro technický profil. Například [technický profil s vlastním uplatněním](self-asserted-technical-profile.md) používá vstupní deklarace identity, které vyplní výstupní deklarace identity, které uživatel poskytuje. REST API technický profil používá vstupní deklarace identity k posílání vstupních parametrů do koncového bodu REST API. Azure Active Directory používá pro čtení, aktualizaci nebo odstranění účtu vstupní deklaraci identity jako jedinečný identifikátor.
1. **Provádění technického profilu** – technický profil vyměňuje deklarace identity s konfigurovanou stranou. Například:
    - Přesměrujte uživatele na zprostředkovatele identity, abyste mohli dokončit přihlášení. Po úspěšném přihlášení se uživatel vrátí zpět a provádění technického profilu bude pokračovat.
    - Volání REST API při posílání parametrů jako InputClaims a získání informací zpět jako OutputClaims.
    - Vytvořte nebo aktualizujte uživatelský účet.
    - Odešle a ověří textovou zprávu MFA.
1. **Ověření Technical** Profiles – [technický profil s vlastním uplatněním](self-asserted-technical-profile.md) může volat [technické profily ověřování](validation-technical-profile.md). Technický profil ověření ověřuje data profilovaná uživatelem a vrací chybovou zprávu nebo je v pořádku s výstupními deklaracemi nebo bez nich. Například předtím, než Azure AD B2C vytvoří nový účet, zkontroluje, zda uživatel již existuje v adresářových službách. Můžete zavolat REST API technický profil a přidat vlastní obchodní logiku.<p>Rozsah výstupních deklarací identity technického profilu ověření je omezený na technický profil, který volá technický profil ověření. a další technické profily pro ověřování ve stejném technickém profilu. Pokud chcete použít výstupní deklarace v dalším kroku orchestrace, je nutné přidat výstupní deklarace identity do technického profilu, který vyvolá technický profil ověření.
1. **Deklarace** identity na výstup – deklarace identity se vrátí zpět do kontejneru deklarací identity. Tyto deklarace identity můžete použít v dalších krokech orchestrace nebo z výstupních transformací deklarací.
1. **Transformace výstupních deklarací** – vstupní deklarace všech transformací výstupních [deklarací](claimstransformations.md) se vybírají z kontejneru deklarací identity. Výstupní deklarace identity technického profilu z předchozích kroků můžou být vstupními deklaracemi výstupní transformace deklarací identity. Po spuštění se výstupní deklarace identity vrátí do kontejneru deklarací identity. Výstupní deklarace výstupní transformace deklarací identity můžou také vstupní deklarace identity následné výstupní transformace deklarací identity.
1. **Správa relací jednotného přihlašování (SSO)** – uchovává data technického profilu do relace pomocí [správy relací jednotného](custom-policy-reference-sso.md)přihlašování.


## <a name="technical-profile-inclusion"></a>Zahrnutí technického profilu

Technický profil může zahrnovat jiný technický profil pro změnu nastavení nebo přidání nových funkcí.  `IncludeTechnicalProfile`Element je odkazem na základní technický profil, ze kterého je odvozen technický profil. Počet úrovní není nijak omezený.

Například technický profil **AAD-UserReadUsingAlternativeSecurityId-inerror** zahrnuje **AAD-UserReadUsingAlternativeSecurityId**. Tento technický profil nastaví `RaiseErrorIfClaimsPrincipalDoesNotExist` položku metadat na `true` a vyvolá chybu, pokud účet sociální sítě v adresáři neexistuje. **AAD-UserReadUsingAlternativeSecurityId-** informování tohoto chování potlačí a zakáže tuto chybovou zprávu.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** zahrnuje `AAD-Common` technický profil.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Parametr **AAD-UserReadUsingAlternativeSecurityId-inerror** a  **AAD-UserReadUsingAlternativeSecurityId** nezaurčují požadovaný element **protokolu** , protože je zadaný v technickém profilu **AAD-Common** .

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```
