---
title: Příklady transformace deklarací řetězců pro vlastní zásady
titleSuffix: Azure AD B2C
description: Příklady transformace deklarací identity pro schéma IEF (identity Experience Framework) Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: df0bd87fffba8ed70c60da358b38079d3d017c76
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505641"
---
# <a name="string-claims-transformations"></a>Transformace deklarací řetězců

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek popisuje příklady použití transformací deklarace identity schématu rozhraní identity v Azure Active Directory B2C (Azure AD B2C). Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Porovnejte dvě deklarace identity a vyvolejte výjimku, pokud se neshodují podle zadaného porovnání inputClaim1, inputClaim2 a stringComparison.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | Typ první deklarace, který se má porovnat. |
| inputClaim | inputClaim2 | string | Typ druhé deklarace identity, který se má porovnat. |
| InputParameter | stringComparison | string | porovnání řetězců, jedna z hodnot: Ordinal, OrdinalIgnoreCase. |

Transformace deklarací **AssertStringClaimsAreEqual** je vždy prováděna z [technického profilu ověření](validation-technical-profile.md) , který je volán pomocí [technického profilu s vlastním uplatněním](self-asserted-technical-profile.md)nebo pomocí [DisplayConrtol](display-controls.md). `UserMessageIfClaimsTransformationStringsAreNotEqual` metadata pro technický profil s vlastním uplatněním řídí chybovou zprávu, která je prezentována uživateli.


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


Technický profil **neinteraktivního ověření přihlášení** volá transformaci deklarací **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** .
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
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Vstupní parametry:
  - **stringComparison**: ordinalIgnoreCase
- Výsledek: došlo k chybě

## <a name="changecase"></a>ChangeCase

Změní velikost případu poskytnuté deklarace na nižší nebo velká písmena v závislosti na operátoru.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | Deklarace ClaimType, která se má změnit. |
| InputParameter | toCase | string | Jedna z následujících hodnot: `LOWER` nebo `UPPER`. |
| outputClaim | outputClaim | string | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. |

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
  - **e-mail**: SomeOne@contoso.com
- Vstupní parametry:
    - **toCase**: nižší
- Deklarace výstupů:
  - **e-mail**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Vytvoří deklaraci řetězce ze zadaného vstupního parametru v transformaci.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | string | Řetězec, který má být nastaven |
| outputClaim | createdClaim | string | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací, s hodnotou zadanou ve vstupním parametru. |

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
    - **hodnota**: podmínka služby contoso...
- Deklarace výstupů:
    - **createdClaim**: služba TOS ClaimType obsahuje "podmínkami služby společnosti Contoso..." osa.

## <a name="compareclaims"></a>CompareClaims

Určete, zda jedna deklarace identity řetězce je shodná s jinou. Výsledkem je nový logický typ ClaimType s hodnotou `true` nebo `false`.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | První typ deklarace identity, který se má porovnat. |
| inputClaim | inputClaim2 | string | Druhý typ deklarace identity, který se má porovnat. |
| InputParameter | operátor | string | Možné hodnoty: `EQUAL` nebo `NOT EQUAL`. |
| InputParameter | ignoreCase | Boolean | Určuje, zda toto porovnání má ignorovat případ porovnávaných řetězců. |
| outputClaim | outputClaim | Boolean | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. |

Pomocí této transformace deklarací identity zjistíte, jestli je deklarace identity rovna jiné deklaraci identity. Například následující transformace deklarací identity kontroluje, jestli je hodnota deklarace **e-mailu** stejná jako **ověřená deklarace identity e-mailu** .

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
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Vstupní parametry:
    - **operátor**: není rovno
    - **IgnoreCase**: pravda
- Deklarace výstupů:
    - **outputClaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Určuje, zda je hodnota deklarace identity shodná se vstupní hodnotou parametru.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | Typ deklarace identity, který se má porovnat. |
| InputParameter | operátor | string | Možné hodnoty: `EQUAL` nebo `NOT EQUAL`. |
| InputParameter | compareTo | string | porovnání řetězců, jedna z hodnot: Ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | Boolean | Určuje, zda toto porovnání má ignorovat případ porovnávaných řetězců. |
| outputClaim | outputClaim | Boolean | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. |

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
    - **operátor**: EQUAL
    - **IgnoreCase**: pravda
