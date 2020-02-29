---
title: Správa relací jednotného přihlašování pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Naučte se spravovat relace jednotného přihlašování pomocí vlastních zásad v Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/27/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b905591266b90e5bba83e7c74b27e7f6b3cab610
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912541"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Správa relací jednotného přihlašování v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Správa relací jednotného přihlašování (SSO) v Azure Active Directory B2C (Azure AD B2C) umožňuje správci řídit interakci s uživatelem poté, co uživatel již ověřený. Správce může třeba určit, jestli se má zobrazit výběr zprostředkovatelů identity, nebo jestli se mají zadat podrobnosti o místním účtu znovu. Tento článek popisuje, jak nakonfigurovat nastavení jednotného přihlašování pro Azure AD B2C.

Správa relace jednotného přihlašování má dvě části. První se zabývá interakcemi uživatele přímo s Azure AD B2C a dalšími obchody s interakcemi uživatele s externími stranami, jako je Facebook. Azure AD B2C nepřepisuje ani neobejde relace jednotného přihlašování, které mohou být uloženy externími stranami. Místo toho, aby se trasa mezi Azure AD B2C dostala na externí stranu, je "rememberd", takže není nutné znovu vyzvat uživatele k výběru poskytovatele identity pro sociální nebo podnikové prostředí. Konečné rozhodnutí SSO zůstává u externí strany.

Správa relací jednotného přihlašování používá stejnou sémantiku jako jakýkoliv jiný technický profil ve vlastních zásadách. Když se spustí krok orchestrace, pro odkaz na `UseTechnicalProfileForSessionManagement` se dotazuje technický profil přidružený k tomuto kroku. Pokud existuje, pak se v odkazovaném poskytovateli relace jednotného přihlašování kontroluje, jestli je uživatel účastníkem relace. V takovém případě se k naplnění relace použije poskytovatel relace jednotného přihlašování. Podobně platí, že po dokončení kroku orchestrace se poskytovatel používá k ukládání informací v relaci, pokud byl zadán zprostředkovatel relace jednotného přihlašování.

Azure AD B2C definovali počet zprostředkovatelů relací jednotného přihlašování, které se dají použít:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Třídy správy jednotného přihlašování jsou určené pomocí `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />`ho prvku technického profilu.

## <a name="input-claims"></a>Vstupní deklarace identity

Element `InputClaims` je prázdný nebo chybí. 

## <a name="persisted-claims"></a>Trvalé deklarace identity

Deklarace identity, které je potřeba vrátit do aplikace nebo použít v rámci předběžných podmínek v následujících krocích, by se měly ukládat do relace nebo rozšiřovat čtením z profilu uživatele v adresáři. Když použijete trvalé deklarace identity, zajistíte tak selhání cest ověřování u chybějících deklarací. K přidání deklarací v relaci použijte `<PersistedClaims>` element technického profilu. Pokud se k naplnění relace použije zprostředkovatel, budou trvalé deklarace identity přidány do kontejneru deklarací identity. 

## <a name="output-claims"></a>Deklarace výstupů

`<OutputClaims>` slouží k načítání deklarací identity z relace.

## <a name="session-providers"></a>Zprostředkovatelé relací

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Když název určíte, tento zprostředkovatel neprovede žádnou akci. Tento zprostředkovatel se dá použít pro potlačení chování jednotného přihlašování pro konkrétní technický profil. Do [Úvodní sady Custom Policy Pack](custom-policy-get-started.md#custom-policy-starter-pack)je zahrnut následující `SM-Noop` Technical Profile.  

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Tento zprostředkovatel se dá použít k ukládání deklarací identity v relaci. Na tohoto poskytovatele se obvykle odkazuje v technickém profilu, který se používá pro správu místních účtů. Do [Úvodní sady Custom Policy Pack](custom-policy-get-started.md#custom-policy-starter-pack)je zahrnut následující `SM-AAD` Technical Profile. 

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

Následující `SM-MFA` technický profil je součástí sady [Customer starter](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa`. Tento technický profil spravuje relaci Multi-Factor Authentication. 

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

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Pomocí tohoto poskytovatele se potlačí obrazovka "zvolit zprostředkovatele identity". Obvykle se na něj odkazuje v technickém profilu nakonfigurovaném pro externího zprostředkovatele identity, jako je Facebook. Do [Úvodní sady Custom Policy Pack](custom-policy-get-started.md#custom-policy-starter-pack)je zahrnut následující `SM-SocialLogin` Technical Profile.

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
| AlwaysFetchClaimsFromProvider | Ne | Aktuálně se nepoužívá, může být ignorováno. |

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Tento zprostředkovatel se používá ke správě Azure AD B2C relací SAML mezi aplikací předávající strany nebo poskytovatelem federované identity SAML. Při použití poskytovatele jednotného přihlašování pro uložení relace zprostředkovatele identity SAML musí být `IncludeSessionIndex` a `RegisterServiceProviders` nastavené na `false`. [Technický profil SAML](saml-technical-profile.md)používá následující `SM-Saml-idp` technický profil.

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IncludeSessionIndex">false</Item>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

Při použití poskytovatele pro ukládání relace SAML B2C musí být `IncludeSessionIndex` a `RegisterServiceProviders` nastavené na `true`. Odhlášení relace SAML vyžaduje `SessionIndex` a `NameID` dokončení.
 
[Technický profil vystavitele SAML](connect-with-saml-service-providers.md) používá následující `SM-Saml-idp` technický profil

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>Metadata
        
| Atribut | Požaduje se | Popis|
| --- | --- | --- |
| IncludeSessionIndex | Ne | Indikuje poskytovateli, že by měl být uložen index relace. Možné hodnoty: `true` (výchozí) nebo `false`.|
| RegisterServiceProviders | Ne | Indikuje, že by měl poskytovatel zaregistrovat všechny poskytovatele služeb SAML, u kterých bylo vydaný kontrolní výraz. Možné hodnoty: `true` (výchozí) nebo `false`.|



