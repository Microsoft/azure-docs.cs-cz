---
title: Lokalizace – Služba Azure Active Directory B2C
description: Zadejte lokalizační prvek vlastní zásady ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 94ff7ddda41f2df2634d927a7dbf8a5a0d4fc1d8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681418"
---
# <a name="localization"></a>Lokalizace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Lokalizační** prvek umožňuje podporovat více národních prostředí nebo jazyků v zásadách pro cesty uživatele. Podpora lokalizace v zásadách umožňuje:

- Nastavte explicitní seznam podporovaných jazyků v zásadách a vyberte výchozí jazyk.
- Poskytněte řetězce a kolekce specifické pro jazyk.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

**Lokalizační** prvek obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Povoleno | Ne | Možné `true` hodnoty: `false`nebo . |

**Lokalizační** element obsahuje následující elementy XML.

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Podporované jazyky | 1:n | Seznam podporovaných jazyků. |
| Lokalizované zdroje | 0:n | Seznam lokalizovaných prostředků. |

## <a name="supportedlanguages"></a>Podporované jazyky

Prvek **SupportedLanguages** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| DefaultLanguage | Ano | Jazyk, který má být použit jako výchozí pro lokalizované prostředky. |
| Sloučit chování | Ne | Výčtové hodnoty hodnot, které jsou sloučeny s libovolným ClaimType v nadřazené zásady se stejným identifikátorem. Tento atribut použijte při přepsání deklarace zadanou v zásadách základní. Možné `Append`hodnoty: `Prepend`, `ReplaceAll`, nebo . Hodnota `Append` určuje, že kolekce dat přítomných by měla být připojena na konec kolekce zadané v nadřazené zásady. Hodnota `Prepend` určuje, že kolekce dat přítomných by měla být přidána před kolekce zadané v nadřazené zásady. Hodnota `ReplaceAll` určuje, že shromažďování dat definovaných v nadřazené zásady by měly být ignorovány, místo toho data definovaná v aktuální zásady. |

### <a name="supportedlanguages"></a>Podporované jazyky

Prvek **SupportedLanguages** obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Podporovaný jazyk | 1:n | Zobrazí obsah, který odpovídá jazykové značce podle rfc 5646 – značek pro identifikaci jazyků. |

## <a name="localizedresources"></a>Lokalizované zdroje

Prvek **LocalizedResources** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Identifikátor, který se používá k jedinečné identifikaci lokalizovaných prostředků. |

Prvek **LocalizedResources** obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Lokalizované kolekce | 0:n | Definuje celé kolekce v různých kulturách. Kolekce může mít různý počet položek a různé řetězce pro různé jazykové verze. Příklady kolekcí zahrnují výčty, které se zobrazují v typech deklarací. Například seznam zemí nebo oblastí se uživateli zobrazí v rozevíracím seznamu. |
| Lokalizované řetězce | 0:n | Definuje všechny řetězce, s výjimkou řetězce, které se zobrazí v kolekcích, v různých kulturách. |

### <a name="localizedcollections"></a>Lokalizované kolekce

The **LocalizedCollections** Element obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Lokalizovaná kolekce | 1:n | Seznam podporovaných jazyků. |

#### <a name="localizedcollection"></a>Lokalizovaná kolekce

Prvek **LocalizedCollection** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ElementType | Ano | Odkazuje na element ClaimType nebo prvek uživatelského rozhraní v souboru zásad. |
| ElementId | Ano | Řetězec, který obsahuje odkaz na typ deklarace identity již definovánv části ClaimsSchema, která se používá, pokud **elementtype** je nastavena na ClaimType. |
| Kolekce targetů | Ano | Cílová kolekce. |

Prvek **LocalizedCollection** obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Položka | 0:n | Definuje dostupnou možnost pro uživatele vybrat pro deklaraci v uživatelském rozhraní, jako je například hodnota v rozevírací masce. |

Element **Item** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Text | Ano | Uživatelsky přívětivý řetězec zobrazení, který by měl být zobrazen uživateli v uživatelském rozhraní pro tuto možnost. |
| Hodnota | Ano | Hodnota deklarace řetězce přidružená k výběru této možnosti. |
| SelectByDefault | Ne | Označuje, zda má být tato možnost vybrána ve výchozím nastavení v ui. Možné hodnoty: True nebo False. |

Následující příklad ukazuje použití **LocalizedCollections** element. Obsahuje dva **prvky LocalizedCollection,** jeden pro angličtinu a druhý pro španělštinu. Obě nastavit **omezení** kolekce `Gender` deklarace se seznamem položek pro angličtinu a španělštinu.

