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
ms.date: 02/13/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 06c9e79a68540cb10557b0951b743bf841963057
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190258"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování Azure Active Directory technického profilu ve vlastních zásadách Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro Azure Active Directory správu uživatelů. Tento článek popisuje konkrétní technické profily pro interakci se zprostředkovatelem deklarací, který podporuje tento standardizovaný protokol.

## <a name="protocol"></a>Protocol (Protokol)

Atribut **Name** elementu **Protocol** musí být nastaven na `Proprietary`. Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Všechny technické profily Azure AD zahrnují technický profil **AAD-Common** . Následující technické profily nezaurčují protokol, protože protokol je nakonfigurovaný v technickém profilu **AAD-Common** :

- **AAD-UserReadUsingAlternativeSecurityId** a **AAD-UserReadUsingAlternativeSecurityId-Error** – vyhledejte v adresáři účet pro sociální sítě.
- **AAD-UserWriteUsingAlternativeSecurityId** – vytvoří nový účet sociální sítě.
- **AAD-UserReadUsingEmailAddress** – vyhledání místního účtu v adresáři.
- **AAD-UserWriteUsingLogonEmail** – vytvoří nový místní účet.
- **AAD-UserWritePasswordUsingObjectId** – aktualizace hesla místního účtu.
- **AAD-UserWriteProfileUsingObjectId** – aktualizuje profil uživatele místního nebo sociálního účtu.
- **AAD-UserReadUsingObjectId** – Přečtěte si profil uživatele místního nebo sociálního účtu.
- **AAD-UserWritePhoneNumberUsingObjectId** – zápis telefonního čísla MFA místního nebo sociálního účtu

Následující příklad ukazuje technický profil pro **AAD-Common** :

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

## <a name="input-claims"></a>Vstupní deklarace identity

Mezi následující technické profily patří **InputClaims** pro sociální a místní účty:

- Technical Profiles pro sociální profily **AAD-UserReadUsingAlternativeSecurityId** a **AAD-UserWriteUsingAlternativeSecurityId** zahrnuje deklaraci identity **AlternativeSecurityId** . Tato deklarace identity obsahuje identifikátor uživatele účtu sociální sítě.
- Technical Profiles s místními profily **AAD-UserReadUsingEmailAddress** a **AAD-UserWriteUsingLogonEmail** zahrnuje deklaraci identity **e-mailu** . Tato deklarace identity obsahuje přihlašovací jméno místního účtu.
- Jednotné (místní a sociální) technické profily **AAD-UserReadUsingObjectId**, **AAD-UserWritePasswordUsingObjectId**, **AAD-UserWriteProfileUsingObjectId**a **AAD-UserWritePhoneNumberUsingObjectId** zahrnují deklaraci **objectID** . Jedinečný identifikátor účtu

Element **InputClaimsTransformations** může obsahovat kolekci prvků **InputClaimsTransformation** , které se používají k úpravě vstupních deklarací nebo generování nových.

## <a name="output-claims"></a>Deklarace výstupů

Element **OutputClaims** obsahuje seznam deklarací vrácených technickým profilem Azure AD. Možná budete muset namapovat název deklarace identity definované v zásadě na název definovaný v Azure Active Directory. Můžete také zahrnout deklarace identity, které nejsou vráceny Azure Active Directory, pokud nastavíte atribut `DefaultValue`.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

Například technický profil **AAD-UserWriteUsingLogonEmail** vytvoří místní účet a vrátí následující deklarace identity:

- **objectID**, což je identifikátor nového účtu
- **nový_uživatel**, který označuje, zda je uživatel nový
- **authenticationSource**, který nastaví ověřování na `localAccountAuthentication`
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

Element **PersistedClaims** obsahuje všechny hodnoty, které by se měly zachovat službou Azure AD s možnými informacemi o mapování mezi typem deklarace identity, který je už definovaný v části ClaimsSchema v zásadách a v názvu atributu Azure AD.

Technický profil **AAD-UserWriteUsingLogonEmail** , který vytváří nový místní účet, uchovává následující deklarace identity:

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

Název deklarace identity je název atributu Azure AD, pokud není zadaný atribut **PartnerClaimType** , který obsahuje název atributu Azure AD.

## <a name="requirements-of-an-operation"></a>Požadavky na operaci

