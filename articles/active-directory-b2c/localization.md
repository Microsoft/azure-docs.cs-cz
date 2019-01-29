---
title: Lokalizace – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zadejte vlastní zásady element Localization v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 8f252b536c80ad997f3c0eb10b10d5cb8c330fc6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187551"
---
# <a name="localization"></a>Lokalizace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Lokalizace** element slouží k podpoře více jazyky nebo národní prostředí v zásadách pro cesty uživatele. Podpora lokalizace v zásadách umožňuje:

- Nastavení explicitní seznam podporovaných jazyků v zásadách a vyberte výchozí jazyk.
- Zadejte řetězců specifické pro jazyk a kolekce.

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

**Lokalizace** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Povoleno | Ne | Možné hodnoty: `true` nebo `false`. |

**Lokalizace** prvek obsahuje následující prvky XML

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1: n | Seznam podporovaných jazyků. | 
| LocalizedResources | 0: n | Seznam lokalizovaných prostředků. |

## <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| DefaultLanguage | Ano | Jazyk, který má být použita jako výchozí pro lokalizované prostředky. |
| MergeBehavior | Ne | Výčtových hodnot z hodnot, které jsou slučovány spolu s jakékoli typu deklarace identity k dispozici v nadřazené zásady se stejným identifikátorem. Pomocí tohoto atributu při přepsání deklarace zadané v základních zásadách. Možné hodnoty: `Append`, `Prepend`, nebo `ReplaceAll`. `Append` Hodnota určuje, že shromažďování dat, které jsou k dispozici, má být připojen na konec kolekce zadaná v nadřazené zásady. `Prepend` Hodnota určuje, že shromažďování dat, které jsou k dispozici by se měl přidat před kolekce zadaná v nadřazené zásady. `ReplaceAll` Hodnota určuje, že shromažďování dat, které jsou definovány v nadřazené zásady by měl být ignorovány, místo toho použít data definovaná v aktuální zásady. |

### <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** prvek obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1: n | Zobrazí obsah, který odpovídá značku jazyka podle RFC 5646 – značky pro identifikaci jazyků. | 

## <a name="localizedresources"></a>LocalizedResources

**LocalizedResources** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Identifikátor, který slouží k jednoznačné identifikaci lokalizované prostředky. |

**LocalizedResources** prvek obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Definuje celé kolekce v různé jazykové verze. Kolekce může mít různý počet položek a různých řetězců pro různé jazykové verze. Kolekce příklady výčty, které se zobrazují v typy deklarací identity. Například je uživateli v rozevíracím seznamu zobrazí seznam země/oblast. |
| LocalizedStrings | 0: n | Definuje všechny řetězce, s výjimkou těchto řetězců, které se zobrazují v kolekcích v různé jazykové verze. |

### <a name="localizedcollections"></a>LocalizedCollections

**LocalizedCollections** prvek obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1: n | Seznam podporovaných jazyků. |

#### <a name="localizedcollection"></a>LocalizedCollection

**LocalizedCollection** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ElementType | Ano | Odkazuje na element typu deklarace identity nebo prvek uživatelského rozhraní v souboru zásad. |
| ElementId | Ano | Řetězec, který již obsahuje odkaz na typ deklarace identity definovaný v sekci ClaimsSchema, který se používá v případě **ElementType** nastavena typu deklarace identity. |
| TargetCollection | Ano | Cílové kolekce. |

**LocalizedCollection** prvek obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Položka | 0: n | Definuje k dispozici možnost uživatele vybrat pro deklaraci identity v uživatelském rozhraní, jako jsou hodnoty v rozevíracího seznamu. |

**Položky** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Text | Ano | Popisný Zobrazovaný řetězec, který má být zobrazena uživateli v uživatelském rozhraní pro tuto možnost. |
| Value | Ano | Řetězec deklarace identity hodnotu přidruženou k výběru této možnosti. |

Následující příklad ukazuje použití **LocalizedCollections** elementu. Obsahuje dva **LocalizedCollection** prvky, jeden pro angličtinu a jinou pro španělštinu. Obě nastaveny **omezení** kolekce deklarace `Gender` se seznamem položek pro angličtinu a slovenštinu.

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

**LocalizedStrings** prvek obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| LocalizedString | 1: n | Lokalizovaný řetězec. |

