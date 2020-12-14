---
title: Definování technického profilu Azure AD ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Definujte Azure Active Directory technický profil ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c8f4c91070d87e9e6e3cdbb5534b988063eaba14
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387172"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování Azure Active Directory technického profilu ve vlastních zásadách Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro Azure Active Directory správu uživatelů. Tento článek popisuje konkrétní technické profily pro interakci se zprostředkovatelem deklarací, který podporuje tento standardizovaný protokol.

## <a name="protocol"></a>Protokol

Atribut **Name** elementu **Protocol** musí být nastaven na hodnotu `Proprietary` . Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` .

Následující technické profily služby Azure AD s [úvodním balíčkem pro vlastní zásady](custom-policy-get-started.md#custom-policy-starter-pack) zahrnují technický profil **AAD-Common** . Technické profily Azure AD nezaurčují protokol, protože protokol je nakonfigurovaný v technickém profilu **AAD-Common** :
 
- **AAD-UserReadUsingAlternativeSecurityId** a **AAD-UserReadUsingAlternativeSecurityId-Error** – vyhledejte v adresáři účet pro sociální sítě.
- **AAD-UserWriteUsingAlternativeSecurityId** – vytvoří nový účet sociální sítě.
- **AAD-UserReadUsingEmailAddress** – vyhledání místního účtu v adresáři.
- **AAD-UserWriteUsingLogonEmail** – vytvoří nový místní účet.
- **AAD-UserWritePasswordUsingObjectId** – aktualizace hesla místního účtu.
- **AAD-UserWriteProfileUsingObjectId** – aktualizuje profil uživatele místního nebo sociálního účtu.
- **AAD-UserReadUsingObjectId** – Přečtěte si profil uživatele místního nebo sociálního účtu.
- **AAD-UserWritePhoneNumberUsingObjectId** – zápis telefonního čísla MFA místního nebo sociálního účtu

Následující příklad ukazuje technický profil pro **AAD-Common** :

```xml
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

## <a name="inputclaims"></a>InputClaims

Element InputClaims obsahuje deklaraci identity, která se používá k vyhledání účtu v adresáři, nebo vytvoření nového. Ve vstupní kolekci deklarací pro všechny technické profily služby Azure AD musí existovat přesně jeden element InputClaim. Možná budete muset namapovat název deklarace identity definované v zásadě na název definovaný v Azure Active Directory.

Pokud chcete číst, aktualizovat nebo odstranit existující uživatelský účet, vstupní deklarace identity je klíč, který jedinečně identifikuje účet v adresáři služby Azure AD. Například **objectID**, **userPrincipalName**, **signInNames. EmailAddress**, **signInNames. username** nebo **alternativeSecurityId**. 

Pokud chcete vytvořit nový uživatelský účet, vstupní deklarace identity je klíč, který jednoznačně identifikuje místní nebo federovaný účet. Například místní účet: **signInNames. EmailAddress** nebo **signInNames. username**. Pro federovaný účet: **alternativeSecurityId**.

