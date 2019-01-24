---
title: Definování technického profilu služby Azure Active Directory ve vlastních zásadách v Azure Active Directory B2C | Dokumentace Microsoftu
description: Technický profil Azure Active Directory definování ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dc9cda92ef725bbfc1a12756912656f0c39474cd
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54846740"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu služby Azure Active Directory ve vlastních zásadách pro Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C poskytuje podporu pro správu uživatelů Azure Active Directory. Tento článek popisuje, jaké jsou specifikace technický profil pro interakci s zprostředkovatele deklarací identity, která podporuje tento protokol standardizované.

## <a name="protocol"></a>Protocol (Protokol)

**Název** atribut **protokol** elementu musí být nastavena na `Proprietary`. **Obslužná rutina** atribut musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Zahrnout všechny profily technické služby Azure AD **AAD běžné** technický profil. Následující technické profily nezadávejte protokolu, protože protokol je nakonfigurovaný v **AAD běžné** technický profil:

- **AAD – UserReadUsingAlternativeSecurityId** a **AAD. UserReadUsingAlternativeSecurityId NoError** – hledejte až účtu na sociální síti v adresáři.
- **AAD – UserWriteUsingAlternativeSecurityId** – vytvoření nového účtu na sociální síti.
- **AAD – UserReadUsingEmailAddress** – hledejte až místní účet v adresáři. 
- **AAD – UserWriteUsingLogonEmail** – vytvořit nový místní účet.
- **AAD – UserWritePasswordUsingObjectId** – aktualizovat heslo místního účtu.
- **AAD – UserWriteProfileUsingObjectId** – aktualizovat profil uživatele ze sociálních sítí nebo místní účet.
- **AAD – UserReadUsingObjectId** – čtení uživatelského profilu účtu místní nebo sociálních sítí.
- **AAD – UserWritePhoneNumberUsingObjectId** – zápis MFA telefonní číslo účtu místní nebo sociálních sítí

Následující příklad ukazuje **AAD běžné** technický profil:

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

Následující technické profily zahrnují **InputClaims** pro účty sociálních sítí a místní:

- Technické profily účtu na sociální síti **AAD UserReadUsingAlternativeSecurityId** a **AAD UserWriteUsingAlternativeSecurityId** zahrnuje **AlternativeSecurityId** deklarací identity. Tato deklarace obsahuje identifikátor uživatele účtu na sociální síti.
- Technické profily místního účtu **AAD UserReadUsingEmailAddress** a **AAD UserWriteUsingLogonEmail** zahrnuje **e-mailu** deklarací identity. Tato deklarace identity obsahuje přihlašovací jméno místního účtu.
- Sjednocené (místní a sociální) technické profily **AAD UserReadUsingObjectId**, **AAD UserWritePasswordUsingObjectId**, **AAD UserWriteProfileUsingObjectId**, a **AAD UserWritePhoneNumberUsingObjectId** zahrnuje **objectId** deklarací identity. Jedinečný identifikátor účtu.

**InputClaimsTransformations** element může obsahovat kolekci **InputClaimsTransformation** prvků, které slouží k úpravě mezi vstupními deklaracemi identity nebo generovat nové značky.

## <a name="output-claims"></a>Výstupní deklarace identit.

**OutputClaims** prvek obsahuje seznam deklarací identity vrátí technický profil Azure AD. Budete muset namapovat název deklarace identity, definovaný ve svojí zásadě název definovaný v Azure Active Directory. Můžete také zahrnout deklarace identity, které nebudou zobrazeny ve službě Azure Active Directory, tak dlouho, dokud je nastavit `DefaultValue` atribut.

**OutputClaimsTransformations** element může obsahovat kolekci **OutputClaimsTransformation** prvky, které se používají ke změně výstupní deklarace identit nebo generovat nové značky.

Například **AAD UserWriteUsingLogonEmail** technický profil místní účet vytvoří a vrátí následující deklarace:

- **ID objektu**, což není identifikátor nového účtu
- **newUser**, což znamená, zda je nový uživatel
- **authenticationSource**, který nastaví ověřování `localAccountAuthentication`
- **userPrincipalName**, což je hlavní název uživatele pro nový účet
- **signInNames.emailAddress**, což je znak v názvu účtu, podobně jako **e-mailu** vstupní deklaraci identity

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

**PersistedClaims** prvek obsahuje všechny hodnoty, které by měl nastavit jako trvalý, službou Azure AD s informací o možných mapování mezi typem deklarace identity již definovaná v části ClaimsSchema v zásady a atributů Azure AD Jméno. 

