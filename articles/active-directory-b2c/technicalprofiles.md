---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Zadejte prvek TechnicalProfiles vlastní zásady ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5196615b6b935e4d37565298be03ad315163d132
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264307"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Prvek **TechnicalProfiles** obsahuje sadu technických profilů podporovaných poskytovatelem deklarací. Každý zprostředkovatel deklarací identity musí mít jeden nebo více technických profilů, které určují koncové body a protokoly potřebné ke komunikaci s poskytovatelem deklarací identity. Zprostředkovatel deklarací identity může mít více technických profilů.

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

Prvek **TechnicalProfile** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
|---------|---------|---------|
| ID | Ano | Jedinečný identifikátor technického profilu. Technický profil lze odkazovat pomocí tohoto identifikátoru z jiných prvků v souboru zásad. Například **OrchestrationSteps** a **ValidationTechnicalProfile**. |

**TechnicalProfile** obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Domain (Doména) | 0:1 | Název domény pro technický profil. Pokud například váš technický profil určuje poskytovatele identity na Facebooku, název domény je Facebook.com. |
| DisplayName | 1:1 | Název technického profilu, který lze zobrazit uživatelům. |
| Popis | 0:1 | Popis technického profilu, který lze zobrazit uživatelům. |
| Protocol (Protokol) | 0:1 | Protokol používaný pro komunikaci s druhou stranou. |
| Metadata | 0:1 | Kolekce párů klíč/hodnota, které jsou využívány protokolem pro komunikaci s koncovým bodem v průběhu transakce. |
| Formát InputToken | 0:1 | Formát vstupního tokenu. Možné `JSON`hodnoty: `JWT` `SAML11`, `SAML2`, , nebo . Hodnota `JWT` představuje webový token JSON podle specifikace IETF. Hodnota `SAML11` představuje token zabezpečení SAML 1.1 podle specifikace OASIS.  Hodnota `SAML2` představuje token zabezpečení SAML 2.0 podle specifikace OASIS. |
| Formát OutputTokenFormat | 0:1 | Formát výstupního tokenu. Možné `JSON`hodnoty: `JWT` `SAML11`, `SAML2`, , nebo . |
| Kryptografické klávesy | 0:1 | Seznam kryptografických klíčů, které se používají v technickém profilu. |
| InputClaimsTransformace | 0:1 | Seznam dříve definovaných odkazů na transformace deklarací, které by měly být provedeny před odesláním deklarací poskytovateli deklarací nebo předávající straně. |
| Vstupní deklarace | 0:1 | Seznam dříve definovaných odkazů na typy deklarací, které jsou brány jako vstup v technickém profilu. |
| Trvalé deklarace identity | 0:1 | Seznam dříve definovaných odkazů na typy deklarací, které jsou trvalé poskytovatelem deklarací identity, který se vztahuje k technickému profilu. |
| Zobrazit deklarace identity | 0:1 | Seznam dříve definovaných odkazů na typy deklarací, které jsou prezentovány poskytovatelem deklarací identity, který se vztahuje k [vlastnímu technickému profilu](self-asserted-technical-profile.md). Funkce DisplayClaims je aktuálně ve **verzi Preview**. |
| OutputClaims | 0:1 | Seznam dříve definovaných odkazů na typy deklarací, které jsou brány jako výstup v technickém profilu. |
| OutputClaimsTransformace | 0:1 | Seznam dříve definovaných odkazů na transformace deklarací identity, které by měly být provedeny po obdržení deklarací od poskytovatele deklarací. |
| ValidaceTechnickéProfily | 0:n | Seznam odkazů na jiné technické profily, které technický profil používá pro účely validace. Další informace naleznete v [tématu ověření technického profilu](validation-technical-profile.md)|
| Informace o pojmenování předmětu | 0:1 | Řídí tvorbu názvu subjektu v tokenech, kde je název subjektu určen odděleně od deklarací identity. Například OAuth nebo SAML.  |
| IncludeInSso | 0:1 |  Zda použití tohoto technického profilu by měla použít jednotné přihlašování (SSO) chování pro relaci, nebo místo toho vyžadují explicitní interakci. Tento prvek je platný pouze v profilech SelfAsserted používaných v rámci technického profilu ověření. Možné hodnoty: `true` (výchozí) nebo `false`. |
| IncludeClaimsFromTechnicalProfile | 0:1 | Identifikátor technického profilu, ze kterého chcete přidat všechny vstupní a výstupní deklarace, které mají být přidány do tohoto technického profilu. Odkazovaný technický profil musí být definován ve stejném souboru zásad. |
| IncludeTechnicalProfile |0:1 | Identifikátor technického profilu, ze kterého chcete, aby byla do tohoto technického profilu přidána všechna data. |
| useTechnicalProfileForSessionManagement Management | 0:1 | Jiný technický profil, který se má použít pro správu relací. |
|EnabledForUserJourneys| 0:1 |Určuje, zda je technický profil proveden v cestě uživatele.  |

