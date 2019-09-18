---
title: Predikáty a PredicateValidations-Azure Active Directory B2C | Microsoft Docs
description: Příklady transformace deklarací účtu sociální sítě pro schéma rozhraní Azure Active Directory B2C prostředí identity
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ecec18945b53711094307162c4aeab2e0580bd5e
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063857"
---
# <a name="predicates-and-predicatevalidations"></a>Predikáty a PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Predikáty** a **PredicateValidations** prvky umožňují provést proces ověřování, aby bylo zajištěno, že budou do tenanta Azure Active Directory B2C (Azure AD B2C) zadávána pouze správně vytvořená data.

Následující diagram znázorňuje vztah mezi prvky:

![Diagram znázorňující vztah ověření a predikátu predikátu](./media/predicates/predicates.png)

## <a name="predicates"></a>Predikáty

Prvek **predikátu** definuje základní ověření pro kontrolu hodnoty typu deklarace identity a vrátí `true` nebo. `false` Ověřování je provedeno pomocí zadaného prvku **metody** a sady prvků **parametru** , které jsou relevantní pro metodu. Predikát například může ověřit, zda délka hodnoty deklarace řetězce spadá do rozsahu minimálního a maximálního zadaného parametru nebo zda hodnota deklarace řetězce obsahuje znaková sada. Element **UserHelpText** poskytuje uživatelům chybovou zprávu, pokud se ověření nepovede. Hodnotu prvku **UserHelpText** lze lokalizovat pomocí [přizpůsobení jazyka](localization.md).

Element **predikáts** obsahuje následující element:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Predikát | 1: n | Seznam predikátů. |

Element **predikátu** obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Identifikátor, který se používá pro predikát. Ostatní elementy můžou tento identifikátor v zásadách použít. |
| Metoda | Ano | Typ metody, který se má použít pro ověření. Možné hodnoty: **IsLengthRange**, **MatchesRegex**, **IncludesCharacters**nebo **IsDateRange**. Hodnota **IsLengthRange** ověří, zda je délka hodnoty deklarace řetězce v rozsahu zadaného minimálního a maximálního počtu zadaných parametrů. Hodnota **MatchesRegex** zkontroluje, jestli hodnota deklarace řetězce odpovídá regulárnímu výrazu. Hodnota **IncludesCharacters** zkontroluje, jestli hodnota deklarace řetězce obsahuje znaková sada. Hodnota **IsDateRange** zkontroluje, jestli je hodnota deklarace data v rozsahu zadaného minimálního a maximálního počtu parametrů. |

Element **predikát** obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 | Chybová zpráva pro uživatele, pokud se ověření nepovede. Tento řetězec se dá lokalizovat pomocí [přizpůsobení jazyka](localization.md) . |
| Parametry | 1:1 | Parametry pro typ metody pro ověření řetězce. |

Element **Parameters** obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Parametr | 1: n | Parametry pro typ metody pro ověření řetězce. |

Element **Parameter** obsahuje následující atributy:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Id | 1:1 | Identifikátor parametru |

Následující příklad ukazuje `IsLengthRange` metodu s parametry `Minimum` a `Maximum` , která určuje rozsah délky řetězce:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

Následující příklad ukazuje `MatchesRegex` metodu s parametrem `RegularExpression` , který určuje regulární výraz:

