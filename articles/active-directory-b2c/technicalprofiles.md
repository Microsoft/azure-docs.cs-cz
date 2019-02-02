---
title: TechnicalProfiles | Dokumentace Microsoftu
description: Zadejte element TechnicalProfiles vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 86f2a8fa11becdf24c0a10c0325893946a033c3d
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55568171"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **TechnicalProfiles** prvek obsahuje sadu technické profily, které podporuje zprostředkovatel deklarací identity. Každý zprostředkovatele deklarací identity, musí mít nejmíň jeden technické profily, které určují koncových bodů a protokolů potřebných ke komunikaci se zprostředkovateli deklarací identity. Zprostředkovatel deklarací může mít více technické profily.

```XML
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">URL of service</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <InputTokenFormat>JWT</InputTokenFormat>
      <OutputTokenFormat>JWT</OutputTokenFormat>
      <CryptographicKeys>
        <Key ID="Key identifier" StorageReferenceId="Storage key container identifier"/>
        ...
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <OutputClaimsTransformations>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="Technical profile identifier" />
        ...
      </ValidationTechnicalProfiles>
      <SubjectNamingInfo ClaimType="Claim type identifier" />
      <IncludeTechnicalProfile ReferenceId="Technical profile identifier" />
      <UseTechnicalProfileForSessionManagement ReferenceId="Technical profile identifier" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

**Technický profil** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
|---------|---------|---------|
| ID | Ano | Jedinečný identifikátor technický profil. Technický profil může být odkazováno pomocí tohoto identifikátoru z dalších prvků v souboru zásad. Například **OrchestrationSteps** a **ValidationTechnicalProfile**. |

**Technický profil** obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Domain (Doména) | 0:1 | Název domény pro technický profil. Například pokud váš technický profil určuje zprostředkovatele identity Facebook, název domény je Facebook.com. |
| Zobrazovaný název | 0:1 | Název technický profil, který může být zobrazen uživatelům. |
| Popis | 0:1 | Popis technický profil, který může být zobrazen uživatelům. |
| Protocol (Protokol) | 0:1 | Protokol používaný pro komunikaci s druhou stranou. |
| Metadata | 0:1 | Kolekce dvojic klíč/hodnota, které využívají protokol pro komunikaci s koncovým bodem v rámci transakce. |
| InputTokenFormat | 0:1 | Formát vstupu tokenu. Možné hodnoty: `JSON`, `JWT`, `SAML11`, nebo `SAML2`. `JWT` Hodnota představuje webového tokenu JSON podle specifikaci IETF. `SAML11` Hodnota představuje token SAML 1.1 zabezpečení podle specifikace OASIS.  `SAML2` Hodnota představuje token SAML 2.0 zabezpečení podle specifikace OASIS. |
| OutputTokenFormat | 0:1 | Formát tokenu výstup. Možné hodnoty: `JSON`, `JWT`, `SAML11`, nebo `SAML2`. |
| CryptographicKeys | 0:1 | Seznam kryptografických klíčů, které se používají v technickém profilu. |
| InputClaimsTransformations | 0:1 | Seznam dříve definované odkazy na transformace deklarací, které budou spuštěny před všechny deklarace identity se odesílají do zprostředkovatele deklarací identity nebo předávající straně. |
| InputClaims | 0:1 | Seznam dříve definované odkazy na typy, které jsou použity jako vstup v technickém profilu. |
| PersistedClaims | 0:1 | Seznam dříve definované odkazy na typy, které jsou trvalé zprostředkovatelem deklarací identity, který se týká technického profilu deklarací identity. |
| OutputClaims | 0:1 | Seznam dříve definované odkazy na typy, které jsou použity jako výstup v technickém profilu deklarací identity. |
| OutputClaimsTransformations | 0:1 | Seznam dříve definované odkazy na transformace deklarací identity, které má být provedena po přijetí deklarace identity od zprostředkovatele deklarací. |
| ValidationTechnicalProfiles | 0: n | Seznam odkazů na jiné technické profily, které používá technického profilu pro účely ověření. Další informace najdete v tématu [technický profil ověření](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | Určuje produkční název subjektu v tokenech, kde je v názvu subjektu zadávají samostatně z deklarací identity. Například, OAuth nebo SAML.  |
| IncludeClaimsFromTechnicalProfile | 0:1 | Identifikátor technického profilu, ze kterého mají být všechny vstupní a výstupní deklarace identity mají být přidány do tohoto technický profil. Odkazované technického profilu musí být definován ve stejném souboru zásad. |
| IncludeTechnicalProfile |0:1 | Identifikátor technického profilu, ze kterého chcete všechna data mají být přidány do tohoto technický profil. Odkazované technického profilu musí existovat ve stejném souboru zásad. |
| UseTechnicalProfileForSessionManagement | 0:1 | Různé technický profil pro správu relací. |
|EnabledForUserJourneys| 0:1 |Určuje, zda technickém profilu je proveden v cestě uživatele.  |

### <a name="protocol"></a>Protocol (Protokol)

**Protokol** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Name | Ano | Název platný protokol podporovaný službou Azure AD B2C, který se používá jako součást technický profil. Možné hodnoty: `OAuth1`, `OAuth2`, `SAML2`, `OpenIdConnect`, `WsFed`, `WsTrust`, `Proprietary`, `session management`, `self-asserted`, nebo `None`. |
| Obslužná rutina | Ne | Pokud název protokolu nastavená na `Proprietary`, zadejte plně kvalifikovaný název sestavení, které se službou Azure AD B2C používá k určení obslužná rutina protokolu. |

### <a name="metadata"></a>Metadata

A **metadat** prvek obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Položka | 0: n | Metadata, která se týká technického profilu. Každý typ technický profil má jinou sadu položky metadat. V části technický profil typů, další informace. |

#### <a name="item"></a>Položka

**Položky** elementu **metadat** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Klíč | Ano | Klíč metadat. Zobrazit jednotlivé typy profilů technické, seznam položky metadat. |

### <a name="cryptographickeys"></a>CryptographicKeys

**CryptographicKeys** prvek obsahuje následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Klíč | 1: n | Kryptografický klíč používaný v tomto technickém profilu. |

#### <a name="key"></a>Klíč

**Klíč** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ID | Ne | Jedinečný identifikátor konkrétní pár klíčů na něj odkazovat z jiných prvků v souboru zásad. |
| StorageReferenceId | Ano | Identifikátor kontejneru klíčů úložiště na něj odkazovat z jiných prvků v souboru zásad. |

### <a name="inputclaimstransformations"></a>InputClaimsTransformations

**InputClaimsTransformations** prvek obsahuje následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1: n | Identifikátor deklarace identity transformace, která má být provedena před všechny deklarace identity se odesílají do zprostředkovatele deklarací identity nebo předávající straně. Transformace deklarací identity lze použít k úpravě existující deklarace identity ClaimsSchema nebo generovat nové značky. |

#### <a name="inputclaimstransformation"></a>InputClaimsTransformation

**InputClaimsTransformation** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor už definovaných v souboru zásad nebo nadřazený soubor zásad transformace deklarací identity. |

### <a name="inputclaims"></a>InputClaims

**InputClaims** prvek obsahuje následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| InputClaim | 1: n | Typ deklarace identity očekávaný vstup. |

#### <a name="inputclaim"></a>InputClaim

**InputClaim** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ano | Identifikátor typu deklarace identity, který je již definován v oddíle ClaimsSchema v souboru zásad nebo nadřazený soubor zásad. |
| Výchozí hodnota | Ne | Výchozí hodnotu použít k vytvoření deklarace identity, pokud se deklarace indikován ClaimTypeReferenceId buď neexistuje, takže výsledné deklarace identity může sloužit jako InputClaim technický profil. |
| PartnerClaimType | Ne | Identifikátor typu deklarace identity externí partnera, že zásady zadaný typ deklarace identity odpovídá. Pokud není zadán atribut PartnerClaimType, konkrétní typ deklarace identity se mapuje na typ deklarace partnera se stejným názvem. Tuto vlastnost používejte, když název typu deklarace identity se liší od druhé strany. Například název pro první deklaraci identity je "jméno", zatímco partnera používá deklaraci identity s názvem "křestní_jméno". |

### <a name="persistedclaims"></a>PersistedClaims

**PersistedClaims** prvek obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| PersistedClaim | 1: n | Typ deklarace k uchování. |

#### <a name="persistedclaim"></a>PersistedClaim

**PersistedClaim** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ano | Identifikátor typu deklarace identity, který je již definován v oddíle ClaimsSchema v souboru zásad nebo nadřazený soubor zásad. |
| Výchozí hodnota | Ne | Výchozí hodnotu použít k vytvoření deklarace identity, pokud se deklarace indikován ClaimTypeReferenceId buď neexistuje, takže výsledné deklarace identity může sloužit jako InputClaim technický profil. |
| PartnerClaimType | Ne | Identifikátor typu deklarace identity externí partnera, že zásady zadaný typ deklarace identity odpovídá. Pokud není zadán atribut PartnerClaimType, konkrétní typ deklarace identity se mapuje na typ deklarace partnera se stejným názvem. Tuto vlastnost používejte, když název typu deklarace identity se liší od druhé strany. Například název pro první deklaraci identity je "jméno", zatímco partnera používá deklaraci identity s názvem "křestní_jméno". |

### <a name="outputclaims"></a>OutputClaims

**OutputClaims** prvek obsahuje následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| outputClaim | 1: n | Typ deklarace identity očekávaný výstup. |

#### <a name="outputclaim"></a>outputClaim

**OutputClaim** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ano | Identifikátor typu deklarace identity, který je již definován v oddíle ClaimsSchema v souboru zásad nebo nadřazený soubor zásad. |
| Výchozí hodnota | Ne | Výchozí hodnotu použít k vytvoření deklarace identity, pokud se deklarace indikován ClaimTypeReferenceId buď neexistuje, takže výsledné deklarace identity může sloužit jako InputClaim technický profil. |
|AlwaysUseDefaultValue |Ne |Vynutí použití výchozí hodnoty.  |
| PartnerClaimType | Ne | Identifikátor typu deklarace identity externí partnera, že zásady zadaný typ deklarace identity odpovídá. Pokud není zadán atribut PartnerClaimType, konkrétní typ deklarace identity se mapuje na typ deklarace partnera se stejným názvem. Tuto vlastnost používejte, když název typu deklarace identity se liší od druhé strany. Například název pro první deklaraci identity je "jméno", zatímco partnera používá deklaraci identity s názvem "křestní_jméno". |

### <a name="outputclaimstransformations"></a>OutputClaimsTransformations

**OutputClaimsTransformations** prvek obsahuje následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1: n | Identifikátory transformace deklarací, které budou spuštěny před všechny deklarace identity se odesílají do zprostředkovatele deklarací identity nebo předávající straně. Transformace deklarací identity lze použít k úpravě existující deklarace identity ClaimsSchema nebo generovat nové značky. |

#### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

**OutputClaimsTransformation** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor už definovaných v souboru zásad nebo nadřazený soubor zásad transformace deklarací identity. |

### <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

**ValidationTechnicalProfiles** prvek obsahuje následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | Identifikátory technické profily, které se používají ověřit některé nebo všechny výstupní deklarace identit odkazující technického profilu. Všechny mezi vstupními deklaracemi identity odkazovaného technického profilu musí být uvedena v výstupní deklarace identit odkazující technického profilu. |

#### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

**ValidationTechnicalProfile** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor technického profilu už definovaných v souboru zásad nebo nadřazený soubor zásad. |

###  <a name="subjectnaminginfo"></a>SubjectNamingInfo

**SubjectNamingInfo** obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ClaimType | Ano | Identifikátor typu deklarace identity, který je již definován v oddíle ClaimsSchema v souboru zásad. |

### <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

**IncludeTechnicalProfile** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor technického profilu už definovaných v souboru zásad nebo nadřazený soubor zásad. |

### <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

**UseTechnicalProfileForSessionManagement** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor technického profilu už definovaných v souboru zásad nebo nadřazený soubor zásad. |

### <a name="enabledforuserjourneys"></a>EnabledForUserJourneys
**ClaimsProviderSelections** uživatele cesty definuje seznam možnosti výběru zprostředkovatele deklarací identity a jejich pořadí. S **EnabledForUserJourneys** element můžete filtrovat, které zprostředkovatel deklarací identity je k dispozici pro uživatele. **EnabledForUserJourneys** element obsahuje jeden z následujících hodnot:

- **Vždy**, provedení technického profilu.
- **Nikdy**, přeskočte technický profil.
- **OnClaimsExistence** spustit pouze v případě, že existuje určitá deklarace identity, zadaná v technickém profilu.
- **OnItemExistenceInStringCollectionClaim**, spustit, jenom když položka existuje v deklaraci identity kolekce řetězců.
- **OnItemAbsenceInStringCollectionClaim** provést, pouze pokud položka neexistuje v deklaraci identity kolekce řetězec.

Pomocí **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** nebo **OnItemAbsenceInStringCollectionClaim**, je potřeba zadat následující metadata: **ClaimTypeOnWhichToEnable** Určuje typ deklarace identity, který se má vyhodnotit, **ClaimValueOnWhichToEnable** Určuje hodnotu, která se má porovnat.

Následující technický profil provádí pouze v případě, **identityProviders** kolekce řetězců obsahuje hodnotu `facebook.com`:

```XML
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
