---
title: ClaimsSchema – Služba Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Zadejte claimsSchema prvek vlastní zásady ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4c3b3318e941723ec333597c7e4b3e48710152d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78397810"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**ClaimsSchema** prvek definuje typy deklarací, které lze odkazovat jako součást zásady. Schéma nároků je místo, kde své nároky deklarujete. Nárok může být křestní jméno, příjmení, zobrazované jméno, telefonní číslo a další. ClaimSchema element obsahuje seznam **ClaimType** prvky. **ClaimType** Element obsahuje Atribut **Id,** což je název deklarace.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>Typ deklarace pohledávky

Prvek **ClaimType** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Identifikátor, který se používá pro typ deklarace. Ostatní prvky můžete použít tento identifikátor v zásadě. |

**ClaimType** Element obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | Název, který se uživatelům zobrazuje na různých obrazovkách. Hodnota může být [lokalizována](localization.md). |
| DataType | 1:1 | Typ deklarace. |
| Výchozí typy nároků partnera | 0:1 | Výchozí typy deklarací partnera, které se mají použít pro zadaný protokol. Hodnotu lze přepsat v **typu PartnerClaimType** určeného v elementech **InputClaim** nebo **OutputClaim.** Tento prvek slouží k určení výchozího názvu protokolu.  |
| Maska | 0:1 | Volitelný řetězec maskovacích znaků, které lze použít při zobrazení deklarace. Například telefonní číslo 324-232-4343 může být maskováno jako XXX-XXX-4343. |
| Text uživatelské nápovědy | 0:1 | Popis typu deklarace, který může být pro uživatele užitečný k pochopení jeho účelu. Hodnota může být [lokalizována](localization.md). |
| Type userInputType | 0:1 | Typ vstupního ovládacího prvku, který by měl být uživateli k dispozici při ručním zadávání dat deklarace pro typ deklarace. Viz typy uživatelských vstupů definované dále na této stránce. |
| AdminHelpText | 0:1 | Popis typu deklarace, který může být užitečný pro správce, aby pochopili jeho účel. |
| Omezení | 0:1 | Omezení hodnoty pro toto tvrzení, například regulární výraz (Regex) nebo seznam přijatelných hodnot. Hodnota může být [lokalizována](localization.md). |
PredikateValidationReference| 0:1 | Odkaz na **predikateValidationsInput** element. **PredikateValidationReference** prvky umožňují provést proces ověření zajistit, že pouze správně vytvořená data je zadána. Další informace naleznete [v tématu Predicáty](predicates.md). |



### <a name="datatype"></a>DataType

Element **DataType** podporuje následující hodnoty:

| Typ | Popis |
| ------- | ----------- |
|Boolean|Představuje logickou`true` ( `false`nebo ) hodnotu.|
|date| Představuje okamžik v čase, obvykle vyjádřené jako datum dne. Hodnota data se řídí úmluvou ISO 8601.|
|data a času.|Představuje okamžik v čase, obvykle vyjádřený jako datum a čas dne. Hodnota data se řídí úmluvou ISO 8601.|
|doba trvání|Představuje časový interval v letech, měsících, dnech, hodinách, minutách a sekundách. Formát je `PnYnMnDTnHnMnS`, `P` kde označuje `N` kladnou nebo pro zápornou hodnotu. `nY`je počet let následovaný literálem `Y`. `nMo`je počet měsíců následovaný literálem `Mo`. `nD`je počet dní následovaný literálem `D`. Příklady: `P21Y` představuje 21 let. `P1Y2Mo`představuje jeden rok a dva měsíce. `P1Y2Mo5D`představuje jeden rok, dva měsíce a pět dní.  `P1Y2M5DT8H5M620S`představuje jeden rok, dva měsíce, pět dní, osm hodin, pět minut a dvacet sekund.  |
|Phonenumber|Představuje telefonní číslo. |
|int| Představuje číslo mezi -2,147,483,648 a 2,147,483,647|
|long| Představuje číslo mezi -9,223,372,036,854,775,808 až 9,223,372,036,854,775,807 |
|řetězec| Představuje text jako posloupnost jednotek kódu UTF-16.|
|Stringcollection|Představuje kolekci `string`.|
|userIdentity| Představuje identitu uživatele.|
|userIdentityCollection|Představuje kolekci `userIdentity`.|

### <a name="defaultpartnerclaimtypes"></a>Výchozí typy nároků partnera

**DefaultPartnerClaimTypes** může obsahovat následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Protocol (Protokol) | 1:n | Seznam protokolů s výchozím názvem typu deklarace zabezpečení partnera. |

Element **Protocol** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Name (Název) | Ano | Název platného protokolu podporovaného Azure AD B2C. Možné hodnoty jsou: OAuth1, OAuth2, SAML2, OpenIdConnect. |
| Typ deklarace programu Partner | Ano | Název typu deklarace, který má být použit. |

