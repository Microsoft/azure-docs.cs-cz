---
title: Řetězec deklaraci příklady transformaci identita prostředí Framework schéma z Azure Active Directory B2C | Dokumentace Microsoftu
description: Řetězec deklaraci příklady transformaci identita prostředí Framework schéma z Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b731b280b3e97076014f609571766a07a3dde1ea
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159886"
---
# <a name="string-claims-transformations"></a>Řetězec deklarace identity transformace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek obsahuje příklady použití transformace deklarací identity řetězců schématu architekturu rozhraní identit v Azure Active Directory (Azure AD) B2C. Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual 

Porovnat dvě deklarace identity a vyvolat výjimku, pokud nejsou stejné podle zadané porovnávací inputClaim1, inputClaim2 a stringComparison.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | řetězec | Typ první deklaraci identity, který je k porovnání. |
| InputClaim | inputClaim2 | řetězec | Druhý typ deklarace identity, který má být porovnána. |
| InputParameter | stringComparison | řetězec | řetězec porovnání, použijte některou z hodnot: řadová, OrdinalIgnoreCase. |

**AssertStringClaimsAreEqual** transformaci deklarací identity je vždy spuštěn z [technický profil ověření](validation-technical-profile.md) , který je volán [držitelem s prohlašovanou technický profil](self-asserted-technical-profile.md). **UserMessageIfClaimsTransformationStringsAreNotEqual** technického profilu s vlastním potvrzením řídí, která se zobrazí uživateli chybovou zprávu.

