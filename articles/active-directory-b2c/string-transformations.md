---
title: Příklady transformace řetězce deklarací pro vlastní zásady
titleSuffix: Azure AD B2C
description: Řetězce deklarace transformace příklady pro rozhraní Identity Experience Framework (IEF) schéma Služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f08107874598a68fb5ce2a1a8a98b6a81d7b94d4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756790"
---
# <a name="string-claims-transformations"></a>Řetězcové deklarace identity transformace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek obsahuje příklady pro použití řetězce deklarace identity schématu rozhraní identity v Azure Active Directory B2C (Azure AD B2C). Další informace naleznete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Porovnejte dva deklarace identity a vyvolat výjimku, pokud nejsou stejné podle zadané ho porovnání inputClaim1, inputClaim2 a stringComparison.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim1 | řetězec | První typ tvrzení, který je třeba porovnat. |
| Vstupní deklarace | inputClaim2 | řetězec | Druhý typ tvrzení, který je třeba porovnat. |
| Parametr Input | Stringcomparison | řetězec | porovnání řetězců, jedna z hodnot: Ordinal, OrdinalIgnoreCase. |

**AssertStringClaimsAreEqual nároky** transformace je vždy proveden z [ověření technického profilu,](validation-technical-profile.md) který je volán [samostatně uplatněný technický profil](self-asserted-technical-profile.md)nebo [DisplayConrtol](display-controls.md). Metadata `UserMessageIfClaimsTransformationStringsAreNotEqual` samoobslužného technického profilu řídí chybovou zprávu, která je uživateli prezentována. Chybové zprávy lze [lokalizovat](localization-string-ids.md#claims-transformations-error-messages).


![AssertStringClaimsAreEqual provádění](./media/string-transformations/assert-execution.png)

Tuto transformaci deklarací můžete určit, dva ClaimTypes mají stejnou hodnotu. Pokud tomu tak není, je vyvolána chybová zpráva. Následující příklad zkontroluje, zda **se strongAuthenticationEmailAddress** ClaimType rovná **e-mailovému** typu ClaimType. V opačném případě je vyvolána chybová zpráva.

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


Technický profil **ověření login-NonInteractive** volá **assertemailandstrongauthenticationemailemailaddressareequal** deklarací identity transformace.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Samoobslužný technický profil volá ověření **login-NonInteractive** technický profil.

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

- Vstupní deklarace:
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
- Vstupní parametry:
  - **stringComparison**: ordinalIgnoreCase
- Výsledek: Vyvolána chyba

## <a name="changecase"></a>ChangeCase

Změní případ poskytnutého nároku na malá nebo velká písmena v závislosti na operátorovi.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim1 | řetězec | ClaimType změnit. |
| Parametr Input | toCase | řetězec | Jedna z následujících `LOWER` `UPPER`hodnot: nebo . |
| Výstupní nárok | outputClaim | řetězec | ClaimType, který je vytvořen po této deklarace transformace byla vyvolána. |

Pomocí této transformace deklarace změnit libovolný řetězec ClaimType na malá nebo velká písmena.

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

- Vstupní deklarace:
  - **e-mail**:SomeOne@contoso.com
- Vstupní parametry:
    - **toCase**: NIŽŠÍ
- Výstupní nároky:
  - **e-mail**:someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Vytvoří deklaraci řetězce z zadaný vstupní parametr v transformaci.

| Položka | TransformationClaimType | Typ dat | Poznámky |
|----- | ----------------------- | --------- | ----- |
| Parametr Input | value | řetězec | Řetězec, který má být nastaven. Tento vstupní parametr podporuje [výrazy transformace deklarací řetězců](string-transformations.md#string-claim-transformations-expressions). |
| Výstupní nárok | createdClaim | řetězec | ClaimType, který je vytvořen po této deklarace transformace byla vyvolána, s hodnotou zadanou ve vstupním parametru. |

Tato transformace deklarací slouží k nastavení hodnoty typu řetězce ClaimType.

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
    - **hodnota:** Podmínky služby Contoso...
- Výstupní nároky:
    - **createdClaim**: Typ deklarace tos obsahuje "Podmínky služby Contoso..." Hodnotu.

## <a name="compareclaims"></a>Porovnat deklarace identity

Určete, zda je jedna deklarace řetězce rovna jiné. Výsledkem je nový logický claimtype s `true` `false`hodnotou nebo .

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim1 | řetězec | První typ deklarace, který je třeba porovnat. |
| Vstupní deklarace | inputClaim2 | řetězec | Druhý typ deklarace, který je třeba porovnat. |
| Parametr Input |  – operátor | řetězec | Možné `EQUAL` hodnoty: `NOT EQUAL`nebo . |
| Parametr Input | Ignorecase | Boolean | Určuje, zda má toto porovnání ignorovat případ porovnávaných řetězců. |
| Výstupní nárok | outputClaim | Boolean | ClaimType, který je vytvořen po této deklarace transformace byla vyvolána. |

Pomocí této transformace deklarací zkontrolujte, zda je deklarace rovná jiné deklaraci. Například následující transformace deklarací zkontroluje, zda se hodnota **deklarace e-mailu** rovná deklaraci **Verified.Email.**

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

- Vstupní deklarace:
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
- Vstupní parametry:
    - **operátor**: NOT EQUAL
    - **ignoreCase**: true
- Výstupní nároky:
    - **outputClaim**: true outputClaim : true outputClaim : true outputClaim

## <a name="compareclaimtovalue"></a>Porovnathodnotu

Určuje, zda je hodnota deklarace rovná hodnotě vstupního parametru.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim1 | řetězec | Typ deklarace, který je třeba porovnat. |
| Parametr Input |  – operátor | řetězec | Možné `EQUAL` hodnoty: `NOT EQUAL`nebo . |
| Parametr Input | Compareto | řetězec | porovnání řetězců, jedna z hodnot: Ordinal, OrdinalIgnoreCase. |
| Parametr Input | Ignorecase | Boolean | Určuje, zda má toto porovnání ignorovat případ porovnávaných řetězců. |
| Výstupní nárok | outputClaim | Boolean | ClaimType, který je vytvořen po této deklarace transformace byla vyvolána. |

Tuto transformaci deklarací můžete použít ke kontrole, zda je deklarace rovná zadané hodnotě. Například následující transformace deklarací zkontroluje, zda je hodnota **deklarace identity termsOfUseConsentVersion** rovna `v1`.

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
- Vstupní deklarace:
    - **inputClaim1**: v1
- Vstupní parametry:
    - **compareTo**: V1
    - **operátor**: EQUAL
    - **ignoreCase**: true
- Výstupní nároky:
    - **outputClaim**: true outputClaim : true outputClaim : true outputClaim

## <a name="createrandomstring"></a>Vytvořit randomstring

Vytvoří náhodný řetězec pomocí generátoru náhodných čísel. Je-li generátor náhodných `integer`čísel typu , může být volitelně zadán parametr osiva a maximální počet. Volitelný parametr formátu řetězce umožňuje formátovat výstup pomocí něj a volitelný base64 parametr určuje, zda je výstup base64 kódované randomGeneratorType [guid, celé] outputClaim (String).

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Parametr Input | randomGeneratorType | řetězec | Určuje náhodnou hodnotu, která `GUID` má být generována `INTEGER` (globální jedinečné ID) nebo (číslo). |
| Parametr Input | Stringformat | řetězec | [Nepovinné] Naformátujte náhodnou hodnotu. |
| Parametr Input | base64 | Boolean | [Nepovinné] Převeďte náhodnou hodnotu na base64. Pokud je použit formát řetězce, hodnota po formátu řetězce je kódován na base64. |
| Parametr Input | maximumČíslo | int | [Nepovinné] Pouze `INTEGER` pro randomGeneratorType. Zadejte maximální počet. |
| Parametr Input | Osiva  | int | [Nepovinné] Pouze `INTEGER` pro randomGeneratorType. Zadejte osivo pro náhodnou hodnotu. Poznámka: stejné semeno dává stejnou sekvenci náhodných čísel. |
| Výstupní nárok | outputClaim | řetězec | ClaimTypes, které budou vytvořeny po této deklarace transformace byla vyvolána. Náhodná hodnota. |

Následující příklad generuje globální jedinečné ID. Tato transformace deklarací identity se používá k vytvoření náhodného hlavního názvu uživatele (název principu uživatele).

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
- Výstupní nároky:
    - **outputClaim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

Následující příklad generuje celé číslo náhodná hodnota mezi 0 a 1000. Hodnota je formátována na OTP_{random value}.

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
    - **maximumČíslo**: 1000
    - **stringFormat**: OTP_{0}
    - **base64**: nepravdivé
- Výstupní nároky:
    - **outputClaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Naformátujte deklaraci podle zadaný formátovací řetězec. Tato transformace používá `String.Format` metodu C#.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim |řetězec |ClaimType, který funguje {0} jako parametr formátu řetězce. |
| Parametr Input | Stringformat | řetězec | Formát řetězce, včetně {0} parametru. Tento vstupní parametr podporuje [výrazy transformace deklarací řetězců](string-transformations.md#string-claim-transformations-expressions).  |
| Výstupní nárok | outputClaim | řetězec | ClaimType, který je vytvořen po této deklarace transformace byla vyvolána. |

Pomocí této transformace deklarací formátovat {0}libovolný řetězec s jedním parametrem . Následující příklad vytvoří **userPrincipalName**. Všechny technické profily zprostředkovatele `Facebook-OAUTH` sociální identity, například volání **CreateUserPrincipalName** pro generování **userPrincipalName**.

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

- Vstupní deklarace:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Vstupní parametry:
    - **stringFormat**:{0}cpim_ @{RelyingPartyTenantId}
- Výstupní nároky:
  - **outputClaim**:cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Naformátujte dvě deklarace podle zadaný formátovací řetězec. Tato transformace používá `String.Format` metodu C#.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim |řetězec | ClaimType, který funguje {0} jako parametr formátu řetězce. |
| Vstupní deklarace | inputClaim | řetězec | ClaimType, který funguje {1} jako parametr formátu řetězce. |
| Parametr Input | Stringformat | řetězec | Formát řetězce, včetně {0} {1} a parametry. Tento vstupní parametr podporuje [výrazy transformace deklarací řetězců](string-transformations.md#string-claim-transformations-expressions).   |
| Výstupní nárok | outputClaim | řetězec | ClaimType, který je vytvořen po této deklarace transformace byla vyvolána. |

Pomocí této transformace deklarací formátovat libovolný {0} {1}řetězec se dvěma parametry a . Následující příklad vytvoří **displayName** se zadaným formátem:

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

- Vstupní deklarace:
    - **inputClaim1**: Joe
    - **inputClaim2**: Fernando
- Vstupní parametry:
    - **stringFormat** {0} :{1}
- Výstupní nároky:
    - **outputClaim**: Joe Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformace

Zkopíruje lokalizované řetězce do deklarací.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Výstupní nárok | Název lokalizovaného řetězce | řetězec | Seznam typů deklarací, které jsou vytvořeny po této transformace deklarací byla vyvolána. |

Chcete-li použít GetLocalizedStringsTransformation deklarace transformace:

1. Definujte [lokalizační řetězec](localization.md) a přidružte jej k [vlastnímu uplatněný technický profil](self-asserted-technical-profile.md).
1. Prvek musí být nastavena `GetLocalizedStringsTransformationClaimType`na . `ElementType` `LocalizedString`
1. Jedná `StringId` se o jedinečný identifikátor, který definujete a použijete jej později v transformaci deklarací identity.
1. V transformaci deklarací zadejte seznam deklarací, které mají být nastaveny s lokalizovaným řetězcem. Jedná `ClaimTypeReferenceId` se o odkaz na ClaimType již definované v claimsSchema části v zásadě. Je `TransformationClaimType` název lokalizovaného řetězce, jak `StringId` je `LocalizedString` definován v prvku.
1. V [samoobslužné masce technického profilu](self-asserted-technical-profile.md)nebo v transformaci vstupních nebo výstupních deklarací [ovládacího prvku zobrazení](display-controls.md) se odkazuje na transformaci deklarací identity.

![GetLocalizedStringsTransformace](./media/string-transformations/get-localized-strings-transformation.png)

Následující příklad vyhledá předmět e-mailu, text, kódovou zprávu a podpis e-mailu z lokalizovaných řetězců. Tyto deklarace identity později používala vlastní šablona pro ověření e-mailu.

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

Transformace deklarací nastaví hodnotu *subjektu* typu `StringId` deklarace identity s hodnotou *email_subject*.

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

- Výstupní nároky:
  - **předmět**: Ověřovací kód e-mailu účtu Contoso
  - **zpráva**: Díky za ověření vašeho účtu!
  - **codeIntro**: Váš kód je
  - **podpis**: S pozdravem


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Vyhledání položky z kolekce **omezení** deklarací.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | mapFromClaim | řetězec | Deklarace, která obsahuje text, který má být vyhledán v **deklaracích restrictionValueClaim** s kolekcí **omezení.**  |
| Výstupní nárok | restrictionValueClaim | řetězec | Deklarace, která obsahuje kolekci **omezení.** Po uplatnění transformace deklarací obsahuje hodnota této deklarace hodnotu vybrané položky. |

Následující příklad vyhledá popis chybové zprávy na základě chybového klíče. Deklarace **odpovědiMsg** obsahuje kolekci chybových zpráv, které mají být odeslány koncovému uživateli nebo které mají být odeslány předávající straně.

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
Transformace deklarací vyhledá text položky a vrátí její hodnotu. Pokud je omezení lokalizováno pomocí `<LocalizedCollection>`, vrátí transformace deklarací lokalizovanou hodnotu.

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

- Vstupní deklarace:
    - **mapFromClaim**: B2C_V1_90001
- Výstupní nároky:
    - **restrictionValueClaim**: Nelze se přihlásit, protože jste nezletilí.

## <a name="lookupvalue"></a>Vyhledávací hodnota

Vyhledejte hodnotu deklarace ze seznamu hodnot na základě hodnoty jiné deklarace.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputParameterId | řetězec | Deklarace, která obsahuje vyhledávací hodnotu |
| Parametr Input | |řetězec | Kolekce inputParameters. |
| Parametr Input | errorOnFailedLookup | Boolean | Řízení, zda je vrácena chyba, pokud žádné odpovídající vyhledávání. |
| Výstupní nárok | inputParameterId | řetězec | ClaimTypes, které budou vytvořeny po této deklarace transformace byla vyvolána. Hodnota odpovídající `Id`. |

Následující příklad vyhledá název domény v jedné z kolekcí inputParameters. Transformace deklarací aplikace vyhledá název domény v identifikátoru a vrátí jeho hodnotu (ID aplikace).

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

- Vstupní deklarace:
    - **inputParameterId**: test.com
- Vstupní parametry:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com:** c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: false
- Výstupní nároky:
    - **outputClaim**: c7026f88-4299-4cdb-965d-3f166464b8a9

Pokud `errorOnFailedLookup` je parametr `true`vstupu nastaven na , transformace deklarací **LookupValue** je vždy provedena z [ověřovacího technického profilu,](validation-technical-profile.md) který je volán [vlastním technickým profilem](self-asserted-technical-profile.md)nebo [DisplayConrtol](display-controls.md). Metadata `LookupNotFound` samoobslužného technického profilu řídí chybovou zprávu, která je uživateli prezentována.

![AssertStringClaimsAreEqual provádění](./media/string-transformations/assert-execution.png)

Následující příklad vyhledá název domény v jedné z kolekcí inputParameters. Transformace deklarací aplikace vyhledá název domény v identifikátoru a vrátí jeho hodnotu (ID aplikace) nebo vyvolá chybovou zprávu.

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

- Vstupní deklarace:
    - **inputParameterId**: live.com
- Vstupní parametry:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com:** c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: true
- Chyba:
    - Nebyla nalezena žádná shoda pro vstupní hodnotu deklarace v seznamu id vstupních parametrů a chybyOnFailedLookup je true.


## <a name="nullclaim"></a>Deklarace null

Vyčistěte hodnotu dané deklarace.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Výstupní nárok | claim_to_null | řetězec | Hodnota deklarace je nastavena na hodnotu NULL. |

Pomocí této transformace deklarace identity odeberte nepotřebná data z vaku vlastností deklarací identity, takže soubor cookie relace bude menší. Následující příklad odebere hodnotu `TermsOfService` typu deklarace.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Vstupní deklarace:
    - **outputClaim**: Vítejte v aplikaci Contoso. Pokud budete pokračovat v prohlížení a používání těchto webových stránek, souhlasíte s tím, že budete dodržovat a budete vázáni následujícími podmínkami...
- Výstupní nároky:
    - **outputClaim**: NULL

## <a name="parsedomain"></a>ParseDomain

Získá doménovou část e-mailové adresy.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | Emailaddress | řetězec | ClaimType, který obsahuje e-mailovou adresu. |
| Výstupní nárok | doména | řetězec | ClaimType, který je vytvořen po této deklarace transformace byla vyvolána - doména. |

Pomocí této transformace deklarací analyzovat název domény za @ symbol uživatele. Následující transformace deklarací identity ukazuje, jak analyzovat název domény z **e-mailové** deklarace identity.

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

- Vstupní deklarace:
  - **e-mailAdresa**:joe@outlook.com
- Výstupní nároky:
    - **doména**: outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

Zkontroluje, zda `claimToMatch` `matchTo` řetězec deklarace identity a vstupní parametr jsou `outputClaimIfMatched` stejné a nastaví výstupní deklarace s hodnotou přítomnou ve vstupním parametru, spolu s porovnáním deklarace výstupu výsledku, která má být nastavena jako `true` nebo `false` na základě výsledku porovnání.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | řetězec | Typ deklarace, který je třeba porovnat. |
| Parametr Input | matchTo | řetězec | Regulární výraz, který má odpovídat. |
| Parametr Input | outputClaimIfMatched | řetězec | Hodnota, která má být nastavena, pokud jsou řetězce stejné. |
| Parametr Input | extractGroups | Boolean | [Nepovinné] Určuje, zda má shoda Regex extrahovat hodnoty skupin. Možné hodnoty: `true` `false` , nebo (výchozí). | 
| Výstupní nárok | outputClaim | řetězec | Pokud je regulární výraz shodovat, tato výstupní deklarace obsahuje hodnotu vstupního parametru. `outputClaimIfMatched` Nebo null, pokud žádná shoda. |
| Výstupní nárok | regexCompareResultClaim | Boolean | Typ deklarace výsledku výsledku, který má `true` `false` být nastaven jako výsledek shody nebo na základě výsledku vyrovnání, odpovídá. |
| Výstupní nárok| Název pohledávky| řetězec | Pokud extractGroups vstupní parametr nastaven na hodnotu true, seznam typů deklarací identity, které jsou vytvořeny po této transformace deklarace identity byla vyvolána. Název claimType musí odpovídat názvu skupiny Regex. | 

### <a name="example-1"></a>Příklad 1

Na základě vzoru regulárního výrazu telefonního čísla zkontroluje, zda je zadaný telefonní číslo platný.

```XML
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

- Vstupní deklarace:
    - **claimToMatch**: "64854114520"
- Vstupní parametry:
    - **matchTo**: "^[0-9]{4,16}$"
    - **outputClaimIfMatched**: "isPhone"
- Výstupní nároky:
    - **outputClaim**: "isPhone"
    - **regexCompareResultClaim**: true

### <a name="example-2"></a>Příklad 2

Zkontroluje, zda je zadaná e-mailová adresa platná, a vrátí e-mailový alias.

```XML
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

- Vstupní deklarace:
    - **claimToMatch**:emily@contoso.com"
- Vstupní parametry:
    - **matchTo**:`(?&lt;mailAlias&gt;.*)@(.*)$`
    - **outputClaimIfMatched**: "isEmail"
    - **extractGroups**: true
- Výstupní nároky:
    - **outputClaim**: "isEmail"
    - **regexCompareResultClaim**: true
    - **mailAlias**: emily
    
## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Zkontroluje, zda `matchTo` řetězec deklarace identity a vstupní parametr jsou `stringMatchMsg` `stringMatchMsgCode` stejné a nastaví výstupní deklarace s hodnotou `true` přítomné v a vstupní parametry, spolu s porovnat deklarace výstupu výsledku, který má být nastaven jako nebo `false` na základě výsledku porovnání.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim | řetězec | Typ deklarace, který je třeba porovnat. |
| Parametr Input | matchTo | řetězec | Řetězec, který má `inputClaim`být porovnán s . |
| Parametr Input | Stringcomparison | řetězec | Možné `Ordinal` hodnoty: `OrdinalIgnoreCase`nebo . |
| Parametr Input | stringMatchMsg | řetězec | První hodnota, která má být nastavena, pokud jsou řetězce stejné. |
| Parametr Input | stringMatchMsgCode | řetězec | Druhá hodnota, která má být nastavena, pokud jsou řetězce stejné. |
| Výstupní nárok | outputClaim1 | řetězec | Pokud řetězce jsou stejné, tato výstupní deklarace obsahuje hodnotu vstupní parametr. `stringMatchMsg` |
| Výstupní nárok | outputClaim2 | řetězec | Pokud řetězce jsou stejné, tato výstupní deklarace obsahuje hodnotu vstupní parametr. `stringMatchMsgCode` |
| Výstupní nárok | stringCompareResultClaim | Boolean | Typ deklarace výstupu s porovnáním, `true` který `false` má být nastaven jako nebo na základě výsledku porovnání. |

Tuto transformaci deklarací můžete použít ke kontrole, zda je deklarace rovná hodnotě, kterou jste zadali. Například následující transformace deklarací zkontroluje, zda je hodnota **deklarace identity termsOfUseConsentVersion** rovna `v1`. Pokud ano, změňte `v2`hodnotu na .

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

- Vstupní deklarace:
    - **inputClaim**: v1
- Vstupní parametry:
    - **matchTo**: V1
    - **stringComparison**: ordinalIgnoreCase
    - **stringMatchMsg**: B2C_V1_90005
    - **stringMatchMsgCode**: TOS je upgradován na v2
- Výstupní nároky:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: TOS je upgradován na v2
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Zkontroluje, zda `matchTo` řetězec deklarace identity a vstupní parametr jsou `outputClaimIfMatched` stejné a nastaví výstupní deklarace s hodnotou přítomnou ve vstupním parametru, spolu s porovnáním deklarace výstupu výsledku, která má být nastavena jako `true` nebo `false` na základě výsledku porovnání.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | claimToMatch | řetězec | Typ deklarace, který je třeba porovnat. |
| Parametr Input | matchTo | řetězec | Řetězec, který má být porovnán s inputClaim. |
| Parametr Input | Stringcomparison | řetězec | Možné `Ordinal` hodnoty: `OrdinalIgnoreCase`nebo . |
| Parametr Input | outputClaimIfMatched | řetězec | Hodnota, která má být nastavena, pokud jsou řetězce stejné. |
| Výstupní nárok | outputClaim | řetězec | Pokud řetězce jsou stejné, tato výstupní deklarace obsahuje hodnotu vstupní parametr. `outputClaimIfMatched` Nebo null, pokud řetězce nejsou shodovat. |
| Výstupní nárok | stringCompareResultClaim | Boolean | Typ deklarace výstupu s porovnáním, `true` který `false` má být nastaven jako nebo na základě výsledku porovnání. |

Například následující transformace deklarací identity zkontroluje, zda `Minor`je hodnota deklarace **ageGroup** rovna . Pokud ano, vraťte `B2C_V1_90001`hodnotu .

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

- Vstupní deklarace:
    - **claimToMatch**: Menší
- Vstupní parametry:
    - **matchTo**: Vedlejší
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched**: B2C_V1_90001
- Výstupní nároky:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: true


## <a name="stringcontains"></a>Obsahuje řetězec

Určete, zda se v rámci vstupní deklarace vyskytuje zadaný podřetězec. Výsledkem je nový logický claimtype s `true` `false`hodnotou nebo . `true`Pokud dojde k parametru hodnoty v `false`rámci tohoto řetězce, jinak .

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim | řetězec | Typ deklarace, který má být prohledán. |
|Parametr Input|obsahuje|řetězec|Hodnota hledání.|
|Parametr Input|Ignorecase|řetězec|Určuje, zda má toto porovnání ignorovat případ porovnávaného řetězce.|
| Výstupní nárok | outputClaim | řetězec | ClaimType, který je vytvořen po této ClaimsTransformation byla vyvolána. Logický indikátor, pokud dojde k podřetězci v rámci vstupní deklarace. |

Pomocí této transformace deklarací zkontrolujte, zda typ deklarace řetězce obsahuje podřetězec. Následující příklad zkontroluje, zda typ `roles` deklarace řetězce obsahuje hodnotu **admin**.

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

- Vstupní deklarace:
    - **inputClaim**: "Admin, Approver, Editor"
- Vstupní parametry:
    - **obsahuje**: "admin,"
    - **ignoreCase**: true
- Výstupní nároky:
    - **outputClaim**: true outputClaim : true outputClaim : true outputClaim

## <a name="stringsubstring"></a>Řetězec podřetězec

Extrahuje části typu deklarace řetězce, počínaje znakem na zadané pozici a vrátí zadaný počet znaků.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim | řetězec | Typ deklarace, který obsahuje řetězec. |
| Parametr Input | Startindex | int | Pozice počátečního znaku na základě nuly podřetězce v této instanci. |
| Parametr Input | length | int | Počet znaků v podřetězci. |
| Výstupní nárok | outputClaim | Boolean | Řetězec, který je ekvivalentní podřetězec délky, která začíná na startIndex v této instanci nebo Empty if startIndex se rovná délce této instance a délka je nula. |

Získejte například předponu země telefonního čísla.


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

- Vstupní deklarace:
    - **inputClaim**: "+1644114520"
- Vstupní parametry:
    - **startIndex**: 0
    - **délka**: 2
- Výstupní nároky:
    - **outputClaim**: "+1"

## <a name="stringreplace"></a>Nahradit řetězec

Vyhledá řetězec typu deklarace pro zadanou hodnotu a vrátí nový řetězec typu deklarace, ve kterém jsou všechny výskyty zadaného řetězce v aktuálním řetězci nahrazeny jiným zadaným řetězcem.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim | řetězec | Typ deklarace, který obsahuje řetězec. |
| Parametr Input | Oldvalue | řetězec | Řetězec, který má být prohledán. |
| Parametr Input | Newvalue | řetězec | Řetězec, který nahradí všechny výskyty`oldValue` |
| Výstupní nárok | outputClaim | Boolean | Řetězec, který je ekvivalentní aktuální řetězec s tím rozdílem, že všechny instance oldValue jsou nahrazeny newValue. Pokud oldValue není nalezen v aktuální instanci, metoda vrátí aktuální instance beze změny. |

Například normalizovat telefonní číslo odebráním `-` znaků


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

- Vstupní deklarace:
    - **inputClaim**: "+164-411-452-054"
- Vstupní parametry:
    - **oldValue**: "-"
    - **délka**: ""
- Výstupní nároky:
    - **outputClaim**: "+164411452054"

## <a name="stringjoin"></a>Spojení řetězců

Zřetězí prvky zadaného typu deklarace kolekce řetězců pomocí zadaného oddělovače mezi jednotlivými prvky nebo členy.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim | Stringcollection | Kolekce, která obsahuje řetězce zřetězit. |
| Parametr Input | delimiter | řetězec | Řetězec, který chcete použít jako oddělovač, například čárka `,`. |
| Výstupní nárok | outputClaim | řetězec | Řetězec, který se skládá z `inputClaim` členů kolekce řetězce, `delimiter` oddělený vstupníparametr. |

Následující příklad přebírá kolekci řetězců uživatelských rolí a převádí ji na řetězec oddělovače čárek. Tuto metodu můžete použít k uložení kolekce řetězců v uživatelském účtu Azure AD. Později při čtení účtu z adresáře, použijte `StringSplit` převést řetězec oddělovač čárky zpět do kolekce řetězců.

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

- Vstupní deklarace:
  - **inputClaim**: [ "Admin", "Autor", "Reader" ]
- Vstupní parametry:
  - **oddělovač**: ","
- Výstupní nároky:
  - **outputClaim**: "Admin,Autor,Reader"


## <a name="stringsplit"></a>ŘetězecSplit

Vrátí pole řetězce, které obsahuje podřetězce v této instanci, které jsou odděleny prvky zadaného řetězce.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim | řetězec | Typ deklarace řetězce, který obsahuje dílčí řetězce rozdělit. |
| Parametr Input | delimiter | řetězec | Řetězec, který chcete použít jako oddělovač, například čárka `,`. |
| Výstupní nárok | outputClaim | Stringcollection | Kolekce řetězce, jejíž prvky obsahují podřetězce v tomto `delimiter` řetězci, které jsou odděleny vstupním parametrem. |

Následující příklad přebírá řetězec oddělovače čárek uživatelských rolí a převádí jej na kolekci řetězců.

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

- Vstupní deklarace:
  - **inputClaim**: "Admin,Autor,Reader"
- Vstupní parametry:
  - **oddělovač**: ","
- Výstupní nároky:
  - **outputClaim**: [ "Admin", "Autor", "Reader" ]

## <a name="string-claim-transformations-expressions"></a>Výrazy transformací deklarací řetězců
Výrazy transformace deklarací ve vlastních zásadách Azure AD B2C poskytují kontextové informace o ID klienta a ID technického profilu.

  | Expression | Popis | Příklad |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | Technický profilId název. | Facebook-OAUTH |
 | `{RelyingPartyTenantId}` | ID klienta zásady předávající strany. | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | ID klienta rámce důvěryhodnosti. | your-tenant.onmicrosoft.com |
