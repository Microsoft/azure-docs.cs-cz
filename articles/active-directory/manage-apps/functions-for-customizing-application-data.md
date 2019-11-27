---
title: Zápis výrazů pro mapování atributů v Azure AD
description: Další informace o použití mapování výrazů má být transformován hodnoty atributů přijatelný formát během automatického zřizování objektů aplikace SaaS ve službě Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f1880a79f7fdb27b407ecb7ed1b761493fe850d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274023"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Zápis výrazů pro mapování atributů ve službě Azure Active Directory
Při konfiguraci zřizování pro aplikace SaaS, je jedním z typů mapování atributů, které můžete zadat mapování výrazu. Pro ty musíte napsat skript jako výraz, který umožňuje transformovat data uživatelů na formáty, které jsou více přijatelné pro aplikace SaaS.

## <a name="syntax-overview"></a>Přehled syntaxe
Syntaxe výrazů pro mapování atributů je připomínající Visual Basic pro funkce Applications (VBA).

* Celý výraz musí být definován jako funkce, které tvoří název, za nímž následuje argumenty v závorkách: <br>
  *Functions (`<<argument 1>>``<<argument N>>`)*
* Může vnořit do jiné funkce. Příklad: <br> *FunctionOne (FunctionTwo (`<<argument1>>`))*
* Tři různé typy argumentů můžete předat do funkce:
  
  1. Atributy, které musí být uzavřeny do hranatých závorek. Příklad: [attributeName]
  2. Řetězcové konstanty, které musí být umístěn do dvojitých uvozovek. Příklad: "USA"
  3. Další funkce. Například: FunctionOne (`<<argument1>>`, FunctionTwo (`<<argument2>>`))