```XML
<Predicate Id="PIN" Method="MatchesRegex">
  <UserHelpText>The password must be numbers only.</UserHelpText>
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

Následující příklad ukazuje `IncludesCharacters` metodu s parametrem `CharacterSet` , který určuje množinu znaků:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters">
  <UserHelpText>a lowercase letter</UserHelpText>
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

`IsDateRange` Následující příklad ukazuje metodu s parametry `Maximum` `Minimum` a, která `yyyy-MM-dd` určuje rozsah kalendářních dat formátu a `Today`.

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations

I když predikáty definují ověření pro kontrolu proti typu deklarace, skupina **PredicateValidations** sadu predikátů pro vytvoření ověřování vstupu uživatele, které lze použít na typ deklarace identity. Každý element **PredicateValidation** obsahuje sadu elementů **predikátu** , které obsahují sadu elementů **PredicateReference** , které odkazují na **predikát**. Chcete-li předat ověření, hodnota deklarace by měla předat všechny testy predikátu v rámci všechny skupiny predikátů s jejich sadou **PredicateReference** prvků.

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

Element **PredicateValidations** obsahuje následující element:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| PredicateValidation | 1: n | Seznam ověření predikátu |

Element **PredicateValidation** obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Identifikátor, který se používá pro ověření predikátu. Element **ClaimType** může použít tento identifikátor v zásadách. |

Element **PredicateValidation** obsahuje následující element:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| PredicateGroups | 1: n | Seznam skupin predikátů. |

Element **PredicateGroups** obsahuje následující element:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| PredicateGroup | 1: n | Seznam predikátů. |

Element **predikátu** obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Identifikátor, který se používá pro skupinu predikátů.  |

Element **predikátu** obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 |  Popis predikátu, který může být užitečný pro uživatele, aby věděli, jaká hodnota by měla být typu. |
| PredicateReferences | 1: n | Seznam odkazů na predikáty |

Element **PredicateReferences** obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| MatchAtLeast | Ne | Určuje, že hodnota se musí shodovat s minimálním počtem definic predikátu, aby bylo možné vstup přijmout. |

Element **PredicateReferences** obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| PredicateReference | 1: n | Odkaz na predikát. |

Element **PredicateReference** obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Identifikátor, který se používá pro ověření predikátu.  |


## <a name="configure-password-complexity"></a>Konfigurace složitosti hesla

Pomocí **predikátů** a **PredicateValidationsInput** můžete řídit požadavky na složitost pro hesla poskytnutá uživatelem při vytváření účtu. Ve výchozím nastavení používá Azure AD B2C používání silných hesel. Azure AD B2C podporuje také možnosti konfigurace pro kontrolu složitosti hesel, která zákazníci můžou používat. Složitost hesla můžete definovat pomocí těchto elementů predikátů:

- **IsLengthBetween8And64** pomocí `IsLengthRange` metody ověří, že heslo musí mít 8 až 64 znaků.
- Pomocí`IncludesCharacters` metody ověří, zda heslo obsahuje malé písmeno.
- Pomocí`IncludesCharacters` metody ověří, zda heslo obsahuje velké písmeno.
- **Číslo** pomocí `IncludesCharacters` metody ověří, zda heslo obsahuje číslici.
- **Symbol** pomocí `IncludesCharacters` metody ověří, zda heslo obsahuje jeden z následujících symbolů`@#$%^&*\-_+=[]{}|\:',?/~"();!`
- **PIN** pomocí `MatchesRegex` metody ověří, zda heslo obsahuje pouze čísla.
- **AllowedAADCharacters** pomocí `MatchesRegex` metody ověří, zda byl zadán pouze neplatný znak hesla.
- **DisallowedWhitespace** pomocí `MatchesRegex` metody ověří, zda heslo nezačíná nebo nekončí prázdným znakem.

```XML
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
    <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters">
    <UserHelpText>a lowercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters">
    <UserHelpText>an uppercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters">
    <UserHelpText>a digit</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters">
    <UserHelpText>a symbol</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\:',?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex">
    <UserHelpText>The password must be numbers only.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex">
    <UserHelpText>An invalid character was provided.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex">
    <UserHelpText>The password must not begin or end with a whitespace character.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

Po definování základních ověření je můžete kombinovat dohromady a vytvořit sadu zásad hesel, které můžete použít v zásadách:

- **SimplePassword** ověřuje DisallowedWhitespace, AllowedAADCharacters a IsLengthBetween8And64
- **Strongpassword** ověří DisallowedWhitespace, AllowedAADCharacters, IsLengthBetween8And64. Poslední skupina `CharacterClasses` spustí další sadu predikátů s `MatchAtLeast` nastavením na 3. Heslo uživatele musí mít 8 až 16 znaků a tři z následujících znaků: Malá písmena, Velká písmena, číslice nebo symboly.
- **CustomPassword** ověří pouze DisallowedWhitespace, AllowedAADCharacters. Takže uživatel může zadat jakékoli heslo s libovolnou délkou, pokud jsou tyto znaky platné.

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

V typu deklarace přidejte element **PredicateValidationReference** a určete identifikátor jako jeden z ověření predikátu, jako je například SimplePassword, strongpassword nebo CustomPassword.

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

Následující příklad ukazuje, jak jsou prvky uspořádány, když Azure AD B2C zobrazuje chybovou zprávu:

![Diagram pro příklad složitosti hesla a predikátu](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Konfigurace rozsahu kalendářních dat

Pomocí **predikátů** a elementů **PredicateValidations** můžete řídit minimální a maximální hodnoty data `DateTimeDropdown` **UserInputType** pomocí. K tomu je potřeba vytvořit **predikát** s `IsDateRange` metodou a zadat minimální a maximální parametry.

```XML
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange">
    <UserHelpText>The date must be between 01-01-1980 and today.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Přidejte **PredicateValidation** s odkazem na `DateRange` predikát.

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

V typu deklarace přidejte element **PredicateValidationReference** a určete identifikátor jako `CustomDateRange`.

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
