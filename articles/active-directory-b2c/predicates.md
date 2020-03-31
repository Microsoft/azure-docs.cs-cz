---
title: Predikáty a predikátyvalidace
titleSuffix: Azure AD B2C
description: Zabraňte přidání poškozených dat do klienta Azure AD B2C pomocí vlastních zásad ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cc61ef5980a8019514f05c1db47f2300fff3603b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187232"
---
# <a name="predicates-and-predicatevalidations"></a>Predikáty a predikátyvalidace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Predikáty** a **PredicateValidations prvky** umožňují provést proces ověření k zajištění, že pouze správně vytvořená data je zadána do vašeho klienta Azure Active Directory B2C (Azure AD B2C).

Následující diagram znázorňuje vztah mezi prvky:

![Diagram znázorňující vztah predikáty a predikáty ověření](./media/predicates/predicates.png)

## <a name="predicates"></a>Predikáty

**Predikát** prvek definuje základní ověření ke kontrole hodnoty typu `true` deklarace a vrátí nebo `false`. Ověření se provádí pomocí zadaného prvku **Method** a sady prvků **Parametr** relevantních pro metodu. Predikát může například zkontrolovat, zda je délka hodnoty deklarace řetězce v rozsahu minimálních a maximálních zadaných parametrů nebo zda hodnota deklarace řetězce obsahuje znakovou sadu. Prvek **UserHelpText** poskytuje uživatelům chybovou zprávu, pokud se kontrola nezdaří. Hodnota prvku **UserHelpText** může být lokalizována pomocí [přizpůsobení jazyka](localization.md).

**Predikáty** prvek musí zobrazit přímo za **ClaimsSchema** prvek v rámci [BuildingBlocks](buildingblocks.md) element.

Prvek **Predikáty** obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Predikátu | 1:n | Seznam predikátů. |

**Predikát** ový prvek obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Identifikátor, který se používá pro predikát. Ostatní prvky můžete použít tento identifikátor v zásadě. |
| Metoda | Ano | Typ metody, který se má použít pro ověření. Možné hodnoty: **IsLengthRange**, **MatchesRegex**, **IncludesCharacters**nebo **IsDateRange**. Hodnota **IsLengthRange** kontroluje, zda je délka hodnoty deklarace řetězce v rozsahu minimálních a maximálních zadaných parametrů. Hodnota **MatchesRegex** kontroluje, zda hodnota deklarace řetězce odpovídá regulárnímu výrazu. Hodnota **IncludesCharacters** kontroluje, zda hodnota deklarace řetězce obsahuje znakovou sadu. Hodnota **IsDateRange** kontroluje, zda je hodnota deklarace data mezi rozsahem minimálních a maximálních zadaných parametrů. |
| Helptext | Ne | Chybová zpráva pro uživatele, pokud se kontrola nezdaří. Tento řetězec lze lokalizovat pomocí [přizpůsobení jazyka.](localization.md) |

**Predikát** prvek obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Text uživatelské nápovědy | 0:1 | (Zastaralé) Chybová zpráva pro uživatele, pokud se kontrola nezdaří. |
| Parametry | 1:1 | Parametry pro typ metody ověření řetězce. |

**Parametry** Element obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Parametr | 1:n | Parametry pro typ metody ověření řetězce. |

Parametr **Element** obsahuje následující atributy:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| ID | 1:1 | Identifikátor parametru. |

Následující příklad ukazuje `IsLengthRange` metodu s `Minimum` `Maximum` parametry a které určují rozsah délky řetězce:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
  <Parameters>
    <Parameter Id="Minimum">8</Parameter>
    <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

Následující příklad ukazuje `MatchesRegex` metodu `RegularExpression` s parametrem, který určuje regulární výraz:

```XML
<Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

Následující příklad ukazuje `IncludesCharacters` metodu `CharacterSet` s parametrem, který určuje sadu znaků:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

Následující příklad ukazuje `IsDateRange` metodu s `Minimum` `Maximum` parametry a které určují `yyyy-MM-dd` rozsah `Today`dat s formátem a .

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredikateValidations

Zatímco predikáty definovat ověření pro kontrolu proti typu **deklarace, PredicateValidations skupiny** sadu predikáty tvořit ověření vstupu uživatele, které lze použít pro typ deklarace. Každý **predikátValidation** prvek obsahuje sadu **PredicateGroup** prvky, které obsahují sadu **PredicateReference** prvky, které odkazuje na **predikát**. Chcete-li předat ověření, hodnota deklarace by měla projít všechny testy všechny predikátu v rámci všech **PredicateGroup** s jejich sadu **PredicateReference** prvky.

**PredikateValidations** element musí být uveden přímo za **Predicates** prvek v rámci [BuildingBlocks](buildingblocks.md) element.

```XML
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

**PredikateValidations** prvek obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Predikatevalidation | 1:n | Seznam ověření predikátu. |

