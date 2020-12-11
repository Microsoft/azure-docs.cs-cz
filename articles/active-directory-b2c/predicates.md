---
title: Predikáty a PredicateValidations
titleSuffix: Azure AD B2C
description: Zabránit přidávání poškozených dat do tenanta Azure AD B2C pomocí vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 46f04c55b40d4f1bdbbf5fd55eb648d1d3294056
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108412"
---
# <a name="predicates-and-predicatevalidations"></a>Predikáty a PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Predikáty** a **PredicateValidations** prvky umožňují provést proces ověřování, aby bylo zajištěno, že budou do tenanta Azure Active Directory B2C (Azure AD B2C) zadávána pouze správně vytvořená data.

Následující diagram znázorňuje vztah mezi prvky:

![Diagram znázorňující vztah ověření a predikátu predikátu](./media/predicates/predicates.png)

## <a name="predicates"></a>Predikáty

Prvek **predikátu** definuje základní ověření pro kontrolu hodnoty typu deklarace identity a vrátí `true` nebo `false` . Ověřování je provedeno pomocí zadaného prvku **metody** a sady prvků **parametru** , které jsou relevantní pro metodu. Predikát například může ověřit, zda délka hodnoty deklarace řetězce spadá do rozsahu minimálního a maximálního zadaného parametru nebo zda hodnota deklarace řetězce obsahuje znaková sada. Element **UserHelpText** poskytuje uživatelům chybovou zprávu, pokud se ověření nepovede. Hodnotu prvku **UserHelpText** lze lokalizovat pomocí [přizpůsobení jazyka](localization.md).

Element **predikáts** musí být uveden přímo za elementem **ClaimsSchema** v rámci elementu [BuildingBlocks](buildingblocks.md) .

Element **predikáts** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Predikát | 1: n | Seznam predikátů. |