- V kontejneru deklarací se musí nacházet přesně jeden element **InputClaim** pro všechny technické profily služby Azure AD.
- Pokud je operace `Write` nebo `DeleteClaims`, musí se také objevit v elementu **PersistedClaims** .
- Hodnota deklarace **userPrincipalName** musí být ve formátu `user@tenant.onmicrosoft.com`.
- Deklarace **DisplayName** je povinná a nemůže být prázdným řetězcem.

## <a name="azure-ad-technical-provider-operations"></a>Operace technického poskytovatele služby Azure AD

### <a name="read"></a>Čtení

Operace **čtení** čte data o jednom uživatelském účtu. Chcete-li číst uživatelská data, je třeba zadat klíč jako vstupní deklaraci identity, jako je například **objectID**, **userPrincipalName**, **signInNames** (libovolný typ, uživatelské jméno a e-mailový účet) nebo **alternativeSecurityId**.

Následující technický profil čte data o uživatelském účtu pomocí identifikátoru objectId uživatele:

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

Operace **zápisu** vytvoří nebo aktualizuje jeden uživatelský účet. Chcete-li zapsat uživatelský účet, je nutné zadat klíč jako vstupní deklaraci identity, jako je například **objectID**, **userPrincipalName**, **signInNames. EmailAddress**nebo **alternativeSecurityId**.

Následující technický profil vytvoří nový účet sociální sítě:

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

### <a name="deleteclaims"></a>DeleteClaims

Operace **DeleteClaims** vymaže informace ze zadaného seznamu deklarací identity. Chcete-li odstranit informace z deklarací identity, je třeba zadat klíč jako vstupní deklaraci identity, jako je například **objectID**, **userPrincipalName**, **signInNames. EmailAddress** nebo **alternativeSecurityId**.

Následující technický profil odstraní deklarace identity:

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

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

Operace **DeleteClaimsPrincipal** odstraní jeden uživatelský účet z adresáře. Chcete-li odstranit uživatelský účet, je nutné zadat klíč jako vstupní deklaraci identity, jako je například **objectID**, **userPrincipalName**, **signInNames. EmailAddress** nebo **alternativeSecurityId**.

Následující technický profil odstraní uživatelský účet z adresáře pomocí hlavního názvu uživatele:

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

Následující technický profil odstraní účet uživatele sociální sítě pomocí **alternativeSecurityId**:

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
| Operace | Ano | Operace, která má být provedena. Možné hodnoty: `Read`, `Write`, `DeleteClaims`nebo `DeleteClaimsPrincipal`. |
| RaiseErrorIfClaimsPrincipalDoesNotExist | Ne | Vyvolá chybu, pokud objekt uživatele v adresáři neexistuje. Možné hodnoty: `true` nebo `false`. |
| UserMessageIfClaimsPrincipalDoesNotExist | Ne | Pokud se má vykazovat chyba (viz popis atributu RaiseErrorIfClaimsPrincipalDoesNotExist), zadejte zprávu, která se zobrazí uživateli, pokud objekt uživatele neexistuje. Hodnota může být [lokalizována](localization.md).|
| RaiseErrorIfClaimsPrincipalAlreadyExists | Ne | Vyvolá chybu, pokud objekt uživatele již existuje. Možné hodnoty: `true` nebo `false`.|
| UserMessageIfClaimsPrincipalAlreadyExists | Ne | Pokud má být vyvolána chyba (viz popis atributu RaiseErrorIfClaimsPrincipalAlreadyExists), zadejte zprávu, která se zobrazí uživateli, pokud již objekt uživatele existuje. Hodnota může být [lokalizována](localization.md).|
| ApplicationObjectId | Ne | Identifikátor objektu aplikace pro atributy rozšíření. Hodnota: ObjectId objektu aplikace. Další informace najdete v tématu [použití vlastních atributů v zásadách úprav vlastního profilu](custom-policy-custom-attributes.md). |
| ClientId | Ne | Identifikátor klienta pro přístup k tenantovi jako třetí strana. Další informace najdete v tématu [použití vlastních atributů v zásadách úprav vlastního profilu](custom-policy-custom-attributes.md) . |
| IncludeClaimResolvingInClaimsHandling  | Ne | Pro vstupní a výstupní deklarace identity určuje, jestli je [řešení deklarací identity](claim-resolver-overview.md) zahrnuté v technickém profilu. Možné hodnoty: `true`nebo `false` (výchozí). Pokud chcete použít překladač deklarací identity v technickém profilu, nastavte tuto hodnotu na `true`. |














