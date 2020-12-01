---
title: Azure AD Connect výrazy zřizování cloudu a odkaz na funkci
description: reference
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a775ce6b7c560783a22697c5dd92288c5d5b7d4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96343701"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Zápis výrazů pro mapování atributů v Azure Active Directory
Při konfiguraci zřizování cloudu je jedním z typů mapování atributů, které lze zadat, mapování výrazu. 

Mapování výrazů umožňuje přizpůsobit atributy pomocí výrazu podobného skriptem.  To vám umožní transformovat místní data na novou nebo jinou hodnotu.  Například můžete chtít zkombinovat dva atributy do jediného atributu, protože tento jediný atribut používá jedna z vašich cloudových aplikací.

Následující dokument bude pokrývat výrazy podobné skriptům, které se používají k transformaci dat.  Toto je jenom ta část procesu.  Dále budete muset použít tento výraz a umístit ho do webového požadavku do svého tenanta.  Další informace o tom, jak se tyto [transformace](how-to-transformation.md) zobrazují

## <a name="syntax-overview"></a>Přehled syntaxe
Syntaxe výrazů pro mapování atributů je Reminiscent funkcí jazyk Visual Basic for Application (VBA).

* Celý výraz musí být definován z funkcí Functions, který se skládá z názvu následovaného argumenty v závorkách: <br>
  *Functions ( `<<argument 1>>` , `<<argument N>>` )*
* Do sebe můžete vnořovat funkce. Příklad: <br> *FunctionOne (FunctionTwo ( `<<argument1>>` ))*
* Do funkcí můžete předat tři různé typy argumentů:
  
  1. Atributy, které musí být uzavřeny do hranatých závorek. Příklad: [attributeName]
  2. Řetězcové konstanty, které musí být uzavřeny do dvojitých uvozovek. Například: "USA"
  3. Další funkce. Příklad: FunctionOne ( `<<argument1>>` , FunctionTwo ( `<<argument2>>` ))
