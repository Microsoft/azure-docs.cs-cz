---
title: O technické profily ve vlastních zásad Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o tom, jak technické profily, které se používají ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 0f36cd798faac275e0f6dcb8a81bd37e14ab6d8d
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275815"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>O technické profily v Azure Active Directory B2C vlastních zásad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Technický profil poskytuje architekturu s předdefinovaný mechanismus pro komunikaci s jiný typ stran pomocí vlastních zásad v Azure Active Directory (Azure AD) B2C. Technické profily se používají ke komunikaci s vašeho tenanta Azure AD B2C, vytvořit uživatele nebo čtení profilu uživatele. Technický profil může být s vlastním potvrzením umožňují interakci s uživatelem. Například můžete shromažďovat přihlašovací údaje uživatele k přihlášení a následnému vykreslení na registrační stránku nebo stránce pro resetování hesla. 

## <a name="type-of-technical-profiles"></a>Typ technické profily

Technický profil umožňuje tyto druhy scénářů:

- [Azure Active Directory](active-directory-technical-profile.md) – poskytuje podporu pro správu uživatelů Azure Active Directory B2C.
- [Vydavatel tokenu JWT](jwt-issuer-technical-profile.md) -vydá token JWT, který se vrátí zpět do aplikace předávající strany. 
- **Poskytovatele Multi-Factor Phone** – ověřování službou Multi-Factor Authentication.
- [OAuth1](oauth1-technical-profile.md) -federace se službou jakýkoli poskytovatel identity protokolu OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md) -federace se službou jakýkoli poskytovatel identity protokol OAuth 2.0.
- [OpenIdConnect](openid-connect-technical-profile.md) -federace se službou jakýkoli poskytovatel identity protokolu OpenId Connect.
- [Transformace deklarací identity](claims-transformation-technical-profile.md) – transformace deklarací výstup volání k manipulaci s deklarací hodnoty, ověřování deklarací identity nebo nastavit výchozí hodnoty pro sadu výstupní deklarace identit.
- [Poskytovatel RESTful](restful-technical-profile.md) – obohacení dat uživatele volání rozhraní REST API služby, jako je ověření vstupu uživatele, nebo integrovat – obchodní aplikace.
- [SAML2](saml-technical-profile.md) -federace se službou jakýkoli poskytovatel identity protokolu SAML.
- [Samoobslužné s prohlašovanou](self-asserted-technical-profile.md) -komunikovat s uživatelem. Například shromažďovat přihlašovací údaje uživatele k přihlášení, vykreslení na stránku pro přihlášení nebo resetování hesla.
- **WsFed** -federace se službou jakýkoli poskytovatel identity WsFed protokolu. 
- [Správa relací](active-directory-b2c-reference-sso-custom.md) – zpracování různých typů relací. 
- **Application insights**

## <a name="technical-profile-flow"></a>Technický profil toku

Všechny typy technické profily sdílet stejný koncept. Odeslání vstupních deklarací identity, spouštět transformace deklarací identity a komunikovat s straně nakonfigurované jako zprostředkovatele identity, rozhraní REST API nebo adresář služby Azure AD. Po dokončení procesu technický profil vrátí výstupní deklarace identit a mohou spouštět výstup transformace deklarací identity. Následující diagram znázorňuje, jak se zpracovávají transformace a mapování odkazované v technickém profilu. Bez ohledu na to, kterou technický profil komunikuje za všechny deklarace, který provádí transformaci, strana výstupní deklarace identit od technického profilu okamžitě jsou uloženy v kontejneru a deklarace identity. 