```XML
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

### <a name="localizedstrings"></a>Lokalizované řetězce

Prvek **LocalizedStrings** obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Lokalizovaný řetězec | 1:n | Lokalizovaný řetězec. |

Prvek **LocalizedString** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ElementType | Ano | Možné hodnoty: [ClaimsProvider](#claimsprovider), [ClaimType](#claimtype), [ErrorMessage](#errormessage), [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype), [Predicate](#predicate), [InputValidation](#inputvalidation)nebo [UxElement](#uxelement).   | 
| ElementId | Ano | Pokud **elementType** je `ClaimType` `Predicate`nastavena `InputValidation`na , nebo , tento prvek obsahuje odkaz na typ deklarace již definována v ClaimsSchema části. |
| Id řetězce | Ano | Pokud **elementType** je `ClaimType`nastavena na , tento prvek obsahuje odkaz na atribut typu deklarace. Možné `DisplayName`hodnoty: `AdminHelpText`, `PatternHelpText`, nebo . Hodnota `DisplayName` se používá k nastavení zobrazovaný název deklarace. Hodnota `AdminHelpText` se používá k nastavení textového názvu nápovědy uživatele deklarace. Hodnota `PatternHelpText` se používá k nastavení textu nápovědy vzoru deklarací. Pokud je hodnota `UxElement` **ElementType** nastavena na , obsahuje tento prvek odkaz na atribut prvku uživatelského rozhraní. Pokud je funkce `ErrorMessage` **ElementType** nastavena na , určuje tento prvek identifikátor chybové zprávy. Úplný seznam identifikátorů naleznete v `UxElement` [tématu ID řetězce lokalizace.](localization-string-ids.md)|

## <a name="elementtype"></a>ElementType

ElementType odkaz na typ deklarace, transformace deklarace nebo prvek uživatelského rozhraní v zásadách, které mají být lokalizovány.

| Prvek lokalizovat | ElementType | ElementId |Id řetězce |
| --------- | -------- | ----------- |----------- |
| Název zprostředkovatele identity |`ClaimsProvider`| | ID prvku ClaimsExchange|
| Atributy typu deklarace|`ClaimType`|Název typu deklarace| Atribut deklarace, která má být lokalizována. Možné `AdminHelpText`hodnoty: `DisplayName` `PatternHelpText`, `UserHelpText`, a .|
|Chybová zpráva|`ErrorMessage`||ID chybové zprávy |
|Zkopíruje lokalizované řetězce do deklarací identity.|`GetLocalizedStringsTra nsformationClaimType`||Název výstupní deklarace|
|Zpráva uživatele predikátu|`Predicate`|Název predikátu| Atribut predikátu, který má být lokalizován. Možné hodnoty: `HelpText`.|
|Zpráva uživatele predikátu skupiny|`InputValidation`|ID predikatevalidation elementu.|ID predikátgroup elementu. Skupina predikátu musí být podřízenou podřízenou prvkem ověření predikátu, jak je definován v ElementId.|
|Prvky uživatelského rozhraní |`UxElement` | | ID prvku uživatelského rozhraní, který má být lokalizován.|

## <a name="examples"></a>Příklady

### <a name="claimsprovider"></a>Zprostředkovatel pohledávek

Hodnota ClaimsProvider se používá k lokalizaci jednoho z zobrazovaných názvů zprostředkovatelů deklarací. 

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

Následující příklad ukazuje, jak lokalizovat zobrazovaný název zprostředkovatelů deklarací.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>Typ deklarace pohledávky

Hodnota ClaimType se používá k lokalizaci jednoho z atributů deklarace. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

Následující příklad ukazuje, jak lokalizovat atributy DisplayName, UserHelpText a PatternHelpText typu deklarace e-mailu.

```XML
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>Errormessage

Hodnota ErrorMessage se používá k lokalizaci jedné ze systémových chybových zpráv. 

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


```XML
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

Hodnota GetLocalizedStringsTransformationClaimType se používá ke kopírování lokalizovaných řetězců do deklarací. Další informace naleznete v tématu [GetLocalizedStringsTransformation deklarace identity transformace](string-transformations.md#getlocalizedstringstransformation)


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

Následující příklad ukazuje, jak lokalizovat výstupní deklarace getlocalizedStringsTransformation deklarace transformace.

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predikátu

Hodnota Predikátu se používá k lokalizaci jedné z chybových zpráv [predikátu.](predicates.md) 

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

Následující příklad ukazuje, jak lokalizovat predikáty textu nápovědy.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>Ověření vstupu

Hodnota InputValidation se používá k lokalizaci jedné z chybových zpráv skupiny [PredikateValidation.](predicates.md) 

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

Následující příklad ukazuje, jak lokalizovat text nápovědy skupiny ověření predikátu.

```XML
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>Prvek Ux

Hodnota UxElement se používá k lokalizaci jednoho z prvků uživatelského rozhraní. Následující příklad ukazuje, jak lokalizovat tlačítka continue a cancel.

```XML
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

## <a name="next-steps"></a>Další kroky

Příklady lokalizace naleznete v následujících článcích:

- [Přizpůsobení jazyka pomocí vlastních zásad ve službě Azure Active Directory B2C](custom-policy-localization.md)
- [Přizpůsobení jazyka s toky uživatelů ve službě Azure Active Directory B2C](user-flow-language-customization.md)