![Spuštění AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

Můžete použít tato deklarace identity transformace, abyste měli jistotu, dva ClaimTypes mají stejnou hodnotu. V opačném případě je vyvolána chybovou zprávu. Následující příklad kontroluje, zda **strongAuthenticationEmailAddress** typu deklarace identity rovná **e-mailu** typu deklarace identity. V opačném případě je vyvolána chybovou zprávu. 

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


**Neinteraktivní přihlášení** volání technický profil ověření **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** transformace deklarací identity.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Volání ověření s vlastním potvrzením technický profil **neinteraktivní přihlášení** technický profil.

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

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **inputClaim1**: someone@contoso.com
    - **inputClaim2**: someone@outlook.com
 - Vstupní parametry:
    - **stringComparison**: ordinalIgnoreCase
- Výsledek: Vyvolána chyba

## <a name="changecase"></a>ChangeCase 

Změní případ zadané deklarace identity ke snížení nebo velká písmena v závislosti na operátor.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | řetězec | Typu deklarace identity, který se změnil. |
| InputParameter | toCase | řetězec | Jeden z následujících hodnot: `LOWER` nebo `UPPER`. |
| outputClaim | outputClaim | řetězec | Zavolání typu deklarace identity, který je vytvořen po to transformace deklarací identity. |

Pomocí této transformace deklarací identity můžete změnit libovolný řetězec typu deklarace identity ke snížení nebo velká písmena.  

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

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **e-mailu**: SomeOne@contoso.com
- Vstupní parametry:
    - **toCase**: nižší
- Výstupní deklarace identit:
    - **e-mailu**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim 

Vytvoří deklaraci identity řetězec z zadaný vstupní parametr v zásadách.

| Položka | TransformationClaimType | Typ dat | Poznámky |
|----- | ----------------------- | --------- | ----- |
| InputParameter | hodnota | řetězec | Řetězec, který má být nastavena |
| outputClaim | createdClaim | řetězec | Typ ClaimType, který je vytvořen po to transformace deklarací identity se vyvolala s hodnota zadaná jako vstupní parametr. |

Použití, které tato deklarace identity transformace, která nastavit řetězec hodnotu typu deklarace identity.

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

### <a name="example"></a>Příklad:

- Vstupní parametr:
    - **Hodnota**: Contoso podmínky služby...
- Výstupní deklarace identit:
    - **createdClaim**: telekomunikací typu deklarace identity obsahuje hodnotu "Contoso podmínky služby...".

## <a name="compareclaims"></a>CompareClaims

Určení, jestli jedna deklarace řetězec je roven jinému. Výsledkem je nový logický typ ClaimType s hodnotou `true` nebo `false`.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | řetězec | První deklarace typu, který je k porovnání. |
| InputClaim | inputClaim2 | řetězec | Za druhé deklarace typu, který má být porovnána. |
| InputParameter | – Operátor | řetězec | Možné hodnoty: `Equal` nebo `Not Equal`. |
| InputParameter | IgnoreCase | Boolean | Určuje, zda toto porovnání by měl ignorovat velikost písmen řetězců, který se porovnává. |
| outputClaim | outputClaim | Boolean | Zavolání typu deklarace identity, který je vytvořen po to transformace deklarací identity. |

Použijte Tato transformace a zkontrolujte, zda deklarace identity rovná další deklarace identity deklarací identity. Například následující deklarace identity transformace kontroly Pokud hodnota **e-mailu** deklarace identity rovná **Verified.Email** deklarací identity.

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

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **inputClaim1**: someone@contoso.com
    - **inputClaim2**: someone@outlook.com
- Vstupní parametry:
    - **operátor**: není ROVNO
    - **ignoreCase**: true
- Výstupní deklarace identit:
    - **outputClaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Určuje, zda je hodnota deklarace identity rovná hodnotě vstupního parametru.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | řetězec | Typ deklarace identity, který je k porovnání. |
| InputParameter | – Operátor | řetězec | Možné hodnoty: `Equal` nebo `Not Equal`. |
| InputParameter | Metoda compareTo | řetězec | řetězec porovnání, použijte některou z hodnot: řadová, OrdinalIgnoreCase. |
| InputParameter | IgnoreCase | Boolean | Určuje, zda toto porovnání by měl ignorovat velikost písmen řetězců, který se porovnává. |
| outputClaim | outputClaim | Boolean | Zavolání typu deklarace identity, který je vytvořen po to transformace deklarací identity. |

Můžete použít tato deklarace identity transformace, která zjistí, zda je deklarace identity rovná hodnotě, který jste zadali. Například následující deklarace identity transformace kontroly Pokud hodnota **termsOfUseConsentVersion** deklarace identity rovná `v1`.

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

### <a name="example"></a>Příklad:
- Vstupní deklarace identity:
    - **inputClaim1**: v1
- Vstupní parametry:
    - **Metoda compareTo**: V1
    - **operátor**: rovná 
    - **ignoreCase**: true
- Výstupní deklarace identit:
    - **outputClaim**: true

## <a name="createrandomstring"></a>CreateRandomString

Vytvoří náhodný řetězec za použití generátor náhodných čísel. Pokud je generátor náhodných čísel typu `integer`, volitelně může zadat parametr počáteční hodnoty a maximální povolený počet. Parametr nepovinný řetězec formátu umožňuje výstup má být formátováno, jeho použití a jako base64 volitelný parametr určuje, zda výstup outputClaim randomGeneratorType [identifikátor guid, celé číslo] kódovanou jako base64 (řetězec).

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | řetězec | Určuje náhodnou hodnotu chcete vygenerovat, `GUID` (globální jedinečné ID) nebo `integer` (číslo). |
| InputParameter | stringFormat | řetězec | [Volitelné] Formátování náhodnou hodnotu. |
| InputParameter | ve formátu Base64 | Boolean | [Volitelné] Převeďte náhodnou hodnotu ve formátu Base64. Pokud je použit formát řetězce, hodnota po formát řetězce je zakódovaný ve formátu Base64. |
| InputParameter | maximumNumber | int | [Volitelné] Pro `Integer` randomGeneratorType pouze. Zadejte číslo maximute. |
| InputParameter | Počáteční hodnota  | int | [Volitelné] Pro `Integer` randomGeneratorType pouze. Zadejte počáteční hodnotu pro náhodnou hodnotu. Poznámka: stejné počáteční hodnoty vrací stejnou posloupnost náhodných čísel. |
| outputClaim | outputClaim | řetězec | Zavolání ClaimTypes, který bude vytvořen poté, co to transformace deklarací identity. Náhodná hodnota. |

Následující příklad generuje globální jedinečné ID. Tato deklarace identity transformace se používá k vytvoření náhodného UPN (hlavní název uživatele).

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
### <a name="example"></a>Příklad:

- Vstupní parametry:
    - **randomGeneratorType**: GUID
- Výstupní deklarace identit: 
    - **outputClaim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

Následující příklad generuje náhodné celočíselnou hodnotu od 0 do 1000. Hodnota je formátovaný, aby OTP_ {náhodnou hodnotu}.

```XML
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="integer" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad:

- Vstupní parametry:
    - **randomGeneratorType**: celé číslo
    - **maximumNumber**: 1000
    - **stringFormat**: OTP_{0}
    - **ve formátu Base64**: false
- Výstupní deklarace identit: 
    - **outputClaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Formát deklarace identity podle poskytnutý řetězec formátu. Tato transformace používá C# `String.Format` metody.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |řetězec |Typ ClaimType, který funguje jako řetězec formátu {0} parametru. |
| InputParameter | stringFormat | řetězec | Řetězec formátu, včetně {0} parametru. |
| outputClaim | outputClaim | řetězec | Zavolání typu deklarace identity, který je vytvořen po to transformace deklarací identity. |

Použijte tato deklarace identity transformace na formát libovolné řetězce s jedním parametrem {0}. Následující příklad vytvoří **userPrincipalName**. Všechny identity v sociálních sítích zprostředkovatele technické profily, jako například `Facebook-OAUTH` volání **CreateUserPrincipalName** ke generování **userPrincipalName**.   

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

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Vstupní parametry:
    - **stringFormat**: cpim_{0}@{RelyingPartyTenantId}
- Výstupní deklarace identit:
    - **outputClaim**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formát dvě deklarace podle poskytnutý řetězec formátu. Tato transformace používá C# **String.Format** metody.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |řetězec | Typ ClaimType, který funguje jako řetězec formátu {0} parametru. |
| InputClaim | InputClaim | řetězec | Typ ClaimType, který funguje jako řetězec formátu {1} parametru. |
| InputParameter | stringFormat | řetězec | Řetězec formátu, včetně {0} a {1} parametry. |
| outputClaim | outputClaim | řetězec | Zavolání typu deklarace identity, který je vytvořen po to transformace deklarací identity. |

Použijte tato deklarace identity transformace na formát některé řetězce se dvěma parametry {0} a {1}. Následující příklad vytvoří **displayName** pomocí určeného formátu:

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

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **inputClaim1**: Joe
    - **inputClaim2**: ostrov Fernando
- Vstupní parametry:
    - **stringFormat**: {0} {1}
- Výstupní deklarace identit:
    - **outputClaim**: Joe Fernando

## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Vyhledání položky v deklaraci identity **omezení** kolekce.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | řetězec | Deklarace identity, který obsahuje text, který má být vyhledána v **restrictionValueClaim** deklarací s **omezení** kolekce.  |
| outputClaim | restrictionValueClaim | řetězec | Deklarace identity, která obsahuje **omezení** kolekce. Po zavolání transformace deklarací identity, hodnota této deklarace obsahuje hodnotu vybrané položky. |

Následující příklad vyhledá popis chybové zprávy na základě klíče chyby. **ResponseMsg** deklarací identity obsahuje kolekci chybové zprávy pro koncového uživatele nebo k předávající straně odeslání.

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
Transformace deklarací identity, vyhledá text položky a vrátí jeho hodnotu. Pokud je omezení lokalizovány pomocí `<LocalizedCollection>`, transformace deklarací identity vrátí lokalizované hodnoty.

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

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **mapFromClaim**: B2C_V1_90001
- Výstupní deklarace identit:
    - **restrictionValueClaim**: vám nemůže přihlásit, protože jsou za.

## <a name="lookupvalue"></a>LookupValue

Vyhledejte hodnotu deklarace identity v seznamu hodnot na základě hodnoty další deklarace identity.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | řetězec | Deklarace identity, který obsahuje hodnotu vyhledávání |
| InputParameter | |řetězec | Kolekce vstupní parametry. |
| InputParameter | errorOnFailedLookup | Boolean | Řízení, zda při žádné odpovídající vyhledávání, vrátí se chyba. |
| outputClaim | inputParameterId | řetězec | Zavolání ClaimTypes, který bude vytvořen poté, co to transformace deklarací identity. Hodnota odpovídající ID. |

Název domény v jedné z kolekcí inpuParameters vyhledá v následujícím příkladu. Transformace deklarací identity názvu domény v identifikátoru vyhledá a vrátí jeho hodnotu (ID aplikace).

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

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **inputParameterId**: test.com
- Vstupní parametry:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **Microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: false
- Výstupní deklarace identit:
    - **outputClaim**: c7026f88-4299-4cdb-965d-3f166464b8a9

## <a name="nullclaim"></a>NullClaim

Odstraňte hodnotu daného deklarace identity.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| outputClaim | claim_to_null | řetězec | Deklarace identity její hodnotu na hodnotu NULL. |

Pomocí této deklarace identity transformace odebrat nepotřebná data z kontejneru objektů a deklarace identity. Ano soubor cookie relace bude menší. Následující příklad odebere hodnotu `TermsOfService` typ deklarace identity.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Vstupní deklarace identity:
    - **outputClaim**: Vítejte v aplikaci Contoso. Pokud budete pokračovat k procházení a použití tohoto webu, vyjadřujete tím následující podmínky a ujednání a v souladu s...
- Výstupní deklarace identit:
    - **outputClaim**: NULL

## <a name="parsedomain"></a>ParseDomain

Vrátí část domény e-mailovou adresu.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | EmailAddress | řetězec | Typ ClaimType, který obsahuje e-mailovou adresu. |
| outputClaim | Doména | řetězec | Zavolání typu deklarace identity, který je vytvořen po to transformace deklarací identity - doméně. |

Použijte tato deklarace identity transformace parsovat název domény po uživatele, symbol @. To může být užitečné při odebírání identifikovatelné osobní údaje (PII) z data auditu. Následující deklarace identity transformace ukazuje, jak analyzovat název domény z **e-mailu** deklarací identity.

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

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **emailAddress**: joe@outlook.com
- Výstupní deklarace identit:
    - **domény**: outlook.com

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Kontroly, které deklarace identity řetězec a `matchTo` rovnají vstupní parametr a sady deklarací identity s hodnotou ve výstupu `stringMatchMsg` a `stringMatchMsgCode` vstupní parametry, společně s deklarací identity výsledek výstup porovnání, která má být nastavena jako `true` nebo `false` na základě výsledku porovnání.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | řetězec | Typ deklarace identity, který je k porovnání. |
| InputParameter | matchTo | řetězec | Řetězec, který se má porovnat s `inputClaim`. |
| InputParameter | stringComparison | řetězec | Možné hodnoty: `Ordinal` nebo `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | řetězec | První hodnota pro lze nastavit, pokud jsou řetězce stejné. |
| InputParameter | stringMatchMsgCode | řetězec | Druhá hodnota pro lze nastavit, pokud jsou řetězce stejné. |
| outputClaim | outputClaim1 | řetězec | Pokud jsou řetězce equals, tato deklarace identity výstup obsahuje hodnotu `stringMatchMsg` vstupního parametru. |
| outputClaim | outputClaim2 | řetězec | Pokud jsou řetězce equals, tato deklarace identity výstup obsahuje hodnotu `stringMatchMsgCode` vstupního parametru. |
| outputClaim | stringCompareResultClaim | Boolean | Výstup výsledku porovnání deklarace typu, který má být nastavena jako `true` nebo `false` na základě výsledku porovnání. |

Můžete použít tato deklarace identity transformace, která zjistí, zda je deklarace identity rovná hodnotě jste zadali. Například následující deklarace identity transformace kontroly Pokud hodnota **termsOfUseConsentVersion** deklarace identity rovná `v1`. Pokud ano, změňte hodnotu na `v2`. 

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
### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **inputClaim**: v1
- Vstupní parametry:
    - **matchTo**: V1
    - **stringComparison**: ordinalIgnoreCase 
    - **stringMatchMsg**: B2C_V1_90005
    - **stringMatchMsgCode**: telekomunikací se upgraduje na v2
- Výstupní deklarace identit:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: telekomunikací se upgraduje na v2
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Kontroly, které deklarace identity řetězec a `matchTo` rovnají vstupní parametr a sady deklarací identity s hodnotou ve výstupu `outputClaimIfMatched` vstupní parametr, společně s deklarací identity výsledek výstup porovnání, která má být nastavena jako `true` nebo `false` na základě výsledek porovnání.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | řetězec | Typ deklarace identity, který je k porovnání. |
| InputParameter | matchTo | řetězec | Řetězec, který se má porovnat s inputClaim. |
| InputParameter | stringComparison | řetězec | Možné hodnoty: `Ordinal` nebo `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | řetězec | Hodnotu lze nastavit, pokud jsou řetězce stejné. |
| outputClaim | outputClaim | řetězec | Pokud jsou řetězce equals, tato deklarace identity výstup obsahuje hodnotu `outputClaimIfMatched` vstupního parametru. Nebo hodnota null, pokud se shoda řetězců. |
| outputClaim | stringCompareResultClaim | Boolean | Výstup výsledku porovnání deklarace typu, který má být nastavena jako `true` nebo `false` na základě výsledku porovnání. |

Například následující deklarace identity transformace kontroly Pokud hodnota **ageGroup** deklarace identity rovná `Minor`. Pokud ano, vrátí hodnotu, která `B2C_V1_90001`. 

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

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **claimToMatch**: podverze
- Vstupní parametry:
    - **matchTo**: podverze
    - **stringComparison**: ordinalIgnoreCase 
    - **outputClaimIfMatched**: B2C_V1_90001
- Výstupní deklarace identit:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: true