* Pro řetězcové konstanty, pokud v řetězci potřebujete zpětné lomítko (\) nebo uvozovky ("), musí být uvozen znakem zpětného lomítka (\). Příklad: "název společnosti: \\ " contoso \\ ""

## <a name="list-of-functions"></a>Seznam funkcí
| Seznam funkcí | Popis |
|-----|----|
|[Připojit](#append)|Převezme hodnotu zdrojového řetězce a připojí příponu na konec IT.|
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
|[FormatDateTime](#formatdatetime) |Vezme řetězec data z jednoho formátu a převede ho do jiného formátu.| 
|[HLAVNÍCH](#guid)|Identifikátor GUID funkce vygeneruje nový náhodný identifikátor GUID.|           
|[IIF](#iif)|Funkce IIF vrátí jednu ze sady možných hodnot na základě zadané podmínky.|
|[InStr](#instr)|Funkce InStr vyhledá první výskyt podřetězce v řetězci.|
|[IsNull](#isnull)|Pokud je výraz vyhodnocen jako null, funkce IsNull vrátí hodnotu true.|
|[IsNullOrEmpty](#isnullorempty)|Pokud má výraz hodnotu null nebo je prázdný řetězec, vrátí funkce IsNullOrEmpty hodnotu true.|         
|[K dispozici](#ispresent)|Pokud se výraz vyhodnotí jako řetězec, který není null a není prázdný, vrátí funkce vracející hodnotu true.|    
|[Řetězec](#isstring)|Pokud je možné výraz vyhodnotit na typ řetězce, pak je funkce typu String vyhodnocena jako true.|
|[Položka](#item)|Funkce Item vrátí jednu položku z vícehodnotového řetězce nebo atributu.|
|[Join](#join) (Spojení) |Join () je podobný jako Append (), s tím rozdílem, že může zkombinovat více hodnot **zdrojového** řetězce do jednoho řetězce a každá hodnota bude oddělena řetězcem **oddělovače** .| 
|[Zbývá](#left)|Funkce Left vrátí zadaný počet znaků nalevo od řetězce.|
|[Středně](#mid) |Vrátí podřetězec zdrojové hodnoty. Podřetězec je řetězec, který obsahuje pouze některé znaky ze zdrojového řetězce.|
|[NormalizeDiacritics](#normalizediacritics)|Vyžaduje jeden řetězcový argument. Vrátí řetězec, ale se všemi diakritikami nahrazenými ekvivalentními nediakritikou znaků.|
|[Mění](#not) |Převrátí logickou hodnotu **zdroje**. Pokud je hodnota **zdroje** "*true*", vrátí "*false*". V opačném případě vrátí "*true*".| 
|[RemoveDuplicates –](#removeduplicates)|Funkce RemoveDuplicates – přebírá řetězec s více hodnotami a ověří, zda jsou všechny hodnoty jedinečné.| 
|[Náhrady](#replace) |Nahradí hodnoty v rámci řetězce. | 
|[SelectUniqueValue](#selectuniquevalue)|Vyžaduje minimálně dva argumenty, což jsou jedinečná pravidla generování hodnot definovaná pomocí výrazů. Funkce vyhodnocuje všechna pravidla a potom kontroluje hodnotu vygenerovanou pro jedinečnost v cílové aplikaci/adresáři.| 
|[SingleAppRoleAssignment](#singleapproleassignment)|Vrátí jeden appRoleAssignment ze seznamu všech appRoleAssignments přiřazených uživateli pro danou aplikaci.| 
|[Rozdělení](#split)|Rozdělí řetězec na pole s více hodnotami pomocí zadaného oddělovače.|
|[StringFromSID](#stringfromsid)|Funkce StringFromSid převede pole bajtů obsahující identifikátor zabezpečení na řetězec.| 
|[StripSpaces](#stripspaces) |Odebere ze zdrojového řetězce všechny znaky ("").| 
|[Přepnutí](#switch)|Když hodnota **zdroje** odpovídá **klíči**, vrátí **hodnotu** pro tento **klíč**. | 
|[ToLower](#tolower)|Převezme hodnotu *zdrojového* řetězce a převede ji na malý případ pomocí pravidel jazykové verze, které jsou určeny.| 
|[ToUpper](#toupper)|Převezme hodnotu *zdrojového* řetězce a převede ji na velká písmena pomocí pravidel jazykové verze, které jsou určeny.|
|[Sklon](#trim)|Funkce Trim odstraní úvodní a koncové prázdné znaky z řetězce.|
|[Word](#word)|Funkce Word vrátí slovo obsažené v řetězci na základě parametrů popisujících oddělovače, které se mají použít, a číslo slova, které se má vrátit.|

---
### <a name="append"></a>Připojit
**Slouží**<br> Připojit (zdroj, přípona)

**Popis:**<br> Převezme hodnotu zdrojového řetězce a připojí příponu na konec IT.

**Ukazatelů**<br> 

   | Název | Požadováno/opakování | Typ | Poznámky |
   | --- | --- | --- | --- |
   | **Zdrojová** |Vyžadováno |Řetězec |Obvykle název atributu ze zdrojového objektu. |
   | **auditování** |Vyžadováno |Řetězec |Řetězec, který chcete připojit ke konci zdrojové hodnoty. |

---
### <a name="bitand"></a>BitAnd
**Popis:**  
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
**Popis:**  
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
**Popis:**  
Funkce ConvertFromBase64 převede zadanou hodnotu v kódování Base64 na běžný řetězec.

**Syntaktick**  
`str ConvertFromBase64(str source)` – předpokládá kódování Unicode.  
`str ConvertFromBase64(str source, enum Encoding)`

* Zdroj: řetězec kódovaný v kódování Base64  
* Kódování: Unicode, ASCII, UTF8

**Příklad**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Oba příklady vrátí "*Hello World!*"

---
### <a name="converttobase64"></a>ConvertToBase64
**Popis:**  
Funkce ConvertToBase64 převede řetězec na řetězec Unicode base64.  
Převede hodnotu pole celých čísel na odpovídající řetězcovou reprezentaci, která je zakódována pomocí číslic-Base-64.

**Syntaktick**  
`str ConvertToBase64(str source)`

**Příklad:**  
`ConvertToBase64("Hello world!")`  
Vrátí "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Popis:**  
Funkce ConvertToUTF8Hex převede řetězec na šestnáctkovou hodnotu v kódování UTF8.

**Syntaktick**  
`str ConvertToUTF8Hex(str source)`

**Mark**  
Výstupní formát této funkce se používá Azure Active Directory jako formát atributu DN.

**Příklad:**  
`ConvertToUTF8Hex("Hello world!")`  
Vrátí 48656C6C6F20776F726C6421

---
### <a name="count"></a>Počet
**Popis:**  
Funkce Count vrátí počet prvků v vícehodnotovém atributu.

**Syntaktick**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>CStr
**Popis:**  
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
**Popis:**  
Funkce DateFromNum převede hodnotu ve formátu data AD na typ DateTime.

**Syntaktick**  
`dt DateFromNum(num value)`

**Příklad:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Vrátí hodnotu DateTime představující 2012-01-01 23:00:00.

---
### <a name="dncomponent"></a>DNComponent
**Popis:**  
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
**Popis:**  
Funkce Error slouží k vrácení vlastní chyby.

**Syntaktick**  
`void Error(str ErrorMessage)`

**Příklad:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Pokud atribut Account není přítomen, vyvolejte chybu objektu.

---
### <a name="formatdatetime"></a>FormatDateTime
**Slouží**<br> FormatDateTime (zdroj, inputFormat, outputFormat)

**Popis:**<br> Vezme řetězec data z jednoho formátu a převede ho do jiného formátu.

**Ukazatelů**<br> 

   | Název | Požadováno/opakování | Typ | Poznámky |
   | --- | --- | --- | --- |
   | **Zdrojová** |Vyžadováno |Řetězec |Obvykle název atributu ze zdrojového objektu. |
   | **inputFormat** |Vyžadováno |Řetězec |Byl očekáván formát zdrojové hodnoty. Podporované formáty najdete v tématu [/dotnet/Standard/Base-Types/Custom-Date-and-Time-Format-Strings](/dotnet/standard/base-types/custom-date-and-time-format-strings). |
   | **outputFormat** |Vyžadováno |Řetězec |Formát data výstupu. |

---
### <a name="guid"></a>Identifikátor GUID
**Popis:**  
Identifikátor GUID funkce vygeneruje nový náhodný identifikátor GUID.

**Syntaktick**  
`str Guid()`

---
### <a name="iif"></a>IIF
**Popis:**  
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
**Popis:**  
Funkce InStr vyhledá první výskyt podřetězce v řetězci.

**Syntaktick**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start, enum compare)`

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
**Popis:**  
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
**Popis:**  
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
**Popis:**  
Pokud se výraz vyhodnotí jako řetězec, který není null a není prázdný, vrátí funkce vracející hodnotu true.

**Syntaktick**  
`bool IsPresent(var expression)`

**Mark**  
Inverzní funkce k této funkci má název IsNullOrEmpty.

**Příklad:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Položka
**Popis:**  
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
**Popis:**  
Pokud je možné výraz vyhodnotit na typ řetězce, pak je funkce typu String vyhodnocena jako true.

**Syntaktick**  
`bool IsString(var expression)`

**Mark**  
Slouží k určení, zda může být CStr () úspěšné k analýze výrazu.

---
### <a name="join"></a>Spojit
**Slouží**<br> Join (oddělovač, source1, SOURCE2,...)

**Popis:**<br> Join () je podobný jako Append (), s tím rozdílem, že může zkombinovat více hodnot **zdrojového** řetězce do jednoho řetězce a každá hodnota bude oddělena řetězcem **oddělovače** .

Pokud je jednou ze zdrojových hodnot atribut s více hodnotami, pak se všechny hodnoty v tomto atributu spojí dohromady, oddělené hodnotou oddělovače.

**Ukazatelů**<br> 

   | Název | Požadováno/opakování | Typ | Poznámky |
   | --- | --- | --- | --- |
   | **oddělování** |Vyžadováno |Řetězec |Řetězec, který se používá k oddělení zdrojových hodnot při zřetězení do jednoho řetězce. Může být "", pokud není vyžadován oddělovač. |
   | **source1 ... sourceN** |Povinný, proměnlivý počet pokusů |Řetězec |Řetězcové hodnoty, které se mají spojit dohromady. |

---
### <a name="left"></a>Left
**Popis:**  
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
**Slouží**<br> Mid (zdroj, začátek, délka)

**Popis:**<br> Vrátí podřetězec zdrojové hodnoty. Podřetězec je řetězec, který obsahuje pouze některé znaky ze zdrojového řetězce.

**Ukazatelů**<br> 

   | Název | Požadováno/opakování | Typ | Poznámky |
   | --- | --- | --- | --- |
   | **Zdrojová** |Vyžadováno |Řetězec |Obvykle název atributu. |
   | **Čína** |Vyžadováno |integer |Index ve **zdrojovém** řetězci, ve kterém by měl být spuštěný dílčí řetězec První znak v řetězci bude mít index 1, druhý znak bude mít index 2 atd. |
   | **length** |Vyžadováno |integer |Délka podřetězce Pokud délka končí mimo **zdrojový** řetězec, funkce vrátí podřetězec z **počátečního** indexu do konce **zdrojového** řetězce. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Slouží**<br> NormalizeDiacritics (zdroj)

**Popis:**<br> Vyžaduje jeden řetězcový argument. Vrátí řetězec, ale se všemi diakritikami nahrazenými ekvivalentními nediakritikou znaků. Obvykle slouží k převodu křestních jmen a příjmení obsahujících diakritická znaménka (značky akcentů) na platné hodnoty, které lze použít v různých uživatelských identifikátorech, jako jsou hlavní názvy uživatelů, názvy účtů SAM a e-mailové adresy.

**Ukazatelů**<br> 

   | Název | Požadováno/opakování | Typ | Poznámky |
   | --- | --- | --- | --- |
   | **Zdrojová** |Vyžadováno |Řetězec | Obvykle se jedná o křestní jméno nebo název atributu příjmení. |

---
### <a name="not"></a>Not
**Slouží**<br> Nepoužívá se (zdroj)

**Popis:**<br> Převrátí logickou hodnotu **zdroje**. Pokud je hodnota **zdroje** "*true*", vrátí "*false*". V opačném případě vrátí "*true*".

**Ukazatelů**<br> 

   | Název | Požadováno/opakování | Typ | Poznámky |
   | --- | --- | --- | --- |
   | **Zdrojová** |Vyžadováno |Logický řetězec |Očekávané **zdrojové** hodnoty jsou "true" nebo "false". |

---
### <a name="removeduplicates"></a>RemoveDuplicates –
**Popis:**  
Funkce RemoveDuplicates – přebírá řetězec s více hodnotami a ověří, zda jsou všechny hodnoty jedinečné.

**Syntaktick**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Příklad:**  
`RemoveDuplicates([proxyAddresses])`  
Vrátí upravený atribut proxyAddress, ve kterém byly odstraněny všechny duplicitní hodnoty.

---
### <a name="replace"></a>Nahrazení
**Slouží**<br> Replace (Source, oldValue, vzor Regex, regexGroupName, replacementValue, replacementAttributeName, Template)

**Popis:**<br>
Nahradí hodnoty v rámci řetězce. Funguje různě v závislosti na zadaných parametrech:

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
**Slouží**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Popis:**<br> Vyžaduje minimálně dva argumenty, což jsou jedinečná pravidla generování hodnot definovaná pomocí výrazů. Funkce vyhodnocuje všechna pravidla a potom kontroluje hodnotu vygenerovanou pro jedinečnost v cílové aplikaci/adresáři. První nalezená jedinečná hodnota bude vrácena. Pokud všechny hodnoty již v cíli existují, bude položka získat uloží a důvod se zaznamená do protokolu auditu. Není k dispozici horní mez počtu argumentů, které by bylo možné poskytnout.

> [!NOTE]
> - Toto je funkce nejvyšší úrovně, nemůže být vnořená.
> - Tuto funkci nelze použít pro atributy, které mají odpovídající prioritu.  
> - Tato funkce je určena pouze pro vytváření záznamů. Při použití s atributem nastavte vlastnost **použít mapování** na **pouze při vytváření objektu**.
> - Tato funkce je momentálně podporovaná jenom pro zřizování uživatelů z Workday do služby Active Directory. Nedá se použít s jinými zřizovacími aplikacemi. 


**Ukazatelů**<br> 

   | Název | Požadováno/opakování | Typ | Poznámky |
   | --- | --- | --- | --- |
   | **uniqueValueRule1 ... uniqueValueRuleN** |Vyžaduje se aspoň 2, bez horní meze. |Řetězec | Seznam pravidel generování jedinečných hodnot, které se mají vyhodnotit |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Slouží**<br> SingleAppRoleAssignment ([appRoleAssignments])

**Popis:**<br> Vrátí jeden appRoleAssignment ze seznamu všech appRoleAssignments přiřazených uživateli pro danou aplikaci. Tato funkce je nutná k převedení objektu appRoleAssignments na jeden řetězec názvu role. Doporučujeme, abyste zajistili, že se jednomu uživateli přiřadí pouze jedna appRoleAssignment a pokud je přiřazeno více rolí, vrácený řetězec role nemusí být předvídatelný. 

**Ukazatelů**<br> 

  | Název | Požadováno/opakování | Typ | Poznámky |
  |--- | --- | --- | --- |
  | **AppRoleAssignments** |Vyžadováno |Řetězec |objekt **[appRoleAssignments]** . |

---
### <a name="split"></a>Rozdělení
**Slouží**<br> Split (Source, oddělovač)

**Popis:**<br> Rozdělí řetězec na pole s více hodnotami pomocí zadaného oddělovače.

**Ukazatelů**<br> 

   | Název | Požadováno/opakování | Typ | Poznámky |
   | --- | --- | --- | --- |
   | **Zdrojová** |Vyžadováno |Řetězec |**zdrojová** hodnota, která se má aktualizovat |
   | **Oddělovač** |Vyžadováno |Řetězec |Určuje znak, který bude použit k rozdělení řetězce (například: ","). |

---
### <a name="stringfromsid"></a>StringFromSid
**Popis:**  
Funkce StringFromSid převede pole bajtů obsahující identifikátor zabezpečení na řetězec.

**Syntaktick**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces
**Slouží**<br> StripSpaces (zdroj)

**Popis:**<br> Odebere ze zdrojového řetězce všechny znaky ("").

**Ukazatelů**<br> 

   | Název | Požadováno/opakování | Typ | Poznámky |
   | --- | --- | --- | --- |
   | **Zdrojová** |Vyžadováno |Řetězec |**zdrojová** hodnota, která se má aktualizovat |

---
### <a name="switch"></a>Přepínač
**Slouží**<br> Switch (zdroj; defaultValue; klíč1; hodnota1; key2; hodnota2;...)

**Popis:**<br> Když hodnota **zdroje** odpovídá **klíči**, vrátí **hodnotu** pro tento **klíč**. Pokud **zdrojová** hodnota neodpovídá žádným klíčům, vrátí hodnotu **DefaultValue**.  Parametry **klíče** a **hodnoty** se musí vždycky nacházet ve dvojicích. Funkce vždycky očekává sudý počet parametrů.

**Ukazatelů**<br> 

   | Název | Požadováno/opakování | Typ | Poznámky |
   | --- | --- | --- | --- |
   | **Zdrojová** |Vyžadováno |Řetězec |**Zdrojová** hodnota, která se má ověřit |
   | **Hodnot** |Volitelné |Řetězec |Výchozí hodnota, která se má použít, pokud zdroj neodpovídá žádným klíčům Může být prázdný řetězec (""). |
   | **zkrat** |Vyžadováno |Řetězec |**Klíč** pro porovnání **zdrojové** hodnoty s. |
   | **value** |Vyžadováno |Řetězec |Nahrazující hodnota pro **zdroj** , který odpovídá klíči. |

---
### <a name="tolower"></a>ToLower
**Slouží**<br> ToLower (zdroj, jazyková verze)

**Popis:**<br> Převezme hodnotu *zdrojového* řetězce a převede ji na malý případ pomocí pravidel jazykové verze, které jsou určeny. Pokud nejsou zadány žádné informace o *jazykové verzi* , pak použije invariantní jazykovou verzi.

**Ukazatelů**<br> 

   | Název | Požadováno/opakování | Typ | Poznámky |
   | --- | --- | --- | --- |
   | **Zdrojová** |Vyžadováno |Řetězec |Obvykle název atributu ze zdrojového objektu |
   | **jazykových** |Volitelné |Řetězec |Formát pro název jazykové verze založený na RFC 4646 je *languagecode2-Country/regioncode2*, kde *languagecode2* je kód jazyka dvou písmen a *země/regioncode2* je kód subjazykové verze se dvěma písmeny. Mezi příklady patří ja-JP pro japonštinu (Japonsko) a EN-US pro angličtinu (USA). V případech, kdy kód jazyka se dvěma písmeny není k dispozici, je použit kód o třech písmenech odvozený z ISO 639-2.|

---

### <a name="toupper"></a>ToUpper
**Slouží**<br> ToUpper (zdroj, jazyková verze)

**Popis:**<br> Převezme hodnotu *zdrojového* řetězce a převede ji na velká písmena pomocí pravidel jazykové verze, které jsou určeny. Pokud nejsou zadány žádné informace o *jazykové verzi* , pak použije invariantní jazykovou verzi.

**Ukazatelů**<br> 

  | Název | Požadováno/opakování | Typ | Poznámky |
  | --- | --- | --- | --- |
  | **Zdrojová** |Vyžadováno |Řetězec |Obvykle název atributu ze zdrojového objektu. |
  | **jazykových** |Volitelné |Řetězec |Formát pro název jazykové verze založený na RFC 4646 je *languagecode2-Country/regioncode2*, kde *languagecode2* je kód jazyka dvou písmen a *země/regioncode2* je kód subjazykové verze se dvěma písmeny. Mezi příklady patří ja-JP pro japonštinu (Japonsko) a EN-US pro angličtinu (USA). V případech, kdy kód jazyka se dvěma písmeny není k dispozici, je použit kód o třech písmenech odvozený z ISO 639-2.|

---

### <a name="trim"></a>Trim
**Popis:**  
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
**Popis:**  
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
### <a name="strip-known-domain-name"></a>Název známé domény
Aby bylo možné získat uživatelské jméno, je nutné odstranit známý název domény z e-mailu uživatele. <br>
Pokud je doména například "contoso.com", můžete použít následující výraz:

**Vyjádření** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Vzorový vstup/výstup:** <br>

* **Vstup** (pošta): " john.doe@contoso.com "
* **Výstup**: Jan. Chvojková

### <a name="append-constant-suffix-to-user-name"></a>Připojit konstantní příponu k uživatelskému jménu
Pokud používáte izolovaný prostor Salesforce, možná budete muset před synchronizací přidat další příponu všem vašim uživatelským jménům.

**Vyjádření** <br>
`Append([userPrincipalName], ".test")`

**Vzorový vstup/výstup:** <br>

* **Vstup**: (userPrincipalName): " John.Doe@contoso.com "
* **Výstup**: " John.Doe@contoso.com.test "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generovat alias uživatele zřetězením částí jména a příjmení
Alias uživatele musíte vygenerovat zadáním prvních 3 písmen křestního jména uživatele a prvních 5 písmen příjmení uživatele.

**Vyjádření** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Vzorový vstup/výstup:** <br>

* **Vstup** (křestní jméno): "Jan"
* **Vstup** (příjmení): "Chvojková"
* **Výstup**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Odebrat diakritická znaménka z řetězce
Je nutné nahradit znaky obsahující diakritická znaménka stejnými znaky, které neobsahují značky akcentů.

**Vyjádření** <br>
NormalizeDiacritics ([křestní jméno])

**Vzorový vstup/výstup:** <br>

* **Vstup** (křestní jméno): "Zoë"
* **Výstup**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Rozdělit řetězec do pole s více hodnotami
Musíte vzít seznam řetězců oddělených čárkami a rozdělit je do pole, které se dá zapojit do vícehodnotového atributu, jako je atribut PermissionSet služby Salesforce. V tomto příkladu se v extensionAttribute5 ve službě Azure AD nastavil seznam sad oprávnění.

**Vyjádření** <br>
Split ([extensionAttribute5]; ";")

**Vzorový vstup/výstup:** <br>

* **Vstup** (extensionAttribute5): "PermissionSetOne, PermissionSetTwo"
* **Výstup**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Datum výstupu jako řetězec v určitém formátu
Chcete odesílat data do aplikace SaaS v určitém formátu. <br>
Například chcete formátovat data pro ServiceNow.

**Vyjádření** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Vzorový vstup/výstup:**

* **Vstup** (extensionAttribute1): "20150123105347.1 z"
* **Výstup**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Nahraďte hodnotu na základě předdefinované sady možností.

Je potřeba definovat časové pásmo uživatele na základě kódu stavu uloženého ve službě Azure AD. <br>
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
* **Výstup**: " john.smith@contoso.com "

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generovat jedinečnou hodnotu pro atribut userPrincipalName (UPN)
Na základě křestního jména a jména uživatele a jména a příjmení je potřeba vygenerovat hodnotu pro atribut hlavního názvu uživatele (UPN) a před přiřazením hodnoty k atributu hlavního názvu uživatele vyhledat jeho jedinečnost v cílovém adresáři služby AD.

**Vyjádření** <br>

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


## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)