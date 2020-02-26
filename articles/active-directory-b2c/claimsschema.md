---
title: ClaimsSchema-Azure Active Directory B2C | Microsoft Docs
description: Zadejte element ClaimsSchema vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2861b882d9b4c00a1c4db87b2dd49d49dfeb53a6
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77581102"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **ClaimsSchema** definuje typy deklarací identity, na které se dá odkazovat v rámci zásad. Schéma deklarací identity je místo, kde deklarujete deklarace identity. Deklarace identity může být křestní jméno, příjmení, zobrazovaný název, telefonní číslo a další. Element ClaimsSchema obsahuje seznam elementů **ClaimType** . Element **ClaimType** obsahuje atribut **ID** , což je název deklarace identity.

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

## <a name="claimtype"></a>ClaimType

Element **ClaimType** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Identifikátor, který se používá pro typ deklarace. Ostatní elementy můžou tento identifikátor v zásadách použít. |

Element **ClaimType** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | Název, který se zobrazí uživatelům na různých obrazovkách. Hodnota může být [lokalizována](localization.md). |
| DataType | 1:1 | Typ deklarace identity. |
| DefaultPartnerClaimTypes | 0:1 | Výchozí typy deklarací partnerů, které se mají použít pro zadaný protokol. Hodnota může být přepsána v **PartnerClaimType** určeném v elementech **InputClaim** nebo **OutputClaim** . Tento prvek slouží k určení výchozího názvu protokolu.  |
| Zrušit | 0:1 | Volitelné řetězce maskování znaků, které lze použít při zobrazení deklarace identity. Například telefonní číslo 324-232-4343 lze maskovat jako XXX-XXX-4343. |
| UserHelpText | 0:1 | Popis typu deklarace, který může být užitečný pro uživatele, aby porozuměl jeho účelu. Hodnota může být [lokalizována](localization.md). |
| UserInputType | 0:1 | Typ vstupního ovládacího prvku, který má být uživateli k dispozici, když ručně zadáte data deklarace pro daný typ deklarace identity. Podívejte se na typy vstupu uživatele definované dále na této stránce. |
| Omezení | 0:1 | Omezení hodnoty pro tuto deklaraci identity, například regulární výraz (Regex) nebo seznam přijatelných hodnot. Hodnota může být [lokalizována](localization.md). |
PredicateValidationReference| 0:1 | Odkaz na element **PredicateValidationsInput** . Prvky **PredicateValidationReference** umožňují provést proces ověření, aby bylo zajištěno, že budou zadána pouze správně vytvořená data. Další informace najdete v tématu [predikáty](predicates.md). |

### <a name="datatype"></a>DataType

Element **DataType** podporuje následující hodnoty:

| Typ | Popis |
| ------- | ----------- | 
|Boolean|Představuje logickou hodnotu (`true` nebo `false`).|
|date| Představuje okamžitý čas, obvykle vyjádřený jako datum dne. Hodnota data dodržuje konvenci ISO 8601.|
|Datum a čas|Představuje okamžitý čas, obvykle vyjádřený jako datum a denní dobu. Hodnota data dodržuje konvenci ISO 8601.|
|duration|Představuje časový interval v letech, měsících, dnech, hodinách, minutách a sekundách. Formát je `PnYnMnDTnHnMnS`, kde `P` označuje kladný nebo `N` pro zápornou hodnotu. `nY` je počet roků následovaný literálem `Y`. `nMo` je počet měsíců následovaný literálem `Mo`. `nD` je počet dní následovaný literálem `D`. Příklady: `P21Y` představuje 21 let. `P1Y2Mo` představuje jeden rok a dva měsíce. `P1Y2Mo5D` představuje jeden rok, dva měsíce a pět dní.  `P1Y2M5DT8H5M620S` představuje jeden rok, dva měsíce, pět dní, osm hodin, pět minut a dvacet sekund.  |
|phoneNumber|Představuje telefonní číslo. |
|int| Představuje číslo mezi-2 147 483 648 a 2 147 483 647.|
|long| Představuje číslo mezi-9223372036854775808 a 9 223 372 036 854 775 807. |
|řetězec| Představuje text jako posloupnost jednotek kódu UTF-16.|
|stringCollection|Představuje kolekci `string`.|
|userIdentity| Představuje identitu uživatele.|
|userIdentityCollection|Představuje kolekci `userIdentity`.|

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

**DefaultPartnerClaimTypes** může obsahovat následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Protocol (Protokol) | 1: n | Seznam protokolů s výchozím názvem typu deklarace identity partnera. |

Element **Protocol** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Název | Ano | Název platného protokolu, který podporuje Azure AD B2C. Možné hodnoty jsou: OAuth1, OAuth2, typu Saml2, OpenIdConnect. |
| PartnerClaimType | Ano | Název typu deklarace, který se má použít |