- Deklarace výstupů:
    - **outputClaim**: true

## <a name="createrandomstring"></a>CreateRandomString

Vytvoří náhodný řetězec pomocí generátoru náhodných čísel. Pokud je generátor náhodných čísel typu `integer`, může být k dispozici i parametr počáteční hodnoty a maximální číslo. Volitelný parametr formátu řetězce umožňuje, aby výstup byl zformátován pomocí a volitelný parametr Base64 určuje, zda je výstupem kódování Base64 randomGeneratorType [GUID, Integer] outputClaim (String).

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | string | Určuje náhodnou hodnotu, která se má vygenerovat, `GUID` (globální jedinečné ID) nebo `INTEGER` (číslo). |
| InputParameter | StringFormat – | string | Volitelné Naformátujte náhodnou hodnotu. |
| InputParameter | Base | Boolean | Volitelné Převeďte náhodnou hodnotu na base64. Pokud je použit formát řetězce, hodnota po formátu řetězce je zakódována na base64. |
| InputParameter | maximumNumber | int | Volitelné Pouze pro `INTEGER` randomGeneratorType. Zadejte maximální číslo. |
| InputParameter | sazení  | int | Volitelné Pouze pro `INTEGER` randomGeneratorType. Zadejte počáteční hodnotu pro náhodnou hodnotu. Poznámka: stejné osazení vrací stejnou sekvenci náhodných čísel. |
| outputClaim | outputClaim | string | ClaimTypes, který bude vytvořen po vyvolání této transformace deklarací. Náhodná hodnota. |

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
    - **randomGeneratorType**: celé číslo
    - **maximumNumber**: 1000
    - **StringFormat –** : OTP_{0}
    - **Base64**: false
- Deklarace výstupů:
    - **outputClaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Naformátuje deklaraci identity podle poskytnutého formátovacího řetězce. Tato transformace používá metodu C# `String.Format`.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim |string |Deklarace ClaimType, která funguje jako formát řetězce {0} parametr. |
| InputParameter | StringFormat – | string | Formát řetězce včetně parametru {0}. |
| outputClaim | outputClaim | string | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. |

Tuto transformaci deklarací použijte k formátování libovolného řetězce s jedním parametrem {0}. V následujícím příkladu je vytvořena hodnota **userPrincipalName**. Všechny technické profily zprostředkovatele sociální identity, například `Facebook-OAUTH`, zavolají **CreateUserPrincipalName** pro generování **userPrincipalName**.

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
  - **outputClaim**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formátujte dvě deklarace podle poskytnutého formátovacího řetězce. Tato transformace používá metodu C# `String.Format`.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim |string | Deklarace ClaimType, která funguje jako formát řetězce {0} parametr. |
| inputClaim | inputClaim | string | Deklarace ClaimType, která funguje jako formát řetězce {1} parametr. |
| InputParameter | StringFormat – | string | Formát řetězce včetně parametrů {0} a {1} |
| outputClaim | outputClaim | string | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. |

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
    - **StringFormat –** : {0} {1}
- Deklarace výstupů:
    - **outputClaim**: Jan Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformation 

Zkopíruje lokalizované řetězce do deklarací identity.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| outputClaim | Název lokalizovaného řetězce | string | Seznam typů deklarací, které jsou vytvářeny po vyvolání této transformace deklarací. |

Použití transformace deklarací GetLocalizedStringsTransformation:

1. Definujte [řetězec lokalizace](localization.md) a přidružte ho k [vlastním kontrolnímu profilu](self-asserted-technical-profile.md).
1. `ElementType` `LocalizedString` elementu musí být nastavené na `GetLocalizedStringsTransformationClaimType`.
1. `StringId` je jedinečný identifikátor, který definujete, a později ho použijte v transformaci deklarací.
1. V transformaci deklarací identity zadejte seznam deklarací, které mají být nastaveny s lokalizovaným řetězcem. `ClaimTypeReferenceId` je odkaz na objekt ClaimType, který je již definován v části ClaimsSchema v zásadách. `TransformationClaimType` je název lokalizovaného řetězce, jak je definován v `StringId` elementu `LocalizedString`.
1. V [technickém profilu s vlastním](self-asserted-technical-profile.md)výkonem nebo na transformaci [vstupních nebo](display-controls.md) výstupních deklarací identity vytvořte odkaz na transformaci deklarací identity.

