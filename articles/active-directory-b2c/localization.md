---
title: Lokalizace – Azure Active Directory B2C
description: Zadejte element lokalizace vlastní zásady v Azure Active Directory B2C.
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
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126755"
---
# <a name="localization"></a>Lokalizace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **Localization** umožňuje v zásadách pro cesty uživatelů podporovat více národních prostředí nebo jazyků. Podpora lokalizace v zásadách vám umožní:

- V zásadách nastavte explicitní seznam podporovaných jazyků a vyberte výchozí jazyk.
- Zadejte řetězce a kolekce specifické pro jazyk.

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

Element **Localization** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Povoleno | Ne | Možné hodnoty: `true` nebo `false`. |

Element **Localization** obsahuje následující elementy XML

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1: n | Seznam podporovaných jazyků |
| LocalizedResources | 0: n | Seznam lokalizovaných prostředků |

## <a name="supportedlanguages"></a>SupportedLanguages

Element **SupportedLanguages** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| DefaultLanguage | Ano | Jazyk, který má být použit jako výchozí pro lokalizované prostředky. |
| MergeBehavior | Ne | Hodnoty výčtu hodnot, které jsou sloučeny spolu s jakýmkoli objektem ClaimType přítomným v nadřazené zásadě se stejným identifikátorem. Tento atribut použijte, pokud přepíšete deklaraci identity zadanou v základních zásadách. Možné hodnoty: `Append`, `Prepend`nebo `ReplaceAll`. Hodnota `Append` určuje, že se má na konci kolekce zadané v nadřazené zásadě připojit kolekce dat. Hodnota `Prepend` určuje, že kolekce dat, která mají být přítomna, by měla být přidána před kolekci zadané v nadřazené zásadě. Hodnota `ReplaceAll` určuje, že kolekce dat definovaná v nadřazené zásadě by měla být použita jako místo dat definovaných v aktuálních zásadách. |

### <a name="supportedlanguages"></a>SupportedLanguages

Element **SupportedLanguages** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1: n | Zobrazí obsah, který odpovídá značce jazyka na značku RFC 5646-Tags pro identifikaci jazyků. |

## <a name="localizedresources"></a>LocalizedResources

Element **LocalizedResources** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Identifikátor, který slouží k jednoznačné identifikaci lokalizovaných prostředků. |

Element **LocalizedResources** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Definuje celé kolekce v různých jazykových verzích. Kolekce může mít různý počet položek a různé řetězce pro různé jazykové verze. Příklady kolekcí zahrnují výčty, které se zobrazí v typech deklarací. Například seznam zemí nebo oblastí se uživateli zobrazí v rozevíracím seznamu. |
| LocalizedStrings | 0: n | Definuje všechny řetězce s výjimkou těch řetězců, které se zobrazují v kolekcích v různých jazykových verzích. |

### <a name="localizedcollections"></a>LocalizedCollections

Element **LocalizedCollections** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Lokalizovaná | 1: n | Seznam podporovaných jazyků |

#### <a name="localizedcollection"></a>Lokalizovaná

**Lokalizovaný** ElementCollection obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ElementType | Ano | Odkazuje na element ClaimType nebo na prvek uživatelského rozhraní v souboru zásad. |
| ElementId | Ano | Řetězec, který obsahuje odkaz na typ deklarace, který je již definován v části ClaimsSchema, která je použita, pokud je typ **ElementType** nastaven na typ ClaimType. |
| Cílovácollection | Ano | Cílová kolekce. |

**Lokalizovaný** ElementCollection obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Položka | 0: n | Definuje dostupnou možnost, kterou může uživatel vybrat pro deklaraci identity v uživatelském rozhraní, jako je například hodnota v rozevíracím seznamu. |

Element **Item** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Text | Ano | Uživatelsky přívětivý řetězec zobrazení, který by měl být zobrazen uživateli v uživatelském rozhraní pro tuto možnost. |
| Hodnota | Ano | Hodnota deklarace řetězce přidružená k výběru této možnosti |
| SelectByDefault | Ne | Určuje, zda má být tato možnost ve výchozím nastavení vybrána v uživatelském rozhraní. Možné hodnoty: true nebo false. |

Následující příklad ukazuje použití elementu **LocalizedCollections** . Obsahuje dva **lokalizované** prvkycollection, jeden pro angličtinu a druhý pro španělštinu. Nastavte kolekci **omezení** `Gender` deklarací pomocí seznamu položek pro angličtinu a španělštinu.

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

