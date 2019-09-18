---
title: Příklady transformačních deklarací identity pro schéma architektury prostředí identity Azure Active Directory B2C | Microsoft Docs
description: Příklady transformačních deklarací identity pro schéma rozhraní Azure Active Directory B2C prostředí identity
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 83379cc194f23ebff977babc7124a7bc90f4bc60
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063444"
---
# <a name="string-claims-transformations"></a>Transformace deklarací řetězců

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek popisuje příklady použití transformací deklarace identity schématu rozhraní identity v Azure Active Directory B2C (Azure AD B2C). Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Porovnejte dvě deklarace identity a vyvolejte výjimku, pokud se neshodují podle zadaného porovnání inputClaim1, inputClaim2 a stringComparison.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | řetězec | Typ první deklarace, který se má porovnat. |
| InputClaim | inputClaim2 | řetězec | Typ druhé deklarace identity, který se má porovnat. |
| InputParameter | stringComparison | řetězec | porovnání řetězců, jedna z hodnot: Ordinal, OrdinalIgnoreCase. |

Transformace deklarací **AssertStringClaimsAreEqual** je vždy prováděna z [technického profilu ověření](validation-technical-profile.md) , který je volán pomocí [technického profilu s vlastním uplatněním](self-asserted-technical-profile.md). Metadata technického profilu **UserMessageIfClaimsTransformationStringsAreNotEqual** s vlastním uplatněním řídí chybovou zprávu, která se zobrazí uživateli.

