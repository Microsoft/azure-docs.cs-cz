---
title: Informace o technických profilech v Azure Active Directory B2C vlastních zásadách | Microsoft Docs
description: Přečtěte si, jak se ve službě Azure Active Directory B2C používají technické profily ve vlastních zásadách.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f90b69cde4a961457c987f004e2605e6884bf323
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063332"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Informace o technických profilech v Azure Active Directory B2C vlastních zásadách

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Technický profil poskytuje rozhraní s integrovaným mechanismem pro komunikaci s různými typy stran pomocí vlastních zásad v Azure Active Directory B2C (Azure AD B2C). Technické profily se používají ke komunikaci s vaším klientem Azure AD B2C, k vytvoření uživatele nebo čtení profilu uživatele. Technický profil může být vlastním uplatněním, aby bylo možné interakci s uživatelem. Například Shromážděte přihlašovací údaje uživatele pro přihlášení a pak vykreslete stránku pro registraci nebo resetování hesla.

## <a name="type-of-technical-profiles"></a>Typ technických profilů

Technický profil umožňuje tyto typy scénářů:

- [Azure Active Directory](active-directory-technical-profile.md) – poskytuje podporu pro správu uživatelů Azure Active Directory B2C.
- [Vystavitel tokenu JWT](jwt-issuer-technical-profile.md) – vygeneruje token JWT, který se vrátí zpět do aplikace předávající strany.
- **Poskytovatel telefonního faktoru** – Multi-Factor Authentication.
- [OAuth1](oauth1-technical-profile.md) – federace s jakýmkoli poskytovatelem identity protokolu OAuth 1,0.
- [OAuth2](oauth2-technical-profile.md) – federace s jakýmkoli poskytovatelem identity protokolu OAuth 2,0.
- [OpenID Connect](openid-connect-technical-profile.md) -Federation s libovolným poskytovatelem identity protokolu OpenID Connect.
- [Transformace deklarací identity](claims-transformation-technical-profile.md) – volá výstupní transformace deklarací identity za účelem manipulace s hodnotami deklarací identity, ověřování deklarací identity nebo nastavení výchozích hodnot pro sadu výstupních deklarací.
- [Poskytovatel RESTful](restful-technical-profile.md) – volání služeb REST API Services, například ověření vstupu uživatele, rozšíření uživatelských dat nebo integrace s podnikovými aplikacemi.
- [Typu Saml2](saml-technical-profile.md) – federace s libovolným poskytovatelem identity protokolu SAML.
- Uživatel s [vlastním uplatněním](self-asserted-technical-profile.md) : interakce s uživatelem. Například Shromážděte přihlašovací údaje uživatele pro přihlášení, vykreslete stránku pro registraci nebo resetování hesla.
- [Správa relací](active-directory-b2c-reference-sso-custom.md) – zpracovává různé typy relací.
- **Application Insights**

## <a name="technical-profile-flow"></a>Tok technického profilu

Všechny typy technických profilů sdílejí stejný koncept. Můžete posílat vstupní deklarace identity, spouštět transformaci deklarací identity a komunikovat s nakonfigurovanou stranou, jako je zprostředkovatel identity, REST API nebo adresářové služby Azure AD. Po dokončení procesu vrátí technický profil výstupní deklarace identity a může spustit transformaci deklarací identity. Následující diagram znázorňuje, jak se zpracovávají transformace a mapování, na které se odkazuje v technickém profilu. Bez ohledu na to, v jaké straně technický profil spolupracuje, se po provedení jakékoli transformace deklarací identity z technického profilu hned uloží i deklarace identity z technického profilu do kontejneru deklarací identity.

