---
title: Azure AD Connect zřizování cloudových výrazů a odkaz na funkce
description: reference
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78298611"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Psaní výrazů pro mapování atributů ve službě Azure Active Directory
Při konfiguraci zřizování cloudu je jedním z typů mapování atributů, které můžete zadat, mapování výrazů. 

Mapování výrazů umožňuje přizpůsobit atributy pomocí výrazu podobného skriptu.  To umožňuje transformovat místní data do nové nebo jiné hodnoty.  Můžete například zkombinovat dva atributy do jednoho atributu, protože tento jeden atribut používá jedna z cloudových aplikací.

Následující dokument se bude týkat skript-jako výrazy, které se používají k transformaci dat.  To je jen část procesu.  Dále budete muset použít tento výraz a umístit jej do webové žádosti do vašeho tenanta.  Další informace naleznete v [tématu Transformace](how-to-transformation.md)

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
| Seznam funkcí | Popis |
|-----|----|
|[Připojit](#append)|Převezme hodnotu zdrojového řetězce a připojí příponu na její konec.|
|[BitAnd](#bitand)|Funkce BitAnd nastaví zadané bity na hodnotu.|
|[CBool](#cbool)|Funkce CBool vrátí logickou hodnotu na základě vyhodnoceného výrazu.|
|[PřevéstFromBase64](#convertfrombase64)|Funkce ConvertFromBase64 převede zadanou hodnotu zakódovánou base64 na běžný řetězec.|
|[Převést na základnu64](#converttobase64)|Funkce ConvertToBase64 převede řetězec na řetězec Unicode base64. |
|[PřevéstToUTF8Hex](#converttoutf8hex)|Funkce ConvertToUTF8Hex převede řetězec na hodnotu kódovku Hex UTF8.|
|[Počet](#count)|Funkce Počet vrátí počet prvků v atributu s více hodnotami.|
|[Cstr](#cstr)|Funkce CStr se převede na datový typ řetězce.|
|[DateFromNum](#datefromnum)|Funkce DateFromNum převede hodnotu ve formátu data služby AD na typ DateTime.|
|[Součást DN](#dncomponent)|Funkce DNComponent vrátí hodnotu zadané součásti DN, která bude zadaná zleva.|
|[Chyba](#error)|Funkce Error se používá k vrácení vlastní chyby.|
|[Formatdatetime](#formatdatetime) |Vezme řetězec kalendářního data z jednoho formátu a převede jej do jiného formátu.| 
|[Identifikátor guid](#guid)|Funkce Guid generuje nový náhodný identifikátor GUID.|           
|[Iif](#iif)|Funkce IIF vrátí jednu ze sady možných hodnot na základě zadané podmínky.|
|[Instr](#instr)|Funkce InStr vyhledá první výskyt podřetězce v řetězci.|
|[Isnull](#isnull)|Pokud výraz vyhodnotí na Null, pak isnull funkce vrátí true.|
|[IsnullorPrázdný](#isnullorempty)|Pokud je výraz null nebo prázdný řetězec, vrátí funkce IsNullOrEmpty hodnotu true.|         
|[Ispresent](#ispresent)|Pokud výraz vyhodnotí řetězec, který není Null a není prázdný, pak ispresent funkce vrátí true.|    
|[IsString](#isstring)|Pokud výraz lze vyhodnotit na typ řetězce, pak IsString funkce vyhodnotí True.|
|[Položka](#item)|Funkce Item vrátí jednu položku z vícehodnotového řetězce/atributu.|
|[Připojit](#join) |Join() je podobný Append(), s tím rozdílem, že může kombinovat více hodnot **zdrojového** řetězce do jednoho řetězce a každá hodnota bude oddělena **oddělovacím** řetězcem.| 
|[Vlevo](#left)|Funkce Left vrátí zadaný počet znaků zleva od řetězce.|
|[Polovině](#mid) |Vrátí podřetězec zdrojové hodnoty. Podřetězec je řetězec, který obsahuje pouze některé znaky ze zdrojového řetězce.|
|[NormalizeDiakritiky](#normalizediacritics)|Vyžaduje jeden řetězec argument. Vrátí řetězec, ale všechny diakritické znaky nahrazeny ekvivalentní nediakritické znaky.|
|[Ne](#not) |Převrátí logickou hodnotu **zdroje**. Pokud je **zdrojová** hodnota "*True*", vrátí "*False*". V opačném případě vrátí "*True*".| 
|[Removeduplicates](#removeduplicates)|Funkce RemoveDuplicates přebírá řetězec s více hodnotami a ujistěte se, že každá hodnota je jedinečná.| 
|[Nahradit](#replace) |Nahradí hodnoty v řetězci. | 
|[SelectUniqueValue](#selectuniquevalue)|Vyžaduje minimálně dva argumenty, což jsou jedinečná pravidla generování hodnoty definovaná pomocí výrazů. Funkce vyhodnotí každé pravidlo a potom zkontroluje hodnotu generovanou pro jedinečnost v cílové aplikaci nebo adresáři.| 
|[SingleAppRolePřiřazení](#singleapproleassignment)|Vrátí jednu aplikaciRoleAssignment ze seznamu všech appRoleAssignments přiřazených uživateli pro danou aplikaci.| 
|[Rozdělit](#split)|Rozdělí řetězec na pole s více hodnotami pomocí zadaného znaku oddělovače.|
|[StringFromSID](#stringfromsid)|Funkce StringFromSid převede bajtové pole obsahující identifikátor zabezpečení na řetězec.| 
|[Stripspaces](#stripspaces) |Odebere ze zdrojového řetězce všechny mezery (").| 
|[Přepnout](#switch)|Pokud **zdrojová** hodnota odpovídá **klíči**, vrátí **hodnotu** pro tento **klíč**. | 
|[Tolower](#tolower)|Převezme hodnotu *zdrojového* řetězce a převede ji na malá písmena pomocí pravidel jazykové verze, které jsou zadány.| 
|[Toupper](#toupper)|Převezme hodnotu *zdrojového* řetězce a převede ji na velká písmena pomocí pravidel jazykové verze, která jsou zadána.|
|[Trim](#trim)|Funkce Trim odstraní úvodní a koncové mezery z řetězce.|
|[Word](#word)|Funkce Word vrátí slovo obsažené v řetězci na základě parametrů popisujících oddělovače, které mají být používány, a číslo slova, které má být vráceno.|

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
**Popis:**  
Funkce BitAnd nastaví zadané bity na hodnotu.

**Syntaxe:**  
`num BitAnd(num value1, num value2)`

* value1, value2: číselné hodnoty, které by měly být and'ed společně

**Poznámky:**  
Tato funkce převede oba parametry na binární reprezentaci a trochu nastaví na:

* 0 - pokud jeden nebo oba odpovídající bity v *hodnotě1* a *hodnota2* jsou 0
* 1 - pokud jsou oba odpovídající bity 1.

Jinými slovy vrátí 0 ve všech případech s výjimkou, pokud jsou odpovídající bity obou parametrů 1.

**Příklad:**  
 
 `BitAnd(&HF, &HF7)`</br>
 Vrátí hodnotu 7, protože šestnáctkové "F" a "F7" vyhodnotit na tuto hodnotu.

---

### <a name="cbool"></a>CBool
**Popis:**  
Funkce CBool vrátí logickou hodnotu na základě vyhodnoceného výrazu.

**Syntaxe:**  
`bool CBool(exp Expression)`

**Poznámky:**  
Pokud výraz vyhodnotí nenulovou hodnotu, pak CBool vrátí True, jinak vrátí False.

**Příklad:**  
`CBool([attrib1] = [attrib2])`  

Vrátí hodnotu True, pokud mají oba atributy stejnou hodnotu.

---
### <a name="convertfrombase64"></a>PřevéstFromBase64
**Popis:**  
Funkce ConvertFromBase64 převede zadanou hodnotu zakódovánou base64 na běžný řetězec.

**Syntaxe:**  
`str ConvertFromBase64(str source)`- předpokládá Unicode pro kódování  
`str ConvertFromBase64(str source, enum Encoding)`

* zdroj: Řetězec kódovaný base64  
* Kódování: Unicode, ASCII, UTF8

**Příklad**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Oba příklady vrátit "*Hello world!*"

---
### <a name="converttobase64"></a>Převést na základnu64
**Popis:**  
Funkce ConvertToBase64 převede řetězec na řetězec Unicode base64.  
Převede hodnotu pole celého čísla na ekvivalentní řetězcovou reprezentaci, která je kódována číslicemi base-64.

**Syntaxe:**  
`str ConvertToBase64(str source)`

**Příklad:**  
`ConvertToBase64("Hello world!")`  
Vrátí "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>PřevéstToUTF8Hex
**Popis:**  
Funkce ConvertToUTF8Hex převede řetězec na hodnotu kódovku Hex UTF8.

**Syntaxe:**  
`str ConvertToUTF8Hex(str source)`

**Poznámky:**  
Výstupní formát této funkce používá služba Azure Active Directory jako formát atributu DN.

**Příklad:**  
`ConvertToUTF8Hex("Hello world!")`  
Vrátí 48656C6C6F20776F726C6421

---
### <a name="count"></a>Počet
**Popis:**  
Funkce Počet vrátí počet prvků v atributu s více hodnotami.

**Syntaxe:**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>Cstr
**Popis:**  
Funkce CStr se převede na datový typ řetězce.

**Syntaxe:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* hodnota: Může být číselná hodnota, referenční atribut nebo logická hodnota.

**Příklad:**  
`CStr([dn])`  
Mohl by vrátit "cn=Joe,dc=contoso,dc=com"

---
### <a name="datefromnum"></a>DateFromNum
**Popis:**  
Funkce DateFromNum převede hodnotu ve formátu data služby AD na typ DateTime.

**Syntaxe:**  
`dt DateFromNum(num value)`

**Příklad:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Vrátí hodnotu DateTime představující 2012-01-01 23:00:00.

---
### <a name="dncomponent"></a>Součást DN
**Popis:**  
Funkce DNComponent vrátí hodnotu zadané součásti DN, která bude zadaná zleva.

**Syntaxe:**  
`str DNComponent(ref dn, num ComponentNumber)`

* dn: referenční atribut pro interpretaci
* ComponentNumber: Součást v DN vrátit

**Příklad:**  
`DNComponent(CRef([dn]),1)`  
Pokud dn je "cn = Joe, ou = ...," vrátí Joe

---
### <a name="error"></a>Chyba
**Popis:**  
Funkce Error se používá k vrácení vlastní chyby.

**Syntaxe:**  
`void Error(str ErrorMessage)`

**Příklad:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Pokud atribut accountName není k dispozici, vyvolat chybu na objekt.

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
**Popis:**  
Funkce Guid generuje nový náhodný identifikátor GUID

**Syntaxe:**  
`str Guid()`

---
### <a name="iif"></a>Iif
**Popis:**  
Funkce IIF vrátí jednu ze sady možných hodnot na základě zadané podmínky.

**Syntaxe:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* podmínka: jakákoli hodnota nebo výraz, který lze vyhodnotit na hodnotu true nebo false.
* valueIfTrue: Pokud se podmínka vyhodnotí jako true, vrácená hodnota.
* valueIfFalse: Pokud se podmínka vyhodnotí jako false, vrácená hodnota.

**Příklad:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Pokud je uživatel intern, vrátí alias uživatele s "t-" přidán na začátek, jinak vrátí alias uživatele, jak je.

---
### <a name="instr"></a>Instr
**Popis:**  
Funkce InStr vyhledá první výskyt podřetězce v řetězci.

**Syntaxe:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: řetězec, který má být prohledán
* stringmatch: řetězec, který má být nalezen
* start: počáteční pozice pro nalezení podřetězce
* porovnat: vbTextCompare nebo vbBinaryCompare

**Poznámky:**  
Vrátí pozici, kde byl nalezen podřetězec nebo 0, pokud nebyl nalezen.

**Příklad:**  
`InStr("The quick brown fox","quick")`  
Ehodnoty na 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Vyhodnotí se na 7

---
### <a name="isnull"></a>Isnull
**Popis:**  
Pokud výraz vyhodnotí na Null, pak isnull funkce vrátí true.

**Syntaxe:**  
`bool IsNull(var Expression)`

**Poznámky:**  
Pro atribut null je vyjádřena absence atributu.

**Příklad:**  
`IsNull([displayName])`  
Vrátí hodnotu True, pokud atribut není k dispozici v CS nebo MV.

---
### <a name="isnullorempty"></a>IsnullorPrázdný
**Popis:**  
Pokud je výraz null nebo prázdný řetězec, vrátí funkce IsNullOrEmpty hodnotu true.

**Syntaxe:**  
`bool IsNullOrEmpty(var Expression)`

**Poznámky:**  
Pro atribut by to vyhodnotit True, pokud atribut chybí nebo je přítomen, ale je prázdný řetězec.  
Inverzní funkce se nazývá IsPresent.

**Příklad:**  
`IsNullOrEmpty([displayName])`  
Vrátí hodnotu True, pokud atribut není přítomen nebo je prázdný řetězec v CS nebo MV.

---
### <a name="ispresent"></a>Ispresent
**Popis:**  
Pokud výraz vyhodnotí řetězec, který není Null a není prázdný, pak ispresent funkce vrátí true.

**Syntaxe:**  
`bool IsPresent(var expression)`

**Poznámky:**  
Inverzní funkce se nazývá IsNullOrEmpty.

**Příklad:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Položka
**Popis:**  
Funkce Item vrátí jednu položku z vícehodnotového řetězce/atributu.

**Syntaxe:**  
`var Item(mvstr attribute, num index)`

* atribut: atribut s více hodnotami
* index: index na položku v řetězci s více hodnotami.

**Poznámky:**  
Funkce Item je užitečná společně s funkcí Contains, protože druhá funkce vrátí index k položce v atributu s více hodnotami.

Vyvolá chybu, pokud je index mimo hranice.

**Příklad:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Vrátí primární e-mailovou adresu.

---
### <a name="isstring"></a>IsString
**Popis:**  
Pokud výraz lze vyhodnotit na typ řetězce, pak IsString funkce vyhodnotí True.

**Syntaxe:**  
`bool IsString(var expression)`

**Poznámky:**  
Slouží k určení, pokud CStr() může být úspěšné analyzovat výraz.

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
**Popis:**  
Funkce Left vrátí zadaný počet znaků zleva od řetězce.

**Syntaxe:**  
`str Left(str string, num NumChars)`

* řetězec: řetězec pro vrácení znaků z
* NumChars: číslo identifikující počet znaků, které mají být vráceny od začátku (vlevo) řetězce

**Poznámky:**  
Řetězec obsahující první znaky numChars v řetězci:

* Pokud numChars = 0, vrátí prázdný řetězec.
* Pokud numChars < 0, vrátí vstupní řetězec.
* Pokud je řetězec null, vrátí prázdný řetězec.

Pokud řetězec obsahuje méně znaků než číslo zadané v numChars, je vrácen řetězec identický s řetězcem (to znamená, že obsahuje všechny znaky v parametru 1).

**Příklad:**  
`Left("John Doe", 3)`  
Vrací objekt `Joh`.

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
### <a name="removeduplicates"></a>Removeduplicates
**Popis:**  
Funkce RemoveDuplicates přebírá řetězec s více hodnotami a ujistěte se, že každá hodnota je jedinečná.

**Syntaxe:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Příklad:**  
`RemoveDuplicates([proxyAddresses])`  
Vrátí sanitizovaný atribut proxyAddress, kde byly odebrány všechny duplicitní hodnoty.

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
  |--- | --- | --- | --- |
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
### <a name="stringfromsid"></a>StringFromSid
**Popis:**  
Funkce StringFromSid převede bajtové pole obsahující identifikátor zabezpečení na řetězec.

**Syntaxe:**  
`str StringFromSid(bin ObjectSID)`  

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

**Popis:**<br> Pokud **zdrojová** hodnota odpovídá **klíči**, vrátí **hodnotu** pro tento **klíč**. Pokud **zdrojová** hodnota neodpovídá žádné klíče, vrátí **defaultValue**.  **Klíčové** a **hodnotové** parametry musí být vždy dodávány ve dvojicích. Funkce vždy očekává sudý počet parametrů.

**Parametry:**<br> 

   | Name (Název) | Povinné/ Opakování | Typ | Poznámky |
   | --- | --- | --- | --- |
   | **Zdroj** |Požaduje se |Řetězec |**Zdrojová** hodnota ke kontrole. |
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

### <a name="trim"></a>Trim
**Popis:**  
Funkce Trim odstraní úvodní a koncové mezery z řetězce.

**Syntaxe:**  
`str Trim(str value)`  

**Příklad:**  
`Trim(" Test ")`  
Vrátí "Test".

`Trim([proxyAddresses])`  
Odebere úvodní a koncové mezery pro každou hodnotu v atributu proxyAddress.

---
### <a name="word"></a>Word
**Popis:**  
Funkce Word vrátí slovo obsažené v řetězci na základě parametrů popisujících oddělovače, které mají být používány, a číslo slova, které má být vráceno.

**Syntaxe:**  
`str Word(str string, num WordNumber, str delimiters)`

* řetězec: řetězec vrátit slovo z.
* WordNumber: číslo identifikující číslo slova, které by se mělo vrátit.
* oddělovače: řetězec představující oddělovač (oddělovače), který by měl být použit k identifikaci slov

**Poznámky:**  
Každý řetězec znaků v řetězci oddělený jedním ze znaků v oddělovačích je identifikován jako slova:

* Pokud číslo < 1, vrátí prázdný řetězec.
* Pokud je řetězec null, vrátí prázdný řetězec.

Pokud řetězec obsahuje méně než číselná slova nebo řetězec neobsahuje žádná slova identifikovaná oddělovači, je vrácen prázdný řetězec.

**Příklad:**  
`Word("The quick brown fox",3," ")`  
Vrátí "hnědou"

`Word("This,string!has&many separators",3,",!&#")`  
Vrátí "má"

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


## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