Element **predikátu** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Identifikátor, který se používá pro predikát. Ostatní elementy můžou tento identifikátor v zásadách použít. |
| Metoda | Ano | Typ metody, který se má použít pro ověření. Možné hodnoty: [IsLengthRange](#islengthrange), [MatchesRegex](#matchesregex), [IncludesCharacters](#includescharacters)nebo [IsDateRange](#isdaterange).  |
| HelpText | Ne | Chybová zpráva pro uživatele, pokud se ověření nepovede. Tento řetězec se dá lokalizovat pomocí [přizpůsobení jazyka](localization.md) . |

Element **predikát** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 | Zastaralé Chybová zpráva pro uživatele, pokud se ověření nepovede. |
| Parametry | 1:1 | Parametry pro typ metody pro ověření řetězce. |

Element **Parameters** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Parametr | 1: n | Parametry pro typ metody pro ověření řetězce. |

Element **Parameter** obsahuje následující atributy:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Id | 1:1 | Identifikátor parametru |

### <a name="predicate-methods"></a>Metody predikátu

#### <a name="islengthrange"></a>IsLengthRange

Metoda IsLengthRange ověřuje, zda délka hodnoty deklarace řetězce spadá do rozsahu zadaného minimálního a maximálního počtu parametrů. Element predikát podporuje následující parametry:

| Parametr | Povinné | Popis |
| ------- | ----------- | ----------- |
| Maximum | Ano | Maximální počet znaků, které lze zadat. |
| Minimum | Ano | Minimální počet znaků, které je třeba zadat. |


Následující příklad ukazuje metodu IsLengthRange s parametry `Minimum` a `Maximum` , která určuje rozsah délky řetězce:

```xml
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
  <Parameters>
    <Parameter Id="Minimum">8</Parameter>
    <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="matchesregex"></a>MatchesRegex

Metoda MatchesRegex ověří, zda hodnota deklarace řetězce odpovídá regulárnímu výrazu. Element predikát podporuje následující parametry:

| Parametr | Povinné | Popis |
| ------- | ----------- | ----------- |
| RegularExpression | Ano | Vzorek regulárního výrazu, který se má shodovat. |

Následující příklad ukazuje `MatchesRegex` metodu s parametrem `RegularExpression` , který určuje regulární výraz:

```xml
<Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="includescharacters"></a>IncludesCharacters

Metoda IncludesCharacters ověří, zda hodnota deklarace řetězce obsahuje znaková sada. Element predikát podporuje následující parametry:

| Parametr | Povinné | Popis |
| ------- | ----------- | ----------- |
| CharacterSet | Ano | Sada znaků, které lze zadat. Například malými písmeny  `a-z` , velkými písmeny `A-Z` , číslicemi `0-9` nebo seznamem symbolů, jako je například `@#$%^&amp;*\-_+=[]{}|\\:',?/~"();!` . |

Následující příklad ukazuje `IncludesCharacters` metodu s parametrem `CharacterSet` , který určuje množinu znaků:

```xml
<Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="isdaterange"></a>IsDateRange

Metoda IsDateRange ověří, zda je hodnota deklarace data v rozsahu zadaného minimálního a maximálního počtu parametrů. Element predikát podporuje následující parametry:

| Parametr | Povinné | Popis |
| ------- | ----------- | ----------- |
| Maximum | Ano | Největší možné datum, které lze zadat. Formát data následuje po `yyyy-mm-dd` konvenci nebo `Today` . |
| Minimum | Ano | Nejnižší možné datum, které lze zadat. Formát data následuje po `yyyy-mm-dd` konvenci nebo `Today` .|

Následující příklad ukazuje `IsDateRange` metodu s parametry `Minimum` a `Maximum` , která určuje rozsah kalendářních dat formátu `yyyy-mm-dd` a `Today` .

```xml
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations

I když predikáty definují ověření pro kontrolu proti typu deklarace, skupina **PredicateValidations** sadu predikátů pro vytvoření ověřování vstupu uživatele, které lze použít na typ deklarace identity. Každý element **PredicateValidation** obsahuje sadu elementů **predikátu** , které obsahují sadu elementů **PredicateReference** , které odkazují na **predikát**. Chcete-li předat ověření, hodnota deklarace by měla předat všechny testy predikátu v rámci všechny skupiny **predikátů** s jejich sadou **PredicateReference** prvků.

Element **PredicateValidations** se musí objevit přímo za prvkem **predikáts** v rámci elementu [BuildingBlocks](buildingblocks.md) .

```xml
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

Element **PredicateValidations** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| PredicateValidation | 1: n | Seznam ověření predikátu |

Element **PredicateValidation** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Identifikátor, který se používá pro ověření predikátu. Element **ClaimType** může použít tento identifikátor v zásadách. |

Element **PredicateValidation** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| PredicateGroups | 1: n | Seznam skupin predikátů. |

Element **PredicateGroups** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Predikátcollection | 1: n | Seznam predikátů. |

Element **predikátu** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Identifikátor, který se používá pro skupinu predikátů.  |

Element **predikátu** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 |  Popis predikátu, který může být užitečný pro uživatele, aby věděli, jaká hodnota by měla být typu. |
| PredicateReferences | 1: n | Seznam odkazů na predikáty |

Element **PredicateReferences** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| MatchAtLeast | Ne | Určuje, že hodnota se musí shodovat s minimálním počtem definic predikátu, aby bylo možné vstup přijmout. Pokud není zadán, hodnota musí odpovídat všem definicím predikátu. |

Element **PredicateReferences** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| PredicateReference | 1: n | Odkaz na predikát. |

Element **PredicateReference** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Identifikátor, který se používá pro ověření predikátu.  |


## <a name="configure-password-complexity"></a>Konfigurace složitosti hesla

Pomocí **predikátů** a **PredicateValidationsInput** můžete řídit požadavky na složitost pro hesla poskytnutá uživatelem při vytváření účtu. Ve výchozím nastavení používá Azure AD B2C používání silných hesel. Azure AD B2C podporuje také možnosti konfigurace pro kontrolu složitosti hesel, která zákazníci můžou používat. Složitost hesla můžete definovat pomocí těchto elementů predikátů:

- **IsLengthBetween8And64** pomocí `IsLengthRange` metody ověří, že heslo musí mít 8 až 64 znaků.
- **Pomocí** `IncludesCharacters` metody ověří, zda heslo obsahuje malé písmeno.
- **Pomocí** `IncludesCharacters` metody ověří, zda heslo obsahuje velké písmeno.
- **Číslo** pomocí `IncludesCharacters` metody ověří, zda heslo obsahuje číslici.
- **Symbol** pomocí `IncludesCharacters` metody ověří, zda heslo obsahuje jeden z několika znaků symbolu.
- **PIN** pomocí `MatchesRegex` metody ověří, zda heslo obsahuje pouze čísla.
- **AllowedAADCharacters** pomocí `MatchesRegex` metody ověří, zda byl zadán pouze neplatný znak hesla.
- **DisallowedWhitespace** pomocí `MatchesRegex` metody ověří, zda heslo nezačíná nebo nekončí prázdným znakem.

```xml
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

Po definování základních ověření je můžete kombinovat dohromady a vytvořit sadu zásad hesel, které můžete použít v zásadách:

- **SimplePassword** ověřuje DisallowedWhitespace, AllowedAADCharacters a IsLengthBetween8And64
- **Strongpassword** ověří DisallowedWhitespace, AllowedAADCharacters, IsLengthBetween8And64. Poslední skupina `CharacterClasses` spustí další sadu predikátů s `MatchAtLeast` nastavením na 3. Heslo uživatele musí mít 8 až 16 znaků a tři z následujících znaků: malá písmena, Velká písmena, číslice nebo symboly.
- **CustomPassword** ověří pouze DisallowedWhitespace, AllowedAADCharacters. Takže uživatel může zadat jakékoli heslo s libovolnou délkou, pokud jsou tyto znaky platné.

```xml
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

V typu deklarace přidejte element **PredicateValidationReference** a určete identifikátor jako jeden z ověření predikátu, jako je například SimplePassword, strongpassword nebo CustomPassword.

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

Následující příklad ukazuje, jak jsou prvky uspořádány, když Azure AD B2C zobrazuje chybovou zprávu:

![Diagram pro příklad složitosti hesla a predikátu](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Konfigurace rozsahu kalendářních dat

Pomocí **predikátů** a elementů **PredicateValidations** můžete řídit minimální a maximální hodnoty data **UserInputType** pomocí `DateTimeDropdown` . K tomu je potřeba vytvořit **predikát** s `IsDateRange` metodou a zadat minimální a maximální parametry.

```xml
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 01-01-1980 and today.">
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Přidejte **PredicateValidation** s odkazem na `DateRange` predikát.

```xml
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

V typu deklarace přidejte element **PredicateValidationReference** a určete identifikátor jako `CustomDateRange` .

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```

## <a name="next-steps"></a>Další kroky

- Naučte se [Konfigurovat složitost hesla pomocí vlastních zásad v Azure Active Directory B2C](password-complexity.md) pomocí ověřování predikátů.