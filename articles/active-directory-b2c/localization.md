---
title: Lokalizace – Azure Active Directory B2C
description: Zadejte element lokalizace vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 3a5afcd8c0ef0c31353cd2369ead332675c9877f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453117"
---
# <a name="localization-element"></a>Element Localization

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **Localization** umožňuje v zásadách pro cesty uživatelů podporovat více národních prostředí nebo jazyků. Podpora lokalizace v zásadách vám umožní:

- V zásadách nastavte explicitní seznam podporovaných jazyků a vyberte výchozí jazyk.
- Zadejte řetězce a kolekce specifické pro jazyk.

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

Element **Localization** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Povoleno | No | Možné hodnoty: `true` nebo `false` . |

Element **Localization** obsahuje následující elementy XML

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1: n | Seznam podporovaných jazyků |
| LocalizedResources | 0: n | Seznam lokalizovaných prostředků |

## <a name="supportedlanguages"></a>SupportedLanguages

Element **SupportedLanguages** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| DefaultLanguage | Yes | Jazyk, který má být použit jako výchozí pro lokalizované prostředky. |
| MergeBehavior | No | Hodnoty výčtu hodnot, které jsou sloučeny spolu s jakýmkoli objektem ClaimType přítomným v nadřazené zásadě se stejným identifikátorem. Tento atribut použijte, pokud přepíšete deklaraci identity zadanou v základních zásadách. Možné hodnoty: `Append` , `Prepend` , nebo `ReplaceAll` . `Append`Hodnota určuje, že se má na konec kolekce zadané v nadřazené zásadě připojit kolekce dat. `Prepend`Hodnota určuje, že kolekce přítomných dat by měla být přidána před kolekci určenou v nadřazené zásadě. `ReplaceAll`Hodnota určuje, že kolekce dat definovaná v nadřazené zásadě by měla být použita jako místo dat definovaných v aktuálních zásadách. |

### <a name="supportedlanguages"></a>SupportedLanguages

Element **SupportedLanguages** obsahuje následující prvky:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1: n | Zobrazí obsah, který odpovídá značce jazyka na značku RFC 5646-Tags pro identifikaci jazyků. |

## <a name="localizedresources"></a>LocalizedResources

Element **LocalizedResources** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Id | Yes | Identifikátor, který slouží k jednoznačné identifikaci lokalizovaných prostředků. |

Element **LocalizedResources** obsahuje následující prvky:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Definuje celé kolekce v různých jazykových verzích. Kolekce může mít různý počet položek a různé řetězce pro různé jazykové verze. Příklady kolekcí zahrnují výčty, které se zobrazí v typech deklarací. Například seznam zemí nebo oblastí se uživateli zobrazí v rozevíracím seznamu. |
| LocalizedStrings | 0: n | Definuje všechny řetězce s výjimkou těch řetězců, které se zobrazují v kolekcích v různých jazykových verzích. |

### <a name="localizedcollections"></a>LocalizedCollections

Element **LocalizedCollections** obsahuje následující prvky:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| Lokalizovaná | 1: n | Seznam podporovaných jazyků |

#### <a name="localizedcollection"></a>Lokalizovaná

**Lokalizovaný** ElementCollection obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Deklarován | Yes | Odkazuje na element ClaimType nebo na prvek uživatelského rozhraní v souboru zásad. |
| ElementId | Yes | Řetězec, který obsahuje odkaz na typ deklarace, který je již definován v části ClaimsSchema, která je použita, pokud je typ **ElementType** nastaven na typ ClaimType. |
| Cílovácollection | Yes | Cílová kolekce. |

**Lokalizovaný** ElementCollection obsahuje následující prvky:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| Položka | 0: n | Definuje dostupnou možnost, kterou může uživatel vybrat pro deklaraci identity v uživatelském rozhraní, jako je například hodnota v rozevíracím seznamu. |