## <a name="protocol"></a>Protocol (Protokol)

Element **Protocol** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Name (Název) | Ano | Název platného protokolu podporovaného Azure AD B2C, který se používá jako součást technického profilu. `OAuth1`Možné hodnoty: `OAuth2` `SAML2`, `OpenIdConnect` `Proprietary`, `None`, , , nebo . |
| Obslužná rutina | Ne | Pokud je název protokolu `Proprietary`nastaven na , zadejte plně kvalifikovaný název sestavení, které používá Azure AD B2C k určení obslužné rutiny protokolu. |

## <a name="metadata"></a>Metadata

Prvek **metadat** obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Položka | 0:n | Metadata, která se vztahuje k technickému profilu. Každý typ technického profilu má jinou sadu položek metadat. Další informace naleznete v části typy technických profilů. |

### <a name="item"></a>Položka

Prvek **Item** prvku **Metadata** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Klíč | Ano | Klíč metadat. Seznam položek metadat naleznete v jednotlivých typech technického profilu. |

## <a name="cryptographickeys"></a>Kryptografické klávesy

Prvek **CryptographicKeys** obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Klíč | 1:n | Kryptografický klíč použitý v tomto technickém profilu. |

### <a name="key"></a>Klíč

Key **Key** Element obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ID | Ne | Jedinečný identifikátor konkrétní dvojice klíčů odkazovaný z jiných prvků v souboru zásad. |
| StorageReferenceId | Ano | Identifer kontejneru klíče úložiště odkazuje z jiných prvků v souboru zásad. |

## <a name="inputclaimstransformations"></a>InputClaimsTransformace

**InputClaimsTransformations** Element obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| InputClaimsTransformace | 1:n | Identifikátor transformace deklarací, která by měla být provedena před odesláním jakékoli deklarace identity poskytovateli deklarací nebo předávající straně. Deklarace transformace lze upravit existující deklarace identity Schema nebo generovat nové. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformace

Element **InputClaimsTransformation** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor transformace deklarací, která je již definována v souboru zásad nebo v nadřazeném souboru zásad. |

## <a name="inputclaims"></a>Vstupní deklarace

**InputClaims** Element obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Vstupní deklarace | 1:n | Očekávaný vstupní typ deklarace. |

### <a name="inputclaim"></a>Vstupní deklarace

**InputClaim** Element obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ano | Identifikátor typu deklarace identity, který je již definován v části ClaimsSchema v souboru zásad nebo v nadřazeném souboru zásad. |
| Defaultvalue | Ne | Výchozí hodnota, která se má použít k vytvoření deklarace, pokud deklarace označená claimTypeReferenceId neexistuje, takže výslednou deklaraci lze použít jako InputClaim podle technického profilu. |
| Typ deklarace programu Partner | Ne | Identifikátor typu deklarace externího partnera, na který se mapuje zadaný typ deklarace zásad. Pokud není zadán atribut PartnerClaimType, je zadaný typ deklarace zásad mapován na typ deklarace partnerské deklarace se stejným názvem. Tuto vlastnost použijte, pokud se název typu deklarace tvrdí liší od druhé strany. Například první název deklarace je "givenName", zatímco partner používá deklaraci s názvem "first_name". |

## <a name="displayclaims"></a>Zobrazit deklarace identity

Element **DisplayClaims** obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Zobrazit nárok | 1:n | Očekávaný vstupní typ deklarace. |

Funkce DislayClaims je aktuálně ve **verzi preview**.

### <a name="displayclaim"></a>Zobrazit nárok

Element **DisplayClaim** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ne | Identifikátor typu deklarace identity, který je již definován v části ClaimsSchema v souboru zásad nebo v nadřazeném souboru zásad. |
| DisplayControlReferenceId | Ne | Identifikátor [ovládacího prvku zobrazení,](display-controls.md) který je již definován v části ClaimsSchema v souboru zásad nebo v souboru nadřazené zásady. |
| Požaduje se | Ne | Označuje, zda je požadována deklarace zobrazení. |

**DisplayClaim** vyžaduje zadání buď `ClaimTypeReferenceId` a `DisplayControlReferenceId`nebo nebo .

### <a name="persistedclaims"></a>Trvalé deklarace identity

Prvek **PersistedClaims** obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Trvalá deklarace | 1:n | Typ deklarace jako trvalé. |

### <a name="persistedclaim"></a>Trvalá deklarace

Prvek **PersistedClaim** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ano | Identifikátor typu deklarace identity, který je již definován v části ClaimsSchema v souboru zásad nebo v nadřazeném souboru zásad. |
| Defaultvalue | Ne | Výchozí hodnota, která se má použít k vytvoření deklarace, pokud deklarace označená claimTypeReferenceId neexistuje, takže výslednou deklaraci lze použít jako InputClaim podle technického profilu. |
| Typ deklarace programu Partner | Ne | Identifikátor typu deklarace externího partnera, na který se mapuje zadaný typ deklarace zásad. Pokud není zadán atribut PartnerClaimType, je zadaný typ deklarace zásad mapován na typ deklarace partnerské deklarace se stejným názvem. Tuto vlastnost použijte, pokud se název typu deklarace tvrdí liší od druhé strany. Například první název deklarace je "givenName", zatímco partner používá deklaraci s názvem "first_name". |

