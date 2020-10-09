---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Zadejte element TechnicalProfiles vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a788134f64066b0469d34fbfbacacd8c45438bde
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85203142"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **TechnicalProfiles** obsahuje sadu technických profilů, které podporuje zprostředkovatel deklarací identity. Každý zprostředkovatel deklarací identity musí mít jeden nebo více technických profilů, které určují koncové body a protokoly potřebné ke komunikaci se zprostředkovatelem deklarací. Zprostředkovatel deklarací může mít více technických profilů.

```xml
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

Element **TechnicalProfile** obsahuje následující atribut:

| Atribut | Povinné | Popis |
|---------|---------|---------|
| Id | Ano | Jedinečný identifikátor technického profilu. Na technický profil se dá odkazovat pomocí tohoto identifikátoru z dalších prvků v souboru zásad. Například **OrchestrationSteps** a **ValidationTechnicalProfile**. |

**TechnicalProfile** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Doména | 0:1 | Název domény pro technický profil. Pokud například váš technický profil určuje poskytovatele identity Facebooku, název domény je Facebook.com. |
| DisplayName | 1:1 | Název technického profilu, který se může zobrazit uživatelům |
| Popis | 0:1 | Popis technického profilu, který se může zobrazit uživatelům |
| Protokol | 0:1 | Protokol používaný pro komunikaci s druhou stranou. |
| Metadata | 0:1 | Kolekce párů klíč/hodnota, které jsou využívány protokolem pro komunikaci s koncovým bodem v průběhu transakce. |
| InputTokenFormat | 0:1 | Formát vstupního tokenu. Možné hodnoty: `JSON` , `JWT` , `SAML11` , nebo `SAML2` . `JWT`Hodnota představuje JSON web token podle specifikace IETF. `SAML11`Hodnota představuje token zabezpečení SAML 1,1 podle specifikace pro Oasis.  `SAML2`Hodnota představuje token zabezpečení SAML 2,0 podle specifikace pro Oasis. |
| OutputTokenFormat | 0:1 | Formát výstupního tokenu. Možné hodnoty: `JSON` , `JWT` , `SAML11` , nebo `SAML2` . |
| CryptographicKeys | 0:1 | Seznam kryptografických klíčů, které jsou používány v technickém profilu. |
| InputClaimsTransformations | 0:1 | Seznam dříve definovaných odkazů na transformace deklarací identity, které by měly být provedeny před odesláním jakýchkoli deklarací do zprostředkovatele deklarací nebo předávající strany. |
| InputClaims | 0:1 | Seznam dříve definovaných odkazů na typy deklarací identity, které jsou pořízeny jako vstup v technickém profilu. |
| PersistedClaims | 0:1 | Seznam dříve definovaných odkazů na typy deklarací, které jsou trvale zachované zprostředkovatelem deklarací identity, který souvisí s technickým profilem. |
| DisplayClaims | 0:1 | Seznam dříve definovaných odkazů na typy deklarací identity, které jsou uvedeny zprostředkovatelem deklarací identity, který souvisí s [technickým profilem s vlastním uplatněním](self-asserted-technical-profile.md). Funkce DisplayClaims je aktuálně ve **verzi Preview**. |
| OutputClaims | 0:1 | Seznam dříve definovaných odkazů na typy deklarací, které jsou pořízeny jako výstup v technickém profilu. |
| OutputClaimsTransformations | 0:1 | Seznam dříve definovaných odkazů na transformace deklarací identity, které by měly být provedeny po přijetí deklarací identity od zprostředkovatele deklarací identity. |
| ValidationTechnicalProfiles | 0: n | Seznam odkazů na jiné technické profily, které technický profil používá pro účely ověření. Další informace najdete v tématu [technický profil ověření](validation-technical-profile.md) .|
| SubjectNamingInfo | 0:1 | Řídí produkci názvu subjektu v tokenech, kde je název subjektu zadán nezávisle na deklaracích identity. Například protokol OAuth nebo SAML.  |
| IncludeInSso | 0:1 |  Zda použití tohoto technického profilu by mělo pro relaci použít chování jednotného přihlašování (SSO), nebo místo toho vyžadovat explicitní interakci. Tento prvek je platný jenom v profilech SelfAsserted používaných v rámci technického profilu ověření. Možné hodnoty: `true` (výchozí), nebo `false` . |
| IncludeClaimsFromTechnicalProfile | 0:1 | Identifikátor technického profilu, ze kterého chcete přidat všechny deklarace identity vstupu a výstupu do tohoto technického profilu. Odkazovaný technický profil musí být definován ve stejném souboru zásad. |
| IncludeTechnicalProfile |0:1 | Identifikátor technického profilu, ze kterého chcete přidat všechna data do tohoto technického profilu. |
| UseTechnicalProfileForSessionManagement | 0:1 | Jiný technický profil, který se má použít pro správu relací. |
|EnabledForUserJourneys| 0:1 |Řídí, jestli se technický profil spustí na cestě uživatele.  |

## <a name="protocol"></a>Protokol

Element **Protocol** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Název | Ano | Název platného protokolu podporovaného Azure AD B2C, který se používá jako součást technického profilu. Možné hodnoty: `OAuth1` , `OAuth2` , `SAML2` , `OpenIdConnect` , `Proprietary` , nebo `None` . |
| Obslužná rutina | No | Pokud je název protokolu nastaven na `Proprietary` , zadejte plně kvalifikovaný název sestavení, které je používáno Azure AD B2C k určení obslužné rutiny protokolu. |

## <a name="metadata"></a>Metadata

Element **metadata** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Položka | 0: n | Metadata vztahující se k technickému profilu. Každý typ technického profilu má jinou sadu položek metadat. Další informace najdete v části typy technických profilů. |

### <a name="item"></a>Položka

Element **Item** elementu **metadata** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Klíč | Ano | Klíč metadat. Seznam položek metadat najdete u každého typu technického profilu. |

## <a name="cryptographickeys"></a>CryptographicKeys

Element **CryptographicKeys** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Klíč | 1: n | Kryptografický klíč použitý v tomto technickém profilu. |

### <a name="key"></a>Klíč

**Klíčový** element obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Id | No | Jedinečný identifikátor konkrétního páru klíčů, na který se odkazuje z dalších prvků v souboru zásad. |
| StorageReferenceId | Ano | Identifikátorem kontejneru klíčů úložiště, na který odkazuje jiný element v souboru zásad. |

## <a name="inputclaimstransformations"></a>InputClaimsTransformations

Element **InputClaimsTransformations** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1: n | Identifikátor transformace deklarací identity, který by měl být proveden před odesláním jakýchkoli deklarací do zprostředkovatele deklarací nebo předávající strany. Transformaci deklarací identity lze použít k úpravě stávajících deklarací ClaimsSchema nebo k vygenerování nových. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

Element **InputClaimsTransformation** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor transformace deklarací identity už je definovaný v souboru zásad nebo v nadřazeném souboru zásad. |

## <a name="inputclaims"></a>InputClaims

Element **InputClaims** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| InputClaim | 1: n | Očekával se vstupní typ deklarace identity. |

### <a name="inputclaim"></a>InputClaim

Element **InputClaim** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ano | Identifikátor typu deklarace identity, který je už definovaný v oddílu ClaimsSchema v souboru zásad nebo v nadřazeném souboru zásad. |
| Hodnot | No | Výchozí hodnota, která se má použít k vytvoření deklarace identity, pokud deklarace identity uvedená v ClaimTypeReferenceId neexistuje, aby se dala výsledná deklarace použít jako InputClaim pro technický profil. |
| PartnerClaimType | No | Identifikátor typu deklarace externího partnera, na který se mapuje zadaný typ deklarace identity. Pokud není zadán atribut PartnerClaimType, je zadaný typ deklarace identity mapován na partnerský typ deklarace se stejným názvem. Tuto vlastnost použijte v případě, že se název typu deklarace identity liší od druhé strany. Například první název deklarace je "daný", zatímco partner používá deklaraci s názvem "first_name". |

## <a name="displayclaims"></a>DisplayClaims

Element **DisplayClaims** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| DisplayClaim | 1: n | Očekával se vstupní typ deklarace identity. |

Funkce DislayClaims je aktuálně ve **verzi Preview**.

### <a name="displayclaim"></a>DisplayClaim

Element **DisplayClaim** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | No | Identifikátor typu deklarace identity, který je už definovaný v oddílu ClaimsSchema v souboru zásad nebo v nadřazeném souboru zásad. |
| DisplayControlReferenceId | No | Identifikátor [ovládacího prvku zobrazení](display-controls.md) , který je už definovaný v oddílu ClaimsSchema v souboru zásad nebo v nadřazeném souboru zásad. |
| Požaduje se | No | Určuje, zda je požadována deklarace identity zobrazení. |

**DisplayClaim** vyžaduje, abyste zadali buď `ClaimTypeReferenceId` nebo `DisplayControlReferenceId` .

### <a name="persistedclaims"></a>PersistedClaims

Element **PersistedClaims** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| PersistedClaim | 1: n | Typ deklarace identity, který se má zachovat |

### <a name="persistedclaim"></a>PersistedClaim

Element **PersistedClaim** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ano | Identifikátor typu deklarace identity, který je už definovaný v oddílu ClaimsSchema v souboru zásad nebo v nadřazeném souboru zásad. |
| Hodnot | No | Výchozí hodnota, která se má použít k vytvoření deklarace identity, pokud deklarace identity uvedená v ClaimTypeReferenceId neexistuje, aby se dala výsledná deklarace použít jako InputClaim pro technický profil. |
| PartnerClaimType | No | Identifikátor typu deklarace externího partnera, na který se mapuje zadaný typ deklarace identity. Pokud není zadán atribut PartnerClaimType, je zadaný typ deklarace identity mapován na partnerský typ deklarace se stejným názvem. Tuto vlastnost použijte v případě, že se název typu deklarace identity liší od druhé strany. Například první název deklarace je "daný", zatímco partner používá deklaraci s názvem "first_name". |

## <a name="outputclaims"></a>OutputClaims

Element **OutputClaims** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| OutputClaim | 1: n | Očekával se výstupní typ deklarace identity. |

### <a name="outputclaim"></a>OutputClaim

Element **OutputClaim** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ano | Identifikátor typu deklarace identity, který je už definovaný v oddílu ClaimsSchema v souboru zásad nebo v nadřazeném souboru zásad. |
| Hodnot | No | Výchozí hodnota, která se má použít k vytvoření deklarace identity, pokud deklarace identity uvedená v ClaimTypeReferenceId neexistuje, aby se dala výsledná deklarace použít jako InputClaim pro technický profil. |
|AlwaysUseDefaultValue |No |Vynutit použití výchozí hodnoty.  |
| PartnerClaimType | No | Identifikátor typu deklarace externího partnera, na který se mapuje zadaný typ deklarace identity. Pokud není zadán atribut PartnerClaimType, je zadaný typ deklarace identity mapován na partnerský typ deklarace se stejným názvem. Tuto vlastnost použijte v případě, že se název typu deklarace identity liší od druhé strany. Například první název deklarace je "daný", zatímco partner používá deklaraci s názvem "first_name". |

## <a name="outputclaimstransformations"></a>OutputClaimsTransformations

Element **OutputClaimsTransformations** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1: n | Identifikátory transformací deklarací identity, které se mají provést před odesláním deklarací do zprostředkovatele deklarací nebo předávající strany. Transformaci deklarací identity lze použít k úpravě stávajících deklarací ClaimsSchema nebo k vygenerování nových. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

Element **OutputClaimsTransformation** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor transformace deklarací identity už je definovaný v souboru zásad nebo v nadřazeném souboru zásad. |

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

Element **ValidationTechnicalProfiles** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | Identifikátory technických profilů, které se používají, ověřují některé nebo všechny výstupní deklarace identity odkazujícího technického profilu. Všechny vstupní deklarace odkazovaného technického profilu se musí objevit ve výstupních deklaracích referenčního technického profilu. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

Element **ValidationTechnicalProfile** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor technického profilu, který už je definovaný v souboru zásad nebo v nadřazeném souboru zásad. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

**SubjectNamingInfo** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ClaimType | Ano | Identifikátor typu deklarace identity, který je už definovaný v oddílu ClaimsSchema v souboru zásad. |

## <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

Element **IncludeTechnicalProfile** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor technického profilu, který už je definovaný v souboru zásad, nebo v nadřazeném souboru zásad. |

## <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

Element **UseTechnicalProfileForSessionManagement** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor technického profilu, který už je definovaný v souboru zásad nebo v nadřazeném souboru zásad. |

## <a name="enabledforuserjourneys"></a>EnabledForUserJourneys

**ClaimsProviderSelections** v cestě uživatele definuje seznam možností výběru zprostředkovatele deklarací a jejich pořadí. Pomocí filtrovaného prvku **EnabledForUserJourneys** , který je k dispozici pro uživatele. Element **EnabledForUserJourneys** obsahuje jednu z následujících hodnot:

- **Vždy**spusťte technický profil.
- **Nikdy**, přeskočte technický profil.
- **OnClaimsExistence** se spustí jenom v případě, že existuje určitá deklarace identity zadaná v technickém profilu.
- **OnItemExistenceInStringCollectionClaim**spustí se pouze v případě, že položka existuje v deklaraci kolekce řetězců.
- **OnItemAbsenceInStringCollectionClaim** provést pouze v případě, že položka v deklaraci kolekce řetězců neexistuje.

Pomocí **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** nebo **OnItemAbsenceInStringCollectionClaim**musíte zadat následující metadata: **ClaimTypeOnWhichToEnable** určuje typ deklarace identity, který se má vyhodnotit, **ClaimValueOnWhichToEnable** Určuje hodnotu, která se má porovnat.

Následující technický profil se spustí pouze v případě, že kolekce řetězců **identityProviders** obsahuje hodnotu `facebook.com` :

```xml
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