![GetLocalizedStringsTransformation](./media/string-transformations/get-localized-strings-transformation.png)

Následující příklad vyhledá předmět e-mailu, tělo, zprávu kódu a podpis e-mailu z lokalizovaných řetězců. Tyto deklarace později používá vlastní šablona pro ověření e-mailu.

Definujte lokalizované řetězce pro angličtinu (výchozí) a španělštinu.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
     </LocalizedStrings>
   </LocalizedResources>
   <LocalizedResources Id="api.localaccountsignup.es">
     <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Atentamente</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

Transformace deklarací nastaví hodnotu *předmětu* typu deklarace identity s hodnotou `StringId` *email_subject*.

```XML
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Deklarace výstupů:
  - **Předmět**: kód pro ověření e-mailu účtu contoso
  - **zpráva**: Děkujeme za ověření účtu! 
  - **codeIntro**: váš kód je 
  - **podpis**: pozdrav  


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Vyhledává se položka z kolekce **omezení** deklarace identity.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | mapFromClaim | string | Deklarace identity obsahující text, který má být vyhledán v deklaracích **restrictionValueClaim** s kolekcí **omezení** .  |
| outputClaim | restrictionValueClaim | string | Deklarace identity, která obsahuje kolekci **omezení** . Po vyvolání transformace deklarací bude hodnota této deklarace identity obsahovat hodnotu vybrané položky. |

Následující příklad vyhledá popis chybové zprávy na základě chybového klíče. Deklarace identity **responseMsg** obsahuje kolekci chybových zpráv, které mají být k dispozici koncovému uživateli, nebo k odeslání do předávající strany.

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
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
    - **restrictionValueClaim**: nemůžete se přihlásit, protože jste nezletilější.

## <a name="lookupvalue"></a>LookupValue

Vyhledá hodnotu deklarace ze seznamu hodnot na základě hodnoty jiné deklarace identity.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputParameterId | string | Deklarace identity, která obsahuje hodnotu vyhledávání |
| InputParameter | |string | Kolekce parametry. |
| InputParameter | errorOnFailedLookup | Boolean | Řízení, zda je vrácena chyba, pokud není nalezeno žádné vyhledávání. |
| outputClaim | inputParameterId | string | ClaimTypes, který bude vytvořen po vyvolání této transformace deklarací. Hodnota odpovídajícího `Id`. |

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
    - **Microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: false
- Deklarace výstupů:
    - **outputClaim**: c7026f88-4299-4cdb-965d-3f166464b8a9

Pokud je vstupní parametr `errorOnFailedLookup` nastaven na hodnotu `true`, transformace deklarací **LookupValue** se vždy provádí z [technického profilu ověření](validation-technical-profile.md) , který je volán pomocí [technického profilu s vlastním uplatněním](self-asserted-technical-profile.md)nebo [DisplayConrtol](display-controls.md). `LookupNotFound` metadata pro technický profil s vlastním uplatněním řídí chybovou zprávu, která je prezentována uživateli.

![Spuštění AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

Následující příklad vyhledá název domény v jedné z kolekcí vstupní parametry. Transformace deklarací vyhledá název domény v identifikátoru a vrátí jeho hodnotu (ID aplikace) nebo vyvolá chybovou zprávu.

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **inputParameterId**: Live.com
- Vstupní parametry:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **Microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: true
- Chyba:
    - Nebyla nalezena žádná shoda pro vstupní hodnotu deklarace v seznamu ID vstupních parametrů a errorOnFailedLookup je true.


## <a name="nullclaim"></a>NullClaim

Vyčistěte hodnotu dané deklarace identity.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| outputClaim | claim_to_null | string | Hodnota deklarace identity je nastavená na hodnotu NULL. |

Tato transformace deklarace identity slouží k odebrání nepotřebných dat z kontejneru vlastností deklarací identity, takže soubor cookie relace bude menší. Následující příklad odebere hodnotu `TermsOfService`ho typu deklarace identity.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Vstupní deklarace identity:
    - **outputClaim**: Vítejte v aplikaci Contoso. Pokud budete pokračovat v procházení a používání tohoto webu, souhlasíte s tím, že splníte následující podmínky a ujednání, které jsou vázány...
- Deklarace výstupů:
    - **outputClaim**: null

## <a name="parsedomain"></a>ParseDomain

Načte doménovou část e-mailové adresy.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | emailAddress | string | Deklarace identity, která obsahuje e-mailovou adresu. |
| outputClaim | domain | string | Deklarace ClaimType vytvořená po vyvolání této transformace deklarací – doména |

Pomocí této transformace deklarací identity můžete analyzovat název domény za symbolem @ uživatele. Následující transformace deklarací identity ukazuje, jak analyzovat název domény z deklarace **e-mailu** .

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
  - **EmailAddress**: joe@outlook.com
- Deklarace výstupů:
    - **doména**: Outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

Kontroluje, zda je deklarace identity `claimToMatch` a vstupní parametr `matchTo` stejné, a nastaví výstupní deklarace identity s hodnotou přítomnou v `outputClaimIfMatched` vstupní parametr společně s deklarací výsledek s výstupem porovnání výsledků, která se nastaví jako `true` nebo `false` na základě výsledku porovnání.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | string | Typ deklarace, který se má porovnat. |
| InputParameter | matchTo | string | Regulární výraz, který se má shodovat. |
| InputParameter | outputClaimIfMatched | string | Hodnota, která má být nastavena, pokud jsou řetězce stejné. |
| outputClaim | outputClaim | string | Pokud se regulární výraz shoduje, tato deklarace výstupu obsahuje hodnotu `outputClaimIfMatched` vstupní parametr. Nebo hodnotu null, pokud se neshoduje. |
| outputClaim | regexCompareResultClaim | Boolean | Regulární výraz odpovídá typu deklarace výstup výsledku, který má být nastaven jako `true` nebo `false` na základě výsledku porovnávání. |

Například ověří, zda je zadané telefonní číslo platné na základě vzoru regulárního výrazu telefonního čísla.  

```XML
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="setClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phone" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="^[0-9]{4,16}$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isPhone" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isPhoneBoolean" TransformationClaimType="regexCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **claimToMatch**: "64854114520"
- Vstupní parametry:
    - **matchTo**: "^ [0-9]{4,16}$"
    - **outputClaimIfMatched**: "-Phone"
