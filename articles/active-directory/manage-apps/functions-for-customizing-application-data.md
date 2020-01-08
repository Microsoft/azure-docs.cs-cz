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
ms.openlocfilehash: 93b8387d4453a3d83bcce55c739548d914545f2f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430067"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Zápis výrazů pro mapování atributů v Azure Active Directory
Při konfiguraci zřizování pro aplikace SaaS, je jedním z typů mapování atributů, které můžete zadat mapování výrazu. Pro ty musíte napsat skript jako výraz, který umožňuje transformovat data uživatelů na formáty, které jsou více přijatelné pro aplikace SaaS.

## <a name="syntax-overview"></a>Přehled syntaxe
Syntaxe výrazů pro mapování atributů je připomínající Visual Basic pro funkce Applications (VBA).

* Celý výraz musí být definován jako funkce, které tvoří název, za nímž následuje argumenty v závorkách: <br>
  *Functions (`<<argument 1>>``<<argument N>>`)*
* Může vnořit do jiné funkce. Příklad: <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* Tři různé typy argumentů můžete předat do funkce:
  
  1. Atributy, které musí být uzavřeny do hranatých závorek. Příklad: [attributeName]
  2. Řetězcové konstanty, které musí být umístěn do dvojitých uvozovek. Příklad: "USA"
  3. Další funkce. Například: FunctionOne (`<<argument1>>`, FunctionTwo (`<<argument2>>`))
