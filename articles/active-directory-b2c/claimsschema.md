---
title: ClaimsSchema – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zadejte element ClaimsSchema vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6c41890922e2235190d8844a573522846b42c779
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434496"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**ClaimsSchema** element definuje typy deklarací identity, které může být odkazováno jako součást této zásady. Schéma deklarací identity je na místě, kde je deklarovat vaše deklarací identity. Deklarace může být křestní jméno, poslední název, zobrazovaný název, telefonní číslo a další. ClaimsSchema prvek obsahuje seznam **typu deklarace identity** elementy. **Typu deklarace identity** obsahuje element **Id** atributů, což je název deklarace identity. 

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

## <a name="claimtype"></a>Typ ClaimType

**Typu deklarace identity** prvek obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Identifikátor, který se používá pro typ deklarace identity. Další prvky můžete pomocí tohoto identifikátoru v zásadách. |

**Typu deklarace identity** prvek obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Zobrazovaný název | 0:1 | Název, který se zobrazí uživatelům na různých obrazovkách. Hodnota může být [lokalizované](localization.md). |
| DataType | 0:1 | Typ deklarace identity. Datové typy logická hodnota, date, dateTime, int, long, string, třída stringCollection, je možné alternativeSecurityIdCollection. |
| DefaultPartnerClaimTypes | 0:1 | Výchozí partnerské deklarace typů, který chcete použít pro zadaný protokol. Hodnota se dají přepsat v **PartnerClaimType** zadané v poli **InputClaim** nebo **OutputClaim** elementy. Tento element slouží k určení výchozí název pro určitý protokol.  |
| Maska | 0:1 | Volitelný řetězec maskování znaků, které mohou být použity při zobrazení deklarace identity. Například můžete jako XXX XXX-4343 maskována 324-232-4343 číslo telefonu. |
| UserHelpText | 0:1 | Popis typu deklarace identity, které vám pomůžou uživatelům pochopit jeho účel. Hodnota může být [lokalizované](localization.md). |
| UserInputType | 0:1 | Typ vstupního ovládacího prvku, který by měl být k dispozici pro uživatele, když ručně zadat data deklarace identity pro typ deklarace identity. Zobrazíte vstupní uživatelem definované později na této stránce. |
| Omezení | 0:1 | Omezení hodnoty pro tato deklarace identity, jako je například regulárních výrazů (Regex) nebo seznam přijatelných hodnot. Hodnota může být [lokalizované](localization.md). |
PredicateValidationReference| 0:1 | Odkaz na **PredicateValidationsInput** elementu. **PredicateValidationReference** prvky umožňují provádět proces ověření k zajištění, že se zadá jenom správně vytvořená data. Další informace najdete v tématu [predikáty](predicates.md). |

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

**DefaultPartnerClaimTypes** může obsahovat následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Protocol (Protokol) | 0: n | Seznam protokolů s příslušným partnerem výchozí název typu deklarace. |

**Protokol** prvek obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Název | Ano | Název platný protokol podporovaný službou Azure AD B2C. Možné hodnoty jsou: OAuth1, OAuth2, typu SAML2, OpenIdConnect, WsFed nebo WsTrust. |
| PartnerClaimType | Ano | Název typu deklarace identity se použije. |

V následujícím příkladu, při architekturu rozhraní identit interakci s zprostředkovatele identity typu SAML2 nebo aplikaci předávající strany **příjmení** deklarace identity se mapuje na `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`, OpenIdConnect a OAuth2, je deklarace identity mapovat na `family_name`.

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

V důsledku toho tokenu JWT vydaného službou Azure AD B2C, vynechává `family_name` namísto názvu typu deklarace identity **příjmení**.
 
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

**Maska** prvek obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Typ | Ano | Typ deklarace identity masky. Možné hodnoty: `Simple` nebo `Regex`. `Simple` Hodnota označuje, že maska jednoduchý text platí pro přední části deklarace řetězec. `Regex` Hodnota značí, že regulární výraz se použije k deklaraci řetězec jako celek.  Pokud `Regex` není zadána hodnota, volitelný atribut musí být také definován pomocí regulárních výrazů používat. |
| regulární výraz | Ne | Pokud **typ** je nastavena na `Regex`, zadejte regulární výraz k použití.

Naleznete příklad konfiguruje **PhoneNumber** deklarace identity s `Simple` masky:

```XML
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>  
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

Architekturu rozhraní identit vykreslí telefonní číslo při skrytí prvních šesti číslic:

![Typ s maskou pomocí deklarace.](./media/claimsschema/mask.png)

Nakonfiguruje naleznete příklad **AlternateEmail** deklarace identity s `Regex` masky:

```XML
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

Architekturu rozhraní identit vykreslí pouze první písmeno e-mailovou adresu a název domény e-mailu:

![Typ s maskou pomocí deklarace.](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Omezení

**Omezení** element může obsahovat následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| MergeBehavior | Ne | Metoda použitá ke sloučení hodnoty výčtu s ClaimType v nadřazené zásady se stejným identifikátorem. Pomocí tohoto atributu při přepsání deklarace zadané v základních zásadách. Možné hodnoty: `Append`, `Prepend`, nebo `ReplaceAll`. `Append` Hodnota je kolekce dat, která má být připojen na konec kolekce zadaná v nadřazené zásady. `Prepend` Hodnota je kolekce dat, která by se měl přidat před kolekce zadaná v nadřazené zásady. `ReplaceAll` Hodnota je kolekce dat zadaná v nadřazené zásady, které mají být ignorovány. |

**Omezení** prvek obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Výčet | 1: n | Dostupné možnosti v uživatelském rozhraní pro uživatele a vyberte deklaraci identity, jako jsou hodnoty v rozevíracího seznamu. |
| Vzor | 1:1 | Regulární výraz k použití. |

### <a name="enumeration"></a>Výčet

**Výčet** prvek obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Text | Ano | Řetězec zobrazení, která se zobrazí uživateli uživatelské rozhraní pro tuto možnost. |
|Hodnota | Ano | Hodnota deklarace identity, která souvisí s výběrem této možnosti. |
| SelectByDefault | Ne | Určuje, zda by měla být vybraná tato možnost ve výchozím nastavení v uživatelském rozhraní. Možné hodnoty: True nebo False. |

Následující příklad nastaví **Město** rozevírací seznam deklarací identity s výchozí nastavenou na `New York`:

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
Rozevírací seznam město s výchozí nastavenou na New York:

![Rozevírací seznam Město](./media/claimsschema/dropdownsingleselect.png)


### <a name="pattern"></a>Vzor

**Vzor** element může obsahovat následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Regulární výraz | Ano | Regulární výraz, aby byla platná musí odpovídat deklarace identity tohoto typu. |
| HelpText | Ne | Vzor nebo regulární výraz pro tuto deklaraci. |

Následující příklad nastaví **e-mailu** deklarace identity s regulárním výrazem vstupní ověření a text nápovědy:

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

Architekturu rozhraní identit vykreslí deklarace identity e-mailové adresy s ověření vstupu formát e-mailu:

![Typ se vzorem pomocí deklarace.](./media/claimsschema/pattern.png)

## <a name="userinputtype"></a>UserInputType

Azure AD B2C podporuje různé typy vstupu uživatele, jako je textové pole, heslo a rozevírací seznam, který se dá použít při ručně zadat data deklarace identity pro typ deklarace identity. Je nutné zadat **UserInputType** při shromažďování informací od uživatele s využitím [držitelem s prohlašovanou technický profil](self-asserted-technical-profile.md).

### <a name="textbox"></a>TextBox

**TextBox** typ vstupu uživatele, používá k poskytování jednořádkové textové pole.

![Typ s textového pole pomocí deklarace.](./media/claimsschema/textbox.png)

```XML
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

### <a name="emailbox"></a>EmailBox

**EmailBox** typ vstupu uživatele se používá k poskytnutí vstupní pole základní e-mailu.

![Typ s emailbox pomocí deklarace.](./media/claimsschema/emailbox.png)

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

### <a name="password"></a>Heslo

**Heslo** typ vstupu uživatele se používá k zaznamenání heslo zadané uživatelem.

![Typ s heslem pomocí deklarace.](./media/claimsschema/password.png)

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

### <a name="datetimedropdown"></a>DateTimeDropdown

**DateTimeDropdown** typ vstupu uživatele, používá k poskytování sadu z rozevírací nabídky vyberte den, měsíc a rok. Predikáty a PredicateValidations prvky můžete řídit data minimální a maximální hodnoty. Další informace najdete v tématu **nakonfigurovat konkrétní období** část [predikáty a PredicateValidations](predicates.md).

![Typ s datetimedropdown pomocí deklarace.](./media/claimsschema/datetimedropdown.png)

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

### <a name="radiosingleselect"></a>RadioSingleSelect

**RadioSingleSelect** typ vstupu uživatele, používá k poskytování kolekce přepínače, který umožňuje uživateli vybrat jednu možnost.

![Typ s radiodsingleselect pomocí deklarace.](./media/claimsschema/radiosingleselect.png)

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

### <a name="dropdownsingleselect"></a>DropdownSingleSelect

**DropdownSingleSelect** typ vstupu uživatele, používá k poskytování rozevíracího seznamu, který umožňuje uživateli vybrat jednu možnost.

![Typ s dropdownsingleselect pomocí deklarace.](./media/claimsschema/dropdownsingleselect.png)

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

### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

**CheckboxMultiSelect** typ vstupu uživatele, používá k poskytování kolekce zaškrtávací políčka, která umožňuje uživateli vybrat více možností.

![Typ s checkboxmultiselect pomocí deklarace.](./media/claimsschema/checkboxmultiselect.png)

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

### <a name="readonly"></a>jen pro čtení

**Jen pro čtení** typ vstupu uživatele se používá k poskytnutí pole jen pro čtení pro zobrazení hodnoty a deklarace identity.

![Typ s jen pro čtení pomocí deklarace.](./media/claimsschema/readonly.png)

```XML
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


### <a name="paragraph"></a>Odstavec

**Odstavec** typ vstupu uživatele se používá k poskytnutí pole, která zobrazí text pouze do značky odstavce. Například &lt;p&gt;text&lt;/p&gt;.

![Typ s odstavec pomocí deklarace.](./media/claimsschema/paragraph.png)

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```

Chcete-li zobrazit jeden z **výčet** hodnoty v **responseMsg** deklarace identity, použijte `GetMappedValueFromLocalizedCollection` nebo `CreateStringClaim` transformace deklarací identity. Další informace najdete v tématu [transformace deklarací identity řetězců](string-transformations.md) 