![Technický profil toku](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **InputClaimsTransformation** – vstupní deklarace identity každý vstupu [transformace deklarací identity](claimstransformations.md) se vezmou z kontejneru deklarace identity a po spuštění, výstupní deklarace identity se vrátit zpět v kontejneru a deklarace identity. Výstupní deklarace identit transformaci vstupních deklarací identity může být vstupní deklarace identity transformace dalších vstupních deklarací identity.
2. **InputClaims** – deklarace identity se vybírají z kontejneru deklarace identity a slouží pro technický profil. Například [držitelem s prohlašovanou technický profil](self-asserted-technical-profile.md) používá vstupní deklarace identity předem výstupní deklarace identit, které uživatel zadá. Technický profil rozhraní REST API mezi vstupními deklaracemi identity používá k odesílání vstupní parametry pro koncový bod rozhraní REST API. Azure Active Directory vstupní deklaraci identity používá jako jedinečný identifikátor číst, aktualizovat nebo odstranit účet.
3. **Technický profil spuštění** – technický profil výměny deklarací identity s straně nakonfigurované. Příklad:
    - Přesměruje uživatele ke zprostředkovateli identity k dokončení přihlášení. Po úspěšném přihlášení se uživatel vrátí zpět a pokračuje v provádění technický profil.
    - Při odesílání parametry jako InputClaims a získávání informací zpět jako OutputClaims volání rozhraní REST API.
    - Vytvořit nebo aktualizovat uživatelský účet.
    - Odešle a ověří textovou zprávu vícefaktorové ověřování.
4. **ValidationTechnicalProfiles** – [samopopisné technický profil](self-asserted-technical-profile.md), můžete volat vstup [technický profil ověření](validation-technical-profile.md). Technický profil ověření ověřuje data profilované uživatelem a vrátí chybovou zprávu nebo Ok, s nebo bez něj výstupní deklarace identit. Například předtím, než Azure AD B2C vytvoří nový účet, zkontroluje, zda uživatel již existuje v adresářové služby. Můžete volat rozhraní REST API technický profil přidat vlastní obchodní logikou.<p>Rozsah výstupní deklarace identit technického profilu ověření je omezená na technický profil, který volá technický profil ověření a další technické profily ověřování v rámci stejné technický profil. Pokud chcete použít výstupní deklarace identit v dalším kroku Orchestrace, budete muset přidat výstupní deklarace identit technický profil, který vyvolá technický profil ověření.
5. **OutputClaims** – deklarace identity se vrátíte zpět do kontejneru deklarací identity. Můžete použít tyto deklarace identit v dalším kroku Orchestrace nebo výstup transformace deklarací identity.
6. **OutputClaimsTransformations** – vstupní deklarace identity každý výstupu [transformace deklarací identity](claimstransformations.md) byly vybrány z kontejneru deklarací identity. Výstupní deklarace identit technického profilu z předchozích kroků může být vstupní deklarace identity transformace deklarací identity výstup. Po spuštění jsou v kontejneru a deklarace identity vrátit výstupní deklarace identit. Výstupní deklarace identity transformace deklarací identity výstup může být také vstupními deklaracemi identity transformace deklarací identity následující výstup.
7. **Jednotné přihlašování (SSO) Správa relací** - [Správa relací jednotného přihlašování](active-directory-b2c-reference-sso-custom.md) ovládací prvky interakce s uživatelem, až uživatel už provedl ověření. Správce může například řídit, určuje, zda je zobrazena škály zprostředkovatelů identity, nebo zda místní účet podrobnosti nutné znovu zadat.

Technický profil může dědit z jiné technického profilu nastavení změnit nebo přidat nové funkce.  **IncludeTechnicalProfile** element je odkaz na základní technický profil, ze kterého je odvozen technický profil.  

Například **AAD. UserReadUsingAlternativeSecurityId NoError** zahrnuje technický profil **AAD UserReadUsingAlternativeSecurityId**. Tento technický profil nastaví **RaiseErrorIfClaimsPrincipalDoesNotExist** položku metadat pro `true`a vyvolá chybu, pokud účet na sociální síti neexistuje v adresáři. **AAD – UserReadUsingAlternativeSecurityId NoError** přepsání tohoto chování a zakáže se chybová zpráva, pokud uživatel.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
``` 

**AAD – UserReadUsingAlternativeSecurityId** zahrnuje `AAD-Common` technický profil.

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

Obě **AAD. UserReadUsingAlternativeSecurityId NoError** a **AAD UserReadUsingAlternativeSecurityId** nezadávejte požadované **protokol** element protože je zadán v **AAD běžné** technický profil.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

Technický profil může obsahovat ani nedědí jiný technický profil, který může obsahovat jiný. Neexistuje žádné omezení počtu zadaných úrovní. V závislosti na obchodních požadavcích, může volat vaši cestu uživatele **AAD UserReadUsingAlternativeSecurityId** , která vyvolává chybu, pokud účet na sociální síti uživatele neexistuje, nebo  **AAD – UserReadUsingAlternativeSecurityId NoError** které nelze vyvolat chyby.