* Pro řetězcové konstanty Pokud potřebujete zpětného lomítka (\) nebo uvozovky (") v řetězci, se musejí být uvozeny symbol zpětného lomítka (\). Příklad: "název společnosti: \\" contoso\\""

## <a name="list-of-functions"></a>Seznam funkcí
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

---
### <a name="append"></a>Připojit
**Funkce:**<br> Append(Source, suffix)

**Popis:**<br> Vezme řetězcovou hodnotu zdroje a připojí přípona na konec.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Obvykle název atributu ze zdrojového objektu. |
| **Přípona** |Požaduje se |Řetězec |Řetězec, který chcete přidat do konce zdrojové hodnoty. |

---
### <a name="bitand"></a>BitAnd
**Funkce:**<br> BitAnd (hodnota1, hodnota2)

**Popis:**<br> Tato funkce převede oba parametry do binární reprezentace a nastaví bit na:

0 – Pokud má jedna nebo obě z odpovídajících bitů v hodnota1 a hodnota2 hodnotu 0                                                  
1 – Pokud jsou obě odpovídající bity 1.                                    

Jinými slovy, vrátí 0 ve všech případech s výjimkou toho, že odpovídající bity obou parametrů jsou 1.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Hodnota1** |Požaduje se |num |Číselná hodnota, která by měla být AND'ed s hodnota2|
| **Argument** |Požaduje se |num |Číselná hodnota, která má být AND'ed s hodnota1|

**Příklad:**<br>
BitAnd (& HF, & HF7)                                                                                
11110111 a 00000111 = 00000111, takže BitAnd vrátí hodnotu 7, binární hodnota 00000111

---
### <a name="cbool"></a>CBool
**Funkce:**<br> CBool (výraz)

**Popis:**<br> Funkce CBool vrátí logickou hodnotu založenou na vyhodnoceném výrazu. Pokud je výraz vyhodnocen jako nenulová hodnota, pak funkce CBool vrátí hodnotu true, jinak vrátí hodnotu false..

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **vyjádření** |Požaduje se | expression | Libovolný platný výraz |

**Příklad:**<br>
CBool ([attribute1] = [attribute2])                                                                    
Vrátí hodnotu true, pokud mají oba atributy stejnou hodnotu.

---
### <a name="coalesce"></a>Coalesce
**Funkce:**<br> COALESCE (source1; SOURCE2;...; defaultValue)

**Popis:**<br> Vrátí první hodnotu zdroje, která není NULL. Pokud jsou všechny argumenty NULL a jsou přítomny hodnoty defaultValue, vrátí se hodnota defaultValue. Pokud jsou všechny argumenty NULL a vlastnost defaultValue není k dispozici, funkce coalesce vrátí hodnotu NULL.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **zdroj1... zdrojN** | Požaduje se | Řetězec |Povinný, proměnlivý počet opakování. Obvykle název atributu ze zdrojového objektu. |
| **Výchozí hodnota** | Volitelné | Řetězec | Výchozí hodnota, která se má použít, pokud jsou všechny zdrojové hodnoty NULL. Může být prázdný řetězec ("").

---
### <a name="converttobase64"></a>ConvertToBase64
**Funkce:**<br> ConvertToBase64 (zdroj)

**Popis:**<br> Funkce ConvertToBase64 převede řetězec na řetězec Unicode base64.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Řetězec, který má být převeden na základní 64|

**Příklad:**<br>
ConvertToBase64 ("Hello World!")                                                                                                        
Vrátí "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Funkce:**<br> ConvertToUTF8Hex (zdroj)

**Popis:**<br> Funkce ConvertToUTF8Hex převede řetězec na šestnáctkovou hodnotu v kódování UTF8.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Řetězec, který se má převést na UTF8 hex|

**Příklad:**<br>
ConvertToUTF8Hex ("Hello World!")                                                                                                         
Returns 48656C6C6F20776F726C6421

---
### <a name="count"></a>Počet
**Funkce:**<br> Count (atribut)

**Popis:**<br> Funkce Count vrátí počet prvků v vícehodnotovém atributu.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **attribute** |Požaduje se |Atribut |Vícehodnotový atribut, který bude obsahovat elementy počítané|

---
### <a name="cstr"></a>CStr
**Funkce:**<br> CStr (hodnota)

**Popis:**<br> Funkce CStr převede hodnotu na datový typ String.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **value** |Požaduje se | Číselná, referenční nebo logická hodnota | Může to být číselná hodnota, odkazový atribut nebo logická hodnota. |

**Příklad:**<br>
CStr ([DN])                                                            
Vrátí "CN = Jan, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Funkce:**<br> DateFromNum (hodnota)

**Popis:**<br> Funkce DateFromNum převede hodnotu ve formátu data AD na typ DateTime.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **value** |Požaduje se | Datum | Datum reklamy, které má být převedeno na typ DateTime |

**Příklad:**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum(129699324000000000)                                                            
Vrátí hodnotu DateTime představující 2012-01-01 23:00:00.

---
### <a name="formatdatetime"></a>formatDateTime
**Funkce:**<br> FormatDateTime (zdroj, inputFormat outputFormat.)

**Popis:**<br> Přebírá řetězec data z jednoho formátu a převede jej na jiný formát.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Obvykle název atributu ze zdrojového objektu. |
| **inputFormat** |Požaduje se |Řetězec |Očekávaný formát zdrojové hodnoty. Podporovaných formátů naleznete v tématu [ https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx ](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Požaduje se |Řetězec |Formát výstupního data. |

---
### <a name="guid"></a>Guid
**Funkce:**<br> GUID ()

**Popis:**<br> Identifikátor GUID funkce vygeneruje nový náhodný identifikátor GUID.

---
### <a name="instr"></a>InStr
**Funkce:**<br> InStr (hodnota1, hodnota2, Start, compareType)

**Popis:**<br> Funkce InStr vyhledá první výskyt podřetězce v řetězci.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Hodnota1** |Požaduje se |Řetězec |Řetězec, který má být prohledán |
| **Argument** |Požaduje se |Řetězec |Řetězec, který se má najít |
| **start** |Volitelné |Integer |Počáteční pozice pro vyhledání podřetězce|
| **compareType** |Volitelné |Výčet |Může být vbTextCompare nebo vbBinaryCompare |

**Příklad:**<br>
InStr ("Rychlá hnědá Fox", "Rychlá")                                                                             
Evalues na 5

InStr ("opakuje", "e"; 3; vbBinaryCompare)                                                                                  
Vyhodnotí na 7

---
### <a name="isnull"></a>IsNull
**Funkce:**<br> IsNull (výraz)

**Popis:**<br> Pokud je výraz vyhodnocen jako null, funkce IsNull vrátí hodnotu true. U atributu je hodnota null vyjádřena nepřítomností atributu.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **vyjádření** |Požaduje se |expression |Výraz, který se má vyhodnotit |

**Příklad:**<br>
IsNull ([DisplayName])                                                                                                
Vrátí hodnotu true, pokud atribut není přítomen.

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Funkce:**<br> IsNullOrEmpty (výraz)

**Popis:**<br> Pokud má výraz hodnotu null nebo je prázdný řetězec, vrátí funkce IsNullOrEmpty hodnotu true. U atributu by se to mělo vyhodnotit na hodnotu true, pokud atribut chybí nebo je přítomen, ale je to prázdný řetězec.
Inverzní část této funkce je pojmenována jako.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **vyjádření** |Požaduje se |expression |Výraz, který se má vyhodnotit |

**Příklad:**<br>
IsNullOrEmpty ([DisplayName])                                               
Vrátí hodnotu true, pokud atribut není přítomen, nebo je prázdný řetězec.

---
### <a name="ispresent"></a>K dispozici
**Funkce:**<br> IsNullOrEmpty (výraz)

**Popis:**<br> Pokud se výraz vyhodnotí jako řetězec, který není null a není prázdný, vrátí funkce vracející hodnotu true. Inverzní funkce k této funkci má název IsNullOrEmpty.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **vyjádření** |Požaduje se |expression |Výraz, který se má vyhodnotit |

**Příklad:**<br>
Switch (přítomen ([directManager]); [directManager]; ([skiplevelManager]); [skiplevelManager]; ([režisér]); [Director])

---
### <a name="isstring"></a>Řetězec
**Funkce:**<br> Řetězec (výraz)

**Popis:**<br> Pokud je možné výraz vyhodnotit na typ řetězce, pak je funkce typu String vyhodnocena jako true.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **vyjádření** |Požaduje se |expression |Výraz, který se má vyhodnotit |

---
### <a name="item"></a>Položka
**Funkce:**<br> Item (atribut; index)

**Popis:**<br> Funkce Item vrátí jednu položku z vícehodnotového řetězce nebo atributu.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **attribute** |Požaduje se |Atribut |Vícehodnotový atribut, který má být prohledán |
| **index** |Požaduje se |Integer | Index položky v řetězci s více hodnotami|

**Příklad:**<br>
Item ([proxyAddresses]; 1)

---
### <a name="join"></a>Spojit
**Funkce:**<br> Připojte se k (oddělovač, zdroj1, zdroj2,...)

**Popis:**<br> Join() je podobný Append(), s tím rozdílem, že ho můžete zkombinovat více **zdroj** hodnoty řetězce do jednoho řetězce a pro jednotlivé hodnoty oddělené bránou **oddělovač** řetězec.

Pokud je jednou ze zdrojových hodnot atribut s více hodnotami, pak se všechny hodnoty v tomto atributu spojí dohromady, oddělené hodnotou oddělovače.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Oddělovač** |Požaduje se |Řetězec |Řetězec použitý k oddělení zdrojové hodnoty, když jsou zřetězeny do jednoho řetězce. Může být "" Pokud žádný oddělovač je povinný. |
| **zdroj1... zdrojN** |Povinné, proměnná počet pokusů |Řetězec |Hodnoty, který se má spojit dohromady řetězce. |

---
### <a name="left"></a>Vlevo
**Funkce:**<br> Left (řetězec; NumChars)

**Popis:**<br> Funkce Left vrátí zadaný počet znaků nalevo od řetězce. Pokud numChars = 0, vrátí prázdný řetězec.
Pokud numChars < 0, vrátí se vstupní řetězec.
Pokud má řetězec hodnotu null, vrátí prázdný řetězec.
Pokud řetězec obsahuje méně znaků než číslo zadané v numChars, vrátí se řetězec shodný s řetězcem (tj. obsahující všechny znaky v parametru 1).

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **řetězec** |Požaduje se |Atribut | Řetězec, ze kterého se mají vracet znaky |
| **NumChars** |Požaduje se |Integer | Číslo určující počet znaků, které mají být vráceny od začátku (vlevo) řetězce|

**Příklad:**<br>
Vlevo ("Jan Novák"; 3)                                                            
Vrátí "Joh"

---
### <a name="mid"></a>Mid
**Funkce:**<br> Mid (source; start, délka)

**Popis:**<br> Vrátí podřetězec zdrojovou hodnotou. Dílčí řetězec je řetězec, který obsahuje pouze některé znaky z zdrojový řetězec.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Obvykle název atributu. |
| **start** |Požaduje se |celé číslo |Index v **zdroj** řetězce, kde by měla začít dílčí řetězec. První znak v řetězci budou mít index hodnotu 1, druhý znak bude mít index 2 a tak dále. |
| **Délka** |Požaduje se |celé číslo |Délka podřetězce. Pokud délka skončí mimo **zdroj** řetězec, funkce vrátí dílčí řetězec z **start** indexu do konce **zdroj** řetězec. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funkce:**<br> NormalizeDiacritics(source)

**Popis:**<br> Vyžaduje jeden argument řetězec. Vrátí řetězec, ale s znaky diakritická nahradí ekvivalentní-diakritická znaků. Obvykle slouží k převodu jména a příjmení obsahující diakritická znaky (znaky s diakritikou značky) do platné hodnoty, které můžete použít různé identifikátory uživatele, jako je například hlavních názvů uživatelů, názvy účtů SAM a e-mailové adresy.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec | Obvykle se jedná o křestní jméno nebo název atributu příjmení. |

---
### <a name="not"></a>Not
**Funkce:**<br> Not(Source)

**Popis:**<br> Převrátí logickou hodnotu **zdroj**. Pokud **zdroj** hodnota je "*True*", vrací "*False*". V opačném případě vrátí "*True*".

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Logického řetězce |Očekávané **zdrojové** hodnoty jsou "true" nebo "false". |

---
### <a name="removeduplicates"></a>RemoveDuplicates –
**Funkce:**<br> RemoveDuplicates – (atribut)

**Popis:**<br> Funkce RemoveDuplicates – přebírá řetězec s více hodnotami a ověří, zda jsou všechny hodnoty jedinečné.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **attribute** |Požaduje se |Vícehodnotový atribut |Vícehodnotový atribut, který bude mít odebrané duplicity|

**Příklad:**<br>
RemoveDuplicates – ([proxyAddresses])                                                                                                       
Vrátí upravený atribut proxyAddress, ve kterém se odebraly všechny duplicitní hodnoty.

---
### <a name="replace"></a>Nahradit
**Funkce:**<br> Nahraďte (zdroj, oldValue, regexPattern, regexGroupName, zastaralá, replacementAttributeName, šablona)

**Popis:**<br>
Nahradí hodnoty v řetězci. V závislosti na parametry, které poskytnou funguje jinak:

* Když **oldValue** a **zastaralá** jsou k dispozici:
  
  * Nahradí všechny výskyty **OldValue** ve **zdroji** pomocí **replacementValue** .
* Když **oldValue** a **šablony** jsou k dispozici:
  
  * Nahradí všechny výskyty **oldValue** v **šablony** s **zdroj** hodnota
* Když jsou k dispozici **vzor Regex** a **replacementValue** :

  * Funkce použije **vzor Regex** na **zdrojový** řetězec a můžete použít názvy skupin Regex k sestavení řetězce pro **replacementValue**
* Když **regexPattern**, **regexGroupName**, **zastaralá** jsou k dispozici:
  
  * Funkce použije **vzor Regex** na **zdrojový** řetězec a nahradí všechny hodnoty, které odpovídají **regexGroupName** , s **replacementValue**
* Když jsou k dispozici **vzor Regex**, **regexGroupName**, **replacementAttributeName** :
  
  * Pokud **zdroj** nemá žádnou hodnotu **zdroj** je vrácena
  * Pokud má **zdroj** hodnotu, funkce použije **vzor Regex** na **zdrojový** řetězec a nahradí všechny hodnoty odpovídající **regexGroupName** hodnotou přidruženou k **replacementAttributeName** .

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Obvykle název atributu ze **zdrojového** objektu. |
| **oldValue** |Volitelné |Řetězec |Hodnota, která má být nahrazen v **zdroj** nebo **šablony**. |
| **regexPattern** |Volitelné |Řetězec |Vzor regulárního výrazu pro hodnota, která má být nahrazen v **zdroj**. Nebo, pokud se používá **replacementPropertyName** , vzorek pro extrakci hodnoty z **replacementPropertyName**. |
| **regexGroupName** |Volitelné |Řetězec |Název skupiny uvnitř **regexPattern**. Jenom v případě, že se používá **replacementPropertyName** , extrahujeme hodnotu této skupiny jako **replacementValue** z **replacementPropertyName**. |
| **Zastaralá** |Volitelné |Řetězec |Nová hodnota nahradí starou s. |
| **replacementAttributeName** |Volitelné |Řetězec |Název atributu, který se má použít k nahrazení hodnoty |
| **Šablony** |Volitelné |Řetězec |Když se zadá hodnota **šablony** , budeme v šabloně Hledat text **OldValue** a nahradit ho **zdrojovou** hodnotou. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funkce:**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3,...)

**Popis:**<br> Vyžaduje minimálně dva argumenty, které jsou definovány pomocí výrazů pravidel pro vytvoření jedinečnou hodnotu. Funkce vyhodnocuje každé pravidlo a poté zkontroluje hodnotu vygenerovat jedinečný v cílové aplikaci/adresáři. První jedinečnou hodnotu najít, bude vrácena ten. Pokud všechny hodnoty již existují v cíli, položka bude získat mezi a důvod získá protokolovat v protokolech auditu. Neexistuje žádná horní mez počtu argumentů, které mohou být k dispozici.

> [!NOTE]
> - Toto je funkce nejvyšší úrovně, nemohou být vnořeny.
> - Tuto funkci nelze použít pro atributy, které mají odpovídající prioritu.  
> - Tato funkce je určená jenom pro vytvoření položky. Při použití ho atributem, nastavte **použít mapování** vlastnost **pouze při vytváření objektu**.
> - Tato funkce je momentálně podporovaná jenom pro zřizování uživatelů z Workday do služby Active Directory. Nedá se použít s jinými zřizovacími aplikacemi. 


**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **uniqueValueRule1... uniqueValueRuleN** |Minimálně 2 jsou povinné, ne horní mez |Řetězec | Seznam pravidel generování jedinečných hodnot, které se mají vyhodnotit |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funkce:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Popis:**<br> Vrátí jeden appRoleAssignment ze seznamu všech appRoleAssignments přiřazených uživateli pro danou aplikaci. Tato funkce je nutná k převedení objektu appRoleAssignments na jeden řetězec názvu role. Doporučujeme, abyste zajistili, že se jednomu uživateli přiřadí pouze jedna appRoleAssignment a pokud je přiřazeno více rolí, vrácený řetězec role nemusí být předvídatelný. 

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Požaduje se |Řetězec |**[appRoleAssignments]**  objektu. |

---
### <a name="split"></a>Rozdělení
**Funkce:**<br> Split (Source, oddělovač)

**Popis:**<br> Rozdělí řetězec na pole s více hodnotami pomocí zadaného oddělovače.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |**Zdroj** hodnotu aktualizovat. |
| **delimiter** |Požaduje se |Řetězec |Určuje znak, který bude použit k rozdělení řetězce (například: ","). |

---
### <a name="stripspaces"></a>StripSpaces
**Funkce:**<br> StripSpaces(source)

**Popis:**<br> Odebere všechny mezery ("") znaků z řetězce zdroje.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |**Zdroj** hodnotu aktualizovat. |

---
### <a name="switch"></a>Přepínač
**Funkce:**<br> Switch (zdroj, výchozí hodnota, key1, value1, key2, value2,...)

**Popis:**<br> Při **zdroj** hodnotu shody **klíč**, vrátí **hodnotu** pro daný **klíč**. Pokud **zdroj** hodnota neodpovídá žádné klíče, vrátí **defaultValue**.  **Klíč** a **hodnotu** parametry musí být vždy ve dvojicích. Funkce očekává vždy sudý počet parametrů. Funkce by neměla být používána pro referenční atributy, jako je například správce. 

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |**Zdroj** hodnotu aktualizovat. |
| **Výchozí hodnota** |Volitelné |Řetězec |Výchozí hodnota má být použit při zdroj neodpovídá žádné klíče. Může být prázdný řetězec (""). |
| **key** |Požaduje se |Řetězec |**Klíč** k porovnání **zdroj** hodnotu. |
| **value** |Požaduje se |Řetězec |Nahrazující hodnotou pro **zdroj** odpovídající klíči. |

---
### <a name="tolower"></a>toLower
**Funkce:**<br> ToLower (zdroj, jazyková verze)

**Popis:**<br> Převezme hodnotu *zdrojového* řetězce a převede ji na malý případ pomocí pravidel jazykové verze, které jsou určeny. Pokud nejsou zadány žádné informace o *jazykové verzi* , pak použije invariantní jazykovou verzi.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Obvykle název atributu ze zdrojového objektu |
| **jazykových** |Volitelné |Řetězec |Formát pro název jazykové verze založený na RFC 4646 je *languagecode2-Country/regioncode2*, kde *languagecode2* je kód jazyka dvou písmen a *země/regioncode2* je kód subjazykové verze se dvěma písmeny. Mezi příklady patří ja-JP pro japonštinu (Japonsko) a EN-US pro angličtinu (USA). V případech, kdy kód jazyka se dvěma písmeny není k dispozici, je použit kód o třech písmenech odvozený z ISO 639-2.|

---
### <a name="toupper"></a>toUpper
**Funkce:**<br> ToUpper (zdroj, jazyková verze)

**Popis:**<br> Převezme hodnotu *zdrojového* řetězce a převede ji na velká písmena pomocí pravidel jazykové verze, které jsou určeny. Pokud nejsou zadány žádné informace o *jazykové verzi* , pak použije invariantní jazykovou verzi.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdroj** |Požaduje se |Řetězec |Obvykle název atributu ze zdrojového objektu. |
| **jazykových** |Volitelné |Řetězec |Formát pro název jazykové verze založený na RFC 4646 je *languagecode2-Country/regioncode2*, kde *languagecode2* je kód jazyka dvou písmen a *země/regioncode2* je kód subjazykové verze se dvěma písmeny. Mezi příklady patří ja-JP pro japonštinu (Japonsko) a EN-US pro angličtinu (USA). V případech, kdy kód jazyka se dvěma písmeny není k dispozici, je použit kód o třech písmenech odvozený z ISO 639-2.|

---
### <a name="word"></a>Word
**Funkce:**<br> Word (řetězec, WordNumber, oddělovače)

**Popis:**<br> Funkce Word vrátí slovo obsažené v řetězci na základě parametrů popisujících oddělovače, které se mají použít, a číslo slova, které se má vrátit. Každý řetězec znaků v řetězci, oddělený jedním ze znaků v oddělovačích, je identifikován jako slova:

Pokud číslo < 1, vrátí prázdný řetězec.
Pokud má řetězec hodnotu null, vrátí prázdný řetězec.
Pokud řetězec obsahuje méně než čísla slov nebo řetězec neobsahuje žádná slova identifikovaná oddělovači, je vrácen prázdný řetězec.

**Parametry:**<br> 

| Name (Název) | Požadovaný / s opakováním | Typ | Poznámky |
| --- | --- | --- | --- |
| **řetězec** |Požaduje se |Vícehodnotový atribut |Řetězec, ze kterého se má vrátit slovo|
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

**Výraz:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Ukázkový vstup / výstup:** <br>

* **VSTUP** (pošta): "john.doe@contoso.com"
* **VÝSTUP**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Připojit konstantní příponu k uživatelské jméno
Pokud používáte Sandboxu služby Salesforce, můžete potřebovat přidat další přípony pro všechny své uživatelské jméno před jejich synchronizaci.

**Výraz:** <br>
`Append([userPrincipalName], ".test")`

**Ukázkový vstup/výstup:** <br>

* **VSTUP**: (hodnota userPrincipalName): "John.Doe@contoso.com"
* **VÝSTUP**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Vytvořte alias uživatele tím, že spojováním části křestní jméno a příjmení
Budete muset vygenerovat uživatele alias provedením první 3 písmena křestní jméno uživatele a prvních 5 písmena příjmení uživatele.

**Výraz:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Ukázkový vstup/výstup:** <br>

* **VSTUP** (givenName): "John"
* **VSTUP** (příjmení): "Nováková"
* **VÝSTUP**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Odebrat znaky s diakritikou v řetězci
Je třeba nahradit znaků obsahující diakritická znaménka s ekvivalentní znaků, které neobsahují slovo značky zvýraznění.

**Výraz:** <br>
NormalizeDiacritics([givenName])

**Ukázkový vstup/výstup:** <br>

* **VSTUP** (givenName): "Zoë"
* **VÝSTUP**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Rozdělit řetězec do pole s více hodnotami
Musíte vzít seznam řetězců oddělených čárkami a rozdělit je do pole, které se dá zapojit do vícehodnotového atributu, jako je atribut PermissionSet služby Salesforce. V tomto příkladu se v extensionAttribute5 ve službě Azure AD nastavil seznam sad oprávnění.

**Výraz:** <br>
Split ([extensionAttribute5]; ";")

**Ukázkový vstup/výstup:** <br>

* **Vstup** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **Výstup**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Výstupní data jako řetězec v určitém formátu
Chcete odesílat data do aplikace SaaS v určitém formátu. <br>
Je třeba k formátování kalendářních dat pro ServiceNow.

**Výraz:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Ukázkový vstup/výstup:**

* **VSTUP** (extensionAttribute1): "20150123105347.1Z"
* **VÝSTUP**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Nahraďte hodnotu podle předdefinovanou sadu možností

Budete muset definovat časové pásmo uživatele na základě kódu stavu uložené ve službě Azure AD. <br>
Pokud kód stavu neodpovídá žádné z předdefinovaných možností, použijte výchozí hodnotu "Austrálie/Sydney".

**Výraz:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Ukázkový vstup/výstup:**

* **VSTUP** (stav): "QLD"
* **VÝSTUP**: "Austrálie/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Nahrazení znaků pomocí regulárního výrazu
Je nutné najít znaky, které odpovídají hodnotě regulárního výrazu, a odebrat je.

**Výraz:** <br>

Replace ([mailNickname];; "[a-zA-Z_] *",, "",,)

**Ukázkový vstup/výstup:**

* **Vstup** (mailnickname: "john_doe72"
* **Výstup**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Převést generovanou hodnotu userPrincipalName (UPN) na malá písmena
V následujícím příkladu je hodnota hlavního názvu uživatele generována zřetězením zdrojových polí PreferredFirstName a PreferredLastName a funkce ToLower funguje na vygenerovaném řetězci pro převod všech znaků na malá písmena. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Ukázkový vstup/výstup:**

* **VSTUP** (PreferredFirstName): "John"
* **VSTUP** (PreferredLastName): "Macek"
* **Výstup**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generovat jedinečnou hodnotu pro atribut userPrincipalName (UPN)
Založené na uživatele křestní jméno, křestní jméno a příjmení, je potřeba vygenerovat hodnotu pro atribut hlavního názvu uživatele a vyhledat jeho jedinečnosti v adresáři cílového AD před přiřazením hodnoty pro atribut hlavního názvu uživatele.

**Výraz:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Ukázkový vstup/výstup:**

* **VSTUP** (PreferredFirstName): "John"
* **VSTUP** (PreferredLastName): "Macek"
* **VÝSTUP**: "John.Smith@contoso.com" Pokud hodnotu hlavního názvu uživatele John.Smith@contoso.com ještě neexistuje v adresáři
* **VÝSTUP**: "J.Smith@contoso.com" Pokud hodnotu hlavního názvu uživatele John.Smith@contoso.com již existuje v adresáři
* **VÝSTUP**: "Jo.Smith@contoso.com" Pokud výše uvedené hodnoty dva hlavní název uživatele v adresáři už existuje

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Hodnota pro poštu flowu, pokud není NULL, jinak Flow userPrincipalName
Chcete-li, aby se atribut mail nacházel, je-li k dispozici. Pokud není, chcete místo toho Flow použít hodnotu userPrincipalName.

**Výraz:** <br>
`Coalesce([mail],[userPrincipalName])`

**Ukázkový vstup/výstup:** <br>

* **Vstup** (mail): null
* **Input** (userPrincipalName): "John.Doe@contoso.com"
* **VÝSTUP**: "John.Doe@contoso.com"

## <a name="related-articles"></a>Související články
* [Automatizace uživatele zřizování a jeho rušení pro aplikace SaaS](user-provisioning.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](customize-application-attributes.md)
* [Filtry oborů pro zřizování uživatelů](define-conditional-rules-for-provisioning-user-accounts.md)
* [Zapnutí automatického zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací pomocí SCIM](use-scim-to-provision-users-and-groups.md)
* [Oznámení o zřizování účtů](user-provisioning.md)
* [Seznam kurzů o integraci aplikací SaaS](../saas-apps/tutorial-list.md)