Element **Item** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Text | Yes | Uživatelsky přívětivý řetězec zobrazení, který by měl být zobrazen uživateli v uživatelském rozhraní pro tuto možnost. |
| Hodnota | Yes | Hodnota deklarace řetězce přidružená k výběru této možnosti |
| SelectByDefault | No | Určuje, zda má být tato možnost ve výchozím nastavení vybrána v uživatelském rozhraní. Možné hodnoty: true nebo false. |

Následující příklad ukazuje použití elementu **LocalizedCollections** . Obsahuje dva **lokalizované** prvkycollection, jeden pro angličtinu a druhý pro španělštinu. Nastavte kolekci **omezení** deklarace identity `Gender` se seznamem položek pro angličtinu a španělštinu.

```xml
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>LocalizedStrings

Element **LocalizedStrings** obsahuje následující prvky:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| LocalizedString | 1: n | Lokalizovaný řetězec. |

Element **LocalizedString** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Deklarován | Yes | Možné hodnoty: [ClaimsProvider](#claimsprovider), [ClaimType](#claimtype), [ErrorMessage](#errormessage), [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype), [FormatLocalizedStringTransformationClaimType](#formatlocalizedstringtransformationclaimtype), [predikát](#predicate), [InputValidation](#inputvalidation)nebo [UxElement](#uxelement).   | 
| ElementId | Yes | Pokud je typ **ElementType** nastaven na `ClaimType` , `Predicate` nebo `InputValidation` , obsahuje tento element odkaz na typ deklarace identity, který je již definován v části ClaimsSchema. |
| StringId | Yes | Pokud je vlastnost **ElementType** nastavena na `ClaimType` , tento prvek obsahuje odkaz na atribut typu deklarace. Možné hodnoty: `DisplayName` , `AdminHelpText` , nebo `PatternHelpText` . `DisplayName`Hodnota se používá k nastavení zobrazovaného názvu deklarace identity. `AdminHelpText`Hodnota slouží k nastavení názvu textu v nápovědě pro uživatele s deklarací identity. `PatternHelpText`Hodnota se používá k nastavení textu v nápovědě ke vzorci deklarace identity. Pokud je vlastnost **ElementType** nastavena na `UxElement` , tento prvek obsahuje odkaz na atribut prvku uživatelského rozhraní. Pokud je typ **ElementType** nastaven na `ErrorMessage` , tento prvek určuje identifikátor chybové zprávy. Úplný seznam identifikátorů naleznete v tématu [lokalizace ID řetězců](localization-string-ids.md) `UxElement` .|

## <a name="elementtype"></a>Deklarován

Odkaz ElementType na typ deklarace identity, transformaci deklarace nebo prvek uživatelského rozhraní v zásadě k lokalizaci.

| Prvek k lokalizaci | Deklarován | ElementId |StringId |
| --------- | -------- | ----------- |----------- |
| Název zprostředkovatele identity |`ClaimsProvider`| | ID elementu ClaimsExchange|
| Atributy typu deklarace identity|`ClaimType`|Název typu deklarace identity| Atribut deklarace identity, která se má lokalizovat Možné hodnoty: `AdminHelpText` , `DisplayName` , a `PatternHelpText` `UserHelpText` .|
|Chybová zpráva|`ErrorMessage`||ID chybové zprávy |
|Zkopíruje lokalizované řetězce do deklarací identity.|`GetLocalizedStringsTra nsformationClaimType`||Název výstupní deklarace identity|
|Zpráva uživatele predikátu|`Predicate`|Název predikátu| Atribut predikátu, který má být lokalizován. Možné hodnoty: `HelpText` .|
|Zpráva uživatele skupiny predikátů|`InputValidation`|ID elementu PredicateValidation|ID elementu predikátu. Skupina predikátu musí být podřízeným elementu ověřování predikátu, jak je definováno v ElementId.|
|Prvky uživatelského rozhraní |`UxElement` | | ID prvku uživatelského rozhraní, který se má lokalizovat|
|[Ovládací prvek zobrazení](display-controls.md) |`DisplayControl` |ID ovládacího prvku zobrazení | ID prvku uživatelského rozhraní, který se má lokalizovat|

## <a name="examples"></a>Příklady

### <a name="claimsprovider"></a>ClaimsProvider

Hodnota ClaimsProvider slouží k lokalizaci jednoho z zobrazovaných názvů poskytovatelů deklarací. 

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
  </ClaimsExchanges>
</OrchestrationStep>

```