**LocalizedString** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ElementType | Ano | Odkaz na element typu deklarace identity nebo prvek uživatelského rozhraní v zásadách. Možné hodnoty: `ClaimType`, `UxElement`, `ErrorMessage`, `Predicate`, nebo. `ClaimType` Hodnota slouží k lokalizaci jeden z atributů deklarace identity, jako zadané v poli řetězců. `UxElement` Hodnota slouží k lokalizaci jeden z prvků uživatelského rozhraní, jako zadané v poli řetězců. `ErrorMessage` Hodnota slouží k lokalizaci některá z chybových zpráv systému jako zadané v poli řetězců. `Predicate` Hodnota slouží k lokalizaci některou [predikátu](predicates.md) chybové zprávy, jako zadané v poli řetězců. `InputValidation` Hodnota slouží k lokalizaci některou [PredicateValidation](predicates.md) skupině chybové zprávy jako zadané v poli řetězců. |
| ElementId | Ano | Pokud **ElementType** je nastavena na `ClaimType`, `Predicate`, nebo `InputValidation`, tento prvek obsahuje odkaz na typ deklarace identity již definovaná v části ClaimsSchema. | 
| StringId | Ano | Pokud **ElementType** je nastavena na `ClaimType`, tento prvek obsahuje odkaz na atribut typu deklarace identity. Možné hodnoty: `DisplayName`, `AdminHelpText`, nebo `PatternHelpText`. `DisplayName` Hodnota se používá k nastavení deklaraci identity zobrazované jméno. `AdminHelpText` Hodnota se používá k nastavení názvu text nápovědy deklarace identity uživatele. `PatternHelpText` Hodnota se používá k nastavení text nápovědy vzor deklarace identity. Pokud **ElementType** je nastavena na `UxElement`, tento prvek obsahuje odkaz na atribut prvku uživatelského rozhraní. Pokud **ElementType** je nastavena na `ErrorMessage`, tento prvek určuje identifikátor chybovou zprávu. Zobrazit [ID lokalizace řetězce](localization-string-ids.md) pro úplný seznam `UxElement` identifikátory.|


Následující příklad ukazuje lokalizované stránku pro přihlášení. První tři **LocalizedString** hodnoty nastavit atribut deklarace identity. Třetí změní hodnotu tlačítka pro pokračování. Poslední změny chybová zpráva.

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

Následující příklad ukazuje lokalizované **UserHelpText** z **predikátu** s Id `IsLengthBetween8And64`. A do **UserHelpText** z **PredicateGroup** s Id `CharacterClasses` z **PredicateValidation** s Id `StrongPassword`.

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

V tomto článku se dozvíte, jak podporovat více jazyky nebo národní prostředí v zásadách pro cesty, uživatele. Lokalizace vyžaduje tři kroky: nastavení explicitní seznam podporovaných jazyků, zadejte řetězců specifické pro jazyk a kolekcí a upravit ContentDefinition stránky.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Nastavení explicitní seznam podporovaných jazyků

V části **BuildingBlocks** prvku, přidejte **lokalizace** element s seznam podporovaných jazyků. Následující příklad ukazuje, jak definovat podporu lokalizace pro angličtinu (výchozí) a španělština:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

### <a name="provide-language-specific-strings-and-collections"></a>Zadejte řetězců specifické pro jazyk a kolekce 

Přidat **LocalizedResources** elementů v rámci **lokalizace** elementu po uzavření **SupportedLanguages** elementu. Přidáte **LocalizedResources** prvky pro každou stránku (obsahu definice) a libovolný jazyk, které chcete podporovat. Chcete-li přizpůsobit jednotná stránka registrace / přihlášení, registrace a vícefaktorové ověřování (MFA) stránky pro angličtina, španělština a (Francie), přidejte následující **LocalizedResources** elementy.  
- Sjednocené stránku registrace / přihlášení, angličtina `<LocalizedResources Id="api.signuporsignin.en">`
- Sjednocené stránku registrace / přihlášení, španělština `<LocalizedResources Id="api.signuporsignin.es">`
- Sjednocené stránku registrace nebo přihlášení (Francie) `<LocalizedResources Id="api.signuporsignin.fr">` 
- Registrace, angličtina `<LocalizedResources Id="api.localaccountsignup.en">`
- Registrace, španělština `<LocalizedResources Id="api.localaccountsignup.es">`
- Registrace, Francie `<LocalizedResources Id="api.localaccountsignup.fr">`
- Vícefaktorové ověřování, angličtina `<LocalizedResources Id="api.phonefactor.en">`
- Vícefaktorové ověřování, španělština `<LocalizedResources Id="api.phonefactor.es">`
- Vícefaktorové ověřování (Francie) `<LocalizedResources Id="api.phonefactor.fr">`

Každý **LocalizedResources** element obsahuje všechny požadované **LocalizedStrings** prvky s několika **LocalizedString** elementy a  **LocalizedCollections** prvky s několika **LocalizedCollection** elementy.  Následující příklad přidá anglické lokalizace stránku pro přihlášení: 

Poznámka: V tomto příkladu odkazuje na `Gender` a `City` typy deklarací identity. Pokud chcete použít v tomto příkladu, ujistěte se, že definovat tyto deklarace identit. Další informace najdete v tématu [ClaimsSchema](claimsschema.md).

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

Lokalizace registrační stránku pro španělštinu.

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

### <a name="edit-the-contentdefinition-for-the-page"></a>Upravit ContentDefinition stránky 

Pro každou stránku, kterou chcete lokalizovat, zadejte kód jazyka v **ContentDefinition**.

V následujícím příkladu Angličtina (en) a španělština (es) vlastního řetězce jsou přidány na stránku pro přihlášení. **LocalizedResourcesReferenceId** pro každou **LocalizedResourcesReference** je stejný jako jejich národní prostředí, ale můžete použít libovolný řetězec jako identifikátor. Pro každou kombinaci jazyka a stránky, přejdete na odpovídající **LocalizedResources** jste dříve vytvořili.

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

Následující příklad ukazuje poslední XML:

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




