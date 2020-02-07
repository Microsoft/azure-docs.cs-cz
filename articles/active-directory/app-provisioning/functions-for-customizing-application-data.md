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
ms.date: 02/05/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: a01f7f48dd93983edf4be4b797f62afede273c66
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066665"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Zápis výrazů pro mapování atributů v Azure Active Directory
Při konfiguraci zřizování pro aplikace SaaS, je jedním z typů mapování atributů, které můžete zadat mapování výrazu. Pro ty musíte napsat skript jako výraz, který umožňuje transformovat data uživatelů na formáty, které jsou více přijatelné pro aplikace SaaS.

## <a name="syntax-overview"></a>Přehled syntaxe
Syntaxe výrazů pro mapování atributů je připomínající Visual Basic pro funkce Applications (VBA).

* Celý výraz musí být definován jako funkce, které tvoří název, za nímž následuje argumenty v závorkách: <br>
  *Functions (`<<argument 1>>``<<argument N>>`)*
* Může vnořit do jiné funkce. Například: <br> *FunctionOne (FunctionTwo (`<<argument1>>`))*
* Tři různé typy argumentů můžete předat do funkce:
  
  1. Atributy, které musí být uzavřeny do hranatých závorek. Příklad: [attributeName]
  2. Řetězcové konstanty, které musí být umístěn do dvojitých uvozovek. Příklad: "USA"
  3. Další funkce. Například: FunctionOne (`<<argument1>>`, FunctionTwo (`<<argument2>>`))
