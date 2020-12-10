---
title: Referenční informace pro zápis výrazů pro mapování atributů v Azure Active Directory
description: Naučte se používat mapování výrazů k transformaci hodnot atributů do přijatelného formátu při automatizovaném zřizování objektů aplikací SaaS v Azure Active Directory. Obsahuje seznam odkazů funkcí.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 02/05/2020
ms.author: kenwith
ms.custom: contperf-fy21q2
ms.openlocfilehash: fe18513bbfc69fb63f8b1b248b7cf02ad55b467d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027369"
---
# <a name="reference-for-writing-expressions-for-attribute-mappings-in-azure-ad"></a>Referenční informace pro zápis výrazů pro mapování atributů v Azure AD

Při konfiguraci zřizování pro aplikaci SaaS je jedním z typů mapování atributů, které lze zadat, mapování výrazu. V takovém případě musíte napsat výraz podobný skriptům, který umožňuje transformovat data uživatelů na formáty, které jsou pro aplikaci SaaS přijatelné.

## <a name="syntax-overview"></a>Přehled syntaxe

Syntaxe výrazů pro mapování atributů je Reminiscent funkcí jazyk Visual Basic for Application (VBA).

* Celý výraz musí být definován z funkcí Functions, které se skládají z názvu následovaného argumenty v závorkách: název *funkce ( `<<argument 1>>` , `<<argument N>>` )* .
* Do sebe můžete vnořovat funkce. Příklad:  *FunctionOne (FunctionTwo ( `<<argument1>>` ))*
* Do funkcí můžete předat tři různé typy argumentů:
  
  1. Atributy, které musí být uzavřeny do hranatých závorek. Příklad: [attributeName]
  2. Řetězcové konstanty, které musí být uzavřeny do dvojitých uvozovek. Například: "USA"
  3. Další funkce. Příklad: FunctionOne ( `<<argument1>>` , FunctionTwo ( `<<argument2>>` ))
