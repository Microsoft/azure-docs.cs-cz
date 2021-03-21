---
title: Příklady transformace deklarací řetězců pro vlastní zásady
titleSuffix: Azure AD B2C
description: Příklady transformace deklarací identity pro schéma IEF (identity Experience Framework) Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85574b7d33af6d9abfe25f5af4d811255f08ce4b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452233"
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

Transformace deklarací **AssertStringClaimsAreEqual** je vždy prováděna z [technického profilu ověření](validation-technical-profile.md) , který je volán pomocí [technického profilu s vlastním uplatněním](self-asserted-technical-profile.md)nebo pomocí [DisplayConrtol](display-controls.md). `UserMessageIfClaimsTransformationStringsAreNotEqual`Metadata technického profilu s vlastním uplatněním řídí chybovou zprávu, která je prezentována uživateli. Chybové zprávy lze [lokalizovat](localization-string-ids.md#claims-transformations-error-messages).


![Spuštění AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

Pomocí této transformace deklarací identity se můžete ujistit, že dvě ClaimTypes mají stejnou hodnotu. V takovém případě je vyvolána chybová zpráva. Následující příklad zkontroluje, zda je **strongAuthenticationEmailAddress** ClaimType rovno hodnotě deklarace identity typu **e-mail** . V opačném případě je vyvolána chybová zpráva.

```xml
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
```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Technický profil s vlastním uplatněním volá ověřovací **přihlášení – neinteraktivní** technický profil.

```xml
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

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | řetězec | Deklarace ClaimType, která se má změnit. |
| InputParameter | toCase | řetězec | Jedna z následujících hodnot: `LOWER` nebo `UPPER` . |
| OutputClaim | outputClaim | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. |

Pomocí této transformace deklarace identity změňte libovolný řetězec ClaimType na nižší nebo velká písmena.

```xml
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

| Položka | TransformationClaimType | Typ dat | Poznámky |
|----- | ----------------------- | --------- | ----- |
| InputParameter | hodnota | řetězec | Řetězec, který má být nastaven. Tento vstupní parametr podporuje [výrazy transformace deklarací řetězců](string-transformations.md#string-claim-transformations-expressions). |
| OutputClaim | createdClaim | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací, s hodnotou zadanou ve vstupním parametru. |

Tuto transformaci deklarací identity použijte k nastavení hodnoty ClaimType typu String.

```xml
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

## <a name="copyclaimifpredicatematch"></a>CopyClaimIfPredicateMatch

Hodnotu deklarace identity zkopírujte na jinou, pokud hodnota vstupní deklarace odpovídá predikátu výstupních deklarací. 

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | řetězec | Typ deklarace, který má být zkopírován. |
| OutputClaim | outputClaim | řetězec | Typ deklarace identity, který je vytvořen po vyvolání této transformace deklarací. Hodnota vstupní deklarace identity je porovnána s tímto predikátem deklarace identity. |

V následujícím příkladu se zkopíruje hodnota deklarace identity signInName do deklarace phoneNumber, jenom pokud je signInName telefonní číslo. Úplnou ukázku najdete v tématu Zásady pro [telefonní číslo nebo e-mailové přihlašovací](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/Phone_Email_Base.xml) informace pro úvodní sadu.

```xml
<ClaimsTransformation Id="SetPhoneNumberIfPredicateMatch" TransformationMethod="CopyClaimIfPredicateMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="signInName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-1"></a>Příklad 1

- Vstupní deklarace identity:
    - **inputClaim**: bob@contoso.com
- Deklarace výstupů:
    - **outputClaim**: deklarace výstupu se nezměnila z původní hodnoty.

### <a name="example-2"></a>Příklad 2

- Vstupní deklarace identity:
    - **inputClaim**: + 11234567890
- Deklarace výstupů:
    - **outputClaim**: + 11234567890

## <a name="compareclaims"></a>CompareClaims

Určete, zda jedna deklarace identity řetězce je shodná s jinou. Výsledkem je nový logický typ ClaimType s hodnotou `true` nebo `false` .

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | řetězec | První typ deklarace identity, který se má porovnat. |
| InputClaim | inputClaim2 | řetězec | Druhý typ deklarace identity, který se má porovnat. |
| InputParameter | operátor | řetězec | Možné hodnoty: `EQUAL` nebo `NOT EQUAL` . |
| InputParameter | ignoreCase | boolean | Určuje, zda toto porovnání má ignorovat případ porovnávaných řetězců. |
| OutputClaim | outputClaim | boolean | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. |

Pomocí této transformace deklarací identity zjistíte, jestli je deklarace identity rovna jiné deklaraci identity. Například následující transformace deklarací identity kontroluje, jestli je hodnota deklarace **e-mailu** rovna deklaraci identity **Verified.email** .

```xml
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

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | řetězec | Typ deklarace identity, který se má porovnat. |
| InputParameter | operátor | řetězec | Možné hodnoty: `EQUAL` nebo `NOT EQUAL` . |
| InputParameter | compareTo | řetězec | porovnání řetězců, jedna z hodnot: Ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | boolean | Určuje, zda toto porovnání má ignorovat případ porovnávaných řetězců. |
| OutputClaim | outputClaim | boolean | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. |

Tuto transformaci deklarací identity můžete použít ke kontrole, jestli se deklarace identity rovná hodnotě, kterou jste zadali. Například následující transformace deklarací identity kontroluje, jestli je hodnota deklarace identity **termsOfUseConsentVersion** rovna `v1` .

```xml
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

Vytvoří náhodný řetězec pomocí generátoru náhodných čísel. Je-li generátor náhodných čísel typu `integer` , volitelně lze zadat parametr počáteční hodnoty a maximální číslo. Volitelný parametr formátu řetězce umožňuje, aby výstup byl zformátován pomocí a volitelný parametr Base64 určuje, zda je výstupem kódování Base64 randomGeneratorType [GUID, Integer] outputClaim (String).

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | řetězec | Určuje náhodnou hodnotu, která má být generována `GUID` (globální jedinečné ID) nebo `INTEGER` (číslo). |
| InputParameter | StringFormat – | řetězec | Volitelné Naformátujte náhodnou hodnotu. |
| InputParameter | base64 | boolean | Volitelné Převeďte náhodnou hodnotu na base64. Pokud je použit formát řetězce, hodnota po formátu řetězce je zakódována na base64. |
| InputParameter | maximumNumber | int | Volitelné `INTEGER` Jenom pro randomGeneratorType. Zadejte maximální číslo. |
| InputParameter | sazení  | int | Volitelné `INTEGER` Jenom pro randomGeneratorType. Zadejte počáteční hodnotu pro náhodnou hodnotu. Poznámka: stejné osazení vrací stejnou sekvenci náhodných čísel. |
| OutputClaim | outputClaim | řetězec | ClaimTypes, který bude vytvořen po vyvolání této transformace deklarací. Náhodná hodnota. |

Následující příklad generuje globální jedinečné ID. Tato transformace deklarací identity se používá k vytvoření náhodného názvu uživatele (UPN).

```xml
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

```xml
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
    - **StringFormat –**: OTP_{0}
    - **Base64**: false
- Deklarace výstupů:
    - **outputClaim**: OTP_853


## <a name="formatlocalizedstring"></a>FormatLocalizedString

Formátujte více deklarací podle zadaného lokalizovaného formátu řetězce. Tato transformace používá metodu jazyka C# `String.Format` .


| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaims |  |řetězec | Kolekce vstupních deklarací identity, které fungují jako formát řetězce {0} , {1} {2} parametry. |
| InputParameter | stringFormatId | řetězec |  `StringId` [Lokalizovaný řetězec](localization.md).   |
| OutputClaim | outputClaim | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. |

> [!NOTE]
> Maximální povolená velikost formátu řetězce je 4000.

Použití transformace deklarací FormatLocalizedString:

1. Definujte [řetězec lokalizace](localization.md)a přidružte ho k [vlastním kontrolnímu profilu](self-asserted-technical-profile.md).
1. `ElementType` `LocalizedString` Prvek elementu musí být nastaven na hodnotu `FormatLocalizedStringTransformationClaimType` .
1. `StringId`Je jedinečný identifikátor, který definujete, a později ho použijte v transformaci deklarací `stringFormatId` .
1. V transformaci deklarací identity zadejte seznam deklarací, které mají být nastaveny s lokalizovaným řetězcem. Pak nastavte na `stringFormatId` `StringId` lokalizovaný element řetězce. 
1. V [technickém profilu s vlastním](self-asserted-technical-profile.md)výkonem nebo na transformaci [vstupních nebo](display-controls.md) výstupních deklarací identity vytvořte odkaz na transformaci deklarací identity.


Následující příklad vygeneruje chybovou zprávu, pokud je účet již v adresáři. Příklad definuje lokalizované řetězce pro angličtinu (výchozí) a španělštinu.

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">The email '{0}' is already an account in this organization. Click Next to sign in with that account.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">Este correo electrónico "{0}" ya es una cuenta de esta organización. Haga clic en Siguiente para iniciar sesión con esa cuenta.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

Transformace deklarací vytvoří zprávu odpovědi na základě lokalizovaného řetězce. Zpráva obsahuje e-mailovou adresu uživatele vloženou do lokalizované Sting *ResponseMessge_EmailExists*.

```xml
<ClaimsTransformation Id="SetResponseMessageForEmailAlreadyExists" TransformationMethod="FormatLocalizedString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormatId" DataType="string" Value="ResponseMessge_EmailExists" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **inputClaim**: sarah@contoso.com
- Vstupní parametry:
    - **StringFormat –**: ResponseMessge_EmailExists
- Deklarace výstupů:
  - **outputClaim**: e-mail ' sarah@contoso.com ' je již v této organizaci účtem. Klikněte na tlačítko Další, abyste se přihlásili pomocí tohoto účtu.


## <a name="formatstringclaim"></a>FormatStringClaim

Naformátuje deklaraci identity podle poskytnutého formátovacího řetězce. Tato transformace používá metodu jazyka C# `String.Format` .

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |řetězec |Deklarace ClaimType, která funguje jako parametr formátu řetězce {0} . |
| InputParameter | StringFormat – | řetězec | Formát řetězce, včetně {0}  parametru. Tento vstupní parametr podporuje [výrazy transformace deklarací řetězců](string-transformations.md#string-claim-transformations-expressions).  |
| OutputClaim | outputClaim | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. |

> [!NOTE]
> Maximální povolená velikost formátu řetězce je 4000.

Tuto transformaci deklarací použijte k formátování libovolného řetězce s jedním parametrem {0} . V následujícím příkladu je vytvořena hodnota **userPrincipalName**. Všechny technické profily zprostředkovatele sociální identity, jako je například `Facebook-OAUTH` volání **CreateUserPrincipalName** pro generování třídy **userPrincipalName**.

```xml
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
    - **StringFormat –**: cpim_ {0} @ {RelyingPartyTenantId}
- Deklarace výstupů:
  - **outputClaim**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formátujte dvě deklarace podle poskytnutého formátovacího řetězce. Tato transformace používá metodu jazyka C# `String.Format` .

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |řetězec | Deklarace ClaimType, která funguje jako parametr formátu řetězce {0} . |
| InputClaim | inputClaim | řetězec | Deklarace ClaimType, která funguje jako parametr formátu řetězce {1} . |
| InputParameter | StringFormat – | řetězec | Formát řetězce včetně {0} {1} parametrů a. Tento vstupní parametr podporuje [výrazy transformace deklarací řetězců](string-transformations.md#string-claim-transformations-expressions).   |
| OutputClaim | outputClaim | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. |

> [!NOTE]
> Maximální povolená velikost formátu řetězce je 4000.

Tuto transformaci deklarací použijte k formátování řetězce se dvěma parametry, {0} a {1} . Následující příklad vytvoří **DisplayName** se zadaným formátem:

```xml
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
    - **StringFormat –**: {0}{1}
- Deklarace výstupů:
    - **outputClaim**: Jan Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformation

Zkopíruje lokalizované řetězce do deklarací identity.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | Název lokalizovaného řetězce | řetězec | Seznam typů deklarací, které byly vytvořeny po vyvolání této transformace deklarací. |

Použití transformace deklarací GetLocalizedStringsTransformation:

1. Definujte [řetězec lokalizace](localization.md) a přidružte ho k [vlastním kontrolnímu profilu](self-asserted-technical-profile.md).
1. `ElementType` `LocalizedString` Prvek elementu musí být nastaven na hodnotu `GetLocalizedStringsTransformationClaimType` .
1. `StringId`Je jedinečný identifikátor, který definujete, a později ho použijte v transformaci deklarací.
1. V transformaci deklarací identity zadejte seznam deklarací, které mají být nastaveny s lokalizovaným řetězcem. `ClaimTypeReferenceId`Je odkaz na objekt ClaimType, který je již definován v části ClaimsSchema v zásadě. `TransformationClaimType`Je název lokalizovaného řetězce, jak je definován v `StringId` `LocalizedString` elementu.
1. V [technickém profilu s vlastním](self-asserted-technical-profile.md)výkonem nebo na transformaci [vstupních nebo](display-controls.md) výstupních deklarací identity vytvořte odkaz na transformaci deklarací identity.

![GetLocalizedStringsTransformation](./media/string-transformations/get-localized-strings-transformation.png)

Následující příklad vyhledá předmět e-mailu, tělo, zprávu kódu a podpis e-mailu z lokalizovaných řetězců. Tyto deklarace později používá vlastní šablona pro ověření e-mailu.

Definujte lokalizované řetězce pro angličtinu (výchozí) a španělštinu.

```xml
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

```xml
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

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | řetězec | Deklarace identity obsahující text, který má být vyhledán v deklaracích **restrictionValueClaim** s kolekcí **omezení** .  |
| OutputClaim | restrictionValueClaim | řetězec | Deklarace identity, která obsahuje kolekci **omezení** . Po vyvolání transformace deklarací bude hodnota této deklarace identity obsahovat hodnotu vybrané položky. |

Následující příklad vyhledá popis chybové zprávy na základě chybového klíče. Deklarace identity **responseMsg** obsahuje kolekci chybových zpráv, které mají být k dispozici koncovému uživateli, nebo k odeslání do předávající strany.

```xml
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
Transformace deklarací vyhledá text položky a vrátí její hodnotu. Pokud je omezení lokalizováno pomocí `<LocalizedCollection>` , transformace deklarací identity vrátí lokalizovanou hodnotu.

```xml
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

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | řetězec | Deklarace identity, která obsahuje hodnotu vyhledávání |
| InputParameter | |řetězec | Kolekce parametry. |
| InputParameter | errorOnFailedLookup | boolean | Řízení, zda je vrácena chyba, pokud není nalezeno žádné vyhledávání. |
| OutputClaim | inputParameterId | řetězec | ClaimTypes, který bude vytvořen po vyvolání této transformace deklarací. Hodnota odpovídajícího `Id` . |

Následující příklad vyhledá název domény v jedné z kolekcí vstupní parametry. Transformace deklarací vyhledá název domény v identifikátoru a vrátí jeho hodnotu (ID aplikace).

```xml
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

Je-li `errorOnFailedLookup` vstupní parametr nastaven na hodnotu `true` , transformace deklarací **LookupValue** je vždy prováděna z [technického profilu ověření](validation-technical-profile.md) , který je volán pomocí [technického profilu s vlastním uplatněním](self-asserted-technical-profile.md)nebo [DisplayConrtol](display-controls.md). `LookupNotFound`Metadata technického profilu s vlastním uplatněním řídí chybovou zprávu, která je prezentována uživateli.

![Spuštění AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

Následující příklad vyhledá název domény v jedné z kolekcí vstupní parametry. Transformace deklarací vyhledá název domény v identifikátoru a vrátí jeho hodnotu (ID aplikace) nebo vyvolá chybovou zprávu.

```xml
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

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | řetězec | Hodnota deklarace identity je nastavená na hodnotu NULL. |

Tato transformace deklarace identity slouží k odebrání nepotřebných dat z kontejneru vlastností deklarací identity, takže soubor cookie relace bude menší. Následující příklad odebere hodnotu `TermsOfService` typu deklarace identity.

```xml
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

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | řetězec | Deklarace identity, která obsahuje e-mailovou adresu. |
| OutputClaim | doména | řetězec | Deklarace ClaimType vytvořená po vyvolání této transformace deklarací – doména |

Pomocí této transformace deklarací identity můžete analyzovat název domény za symbolem @ uživatele. Následující transformace deklarací identity ukazuje, jak analyzovat název domény z deklarace **e-mailu** .

```xml
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

Kontroluje, zda deklarace identity `claimToMatch` a `matchTo` vstupní parametr jsou stejné, a nastaví výstupní deklarace identity s hodnotou obsaženou ve `outputClaimIfMatched` vstupním parametru společně s deklarací výsledek s výstupem porovnání výsledků, která se má nastavit jako `true` nebo na `false` základě výsledku porovnání.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | řetězec | Typ deklarace, který se má porovnat. |
| InputParameter | matchTo | řetězec | Regulární výraz, který se má shodovat. |
| InputParameter | outputClaimIfMatched | řetězec | Hodnota, která má být nastavena, pokud jsou řetězce stejné. |
| InputParameter | extractGroups | boolean | Volitelné Určuje, zda se má při porovnávání regulárního výrazu extrahovat hodnoty skupin. Možné hodnoty: `true` , nebo `false` (výchozí). | 
| OutputClaim | outputClaim | řetězec | Pokud se regulární výraz shoduje, tato deklarace výstupu obsahuje hodnotu `outputClaimIfMatched` vstupního parametru. Nebo hodnotu null, pokud se neshoduje. |
| OutputClaim | regexCompareResultClaim | boolean | Regulární výraz odpovídá typu deklarace výstup výsledku, který má být nastaven jako `true` nebo `false` založený na výsledku porovnávání. |
| OutputClaim| Název deklarace identity| řetězec | Pokud je vstupní parametr extractGroups nastaven na hodnotu true, seznam typů deklarací, které jsou vytvořeny po vyvolání této transformace deklarací. Název claimType musí odpovídat názvu skupiny Regex. | 

### <a name="example-1"></a>Příklad 1

Ověří, zda je zadané telefonní číslo platné, na základě vzoru regulárního výrazu telefonního čísla.

```xml
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="SetClaimsIfRegexMatch">
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

- Vstupní deklarace identity:
    - **claimToMatch**: "64854114520"
- Vstupní parametry:
    - **matchTo**: "^ [0-9] {4,16} $"
    - **outputClaimIfMatched**: "-Phone"
- Deklarace výstupů:
    - **outputClaim**: "-Phone"
    - **regexCompareResultClaim**: true

### <a name="example-2"></a>Příklad 2

Zkontroluje, jestli je poskytnutá e-mailová adresa platná, a vrátí e-mailový alias.

```xml
<ClaimsTransformation Id="GetAliasFromEmail" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="(?&lt;mailAlias&gt;.*)@(.*)$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isEmail" />
    <InputParameter Id="extractGroups" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isEmailString" TransformationClaimType="regexCompareResultClaim" />
    <OutputClaim ClaimTypeReferenceId="mailAlias" />
  </OutputClaims>
</ClaimsTransformation>
```

- Vstupní deklarace identity:
    - **claimToMatch**: " emily@contoso.com "
- Vstupní parametry:
    - **matchTo**: `(?&lt;mailAlias&gt;.*)@(.*)$`
    - **outputClaimIfMatched**: "" e-mail "
    - **extractGroups**: true
- Deklarace výstupů:
    - **outputClaim**: "" e-mail "
    - **regexCompareResultClaim**: true
    - **mailAlias**: Emily
    
## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Kontroluje, zda deklarace identity a `matchTo` vstupní parametr jsou stejné, a nastaví výstupní deklarace identity s hodnotou přítomnou v `stringMatchMsg` a `stringMatchMsgCode` vstupními parametry společně s deklarací výsledek s výstupem porovnání výsledků, která se má nastavit jako `true` nebo na `false` základě výsledku porovnání.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | řetězec | Typ deklarace, který se má porovnat. |
| InputParameter | matchTo | řetězec | Řetězec, se kterým se má porovnat `inputClaim` . |
| InputParameter | stringComparison | řetězec | Možné hodnoty: `Ordinal` nebo `OrdinalIgnoreCase` . |
| InputParameter | stringMatchMsg | řetězec | První hodnota, která má být nastavena, pokud jsou řetězce stejné. |
| InputParameter | stringMatchMsgCode | řetězec | Druhá hodnota, která má být nastavena, pokud jsou řetězce stejné. |
| OutputClaim | outputClaim1 | řetězec | Pokud jsou řetězce stejné, obsahuje tato výstupní deklarace hodnotu `stringMatchMsg` vstupního parametru. |
| OutputClaim | outputClaim2 | řetězec | Pokud jsou řetězce stejné, obsahuje tato výstupní deklarace hodnotu `stringMatchMsgCode` vstupního parametru. |
| OutputClaim | stringCompareResultClaim | boolean | Typ deklarace výstup výsledku porovnání, který má být nastaven jako `true` nebo `false` založený na výsledku porovnání. |

Tuto transformaci deklarací identity můžete použít ke kontrole, jestli se deklarace identity rovná hodnotě, kterou jste zadali. Například následující transformace deklarací identity kontroluje, jestli je hodnota deklarace identity **termsOfUseConsentVersion** rovna `v1` . Pokud ano, změňte hodnotu na `v2` .

```xml
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

Kontroluje, zda deklarace identity a `matchTo` vstupní parametr jsou stejné, a nastaví výstupní deklarace identity s hodnotou obsaženou ve `outputClaimIfMatched` vstupním parametru společně s deklarací výsledek s výstupem porovnání výsledků, která se má nastavit jako `true` nebo na `false` základě výsledku porovnání.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | řetězec | Typ deklarace, který se má porovnat. |
| InputParameter | matchTo | řetězec | Řetězec, který má být porovnán s inputClaim. |
| InputParameter | stringComparison | řetězec | Možné hodnoty: `Ordinal` nebo `OrdinalIgnoreCase` . |
| InputParameter | outputClaimIfMatched | řetězec | Hodnota, která má být nastavena, pokud jsou řetězce stejné. |
| OutputClaim | outputClaim | řetězec | Pokud jsou řetězce stejné, obsahuje tato výstupní deklarace hodnotu `outputClaimIfMatched` vstupního parametru. Nebo hodnotu null, pokud se řetězce neshodují. |
| OutputClaim | stringCompareResultClaim | boolean | Typ deklarace výstup výsledku porovnání, který má být nastaven jako `true` nebo `false` založený na výsledku porovnání. |

Například následující transformace deklarací identity kontroluje, zda je hodnota deklarace identity **ageGroup** rovna `Minor` . Pokud ano, vraťte hodnotu na `B2C_V1_90001` .

```xml
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

Určí, zda zadaný podřetězec spadá do vstupní deklarace. Výsledkem je nový logický typ ClaimType s hodnotou `true` nebo `false` . `true` Pokud parametr value spadá do tohoto řetězce, jinak `false` .

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | řetězec | Typ deklarace, který má být prohledán. |
|InputParameter|obsahuje|řetězec|Hodnota, která se má hledat|
|InputParameter|ignoreCase|řetězec|Určuje, zda toto porovnání má ignorovat velikost řetězce, který je porovnán.|
| OutputClaim | outputClaim | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation. Logický indikátor, pokud se v rámci vstupní deklarace vyskytuje dílčí řetězec. |

Pomocí této transformace deklarací identity zkontrolujete, zda typ deklarace identity obsahuje dílčí řetězec. Následující příklad zkontroluje, zda `roles` typ deklarace řetězce obsahuje hodnotu **admin**.

```xml
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

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | řetězec | Typ deklarace identity, který obsahuje řetězec. |
| InputParameter | Indexu | int | Počáteční pozice znaku v podřetězci vycházející z nuly v této instanci. |
| InputParameter | length | int | Počet znaků v podřetězci. |
| OutputClaim | outputClaim | boolean | Řetězec, který je ekvivalentní podřetězci délky začínající v startIndex v této instanci, nebo prázdný, pokud je hodnota startIndex rovna délce této instance a délka je nula. |

Můžete například získat předponu země/oblasti telefonního čísla.


```xml
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

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | řetězec | Typ deklarace identity, který obsahuje řetězec. |
| InputParameter | oldValue | řetězec | Řetězec, který má být prohledán. |
| InputParameter | newValue | řetězec | Řetězec, který má nahradit všechny výskyty `oldValue` |
| OutputClaim | outputClaim | boolean | Řetězec, který je ekvivalentní aktuálnímu řetězci s tím rozdílem, že všechny instance oldValue jsou nahrazeny newValue. Pokud se v aktuální instanci nenalezne oldValue, vrátí tato metoda aktuální instanci beze změny. |

Například Normalizujte telefonní číslo odebráním `-` znaků.


```xml
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
    - **NewValue**: ""
- Deklarace výstupů:
    - **outputClaim**: "+ 164411452054"

## <a name="stringjoin"></a>StringJoin

Zřetězí prvky zadaného typu deklarace kolekce řetězců pomocí zadaného oddělovače mezi jednotlivými prvky nebo členy.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | Třída StringCollection | Kolekce obsahující řetězce, které mají být zřetězeny. |
| InputParameter | Oddělovač | řetězec | Řetězec, který má být použit jako oddělovač, například čárka `,` . |
| OutputClaim | outputClaim | řetězec | Řetězec, který se skládá z členů `inputClaim` kolekce řetězců oddělený `delimiter` vstupním parametrem. |

Následující příklad přebírá řetězcovou kolekci rolí uživatele a převede ho na řetězec oddělovače čárky. Tuto metodu můžete použít k uložení kolekce řetězců v uživatelském účtu Azure AD. Později, když přečtete účet z adresáře, použijte `StringSplit` k převedení řetězce oddělovače čárky zpátky na kolekci řetězců.

```xml
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

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | řetězec | Typ deklarace řetězce, který obsahuje podřetězce, které mají být rozděleny. |
| InputParameter | Oddělovač | řetězec | Řetězec, který má být použit jako oddělovač, například čárka `,` . |
| OutputClaim | outputClaim | Třída StringCollection | Kolekce řetězců, jejíž prvky obsahují podřetězce v tomto řetězci, které jsou odděleny `delimiter` vstupním parametrem. |

Následující příklad přijímá řetězec s oddělovači rolí uživatele čárkami a převede ho na kolekci řetězců.

```xml
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

## <a name="string-claim-transformations-expressions"></a>Výrazy transformace deklarace řetězce
Výrazy transformace deklarace v Azure AD B2C vlastní zásady poskytují kontextové informace o ID tenanta a ID technického profilu.

  | Výraz | Popis | Příklad |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | Název technického profileIdu | Facebook – OAUTH |
 | `{RelyingPartyTenantId}` | ID tenanta zásady předávající strany. | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | ID tenanta architektury vztahu důvěryhodnosti. | your-tenant.onmicrosoft.com |
