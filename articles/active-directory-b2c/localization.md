---
title: Lokalizace – Služba Azure Active Directory B2C
description: Zadejte lokalizační prvek vlastní zásady ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e73eae4d66f4ff94a48dfa27e258f8ba8ef87633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126755"
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

| Element | Výskyty | Popis |
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

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Podporovaný jazyk | 1:n | Zobrazí obsah, který odpovídá jazykové značce podle rfc 5646 – značek pro identifikaci jazyků. |

## <a name="localizedresources"></a>Lokalizované zdroje

Prvek **LocalizedResources** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Identifikátor, který se používá k jedinečné identifikaci lokalizovaných prostředků. |

Prvek **LocalizedResources** obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Lokalizované kolekce | 0:n | Definuje celé kolekce v různých kulturách. Kolekce může mít různý počet položek a různé řetězce pro různé jazykové verze. Příklady kolekcí zahrnují výčty, které se zobrazují v typech deklarací. Například seznam zemí nebo oblastí se uživateli zobrazí v rozevíracím seznamu. |
| Lokalizované řetězce | 0:n | Definuje všechny řetězce, s výjimkou řetězce, které se zobrazí v kolekcích, v různých kulturách. |

### <a name="localizedcollections"></a>Lokalizované kolekce

The **LocalizedCollections** Element obsahuje následující prvky:

| Element | Výskyty | Popis |
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

| Element | Výskyty | Popis |
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

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Lokalizovaný řetězec | 1:n | Lokalizovaný řetězec. |

Prvek **LocalizedString** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ElementType | Ano | Odkaz na prvek typu deklarace nebo prvek uživatelského rozhraní v zásadě. `ClaimType`Možné hodnoty: `UxElement` `ErrorMessage`, `Predicate`, `GetLocalizedStringsTransformationClaimType`, , nebo . Hodnota `ClaimType` se používá k lokalizaci jednoho z atributů deklarace, jak je uvedeno v StringId. Hodnota `UxElement` se používá k lokalizaci jednoho z prvků uživatelského rozhraní, jak je uvedeno v StringId. Hodnota `ErrorMessage` se používá k lokalizaci jedné ze systémových chybových zpráv, jak je uvedeno v StringId. Hodnota `Predicate` se používá k lokalizaci jedné z [predikátových](predicates.md) chybových zpráv, jak je uvedeno v StringId. Hodnota `InputValidation` se používá k lokalizaci jedné z chybových zpráv skupiny [PredikateValidation,](predicates.md) jak je zadáno v StringId. Hodnota `GetLocalizedStringsTransformationClaimType` se používá ke kopírování lokalizovaných řetězců do deklarací. Další informace naleznete v tématu [GetLocalizedStringsTransformation deklarace identity transformace](string-transformations.md#getlocalizedstringstransformation)  | 
| ElementId | Ano | Pokud **elementType** je `ClaimType` `Predicate`nastavena `InputValidation`na , nebo , tento prvek obsahuje odkaz na typ deklarace již definována v ClaimsSchema části. |
| Id řetězce | Ano | Pokud **elementType** je `ClaimType`nastavena na , tento prvek obsahuje odkaz na atribut typu deklarace. Možné `DisplayName`hodnoty: `AdminHelpText`, `PatternHelpText`, nebo . Hodnota `DisplayName` se používá k nastavení zobrazovaný název deklarace. Hodnota `AdminHelpText` se používá k nastavení textového názvu nápovědy uživatele deklarace. Hodnota `PatternHelpText` se používá k nastavení textu nápovědy vzoru deklarací. Pokud je hodnota `UxElement` **ElementType** nastavena na , obsahuje tento prvek odkaz na atribut prvku uživatelského rozhraní. Pokud je funkce `ErrorMessage` **ElementType** nastavena na , určuje tento prvek identifikátor chybové zprávy. Úplný seznam identifikátorů naleznete v `UxElement` [tématu ID řetězce lokalizace.](localization-string-ids.md)|


Následující příklad ukazuje lokalizovanou registrační stránku. První tři hodnoty **LocalizedString** nastavují atribut deklarace. Třetí změní hodnotu tlačítka continue. Poslední změní chybovou zprávu.

```XML
<LocalizedResources Id="api.selfasserted.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

Následující příklad ukazuje lokalizovaný **UserHelpText** **predikátu** `IsLengthBetween8And64`s ID . A lokalizovaný **UserHelpText** **PredikateGroup** `CharacterClasses` s ID **PredicateValidation** s ID `StrongPassword`.

```XML
<PredicateValidation Id="StrongPassword">
  <PredicateGroups>
    ...
    <PredicateGroup Id="CharacterClasses">
    ...
    </PredicateGroup>
  </PredicateGroups>
</PredicateValidation>

...

<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  ...
</Predicate>
...


<LocalizedString ElementType="InputValidation" ElementId="StrongPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>

<LocalizedString ElementType="Predicate" ElementId="IsLengthBetween8And64" StringId="HelpText">The password must be between 8 and 64 characters.</LocalizedString>
```

## <a name="set-up-localization"></a>Nastavit lokalizaci

Tento článek ukazuje, jak podporovat více národních prostředí nebo jazyků v zásadách pro cesty uživatelů. Lokalizace vyžaduje tři kroky: nastavení explicitního seznamu podporovaných jazyků, poskytnutí řetězců a kolekcí specifických pro jazyk a úprava definice obsahu stránky.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Nastavení explicitního seznamu podporovaných jazyků

Pod **elementem BuildingBlocks** přidejte prvek **Lokalizace** se seznamem podporovaných jazyků. Následující příklad ukazuje, jak definovat podporu lokalizace pro angličtinu (výchozí) a španělštinu:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="next-steps"></a>Další kroky

Příklady lokalizace naleznete v následujících článcích:

- [Přizpůsobení jazyka pomocí vlastních zásad ve službě Azure Active Directory B2C](custom-policy-localization.md)
- [Přizpůsobení jazyka s toky uživatelů ve službě Azure Active Directory B2C](user-flow-language-customization.md)