**PredikateValidation** element obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Identifikátor, který se používá pro ověření predikátu. **ClaimType** Element můžete použít tento identifikátor v zásadě. |

**PredikateValidation** prvek obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Predikátové skupiny | 1:n | Seznam predikátových skupin. |

**PredikátGroups** Prvek obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Predikátová skupina | 1:n | Seznam predikátů. |

Prvek **PredikateGroup** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Identifikátor, který se používá pro skupinu predikátu.  |

Prvek **PredikateGroup** obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Text uživatelské nápovědy | 0:1 |  Popis predikátu, který může být pro uživatele užitečný, aby věděli, jakou hodnotu by měli zadat. |
| PredikátOdkazy | 1:n | Seznam predikátových odkazů. |

Prvek **PredicateReferences** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Matchatleast | Ne | Určuje, že hodnota musí odpovídat alespoň tolik a mnoho predikát definice pro vstup, který má být přijat. Pokud není zadán, hodnota musí odpovídat všem predikátdefinice. |

**PredikátReferenceelement** obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| PredikátOdkaz | 1:n | Odkaz na predikát. |

**PredikátReference** element obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Identifikátor, který se používá pro ověření predikátu.  |


## <a name="configure-password-complexity"></a>Konfigurace složitosti hesla

S **Predicáty** a **PredicateValidationsInput** můžete řídit požadavky na složitost pro hesla poskytované uživatelem při vytváření účtu. Ve výchozím nastavení Azure AD B2C používá silná hesla. Azure AD B2C také podporuje možnosti konfigurace pro řízení složitosti hesel, které zákazníci mohou používat. Složitost hesla můžete definovat pomocí těchto predikátových prvků:

- **IsLengthBetween8And64** pomocí `IsLengthRange` metody, ověří, že heslo musí být mezi 8 a 64 znaků.
- **Malá písmena** pomocí `IncludesCharacters` metody, ověří, že heslo obsahuje malé písmeno.
- **Velká písmena** pomocí `IncludesCharacters` metody, ověří, že heslo obsahuje velké písmeno.
- **Číslo** pomocí `IncludesCharacters` metody, ověří, že heslo obsahuje číslici.
- **Symbol** pomocí `IncludesCharacters` metody ověří, zda heslo obsahuje jeden z několika znaků symbolu.
- **PIN** pomocí `MatchesRegex` metody, ověří, že heslo obsahuje pouze čísla.
- **AllowedAADCharacters** pomocí `MatchesRegex` metody, ověří, že heslo pouze neplatný znak byl poskytnut.
- **NepovolenýWhitespace** pomocí `MatchesRegex` metody, ověří, že heslo nezačíná nebo končí s prázdné znaky.

```XML
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
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

  <Predicate Id="Number" Method="IncludesCharacters" HelpText="a digit">
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters" HelpText="a symbol">
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex" HelpText="An invalid character was provided.">
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex" HelpText="The password must not begin or end with a whitespace character.">
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

Po definování základních ověření je můžete kombinovat a vytvořit sadu zásad hesel, které můžete použít v zásadách:

- **SimplePassword** ověří disallowedWhitespace, AllowedAADCharacters a IsLengthBetween8And64
- **StrongPassword** ověří DisallowedWhitespace, AllowedAADCharacters, IsLengthBetween8And64. Poslední skupina `CharacterClasses` spustí další sadu predikátů s `MatchAtLeast` nastavenou na 3. Uživatelské heslo musí mít 8 až 16 znaků a tři z následujících znaků: Malá písmena, Velká písmena, Číslo nebo Symbol.
- **CustomPassword** ověří pouze DisallowedWhitespace, AllowedAADCharacters. Takže uživatel může poskytnout libovolné heslo s libovolnou délkou, pokud jsou znaky platné.

```XML
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
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

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

V typu deklarace aplikace přidejte **predikátValidationReference** element a zadejte identifikátor jako jeden z ověření predikátu, jako je například SimplePassword, StrongPassword nebo CustomPassword.

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

Následující ukazuje, jak jsou uspořádány prvky při Azure AD B2C zobrazí chybovou zprávu:

![Příklad složitosti hesla predikátu a predikátuSkupiny](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Konfigurace rozsahu dat

S **Predikáty** a **PredicateValidations prvky** můžete řídit minimální a maximální hodnoty `DateTimeDropdown`data **UserInputType** pomocí . Chcete-li to provést, vytvořte `IsDateRange` **predikát** s metodou a zadejte minimální a maximální parametry.

```XML
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 01-01-1980 and today.">
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Přidejte **PredikátValidation** s odkazem na `DateRange` predikát.

```XML
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Do typu deklarace aplikace přidejte prvek **PredicateValidationReference** a zadejte identifikátor jako `CustomDateRange`.

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```