### <a name="localizedstrings"></a>LocalizedStrings

Element **LocalizedStrings** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| LocalizedString | 1: n | Lokalizovaný řetězec. |

Element **LocalizedString** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ElementType | Ano | Odkaz na element typu deklarace identity nebo prvek uživatelského rozhraní v zásadách. Možné hodnoty: `ClaimType`, `UxElement`, `ErrorMessage`, `Predicate`nebo `GetLocalizedStringsTransformationClaimType`. Hodnota `ClaimType` slouží k lokalizaci jednoho z atributů deklarace identity, jak je uvedeno v StringId. Hodnota `UxElement` slouží k lokalizaci jednoho z prvků uživatelského rozhraní, jak je uvedeno v StringId. Hodnota `ErrorMessage` slouží k lokalizaci jedné z chybových zpráv systému, jak je uvedeno v StringId. Hodnota `Predicate` slouží k lokalizaci jedné z chybových zpráv [predikátu](predicates.md) , jak je uvedeno v StringId. Hodnota `InputValidation` slouží k lokalizaci jedné z chybových zpráv skupiny [PredicateValidation](predicates.md) , jak je uvedeno v StringId. Hodnota `GetLocalizedStringsTransformationClaimType` se používá ke kopírování lokalizovaných řetězců do deklarací identity. Další informace najdete v tématu [transformace deklarací identity GetLocalizedStringsTransformation](string-transformations.md#getlocalizedstringstransformation) .  | 
| ElementId | Ano | Pokud je typ **ElementType** nastaven na `ClaimType`, `Predicate`nebo `InputValidation`, tento prvek obsahuje odkaz na typ deklarace, který je již definován v oddílu ClaimsSchema. |
| StringId | Ano | Pokud je vlastnost **ElementType** nastavena na `ClaimType`, tento prvek obsahuje odkaz na atribut typu deklarace. Možné hodnoty: `DisplayName`, `AdminHelpText`nebo `PatternHelpText`. Hodnota `DisplayName` slouží k nastavení zobrazovaného názvu deklarace identity. Hodnota `AdminHelpText` slouží k nastavení názvu textu v nápovědě pro uživatele s deklarací identity. Hodnota `PatternHelpText` slouží k nastavení textu v nápovědě ke vzorci deklarace identity. Pokud je vlastnost **ElementType** nastavena na `UxElement`, tento prvek obsahuje odkaz na atribut prvku uživatelského rozhraní. Pokud je typ **ElementType** nastaven na `ErrorMessage`, tento prvek určuje identifikátor chybové zprávy. Úplný seznam identifikátorů `UxElement` naleznete v tématu [lokalizace ID řetězců](localization-string-ids.md) .|


Následující příklad ukazuje lokalizovanou registrační stránku. První tři hodnoty **LocalizedString** nastavily atribut deklarace identity. Třetí změní hodnotu tlačítka pro pokračování. Poslední z nich se změní chybová zpráva.

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

Následující příklad ukazuje lokalizovaný **UserHelpText** **predikátu** s ID `IsLengthBetween8And64`. A lokalizované **UserHelpTexty** ve službě **predikátů** s ID `CharacterClasses` **PredicateValidation** s ID `StrongPassword`.

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

## <a name="set-up-localization"></a>Nastavení lokalizace

V tomto článku se dozvíte, jak v zásadách pro cesty uživatelů podporovat několik národních prostředí nebo jazyků. Lokalizace vyžaduje tři kroky: nastavte explicitní seznam podporovaných jazyků, zadejte řetězce a kolekce specifické pro jazyk a upravte ContentDefinition pro stránku.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Nastavení explicitního seznamu podporovaných jazyků

V rámci elementu **BuildingBlocks** přidejte element **Localization** se seznamem podporovaných jazyků. Následující příklad ukazuje, jak definovat podporu lokalizace pro angličtinu (výchozí) a španělštinu:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="next-steps"></a>Další kroky

Příklady lokalizace najdete v následujících článcích:

- [Přizpůsobení jazyka s vlastními zásadami v Azure Active Directory B2C](custom-policy-localization.md)
- [Přizpůsobení jazyka pomocí toků uživatelů v Azure Active Directory B2C](user-flow-language-customization.md)