![Diagram znázorňující tok technického profilu](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **InputClaimsTransformation** – vstupní deklarace identity každé [transformace vstupních deklarací](claimstransformations.md) se vybírají z kontejneru deklarací identity a po provedení se výstupní deklarace identity vrátí do kontejneru deklarací identity. Deklarace výstupů vstupní deklarace identity můžou být vstupními deklaracemi za následné transformace vstupních deklarací.
2. **InputClaims** – deklarace identity se vybírají z kontejneru deklarací identity a používají se pro technický profil. Například [technický profil s vlastním uplatněním](self-asserted-technical-profile.md) používá vstupní deklarace identity, které vyplní výstupní deklarace identity, které uživatel poskytuje. REST API technický profil používá vstupní deklarace identity k posílání vstupních parametrů do koncového bodu REST API. Azure Active Directory používá pro čtení, aktualizaci nebo odstranění účtu vstupní deklaraci identity jako jedinečný identifikátor.
3. **Provádění technického profilu** – technický profil vyměňuje deklarace identity s konfigurovanou stranou. Příklad:
    - Přesměrujte uživatele na zprostředkovatele identity, abyste mohli dokončit přihlášení. Po úspěšném přihlášení se uživatel vrátí zpět a provádění technického profilu bude pokračovat.
    - Volání REST API při posílání parametrů jako InputClaims a získání informací zpět jako OutputClaims.
    - Vytvořte nebo aktualizujte uživatelský účet.
    - Odešle a ověří textovou zprávu MFA.
4. **ValidationTechnicalProfiles** – pro [technický profil s vlastním uplatněním](self-asserted-technical-profile.md)můžete zavolat [technický profil pro ověření](validation-technical-profile.md)vstupu. Technický profil ověření ověřuje data profilovaná uživatelem a vrací chybovou zprávu nebo je v pořádku s výstupními deklaracemi nebo bez nich. Například předtím, než Azure AD B2C vytvoří nový účet, zkontroluje, zda uživatel již existuje v adresářových službách. Můžete zavolat REST API technický profil a přidat vlastní obchodní logiku.<p>Rozsah výstupních deklarací pro technický profil ověření je omezený na technický profil, který volá technický profil ověřování a další technické profily ověřování ve stejném technickém profilu. Pokud chcete použít výstupní deklarace v dalším kroku orchestrace, je nutné přidat výstupní deklarace identity do technického profilu, který vyvolá technický profil ověření.
5. **OutputClaims** – deklarace identity se vrátí zpět do kontejneru deklarací identity. Tyto deklarace identity můžete použít v dalších krokech orchestrace nebo z výstupních transformací deklarací.
6. **OutputClaimsTransformations** – vstupní deklarace všech výstupních [transformací deklarací identity](claimstransformations.md) se vybírají z kontejneru deklarací identity. Výstupní deklarace identity technického profilu z předchozích kroků můžou být vstupními deklaracemi výstupní transformace deklarací identity. Po spuštění se výstupní deklarace identity vrátí do kontejneru deklarací identity. Výstupní deklarace výstupní transformace deklarací identity můžou také vstupní deklarace identity následné výstupní transformace deklarací identity.
7. **Správa relace jednotného přihlašování (SSO)**  - Správa relace[jednotného](active-directory-b2c-reference-sso-custom.md) přihlašování řídí interakci s uživatelem po ověření uživatele. Správce může třeba určit, jestli se má zobrazit výběr zprostředkovatelů identity, nebo jestli se mají zadat podrobnosti o místním účtu znovu.

Technický profil může dědit z jiného technického profilu a změnit nastavení nebo přidat nové funkce.  Element **IncludeTechnicalProfile** je odkazem na základní technický profil, ze kterého je odvozen technický profil.

Například technický profil **AAD-UserReadUsingAlternativeSecurityId-inerror** zahrnuje **AAD-UserReadUsingAlternativeSecurityId**. Tento technický profil nastaví položku metadat **RaiseErrorIfClaimsPrincipalDoesNotExist** na `true`a vyvolá chybu, pokud účet sociální sítě v adresáři neexistuje. **AAD-UserReadUsingAlternativeSecurityId-** informování tohoto chování potlačí a zakáže chybovou zprávu, pokud uživatel neexistoval.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** zahrnuje `AAD-Common` technický profil.

```XML
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

Parametr **AAD-UserReadUsingAlternativeSecurityId-inerror** a **AAD-UserReadUsingAlternativeSecurityId** Nezaurčují požadovaný element **protokolu** , protože je zadaný v technickém profilu **AAD-Common** .

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

Technický profil může zahrnovat nebo zdědit jiný technický profil, který může obsahovat další. Počet úrovní není nijak omezený. V závislosti na podnikových požadavcích může vaše cesta uživatele volat **AAD-UserReadUsingAlternativeSecurityId** , která vyvolá chybu, pokud účet sociální sítě uživatele neexistuje nebo pokud není k dispozici **Chyba AAD-UserReadUsingAlternativeSecurityId-** bez vyvolá chybu.