* Pro řetězcové konstanty Pokud potřebujete zpětného lomítka (\) nebo uvozovky (") v řetězci, se musejí být uvozeny symbol zpětného lomítka (\). Příklad: "název společnosti: \\" contoso\\""

## <a name="list-of-functions"></a>Seznam funkcí
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [NumFromDate](#numfromdate) &nbsp;&nbsp;&nbsp;&nbsp;[RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

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
**Slouží**<br> BitAnd (hodnota1, hodnota2)

**Název**<br> Tato funkce převede oba parametry do binární reprezentace a nastaví bit na:

0 – Pokud má jedna nebo obě z odpovídajících bitů v hodnota1 a hodnota2 hodnotu 0                                                  
1 – Pokud jsou obě odpovídající bity 1.                                    

Jinými slovy, vrátí 0 ve všech případech s výjimkou toho, že odpovídající bity obou parametrů jsou 1.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **Hodnota1** |Požaduje se |počet |Číselná hodnota, která by měla být AND'ed s hodnota2|
| **Argument** |Požaduje se |počet |Číselná hodnota, která má být AND'ed s hodnota1|

**Příklad:**<br>
BitAnd (& HF, & HF7)                                                                                
11110111 a 00000111 = 00000111, takže BitAnd vrátí hodnotu 7, binární hodnota 00000111

---
### <a name="cbool"></a>CBool
**Slouží**<br> CBool (výraz)

**Název**<br> Funkce CBool vrátí logickou hodnotu založenou na vyhodnoceném výrazu. Pokud je výraz vyhodnocen jako nenulová hodnota, pak funkce CBool vrátí hodnotu true, jinak vrátí hodnotu false..

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **vyjádření** |Požaduje se | expression | Libovolný platný výraz |

**Příklad:**<br>
CBool ([attribute1] = [attribute2])                                                                    
Vrátí hodnotu true, pokud mají oba atributy stejnou hodnotu.

---
### <a name="coalesce"></a>COALESCE
**Slouží**<br> COALESCE (source1; SOURCE2;...; defaultValue)

**Název**<br> Vrátí první hodnotu zdroje, která není NULL. Pokud jsou všechny argumenty NULL a jsou přítomny hodnoty defaultValue, vrátí se hodnota defaultValue. Pokud jsou všechny argumenty NULL a vlastnost defaultValue není k dispozici, funkce coalesce vrátí hodnotu NULL.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **source1 ... sourceN** | Požaduje se | Řetězec |Povinný, proměnlivý počet opakování. Obvykle název atributu ze zdrojového objektu. |
| **Hodnot** | Nepovinné | Řetězec | Výchozí hodnota, která se má použít, pokud jsou všechny zdrojové hodnoty NULL. Může být prázdný řetězec ("").

---
### <a name="converttobase64"></a>ConvertToBase64
**Slouží**<br> ConvertToBase64 (zdroj)

**Název**<br> Funkce ConvertToBase64 převede řetězec na řetězec Unicode base64.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **Zdrojová** |Požaduje se |Řetězec |Řetězec, který má být převeden na základní 64|

**Příklad:**<br>
ConvertToBase64 ("Hello World!")                                                                                                        
Vrátí "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Slouží**<br> ConvertToUTF8Hex (zdroj)

**Název**<br> Funkce ConvertToUTF8Hex převede řetězec na šestnáctkovou hodnotu v kódování UTF8.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **Zdrojová** |Požaduje se |Řetězec |Řetězec, který se má převést na UTF8 hex|

**Příklad:**<br>
ConvertToUTF8Hex ("Hello World!")                                                                                                         
Returns 48656C6C6F20776F726C6421

---
### <a name="count"></a>Počet
**Slouží**<br> Count (atribut)

**Název**<br> Funkce Count vrátí počet prvků v vícehodnotovém atributu.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **přidělen** |Požaduje se |přidělen |Vícehodnotový atribut, který bude obsahovat elementy počítané|

---
### <a name="cstr"></a>CStr
**Slouží**<br> CStr (hodnota)

**Název**<br> Funkce CStr převede hodnotu na datový typ String.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **value** |Požaduje se | Číselná, referenční nebo logická hodnota | Může to být číselná hodnota, odkazový atribut nebo logická hodnota. |

**Příklad:**<br>
CStr ([DN])                                                            
Vrátí "CN = Jan, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Slouží**<br> DateFromNum (hodnota)

**Název**<br> Funkce DateFromNum převede hodnotu ve formátu data AD na typ DateTime.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **value** |Požaduje se | Datum | Datum reklamy, které má být převedeno na typ DateTime |

**Příklad:**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum(129699324000000000)                                                            
Vrátí hodnotu DateTime představující 2012-01-01 23:00:00.

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
**Slouží**<br> GUID ()

**Název**<br> Identifikátor GUID funkce vygeneruje nový náhodný identifikátor GUID.

---
### <a name="instr"></a>InStr
**Slouží**<br> InStr (hodnota1, hodnota2, Start, compareType)

**Název**<br> Funkce InStr vyhledá první výskyt podřetězce v řetězci.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **Hodnota1** |Požaduje se |Řetězec |Řetězec, který má být prohledán |
| **Argument** |Požaduje se |Řetězec |Řetězec, který se má najít |
| **start** |Nepovinné |Integer |Počáteční pozice pro vyhledání podřetězce|
| **compareType** |Nepovinné |Výčet |Může být vbTextCompare nebo vbBinaryCompare |

**Příklad:**<br>
InStr ("Rychlá hnědá Fox", "Rychlá")                                                                             
Evalues na 5

InStr ("opakuje", "e"; 3; vbBinaryCompare)                                                                                  
Vyhodnotí na 7

---
### <a name="isnull"></a>IsNull
**Slouží**<br> IsNull (výraz)

**Název**<br> Pokud je výraz vyhodnocen jako null, funkce IsNull vrátí hodnotu true. U atributu je hodnota null vyjádřena nepřítomností atributu.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **vyjádření** |Požaduje se |expression |Výraz, který se má vyhodnotit |

**Příklad:**<br>
IsNull ([DisplayName])                                                                                                
Vrátí hodnotu true, pokud atribut není přítomen.

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Slouží**<br> IsNullOrEmpty (výraz)

**Název**<br> Pokud má výraz hodnotu null nebo je prázdný řetězec, vrátí funkce IsNullOrEmpty hodnotu true. U atributu by se to mělo vyhodnotit na hodnotu true, pokud atribut chybí nebo je přítomen, ale je to prázdný řetězec.
Inverzní část této funkce je pojmenována jako.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **vyjádření** |Požaduje se |expression |Výraz, který se má vyhodnotit |

**Příklad:**<br>
IsNullOrEmpty ([DisplayName])                                               
Vrátí hodnotu true, pokud atribut není přítomen, nebo je prázdný řetězec.

---
### <a name="ispresent"></a>K dispozici
**Slouží**<br> K dispozici (výraz)

**Název**<br> Pokud se výraz vyhodnotí jako řetězec, který není null a není prázdný, vrátí funkce vracející hodnotu true. Inverzní funkce k této funkci má název IsNullOrEmpty.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **vyjádření** |Požaduje se |expression |Výraz, který se má vyhodnotit |

**Příklad:**<br>
Switch (přítomen ([directManager]); [directManager]; ([skiplevelManager]); [skiplevelManager]; ([režisér]); [Director])

---
### <a name="isstring"></a>Řetězec
**Slouží**<br> Řetězec (výraz)

**Název**<br> Pokud je možné výraz vyhodnotit na typ řetězce, pak je funkce typu String vyhodnocena jako true.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **vyjádření** |Požaduje se |expression |Výraz, který se má vyhodnotit |

---
### <a name="item"></a>Položka
**Slouží**<br> Item (atribut; index)

**Název**<br> Funkce Item vrátí jednu položku z vícehodnotového řetězce nebo atributu.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **přidělen** |Požaduje se |Atribut |Vícehodnotový atribut, který má být prohledán |
| **indexovacím** |Požaduje se |Integer | Index položky v řetězci s více hodnotami|

**Příklad:**<br>
Item ([proxyAddresses]; 1)

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
**Slouží**<br> Left (řetězec; NumChars)

**Název**<br> Funkce Left vrátí zadaný počet znaků nalevo od řetězce. Pokud numChars = 0, vrátí prázdný řetězec.
Pokud numChars < 0, vrátí se vstupní řetězec.
Pokud má řetězec hodnotu null, vrátí prázdný řetězec.
Pokud řetězec obsahuje méně znaků než číslo zadané v numChars, vrátí se řetězec shodný s řetězcem (tj. obsahující všechny znaky v parametru 1).

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **Řetezce** |Požaduje se |Atribut | Řetězec, ze kterého se mají vracet znaky |
| **NumChars** |Požaduje se |Integer | Číslo určující počet znaků, které mají být vráceny od začátku (vlevo) řetězce|

**Příklad:**<br>
Vlevo ("Jan Novák"; 3)                                                            
Vrátí "Joh"

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
### <a name="numfromdate"></a>NumFromDate
**Slouží**<br> NumFromDate (hodnota)

**Název**<br> Funkce NumFromDate převede hodnotu DateTime na formát služby Active Directory, který je vyžadován pro nastavení atributů jako [accountExpires](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires). Pomocí této funkce můžete převést hodnoty DateTime obdržené z cloudových aplikací pro HR, jako je třeba Workday a SuccessFactors, do jejich ekvivalentu reklamy. 

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **value** |Požaduje se | Řetězec | Řetězec data a času v podporovaném formátu. Podporované formáty najdete v tématu https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx. |

**Příklad:**<br>
* Příklad pracovního dne <br>
  Za předpokladu, že chcete mapovat atribut *ContractEndDate* z Workday, který je ve formátu *2020-12-31-08:00* až *accountExpires* pole ve službě AD, zde je postup, jak můžete tuto funkci použít a změnit posun časového pásma tak, aby odpovídal vašemu národnímu prostředí. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* Příklad SuccessFactors <br>
  Za předpokladu, že chcete mapovat atribut *EndDate* z SuccessFactors, který je ve formátu *M/d/rrrr hh: mm: SS TT* na *accountExpires* pole ve službě AD, zde je postup použití této funkce a změna posunu časového pásma tak, aby odpovídala vašemu národnímu prostředí.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates –
**Slouží**<br> RemoveDuplicates – (atribut)

**Název**<br> Funkce RemoveDuplicates – přebírá řetězec s více hodnotami a ověří, zda jsou všechny hodnoty jedinečné.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **přidělen** |Požaduje se |Vícehodnotový atribut |Vícehodnotový atribut, který bude mít odebrané duplicity|

**Příklad:**<br>
RemoveDuplicates – ([proxyAddresses])                                                                                                       
Vrátí upravený atribut proxyAddress, ve kterém se odebraly všechny duplicitní hodnoty.

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
| --- | --- | --- | --- |
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

**Název**<br> Když hodnota **zdroje** odpovídá **klíči**, vrátí **hodnotu** pro tento **klíč**. Pokud **zdrojová** hodnota neodpovídá žádným klíčům, vrátí hodnotu **DefaultValue**.  Parametry **klíče** a **hodnoty** se musí vždycky nacházet ve dvojicích. Funkce očekává vždy sudý počet parametrů. Funkce by neměla být používána pro referenční atributy, jako je například správce. 

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **Zdrojová** |Požaduje se |Řetězec |**Zdrojová** hodnota, která se má aktualizovat |
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
### <a name="word"></a>Word
**Slouží**<br> Word (řetězec, WordNumber, oddělovače)

**Název**<br> Funkce Word vrátí slovo obsažené v řetězci na základě parametrů popisujících oddělovače, které se mají použít, a číslo slova, které se má vrátit. Každý řetězec znaků v řetězci, oddělený jedním ze znaků v oddělovačích, je identifikován jako slova:

Pokud číslo < 1, vrátí prázdný řetězec.
Pokud má řetězec hodnotu null, vrátí prázdný řetězec.
Pokud řetězec obsahuje méně než čísla slov nebo řetězec neobsahuje žádná slova identifikovaná oddělovači, je vrácen prázdný řetězec.

**Ukazatelů**<br> 

| Název | Požadovaný / s opakováním | Typ | Poznámky: |
| --- | --- | --- | --- |
| **Řetezce** |Požaduje se |Vícehodnotový atribut |Řetězec, ze kterého se má vrátit slovo|
| **WordNumber** |Požaduje se | Integer | Číslo určující, které číslo slova se má vrátit|
| **oddělovače** |Požaduje se |Řetězec| Řetězec představující oddělovače, které se mají použít k identifikaci slov|

**Příklad:**<br>
Word ("Rychlá hnědá Fox", 3, "")                                                                                       
Vrátí "Brown"

Word ("This, String! má & mnoho oddělovačů", 3, ",! & #").                                                                       
Vrátí "has"

---

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

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Hodnota pro poštu flowu, pokud není NULL, jinak Flow userPrincipalName
Chcete-li, aby se atribut mail nacházel, je-li k dispozici. Pokud není, chcete místo toho Flow použít hodnotu userPrincipalName.

**Vyjádření** <br>
`Coalesce([mail],[userPrincipalName])`

**Vzorový vstup/výstup:** <br>

* **Vstup** (mail): null
* **Input** (userPrincipalName): "John.Doe@contoso.com"
* **Výstup**: "John.Doe@contoso.com"

## <a name="related-articles"></a>Související články
* [Automatizace zřizování a rušení zřizování uživatelů pro aplikace SaaS](../app-provisioning/user-provisioning.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](../app-provisioning/customize-application-attributes.md)
* [Filtry oborů pro zřizování uživatelů](define-conditional-rules-for-provisioning-user-accounts.md)
* [Zapnutí automatického zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací pomocí SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Oznámení zřizování účtů](../app-provisioning/user-provisioning.md)
* [Seznam kurzů, jak integrovat aplikace SaaS](../saas-apps/tutorial-list.md)