- Deklarace výstupů:
    - **outputClaim**: "-Phone"
    - **regexCompareResultClaim**: true

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Kontroluje, zda je deklarace řetězce a vstupní parametr `matchTo` stejné, a nastaví výstupní deklarace identity s hodnotou přítomnou v `stringMatchMsg` a `stringMatchMsgCode` vstupními parametry společně s deklarací výsledek s výstupem porovnání výsledků, která se má nastavit jako `true` nebo `false` na základě výsledku porovnání.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | string | Typ deklarace, který se má porovnat. |
| InputParameter | matchTo | string | Řetězec, který má být porovnán s `inputClaim`. |
| InputParameter | stringComparison | string | Možné hodnoty: `Ordinal` nebo `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | string | První hodnota, která má být nastavena, pokud jsou řetězce stejné. |
| InputParameter | stringMatchMsgCode | string | Druhá hodnota, která má být nastavena, pokud jsou řetězce stejné. |
| outputClaim | outputClaim1 | string | Pokud jsou řetězce stejné, obsahuje tato deklarace výstup hodnotu `stringMatchMsg` vstupním parametrem. |
| outputClaim | outputClaim2 | string | Pokud jsou řetězce stejné, obsahuje tato deklarace výstup hodnotu `stringMatchMsgCode` vstupním parametrem. |
| outputClaim | stringCompareResultClaim | Boolean | Typ deklarace výstup porovnání výsledků, který má být nastaven jako `true` nebo `false` na základě výsledku porovnání. |

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
    - **stringMatchMsg**: B2C_V1_90005
    - **stringMatchMsgCode**: provozovatelé nástroje pro upgrade na verzi v2
- Deklarace výstupů:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: provozovatelé nástroje pro upgrade na verzi v2
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Kontroluje, zda je hodnota deklarace řetězce a vstupní parametr `matchTo` shodná, a nastaví výstupní deklarace identity s hodnotou přítomnou v parametru `outputClaimIfMatched` Input, společně s deklarací Compare Output Output, která se má nastavit jako `true` nebo `false` na základě výsledku porovnání.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | string | Typ deklarace, který se má porovnat. |
| InputParameter | matchTo | string | Řetězec, který má být porovnán s inputClaim. |
| InputParameter | stringComparison | string | Možné hodnoty: `Ordinal` nebo `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | string | Hodnota, která má být nastavena, pokud jsou řetězce stejné. |
| outputClaim | outputClaim | string | Pokud jsou řetězce stejné, obsahuje tato deklarace výstup hodnotu `outputClaimIfMatched` vstupním parametrem. Nebo hodnotu null, pokud se řetězce neshodují. |
| outputClaim | stringCompareResultClaim | Boolean | Typ deklarace výstup porovnání výsledků, který má být nastaven jako `true` nebo `false` na základě výsledku porovnání. |

