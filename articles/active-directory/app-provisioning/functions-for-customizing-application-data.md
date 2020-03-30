---
title: Zápis výrazů pro mapování atributů ve službě Azure AD
description: Zjistěte, jak pomocí mapování výrazů transformovat hodnoty atributů do přijatelného formátu během automatického zřizování objektů aplikace SaaS ve službě Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc41a18063202bfefb9ddf7238de17fc691984af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612139"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Zápis výrazů pro mapování atributů v Azure Active Directory
Při konfiguraci zřizování do aplikace SaaS je jedním z typů mapování atributů, které můžete zadat, mapování výrazů. Pro tyto musíte napsat skript jako výraz, který umožňuje transformovat data uživatelů do formátů, které jsou přijatelnější pro aplikaci SaaS.

## <a name="syntax-overview"></a>Přehled syntaxe
Syntaxe výrazů pro mapování atributů připomíná funkce jazyka Visual Basic for Applications (VBA).

* Celý výraz musí být definován z hlediska funkcí, které se skládají z názvu následovaného argumenty v závorcích: <br>
  *FunctionName(`<<argument 1>>``<<argument N>>`, )*
* Můžete vnořit funkce do sebe. Například: <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* Do funkcí můžete předat tři různé typy argumentů:
  
  1. Atributy, které musí být uzavřeny v hranatých závorkách. Příklad: [attributeName]
  2. Řetězcové konstanty, které musí být uzavřeny v uvozovkách. Například: "Spojené státy"
  3. Další funkce. Například: FunctionOne(`<<argument1>>`,`<<argument2>>`FunctionTwo( ))
