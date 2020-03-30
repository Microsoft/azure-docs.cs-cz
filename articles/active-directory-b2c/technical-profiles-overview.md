---
title: Přehled technických profilů ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Zjistěte, jak se technické profily používají ve vlastních zásadách ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5d89d123fe757b0502f4c6b3a0d33c185b25224b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057315"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Technické profily ve vlastních zásadách služby Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Technický profil poskytuje rámec s integrovaným mechanismem pro komunikaci s různými typy stran pomocí vlastní zásady v Azure Active Directory B2C (Azure AD B2C). Technické profily se používají ke komunikaci s klientem Azure AD B2C, k vytvoření uživatele nebo ke čtení profilu uživatele. Technický profil lze uplatnit samostatně, aby bylo možné s uživatelem. Můžete například shromáždit pověření uživatele pro přihlášení a potom vykreslit registrační stránku nebo stránku pro obnovení hesla.

## <a name="type-of-technical-profiles"></a>Typ technických profilů

Technický profil umožňuje tyto typy scénářů:

- [Application Insights](application-insights-technical-profile.md) – odesílání dat událostí do [Application Insights](../azure-monitor/app/app-insights-overview.md).
- [Azure Active Directory](active-directory-technical-profile.md) – poskytuje podporu pro správu uživatelů služby Azure Active Directory B2C.
- [Azure Multi-Factor Authentication](multi-factor-auth-technical-profile.md) – poskytuje podporu pro ověření telefonního čísla pomocí Azure Multi-Factor Authentication (MFA). 
- [Transformace deklarací](claims-transformation-technical-profile.md) – volání transformace deklarací výstupu k manipulaci s hodnotami deklarací identity, ověření deklarací nebo nastavení výchozích hodnot pro sadu výstupních deklarací.
- [JWT token vystavitel](jwt-issuer-technical-profile.md) - vyzařuje token JWT, který je vrácen zpět do aplikace předávající strany.
- [OAuth1](oauth1-technical-profile.md) - Federace s jakýmkoli poskytovatelem identity protokolu OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md) - Federace s jakýmkoli poskytovatelem identity protokolu OAuth 2.0.
- [Jednorázové heslo](one-time-password-technical-profile.md) – poskytuje podporu pro správu generování a ověřování jednorázového hesla.
- [OpenID Connect](openid-connect-technical-profile.md) - Federace s jakýmkoli poskytovatelem identity protokolu OpenID Connect.
- [Faktor telefonu](phone-factor-technical-profile.md) – podpora pro registraci a ověření telefonních čísel.
- [Restful zprostředkovatel](restful-technical-profile.md) – volání rest API služby, jako je například ověření vstupu uživatele, obohatit uživatelská data nebo integrovat s obchodními aplikacemi.
- [SAML2](saml-technical-profile.md) - Federace s libovolným poskytovatelem identity protokolu SAML.
- [Saml token issuer](saml-issuer-technical-profile.md) - vyzařuje token SAML, který je vrácen zpět do aplikace předávající strany.
- [Self-Asserted](self-asserted-technical-profile.md) - Interakce s uživatelem. Můžete například shromáždit pověření uživatele pro přihlášení, vykreslení registrační stránky nebo obnovení hesla.
- [Správa relací](custom-policy-reference-sso.md) – zpracovává různé typy relací.

## <a name="technical-profile-flow"></a>Tok technického profilu

Všechny typy technických profilů mají stejný koncept. Odesíláte vstupní deklarace identity, spouštějte transformaci deklarací identity a komunikujete s nakonfigurovanou stranou, jako je například poskytovatel identity, rozhraní REST API nebo adresářové služby Azure AD. Po dokončení procesu technický profil vrátí výstupní deklarace identity a může spustit transformaci deklarací výstupu. Následující diagram znázorňuje, jak jsou zpracovány transformace a mapování odkazované v technickém profilu. Bez ohledu na stranu, se kterou technický profil spolupracuje, po provedení jakékoli transformace deklarací jsou výstupní deklarace z technického profilu okamžitě uloženy v vaku deklarací identity.