Například následující transformace deklarací identity kontroluje, zda je hodnota deklarace identity **ageGroup** rovna hodnotě `Minor`. Pokud ano, vraťte hodnotu pro `B2C_V1_90001`.

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
    - **claimToMatch**: podverze
- Vstupní parametry:
    - **matchTo**: podverze
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched**: B2C_V1_90001
- Deklarace výstupů:
    - **isMinorResponseCode**: B2C_V1_90001
    - **podverze**: true


## <a name="stringcontains"></a>StringContains

Určí, zda zadaný podřetězec spadá do vstupní deklarace. Výsledkem je nový logický typ ClaimType s hodnotou `true` nebo `false`. `true`, pokud se v tomto řetězci vyskytne parametr hodnoty, jinak `false`.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | string | Typ deklarace, který má být prohledán. |
|InputParameter|Obsahuje|string|Hodnota, která se má hledat|
|InputParameter|ignoreCase|string|Určuje, zda toto porovnání má ignorovat velikost řetězce, který je porovnán.|
| outputClaim | outputClaim | string | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation. Logický indikátor, pokud se v rámci vstupní deklarace vyskytuje dílčí řetězec. |

Pomocí této transformace deklarací identity zkontrolujete, zda typ deklarace identity obsahuje dílčí řetězec. Následující příklad zkontroluje, zda typ deklarace `roles` řetězců obsahuje hodnotu **admin**.

```XML
<ClaimsTransformation Id="CheckIsAdmin" TransformationMethod="StringContains"> 
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="contains" DataType="string" Value="admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>         
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **inputClaim**: "admin, schvalovatel, Editor"
- Vstupní parametry:
    - **obsahuje**: admin,
    - **IgnoreCase**: pravda
- Deklarace výstupů:
    - **outputClaim**: true 

## <a name="stringsubstring"></a>StringSubstring

Extrahuje části typu deklarace řetězce, počínaje znakem na zadané pozici a vrátí zadaný počet znaků.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | string | Typ deklarace identity, který obsahuje řetězec. |
| InputParameter | Počáteční index | int | Počáteční pozice znaku v podřetězci vycházející z nuly v této instanci. |
| InputParameter | length | int | Počet znaků v podřetězci. |
| outputClaim | outputClaim | Boolean | Řetězec, který je ekvivalentní podřetězci délky začínající v startIndex v této instanci, nebo prázdný, pokud je hodnota startIndex rovna délce této instance a délka je nula. |

Můžete například získat předponu země telefonního čísla.  


```XML
<ClaimsTransformation Id="GetPhonePrefix" TransformationMethod="StringSubstring">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="startIndex" DataType="int" Value="0" />
  <InputParameter Id="length" DataType="int" Value="2" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phonePrefix" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **inputClaim**: "+ 1644114520"
