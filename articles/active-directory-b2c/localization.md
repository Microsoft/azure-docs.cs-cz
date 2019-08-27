---
title: Lokalizace – Azure Active Directory B2C
description: Zadejte element lokalizace vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/27/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ec9b4e7ce761d524d047f4d12cab9e5b782e6032
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70033463"
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

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Enabled | Ne | Možné hodnoty: `true` nebo `false`. |

Element **Localization** obsahuje následující elementy XML

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1: n | Seznam podporovaných jazyků |
| LocalizedResources | 0: n | Seznam lokalizovaných prostředků |

## <a name="supportedlanguages"></a>SupportedLanguages

Element **SupportedLanguages** obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| DefaultLanguage | Ano | Jazyk, který má být použit jako výchozí pro lokalizované prostředky. |
| MergeBehavior | Ne | Hodnoty výčtu hodnot, které jsou sloučeny spolu s jakýmkoli objektem ClaimType přítomným v nadřazené zásadě se stejným identifikátorem. Tento atribut použijte, pokud přepíšete deklaraci identity zadanou v základních zásadách. Možné hodnoty: `Append`, `Prepend`, nebo `ReplaceAll`. `Append` Hodnota určuje, že se má na konec kolekce zadané v nadřazené zásadě připojit kolekce dat. `Prepend` Hodnota určuje, že kolekce přítomných dat by měla být přidána před kolekci určenou v nadřazené zásadě. `ReplaceAll` Hodnota určuje, že kolekce dat definovaná v nadřazené zásadě by měla být použita jako místo dat definovaných v aktuálních zásadách. |

### <a name="supportedlanguages"></a>SupportedLanguages

Element **SupportedLanguages** obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1: n | Zobrazí obsah, který odpovídá značce jazyka na značku RFC 5646-Tags pro identifikaci jazyků. |

## <a name="localizedresources"></a>LocalizedResources

Element **LocalizedResources** obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Identifikátor, který slouží k jednoznačné identifikaci lokalizovaných prostředků. |

Element **LocalizedResources** obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Definuje celé kolekce v různých jazykových verzích. Kolekce může mít různý počet položek a různé řetězce pro různé jazykové verze. Příklady kolekcí zahrnují výčty, které se zobrazí v typech deklarací. Například seznam zemí nebo oblastí se uživateli zobrazí v rozevíracím seznamu. |
| LocalizedStrings | 0: n | Definuje všechny řetězce s výjimkou těch řetězců, které se zobrazují v kolekcích v různých jazykových verzích. |

### <a name="localizedcollections"></a>LocalizedCollections

Element **LocalizedCollections** obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Lokalizovaná | 1: n | Seznam podporovaných jazyků |

#### <a name="localizedcollection"></a>Lokalizovaná

**Lokalizovaný** ElementCollection obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Deklarován | Ano | Odkazuje na element ClaimType nebo na prvek uživatelského rozhraní v souboru zásad. |
| ElementId | Ano | Řetězec, který obsahuje odkaz na typ deklarace, který je již definován v části ClaimsSchema, která je použita, pokud je typ **ElementType** nastaven na typ ClaimType. |
| Cílovácollection | Ano | Cílová kolekce. |

**Lokalizovaný** ElementCollection obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Položka | 0: n | Definuje dostupnou možnost, kterou může uživatel vybrat pro deklaraci identity v uživatelském rozhraní, jako je například hodnota v rozevíracím seznamu. |

Element **Item** obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Text | Ano | Uživatelsky přívětivý řetězec zobrazení, který by měl být zobrazen uživateli v uživatelském rozhraní pro tuto možnost. |
| Value | Ano | Hodnota deklarace řetězce přidružená k výběru této možnosti |
| SelectByDefault | Ne | Určuje, zda má být tato možnost ve výchozím nastavení vybrána v uživatelském rozhraní. Možné hodnoty: True nebo False. |

Následující příklad ukazuje použití elementu **LocalizedCollections** . Obsahuje dva **lokalizované** prvkycollection, jeden pro angličtinu a druhý pro španělštinu. Nastavte kolekci **omezení** deklarace identity `Gender` se seznamem položek pro angličtinu a španělštinu.

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

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| LocalizedString | 1: n | Lokalizovaný řetězec. |

Element **LocalizedString** obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Deklarován | Ano | Odkaz na element typu deklarace identity nebo prvek uživatelského rozhraní v zásadách. Možné hodnoty: `ClaimType`, `UxElement`, `ErrorMessage`, `Predicate`, nebo. `ClaimType` Hodnota se používá k lokalizaci jednoho z atributů deklarace identity, jak je uvedeno v StringId. `UxElement` Hodnota se používá k lokalizaci jednoho z prvků uživatelského rozhraní, jak je uvedeno v StringId. `ErrorMessage` Hodnota se používá k lokalizaci jedné z chybových zpráv systému, jak je uvedeno v StringId. Hodnota se používá k lokalizaci jedné z chybových zpráv predikátu, jak je uvedeno v StringId. [](predicates.md) `Predicate` Hodnota se používá k lokalizaci jedné z chybových zpráv skupiny PredicateValidation, jak je uvedeno v StringId. [](predicates.md) `InputValidation` |
| ElementId | Ano | Pokud je typ **ElementType** nastaven `ClaimType`na `Predicate`, nebo `InputValidation`, obsahuje tento element odkaz na typ deklarace identity, který je již definován v části ClaimsSchema. |
| StringId | Ano | Pokud je vlastnost **ElementType** nastavena `ClaimType`na, tento prvek obsahuje odkaz na atribut typu deklarace. Možné hodnoty: `DisplayName`, `AdminHelpText`, nebo `PatternHelpText`. `DisplayName` Hodnota se používá k nastavení zobrazovaného názvu deklarace identity. `AdminHelpText` Hodnota slouží k nastavení názvu textu v nápovědě pro uživatele s deklarací identity. `PatternHelpText` Hodnota se používá k nastavení textu v nápovědě ke vzorci deklarace identity. Pokud je vlastnost **ElementType** nastavena `UxElement`na, tento prvek obsahuje odkaz na atribut prvku uživatelského rozhraní. Pokud je typ **ElementType** nastaven `ErrorMessage`na, tento prvek určuje identifikátor chybové zprávy. Úplný seznam `UxElement` identifikátorů naleznete v tématu [lokalizace ID řetězců](localization-string-ids.md) .|


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

