---
title: Definování technického profilu Azure AD ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Definujte technický profil služby Azure Active Directory ve vlastních zásadách ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7db47eda47850c1c080b6a49256c8a0b37bb0d3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330389"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu služby Azure Active Directory ve vlastní chodové službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro správu uživatelů Služby Azure Active Directory. Tento článek popisuje specifika technického profilu pro interakci s poskytovatelem deklarací identity, který podporuje tento standardizovaný protokol.

## <a name="protocol"></a>Protocol (Protokol)

**Atribut Name** elementu **Protocol** musí být `Proprietary`nastaven na . Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`obslužné rutiny protokolu .

Následující [vlastní zásady startovací balíček](custom-policy-get-started.md#custom-policy-starter-pack) Azure AD technické profily patří **AAD společné** technické profily. Technické profily Služby Azure AD neurčují protokol, protože protokol je nakonfigurovaný v technickém profilu **AAD-Common:**
 
- **AAD-UserReadUsingAlternativeSecurityId** a **AAD-UserReadUsingAlternativeSecurityId-NoError** - Vyhledejte sociální účet v adresáři.
- **AAD-UserWriteUsingAlternativeSecurityId** - Vytvořte nový sociální účet.
- **AAD-UserReadUsingEmailAddress** - Vyhledejte místní účet v adresáři.
- **AAD-UserWriteUsingLogonEmail** - Vytvoření nového místního účtu.
- **AAD-UserWritePasswordUsingObjectId** - Aktualizace hesla místního účtu.
- **AAD-UserWriteProfileUsingObjectId** - Aktualizace profilu uživatele místního nebo sociálního účtu.
- **AAD-UserReadUsingObjectId** - Přečtěte si profil uživatele místního nebo sociálního účtu.
- **AAD-UserWritePhoneUseUsingObjectId** - Zápis telefonního čísla MFA místního nebo sociálního účtu

Následující příklad ukazuje technický profil **Společné ho daD:**

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="inputclaims"></a>Vstupní deklarace

InputClaims Element obsahuje deklaraci identity, která se používá k vyhledat účet v adresáři nebo vytvořit nový. Musí být přesně jeden inputclaim prvek ve vstupní deklarace kolekce pro všechny technické profily Azure AD. Možná budete muset namapovat název deklarace deklarace definované ve vaší zásadě na název definovaný ve službě Azure Active Directory.

Chcete-li číst, aktualizovat nebo odstranit existující uživatelský účet, vstupní deklarace identity je klíč, který jednoznačně identifikuje účet v adresáři Azure AD. Například **objectId**, **userPrincipalName**, **signInNames.emailAddress**, **signInNames.userName**nebo **alternativeSecurityId**. 

Chcete-li vytvořit nový uživatelský účet, vstupní deklarace identity je klíč, který jednoznačně identifikuje místní nebo federovaný účet. Například místní účet: **signInNames.emailAddress**nebo **signInNames.userName**. Pro federovaný účet: **alternativeSecurityId**.

[InputClaimsTransformations](technicalprofiles.md#inputclaimstransformations) Element může obsahovat kolekci vstupní deklarace transformační prvky, které se používají k úpravě vstupní deklarace nebo generovat nové.

## <a name="outputclaims"></a>OutputClaims

**OutputClaims** Element obsahuje seznam deklarací vrácených technickým profilem Azure AD. Možná budete muset namapovat název deklarace deklarace definované ve vaší zásadě na název definovaný ve službě Azure Active Directory. Můžete také zahrnout deklarace identity, které nejsou vráceny službou `DefaultValue` Azure Active Directory, pokud nastavíte atribut.

[OutputClaimsTransformations](technicalprofiles.md#outputclaimstransformations) Element může obsahovat kolekci **OutputClaimsTransformation** prvky, které se používají k úpravě výstupní deklarace nebo generovat nové.

Například technický profil **AAD-UserWriteUsingLogonEmail** vytvoří místní účet a vrátí následující deklarace identity:

- **objectId**, což je identifikátor nového účtu
- **newUser**, který označuje, zda je uživatel nový
- **authenticationSource**, který nastaví ověřování na`localAccountAuthentication`
- **userPrincipalName**, což je hlavní uživatelské jméno nového účtu
- **signInNames.emailAddress**, což je přihlašovací jméno účtu, podobné deklaraci vstupu **e-mailu**

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>Trvalé deklarace identity

**PersistedClaims** Element obsahuje všechny hodnoty, které by měly být trvalé služby Azure AD s možnými informacemi o mapování mezi typem deklarace identity již definované v [claimsSchema](claimsschema.md) části v zásadě a název atributu Azure AD.

Technický profil **AAD-UserWriteUsingLogonEmail,** který vytváří nový místní účet, přetrvává následující deklarace identity:

```XML
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