* Pro řetězcové konstanty, pokud v řetězci potřebujete zpětné lomítko (\) nebo uvozovky ("), musí být uvozen znakem zpětného lomítka (\). Příklad: "název společnosti: \\ " contoso \\ ""
* Syntaxe rozlišuje velká a malá písmena, která je nutné vzít v úvahu při jejich zadávání jako řetězců ve funkci vs kopírování jejich vložení přímo z tohoto místa. 

## <a name="list-of-functions"></a>Seznam funkcí

[](#append) &nbsp; &nbsp; Připojit &nbsp; &nbsp; [](#bitand) &nbsp; &nbsp; BitAnd &nbsp; &nbsp; [](#cbool) &nbsp; &nbsp; CBool &nbsp; &nbsp; [](#coalesce) &nbsp; &nbsp; COALESCE &nbsp; &nbsp; [](#converttobase64) &nbsp; &nbsp; ConvertToBase64 &nbsp; &nbsp; [](#converttoutf8hex) &nbsp; &nbsp; ConvertToUTF8Hex &nbsp; &nbsp; [](#count) &nbsp; &nbsp; Počet &nbsp; &nbsp; [](#cstr) &nbsp; &nbsp; CStr &nbsp; &nbsp; [DateFromNum](#datefromnum) &nbsp; [](#formatdatetime) &nbsp; &nbsp; FormatDateTime &nbsp; &nbsp; [Identifikátor](#guid) &nbsp; &nbsp; GUID &nbsp; &nbsp; [](#iif) &nbsp; &nbsp; IIf &nbsp; &nbsp; [](#instr) &nbsp; &nbsp; InStr &nbsp; &nbsp; [](#isnull) &nbsp; &nbsp; IsNull &nbsp; &nbsp; [](#isnullorempty) &nbsp; &nbsp; IsNullOrEmpty &nbsp; &nbsp; K [dispozici](#ispresent) &nbsp; &nbsp; &nbsp; &nbsp; [](#isstring) &nbsp; &nbsp; Řetězec &nbsp; &nbsp; [](#item) &nbsp; &nbsp; Položka &nbsp; &nbsp; [Připojit](#join) &nbsp; &nbsp; se &nbsp; &nbsp; [](#left) &nbsp; &nbsp; Vlevo &nbsp; &nbsp; [Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp; &nbsp; &nbsp; &nbsp; [RemoveDuplicates –](#removeduplicates) &nbsp; &nbsp; &nbsp; &nbsp; [Replace](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [SelectUniqueValue](#selectuniquevalue) &nbsp; &nbsp; &nbsp; &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; [rozdělit](#split) &nbsp; &nbsp; &nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [Switch](#switch) &nbsp; &nbsp; &nbsp; &nbsp; [ToLower](#tolower) &nbsp; &nbsp; &nbsp; &nbsp; [ToUpper](#toupper) &nbsp; &nbsp; &nbsp; &nbsp; [Word](#word)

---
### <a name="append"></a>Připojit

**Funkce:** Připojit (zdroj, přípona)

**Popis:** Převezme hodnotu zdrojového řetězce a připojí příponu na konec IT.

**Ukazatelů**

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |Obvykle název atributu ze zdrojového objektu. |
| **auditování** |Vyžadováno |Řetězec |Řetězec, který chcete připojit ke konci zdrojové hodnoty. |

---
### <a name="bitand"></a>BitAnd
**Funkce:** BitAnd (hodnota1, hodnota2)

**Popis:** Tato funkce převede oba parametry do binární reprezentace a nastaví bit na:

- 0 – Pokud má jedna nebo obě z odpovídajících bitů v hodnota1 a hodnota2 hodnotu 0
- 1 – Pokud jsou obě odpovídající bity 1.

Jinými slovy, vrátí 0 ve všech případech s výjimkou toho, že odpovídající bity obou parametrů jsou 1.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Hodnota1** |Vyžadováno |num |Číselná hodnota, která by měla být AND'ed s hodnota2|
| **Argument** |Vyžadováno |num |Číselná hodnota, která má být AND'ed s hodnota1|

**Případě**
`BitAnd(&HF, &HF7)`

11110111 a 00000111 = 00000111 a `BitAnd` vrátí hodnotu 7, binární hodnota 00000111.

---
### <a name="cbool"></a>CBool
**Slouží** 
`CBool(Expression)`

**Popis:**  
 `CBool` Vrátí logickou hodnotu založenou na vyhodnoceném výrazu. Pokud je výraz vyhodnocen jako nenulová hodnota, pak `CBool` vrátí *hodnotu true*, jinak vrátí *hodnotu false*.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **expression** |Vyžadováno | expression | Libovolný platný výraz |

**Případě**
`CBool([attribute1] = [attribute2])`                                                                    
Vrátí hodnotu true, pokud mají oba atributy stejnou hodnotu.

---
### <a name="coalesce"></a>Coalesce
**Funkce:** COALESCE (source1; SOURCE2;...; defaultValue)

**Popis:** Vrátí první hodnotu zdroje, která není NULL. Pokud jsou všechny argumenty NULL a jsou přítomny hodnoty defaultValue, vrátí se hodnota defaultValue. Pokud jsou všechny argumenty NULL a vlastnost defaultValue není k dispozici, funkce coalesce vrátí hodnotu NULL.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **source1 ... sourceN** | Vyžadováno | Řetězec |Povinný, proměnlivý počet opakování. Obvykle název atributu ze zdrojového objektu. |
| **Hodnot** | Volitelné | Řetězec | Výchozí hodnota, která se má použít, pokud jsou všechny zdrojové hodnoty NULL. Může být prázdný řetězec ("").

---
### <a name="converttobase64"></a>ConvertToBase64
**Funkce:** ConvertToBase64 (zdroj)

**Popis:** Funkce ConvertToBase64 převede řetězec na řetězec Unicode base64.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |Řetězec, který má být převeden na základní 64|

**Případě**
`ConvertToBase64("Hello world!")`

Vrátí "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Funkce:** ConvertToUTF8Hex (zdroj)

**Popis:** Funkce ConvertToUTF8Hex převede řetězec na šestnáctkovou hodnotu v kódování UTF8.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |Řetězec, který se má převést na UTF8 hex|

**Případě**
`ConvertToUTF8Hex("Hello world!")`

Vrátí 48656C6C6F20776F726C6421

---
### <a name="count"></a>Počet
**Funkce:** Count (atribut)

**Popis:** Funkce Count vrátí počet prvků v vícehodnotovém atributu.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **přidělen** |Vyžadováno |– atribut |Vícehodnotový atribut, který bude obsahovat elementy počítané|

---
### <a name="cstr"></a>CStr
**Funkce:** CStr (hodnota)

**Popis:** Funkce CStr převede hodnotu na datový typ String.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **value** |Vyžadováno | Číselná, odkazová nebo logická hodnota | Může to být číselná hodnota, odkazový atribut nebo logická hodnota. |

**Případě**
`CStr([dn])`

Vrátí "CN = Jan, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Funkce:** DateFromNum (hodnota)

**Popis:** Funkce DateFromNum převede hodnotu ve formátu data AD na typ DateTime.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **value** |Vyžadováno | Datum | Datum reklamy, které má být převedeno na typ DateTime |

**Případě**
`DateFromNum([lastLogonTimestamp])`

`DateFromNum(129699324000000000)`

Vrátí hodnotu DateTime představující 1. ledna 2012 v hodnotě 11:13:00.

---
### <a name="formatdatetime"></a>FormatDateTime
**Funkce:** FormatDateTime (zdroj, inputFormat, outputFormat)

**Popis:** Vezme řetězec data z jednoho formátu a převede ho do jiného formátu.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |Obvykle název atributu ze zdrojového objektu. |
| **inputFormat** |Vyžadováno |Řetězec |Byl očekáván formát zdrojové hodnoty. Podporované formáty najdete v tématu [/dotnet/Standard/Base-Types/Custom-Date-and-Time-Format-Strings](/dotnet/standard/base-types/custom-date-and-time-format-strings). |
| **outputFormat** |Vyžadováno |Řetězec |Formát data výstupu. |

---
### <a name="guid"></a>Identifikátor GUID
**Funkce:** GUID ()

**Popis:** Identifikátor GUID funkce vygeneruje nový náhodný identifikátor GUID.

---
### <a name="iif"></a>IIF
**Funkce:** IIF (podmínka, valueIfTrue, valueIfFalse)

**Popis:** Funkce IIF vrátí jednu ze sady možných hodnot na základě zadané podmínky.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **pomocné** |Vyžadováno |Proměnná nebo výraz |Libovolná hodnota nebo výraz, který lze vyhodnotit na hodnotu true nebo false. |
| **valueIfTrue** |Vyžadováno |Proměnná nebo řetězec | Pokud je podmínka vyhodnocena jako true, vrácená hodnota. |
| **valueIfFalse** |Vyžadováno |Proměnná nebo řetězec |Pokud je podmínka vyhodnocena jako false, vrácená hodnota.|

**Případě**
`IIF([country]="USA",[country],[department])`

---
### <a name="instr"></a>InStr
**Funkce:** InStr (hodnota1, hodnota2, Start, compareType)

**Popis:** Funkce InStr vyhledá první výskyt podřetězce v řetězci.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Hodnota1** |Vyžadováno |Řetězec |Řetězec, který má být prohledán |
| **Argument** |Vyžadováno |Řetězec |Řetězec, který se má najít |
| **Čína** |Volitelné |Integer |Počáteční pozice pro vyhledání podřetězce|
| **compareType** |Volitelné |Výčet |Může být vbTextCompare nebo vbBinaryCompare |

**Případě**
`InStr("The quick brown fox","quick")`

Vyhodnotí na 5.

`InStr("repEated","e",3,vbBinaryCompare)`

Vyhodnotí na 7

---
### <a name="isnull"></a>IsNull
**Funkce:** IsNull (výraz)

**Popis:** Pokud je výraz vyhodnocen jako null, funkce IsNull vrátí hodnotu true. U atributu je hodnota null vyjádřena nepřítomností atributu.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **expression** |Vyžadováno |expression |Výraz, který se má vyhodnotit |

**Případě**
`IsNull([displayName])`

Vrátí hodnotu true, pokud atribut není přítomen.

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Funkce:** IsNullOrEmpty (výraz)

**Popis:** Pokud má výraz hodnotu null nebo je prázdný řetězec, vrátí funkce IsNullOrEmpty hodnotu true. U atributu by se to mělo vyhodnotit na hodnotu true, pokud atribut chybí nebo je přítomen, ale je to prázdný řetězec.
Inverzní část této funkce je pojmenována jako.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **expression** |Vyžadováno |expression |Výraz, který se má vyhodnotit |

**Případě**
`IsNullOrEmpty([displayName])`

Vrátí hodnotu true, pokud atribut není přítomen, nebo je prázdný řetězec.

---
### <a name="ispresent"></a>K dispozici
**Funkce:** K dispozici (výraz)

**Popis:** Pokud se výraz vyhodnotí jako řetězec, který není null a není prázdný, vrátí funkce vracející hodnotu true. Inverzní funkce k této funkci má název IsNullOrEmpty.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **expression** |Vyžadováno |expression |Výraz, který se má vyhodnotit |

**Případě**
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="isstring"></a>Řetězec
**Funkce:** Řetězec (výraz)

**Popis:** Pokud je možné výraz vyhodnotit na typ řetězce, pak je funkce typu String vyhodnocena jako true.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **expression** |Vyžadováno |expression |Výraz, který se má vyhodnotit |

---
### <a name="item"></a>Položka
**Funkce:** Item (atribut; index)

**Popis:** Funkce Item vrátí jednu položku z vícehodnotového řetězce nebo atributu.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **přidělen** |Vyžadováno |Atribut |Vícehodnotový atribut, který má být prohledán |
| **indexovacím** |Vyžadováno |Integer | Index položky v řetězci s více hodnotami|

**Případě**
`Item([proxyAddresses], 1)`

---
### <a name="join"></a>Spojit
**Funkce:** Join (oddělovač, source1, SOURCE2,...)

**Popis:** Join () je podobný jako Append (), s tím rozdílem, že může zkombinovat více hodnot **zdrojového** řetězce do jednoho řetězce a každá hodnota bude oddělena řetězcem **oddělovače** .

Pokud je jednou ze zdrojových hodnot atribut s více hodnotami, pak se všechny hodnoty v tomto atributu spojí dohromady, oddělené hodnotou oddělovače.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **oddělování** |Vyžadováno |Řetězec |Řetězec, který se používá k oddělení zdrojových hodnot při zřetězení do jednoho řetězce. Může být "", pokud není vyžadován oddělovač. |
| **source1 ... sourceN** |Povinný, proměnlivý počet pokusů |Řetězec |Řetězcové hodnoty, které se mají spojit dohromady. |

---
### <a name="left"></a>Left
**Funkce:** Left (řetězec; NumChars)

**Popis:** Funkce Left vrátí zadaný počet znaků nalevo od řetězce. Pokud numChars = 0, vrátí prázdný řetězec.
Pokud numChars < 0, vrátí se vstupní řetězec.
Pokud má řetězec hodnotu null, vrátí prázdný řetězec.
Pokud řetězec obsahuje méně znaků než číslo zadané v numChars, vrátí se řetězec shodný s řetězcem (tj. obsahující všechny znaky v parametru 1).

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Řetězec** |Vyžadováno |Atribut | Řetězec, ze kterého se mají vracet znaky |
| **NumChars** |Vyžadováno |Integer | Číslo určující počet znaků, které mají být vráceny od začátku (vlevo) řetězce|

**Případě**
`Left("John Doe", 3)`

Vrátí "Joh".

---
### <a name="mid"></a>Mid
**Funkce:** Mid (zdroj, začátek, délka)

**Popis:** Vrátí podřetězec zdrojové hodnoty. Podřetězec je řetězec, který obsahuje pouze některé znaky ze zdrojového řetězce.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |Obvykle název atributu. |
| **Čína** |Vyžadováno |integer |Index ve **zdrojovém** řetězci, ve kterém by měl být spuštěný dílčí řetězec První znak v řetězci bude mít index 1, druhý znak bude mít index 2 atd. |
| **length** |Vyžadováno |integer |Délka podřetězce Pokud délka končí mimo **zdrojový** řetězec, funkce vrátí podřetězec z **počátečního** indexu do konce **zdrojového** řetězce. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funkce:** NormalizeDiacritics (zdroj)

**Popis:** Vyžaduje jeden řetězcový argument. Vrátí řetězec, ale se všemi diakritikami nahrazenými ekvivalentními nediakritikou znaků. Obvykle slouží k převodu křestních jmen a příjmení obsahujících diakritická znaménka (značky akcentů) na platné hodnoty, které lze použít v různých uživatelských identifikátorech, jako jsou hlavní názvy uživatelů, názvy účtů SAM a e-mailové adresy.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec | Obvykle se jedná o křestní jméno nebo název atributu příjmení. |

---
### <a name="not"></a>Not
**Funkce:** Nepoužívá se (zdroj)

**Popis:** Převrátí logickou hodnotu **zdroje**. Pokud je **zdrojová** hodnota true, vrátí hodnotu false. V opačném případě vrátí hodnotu true.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Logický řetězec |Očekávané **zdrojové** hodnoty jsou "true" nebo "false". |

---
### <a name="numfromdate"></a>NumFromDate
**Funkce:** NumFromDate (hodnota)

**Popis:** Funkce NumFromDate převede hodnotu DateTime na formát služby Active Directory, který je vyžadován pro nastavení atributů jako [accountExpires](/windows/win32/adschema/a-accountexpires). Pomocí této funkce můžete převést hodnoty DateTime obdržené z cloudových aplikací pro HR, jako je třeba Workday a SuccessFactors, do jejich ekvivalentu reklamy. 

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **value** |Vyžadováno | Řetězec | Řetězec data a času v podporovaném formátu. Podporované formáty naleznete v tématu https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx . |

**Příklad:**
* Příklad pracovního dne předpokládá, že chcete namapovat atribut *ContractEndDate* z Workday, který je ve formátu *2020-12-31-08:00* až *accountExpires* pole ve službě AD, tady je postup, jak můžete tuto funkci použít, a změnit posun časového pásma tak, aby odpovídal vašemu národnímu prostředí. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* SuccessFactors příklad předpokládá, že chcete mapovat atribut *EndDate* z SuccessFactors, který je ve formátu *M/d/rrrr hh: mm: SS TT* na *accountExpires* pole ve službě AD, tady je postup, jak můžete tuto funkci použít a změnit posun časového pásma tak, aby odpovídal vašemu národnímu prostředí.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates –
**Funkce:** RemoveDuplicates – (atribut)

**Popis:** Funkce RemoveDuplicates – přebírá řetězec s více hodnotami a ověří, zda jsou všechny hodnoty jedinečné.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **přidělen** |Vyžadováno |Vícehodnotový atribut |Vícehodnotový atribut, který bude mít odebrané duplicity|

**Příklad:** 
 `RemoveDuplicates([proxyAddresses])` Vrátí upravený atribut proxyAddress, ve kterém byly odstraněny všechny duplicitní hodnoty.

---
### <a name="replace"></a>Nahrazení
**Funkce:** Replace (Source, oldValue, vzor Regex, regexGroupName, replacementValue, replacementAttributeName, Template)

**Popis:** Nahradí hodnoty v rámci řetězce způsobem, který rozlišuje velká a malá písmena. Funkce se chová odlišně v závislosti na zadaných parametrech:

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

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |Obvykle název atributu ze **zdrojového** objektu. |
| **oldValue** |Volitelné |Řetězec |Hodnota, která má být nahrazena ve **zdroji** nebo **šabloně**. |
| **Vzor Regex** |Volitelné |Řetězec |Vzor regulárního výrazu pro hodnotu, která má být nahrazena **zdrojem**. Nebo, pokud se používá **replacementPropertyName** , vzorek pro extrakci hodnoty z **replacementPropertyName**. |
| **regexGroupName** |Volitelné |Řetězec |Název skupiny uvnitř **vzor Regex** Jenom v případě, že se používá  **replacementPropertyName** , extrahujeme hodnotu této skupiny jako **replacementValue** z **replacementPropertyName**. |
| **replacementValue** |Volitelné |Řetězec |Nová hodnota, kterou chcete nahradit starou. |
| **replacementAttributeName** |Volitelné |Řetězec |Název atributu, který se má použít k nahrazení hodnoty |
| **vzhledu** |Volitelné |Řetězec |Když se zadá hodnota **šablony** , budeme v šabloně Hledat text **OldValue** a nahradit ho **zdrojovou** hodnotou. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funkce:** SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Popis:** Vyžaduje minimálně dva argumenty, což jsou jedinečná pravidla generování hodnot definovaná pomocí výrazů. Funkce vyhodnocuje všechna pravidla a potom kontroluje hodnotu vygenerovanou pro jedinečnost v cílové aplikaci/adresáři. První nalezená jedinečná hodnota bude vrácena. Pokud všechny hodnoty již v cíli existují, bude položka získat uloží a důvod se zaznamená do protokolu auditu. Není k dispozici horní mez počtu argumentů, které by bylo možné poskytnout.


 - Toto je funkce nejvyšší úrovně, nemůže být vnořená.
 - Tuto funkci nelze použít pro atributy, které mají odpovídající prioritu.   
 - Tato funkce je určena pouze pro vytváření záznamů. Při použití s atributem nastavte vlastnost **použít mapování** na **pouze při vytváření objektu**.
 - Tato funkce je momentálně podporovaná jenom pro zřizování uživatelů z Workday do služby Active Directory. Nedá se použít s jinými zřizovacími aplikacemi. 


**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **uniqueValueRule1 ... uniqueValueRuleN** |Vyžaduje se aspoň 2, bez horní meze. |Řetězec | Seznam pravidel generování jedinečných hodnot, které se mají vyhodnotit |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funkce:** SingleAppRoleAssignment ([appRoleAssignments])

**Popis:** Vrátí jeden appRoleAssignment ze seznamu všech appRoleAssignments přiřazených uživateli pro danou aplikaci. Tato funkce je nutná k převedení objektu appRoleAssignments na jeden řetězec názvu role. Doporučujeme, abyste zajistili, že se jednomu uživateli přiřadí pouze jedna appRoleAssignment a pokud je přiřazeno více rolí, vrácený řetězec role nemusí být předvídatelný. 

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **AppRoleAssignments** |Vyžadováno |Řetězec |objekt **[appRoleAssignments]** . |

---
### <a name="split"></a>Rozdělení
**Funkce:** Split (Source, oddělovač)

**Popis:** Rozdělí řetězec na pole s více hodnotami pomocí zadaného oddělovače.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |**zdrojová** hodnota, která se má aktualizovat |
| **Oddělovač** |Vyžadováno |Řetězec |Určuje znak, který bude použit k rozdělení řetězce (například: ","). |

---
### <a name="stripspaces"></a>StripSpaces
**Funkce:** StripSpaces (zdroj)

**Popis:** Odebere ze zdrojového řetězce všechny znaky ("").

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |**zdrojová** hodnota, která se má aktualizovat |

---
### <a name="switch"></a>Přepínač
**Funkce:** Switch (zdroj; defaultValue; klíč1; hodnota1; key2; hodnota2;...)

**Popis:** Když hodnota **zdroje** odpovídá **klíči**, vrátí **hodnotu** pro tento **klíč**. Pokud **zdrojová** hodnota neodpovídá žádným klíčům, vrátí hodnotu **DefaultValue**.  Parametry **klíče** a **hodnoty** se musí vždycky nacházet ve dvojicích. Funkce vždycky očekává sudý počet parametrů. Funkce by neměla být používána pro referenční atributy, jako je například správce. 

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |**Zdrojová** hodnota, která se má aktualizovat |
| **Hodnot** |Volitelné |Řetězec |Výchozí hodnota, která se má použít, pokud zdroj neodpovídá žádným klíčům Může být prázdný řetězec (""). |
| **zkrat** |Vyžadováno |Řetězec |**Klíč** pro porovnání **zdrojové** hodnoty s. |
| **value** |Vyžadováno |Řetězec |Nahrazující hodnota pro **zdroj** , který odpovídá klíči. |

---
### <a name="tolower"></a>ToLower
**Funkce:** ToLower (zdroj, jazyková verze)

**Popis:** Převezme hodnotu *zdrojového* řetězce a převede ji na malý případ pomocí pravidel jazykové verze, které jsou určeny. Pokud nejsou zadány žádné informace o *jazykové verzi* , pak použije invariantní jazykovou verzi.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |Obvykle název atributu ze zdrojového objektu |
| **jazykových** |Volitelné |Řetězec |Formát pro název jazykové verze založený na RFC 4646 je *languagecode2-Country/regioncode2*, kde *languagecode2* je kód jazyka dvou písmen a *země/regioncode2* je kód subjazykové verze se dvěma písmeny. Mezi příklady patří ja-JP pro japonštinu (Japonsko) a EN-US pro angličtinu (USA). V případech, kdy kód jazyka se dvěma písmeny není k dispozici, je použit kód o třech písmenech odvozený z ISO 639-2.|

---
### <a name="toupper"></a>ToUpper
**Funkce:** ToUpper (zdroj, jazyková verze)

**Popis:** Převezme hodnotu *zdrojového* řetězce a převede ji na velká písmena pomocí pravidel jazykové verze, které jsou určeny. Pokud nejsou zadány žádné informace o *jazykové verzi* , pak použije invariantní jazykovou verzi.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |Obvykle název atributu ze zdrojového objektu. |
| **jazykových** |Volitelné |Řetězec |Formát pro název jazykové verze založený na RFC 4646 je *languagecode2-Country/regioncode2*, kde *languagecode2* je kód jazyka dvou písmen a *země/regioncode2* je kód subjazykové verze se dvěma písmeny. Mezi příklady patří ja-JP pro japonštinu (Japonsko) a EN-US pro angličtinu (USA). V případech, kdy kód jazyka se dvěma písmeny není k dispozici, je použit kód o třech písmenech odvozený z ISO 639-2.|

---
### <a name="word"></a>Word
**Funkce:** Word (řetězec, WordNumber, oddělovače)

**Popis:** Funkce Word vrátí slovo obsažené v řetězci na základě parametrů popisujících oddělovače, které se mají použít, a číslo slova, které se má vrátit. Každý řetězec znaků v řetězci, oddělený jedním ze znaků v oddělovačích, je identifikován jako slova:

Pokud číslo < 1, vrátí prázdný řetězec.
Pokud má řetězec hodnotu null, vrátí prázdný řetězec.
Pokud řetězec obsahuje méně než čísla slov nebo řetězec neobsahuje žádná slova identifikovaná oddělovači, je vrácen prázdný řetězec.

**Ukazatelů** 

| Název | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Řetězec** |Vyžadováno |Vícehodnotový atribut |Řetězec, ze kterého se má vrátit slovo|
| **WordNumber** |Vyžadováno | Integer | Číslo určující, které číslo slova se má vrátit|
| **oddělovače** |Vyžadováno |Řetězec| Řetězec představující oddělovače, které se mají použít k identifikaci slov|

**Případě**
`Word("The quick brown fox",3," ")`

Vrátí "Brown".

`Word("This,string!has&many separators",3,",!&#")`

Vrátí "has".

---

## <a name="examples"></a>Příklady
### <a name="strip-known-domain-name"></a>Název známé domény
Aby bylo možné získat uživatelské jméno, je nutné odstranit známý název domény z e-mailu uživatele. Pokud je doména například "contoso.com", můžete použít následující výraz:

**Vyjádření** 
`Replace([mail], "@contoso.com", , ,"", ,)`

**Vzorový vstup/výstup:** 

* **Vstup** (pošta): " john.doe@contoso.com "
* **Výstup**: Jan. Chvojková

### <a name="append-constant-suffix-to-user-name"></a>Připojit konstantní příponu k uživatelskému jménu
Pokud používáte izolovaný prostor Salesforce, možná budete muset před synchronizací přidat další příponu všem vašim uživatelským jménům.

**Vyjádření** 
`Append([userPrincipalName], ".test")`

**Vzorový vstup/výstup:** 

* **Vstup**: (userPrincipalName): " John.Doe@contoso.com "
* **Výstup**: " John.Doe@contoso.com.test "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generovat alias uživatele zřetězením částí jména a příjmení
Alias uživatele musíte vygenerovat zadáním prvních 3 písmen křestního jména uživatele a prvních 5 písmen příjmení uživatele.

**Vyjádření** 
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Vzorový vstup/výstup:** 

* **Vstup** (křestní jméno): "Jan"
* **Vstup** (příjmení): "Chvojková"
* **Výstup**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Odebrat diakritická znaménka z řetězce
Je nutné nahradit znaky obsahující diakritická znaménka stejnými znaky, které neobsahují značky akcentů.

**Výraz:** NormalizeDiacritics ([křestní jméno])

**Vzorový vstup/výstup:** 

* **Vstup** (křestní jméno): "Zoë"
* **Výstup**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Rozdělit řetězec do pole s více hodnotami
Musíte vzít seznam řetězců oddělených čárkami a rozdělit je do pole, které se dá zapojit do vícehodnotového atributu, jako je atribut PermissionSet služby Salesforce. V tomto příkladu se v extensionAttribute5 ve službě Azure AD nastavil seznam sad oprávnění.

**Výraz:** Split ([extensionAttribute5]; ";")

**Vzorový vstup/výstup:** 

* **Vstup** (extensionAttribute5): "PermissionSetOne, PermissionSetTwo"
* **Výstup**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Datum výstupu jako řetězec v určitém formátu
Chcete odesílat data do aplikace SaaS v určitém formátu. Například chcete formátovat data pro ServiceNow.

**Vyjádření** 

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Vzorový vstup/výstup:**

* **Vstup** (extensionAttribute1): "20150123105347.1 z"
* **Výstup**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Nahraďte hodnotu na základě předdefinované sady možností.

Je potřeba definovat časové pásmo uživatele na základě kódu stavu uloženého ve službě Azure AD. Pokud kód stavu neodpovídá žádné z předdefinovaných možností, použijte výchozí hodnotu "Austrálie/Sydney".

**Vyjádření** 
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Vzorový vstup/výstup:**

* **Vstup** (stav): "QLD"
* **Výstup**: "Austrálie/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Nahrazení znaků pomocí regulárního výrazu
Je nutné najít znaky, které odpovídají hodnotě regulárního výrazu, a odebrat je.

**Vyjádření** 

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
* **Výstup**: " john.smith@contoso.com "

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generovat jedinečnou hodnotu pro atribut userPrincipalName (UPN)
Na základě křestního jména a jména uživatele a jména a příjmení je potřeba vygenerovat hodnotu pro atribut hlavního názvu uživatele (UPN) a před přiřazením hodnoty k atributu hlavního názvu uživatele vyhledat jeho jedinečnost v cílovém adresáři služby AD.

**Vyjádření** 

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**Vzorový vstup/výstup:**

* **Vstup** (PreferredFirstName): "Jan"
* **Vstup** (PreferredLastName): "Smith"
* **Výstup**: " John.Smith@contoso.com ", pokud hodnota hlavního názvu uživatele (UPN) John.Smith@contoso.com ještě v adresáři neexistuje
* **Výstup**: " J.Smith@contoso.com ", pokud John.Smith@contoso.com v adresáři již existuje hodnota hlavního názvu uživatele (UPN).
* **Výstup**: " Jo.Smith@contoso.com ", pokud výše uvedené dvě hodnoty UPN v adresáři již existují

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Hodnota pro poštu flowu, pokud není NULL, jinak Flow userPrincipalName
Chcete-li, aby se atribut mail nacházel, je-li k dispozici. Pokud není, chcete místo toho Flow použít hodnotu userPrincipalName.

**Vyjádření** 
`Coalesce([mail],[userPrincipalName])`

**Vzorový vstup/výstup:** 

* **Vstup** (mail): null
* **Vstup** (userPrincipalName): " John.Doe@contoso.com "
* **Výstup**: " John.Doe@contoso.com "

## <a name="related-articles"></a>Související články
* [Automatizace zřizování a rušení zřizování uživatelů pro aplikace SaaS](../app-provisioning/user-provisioning.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](../app-provisioning/customize-application-attributes.md)
* [Filtry oborů pro zřizování uživatelů](define-conditional-rules-for-provisioning-user-accounts.md)
* [Pomocí SCIM zapněte automatické zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Oznámení o zřizování účtů](../app-provisioning/user-provisioning.md)
* [Seznam kurzů o integraci aplikací SaaS](../saas-apps/tutorial-list.md)