Následující příklad ukazuje lokalizovaný **UserHelpText** predikátu s ID `IsLengthBetween8And64`. A lokalizovaný **UserHelpText** pro objekt **predikátu** s ID `CharacterClasses` **PredicateValidation** s ID `StrongPassword`.

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

### <a name="provide-language-specific-strings-and-collections"></a>Poskytování řetězců a kolekcí specifických pro jazyk

Přidejte prvky **LocalizedResources** uvnitř elementu **lokalizace** za ukončení elementu **SupportedLanguages** . Přidáte **LocalizedResources** prvky pro každou stránku (definice obsahu) a jakýkoli jazyk, který chcete podporovat. Chcete-li přizpůsobit jednotné přihlašování nebo přihlašovací stránku, stránky pro registraci a vícefaktorové ověřování (MFA) pro angličtinu, španělštinu a Francii, přidejte následující prvky **LocalizedResources** .

- Sjednocená stránka pro registraci nebo přihlášení, angličtina`<LocalizedResources Id="api.signuporsignin.en">`
- Jednotná registrace nebo přihlašovací stránka, španělština`<LocalizedResources Id="api.signuporsignin.es">`
- Jednotná registrace nebo přihlašovací stránka, Francie`<LocalizedResources Id="api.signuporsignin.fr">`
- Registrace, angličtina`<LocalizedResources Id="api.localaccountsignup.en">`
- Registrace, španělština`<LocalizedResources Id="api.localaccountsignup.es">`
- Registrace, Francie`<LocalizedResources Id="api.localaccountsignup.fr">`
- MFA, angličtina`<LocalizedResources Id="api.phonefactor.en">`
- MFA, španělština`<LocalizedResources Id="api.phonefactor.es">`
- MFA, Francie`<LocalizedResources Id="api.phonefactor.fr">`

Každý element **LocalizedResources** obsahuje všechny požadované elementy **LocalizedStrings** s více prvky **LocalizedString** a **LocalizedCollections** elementy s více lokalizovanými. elementu.  V následujícím příkladu se přidá lokalizace anglické stránky pro registraci:

Poznámka: Tento příklad vytvoří odkaz na `Gender` typy deklarací a. `City` Chcete-li použít tento příklad, ujistěte se, že definujete tyto deklarace identity. Další informace najdete v tématu [ClaimsSchema](claimsschema.md).

```XML
<LocalizedResources Id="api.localaccountsignup.en">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="New York" Value="NY" />
      <Item Text="Paris" Value="Paris" />
      <Item Text="London" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

Lokalizace registrační stránky pro španělštinu

```XML
<LocalizedResources Id="api.localaccountsignup.es">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="Nueva York" Value="NY" />
      <Item Text="París" Value="Paris" />
      <Item Text="Londres" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

### <a name="edit-the-contentdefinition-for-the-page"></a>Upravit ContentDefinition pro stránku

Pro každou stránku, kterou chcete lokalizovat, zadejte kódy jazyků, které se mají hledat v **ContentDefinition**.

V následujícím příkladu jsou přidány vlastní řetězce anglické (EN) a španělština (ES) do registrační stránky. **LocalizedResourcesReferenceId** pro každý **LocalizedResourcesReference** je stejný jako národní prostředí, ale můžete použít libovolný řetězec jako identifikátor. Pro každou kombinaci jazyka a stránky odkazujete na odpovídající **LocalizedResources** , které jste vytvořili dříve.

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

Následující příklad ukazuje finální XML:

```XML
<BuildingBlocks>
  <ContentDefinitions>
    <ContentDefinition Id="api.localaccountsignup">
      <!-- Other content definitions elements... -->
      <LocalizedResourcesReferences MergeBehavior="Prepend">
         <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
         <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
      </LocalizedResourcesReferences>
    </ContentDefinition>
    <!-- More content definitions... -->
  </ContentDefinitions>

  <Localization Enabled="true">

    <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
      <SupportedLanguage>en</SupportedLanguage>
      <SupportedLanguage>es</SupportedLanguage>
      <!-- More supported language... -->
    </SupportedLanguages>

    <LocalizedResources Id="api.localaccountsignup.en">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Female" Value="F" />
          <Item Text="Male" Value="M" />
          <!-- More items... -->
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="New York" Value="NY" />
          <Item Text="Paris" Value="Paris" />
          <Item Text="London" Value="London" />
        </LocalizedCollection>
        <!-- More localized collections... -->
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
       <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
        <!-- More localized strings... -->
      </LocalizedStrings>
    </LocalizedResources>

    <LocalizedResources Id="api.localaccountsignup.es">
      <LocalizedCollections>
       <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Femenino" Value="F" />
          <Item Text="Masculino" Value="M" />
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="Nueva York" Value="NY" />
          <Item Text="París" Value="Paris" />
          <Item Text="Londres" Value="London" />
        </LocalizedCollection>
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
    <!-- More localized resources... -->
  </Localization>
</BuildingBlocks>
```