Název deklarace pohledávky je název atributu Azure AD, pokud není zadán atribut **PartnerClaimType,** který obsahuje název atributu Azure AD.

## <a name="requirements-of-an-operation"></a>Požadavky na operaci

- Musí být přesně jeden prvek **InputClaim** v vaku deklarací identity pro všechny technické profily Azure AD.
- [Článek atributů profilu uživatele](user-profile-attributes.md) popisuje podporované atributy profilu uživatele Azure AD B2C, které můžete použít ve vstupních deklaracích identity, výstupní deklarace identity a trvalé deklarace identity. 
- Pokud je `Write` operace `DeleteClaims`nebo , musí se také zobrazit v **prvku PersistedClaims.**
- Hodnota deklarace **userPrincipalName** musí být ve `user@tenant.onmicrosoft.com`formátu .
- Deklarace **displayName** je vyžadována a nemůže být prázdný řetězec.

## <a name="azure-ad-technical-provider-operations"></a>Operace technického zprostředkovatele Azure AD

### <a name="read"></a>Čtení

Operace **Čtení** čte data o jednom uživatelském účtu. Následující technický profil přečte data o uživatelském účtu pomocí objektu id uživatele:

```XML
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>Zápis

Operace **Zápis** vytvoří nebo aktualizuje jeden uživatelský účet. Následující technický profil vytváří nový sociální účet:

```XML
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>Odstranit deklarace identity

Operace **DeleteClaims** vymaže informace z poskytnutého seznamu deklarací identity. Následující technický profil odstraní deklarace identity:

```XML
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>Odstranit objekt DeleteClaimsPrincipal

Operace **DeleteClaimsPrincipal** odstraní z adresáře jeden uživatelský účet. Následující technický profil odstraní uživatelský účet z adresáře pomocí hlavního uživatelského jména:

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Následující technický profil odstraní sociální uživatelský účet pomocí **alternativeSecurityId**:

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Operace | Ano | Operace, která má být provedena. Možné `Read`hodnoty: `Write` `DeleteClaims`, `DeleteClaimsPrincipal`, , nebo . |
| RaiseErrorIfClaimsPrincipalDoesNotExist | Ne | Pokud objekt uživatele v adresáři neexistuje, vyvolá temene chybou. Možné `true` hodnoty: `false`nebo . |
| RaiseErrorIfClaimsPrincipalAlreadyExists | Ne | Pokud objekt uživatele již existuje, vyvolá temene chybou. Možné `true` hodnoty: `false`nebo .|
| ApplicationObjectId | Ne | Identifikátor objektu aplikace pro atributy rozšíření. Hodnota: ObjectId aplikace. Další informace naleznete v tématu [Použití vlastních atributů ve vlastních zásadách úprav profilu](custom-policy-custom-attributes.md). |
| ClientId | Ne | Identifikátor klienta pro přístup k tenantovi jako třetí strana. Další informace naleznete v tématu [Použití vlastních atributů ve vlastních zásadách úprav profilu.](custom-policy-custom-attributes.md) |
| IncludeClaimResolvingInClaimsHandling  | Ne | Pro vstupní a výstupní deklarace určuje, zda je [řešení deklarací](claim-resolver-overview.md) zahrnuto do technického profilu. Možné hodnoty: `true` `false`  , nebo (výchozí). Pokud chcete použít překladač deklarací identity v `true`technickém profilu, nastavte toto na . |

### <a name="ui-elements"></a>Prvky uživatelského rozhraní
 
Následující nastavení lze použít ke konfiguraci chybové zprávy zobrazené při selhání. Metadata by měla být konfigurována v [samoobslužném technickém](self-asserted-technical-profile.md) profilu. Chybové zprávy lze [lokalizovat](localization.md).

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalAlreadyExists | Ne | Pokud má být vyvolána chyba (viz RaiseErrorIfClaimsPrincipalAlreadyExists popis atributu), zadejte zprávu, která se má zobrazit uživateli, pokud objekt uživatele již existuje. |
| UserMessageIfClaimsPrincipalDoesNotExist | Ne | Pokud má být vyvolána chyba (viz popis atributu RaiseErrorIfClaimsPrincipalDoesNotExist), zadejte zprávu, která se má uživateli zobrazit, pokud objekt uživatele neexistuje. |


## <a name="next-steps"></a>Další kroky

V následujícím článku, například pomocí technického profilu Azure AD:

- [Přidání deklarací identity a přizpůsobení vstupu uživatele pomocí vlastních zásad ve službě Azure Active Directory B2C](custom-policy-configure-user-input.md)