* Pro řetězcové konstanty, pokud potřebujete zpětné lomítko ( \ ) nebo uvozovky ( " ) v řetězci, musí být uvozeno symbolem zpětného lomítka ( \ ). Například: "Název \\společnosti: "Contoso\\""

## <a name="list-of-functions"></a>Seznam funkcí
[Připojit](#append) &nbsp; &nbsp; [Count](#count) [BitAnd](#bitand) &nbsp; &nbsp; [CBool](#cbool) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [Coalesce](#coalesce) &nbsp; &nbsp; [CStr](#cstr) [ConvertToBase64](#converttobase64) &nbsp; [ConvertToUTF8Hex](#converttoutf8hex) BitAnd &nbsp; &nbsp; &nbsp; CBool &nbsp; &nbsp; Coalesce &nbsp; ConvertToBase64 ConvertToUTF8Hex Počet CStr &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [Guid](#guid) &nbsp; &nbsp; [DateFromNum](#datefromnum) &nbsp; &nbsp; [IIF](#iif) &nbsp; &nbsp; [InStr](#instr) &nbsp; [FormatDateTime](#formatdatetime) &nbsp; &nbsp; &nbsp; [IsNull](#isnull) [IsNullOrEmpty](#isnullorempty) DateFromNum &nbsp;FormatDateTime &nbsp; &nbsp; &nbsp; Identifikátor &nbsp; Guid &nbsp; &nbsp; IIF &nbsp; InStr &nbsp; IsNull IsNullOrEmpty &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [IsPresent](#ispresent) &nbsp; &nbsp; &nbsp; [IsString](#isstring) &nbsp; [Mid](#mid) [Item](#item) &nbsp; [Not](#not) [Left](#left) [Join](#join) [NormalizeDiacritics](#normalizediacritics) IsString &nbsp; &nbsp; položka &nbsp; spojit &nbsp; vlevo &nbsp; střední &nbsp; normalizediakritika není &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [Replace](#replace) &nbsp; [Split](#split) [RemoveDuplicates](#removeduplicates) &nbsp; [SelectUniqueValue](#selectuniquevalue) &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) Odstranitduplicates&nbsp; nahradit&nbsp; SelectUniqueValue&nbsp; &nbsp; SingleAppRoleAssignment Split&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [ Stripspaces](#stripspaces) &nbsp; &nbsp; &nbsp; [Word](#word) [Switch](#switch) &nbsp; [ToUpper](#toupper) [ToLower](#tolower) &nbsp; přepnout&nbsp; &nbsp; &nbsp; na&nbsp; dolní&nbsp; slovo&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;

---
### <a name="append"></a>Připojit
**Funkce:**<br> Append (zdroj, přípona)

**Popis:**<br> Převezme hodnotu zdrojového řetězce a připojí příponu na její konec.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Obvykle název atributu ze zdrojového objektu. |
| **Přípona** |Požaduje se |Řetězec |Řetězec, který chcete připojit na konec zdrojové hodnoty. |

---
### <a name="bitand"></a>BitAnd
**Funkce:**<br> BitAnd(value1, value2)

**Popis:**<br> Tato funkce převede oba parametry na binární reprezentaci a trochu nastaví na:

0 - pokud jeden nebo oba odpovídající bity v hodnotě1 a hodnota2 jsou 0                                                  
1 - pokud jsou oba odpovídající bity 1.                                    

Jinými slovy vrátí 0 ve všech případech s výjimkou, pokud jsou odpovídající bity obou parametrů 1.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **hodnota1** |Požaduje se |num |Číselná hodnota, která by měla být AND'ed s value2|
| **hodnota2** |Požaduje se |num |Číselná hodnota, která by měla být and'ed s value1|

**Příklad:**<br>
BitAnd (&HF, &HF7)                                                                                
11110111 A 00000111 = 00000111 tak BitAnd vrátí 7, binární hodnota 00000111

---
### <a name="cbool"></a>CBool
**Funkce:**<br> CBool(Výraz)

**Popis:**<br> CBool vrátí logickou hodnotu na základě vyhodnoceného výrazu. Pokud výraz vyhodnotí nenulovou hodnotu, pak CBool vrátí True, jinak vrátí False..

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Výraz** |Požaduje se | výraz | Libovolný platný výraz |

**Příklad:**<br>
CBool([atribut1] = [attribute2])                                                                    
Vrátí hodnotu True, pokud mají oba atributy stejnou hodnotu.

---
### <a name="coalesce"></a>Coalesce
**Funkce:**<br> Coalesce(source1, source2, ..., defaultValue)

**Popis:**<br> Vrátí první zdrojovou hodnotu, která není null. Pokud jsou všechny argumenty null a defaultValue je k dispozici, defaultValue bude vrácena. Pokud jsou všechny argumenty null a defaultValue není k dispozici, Coalesce vrátí NULL.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **zdroj1 ... sourceN** | Požaduje se | Řetězec |Povinné, proměnné počet opakování. Obvykle název atributu ze zdrojového objektu. |
| **Defaultvalue** | Nepovinné | Řetězec | Výchozí hodnota, která má být použita, pokud jsou všechny zdrojové hodnoty null. Může být prázdný řetězec ("").

---
### <a name="converttobase64"></a>Převést na základnu64
**Funkce:**<br> PřevéstToBase64(zdroj)

**Popis:**<br> Funkce ConvertToBase64 převede řetězec na řetězec Unicode base64.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Řetězec, který má být převeden na základnu 64|

**Příklad:**<br>
ConvertToBase64("Hello world!")                                                                                                        
Vrátí "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>PřevéstToUTF8Hex
**Funkce:**<br> PřevéstToUTF8Hex(zdroj)

**Popis:**<br> Funkce ConvertToUTF8Hex převede řetězec na hodnotu kódovku Hex UTF8.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Řetězec, který má být převeden na UTF8 Hex|

**Příklad:**<br>
ConvertToUTF8Hex("Hello world!")                                                                                                         
Vrátí 48656C6C6F20776F726C6421

---
### <a name="count"></a>Počet
**Funkce:**<br> Count(atribut)

**Popis:**<br> Funkce Počet vrátí počet prvků v atributu s více hodnotami.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Atribut** |Požaduje se |– atribut |Atribut s více hodnotami, který bude mít spočítané prvky|

---
### <a name="cstr"></a>Cstr
**Funkce:**<br> CStr(hodnota)

**Popis:**<br> Funkce CStr převede hodnotu na datový typ řetězce.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **value** |Požaduje se | číselné, referenční nebo logické | Může se jedná o číselnou hodnotu, atribut odkazu nebo logickou hodnotu. |

**Příklad:**<br>
CStr([dn])                                                            
Vrátí "cn=Joe,dc=contoso,dc=com"

---
### <a name="datefromnum"></a>DateFromNum
**Funkce:**<br> DateFromNum(hodnota)

**Popis:**<br> Funkce DateFromNum převede hodnotu ve formátu data služby AD na typ DateTime.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **value** |Požaduje se | Datum | Datum ad, které má být převedeno na typ DateTime |

**Příklad:**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum(1296993240000000000)                                                            
Vrátí hodnotu DateTime představující 2012-01-01 23:00:00.

---
### <a name="formatdatetime"></a>Formatdatetime
**Funkce:**<br> FormatDateTime (zdroj, inputFormat, outputFormat)

**Popis:**<br> Vezme řetězec kalendářního data z jednoho formátu a převede jej do jiného formátu.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Obvykle název atributu ze zdrojového objektu. |
| **inputFormat** |Požaduje se |Řetězec |Očekávaný formát zdrojové hodnoty. Podporované formáty naleznete [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)v tématu . |
| **outputFormat** |Požaduje se |Řetězec |Formát výstupního data. |

---
### <a name="guid"></a>Identifikátor GUID
**Funkce:**<br> Guid()

**Popis:**<br> Funkce Guid generuje nový náhodný identifikátor GUID

---
### <a name="iif"></a>Iif
**Funkce:**<br> IIF(condition,valueIfTrue,valueIfFalse)

**Popis:**<br> Funkce IIF vrátí jednu ze sady možných hodnot na základě zadané podmínky.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Podmínka** |Požaduje se |Proměnná nebo výraz |Libovolná hodnota nebo výraz, který lze vyhodnotit na hodnotu true nebo false. |
| **valueIfTrue** |Požaduje se |Proměnná nebo řetězec | Pokud se podmínka vyhodnotí jako true, vrácená hodnota. |
| **valueIfFalse** |Požaduje se |Proměnná nebo řetězec |Pokud se podmínka vyhodnotí jako false, vrácená hodnota.|

**Příklad:**<br>
IIF([země]="USA",[země],[oddělení])

---
### <a name="instr"></a>Instr
**Funkce:**<br> InStr(hodnota1,hodnota2,začátek,compareType)

**Popis:**<br> Funkce InStr vyhledá první výskyt podřetězce v řetězci.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **hodnota1** |Požaduje se |Řetězec |Řetězec, který má být prohledán |
| **hodnota2** |Požaduje se |Řetězec |Řetězec, který má být nalezen |
| **Spustit** |Nepovinné |Integer |Počáteční pozice pro nalezení podřetězce|
| **compareType** |Nepovinné |Výčet |Může být vbTextCompare nebo vbBinaryCompare |

**Příklad:**<br>
InStr("Rychlá hnědá liška",quick")                                                                             
Ehodnoty na 5

InStr("repEated","e",3,vbBinaryCompare)                                                                                  
Vyhodnotí se na 7

---
### <a name="isnull"></a>Isnull
**Funkce:**<br> Isnull(výraz)

**Popis:**<br> Pokud výraz vyhodnotí na Null, pak isnull funkce vrátí true. Pro atribut null je vyjádřena absence atributu.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Výraz** |Požaduje se |výraz |Výraz, který má být vyhodnocen |

**Příklad:**<br>
IsNull([displayName])                                                                                                
Vrátí hodnotu True, pokud atribut není k dispozici.

---
### <a name="isnullorempty"></a>IsNullorPrázdný
**Funkce:**<br> IsnullorEmpty(Výraz)

**Popis:**<br> Pokud je výraz null nebo prázdný řetězec, vrátí funkce IsNullOrEmpty hodnotu true. Pro atribut by to vyhodnotit True, pokud atribut chybí nebo je přítomen, ale je prázdný řetězec.
Inverzní funkce se nazývá IsPresent.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Výraz** |Požaduje se |výraz |Výraz, který má být vyhodnocen |

**Příklad:**<br>
IsNullOrEmpty([displayName])                                               
Vrátí hodnotu True, pokud atribut není přítomen nebo je prázdný řetězec.

---
### <a name="ispresent"></a>Ispresent
**Funkce:**<br> IsPresent(Výraz)

**Popis:**<br> Pokud výraz vyhodnotí řetězec, který není Null a není prázdný, pak ispresent funkce vrátí true. Inverzní funkce se nazývá IsNullOrEmpty.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Výraz** |Požaduje se |výraz |Výraz, který má být vyhodnocen |

**Příklad:**<br>
Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])

---
### <a name="isstring"></a>IsString
**Funkce:**<br> IsString(výraz)

**Popis:**<br> Pokud výraz lze vyhodnotit na typ řetězce, pak IsString funkce vyhodnotí True.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Výraz** |Požaduje se |výraz |Výraz, který má být vyhodnocen |

---
### <a name="item"></a>Položka
**Funkce:**<br> Položka(atribut, index)

**Popis:**<br> Funkce Item vrátí jednu položku z vícehodnotového řetězce/atributu.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Atribut** |Požaduje se |Atribut |Vícehodnotový atribut, který má být prohledán |
| **Index** |Požaduje se |Integer | Index na položku v řetězci s více hodnotami|

**Příklad:**<br>
Položka([proxyAdresy], 1)

---
### <a name="join"></a>Spojit
**Funkce:**<br> Join(oddělovač, zdroj1, zdroj2, ...)

**Popis:**<br> Join() je podobný Append(), s tím rozdílem, že může kombinovat více hodnot **zdrojového** řetězce do jednoho řetězce a každá hodnota bude oddělena **oddělovacím** řetězcem.

Pokud je jedna ze zdrojových hodnot atributem s více hodnotami, bude každá hodnota v tomto atributu spojena společně, oddělena hodnotou oddělovače.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Oddělovač** |Požaduje se |Řetězec |Řetězec používaný k oddělení zdrojových hodnot, když jsou zřetězeny do jednoho řetězce. Může být "", pokud není vyžadován oddělovač. |
| **zdroj1 ... sourceN** |Povinné, proměnný počet opakování |Řetězec |Řetězcové hodnoty, které mají být spojeny dohromady. |

---
### <a name="left"></a>Left
**Funkce:**<br> Vlevo (řetězec,NumChars)

**Popis:**<br> Funkce Left vrátí zadaný počet znaků zleva od řetězce. Pokud numChars = 0, vrátí prázdný řetězec.
Pokud numChars < 0, vrátí vstupní řetězec.
Pokud je řetězec null, vrátí prázdný řetězec.
Pokud řetězec obsahuje méně znaků než číslo zadané v numChars, je vrácen řetězec identický s řetězcem (to znamená, že obsahuje všechny znaky v parametru 1).

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Řetězec** |Požaduje se |Atribut | Řetězec, ze který chcete vrátit znaky |
| **Funkce NumChars** |Požaduje se |Integer | Číslo identifikující počet znaků, které mají být vráceny od začátku (vlevo) řetězce|

**Příklad:**<br>
Vlevo("John Doe", 3)                                                            
Vrátí "Joh"

---
### <a name="mid"></a>Mid
**Funkce:**<br> Střední (zdroj, začátek, délka)

**Popis:**<br> Vrátí podřetězec zdrojové hodnoty. Podřetězec je řetězec, který obsahuje pouze některé znaky ze zdrojového řetězce.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Obvykle název atributu. |
| **Spustit** |Požaduje se |celé číslo |Index ve **zdrojovém** řetězci, kde by měl začít podřetězec. První znak v řetězci bude mít index 1, druhý znak bude mít index 2 a tak dále. |
| **Délka** |Požaduje se |celé číslo |Délka podřetězce. Pokud délka končí mimo **zdrojový** řetězec, funkce vrátí podřetězec z **počátečního** indexu do konce **zdrojového** řetězce. |

---
### <a name="normalizediacritics"></a>NormalizeDiakritiky
**Funkce:**<br> NormalizeDiacritics(zdroj)

**Popis:**<br> Vyžaduje jeden řetězec argument. Vrátí řetězec, ale všechny diakritické znaky nahrazeny ekvivalentní nediakritické znaky. Obvykle se používá k převodu křestních jmen a příjmení obsahujících diakritické znaky (zvýrazňující značky) na právní hodnoty, které lze použít v různých identifikátorech uživatelů, jako jsou hlavní uživatelská jména, názvy účtů SAM a e-mailové adresy.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec | Obvykle atribut křestníjméno nebo příjmení. |

---
### <a name="not"></a>Not
**Funkce:**<br> Ne(zdroj)

**Popis:**<br> Převrátí logickou hodnotu **zdroje**. Pokud je **zdrojová** hodnota "*True*", vrátí "*False*". V opačném případě vrátí "*True*".

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Logický řetězec |Očekávané **zdrojové** hodnoty jsou "True" nebo "False". |

---
### <a name="numfromdate"></a>Datum numfrom
**Funkce:**<br> NumFromDate(hodnota)

**Popis:**<br> Funkce NumFromDate převede hodnotu DateTime do formátu služby Active Directory, který je nutný k nastavení atributů, jako je [accountExpires](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires). Pomocí této funkce můžete převést hodnoty DateTime přijaté z cloudových hr aplikací, jako jsou Workday a SuccessFactors, na jejich ekvivalentní reprezentaci služby AD. 

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **value** |Požaduje se | Řetězec | Řetězec časdata v podporovaném formátu. Podporované formáty naleznete https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspxv tématu . |

**Příklad:**<br>
* Příklad pracovního dne <br>
  Za předpokladu, že chcete mapovat atribut *ContractEndDate* z Workday, který je ve formátu *2020-12-31-08:00* na *pole AccountExpires* ve službě AD, můžete tuto funkci použít a změnit posun časového pásma tak, aby odpovídal vašemu národnímu prostředí. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* Příklad SuccessFactors <br>
  Za předpokladu, že chcete mapovat atribut *endDate* z SuccessFactors, který je ve formátu *M/d/yyyy hh:mm:ss tt* na *accountExpires* pole ve ad, zde je, jak můžete použít tuto funkci a změnit posun časového pásma tak, aby odpovídaly národní prostředí.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>Removeduplicates
**Funkce:**<br> Odstranit duplicates(atribut)

**Popis:**<br> Funkce RemoveDuplicates přebírá řetězec s více hodnotami a ujistěte se, že každá hodnota je jedinečná.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Atribut** |Požaduje se |Atribut s více hodnotami |Atribut s více hodnotami, který bude mít odebrány duplikáty|

**Příklad:**<br>
RemoveDuplicates([proxyAddresses])                                                                                                       
Vrátí atribut publicized proxyAddress, kde byly odebrány všechny duplicitní hodnoty.

---
### <a name="replace"></a>Nahradit
**Funkce:**<br> Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**Popis:**<br>
Nahradí hodnoty v řetězci. Funguje odlišně v závislosti na zadaných parametrech:

* Pokud jsou k dispozici **oldValue** a **replacementValue:**
  
  * Nahradí všechny výskyty **oldValue** ve **zdroji** **replacementValue.**
* Pokud jsou k dispozici **oldValue** a **šablona:**
  
  * Nahradí všechny výskyty **oldValue** v **šabloně** **zdrojovou** hodnotou.
* Pokud jsou k dispozici **regexPattern** a **replacementValue:**

  * Funkce použije **regexPattern** na **zdrojový** řetězec a můžete použít názvy skupin regulárních výrazů k vytvoření řetězce pro **replacementValue**
* Pokud **regexPattern**, **regexGroupName**, **replacementValue** jsou k dispozici:
  
  * Funkce aplikuje **regexPattern** na **zdrojový** řetězec a nahradí všechny hodnoty odpovídající **výrazu regexGroupName** **funkcí replacementValue.**
* Pokud **regexPattern**, **regexGroupName**, **replacementAttributeName** jsou k dispozici:
  
  * Pokud **zdroj** nemá žádnou hodnotu, je vrácen **zdroj**
  * Pokud **má zdroj** hodnotu, funkce použije **regexPattern** na **zdrojový** řetězec a nahradí všechny hodnoty odpovídající **hodnotě regexGroupName** hodnotou přidruženou k **replacementAttributeName**

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Obvykle název atributu ze **zdrojového** objektu. |
| **Oldvalue** |Nepovinné |Řetězec |Hodnota, která má být nahrazena ve **zdroji** nebo **šabloně**. |
| **regexPattern** |Nepovinné |Řetězec |Regex vzor pro hodnotu, která má být nahrazena ve **zdroji**. Nebo při **replacementPropertyName** se používá vzorek extrahovat hodnotu z **replacementPropertyName**. |
| **regexGroupName** |Nepovinné |Řetězec |Název skupiny uvnitř **regexPattern**. Pouze při **replacementPropertyName,** budeme extrahovat hodnotu této skupiny jako **replacementValue** z **replacementPropertyName**. |
| **replacementValue** |Nepovinné |Řetězec |Nová hodnota, kterou chcete nahradit starou hodnotu. |
| **replacementAttributeName** |Nepovinné |Řetězec |Název atributu, který má být použit pro reprodukční hodnotu |
| **šablona** |Nepovinné |Řetězec |Když je zadána hodnota **šablony,** budeme hledat **oldValue** uvnitř šablony a nahradit ji **zdrojovou** hodnotou. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funkce:**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Popis:**<br> Vyžaduje minimálně dva argumenty, což jsou jedinečná pravidla generování hodnoty definovaná pomocí výrazů. Funkce vyhodnotí každé pravidlo a potom zkontroluje hodnotu generovanou pro jedinečnost v cílové aplikaci nebo adresáři. První nalezená jedinečná hodnota bude vrácená. Pokud všechny hodnoty již existují v cíli, položka získá úschovu a důvod získá zaznamenány v protokolech auditu. Neexistuje žádná horní mez počtu argumentů, které mohou být poskytnuty.

> [!NOTE]
> - Jedná se o funkci nejvyšší úrovně, nelze ji vnořit.
> - Tuto funkci nelze použít na atributy, které mají odpovídající prioritu.  
> - Tato funkce je určena pouze pro vstupní výtvory. Při použití s atributem nastavte vlastnost **Použít mapování** pouze během **vytváření objektu**.
> - Tato funkce je v současné době podporována pouze pro "Zřizování uživatelů služby Active Directory do služby Active Directory". Nelze jej použít s jinými zřizovacími aplikacemi. 


**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **uniqueValueRule1 ... uniqueValueRuleN** |Jsou vyžadovány alespoň 2, žádná horní mez |Řetězec | Seznam jedinečných pravidel generování hodnoty k vyhodnocení. |


---
### <a name="singleapproleassignment"></a>SingleAppRolePřiřazení
**Funkce:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Popis:**<br> Vrátí jednu aplikaciRoleAssignment ze seznamu všech appRoleAssignments přiřazených uživateli pro danou aplikaci. Tato funkce je vyžadována k převodu objektu appRoleAssignments na jeden řetězec názvu role. Všimněte si, že osvědčeným postupem je zajistit, aby pouze jeden appRoleAssignment je přiřazena k jednomu uživateli najednou a pokud více rolí jsou přiřazeny řetězec role vrácena nemusí být předvídatelné. 

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Požaduje se |Řetězec |**[appRoleAssignments]** objektu. |

---
### <a name="split"></a>Rozdělení
**Funkce:**<br> Split (zdroj, oddělovač)

**Popis:**<br> Rozdělí řetězec na pole s více hodnotami pomocí zadaného znaku oddělovače.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |**zdrojové** hodnoty, která se má aktualizovat. |
| **Oddělovač** |Požaduje se |Řetězec |Určuje znak, který bude použit k rozdělení řetězce (příklad: ",") |

---
### <a name="stripspaces"></a>Stripspaces
**Funkce:**<br> StripSpaces(zdroj)

**Popis:**<br> Odebere ze zdrojového řetězce všechny mezery (").

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |**zdrojové** hodnoty, která se má aktualizovat. |

---
### <a name="switch"></a>Přepínač
**Funkce:**<br> Switch(zdroj, defaultValue, key1, value1, key2, value2, ...)

**Popis:**<br> Pokud **zdrojová** hodnota odpovídá **klíči**, vrátí **hodnotu** pro tento **klíč**. Pokud **zdrojová** hodnota neodpovídá žádné klíče, vrátí **defaultValue**.  **Klíčové** a **hodnotové** parametry musí být vždy dodávány ve dvojicích. Funkce vždy očekává sudý počet parametrů. Funkce by neměla být použita pro referenční atributy, jako je například manažer. 

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |**Zdrojová** hodnota k aktualizaci. |
| **Defaultvalue** |Nepovinné |Řetězec |Výchozí hodnota, která se použije, když zdroj neodpovídá žádné klíče. Může být prázdný řetězec (""). |
| **key** |Požaduje se |Řetězec |**Klíč** pro porovnání **zdrojové** hodnoty. |
| **value** |Požaduje se |Řetězec |Hodnota nahrazení **zdroje** odpovídající klíči. |

---
### <a name="tolower"></a>Tolower
**Funkce:**<br> ToLower(zdroj, jazyková verze)

**Popis:**<br> Převezme hodnotu *zdrojového* řetězce a převede ji na malá písmena pomocí pravidel jazykové verze, které jsou zadány. Pokud není zadána žádná informace o *jazykové verzi,* pak bude používat invariantní jazykovou verzi.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Obvykle název atributu ze zdrojového objektu |
| **jazyková verze** |Nepovinné |Řetězec |Formát názvu jazykové verze na základě RFC 4646 je *languagecode2-country/regioncode2*, kde *languagecode2* je dvoupísmenný kód jazyka a *kód země/regionu2* je dvoupísmenný kód subkultury. Příklady zahrnují ja-JP pro japonštinu (Japonsko) a en-US pro angličtinu (Spojené státy). V případech, kdy dvoupísmenný kód jazyka není k dispozici, se používá třípísmenný kód odvozený z ISO 639-2.|

---
### <a name="toupper"></a>Toupper
**Funkce:**<br> ToUpper (zdroj, jazyková verze)

**Popis:**<br> Převezme hodnotu *zdrojového* řetězce a převede ji na velká písmena pomocí pravidel jazykové verze, která jsou zadána. Pokud není zadána žádná informace o *jazykové verzi,* pak bude používat invariantní jazykovou verzi.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Obvykle název atributu ze zdrojového objektu. |
| **jazyková verze** |Nepovinné |Řetězec |Formát názvu jazykové verze na základě RFC 4646 je *languagecode2-country/regioncode2*, kde *languagecode2* je dvoupísmenný kód jazyka a *kód země/regionu2* je dvoupísmenný kód subkultury. Příklady zahrnují ja-JP pro japonštinu (Japonsko) a en-US pro angličtinu (Spojené státy). V případech, kdy dvoupísmenný kód jazyka není k dispozici, se používá třípísmenný kód odvozený z ISO 639-2.|

---
### <a name="word"></a>Word
**Funkce:**<br> Word (řetězec,WordNumber,oddělovače)

**Popis:**<br> Funkce Word vrátí slovo obsažené v řetězci na základě parametrů popisujících oddělovače, které mají být používány, a číslo slova, které má být vráceno. Každý řetězec znaků v řetězci oddělený jedním ze znaků v oddělovačích je identifikován jako slova:

Pokud číslo < 1, vrátí prázdný řetězec.
Pokud je řetězec null, vrátí prázdný řetězec.
Pokud řetězec obsahuje méně než číselná slova nebo řetězec neobsahuje žádná slova identifikovaná oddělovači, je vrácen prázdný řetězec.

**Parametry:**<br> 

| Name (Název) | Povinné/ Opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Řetězec** |Požaduje se |Atribut s více hodnotami |Řetězec pro vrácení slova.|
| **Číslo aplikace Word** |Požaduje se | Integer | Číslo identifikující číslo slova, které by mělo být vráceno|
| **Oddělovače** |Požaduje se |Řetězec| Řetězec představující oddělovač (oddělovače), který by měl být použit k identifikaci slov|

**Příklad:**<br>
Slovo ("Rychlá hnědá liška",3," ")                                                                                       
Vrátí "hnědou"

Word("This,string!má&mnoho oddělovačů",3,",!&#")                                                                       
Vrátí "má"

---

## <a name="examples"></a>Příklady
### <a name="strip-known-domain-name"></a>Překlovanou doménu
Chcete-li získat uživatelské jméno, musíte z e-mailu uživatele odstranit známý název domény. <br>
Pokud je například doména "contoso.com", můžete použít následující výraz:

**Výraz:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Vstup /výstup vzorku:** <br>

* **VSTUP** (pošta): "john.doe@contoso.com
* **VÝSTUP**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Připojit konstantní příponu k uživatelskému jménu
Pokud používáte sandbox Salesforce, možná budete muset připojit další příponu ke všem uživatelským jménům před jejich synchronizací.

**Výraz:** <br>
`Append([userPrincipalName], ".test")`

**Vstup/výstup vzorku:** <br>

* **VSTUP**: (userPrincipalName): "John.Doe@contoso.com
* **VÝSTUP**:John.Doe@contoso.com.test" "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generovat alias uživatele zřetězením částí křestního jména a příjmení
Musíte vygenerovat uživatelský alias tím, že první 3 písmena křestního jména uživatele a prvních 5 písmen příjmení uživatele.

**Výraz:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Vstup/výstup vzorku:** <br>

* **INPUT** (givenName): "Jan"
* **VSTUP** (příjmení): "Doe"
* **VÝSTUP**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Odebrání diakritiky z řetězce
Je třeba nahradit znaky obsahující zvýrazňující znaky ekvivalentními znaky, které neobsahují zvýrazňující znaky.

**Výraz:** <br>
NormalizeDiacritics([givenName])

**Vstup/výstup vzorku:** <br>

* **INPUT** (givenName): "Zoë"
* **VÝSTUP**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Rozdělení řetězce na pole s více hodnotami
Je třeba vzít seznam řetězců oddělený chod čárkami a rozdělit je do pole, které lze připojit do atributu s více hodnotami, jako je atribut Salesforce PermissionSets. V tomto příkladu seznam sad oprávnění byla naplněna v extensionAttribute5 ve službě Azure AD.

**Výraz:** <br>
Split([extensionAttribute5], ",")

**Vstup/výstup vzorku:** <br>

* **INPUT** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **VÝSTUP**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Výstupní datum jako řetězec v určitém formátu
Chcete odeslat data do aplikace SaaS v určitém formátu. <br>
Například chcete formátovat data pro ServiceNow.

**Výraz:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Vstup/výstup vzorku:**

* **VSTUP** (extensionAttribute1): "20150123105347.1Z"
* **VÝSTUP**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Nahrazení hodnoty na základě předdefinované sady možností

Je třeba definovat časové pásmo uživatele na základě kódu stavu uloženého ve službě Azure AD. <br>
Pokud kód státu neodpovídá žádné z předdefinovaných možností, použijte výchozí hodnotu "Austrálie/Sydney".

**Výraz:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Vstup/výstup vzorku:**

* **VSTUP** (stav): "QLD"
* **VÝSTUP**: "Austrálie/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Nahrazení znaků pomocí regulárního výrazu
Musíte najít znaky, které odpovídají hodnotě regulárního výrazu, a odebrat je.

**Výraz:** <br>

Replace([mailNickname], , "[a-zA-Z_]*", , "", , , )

**Vstup/výstup vzorku:**

* **VSTUP** (mailPřezdívka: "john_doe72"
* **VÝSTUP**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Převést vygenerovanou hodnotu userPrincipalName (UPN) na malá písmena
V níže uvedeném příkladu je hodnota UPN generována zřetězením zdrojových polí PreferredFirstName a PreferredLastName a funkce ToLower pracuje na generovaném řetězci a převede všechny znaky na malá písmena. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Vstup/výstup vzorku:**

* **INPUT** (PreferredFirstName): "Jan"
* **INPUT** (PreferredLastName): "Smith"
* **VÝSTUP**:john.smith@contoso.com" "

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generovat jedinečnou hodnotu pro atribut userPrincipalName (UPN)
Na základě křestního jména, druhého jména a příjmení uživatele je třeba před přiřazením hodnoty atributu HLAVNÍHO názvu uživatele vygenerovat hodnotu atributu HLAVNÍHO názvu uživatele a zkontrolovat jeho jedinečnost v cílovém adresáři služby AD.

**Výraz:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Vstup/výstup vzorku:**

* **INPUT** (PreferredFirstName): "Jan"
* **INPUT** (PreferredLastName): "Smith"
* **VÝSTUP**:John.Smith@contoso.com" ", John.Smith@contoso.com pokud hodnota UPN ještě v adresáři neexistuje
* **VÝSTUP**:J.Smith@contoso.com" ", John.Smith@contoso.com pokud hodnota UPN již v adresáři existuje
* **VÝSTUP**:Jo.Smith@contoso.com" ", pokud výše uvedené dvě hodnoty UPN již v adresáři existují

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Hodnota pošty toku, pokud není null, jinak tok userPrincipalName
Chcete tok atribut mail, pokud je k dispozici. Pokud tomu tak není, chcete natočit hodnotu userPrincipalName místo.

**Výraz:** <br>
`Coalesce([mail],[userPrincipalName])`

**Vstup/výstup vzorku:** <br>

* **VSTUP** (pošta): NULL
* **INPUT** (userPrincipalName):John.Doe@contoso.com"
* **VÝSTUP**:John.Doe@contoso.com" "

## <a name="related-articles"></a>Související články
* [Automatizace zřizování a odzřízení uživatelů do aplikací SaaS](../app-provisioning/user-provisioning.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](../app-provisioning/customize-application-attributes.md)
* [Filtry oborů pro zřizování uživatelů](define-conditional-rules-for-provisioning-user-accounts.md)
* [Pomocí SCIM zapněte automatické zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Oznámení o zřizování účtů](../app-provisioning/user-provisioning.md)
* [Seznam kurzů o integraci aplikací SaaS](../saas-apps/tutorial-list.md)