**AAD UserWriteUsingLogonEmail** technický profil, který vytvoří nový místní účet, se opakuje následující deklarace:

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

Název deklarace je název atributu Azure AD, pokud **PartnerClaimType** je zadán atribut, který obsahuje název atributu Azure AD.

## <a name="requirements-of-an-operation"></a>Požadavky na operace

- Musí obsahovat přesně jeden **InputClaim** element v kontejneru a deklarace identity pro všechny profily technické služby Azure AD. 
- Pokud je operace `Write` nebo `DeleteClaims`, pak musí také zobrazí v **PersistedClaims** elementu.
- Hodnota **userPrincipalName** deklarace identity musí být ve formátu `user@tenant.onmicrosoft.com`.
- **DisplayName** deklarací identity je vyžadován a nemůže být prázdný řetězec.

## <a name="azure-ad-technical-provider-operations"></a>Operace technické poskytovatele služby Azure AD

### <a name="read"></a>Čtení

**Čtení** operace čte data o jeden uživatelský účet. Další uživatelská data, budete muset zadat klíč jako vstupní deklarace identity, jako například **objectId**, **userPrincipalName**, **signInNames** (libovolný typ, uživatelské jméno a e mailových účtů) nebo **alternativeSecurityId**.  

Následující technický profil čte data o uživatelském účtu pomocí ID objektu uživatele:

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

**Zápisu** operace vytvoří nebo aktualizuje jeden uživatelský účet. Zapsat uživatelský účet, budete muset zadat klíč jako vstupní deklarace identity, jako například **objectId**, **userPrincipalName**, **signInNames.emailAddress**, nebo  **alternativeSecurityId**.  

Následující technický profil se vytvoří nové účtu na sociální síti:

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

**DeleteClaims** operace vymaže informace z poskytnutého seznamu deklarací identity. Můžete odstranit informace z deklarací identity, je třeba zadat klíč jako vstupní deklarace identity, jako například **objectId**, **userPrincipalName**, **signInNames.emailAddress** nebo **alternativeSecurityId**.  

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

**DeleteClaimsPrincipal** operace odstraní jediného uživatelského účtu z adresáře. K odstranění uživatelského účtu, budete muset zadejte klíč jako vstupní deklarace identity, jako třeba **objectId**, **userPrincipalName**, **signInNames.emailAddress** nebo  **alternativeSecurityId**.  

Následující technický profil odstraní účet uživatele z adresáře pomocí hlavní název uživatele:

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

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Operace | Ano | Operace, která se má provést. Možné hodnoty: `Read`, `Write`, `DeleteClaims`, nebo `DeleteClaimsPrincipal`. | 
| RaiseErrorIfClaimsPrincipalDoesNotExist | Ne | Vyvolá chybu, pokud objekt uživatele neexistuje v adresáři. Možné hodnoty: `true` nebo `false`. | 
| UserMessageIfClaimsPrincipalDoesNotExist | Ne | Pokud chybu, je vyvolána (viz popis atributu RaiseErrorIfClaimsPrincipalDoesNotExist), zadejte zprávu, která se zobrazí uživateli, pokud objekt uživatele neexistuje. Hodnota může být [lokalizované](localization.md).| 
| RaiseErrorIfClaimsPrincipalAlreadyExists | Ne | Vyvolat chybu, pokud již existuje objekt uživatele. Možné hodnoty: `true` nebo `false`.| 
| UserMessageIfClaimsPrincipalAlreadyExists | Ne | Pokud chybu, je vyvolána (viz popis atributu RaiseErrorIfClaimsPrincipalAlreadyExists), zadejte zprávu, která se zobrazí uživateli, pokud objekt uživatele již existuje. Hodnota může být [lokalizované](localization.md).| 
| ApplicationObjectId | Ne | Identifikátor objektu aplikace pro atributy rozšíření. Hodnota: ID objektu aplikace. Další informace najdete v tématu [použití vlastních atributů ve vlastním profilu upravit zásadu](active-directory-b2c-create-custom-attributes-profile-edit-custom.md). | 
| ClientId | Ne | Identifikátor klienta pro přístup k tenantovi v roli třetích stran. Další informace najdete v tématu [použití vlastních atributů ve vlastním profilu upravit zásadu](active-directory-b2c-create-custom-attributes-profile-edit-custom.md) | 














