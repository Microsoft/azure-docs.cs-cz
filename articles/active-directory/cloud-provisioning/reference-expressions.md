---
title: Azure AD Connect výrazy zřizování cloudu a odkaz na funkci
description: Referenční dokumentace
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51c14fd7f427c29c47521a7355309e62ab2254ca
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298611"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Zápis výrazů pro mapování atributů v Azure Active Directory
Při konfiguraci zřizování cloudu je jedním z typů mapování atributů, které lze zadat, mapování výrazu. 

Mapování výrazů umožňuje přizpůsobit atributy pomocí výrazu podobného skriptem.  To vám umožní transformovat místní data na novou nebo jinou hodnotu.  Například můžete chtít zkombinovat dva atributy do jediného atributu, protože tento jediný atribut používá jedna z vašich cloudových aplikací.

Následující dokument bude pokrývat výrazy podobné skriptům, které se používají k transformaci dat.  Toto je jenom ta část procesu.  Dále budete muset použít tento výraz a umístit ho do webového požadavku do svého tenanta.  Další informace o tom, jak se tyto [transformace](how-to-transformation.md) zobrazují

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
| Seznam funkcí | Popis |
|-----|----|
|[Příloh](#append)|Vezme řetězcovou hodnotu zdroje a připojí přípona na konec.|
|[BitAnd](#bitand)|Funkce BitAnd nastaví v hodnotě zadané bity na hodnotu.|
|[CBool](#cbool)|Funkce CBool vrátí logickou hodnotu založenou na vyhodnoceném výrazu.|
|[ConvertFromBase64](#convertfrombase64)|Funkce ConvertFromBase64 převede zadanou hodnotu v kódování Base64 na běžný řetězec.|
|[ConvertToBase64](#converttobase64)|Funkce ConvertToBase64 převede řetězec na řetězec Unicode base64. |
|[ConvertToUTF8Hex](#converttoutf8hex)|Funkce ConvertToUTF8Hex převede řetězec na šestnáctkovou hodnotu v kódování UTF8.|
|[Výpočtu](#count)|Funkce Count vrátí počet prvků v vícehodnotovém atributu.|
|[CStr](#cstr)|Funkce CStr se převede na datový typ String.|
|[DateFromNum](#datefromnum)|Funkce DateFromNum převede hodnotu ve formátu data AD na typ DateTime.|
|[DNComponent](#dncomponent)|Funkce DNComponent vrací hodnotu zadané složky DN z levé části.|
|[Chyba](#error)|Funkce Error slouží k vrácení vlastní chyby.|
|[FormatDateTime](#formatdatetime) |Přebírá řetězec data z jednoho formátu a převede jej na jiný formát.| 
|[HLAVNÍCH](#guid)|Identifikátor GUID funkce vygeneruje nový náhodný identifikátor GUID.|           
|[IIF](#iif)|Funkce IIF vrátí jednu ze sady možných hodnot na základě zadané podmínky.|
|[InStr](#instr)|Funkce InStr vyhledá první výskyt podřetězce v řetězci.|
|[IsNull](#isnull)|Pokud je výraz vyhodnocen jako null, funkce IsNull vrátí hodnotu true.|
|[IsNullOrEmpty](#isnullorempty)|Pokud má výraz hodnotu null nebo je prázdný řetězec, vrátí funkce IsNullOrEmpty hodnotu true.|         
|[K dispozici](#ispresent)|Pokud se výraz vyhodnotí jako řetězec, který není null a není prázdný, vrátí funkce vracející hodnotu true.|    
|[Řetězec](#isstring)|Pokud je možné výraz vyhodnotit na typ řetězce, pak je funkce typu String vyhodnocena jako true.|
|[Položka](#item)|Funkce Item vrátí jednu položku z vícehodnotového řetězce nebo atributu.|
|[Spojení](#join) |Join () je podobný jako Append (), s tím rozdílem, že může zkombinovat více hodnot **zdrojového** řetězce do jednoho řetězce a každá hodnota bude oddělena řetězcem **oddělovače** .| 
|[Zbývá](#left)|Funkce Left vrátí zadaný počet znaků nalevo od řetězce.|
|[Středně](#mid) |Vrátí podřetězec zdrojovou hodnotou. Dílčí řetězec je řetězec, který obsahuje pouze některé znaky z zdrojový řetězec.|
|[NormalizeDiacritics](#normalizediacritics)|Vyžaduje jeden argument řetězec. Vrátí řetězec, ale s znaky diakritická nahradí ekvivalentní-diakritická znaků.|
|[Mění](#not) |Převrátí logickou hodnotu **zdroje**. Pokud je hodnota **zdroje** "*true*", vrátí "*false*". V opačném případě vrátí "*true*".| 
|[RemoveDuplicates –](#removeduplicates)|Funkce RemoveDuplicates – přebírá řetězec s více hodnotami a ověří, zda jsou všechny hodnoty jedinečné.| 
|[Náhrady](#replace) |Nahradí hodnoty v řetězci. | 
|[SelectUniqueValue](#selectuniquevalue)|Vyžaduje minimálně dva argumenty, které jsou definovány pomocí výrazů pravidel pro vytvoření jedinečnou hodnotu. Funkce vyhodnocuje každé pravidlo a poté zkontroluje hodnotu vygenerovat jedinečný v cílové aplikaci/adresáři.| 
|[SingleAppRoleAssignment](#singleapproleassignment)|Vrátí jeden appRoleAssignment ze seznamu všech appRoleAssignments přiřazených uživateli pro danou aplikaci.| 
|[Rozdělení](#split)|Rozdělí řetězec na pole s více hodnotami pomocí zadaného oddělovače.|
|[StringFromSID](#stringfromsid)|Funkce StringFromSid převede pole bajtů obsahující identifikátor zabezpečení na řetězec.| 
|[StripSpaces](#stripspaces) |Odebere všechny mezery ("") znaků z řetězce zdroje.| 
|[Přepnutí](#switch)|Když hodnota **zdroje** odpovídá **klíči**, vrátí **hodnotu** pro tento **klíč**. | 
|[ToLower](#tolower)|Převezme hodnotu *zdrojového* řetězce a převede ji na malý případ pomocí pravidel jazykové verze, které jsou určeny.| 
|[ToUpper](#toupper)|Převezme hodnotu *zdrojového* řetězce a převede ji na velká písmena pomocí pravidel jazykové verze, které jsou určeny.|
|[Sklon](#trim)|Funkce Trim odstraní úvodní a koncové prázdné znaky z řetězce.|
|[Textových](#word)|Funkce Word vrátí slovo obsažené v řetězci na základě parametrů popisujících oddělovače, které se mají použít, a číslo slova, které se má vrátit.|

---
### <a name="append"></a>Připojit
**Slouží**<br> Append(Source, suffix)

**Název**<br> Vezme řetězcovou hodnotu zdroje a připojí přípona na konec.

**Ukazatelů**<br> 

   | Název | Požadovaný / s opakováním | Typ | Poznámky: |
   | --- | --- | --- | --- |
   | **Zdrojová** |Požaduje se |Řetězec |Obvykle název atributu ze zdrojového objektu. |
   | **auditování** |Požaduje se |Řetězec |Řetězec, který chcete přidat do konce zdrojové hodnoty. |

---
### <a name="bitand"></a>BitAnd
**Název**  
Funkce BitAnd nastaví v hodnotě zadané bity na hodnotu.

**Syntaktick**  
`num BitAnd(num value1, num value2)`

* Hodnota1, hodnota2: číselné hodnoty, které by měly být AND'ed společně

**Mark**  
Tato funkce převede oba parametry do binární reprezentace a nastaví bit na:

* 0 – Pokud má jedna nebo obě z odpovídajících bitů v *hodnota1* a *hodnota2* hodnotu 0
* 1 – Pokud jsou obě odpovídající bity 1.

Jinými slovy, vrátí 0 ve všech případech s výjimkou toho, že odpovídající bity obou parametrů jsou 1.

**Příklad:**  
 
 `BitAnd(&HF, &HF7)`</br>
 Vrátí hodnotu 7, protože hexadecimální hodnota "F" a "F7" je vyhodnocena.

---

### <a name="cbool"></a>CBool
**Název**  
Funkce CBool vrátí logickou hodnotu založenou na vyhodnoceném výrazu.

**Syntaktick**  
`bool CBool(exp Expression)`

**Mark**  
Pokud je výraz vyhodnocen jako nenulová hodnota, pak funkce CBool vrátí hodnotu true, jinak vrátí hodnotu false.

**Příklad:**  
`CBool([attrib1] = [attrib2])`  

Vrátí hodnotu true, pokud mají oba atributy stejnou hodnotu.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Název**  
Funkce ConvertFromBase64 převede zadanou hodnotu v kódování Base64 na běžný řetězec.

**Syntaktick**  
`str ConvertFromBase64(str source)` – předpokládá kódování Unicode.  
`str ConvertFromBase64(str source, enum Encoding)`

* Zdroj: řetězec kódovaný v kódování Base64  
* Kódování: Unicode, ASCII, UTF8

**Příklad**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Oba příklady vrátí "*Hello World!* "

---
### <a name="converttobase64"></a>ConvertToBase64
**Název**  
Funkce ConvertToBase64 převede řetězec na řetězec Unicode base64.  
Převede hodnotu pole celých čísel na odpovídající řetězcovou reprezentaci, která je zakódována pomocí číslic-Base-64.

**Syntaktick**  
`str ConvertToBase64(str source)`

**Příklad:**  
`ConvertToBase64("Hello world!")`  
Vrátí "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Název**  
Funkce ConvertToUTF8Hex převede řetězec na šestnáctkovou hodnotu v kódování UTF8.

**Syntaktick**  
`str ConvertToUTF8Hex(str source)`

**Mark**  
Výstupní formát této funkce se používá Azure Active Directory jako formát atributu DN.

**Příklad:**  
`ConvertToUTF8Hex("Hello world!")`  
Returns 48656C6C6F20776F726C6421

---
### <a name="count"></a>Počet
**Název**  
Funkce Count vrátí počet prvků v vícehodnotovém atributu.

**Syntaktick**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>CStr
**Název**  
Funkce CStr se převede na datový typ String.

**Syntaktick**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* hodnota: může to být číselná hodnota, odkazový atribut nebo logická hodnota.

**Příklad:**  
`CStr([dn])`  
Může vracet "CN = Jan, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Název**  
Funkce DateFromNum převede hodnotu ve formátu data AD na typ DateTime.

**Syntaktick**  
`dt DateFromNum(num value)`

**Příklad:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Vrátí hodnotu DateTime představující 2012-01-01 23:00:00.

---
### <a name="dncomponent"></a>DNComponent
**Název**  
Funkce DNComponent vrací hodnotu zadané složky DN z levé části.

**Syntaktick**  
`str DNComponent(ref dn, num ComponentNumber)`

* DN: atribut reference, který se má interpretovat
* ComponentNumber: komponenta v rozlišujícím názvu, která se má vrátit

**Příklad:**  
`DNComponent(CRef([dn]),1)`  
Pokud je DN "CN = Jan, OU =...", vrátí Jana

---
### <a name="error"></a>Chyba
**Název**  
Funkce Error slouží k vrácení vlastní chyby.

**Syntaktick**  
`void Error(str ErrorMessage)`

**Příklad:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Pokud atribut Account není přítomen, vyvolejte chybu objektu.

---
### <a name="formatdatetime"></a>formatDateTime
**Slouží**<br> FormatDateTime (zdroj, inputFormat outputFormat.)

**Název**<br> Přebírá řetězec data z jednoho formátu a převede jej na jiný formát.

**Ukazatelů**<br> 

   | Název | Požadovaný / s opakováním | Typ | Poznámky: |
   | --- | --- | --- | --- |
   | **Zdrojová** |Požaduje se |Řetězec |Obvykle název atributu ze zdrojového objektu. |
   | **inputFormat** |Požaduje se |Řetězec |Očekávaný formát zdrojové hodnoty. Podporované formáty najdete v tématu [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
   | **outputFormat** |Požaduje se |Řetězec |Formát výstupního data. |

---
### <a name="guid"></a>identifikátor GUID
**Název**  
Identifikátor GUID funkce vygeneruje nový náhodný identifikátor GUID.

**Syntaktick**  
`str Guid()`

---
### <a name="iif"></a>IIF
**Název**  
Funkce IIF vrátí jednu ze sady možných hodnot na základě zadané podmínky.

**Syntaktick**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* podmínka: libovolná hodnota nebo výraz, který lze vyhodnotit na hodnotu true nebo false.
* valueIfTrue: Pokud je podmínka vyhodnocena jako true, vrácená hodnota.
* valueIfFalse: Pokud je podmínka vyhodnocena jako false, vrácená hodnota.

**Příklad:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Pokud je uživatel interně, vrátí alias uživatele s názvem "t-", který je přidán na začátek, jinak vrátí alias uživatele tak, jak je.

---
### <a name="instr"></a>InStr
**Název**  
Funkce InStr vyhledá první výskyt podřetězce v řetězci.

**Syntaktick**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: řetězec, který má být prohledán
* stringmatch: řetězec, který se má najít
* Start: počáteční pozice pro vyhledání podřetězce
* porovnání: vbTextCompare nebo vbBinaryCompare

**Mark**  
Vrátí pozici, kde byl dílčí řetězec nalezen, nebo 0, pokud nebyl nalezen.

**Příklad:**  
`InStr("The quick brown fox","quick")`  
Evalues na 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Vyhodnotí na 7

---
### <a name="isnull"></a>IsNull
**Název**  
Pokud je výraz vyhodnocen jako null, funkce IsNull vrátí hodnotu true.

**Syntaktick**  
`bool IsNull(var Expression)`

**Mark**  
U atributu je hodnota null vyjádřena nepřítomností atributu.

**Příklad:**  
`IsNull([displayName])`  
Vrátí hodnotu true, pokud atribut není přítomen v CS nebo MV.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Název**  
Pokud má výraz hodnotu null nebo je prázdný řetězec, vrátí funkce IsNullOrEmpty hodnotu true.

**Syntaktick**  
`bool IsNullOrEmpty(var Expression)`

**Mark**  
U atributu by se to mělo vyhodnotit na hodnotu true, pokud atribut chybí nebo je přítomen, ale je to prázdný řetězec.  
Inverzní část této funkce je pojmenována jako.

**Příklad:**  
`IsNullOrEmpty([displayName])`  
Vrátí hodnotu true, pokud atribut není přítomen, nebo je prázdný řetězec v CS nebo MV.

---
### <a name="ispresent"></a>K dispozici
**Název**  
Pokud se výraz vyhodnotí jako řetězec, který není null a není prázdný, vrátí funkce vracející hodnotu true.

**Syntaktick**  
`bool IsPresent(var expression)`

**Mark**  
Inverzní funkce k této funkci má název IsNullOrEmpty.

**Příklad:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Položka
**Název**  
Funkce Item vrátí jednu položku z vícehodnotového řetězce nebo atributu.

**Syntaktick**  
`var Item(mvstr attribute, num index)`

* atribut: vícehodnotový atribut
* index: index položky v řetězci s více hodnotami.

**Mark**  
Funkce Item je užitečná společně s funkcí Contains, protože druhá funkce vrací index na položku v vícehodnotovém atributu.

Vyvolá chybu, pokud je index mimo rozsah.

**Příklad:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Vrátí primární e-mailovou adresu.

---
### <a name="isstring"></a>Řetězec
**Název**  
Pokud je možné výraz vyhodnotit na typ řetězce, pak je funkce typu String vyhodnocena jako true.

**Syntaktick**  
`bool IsString(var expression)`

**Mark**  
Slouží k určení, zda může být CStr () úspěšné k analýze výrazu.

---
### <a name="join"></a>Spojit
**Slouží**<br> Připojte se k (oddělovač, zdroj1, zdroj2,...)

**Název**<br> Join () je podobný jako Append (), s tím rozdílem, že může zkombinovat více hodnot **zdrojového** řetězce do jednoho řetězce a každá hodnota bude oddělena řetězcem **oddělovače** .

Pokud je jednou ze zdrojových hodnot atribut s více hodnotami, pak se všechny hodnoty v tomto atributu spojí dohromady, oddělené hodnotou oddělovače.

**Ukazatelů**<br> 

   | Název | Požadovaný / s opakováním | Typ | Poznámky: |
   | --- | --- | --- | --- |
   | **oddělování** |Požaduje se |Řetězec |Řetězec použitý k oddělení zdrojové hodnoty, když jsou zřetězeny do jednoho řetězce. Může být "" Pokud žádný oddělovač je povinný. |
   | **source1 ... sourceN** |Povinné, proměnná počet pokusů |Řetězec |Hodnoty, který se má spojit dohromady řetězce. |

---
### <a name="left"></a>Zbývá
**Název**  
Funkce Left vrátí zadaný počet znaků nalevo od řetězce.

**Syntaktick**  
`str Left(str string, num NumChars)`

* řetězec: řetězec, ze kterého se mají vracet znaky
* NumChars: číslo určující počet znaků, které mají být vráceny od začátku (vlevo) řetězce

**Mark**  
Řetězec obsahující první numChars znaky v řetězci:

* Pokud numChars = 0, vrátí prázdný řetězec.
* Pokud numChars < 0, vrátí se vstupní řetězec.
* Pokud má řetězec hodnotu null, vrátí prázdný řetězec.

Pokud řetězec obsahuje méně znaků než číslo zadané v numChars, vrátí se řetězec shodný s řetězcem (tj. obsahující všechny znaky v parametru 1).

**Příklad:**  
`Left("John Doe", 3)`  
Vrací objekt `Joh`.

---
### <a name="mid"></a>Mid
**Slouží**<br> Mid (source; start, délka)

**Název**<br> Vrátí podřetězec zdrojovou hodnotou. Dílčí řetězec je řetězec, který obsahuje pouze některé znaky z zdrojový řetězec.

**Ukazatelů**<br> 

   | Název | Požadovaný / s opakováním | Typ | Poznámky: |
   | --- | --- | --- | --- |
   | **Zdrojová** |Požaduje se |Řetězec |Obvykle název atributu. |
   | **start** |Požaduje se |celé číslo |Index ve **zdrojovém** řetězci, ve kterém by měl být spuštěný dílčí řetězec První znak v řetězci budou mít index hodnotu 1, druhý znak bude mít index 2 a tak dále. |
   | **časový** |Požaduje se |celé číslo |Délka podřetězce. Pokud délka končí mimo **zdrojový** řetězec, funkce vrátí podřetězec z **počátečního** indexu do konce **zdrojového** řetězce. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Slouží**<br> NormalizeDiacritics(source)

**Název**<br> Vyžaduje jeden argument řetězec. Vrátí řetězec, ale s znaky diakritická nahradí ekvivalentní-diakritická znaků. Obvykle slouží k převodu jména a příjmení obsahující diakritická znaky (znaky s diakritikou značky) do platné hodnoty, které můžete použít různé identifikátory uživatele, jako je například hlavních názvů uživatelů, názvy účtů SAM a e-mailové adresy.

**Ukazatelů**<br> 

   | Název | Požadovaný / s opakováním | Typ | Poznámky: |
   | --- | --- | --- | --- |
   | **Zdrojová** |Požaduje se |Řetězec | Obvykle se jedná o křestní jméno nebo název atributu příjmení. |

---
### <a name="not"></a>Not
**Slouží**<br> Not(Source)

**Název**<br> Převrátí logickou hodnotu **zdroje**. Pokud je hodnota **zdroje** "*true*", vrátí "*false*". V opačném případě vrátí "*true*".

**Ukazatelů**<br> 

   | Název | Požadovaný / s opakováním | Typ | Poznámky: |
   | --- | --- | --- | --- |
   | **Zdrojová** |Požaduje se |Logického řetězce |Očekávané **zdrojové** hodnoty jsou "true" nebo "false". |

---
### <a name="removeduplicates"></a>RemoveDuplicates –
**Název**  
Funkce RemoveDuplicates – přebírá řetězec s více hodnotami a ověří, zda jsou všechny hodnoty jedinečné.

**Syntaktick**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Příklad:**  
`RemoveDuplicates([proxyAddresses])`  
Vrátí upravený atribut proxyAddress, ve kterém byly odstraněny všechny duplicitní hodnoty.

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

   | Název | Požadovaný / s opakováním | Typ | Poznámky: |
   | --- | --- | --- | --- |
   | **Zdrojová** |Požaduje se |Řetězec |Obvykle název atributu ze **zdrojového** objektu. |
   | **oldValue** |Nepovinné |Řetězec |Hodnota, která má být nahrazena ve **zdroji** nebo **šabloně**. |
   | **Vzor Regex** |Nepovinné |Řetězec |Vzor regulárního výrazu pro hodnotu, která má být nahrazena **zdrojem**. Nebo, pokud se používá **replacementPropertyName** , vzorek pro extrakci hodnoty z **replacementPropertyName**. |
   | **regexGroupName** |Nepovinné |Řetězec |Název skupiny uvnitř **vzor Regex** Jenom v případě, že se používá **replacementPropertyName** , extrahujeme hodnotu této skupiny jako **replacementValue** z **replacementPropertyName**. |
   | **replacementValue** |Nepovinné |Řetězec |Nová hodnota nahradí starou s. |
   | **replacementAttributeName** |Nepovinné |Řetězec |Název atributu, který se má použít k nahrazení hodnoty |
   | **vzhledu** |Nepovinné |Řetězec |Když se zadá hodnota **šablony** , budeme v šabloně Hledat text **OldValue** a nahradit ho **zdrojovou** hodnotou. |

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

   | Název | Požadovaný / s opakováním | Typ | Poznámky: |
   | --- | --- | --- | --- |
   | **uniqueValueRule1 ... uniqueValueRuleN** |Minimálně 2 jsou povinné, ne horní mez |Řetězec | Seznam pravidel generování jedinečných hodnot, které se mají vyhodnotit |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Slouží**<br> SingleAppRoleAssignment([appRoleAssignments])

**Název**<br> Vrátí jeden appRoleAssignment ze seznamu všech appRoleAssignments přiřazených uživateli pro danou aplikaci. Tato funkce je nutná k převedení objektu appRoleAssignments na jeden řetězec názvu role. Doporučujeme, abyste zajistili, že se jednomu uživateli přiřadí pouze jedna appRoleAssignment a pokud je přiřazeno více rolí, vrácený řetězec role nemusí být předvídatelný. 

**Ukazatelů**<br> 

  | Název | Požadovaný / s opakováním | Typ | Poznámky: |
  |--- | --- | --- | --- |
  | **AppRoleAssignments** |Požaduje se |Řetězec |objekt **[appRoleAssignments]** . |

---
### <a name="split"></a>Rozdělení
**Slouží**<br> Split (Source, oddělovač)

**Název**<br> Rozdělí řetězec na pole s více hodnotami pomocí zadaného oddělovače.

**Ukazatelů**<br> 

   | Název | Požadovaný / s opakováním | Typ | Poznámky: |
   | --- | --- | --- | --- |
   | **Zdrojová** |Požaduje se |Řetězec |**zdrojová** hodnota, která se má aktualizovat |
   | **oddělovač** |Požaduje se |Řetězec |Určuje znak, který bude použit k rozdělení řetězce (například: ","). |

---
### <a name="stringfromsid"></a>StringFromSid
**Název**  
Funkce StringFromSid převede pole bajtů obsahující identifikátor zabezpečení na řetězec.

**Syntaktick**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces
**Slouží**<br> StripSpaces(source)

**Název**<br> Odebere všechny mezery ("") znaků z řetězce zdroje.

**Ukazatelů**<br> 

   | Název | Požadovaný / s opakováním | Typ | Poznámky: |
   | --- | --- | --- | --- |
   | **Zdrojová** |Požaduje se |Řetězec |**zdrojová** hodnota, která se má aktualizovat |

---
### <a name="switch"></a>Přepínač
**Slouží**<br> Switch (zdroj, výchozí hodnota, key1, value1, key2, value2,...)

**Název**<br> Když hodnota **zdroje** odpovídá **klíči**, vrátí **hodnotu** pro tento **klíč**. Pokud **zdrojová** hodnota neodpovídá žádným klíčům, vrátí hodnotu **DefaultValue**.  Parametry **klíče** a **hodnoty** se musí vždycky nacházet ve dvojicích. Funkce očekává vždy sudý počet parametrů.

**Ukazatelů**<br> 

   | Název | Požadovaný / s opakováním | Typ | Poznámky: |
   | --- | --- | --- | --- |
   | **Zdrojová** |Požaduje se |Řetězec |**Zdrojová** hodnota, která se má ověřit |
   | **Hodnot** |Nepovinné |Řetězec |Výchozí hodnota má být použit při zdroj neodpovídá žádné klíče. Může být prázdný řetězec (""). |
   | **key** |Požaduje se |Řetězec |**Klíč** pro porovnání **zdrojové** hodnoty s. |
   | **value** |Požaduje se |Řetězec |Nahrazující hodnota pro **zdroj** , který odpovídá klíči. |

---
### <a name="tolower"></a>toLower
**Slouží**<br> ToLower (zdroj, jazyková verze)

**Název**<br> Převezme hodnotu *zdrojového* řetězce a převede ji na malý případ pomocí pravidel jazykové verze, které jsou určeny. Pokud nejsou zadány žádné informace o *jazykové verzi* , pak použije invariantní jazykovou verzi.

**Ukazatelů**<br> 

   | Název | Požadovaný / s opakováním | Typ | Poznámky: |
   | --- | --- | --- | --- |
   | **Zdrojová** |Požaduje se |Řetězec |Obvykle název atributu ze zdrojového objektu |
   | **jazykových** |Nepovinné |Řetězec |Formát pro název jazykové verze založený na RFC 4646 je *languagecode2-Country/regioncode2*, kde *languagecode2* je kód jazyka dvou písmen a *země/regioncode2* je kód subjazykové verze se dvěma písmeny. Mezi příklady patří ja-JP pro japonštinu (Japonsko) a EN-US pro angličtinu (USA). V případech, kdy kód jazyka se dvěma písmeny není k dispozici, je použit kód o třech písmenech odvozený z ISO 639-2.|

---

### <a name="toupper"></a>toUpper
**Slouží**<br> ToUpper (zdroj, jazyková verze)

**Název**<br> Převezme hodnotu *zdrojového* řetězce a převede ji na velká písmena pomocí pravidel jazykové verze, které jsou určeny. Pokud nejsou zadány žádné informace o *jazykové verzi* , pak použije invariantní jazykovou verzi.

**Ukazatelů**<br> 

  | Název | Požadovaný / s opakováním | Typ | Poznámky: |
  | --- | --- | --- | --- |
  | **Zdrojová** |Požaduje se |Řetězec |Obvykle název atributu ze zdrojového objektu. |
  | **jazykových** |Nepovinné |Řetězec |Formát pro název jazykové verze založený na RFC 4646 je *languagecode2-Country/regioncode2*, kde *languagecode2* je kód jazyka dvou písmen a *země/regioncode2* je kód subjazykové verze se dvěma písmeny. Mezi příklady patří ja-JP pro japonštinu (Japonsko) a EN-US pro angličtinu (USA). V případech, kdy kód jazyka se dvěma písmeny není k dispozici, je použit kód o třech písmenech odvozený z ISO 639-2.|

---

### <a name="trim"></a>Sklon
**Název**  
Funkce Trim odstraní úvodní a koncové prázdné znaky z řetězce.

**Syntaktick**  
`str Trim(str value)`  

**Příklad:**  
`Trim(" Test ")`  
Vrátí "test".

`Trim([proxyAddresses])`  
Odstraní úvodní a koncové mezery pro každou hodnotu v atributu proxyAddress.

---
### <a name="word"></a>Word
**Název**  
Funkce Word vrátí slovo obsažené v řetězci na základě parametrů popisujících oddělovače, které se mají použít, a číslo slova, které se má vrátit.

**Syntaktick**  
`str Word(str string, num WordNumber, str delimiters)`

* řetězec: řetězec, ze kterého se má vrátit slovo.
* WordNumber: číslo určující, které číslo slova se má vrátit.
* oddělovače: řetězec představující oddělovače, které by se měly použít k identifikaci slov

**Mark**  
Každý řetězec znaků v řetězci, oddělený jedním ze znaků v oddělovačích, je identifikován jako slova:

* Pokud číslo < 1, vrátí prázdný řetězec.
* Pokud má řetězec hodnotu null, vrátí prázdný řetězec.

Pokud řetězec obsahuje méně než čísla slov nebo řetězec neobsahuje žádná slova identifikovaná oddělovači, je vrácen prázdný řetězec.

**Příklad:**  
`Word("The quick brown fox",3," ")`  
Vrátí "Brown"

`Word("This,string!has&many separators",3,",!&#")`  
Vrátí "has"

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


## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřizování cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