![Diagram znázorňující tok technického profilu](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **Správa relace jednotného přihlašování (SSO)** – obnoví stav relace technického profilu pomocí [správy relací jednotného přihlašování](custom-policy-reference-sso.md).
1. **Vstupní deklarace transformace** - Vstupní deklarace každé [vstupní deklarace transformace](claimstransformations.md) jsou vyzvednuty z vaku deklarací identity.  Výstupní deklarace transformace vstupních deklarací mohou být vstupní deklarace následné transformace vstupních deklarací.
1. **Vstupní nároky** - Nároky jsou vyzvednuty z reklamačního vaku a používají se pro technický profil. Například [vlastní uplatněný technický profil](self-asserted-technical-profile.md) používá vstupní deklarace k předběžnému vyplnění výstupních deklarací, které uživatel poskytuje. Technický profil rozhraní REST API používá vstupní deklarace k odeslání vstupních parametrů do koncového bodu rozhraní REST API. Služba Azure Active Directory používá vstupní deklaraci jako jedinečný identifikátor ke čtení, aktualizaci nebo odstranění účtu.
1. **Provádění technického profilu** - Technický profil vyměňuje deklarace s nakonfigurovanou stranou. Například:
    - Přesměrujte uživatele na poskytovatele identity a dokončete přihlášení. Po úspěšném přihlášení se uživatel vrátí zpět a provádění technického profilu pokračuje.
    - Volání rozhraní REST API při odesílání parametrů jako InputClaims a získávání informací zpět jako OutputClaims.
    - Vytvořte nebo aktualizujte uživatelský účet.
    - Odešle a ověří textovou zprávu MFA.
1. **Validace technických profilů** - [Samozvaný technický profil](self-asserted-technical-profile.md) může volat [ověření technické profily](validation-technical-profile.md). Technický profil ověření ověří data profilovaná uživatelem a vrátí chybovou zprávu nebo Ok, s výstupními deklaracemi nebo bez ní. Například před Azure AD B2C vytvoří nový účet, zkontroluje, zda uživatel již existuje v adresářových službách. Můžete volat rest API technický profil přidat vlastní obchodní logiku.<p>Rozsah výstupních deklarací technického profilu ověření je omezen na technický profil, který vyvolá technický profil ověření. a další validační technické profily ve stejném technickém profilu. Pokud chcete použít výstupní deklarace v dalším kroku orchestrace, je třeba přidat výstupní deklarace do technického profilu, který vyvolá technický profil ověření.
1. **Výstupní deklarace** – deklarace jsou vráceny zpět do vaku deklarací identity. Tyto deklarace identity můžete použít v dalším kroku orchestrace nebo transformace výstupdeklarace.
1. **Transformace výstupních deklarací** – vstupní deklarace každé [transformace výstupních deklarací](claimstransformations.md) jsou vyzvednuty z vaku deklarací identity. Výstupní deklarace technického profilu z předchozích kroků mohou být vstupní deklarace transformace výstupních deklarací. Po provedení výstupní deklarace jsou vloženy zpět do vaku deklarací identity. Výstupní deklarace transformace výstupních pohledávek mohou být také vstupními deklaracemi následné transformace deklarací výstupu.
1. **Správa relace jednotného přihlašování (SSO)** – uchovává data technického profilu do relace pomocí [správy relací jednotného přihlašování](custom-policy-reference-sso.md).


## <a name="technical-profile-inclusion"></a>Zahrnutí technického profilu

Technický profil může obsahovat jiný technický profil pro změnu nastavení nebo přidání nových funkcí.  Prvek `IncludeTechnicalProfile` je odkazem na základní technický profil, ze kterého je odvozen technický profil. Počet úrovní není nijak omezen.

Například technický profil **AAD-UserReadUsingAlternativeSecurityId-NoError** zahrnuje **aad-userReadUsingAlternativeSecurityId**. Tento technický profil `RaiseErrorIfClaimsPrincipalDoesNotExist` nastaví `true`položku metadat na položku a vyvolá chybu, pokud v adresáři neexistuje účet sociální sítě. **AAD-UserReadUsingAlternativeSecurityId-NoError** přepíše toto chování a zakáže tuto chybovou zprávu.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** obsahuje `AAD-Common` technický profil.

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

**Oba AAD-UserReadUsingAlternativeSecurityId-NoError** a **AAD-UserReadUsingAlternativeSecurityId** nezadejte požadovaný element **Protokolu,** protože je zadán v technickém profilu **AAD Společné.**

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```