Když v následujícím příkladu architektura prostředí identit komunikuje s poskytovatelem identity typu Saml2 nebo s aplikací předávající strany, **je tato deklarace identity** namapovaná na `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`s OpenIdConnect a OAuth2 je tato deklarace namapovaná na `family_name`.

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

Výsledkem je, že token JWT vystavil Azure AD B2C, vygeneruje `family_name` **namísto názvu deklarace**identity.

```JSON
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Zrušit

Element **Maske** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| `Type` | Ano | Typ masky deklarace identity. Možné hodnoty: `Simple` nebo `Regex`. Hodnota `Simple` označuje, že se pro úvodní část deklarace řetězce používá jednoduchá textová maska. Hodnota `Regex` označuje, že regulární výraz se aplikuje na deklaraci řetězce jako celek.  Pokud je zadána hodnota `Regex`, musí být také definován volitelný atribut s regulárním výrazem, který má být použit. |
| `Regex` | Ne | Pokud je **`Type`** nastavené na `Regex`, zadejte regulární výraz, který se má použít.

Následující příklad konfiguruje deklaraci **PhoneNumber** s `Simple`ovou maskou:

```XML
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

Architektura prostředí identity vykreslí telefonní číslo a skryje prvních šest číslic:

![Deklarace identity telefonního čísla zobrazená v prohlížeči s prvních šesti číslicemi maskovánými x](./media/claimsschema/mask.png)

Následující příklad nakonfiguruje deklaraci **AlternateEmail** s maskou `Regex`:

```XML
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

Architektura prostředí identity vykresluje jenom první písmeno e-mailové adresy a název e-mailové domény:

![Deklarace e-mailu zobrazená v prohlížeči se znaky maskovánými hvězdičkami](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Omezení

Element **omezení** může obsahovat následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| MergeBehavior | Ne | Metoda použitá ke sloučení hodnot výčtu se třídou ClaimType v nadřazené zásadě se stejným identifikátorem. Tento atribut použijte, pokud přepíšete deklaraci identity zadanou v základní zásadě. Možné hodnoty: `Append`, `Prepend`nebo `ReplaceAll`. Hodnota `Append` je kolekce dat, která by se měla připojit na konec kolekce zadané v nadřazené zásadě. Hodnota `Prepend` je kolekce dat, která by se měla přidat před kolekce zadané v nadřazené zásadě. Hodnota `ReplaceAll` je kolekce dat zadaných v nadřazené zásadě, která by se měla ignorovat. |

Element **omezení** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Výčet | 1: n | Dostupné možnosti v uživatelském rozhraní pro uživatele, kteří mají vybrat pro deklaraci identity, například hodnotu v rozevíracím seznamu. |
| Vzor | 1:1 | Regulární výraz, který má být použit. |

#### <a name="enumeration"></a>Výčet

Prvek **výčtu** definuje dostupné možnosti pro uživatele k výběru deklarace v uživatelském rozhraní, jako je například hodnota v `CheckboxMultiSelect`, `DropdownSingleSelect`nebo `RadioSingleSelect`. Alternativně můžete definovat a lokalizovat dostupné možnosti pomocí elementu [LocalizedCollections](localization.md#localizedcollections) . Chcete-li vyhledat položku z kolekce **výčtu** deklarací identity, použijte transformaci deklarací [GetMappedValueFromLocalizedCollection](string-transformations.md#getmappedvaluefromlocalizedcollection) .

Prvek **výčtu** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Text | Ano | Zobrazovaný řetězec, který je zobrazen uživateli v uživatelském rozhraní pro tuto možnost. |
|Hodnota | Ano | Hodnota deklarace identity, která je přidružená k výběru této možnosti. |
| SelectByDefault | Ne | Určuje, zda má být tato možnost ve výchozím nastavení vybrána v uživatelském rozhraní. Možné hodnoty: true nebo false. |

Následující příklad konfiguruje deklaraci identity rozevíracího seznamu **měst** s výchozí hodnotou nastavenou na `New York`:

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

![Ovládací prvek DropDown vykreslený v prohlížeči a zobrazení výchozí hodnoty](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>Vzor

Prvek **vzoru** může obsahovat následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| RegularExpression | Ano | Regulární výraz, který deklarace identity tohoto typu musí splňovat, aby byl platný. |
| HelpText | Ne | Chybová zpráva pro uživatele, pokud se chyba kontroly regulárního výrazu nezdařila. |

V následujícím příkladu je nakonfiguruje deklarace **e-mailu** pomocí ověřování vstupu regulárního výrazu a textu v nápovědě:

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

Architektura prostředí identity vykreslí deklaraci identity e-mailové adresy pomocí ověřování vstupu formátu e-mailu:

![Textové pole zobrazující chybovou zprávu aktivovaný omezením Regex](./media/claimsschema/pattern.png)

### <a name="userinputtype"></a>UserInputType

Azure AD B2C podporuje různé typy vstupu uživatele, jako je textové pole, heslo a rozevírací seznam, které se dají použít, když ručně zadáte data deklarace identity pro daný typ deklarace identity. Při shromažďování informací od uživatele musíte zadat **UserInputType** pomocí technického profilu a [ovládacích prvků zobrazení](display-controls.md)s [vlastním uplatněním](self-asserted-technical-profile.md) .

K dispozici jsou typy vstupu uživatele dostupné pro **UserInputType** prvky:

| UserInputType | Podporovaný deklarace ClaimType | Popis |
| --------- | -------- | ----------- |
|CheckboxMultiSelect| `string` |Rozevírací seznam pro vícenásobný výběr. Hodnota deklarace je reprezentovaná v řetězci oddělovače (čárky) vybraných hodnot. |
|DateTimeDropdown | `date`, `dateTime` |Rozevírací seznam pro výběr dne, měsíce a roku. |
|DropdownSingleSelect |`string` |Rozevírací seznam pro jedno výběr. Hodnota deklarace je vybraná hodnota.|
|EmailBox | `string` |Vstupní pole e-mailu |
|Odstavec | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`|Pole, které zobrazuje text pouze v označení odstavce. |
|Heslo | `string` |Textové pole pro heslo|
|RadioSingleSelect |`string` | Kolekce přepínacích tlačítek. Hodnota deklarace je vybraná hodnota.|
|ReadOnly | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`| Textové pole určené jen pro čtení. |
|TextBox |`boolean`, `int`, `string` |Textové pole s jedním řádkem. |


#### <a name="textbox"></a>TextBox

Textové **pole pro zadání uživatelského rozhraní** slouží k zadání jednořádkového textového pole.

![Textové pole zobrazující vlastnosti zadané v typu deklarace](./media/claimsschema/textbox.png)

```XML
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="emailbox"></a>EmailBox