* Pro řetězcové konstanty Pokud potřebujete zpětného lomítka (\) nebo uvozovky (") v řetězci, se musejí být uvozeny symbol zpětného lomítka (\). Příklad: "název společnosti: \\" contoso\\""

## <a name="list-of-functions"></a>Seznam funkcí
[Připojit](#append) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [&nbsp;&nbsp;&nbsp;](#not) &nbsp; &nbsp;[nahradit](#replace) &nbsp;&nbsp;&nbsp;&nbsp;[SelectUniqueValue](#selectuniquevalue) &nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [rozdělení](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [přepínač](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)

---
### <a name="append"></a>Připojit
**Slouží**<br> Append(Source, suffix)

**Název**<br> Vezme řetězcovou hodnotu zdroje a připojí přípona na konec.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Požadováno |Řetězec |Obvykle název atributu ze zdrojového objektu. |
| **auditování** |Požadováno |Řetězec |Řetězec, který chcete přidat do konce zdrojové hodnoty. |

---
### <a name="formatdatetime"></a>formatDateTime
**Slouží**<br> FormatDateTime (zdroj, inputFormat outputFormat.)

**Název**<br> Přebírá řetězec data z jednoho formátu a převede jej na jiný formát.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Požadováno |Řetězec |Obvykle název atributu ze zdrojového objektu. |
| **inputFormat** |Požadováno |Řetězec |Očekávaný formát zdrojové hodnoty. Podporované formáty najdete v tématu [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Požadováno |Řetězec |Formát výstupního data. |

---
### <a name="join"></a>Spojit
**Slouží**<br> Připojte se k (oddělovač, zdroj1, zdroj2,...)

**Název**<br> Join () je podobný jako Append (), s tím rozdílem, že může zkombinovat více hodnot **zdrojového** řetězce do jednoho řetězce a každá hodnota bude oddělena řetězcem **oddělovače** .

Pokud je jednou ze zdrojových hodnot atribut s více hodnotami, pak se všechny hodnoty v tomto atributu spojí dohromady, oddělené hodnotou oddělovače.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **oddělování** |Požadováno |Řetězec |Řetězec použitý k oddělení zdrojové hodnoty, když jsou zřetězeny do jednoho řetězce. Může být "" Pokud žádný oddělovač je povinný. |
| **source1 ... sourceN** |Povinné, proměnná počet pokusů |Řetězec |Hodnoty, který se má spojit dohromady řetězce. |

---
### <a name="mid"></a>Mid
**Slouží**<br> Mid (source; start, délka)

**Název**<br> Vrátí podřetězec zdrojovou hodnotou. Dílčí řetězec je řetězec, který obsahuje pouze některé znaky z zdrojový řetězec.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Požadováno |Řetězec |Obvykle název atributu. |
| **start** |Požadováno |integer |Index ve **zdrojovém** řetězci, ve kterém by měl být spuštěný dílčí řetězec První znak v řetězci budou mít index hodnotu 1, druhý znak bude mít index 2 a tak dále. |
| **časový** |Požadováno |integer |Délka podřetězce. Pokud délka končí mimo **zdrojový** řetězec, funkce vrátí podřetězec z **počátečního** indexu do konce **zdrojového** řetězce. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Slouží**<br> NormalizeDiacritics(source)

**Název**<br> Vyžaduje jeden argument řetězec. Vrátí řetězec, ale s znaky diakritická nahradí ekvivalentní-diakritická znaků. Obvykle slouží k převodu jména a příjmení obsahující diakritická znaky (znaky s diakritikou značky) do platné hodnoty, které můžete použít různé identifikátory uživatele, jako je například hlavních názvů uživatelů, názvy účtů SAM a e-mailové adresy.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Požadováno |Řetězec | Obvykle se jedná o křestní jméno nebo název atributu příjmení. |

---
### <a name="not"></a>Not
**Slouží**<br> Not(Source)

**Název**<br> Převrátí logickou hodnotu **zdroje**. Pokud je hodnota **zdroje** "*true*", vrátí "*false*". V opačném případě vrátí "*true*".

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Požadováno |Logického řetězce |Očekávané **zdrojové** hodnoty jsou "true" nebo "false". |

---
### <a name="replace"></a>Nahradit
**Slouží**<br> Nahraďte (zdroj, oldValue, regexPattern, regexGroupName, zastaralá, replacementAttributeName, šablona)

**Název**<br>
Nahradí hodnoty v řetězci. V závislosti na parametry, které poskytnou funguje jinak:

* Když jsou k dispozici **OldValue** a **replacementValue** :
  
  * Nahradí všechny výskyty **OldValue** ve **zdroji** pomocí **replacementValue** .
* Pokud jsou zadány **OldValue** a **Šablona** :
  
  * Nahradí všechny výskyty **OldValue** v **šabloně** **zdrojovou** hodnotou.
* Když jsou k dispozici **vzor Regex** a **replacementValue** :

  * Funkce použije **vzor Regex** na **zdrojový** řetězec a můžete použít názvy skupin Regex k sestavení řetězce pro **replacementValue**
* Když jsou k dispozici **vzor Regex**, **regexGroupName**, **replacementValue** :
  
  * Funkce použije **vzor Regex** na **zdrojový** řetězec a nahradí všechny hodnoty, které odpovídají **regexGroupName** , s **replacementValue**
* Když jsou k dispozici **vzor Regex**, **regexGroupName**, **replacementAttributeName** :
  
  * Pokud **zdroj** nemá žádnou hodnotu, vrátí se **zdroj** .
  * Pokud má **zdroj** hodnotu, funkce použije **vzor Regex** na **zdrojový** řetězec a nahradí všechny hodnoty odpovídající **regexGroupName** hodnotou přidruženou k **replacementAttributeName** .

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Požadováno |Řetězec |Obvykle název atributu ze **zdrojového** objektu. |
| **oldValue** |volitelná, |Řetězec |Hodnota, která má být nahrazena ve **zdroji** nebo **šabloně**. |
| **Vzor Regex** |volitelná, |Řetězec |Vzor regulárního výrazu pro hodnotu, která má být nahrazena **zdrojem**. Nebo, pokud se používá **replacementPropertyName** , vzorek pro extrakci hodnoty z **replacementPropertyName**. |
| **regexGroupName** |volitelná, |Řetězec |Název skupiny uvnitř **vzor Regex** Jenom v případě, že se používá **replacementPropertyName** , extrahujeme hodnotu této skupiny jako **replacementValue** z **replacementPropertyName**. |
| **replacementValue** |volitelná, |Řetězec |Nová hodnota nahradí starou s. |
| **replacementAttributeName** |volitelná, |Řetězec |Název atributu, který se má použít k nahrazení hodnoty |
| **vzhledu** |volitelná, |Řetězec |Když se zadá hodnota **šablony** , budeme v šabloně Hledat text **OldValue** a nahradit ho **zdrojovou** hodnotou. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Slouží**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3,...)

**Název**<br> Vyžaduje minimálně dva argumenty, které jsou definovány pomocí výrazů pravidel pro vytvoření jedinečnou hodnotu. Funkce vyhodnocuje každé pravidlo a poté zkontroluje hodnotu vygenerovat jedinečný v cílové aplikaci/adresáři. První jedinečnou hodnotu najít, bude vrácena ten. Pokud všechny hodnoty již existují v cíli, položka bude získat mezi a důvod získá protokolovat v protokolech auditu. Neexistuje žádná horní mez počtu argumentů, které mohou být k dispozici.

> [!NOTE]
> - Toto je funkce nejvyšší úrovně, nemohou být vnořeny.
> - Tuto funkci nelze použít pro atributy, které mají odpovídající prioritu.  
> - Tato funkce je určená jenom pro vytvoření položky. Při použití s atributem nastavte vlastnost **použít mapování** na **pouze při vytváření objektu**.
> - Tato funkce je momentálně podporovaná jenom pro zřizování uživatelů z Workday do služby Active Directory. Nedá se použít s jinými zřizovacími aplikacemi. 


**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **uniqueValueRule1 ... uniqueValueRuleN** |Minimálně 2 jsou povinné, ne horní mez |Řetězec | Seznam pravidel generování jedinečných hodnot, které se mají vyhodnotit |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Slouží**<br> SingleAppRoleAssignment([appRoleAssignments])

**Název**<br> Vrátí jeden appRoleAssignment ze seznamu všech appRoleAssignments přiřazených uživateli pro danou aplikaci. Tato funkce je nutná k převedení objektu appRoleAssignments na jeden řetězec názvu role. Doporučujeme, abyste zajistili, že se jednomu uživateli přiřadí pouze jedna appRoleAssignment a pokud je přiřazeno více rolí, vrácený řetězec role nemusí být předvídatelný. 

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **AppRoleAssignments** |Požadováno |Řetězec |objekt **[appRoleAssignments]** . |

---
### <a name="split"></a>Rozdělení
**Slouží**<br> Split (Source, oddělovač)

**Název**<br> Rozdělí řetězec na pole s více hodnotami pomocí zadaného oddělovače.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Požadováno |Řetězec |**zdrojová** hodnota, která se má aktualizovat |
| **oddělovač** |Požadováno |Řetězec |Určuje znak, který bude použit k rozdělení řetězce (například: ","). |

---
### <a name="stripspaces"></a>StripSpaces
**Slouží**<br> StripSpaces(source)

**Název**<br> Odebere všechny mezery ("") znaků z řetězce zdroje.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Požadováno |Řetězec |**zdrojová** hodnota, která se má aktualizovat |

---
### <a name="switch"></a>Přepínač
**Slouží**<br> Switch (zdroj, výchozí hodnota, key1, value1, key2, value2,...)

**Název**<br> Když hodnota **zdroje** odpovídá **klíči**, vrátí **hodnotu** pro tento **klíč**. Pokud **zdrojová** hodnota neodpovídá žádným klíčům, vrátí hodnotu **DefaultValue**.  Parametry **klíče** a **hodnoty** se musí vždycky nacházet ve dvojicích. Funkce očekává vždy sudý počet parametrů.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Požadováno |Řetězec |**Zdrojová** hodnota, která se má aktualizovat |
| **Hodnot** |volitelná, |Řetězec |Výchozí hodnota má být použit při zdroj neodpovídá žádné klíče. Může být prázdný řetězec (""). |
| **key** |Požadováno |Řetězec |**Klíč** pro porovnání **zdrojové** hodnoty s. |
| **value** |Požadováno |Řetězec |Nahrazující hodnota pro **zdroj** , který odpovídá klíči. |

---
### <a name="tolower"></a>ToLower
**Slouží**<br> ToLower (zdroj, jazyková verze)

**Název**<br> Převezme hodnotu *zdrojového* řetězce a převede ji na malý případ pomocí pravidel jazykové verze, které jsou určeny. Pokud nejsou zadány žádné informace o *jazykové verzi* , pak použije invariantní jazykovou verzi.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Požadováno |Řetězec |Obvykle název atributu ze zdrojového objektu |
| **jazykových** |volitelná, |Řetězec |Formát pro název jazykové verze založený na RFC 4646 je *languagecode2-Country/regioncode2*, kde *languagecode2* je kód jazyka dvou písmen a *země/regioncode2* je kód subjazykové verze se dvěma písmeny. Mezi příklady patří ja-JP pro japonštinu (Japonsko) a EN-US pro angličtinu (USA). V případech, kdy kód jazyka se dvěma písmeny není k dispozici, je použit kód o třech písmenech odvozený z ISO 639-2.|

---
### <a name="toupper"></a>ToUpper
**Slouží**<br> ToUpper (zdroj, jazyková verze)

**Název**<br> Převezme hodnotu *zdrojového* řetězce a převede ji na velká písmena pomocí pravidel jazykové verze, které jsou určeny. Pokud nejsou zadány žádné informace o *jazykové verzi* , pak použije invariantní jazykovou verzi.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Požadováno |Řetězec |Obvykle název atributu ze zdrojového objektu. |
| **jazykových** |volitelná, |Řetězec |Formát pro název jazykové verze založený na RFC 4646 je *languagecode2-Country/regioncode2*, kde *languagecode2* je kód jazyka dvou písmen a *země/regioncode2* je kód subjazykové verze se dvěma písmeny. Mezi příklady patří ja-JP pro japonštinu (Japonsko) a EN-US pro angličtinu (USA). V případech, kdy kód jazyka se dvěma písmeny není k dispozici, je použit kód o třech písmenech odvozený z ISO 639-2.|

## <a name="examples"></a>Příklady
### <a name="strip-known-domain-name"></a>Název domény známý pruhu
Je potřeba odstranit název domény známý z e-mailu uživatele k získání uživatelského jména. <br>
Například pokud je doména "contoso.com", pak můžete použít následující výraz:

**Vyjádření** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Vzorový vstup/výstup:** <br>

* **Vstup** (mail): "john.doe@contoso.com"
* **Výstup**: Jan. Chvojková

### <a name="append-constant-suffix-to-user-name"></a>Připojit konstantní příponu k uživatelské jméno
Pokud používáte Sandboxu služby Salesforce, můžete potřebovat přidat další přípony pro všechny své uživatelské jméno před jejich synchronizaci.

**Vyjádření** <br>
`Append([userPrincipalName], ".test")`

**Vzorový vstup/výstup:** <br>

* **Vstup**: (userPrincipalName): "John.Doe@contoso.com"
* **Výstup**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Vytvořte alias uživatele tím, že spojováním části křestní jméno a příjmení
Budete muset vygenerovat uživatele alias provedením první 3 písmena křestní jméno uživatele a prvních 5 písmena příjmení uživatele.

**Vyjádření** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Vzorový vstup/výstup:** <br>

* **Vstup** (křestní jméno): "Jan"
* **Vstup** (příjmení): "Chvojková"
* **Výstup**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Odebrat znaky s diakritikou v řetězci
Je třeba nahradit znaků obsahující diakritická znaménka s ekvivalentní znaků, které neobsahují slovo značky zvýraznění.

**Vyjádření** <br>
NormalizeDiacritics([givenName])

**Vzorový vstup/výstup:** <br>

* **Vstup** (křestní jméno): "Zoë"
* **Výstup**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Rozdělit řetězec do pole s více hodnotami
Musíte vzít seznam řetězců oddělených čárkami a rozdělit je do pole, které se dá zapojit do vícehodnotového atributu, jako je atribut PermissionSet služby Salesforce. V tomto příkladu se v extensionAttribute5 ve službě Azure AD nastavil seznam sad oprávnění.

**Vyjádření** <br>
Split ([extensionAttribute5]; ";")

**Vzorový vstup/výstup:** <br>

* **Vstup** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **Výstup**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Výstupní data jako řetězec v určitém formátu
Chcete odesílat data do aplikace SaaS v určitém formátu. <br>
Je třeba k formátování kalendářních dat pro ServiceNow.

**Vyjádření** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Vzorový vstup/výstup:**

* **Vstup** (extensionAttribute1): "20150123105347.1 z"
* **Výstup**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Nahraďte hodnotu podle předdefinovanou sadu možností

Budete muset definovat časové pásmo uživatele na základě kódu stavu uložené ve službě Azure AD. <br>
Pokud kód stavu neodpovídá žádné z předdefinovaných možností, použijte výchozí hodnotu "Austrálie/Sydney".

**Vyjádření** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Vzorový vstup/výstup:**

* **Vstup** (stav): "QLD"
* **Výstup**: "Austrálie/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Nahrazení znaků pomocí regulárního výrazu
Je nutné najít znaky, které odpovídají hodnotě regulárního výrazu, a odebrat je.

**Vyjádření** <br>

Replace ([mailNickname];; "[a-zA-Z_] *",, "",,)

**Vzorový vstup/výstup:**

* **Vstup** (mailnickname: "john_doe72"
* **Výstup**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Převést generovanou hodnotu userPrincipalName (UPN) na malá písmena
V následujícím příkladu je hodnota hlavního názvu uživatele generována zřetězením zdrojových polí PreferredFirstName a PreferredLastName a funkce ToLower funguje na vygenerovaném řetězci pro převod všech znaků na malá písmena. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Vzorový vstup/výstup:**

* **Vstup** (PreferredFirstName): "Jan"
* **Vstup** (PreferredLastName): "Smith"
* **Výstup**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generovat jedinečnou hodnotu pro atribut userPrincipalName (UPN)
Založené na uživatele křestní jméno, křestní jméno a příjmení, je potřeba vygenerovat hodnotu pro atribut hlavního názvu uživatele a vyhledat jeho jedinečnosti v adresáři cílového AD před přiřazením hodnoty pro atribut hlavního názvu uživatele.

**Vyjádření** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Vzorový vstup/výstup:**

* **Vstup** (PreferredFirstName): "Jan"
* **Vstup** (PreferredLastName): "Smith"
* **Výstup**: "John.Smith@contoso.com", pokud hodnota John.Smith@contoso.com UPN v adresáři ještě neexistuje
* **Výstup**: "J.Smith@contoso.com", pokud v adresáři již existuje hodnota John.Smith@contoso.com hlavního názvu uživatele (UPN).
* **Výstup**: "Jo.Smith@contoso.com", pokud výše uvedené dvě hodnoty UPN již v adresáři existují

## <a name="related-articles"></a>Související články
* [Automatizace zřizování a rušení zřizování uživatelů pro aplikace SaaS](user-provisioning.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](customize-application-attributes.md)
* [Filtry oborů pro zřizování uživatelů](define-conditional-rules-for-provisioning-user-accounts.md)
* [Zapnutí automatického zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací pomocí SCIM](use-scim-to-provision-users-and-groups.md)
* [Oznámení zřizování účtů](user-provisioning.md)
* [Seznam kurzů, jak integrovat aplikace SaaS](../saas-apps/tutorial-list.md)