![Spuštění AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

Pomocí této transformace deklarací identity se můžete ujistit, že dvě ClaimTypes mají stejnou hodnotu. V takovém případě je vyvolána chybová zpráva. Následující příklad zkontroluje, zda je **strongAuthenticationEmailAddress** ClaimType rovno hodnotě deklarace identity typu **e-mail** . V opačném případě je vyvolána chybová zpráva.

```XML
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


Technický profil neinteraktivního ověření **přihlášení** volá transformaci deklarací **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** .
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Technický profil s vlastním uplatněním volá ověřovací **přihlášení – neinteraktivní** technický profil.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
    - Vstupní parametry:
  - **stringComparison**: ordinalIgnoreCase
- Vyústit Došlo k chybě

## <a name="changecase"></a>ChangeCase

Změní velikost případu poskytnuté deklarace na nižší nebo velká písmena v závislosti na operátoru.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | řetězec | Deklarace ClaimType, která se má změnit. |
| InputParameter | toCase | řetězec | Jedna z následujících hodnot: `LOWER` nebo. `UPPER` |
| OutputClaim | OutputClaim | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. |

Pomocí této transformace deklarace identity změňte libovolný řetězec ClaimType na nižší nebo velká písmena.

```XML
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **e-mail**:SomeOne@contoso.com
- Vstupní parametry:
    - **toCase**: LOWER
- Deklarace výstupů:
  - **e-mail**:someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Vytvoří deklaraci řetězce ze zadaného vstupního parametru v zásadě.

| Položka | TransformationClaimType | Typ dat | Poznámky |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | řetězec | Řetězec, který má být nastaven |
| OutputClaim | createdClaim | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací, s hodnotou zadanou ve vstupním parametru. |

Tuto transformaci deklarací identity použijte k nastavení hodnoty ClaimType typu String.

```XML
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní parametr:
    - **hodnota**: Podmínka služby společnosti Contoso...
- Deklarace výstupů:
    - **createdClaim**: Služba TOS ClaimType obsahuje "podmínkami služby společnosti Contoso..." osa.

## <a name="compareclaims"></a>CompareClaims

Určete, zda jedna deklarace identity řetězce je shodná s jinou. Výsledkem je nový logický typ ClaimType s hodnotou `true` nebo. `false`

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | řetězec | První typ deklarace identity, který se má porovnat. |
| InputClaim | inputClaim2 | řetězec | Druhý typ deklarace identity, který se má porovnat. |
| InputParameter | operator | řetězec | Možné hodnoty: `EQUAL` nebo `NOT EQUAL`. |
| InputParameter | ignoreCase | boolean | Určuje, zda toto porovnání má ignorovat případ porovnávaných řetězců. |
| OutputClaim | OutputClaim | boolean | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. |

Pomocí této transformace deklarací identity zjistíte, jestli je deklarace identity rovna jiné deklaraci identity. Například následující transformace deklarací identity kontroluje, jestli je hodnota deklarace **e-mailu** stejná jako ověřená deklarace identity **e-mailu** .

```XML
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
- Vstupní parametry:
    - **operátor**:  NENÍ ROVNO
    - **IgnoreCase**: pravda
- Deklarace výstupů:
    - **outputClaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Určuje, zda je hodnota deklarace identity shodná se vstupní hodnotou parametru.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | řetězec | Typ deklarace identity, který se má porovnat. |
| InputParameter | operator | řetězec | Možné hodnoty: `EQUAL` nebo `NOT EQUAL`. |
| InputParameter | compareTo | řetězec | porovnání řetězců, jedna z hodnot: Ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | boolean | Určuje, zda toto porovnání má ignorovat případ porovnávaných řetězců. |
| OutputClaim | OutputClaim | boolean | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. |

Tuto transformaci deklarací identity můžete použít ke kontrole, jestli se deklarace identity rovná hodnotě, kterou jste zadali. Například následující transformace deklarací identity kontroluje, jestli je hodnota deklarace identity **termsOfUseConsentVersion** rovna `v1`.

```XML
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad
- Vstupní deklarace identity:
    - **inputClaim1**: V1
- Vstupní parametry:
    - **compareTo**: V1
    - **operátor**: VÝŠI
    - **IgnoreCase**: pravda
- Deklarace výstupů:
    - **outputClaim**: true

## <a name="createrandomstring"></a>CreateRandomString

Vytvoří náhodný řetězec pomocí generátoru náhodných čísel. Je-li generátor náhodných čísel typu `integer`, volitelně lze zadat parametr počáteční hodnoty a maximální číslo. Volitelný parametr formátu řetězce umožňuje, aby výstup byl zformátován pomocí a volitelný parametr Base64 určuje, zda je výstupem kódování Base64 randomGeneratorType [GUID, Integer] outputClaim (String).

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | řetězec | Určuje náhodnou hodnotu, `GUID` která má být generována (globální jedinečné ID) nebo `INTEGER` (číslo). |
| InputParameter | StringFormat – | řetězec | Volitelné Naformátujte náhodnou hodnotu. |
| InputParameter | Base | boolean | Volitelné Převeďte náhodnou hodnotu na base64. Pokud je použit formát řetězce, hodnota po formátu řetězce je zakódována na base64. |
| InputParameter | maximumNumber | int | Volitelné Jenom `INTEGER` pro randomGeneratorType. Zadejte maximální číslo. |
| InputParameter | sazení  | int | Volitelné Jenom `INTEGER` pro randomGeneratorType. Zadejte počáteční hodnotu pro náhodnou hodnotu. Poznámka: stejné osazení vrací stejnou sekvenci náhodných čísel. |
| OutputClaim | OutputClaim | řetězec | ClaimTypes, který bude vytvořen po vyvolání této transformace deklarací. Náhodná hodnota. |

Následující příklad generuje globální jedinečné ID. Tato transformace deklarací identity se používá k vytvoření náhodného názvu uživatele (UPN).

```XML
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Příklad

- Vstupní parametry:
    - **randomGeneratorType**: GUID
- Deklarace výstupů:
    - **outputClaim**: bc8bedd2-AAA3-411E-bdee-2f1810b73dfc

Následující příklad generuje celočíselnou náhodnou hodnotu mezi 0 a 1000. Hodnota je formátována na OTP_ {Random Value}.

```XML
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní parametry:
    - **randomGeneratorType**: INTEGER
    - **maximumNumber**: 1000
    - **stringFormat**: OTP_{0}
    - **Base64**: false
- Deklarace výstupů:
    - **outputClaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Naformátuje deklaraci identity podle poskytnutého formátovacího řetězce. Tato transformace používá C# `String.Format` metodu.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |řetězec |Deklarace ClaimType, která funguje jako parametr {0} formátu řetězce. |
| InputParameter | StringFormat – | řetězec | Formát řetězce, včetně {0} parametru. |
| OutputClaim | OutputClaim | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. |

Tuto transformaci deklarací použijte k formátování libovolného řetězce s jedním {0}parametrem. V následujícím příkladu je vytvořena hodnota **userPrincipalName**. Všechny technické profily zprostředkovatele sociální identity, jako `Facebook-OAUTH` je například volání **CreateUserPrincipalName** pro generování třídy **userPrincipalName**.

```XML
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Vstupní parametry:
    - **StringFormat –** : cpim_{0}@ {RelyingPartyTenantId}
- Deklarace výstupů:
  - **outputClaim**:cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formátujte dvě deklarace podle poskytnutého formátovacího řetězce. Tato transformace používá C# metodu **String. Format** .

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |řetězec | Deklarace ClaimType, která funguje jako parametr {0} formátu řetězce. |
| InputClaim | InputClaim | řetězec | Deklarace ClaimType, která funguje jako parametr {1} formátu řetězce. |
| InputParameter | StringFormat – | řetězec | Formát řetězce včetně {0} parametrů a {1} . |
| OutputClaim | OutputClaim | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. |

Tuto transformaci deklarací použijte k formátování řetězce se dvěma parametry, {0} a {1}. Následující příklad vytvoří **DisplayName** se zadaným formátem:

```XML
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **inputClaim1**: Jana
    - **inputClaim2**: Fernando
- Vstupní parametry:
    - **StringFormat –** : {0}{1}
- Deklarace výstupů:
    - **outputClaim**: Jana Fernando

## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Vyhledává se položka z kolekce **omezení** deklarace identity.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | řetězec | Deklarace identity obsahující text, který má být vyhledán v deklaracích **restrictionValueClaim** s kolekcí **omezení** .  |
| OutputClaim | restrictionValueClaim | řetězec | Deklarace identity, která obsahuje kolekci **omezení** . Po vyvolání transformace deklarací bude hodnota této deklarace identity obsahovat hodnotu vybrané položky. |

Následující příklad vyhledá popis chybové zprávy na základě chybového klíče. Deklarace identity **responseMsg** obsahuje kolekci chybových zpráv, které mají být k dispozici koncovému uživateli, nebo k odeslání do předávající strany.

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
Transformace deklarací vyhledá text položky a vrátí její hodnotu. Pokud je omezení lokalizováno pomocí `<LocalizedCollection>`, transformace deklarací identity vrátí lokalizovanou hodnotu.

```XML
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />        
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **mapFromClaim**: B2C_V1_90001
- Deklarace výstupů:
    - **restrictionValueClaim**: Nemůžete se přihlásit, protože jste nezletilí.

## <a name="lookupvalue"></a>LookupValue

Vyhledá hodnotu deklarace ze seznamu hodnot na základě hodnoty jiné deklarace identity.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | řetězec | Deklarace identity, která obsahuje hodnotu vyhledávání |
| InputParameter | |řetězec | Kolekce parametry. |
| InputParameter | errorOnFailedLookup | boolean | Řízení, zda je vrácena chyba, pokud není nalezeno žádné vyhledávání. |
| OutputClaim | inputParameterId | řetězec | ClaimTypes, který bude vytvořen po vyvolání této transformace deklarací. Hodnota odpovídajícího ID. |

Následující příklad vyhledá název domény v jedné z kolekcí vstupní parametry. Transformace deklarací vyhledá název domény v identifikátoru a vrátí jeho hodnotu (ID aplikace).

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **inputParameterId**: test.com
- Vstupní parametry:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: false
- Deklarace výstupů:
    - **outputClaim**:  c7026f88-4299-4cdb-965d-3f166464b8a9

## <a name="nullclaim"></a>NullClaim

Vyčistěte hodnotu dané deklarace identity.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | řetězec | Deklarace identity má hodnotu NULL. |

Pomocí této transformace deklarace identity můžete z kontejneru vlastností deklarací identity odebrat nepotřebná data. Soubory cookie relace tak budou menší. Následující příklad odebere hodnotu `TermsOfService` typu deklarace identity.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Vstupní deklarace identity:
    - **outputClaim**: Vítá vás aplikace contoso. Pokud budete pokračovat v procházení a používání tohoto webu, souhlasíte s tím, že splníte následující podmínky a ujednání, které jsou vázány...
- Deklarace výstupů:
    - **outputClaim**: NULL

## <a name="parsedomain"></a>ParseDomain

Načte doménovou část e-mailové adresy.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | řetězec | Deklarace identity, která obsahuje e-mailovou adresu. |
| OutputClaim | doména | řetězec | Deklarace ClaimType vytvořená po vyvolání této transformace deklarací – doména |

Pomocí této transformace deklarací identity můžete analyzovat název domény za symbolem @ uživatele. To může být užitečné v případě, že se z dat auditu odstraňují osobně identifikovatelné osobní údaje (PII). Následující transformace deklarací identity ukazuje, jak analyzovat název domény z deklarace **e-mailu** .

```XML
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **EmailAddress**:joe@outlook.com
- Deklarace výstupů:
    - **doména**: Outlook.com

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Kontroluje, zda deklarace identity a `matchTo` vstupní parametr jsou stejné, a nastaví výstupní deklarace identity s hodnotou přítomnou v `stringMatchMsg` a `stringMatchMsgCode` vstupními parametry společně s deklarací výsledek s výstupem porovnání výsledků, která se má `true` nastavit jako nebo .`false` na základě výsledku porovnání.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | řetězec | Typ deklarace, který se má porovnat. |
| InputParameter | matchTo | řetězec | Řetězec, se `inputClaim`kterým se má porovnat. |
| InputParameter | stringComparison | řetězec | Možné hodnoty: `Ordinal` nebo `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | řetězec | První hodnota, která má být nastavena, pokud jsou řetězce stejné. |
| InputParameter | stringMatchMsgCode | řetězec | Druhá hodnota, která má být nastavena, pokud jsou řetězce stejné. |
| OutputClaim | outputClaim1 | řetězec | Pokud jsou řetězce stejné, obsahuje tato výstupní deklarace hodnotu `stringMatchMsg` vstupního parametru. |
| OutputClaim | outputClaim2 | řetězec | Pokud jsou řetězce stejné, obsahuje tato výstupní deklarace hodnotu `stringMatchMsgCode` vstupního parametru. |
| OutputClaim | stringCompareResultClaim | boolean | Typ deklarace výstup výsledku porovnání, který má být nastaven jako `true` nebo `false` založený na výsledku porovnání. |

Tuto transformaci deklarací identity můžete použít ke kontrole, jestli se deklarace identity rovná hodnotě, kterou jste zadali. Například následující transformace deklarací identity kontroluje, jestli je hodnota deklarace identity **termsOfUseConsentVersion** rovna `v1`. Pokud ano, změňte hodnotu na `v2`.

```XML
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **inputClaim**: V1
- Vstupní parametry:
    - **matchTo**: V1
    - **stringComparison**: ordinalIgnoreCase
    - **stringMatchMsg**:  B2C_V1_90005
    - **stringMatchMsgCode**:  Rozhraní TOS se upgraduje na v2.
- Deklarace výstupů:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: Rozhraní TOS se upgraduje na v2.
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Kontroluje, zda deklarace identity a `matchTo` vstupní parametr jsou stejné, a nastaví výstupní deklarace identity s hodnotou obsaženou ve `outputClaimIfMatched` vstupním parametru společně s deklarací výsledek s výstupem porovnání výsledků, která se má `true` nastavit `false` jako nebo na základě výsledek porovnání

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | řetězec | Typ deklarace, který se má porovnat. |
| InputParameter | matchTo | řetězec | Řetězec, který má být porovnán s inputClaim. |
| InputParameter | stringComparison | řetězec | Možné hodnoty: `Ordinal` nebo `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | řetězec | Hodnota, která má být nastavena, pokud jsou řetězce stejné. |
| OutputClaim | OutputClaim | řetězec | Pokud jsou řetězce stejné, obsahuje tato výstupní deklarace hodnotu `outputClaimIfMatched` vstupního parametru. Nebo hodnotu null, pokud se řetězce neshodují. |
| OutputClaim | stringCompareResultClaim | boolean | Typ deklarace výstup výsledku porovnání, který má být nastaven jako `true` nebo `false` založený na výsledku porovnání. |

Například následující transformace deklarací identity kontroluje, zda je hodnota deklarace identity **ageGroup** rovna `Minor`. Pokud ano, vraťte hodnotu na `B2C_V1_90001`.

```XML
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **claimToMatch**: Podverze
- Vstupní parametry:
    - **matchTo**: Podverze
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched**:  B2C_V1_90001
- Deklarace výstupů:
    - **isMinorResponseCode**: B2C_V1_90001
    - podverze: true