Typ vstupu uživatele **EmailBox** se používá k poskytnutí základního pole pro zadání e-mailu.

![EmailBox zobrazující vlastnosti zadané v typu deklarace identity](./media/claimsschema/emailbox.png)

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

Typ vstupu uživatele **hesla** se používá k zaznamenání hesla zadaného uživatelem.

![Použití typu deklarace identity s heslem](./media/claimsschema/password.png)

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

#### <a name="datetimedropdown"></a>DateTimeDropdown

Typ vstupu uživatele **DateTimeDropdown** slouží k poskytnutí sady rozevíracích seznamu pro výběr dne, měsíce a roku. Pomocí predikátů a elementů PredicateValidations můžete řídit minimální a maximální hodnoty data. Další informace najdete v části predikáty **Konfigurace rozsahu kalendářních dat** v [predikátech a PredicateValidations](predicates.md).

![Použití typu deklarace identity s datetimedropdown](./media/claimsschema/datetimedropdown.png)

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

Typ vstupu uživatele **RadioSingleSelect** slouží k poskytnutí kolekce přepínačů, které umožňují uživateli vybrat jednu možnost.

![Použití typu deklarace identity s radiodsingleselect](./media/claimsschema/radiosingleselect.png)

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

Typ vstupu uživatele **DropdownSingleSelect** slouží k poskytnutí rozevíracího seznamu, který uživateli umožňuje vybrat jednu možnost.

![Použití typu deklarace identity s dropdownsingleselect](./media/claimsschema/dropdownsingleselect.png)

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

#### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

Typ vstupu uživatele **CheckboxMultiSelect** slouží k poskytnutí kolekce zaškrtávacích políček, která uživateli umožní vybrat více možností.

![Použití typu deklarace identity s checkboxmultiselect](./media/claimsschema/checkboxmultiselect.png)

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

#### <a name="readonly"></a>ReadOnly

Typ vstupu uživatele **jen pro čtení** se používá k zadání pole jen pro čtení, které zobrazí deklaraci identity a hodnotu.

![Použití typu deklarace identity s jen pro čtení](./media/claimsschema/readonly.png)

```XML
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


#### <a name="paragraph"></a>Odstavec

**Odstavcový** typ vstupu uživatele slouží k poskytnutí pole, které zobrazuje text pouze v označení odstavce.  Například &lt;p&gt;text&lt;/p&gt;. Typ uživatelského vstupu v **odstavci** `OutputClaim` technického profilu s vlastním uplatněním, musí nastavit atribut `Required` `false` (výchozí).

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