- Vstupní parametry:
    - **startIndex**: 0
    - **Délka**: 2
- Deklarace výstupů:
    - **outputClaim**: "+ 1"

## <a name="stringreplace"></a>StringReplace

Vyhledá v řetězci typu deklarace zadanou hodnotu a vrátí nový řetězec typu deklarace, ve kterém jsou všechny výskyty zadaného řetězce v aktuálním řetězci nahrazeny jiným zadaným řetězcem.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | string | Typ deklarace identity, který obsahuje řetězec. |
| InputParameter | oldValue | string | Řetězec, který má být prohledán. |
| InputParameter | newValue | string | Řetězec, který nahradí všechny výskyty `oldValue` |
| outputClaim | outputClaim | Boolean | Řetězec, který je ekvivalentní aktuálnímu řetězci s tím rozdílem, že všechny instance oldValue jsou nahrazeny newValue. Pokud se v aktuální instanci nenalezne oldValue, vrátí tato metoda aktuální instanci beze změny. |

Například Normalizujte telefonní číslo tak, že odeberete `-`é znaky.  


```XML
<ClaimsTransformation Id="NormalizePhoneNumber" TransformationMethod="StringReplace">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="oldValue" DataType="string" Value="-" />
  <InputParameter Id="newValue" DataType="string" Value="" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **inputClaim**: "+ 164-411-452-054"
- Vstupní parametry:
    - **OldValue**: "-"
    - **Délka**: ""
- Deklarace výstupů:
    - **outputClaim**: "+ 164411452054"

## <a name="stringjoin"></a>StringJoin

Zřetězí prvky zadaného typu deklarace kolekce řetězců pomocí zadaného oddělovače mezi jednotlivými prvky nebo členy.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | stringCollection | Kolekce obsahující řetězce, které mají být zřetězeny. |
| InputParameter | oddělovač | string | Řetězec, který má být použit jako oddělovač, například čárka `,`. |
| outputClaim | outputClaim | string | Řetězec, který se skládá z členů kolekce řetězců `inputClaim`, oddělený vstupním parametrem `delimiter`. |
  
Následující příklad přebírá řetězcovou kolekci rolí uživatele a převede ho na řetězec oddělovače čárky. Tuto metodu můžete použít k uložení kolekce řetězců v uživatelském účtu Azure AD. Později při čtení účtu z adresáře použijte `StringSplit` k převedení řetězce oddělovače čárky zpátky na kolekci řetězců.

```XML
<ClaimsTransformation Id="ConvertRolesStringCollectionToCommaDelimiterString" TransformationMethod="StringJoin">
  <InputClaims>
   <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter DataType="string" Id="delimiter" Value="," />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="rolesCommaDelimiterConverted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **inputClaim**: ["admin"; "Author"; "Reader"]
- Vstupní parametry:
  - **oddělovač**: ","
- Deklarace výstupů:
  - **outputClaim**: "admin, autor, čtenář"


## <a name="stringsplit"></a>StringSplit

Vrátí pole řetězce, které obsahuje podřetězce v této instanci, které jsou odděleny elementy zadaného řetězce.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | string | Typ deklarace řetězce, který obsahuje podřetězce, které mají být rozděleny. |
| InputParameter | oddělovač | string | Řetězec, který má být použit jako oddělovač, například čárka `,`. |
| outputClaim | outputClaim | stringCollection | Kolekce řetězců, jejíž prvky obsahují podřetězce v tomto řetězci, které jsou odděleny parametrem `delimiter` Input. |
  
Následující příklad přijímá řetězec s oddělovači rolí uživatele čárkami a převede ho na kolekci řetězců.

```XML
<ClaimsTransformation Id="ConvertRolesToStringCollection" TransformationMethod="StringSplit">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="rolesCommaDelimiter" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
  <InputParameter DataType="string" Id="delimiter" Value="," />
    </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="roles" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **inputClaim**: "admin, autor, čtenář"
- Vstupní parametry:
  - **oddělovač**: ","
- Deklarace výstupů:
  - **outputClaim**: ["admin"; "Author"; "Reader"]