Element [InputClaimsTransformations](technicalprofiles.md#input-claims-transformations) může obsahovat kolekci vstupních transformačních prvků deklarací identity, které se používají k úpravě vstupní deklarace identity nebo k vygenerování nového.

## <a name="outputclaims"></a>OutputClaims

Element **OutputClaims** obsahuje seznam deklarací vrácených technickým profilem Azure AD. Možná budete muset namapovat název deklarace identity definované v zásadě na název definovaný v Azure Active Directory. Můžete také zahrnout deklarace identity, které nejsou vráceny Azure Active Directory, pokud nastavíte `DefaultValue` atribut.

Element [OutputClaimsTransformations](technicalprofiles.md#output-claims-transformations) může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

Například technický profil **AAD-UserWriteUsingLogonEmail** vytvoří místní účet a vrátí následující deklarace identity:

- **objectID**, což je identifikátor nového účtu
- **nový_uživatel**, který označuje, zda je uživatel nový
- **authenticationSource**, který nastavuje ověřování na `localAccountAuthentication`
- **userPrincipalName**, což je hlavní název uživatele nového účtu
- **signInNames. EmailAddress**, což je přihlašovací jméno účtu, podobně jako deklarace vstupu **e-mailu**

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

Element **PersistedClaims** obsahuje všechny hodnoty, které by se měly zachovat službou Azure AD s možnými informacemi o mapování mezi typem deklarace identity, který je už definovaný v části [ClaimsSchema](claimsschema.md) v zásadách a v názvu atributu Azure AD.

Technický profil **AAD-UserWriteUsingLogonEmail** , který vytváří nový místní účet, uchovává následující deklarace identity:

```xml
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

Název deklarace identity je název atributu Azure AD, pokud není zadaný atribut **PartnerClaimType** , který obsahuje název atributu Azure AD.

## <a name="requirements-of-an-operation"></a>Požadavky na operaci

- V kontejneru deklarací se musí nacházet přesně jeden element **InputClaim** pro všechny technické profily služby Azure AD.
- [Článek s atributy profilu uživatele](user-profile-attributes.md) popisuje podporované Azure AD B2C atributy profilu uživatele, které můžete použít ve vstupních deklaracích, výstupech deklarací identity a trvalých deklaracích identity. 
- Pokud je operace `Write` nebo `DeleteClaims` , musí se také objevit v elementu **PersistedClaims** .
- Hodnota deklarace **userPrincipalName** musí být ve formátu `user@tenant.onmicrosoft.com` .
- Deklarace **DisplayName** je povinná a nemůže být prázdným řetězcem.

## <a name="azure-ad-technical-provider-operations"></a>Operace technického poskytovatele služby Azure AD

### <a name="read"></a>Číst

Operace **čtení** čte data o jednom uživatelském účtu. Následující technický profil čte data o uživatelském účtu pomocí identifikátoru objectId uživatele:

```xml
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

Operace **zápisu** vytvoří nebo aktualizuje jeden uživatelský účet. Následující technický profil vytvoří nový účet sociální sítě:

```xml
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

### <a name="deleteclaims"></a>DeleteClaims

Operace **DeleteClaims** vymaže informace ze zadaného seznamu deklarací identity. Následující technický profil odstraní deklarace identity:

```xml
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

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

Operace **DeleteClaimsPrincipal** odstraní jeden uživatelský účet z adresáře. Následující technický profil odstraní uživatelský účet z adresáře pomocí hlavního názvu uživatele:

```xml
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

Následující technický profil odstraní účet uživatele sociální sítě pomocí **alternativeSecurityId**:

```xml
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

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Operace | Ano | Operace, která má být provedena. Možné hodnoty: `Read` , `Write` , `DeleteClaims` , nebo `DeleteClaimsPrincipal` . |
| RaiseErrorIfClaimsPrincipalDoesNotExist | Ne | Vyvolá chybu, pokud objekt uživatele v adresáři neexistuje. Možné hodnoty: `true` nebo `false` . |
| RaiseErrorIfClaimsPrincipalAlreadyExists | Ne | Vyvolá chybu, pokud objekt uživatele již existuje. Možné hodnoty: `true` nebo `false` .|
| ApplicationObjectId | Ne | Identifikátor objektu aplikace pro atributy rozšíření. Hodnota: ObjectId objektu aplikace. Další informace najdete v tématu [použití vlastních atributů v zásadách úprav vlastního profilu](custom-policy-custom-attributes.md). |
| ClientId | Ne | Identifikátor klienta pro přístup k tenantovi jako třetí strana. Další informace najdete v tématu [použití vlastních atributů v zásadách úprav vlastního profilu](custom-policy-custom-attributes.md) . |
| IncludeClaimResolvingInClaimsHandling  | Ne | Pro vstupní a výstupní deklarace identity určuje, jestli je [řešení deklarací identity](claim-resolver-overview.md) zahrnuté v technickém profilu. Možné hodnoty: `true` , nebo `false` (výchozí). Pokud chcete použít překladač deklarací identity v technickém profilu, nastavte tuto hodnotu na `true` . |

### <a name="ui-elements"></a>Prvky uživatelského rozhraní
 
Následující nastavení lze použít ke konfiguraci chybové zprávy, která se zobrazí po selhání. Metadata by měla být nakonfigurovaná v technickém profilu s [vlastním kontrolním](self-asserted-technical-profile.md) výrazem. Chybové zprávy lze [lokalizovat](localization.md).

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalAlreadyExists | Ne | Pokud má být vyvolána chyba (viz popis atributu RaiseErrorIfClaimsPrincipalAlreadyExists), zadejte zprávu, která se zobrazí uživateli, pokud již objekt uživatele existuje. |
| UserMessageIfClaimsPrincipalDoesNotExist | Ne | Pokud se má vykazovat chyba (viz popis atributu RaiseErrorIfClaimsPrincipalDoesNotExist), zadejte zprávu, která se zobrazí uživateli, pokud objekt uživatele neexistuje. |


## <a name="next-steps"></a>Další kroky

Podívejte se na následující článek, například používání technického profilu Azure AD:

- [Přidání deklarací identity a přizpůsobení uživatelského vstupu pomocí vlastních zásad v Azure Active Directory B2C](configure-user-input.md)