Následující příklad ukazuje, jak lokalizovat zobrazované jméno poskytovatelů deklarací identity.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>ClaimType

Hodnota ClaimType se používá k lokalizaci jednoho z atributů deklarace identity. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

Následující příklad ukazuje, jak lokalizovat atributy DisplayName, UserHelpText a PatternHelpText typu deklarace e-mailu.

```xml
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>Chybová

Hodnota ErrorMessage slouží k lokalizaci jedné z chybových zpráv systému. 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

Následující příklad ukazuje, jak lokalizovat chybovou zprávu UserMessageIfClaimsPrincipalAlreadyExists.


```xml
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="formatlocalizedstringtransformationclaimtype"></a>FormatLocalizedStringTransformationClaimType

Hodnota FormatLocalizedStringTransformationClaimType slouží k formátování deklarací do lokalizovaného řetězce. Další informace najdete v tématu [transformace deklarací identity FormatLocalizedString](string-transformations.md#formatlocalizedstring) .


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

Následující příklad ukazuje, jak lokalizovat formát řetězce transformace FormatLocalizedStringTransformationClaimType deklarací identity.

```xml
<LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">The email '{0}' is already an account in this organization. Click Next to sign in with that account.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

Hodnota GetLocalizedStringsTransformationClaimType se používá ke kopírování lokalizovaných řetězců do deklarací identity. Další informace najdete v tématu [transformace deklarací identity GetLocalizedStringsTransformation](string-transformations.md#getlocalizedstringstransformation) .


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

Následující příklad ukazuje, jak lokalizovat výstupní deklarace transformace deklarací identity GetLocalizedStringsTransformation.

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predikát

Hodnota predikátu slouží k lokalizaci jedné z chybových zpráv [predikátu](predicates.md) . 

```xml
<Predicates>
  <Predicate Id="LengthRange" Method="IsLengthRange"  HelpText="The password must be between 6 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">6</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Následující příklad ukazuje, jak lokalizovat predikáty text v nápovědě.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>InputValidation

Hodnota InputValidation se používá k lokalizaci jedné z chybových zpráv skupiny [PredicateValidation](predicates.md) . 

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences MatchAtLeast="1">
          <PredicateReference Id="LengthRange" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Následující příklad ukazuje, jak lokalizovat text v nápovědě skupiny ověření predikátu.

```xml
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement

Hodnota UxElement slouží k lokalizaci jednoho z prvků uživatelského rozhraní. Následující příklad ukazuje, jak lokalizovat tlačítka pokračovat a zrušit.

```xml
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

### <a name="displaycontrol"></a>Prvek

Hodnota ovládacího prvku DataGridView slouží k lokalizaci jednoho z prvků uživatelského rozhraní [ovládacího prvku zobrazení](display-controls.md) . Pokud je tato možnost povolená, localizedStrings ovládací prvek zobrazení přebírá ***přednost** před některými StringIDsy _ *UxElement**, jako je **ver_but_send**, **ver_but_edit**, **ver_but_resend** a **ver_but_verify**. Následující příklad ukazuje, jak lokalizovat tlačítka Odeslat a ověřit. 

```xml
<LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
<LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
```

V části metadata technického profilu s vlastním příznakem musí mít odkazované ContentDefinition DataUri nastavenou na [rozložení stránky verze](page-layout.md) 2.1.0 nebo vyšší. Například:

```xml
<ContentDefinition Id="api.selfasserted">
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.1.0</DataUri>
  ...
```

## <a name="next-steps"></a>Další kroky

Příklady lokalizace najdete v následujících článcích:

- [Přizpůsobení jazyka s vlastními zásadami v Azure Active Directory B2C](language-customization.md)
- [Přizpůsobení jazyka pomocí toků uživatelů v Azure Active Directory B2C](language-customization.md)
