---
title: Správa relací jednotného přihlašování pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Naučte se spravovat relace jednotného přihlašování pomocí vlastních zásad v Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f690f4a416e86b02de0d35fc673849c1293df577
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095761"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Správa relací jednotného přihlašování v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Správa [relací jednotného přihlašování (SSO)](session-behavior.md) používá stejnou sémantiku jako jakýkoliv jiný technický profil v rámci vlastních zásad. Při spuštění kroku orchestrace se pro referenci zobrazí dotaz na technický profil přidružený k tomuto kroku `UseTechnicalProfileForSessionManagement` . Pokud existuje, pak se v odkazovaném poskytovateli relace jednotného přihlašování kontroluje, jestli je uživatel účastníkem relace. V takovém případě se k naplnění relace použije poskytovatel relace jednotného přihlašování. Podobně platí, že po dokončení kroku orchestrace se poskytovatel používá k ukládání informací v relaci, pokud byl zadán zprostředkovatel relace jednotného přihlašování.

Azure AD B2C definovali počet zprostředkovatelů relací jednotného přihlašování, které se dají použít:

|Zprostředkovatel relací  |Obor  |
|---------|---------|
|[NoopSSOSessionProvider](#noopssosessionprovider)     |  Žádné       |       
|[DefaultSSOSessionProvider](#defaultssosessionprovider)    | Azure AD B2C interního správce relací.      |       
|[ExternalLoginSSOSessionProvider](#externalloginssosessionprovider)     | Mezi poskytovatelem identity Azure AD B2C a OAuth1, OAuth2 nebo OpenId Connect.        |         |
|[OAuthSSOSessionProvider](#oauthssosessionprovider)     | Mezi aplikací předávající strany OAuth2 nebo OpenId Connect a Azure AD B2C.        |        
|[SamlSSOSessionProvider](#samlssosessionprovider)     | Mezi Azure AD B2C a zprostředkovatelem identity SAML. A mezi poskytovatelem služby SAML (aplikace předávající strany) a Azure AD B2C.  |        




Třídy správy jednotného přihlašování jsou určené pomocí `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` elementu technického profilu.

## <a name="input-claims"></a>Vstupní deklarace identity

`InputClaims`Element je prázdný nebo chybí.

## <a name="persisted-claims"></a>Trvalé deklarace identity

Deklarace identity, které je potřeba vrátit do aplikace nebo použít v rámci předběžných podmínek v následujících krocích, by se měly ukládat do relace nebo rozšiřovat čtením z profilu uživatele v adresáři. Když použijete trvalé deklarace identity, zajistíte tak selhání cest ověřování u chybějících deklarací. Pokud chcete v relaci přidat deklarace identity, použijte `<PersistedClaims>` element technického profilu. Pokud se k naplnění relace použije zprostředkovatel, budou trvalé deklarace identity přidány do kontejneru deklarací identity.

## <a name="output-claims"></a>Deklarace výstupů

`<OutputClaims>`Slouží k načítání deklarací identity z relace.

## <a name="session-providers"></a>Zprostředkovatelé relací

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Když název určíte, tento zprostředkovatel neprovede žádnou akci. Tento zprostředkovatel se dá použít pro potlačení chování jednotného přihlašování pro konkrétní technický profil. Do `SM-Noop` [Úvodní sady Custom Policy Pack](custom-policy-get-started.md#custom-policy-starter-pack)je zahrnutý následující technický profil.

```xml
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Tento zprostředkovatel se dá použít k ukládání deklarací identity v relaci. Na tohoto poskytovatele se obvykle odkazuje v technickém profilu, který se používá ke správě místních a federovaných účtů. Do `SM-AAD` [Úvodní sady Custom Policy Pack](custom-policy-get-started.md#custom-policy-starter-pack)je zahrnutý následující technický profil.

```xml
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Management Provider</DisplayName>
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


Do `SM-MFA` [Úvodní sady Custom Policy Pack](custom-policy-get-started.md#custom-policy-starter-pack) je zahrnutý následující technický profil `SocialAndLocalAccountsWithMfa` . Tento technický profil spravuje relaci Multi-Factor Authentication.

```xml
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

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Tento zprostředkovatel se používá k potlačení obrazovky "zvolit zprostředkovatele identity" a odhlášení ze Spojeného poskytovatele identity. Obvykle se na něj odkazuje v technickém profilu nakonfigurovaném pro federovaného poskytovatele identity, jako je Facebook nebo Azure Active Directory. Do `SM-SocialLogin` [Úvodní sady Custom Policy Pack](custom-policy-get-started.md#custom-policy-starter-pack)je zahrnutý následující technický profil.

```xml
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadata

| Atribut | Povinné | Popis|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | Ne | Aktuálně se nepoužívá, může být ignorováno. |

### <a name="oauthssosessionprovider"></a>OAuthSSOSessionProvider

Tento zprostředkovatel se používá ke správě Azure AD B2Cch relací mezi OAuth2 nebo OpenId a Azure AD B2Cem připojení.

```xml
<TechnicalProfile Id="SM-jwt-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Tento zprostředkovatel se používá ke správě Azure AD B2C relací SAML mezi aplikací předávající strany nebo poskytovatelem federované identity SAML. Při použití poskytovatele jednotného přihlašování pro uložení relace zprostředkovatele identity SAML `RegisterServiceProviders` musí být nastavená na `false` . `SM-Saml-idp` [Zprostředkovatel identity SAML](identity-provider-generic-saml.md)používá následující technický profil.

```xml
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

Při použití poskytovatele pro ukládání relace SAML B2C `RegisterServiceProviders` musí být nastavená na `true` . Odhlášení relace SAML vyžaduje `SessionIndex` a `NameID` k dokončení.

`SM-Saml-issuer` [Technický profil vystavitele SAML](saml-service-provider.md) používá následující technický profil

```xml
<TechnicalProfile Id="SM-Saml-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadata

| Atribut | Povinné | Popis|
| --- | --- | --- |
| IncludeSessionIndex | Ne | Aktuálně se nepoužívá, může být ignorováno.|
| RegisterServiceProviders | Ne | Indikuje, že by měl poskytovatel zaregistrovat všechny poskytovatele služeb SAML, u kterých bylo vydaný kontrolní výraz. Možné hodnoty: `true` (výchozí), nebo `false` .|


## <a name="next-steps"></a>Další kroky

Naučte se [Konfigurovat chování relace](session-behavior.md).