V následujícím příkladu při rozhraní Identity Experience Framework spolupracuje s poskytovatelem identity SAML2 nebo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`předávající stranou aplikace, **deklarace příjmení** je mapována `family_name`na , s OpenIdConnect a OAuth2, deklarace identity je mapována na .

```XML
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

V důsledku toho token JWT vydaný Azure AD B2C, vyzařuje `family_name` místo ClaimType jméno **příjmení**.

```JSON
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Maska

Prvek **Mask** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| `Type` | Ano | Typ masky deklarace. Možné `Simple` hodnoty: `Regex`nebo . Hodnota `Simple` označuje, že jednoduchá textová maska je použita na úvodní část deklarace řetězce. Hodnota `Regex` označuje, že regulární výraz je použit pro deklaraci řetězce jako celek.  Pokud `Regex` je zadána hodnota, musí být také definován volitelný atribut s regulárním výrazem, který chcete použít. |
| `Regex` | Ne | Pokud **`Type`** je `Regex`nastavena na , zadejte regulární výraz, který chcete použít.

Následující příklad konfiguruje deklaraci **PhoneNumber** s maskou: `Simple`

```XML
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

Rozhraní Identity Experience Framework vykreslí telefonní číslo a skryje prvních šest číslic:

![Nárok na telefonní číslo zobrazený v prohlížeči s prvními šesti číslicemi maskovanými xs](./media/claimsschema/mask.png)

Následující příklad konfiguruje deklaraci **AlternateEmail** s maskou: `Regex`

```XML
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

Rozhraní Identity Experience Framework vykreslí pouze první písmeno e-mailové adresy a název e-mailové domény:

![E-mailová deklarace zobrazená v prohlížeči se znaky maskovanými hvězdičkami](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Omezení

Prvek **Restriction** může obsahovat následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Sloučit chování | Ne | Metoda použitá ke sloučení hodnot výčtu s Typem claimtype v nadřazené zásadě se stejným identifikátorem. Tento atribut použijte při přepsání deklarace zadanou v zásadách základní. Možné `Append`hodnoty: `Prepend`, `ReplaceAll`, nebo . Hodnota `Append` je kolekce dat, která by měla být připojena na konec kolekce zadané v nadřazené zásady. Hodnota `Prepend` je kolekce dat, která by měla být přidána před kolekci zadanou v nadřazené zásadě. Hodnota `ReplaceAll` je kolekce dat zadaných v nadřazené zásady, které by měly být ignorovány. |

Prvek **Restriction** obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Výčet | 1:n | Dostupné možnosti v uživatelském rozhraní pro uživatele vybrat pro deklaraci, jako je například hodnota v rozevírací nabídka. |
| Vzor | 1:1 | Regulární výraz, který chcete použít. |

#### <a name="enumeration"></a>Výčet

Prvek **Výčtdefinuje** dostupné možnosti pro uživatele, aby si vybral deklaraci v uživatelském rozhraní, například hodnotu v `CheckboxMultiSelect`rozhraní , `DropdownSingleSelect`nebo `RadioSingleSelect`. Alternativně můžete definovat a lokalizovat dostupné možnosti s [LocalizedCollections](localization.md#localizedcollections) element. Chcete-li vyhledat položku z kolekce **výčtu deklarací,** použijte transformaci deklarací [deklarací GetMappedValueFromLocalizedCollection.](string-transformations.md#getmappedvaluefromlocalizedcollection)

Prvek **Výčtobsahuje** následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Text | Ano | Řetězec zobrazení, který je zobrazen uživateli v uživatelském rozhraní pro tuto možnost. |
|Hodnota | Ano | Hodnota deklarace, která je přidružena k výběru této možnosti. |
| SelectByDefault | Ne | Označuje, zda má být tato možnost vybrána ve výchozím nastavení v ui. Možné hodnoty: True nebo False. |

Následující příklad konfiguruje deklaraci rozevíracího `New York`seznamu **měst** s výchozí hodnotou nastavenou na :

```XML
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

Rozevírací seznam měst s výchozí hodnotou nastavenou na New York:

![Ovládací prvek rozevírací vykreslování v prohlížeči a zobrazující výchozí hodnotu](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>Vzor

Prvek **Pattern** může obsahovat následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Regulární výraz | Ano | Regulární výraz, který deklarace tohoto typu musí odpovídat, aby byly platné. |
| Helptext | Ne | Chybová zpráva pro uživatele, pokud se kontrola regulárního výrazu nezdaří. |

Následující příklad konfiguruje **deklaraci e-mailu** s ověřením vstupu regulárního výrazu a textem nápovědy:

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
    </Restriction>
 </ClaimType>
```

Rozhraní Identity Experience Framework vykreslí deklaraci e-mailové adresy se vstupním ověřením formátu e-mailu:

![Textové okno zobrazující chybovou zprávu aktivovanou omezením regulárního výrazu](./media/claimsschema/pattern.png)

### <a name="userinputtype"></a>Type userInputType

Azure AD B2C podporuje různé typy uživatelských vstupů, jako je textové pole, heslo a rozevírací seznam, který lze použít při ručním zadávání dat deklarace identity pro typ deklarace. Je nutné zadat **UserInputType** při shromažďování informací od uživatele pomocí [self-tvrdil technický profil](self-asserted-technical-profile.md) a [ovládací prvky zobrazení](display-controls.md).

Typy vstupů uživatele k dispozici **prvek UserInputType:**

| Type userInputType | Podporovaný typ deklarace | Popis |
| --------- | -------- | ----------- |
|Zaškrtávací políčkoMultiSelect| `string` |Vícenásobný výběr rozevíracího pole. Hodnota deklarace je reprezentována v řetězci oddělovače čárek vybraných hodnot. |
|DatumČasový pokles | `date`, `dateTime` |Rozevírací položky pro výběr dne, měsíce a roku. |
|DropdownSingleSelect |`string` |Jeden rozevírací rámeček pro výběr. Hodnota deklarace je vybraná hodnota.|
|E-mailové pole | `string` |Vstupní pole e-mailu. |
|Odstavec | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`|Pole, které zobrazuje text pouze ve značce odstavce. |
|Heslo | `string` |Textové pole s heslem.|
|RadioSingleSelect |`string` | Kolekce přepínacích tlačítek. Hodnota deklarace je vybraná hodnota.|
|Readonly | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`| Textové pole jen pro čtení. |
|TextBox |`boolean`, `int`, `string` |Jednořádkové textové pole. |


#### <a name="textbox"></a>TextBox

Typ vstupu uživatele **TextBox** se používá k poskytnutí jednořádkového textového pole.

![Textové pole zobrazující vlastnosti zadané v typu deklarace.](./media/claimsschema/textbox.png)

```XML
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="emailbox"></a>E-mailové pole

Typ vstupu uživatele **EmailBox** se používá k poskytnutí základního vstupního pole e-mailu.

![EmailBox zobrazující vlastnosti zadané v typu deklarace.](./media/claimsschema/emailbox.png)

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

#### <a name="password"></a>Heslo

Typ vstupu **uživatele Heslo** se používá k zaznamenání hesla zadaného uživatelem.

![Použití typu deklarace s heslem](./media/claimsschema/password.png)

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

#### <a name="datetimedropdown"></a>DatumČasový pokles

Typ vstupu uživatele **DateTimeDropdown** se používá k poskytnutí sady rozevíracích řad pro výběr dne, měsíce a roku. Predikáty a PredikateValidations prvky můžete použít k řízení minimální a maximální hodnoty data. Další informace naleznete v části **Konfigurace rozsahu dat** [predikáty a predikáty validations](predicates.md).

![Použití typu deklarace s časovým časem datedown](./media/claimsschema/datetimedropdown.png)

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

Typ vstupu uživatele **RadioSingleSelect** se používá k poskytnutí kolekce přepínacích tlačítek, která umožňuje uživateli vybrat jednu možnost.

![Použití typu deklarace s jedním výběrem radiod](./media/claimsschema/radiosingleselect.png)

```XML
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

Typ vstupu uživatele **DropdownSingleSelect** se používá k poskytnutí rozevíracího pole, které umožňuje uživateli vybrat jednu možnost.

![Použití typu deklarace s jedním výběrem rozevíracího výběru](./media/claimsschema/dropdownsingleselect.png)

```XML
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="checkboxmultiselect"></a>Zaškrtávací políčkoMultiSelect

Typ uživatelského vstupu **CheckboxMultiSelect** slouží k poskytnutí kolekce zaškrtávacích políček, která uživateli umožňuje vybrat více možností.

![Použití typu deklarace s vícenásobným výběrem zaškrtávacího políčka](./media/claimsschema/checkboxmultiselect.png)

```XML
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="readonly"></a>Readonly

Typ vstupu uživatele **pouze pro čtení** se používá k poskytnutí pole jen pro čtení k zobrazení deklarace a hodnoty.

![Použití typu deklarace s jen pro čtení](./media/claimsschema/readonly.png)

```XML
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


#### <a name="paragraph"></a>Odstavec

Typ vstupu **uživatele Odstavce** se používá k zadání pole, které zobrazuje text pouze ve značce odstavce.  Například &lt;p&gt;&lt;text&gt;/p . **Odstavec** uživatele `OutputClaim` vstupní typ self-tvrdil technický profil, musí nastavit `Required` atribut `false` (výchozí).

![Použití typu deklarace s odstavcem](./media/claimsschema/paragraph.png)

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
