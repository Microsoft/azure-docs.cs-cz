---
title: Správa relací jednotného přihlášení pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Zjistěte, jak spravovat relace přizpůsobování individuálního zabezpečení pomocí vlastních zásad v Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80cf0d101a29de7fca9d4dd36e188a500d35e290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246029"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Správa relací jednotného přihlášení ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Správa relací jednotného přihlašování (SSO) ve službě Azure Active Directory B2C (Azure AD B2C) umožňuje správci řídit interakci s uživatelem po ověření uživatele. Správce může například určit, zda se zobrazí výběr zprostředkovatelů identity nebo zda je třeba znovu zadat podrobnosti místního účtu. Tento článek popisuje, jak nakonfigurovat nastavení přistahou k obecnému zabezpečení pro Azure AD B2C.

Správa relace spřimaže má dvě části. První se zabývá interakcemi uživatele přímo s Azure AD B2C a další se zabývá interakcemi uživatele s externími stranami, jako je Facebook. Azure AD B2C nepřepíše nebo obejít relace přistajení, které mohou být v držení externích stran. Spíše trasa přes Azure AD B2C se dostat do externí strany je "pamatoval", vyhnout se nutnosti znovu vyzvat uživatele k výběru jejich sociální nebo podnikové identity poskytovatele. Konečné rozhodnutí o sazbě s sebou zůstává na externí straně.

Správa relací při správě přiřazování obvyklých škol používá stejnou sémantiku jako jakýkoli jiný technický profil ve vlastních zásadách. Při spuštění kroku orchestrace technický profil přidružený ke kroku je `UseTechnicalProfileForSessionManagement` dotazován na odkaz. Pokud jeden existuje, odkazovaný zprostředkovatel relace s přihlašování k internetu je pak kontrolována, zda je uživatel účastníkem relace. Pokud ano, zprostředkovatel relace s přihlašování se používá k opětovnému naplnění relace. Podobně po dokončení provádění krok orchestrace zprostředkovatele slouží k ukládání informací v relaci, pokud byl zadán zprostředkovatel relace s přihlašování.

Azure AD B2C definovala řadu zprostředkovatelů relací při způsobovaného zabezpečení, které lze použít:

* Zprostředkovatel noopSSOSessionProvider
* DefaultSSOSessionProvider
* Externí LoginSSOSessionProvider
* SamlSSOSessionProvider

Třídy správy spřimjené stemi s stezací služby jsou určeny `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` pomocí prvku technického profilu.

## <a name="input-claims"></a>Vstupní deklarace

Prvek `InputClaims` je prázdný nebo chybí.

## <a name="persisted-claims"></a>Trvalé nároky

Deklarace, které je třeba vrátit do aplikace nebo použít předběžné podmínky v následujících krocích, by měly být uloženy v relaci nebo rozšířeny o čtení z profilu uživatele v adresáři. Použití trvalých deklarací zajistí, že vaše cesty ověřování neselžou u chybějících deklarací identity. Chcete-li přidat deklarace `<PersistedClaims>` identity v relaci, použijte prvek technického profilu. Při použití zprostředkovatele k opětovnému naplnění relace, trvalé deklarace identity jsou přidány do vaku deklarací identity.

## <a name="output-claims"></a>Výstupní pohledávky

Používá `<OutputClaims>` se pro načítání deklarací z relace.

## <a name="session-providers"></a>Poskytovatelé relací

### <a name="noopssosessionprovider"></a>Zprostředkovatel noopSSOSessionProvider

Jak určuje název, tento zprostředkovatel neprovede žádné. Tento zprostředkovatel lze použít k potlačení chování přisyzování služeb přisilničního a so pro konkrétní technický profil. Následující `SM-Noop` technický profil je součástí [sady custom policy starter pack](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Tohoto zprostředkovatele lze použít pro ukládání deklarací identity v relaci. Tento zprostředkovatel je obvykle odkazován v technickém profilu používaném pro správu místních účtů. Následující `SM-AAD` technický profil je součástí [sady custom policy starter pack](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

Následující `SM-MFA` technický profil je součástí [sady custom policy starter pack](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa`. Tento technický profil spravuje vícefaktorové ověřování relace.

```XML
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>Externí LoginSSOSessionProvider

Tento zprostředkovatel se používá k potlačení obrazovky "zvolit zprostředkovatele identity". Obvykle se na něj odkazuje v technickém profilu nakonfigurovaném pro externího poskytovatele identity, jako je Facebook. Následující `SM-SocialLogin` technický profil je součástí [sady custom policy starter pack](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="AlwaysFetchClaimsFromProvider">true</Item>
  </Metadata>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis|
| --- | --- | --- |
| VždyFetchClaimsFromProvider | Ne | Není aktuálně používán, lze je ignorovat. |

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Tento zprostředkovatel se používá pro správu relací Azure AD B2C SAML mezi aplikací předávající strany nebo federovaným poskytovatelem identity SAML. Při použití zprostředkovatele spřimit pro ukládání relace zprostředkovatele identity SAML, musí být nastavena `RegisterServiceProviders` na `false`. Následující `SM-Saml-idp` technický profil je používán [technickým profilem SAML](saml-technical-profile.md).

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

Při použití zprostředkovatele pro ukládání relace B2C `RegisterServiceProviders` SAML `true`musí být nastavena na . Odhlášení relace SAML `SessionIndex` `NameID` vyžaduje a dokončení.

Následující `SM-Saml-idp` technický profil používá [technický profil emitenta SAML](saml-issuer-technical-profile.md)

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis|
| --- | --- | --- |
| IncludeSessionIndex | Ne | Není aktuálně používán, lze je ignorovat.|
| RegisterServiceProviders | Ne | Označuje, že poskytovatel by měl zaregistrovat všechny poskytovatele služeb SAML, kterým byl vydán kontrolní výraz. Možné hodnoty: `true` (výchozí) nebo `false`.|