## <a name="outputclaims"></a>OutputClaims

**OutputClaims** Element obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Výstupní nárok | 1:n | Očekávaný typ deklarace výstupu. |

### <a name="outputclaim"></a>Výstupní nárok

Element **OutputClaim** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ano | Identifikátor typu deklarace identity, který je již definován v části ClaimsSchema v souboru zásad nebo v nadřazeném souboru zásad. |
| Defaultvalue | Ne | Výchozí hodnota, která se má použít k vytvoření deklarace, pokud deklarace označená claimTypeReferenceId neexistuje, takže výslednou deklaraci lze použít jako InputClaim podle technického profilu. |
|Hodnota AlwaysUseDefaultValue |Ne |Vynutit použití výchozí hodnoty.  |
| Typ deklarace programu Partner | Ne | Identifikátor typu deklarace externího partnera, na který se mapuje zadaný typ deklarace zásad. Pokud není zadán atribut PartnerClaimType, je zadaný typ deklarace zásad mapován na typ deklarace partnerské deklarace se stejným názvem. Tuto vlastnost použijte, pokud se název typu deklarace tvrdí liší od druhé strany. Například první název deklarace je "givenName", zatímco partner používá deklaraci s názvem "first_name". |

## <a name="outputclaimstransformations"></a>OutputClaimsTransformace

**OutputClaimsTransformations** Element obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| OutputClaimsTransformace | 1:n | Identifikátory transformací deklarací, které by měly být provedeny před odesláním jakékoli deklarace identity poskytovateli deklarací nebo předávající straně. Deklarace transformace lze upravit existující deklarace identity Schema nebo generovat nové. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformace

Element **OutputClaimsTransformation** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor transformace deklarací, která je již definována v souboru zásad nebo v nadřazeném souboru zásad. |

## <a name="validationtechnicalprofiles"></a>ValidaceTechnickéProfily

Prvek **ValidationTechnicalProfiles** obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| ValidaceTechnicalProfile | 1:n | Identifikátory technických profilů, které se používají, ověřují některé nebo všechny výstupní deklarace referenčního technického profilu. Všechna vstupní tvrzení referenčního technického profilu musí být uvedena ve výstupních tvrzeních referenčního technického profilu. |

### <a name="validationtechnicalprofile"></a>ValidaceTechnicalProfile

Prvek **ValidationTechnicalProfile** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor technického profilu, který je již definován v souboru zásad nebo v nadřazeném souboru zásad. |

## <a name="subjectnaminginfo"></a>Informace o pojmenování předmětu

**SubjectNamingInfo** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Typ deklarace pohledávky | Ano | Identifikátor typu deklarace identity, který je již definován v části ClaimsSchema v souboru zásad. |

## <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

Prvek **IncludeTechnicalProfile** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor technického profilu, který je již definován v souboru zásad nebo v nadřazeném souboru zásad. |

## <a name="usetechnicalprofileforsessionmanagement"></a>useTechnicalProfileForSessionManagement Management

Prvek **UseTechnicalProfileForSessionManagement** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor technického profilu, který je již definován v souboru zásad nebo v nadřazeném souboru zásad. |

## <a name="enabledforuserjourneys"></a>EnabledForUserJourneys

**ClaimsProviderSelections** v cestě uživatele definuje seznam možností výběru zprostředkovatele deklarací identity a jejich pořadí. S **EnabledForUserJourneys** prvek filtrujete, který deklarace identity zprostředkovatelje k dispozici uživateli. Prvek **EnabledForUserJourneys** obsahuje jednu z následujících hodnot:

- **Vždy**proveďte technický profil.
- **Nikdy**, přeskočte technický profil.
- **OnClaimsExistence** spustit pouze v případě, že existuje určité nároky, uvedené v technickém profilu.
- **OnItemExistInStringCollectionClaim**, spusťte pouze v případě, že položka existuje v deklaraci kolekce řetězců.
- **OnItemAbsenceInStringCollectionClaim** spustit pouze v případě, že položka neexistuje v deklaraci kolekce řetězců.

Použití **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** nebo **OnItemAbsenceInStringCollectionClaim**, vyžaduje, abyste poskytli následující metadata: **ClaimTypeOnWhichToEnable** určuje typ deklarace, která má být vyhodnocena, **ClaimValueOnWhichToEnable** určuje hodnotu, která má být porovnána.

Následující technický profil je proveden pouze v případě, že `facebook.com`kolekce řetězce **identityProviders** obsahuje hodnotu :

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
