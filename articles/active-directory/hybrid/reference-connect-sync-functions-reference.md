---
title: 'Synchronizace Azure AD Connect: Funkce Reference | Dokumentace Microsoftu'
description: Odkaz na výrazů deklarativního zřizování ve službě Azure AD Connect sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 54378876ed3f8326a19aafa4d71347d17f1c4bab
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162544"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Synchronizace Azure AD Connect: Reference k funkcím
Ve službě Azure AD Connect funkce se používají k práci s hodnotu atributu během synchronizace.  
Syntaxe funkce je vyjádřena pomocí následujícího formátu:  
`<output type> FunctionName(<input type> <position name>, ..)`

Pokud funkce je přetížená a přijímá více syntaxe, jsou uvedeny všechny platné syntaxe.  
Funkce jsou silného typu a po ověření, že typ předaný v odpovídá dokumentovanému typu.  
Pokud typ neodpovídá, je vržena chyba.

Typy jsou vyjádřeny s následující syntaxí:

* **bin** – binární
* **BOOL** – Boolean
* **DT** – datum/čas UTC
* **výčet** – výčet známé konstanty
* **Exp** – výraz, který je vyhodnocena jako logická hodnota
* **mvbin** – vícehodnotových binární
* **mvstr** – vícehodnotových řetězců
* **mvref** – odkaz na vícehodnotových
* **NUM** – číselné
* **REF** – referenční dokumentace
* **Str** – řetězce
* **var** – hodnotu typu variant (téměř) jakéhokoli jiného typu
* **void** – nevrací hodnotu

Funkce s typy **mvbin**, **mvstr**, a **mvref** funguje jenom u více jednohodnotových atributů. Funguje s **bin**, **str**, a **ref** pracovat na atributy jednohodnotové i více Vážíme si toho.

## <a name="functions-reference"></a>Reference k funkcím
| Seznam funkcí |  |  |  |  |
| --- | --- | --- | --- | --- | --- |
| **Certifikát** | | | | |
| [CertExtensionOids](#certextensionoids) |[CertFormat](#certformat) |[CertFriendlyName](#certfriendlyname) |[CertHashString](#certhashstring) | |
| [CertIssuer](#certissuer) |[CertIssuerDN](#certissuerdn) |[CertIssuerOid](#certissueroid) |[CertKeyAlgorithm](#certkeyalgorithm) | |
| [CertKeyAlgorithmParams](#certkeyalgorithmparams) |[CertNameInfo](#certnameinfo) |[CertNotAfter](#certnotafter) |[CertNotBefore](#certnotbefore) | |
| [CertPublicKeyOid](#certpublickeyoid) |[CertPublicKeyParametersOid](#certpublickeyparametersoid) |[CertSerialNumber](#certserialnumber) |[CertSignatureAlgorithmOid](#certsignaturealgorithmoid) | |
| [CertSubject](#certsubject) |[CertSubjectNameDN](#certsubjectnamedn) |[CertSubjectNameOid](#certsubjectnameoid) |[CertThumbprint](#certthumbprint) | |
[ CertVersion](#certversion) |[IsCert](#iscert) | | | |
| **Převod** | | | | |
| [CBool](#cbool) |[CDate](#cdate) |[CGuid](#cguid) |[ConvertFromBase64](#convertfrombase64) | |
| [ConvertToBase64](#converttobase64) |[ConvertFromUTF8Hex](#convertfromutf8hex) |[ConvertToUTF8Hex](#converttoutf8hex) |[CNum](#cnum) | |
| [CRef](#cref) |[CStr](#cstr) |[StringFromGuid](#StringFromGuid) |[StringFromSid](#stringfromsid) | |
| **Datum / čas** | | | | |
| [DateAdd](#dateadd) |[DateFromNum](#datefromnum) |[FormatDateTime](#formatdatetime) |[Nyní](#now) | |
| [NumFromDate](#numfromdate) | | | | |
| **Adresář** | | | | |
| [DNComponent](#dncomponent) |[DNComponentRev](#dncomponentrev) |[EscapeDNComponent](#escapedncomponent) | | |
| **Hodnocení** | | | | |
| [IsBitSet](#isbitset) |[IsDate](#isdate) |[IsEmpty](#isempty) |[IsGuid](#isguid) | |
| [IsNull](#isnull) |[IsNullOrEmpty](#isnullorempty) |[IsNumeric](#isnumeric) |[IsPresent](#ispresent) | |
| [IsString](#isstring) | | | | |
| **Matematické** | | | | |
| [BitAnd](#bitand) |[BitOr](#bitor) |[RandomNum](#randomnum) | | |
| **Více Vážíme si toho** | | | | |
| [Obsahuje](#contains) |[Počet](#count) |[Položka](#item) |[ItemOrNull](#itemornull) | |
| [Spojení](#join) |[Removeduplicates –](#removeduplicates) |[rozdělení](#split) | | |
| **Program Flow** | | | | |
| [Chyba](#error) |[IIF](#iif) |[Výběr](#select) |[Přepínač](#switch) | |
| [kde](#where) |[s](#with) | | | |
| **Text** | | | | |
| [GUID](#guid) |[InStr](#instr) |[InStrRev](#instrrev) |[LCase](#lcase) | |
| [doleva](#left) |[Délka](#len) |[LTrim](#ltrim) |[Mid](#mid) | |
| [PadLeft](#padleft) |[PadRight –](#padright) |[PCase](#pcase) |[nahradit](#replace) | |
| [ReplaceChars](#replacechars) |[doprava](#right) |[RTrim](#rtrim) |[Trim](#trim) | |
| [UCase](#ucase) |[Aplikace Word](#word) | | | |

- - -
### <a name="bitand"></a>BitAnd
**Popis:**  
BitAnd – funkce nastaví na hodnotu zadanou bity.

**Syntaxe:**  
`num BitAnd(num value1, num value2)`

* Hodnota1; hodnota2: číselné hodnoty, které by se měly logickým společně

**Poznámky:**  
Tato funkce převede oba parametry do binárního formátu a nastaví se bit:

* 0 – když jeden nebo oba odpovídající bity *maska* a *příznak* mají hodnotu 0
* 1 – Pokud jsou obě odpovídající bitů 1.

Jinými slovy vrátí hodnotu 0 ve všech případech, kromě případů, kdy odpovídající bits oba parametry jsou 1.

**Příklad:**  
`BitAnd(&HF, &HF7)`  
Protože šestnáctkové "F" a "F7" vyhodnocení na tuto hodnotu, vrátí funkce hodnotu 7.

- - -
### <a name="bitor"></a>BitOr
**Popis:**  
BitOr – funkce nastaví na hodnotu zadanou bity.

**Syntaxe:**  
`num BitOr(num value1, num value2)`

* Hodnota1; hodnota2: číselné hodnoty, které by měl být společně sjednocením (OR)

**Poznámky:**  
Tato funkce převede oba parametry do binárního formátu a nastaví bit na hodnotu 1, pokud jeden nebo oba odpovídající bity maska a příznak jsou 1 a na hodnotu 0 Pokud jsou obě odpovídající bitů 0. Jinými slovy vrátí se 1 ve všech případech, kromě případů, kdy odpovídající bits oba parametry 0.

- - -
### <a name="cbool"></a>CBool
**Popis:**  
CBool – funkce vrátí logickou hodnotu, podle vyhodnocený výraz

**Syntaxe:**  
`bool CBool(exp Expression)`

**Poznámky:**  
Pokud výraz vyhodnocen jako nenulovou hodnotu, pak CBool – vrátí hodnotu True, jinak vrátí False.

**Příklad:**  
`CBool([attrib1] = [attrib2])`  

Vrátí hodnotu True, pokud oba atributy mají stejnou hodnotu.

- - -
### <a name="cdate"></a>CDate
**Popis:**  
CDate – funkce vrací hodnotu DateTime UTC z řetězce. Datum a čas se nejedná o typ nativní atribut synchronizovaný, ale používá některé funkce.

**Syntaxe:**  
`dt CDate(str value)`

* Hodnota: Řetězec datum, čas a volitelně časové pásmo

**Poznámky:**  
Vrácený řetězec je vždy ve standardu UTC.

**Příklad:**  
`CDate([employeeStartTime])`  
Vrátí datum a čas v závislosti na zaměstnance počáteční čas

`CDate("2013-01-10 4:00 PM -8")`  
Vrátí datum a čas představující "2013-01-11 12:00 dop."


- - -
### <a name="certextensionoids"></a>CertExtensionOids
**Popis:**  
Vrátí hodnoty Oid pro všechny kritické rozšíření objekt certifikátu.

**Syntaxe:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certformat"></a>CertFormat
**Popis:**  
Vrátí název formátu tento certifikát x.509 v3.

**Syntaxe:**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certfriendlyname"></a>CertFriendlyName
**Popis:**  
Vrátí související alias pro certifikát.

**Syntaxe:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certhashstring"></a>CertHashString
**Popis:**  
Vrátí hodnotu hash SHA1 pro certifikát x.509 v3 jako šestnáctkový řetězec.

**Syntaxe:**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certissuer"></a>CertIssuer
**Popis:**  
Vrátí název certifikační autority, která vydala certifikát x.509 v3.

**Syntaxe:**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certissuerdn"></a>CertIssuerDN
**Popis:**  
Vrátí rozlišující název vystavitele certifikátu.

**Syntaxe:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certissueroid"></a>CertIssuerOid
**Popis:**  
Vrátí identifikátor Oid vystavitele certifikátu.

**Syntaxe:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Popis:**  
Vrátí informace o algoritmus klíče pro tento certifikát x.509 v3 jako řetězec.

**Syntaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Popis:**  
Vrátí parametry algoritmus klíče pro certifikát x.509 v3 jako šestnáctkový řetězec.

**Syntaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certnameinfo"></a>CertNameInfo
**Popis:**  
Vrátí předmět a vystavitele názvy z certifikátu.

**Syntaxe:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.
*   X509NameType: X509NameType hodnoty pro předmět.
*   includesIssuerName: true, pokud chcete zahrnout název vystavitele; v opačném případě hodnota false.

- - -
### <a name="certnotafter"></a>CertNotAfter
**Popis:**  
Vrátí datum v místní čase, po kterém certifikát již není platný.

**Syntaxe:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certnotbefore"></a>CertNotBefore
**Popis:**  
Vrátí datum v místní čase, kdy certifikát vstupuje v platnost.

**Syntaxe:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Popis:**  
Vrátí identifikátor veřejné klíče pro certifikát x.509 v3.

**Syntaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Popis:**  
Vrátí identifikátor Oid parametrů veřejného klíče pro certifikát x.509 v3.

**Syntaxe:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certserialnumber"></a>CertSerialNumber
**Popis:**  
Vrátí sériové číslo certifikátu x.509 v3.

**Syntaxe:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Popis:**  
Vrátí identifikátor Oid algoritmus použitý k vytvoření podpisu certifikátu.

**Syntaxe:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certsubject"></a>CertSubject
**Popis:**  
Získá název subjektu rozlišující z certifikátu.

**Syntaxe:**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Popis:**  
Vrátí název subjektu rozlišující z certifikátu.

**Syntaxe:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Popis:**  
Vrátí identifikátor názvu subjektu z certifikátu.

**Syntaxe:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certthumbprint"></a>certThumbprint
**Popis:**  
Vrátí kryptografický otisk certifikátu.

**Syntaxe:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certversion"></a>CertVersion
**Popis:**  
Vrátí verzi formátu X.509 certifikátu.

**Syntaxe:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="cguid"></a>CGuid
**Popis:**  
Funkce CGuid převede řetězcové vyjádření identifikátoru GUID na binární vyjádření.

**Syntaxe:**  
`bin CGuid(str GUID)`

* V tomto modelu ve formátu řetězce: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx nebo {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

- - -
### <a name="contains"></a>Contains
**Popis:**  
Funkce obsahuje najde řetězec uvnitř vícehodnotového atributu

**Syntaxe:**  
`num Contains (mvstring attribute, str search)` -malá a velká písmena  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)` -malá a velká písmena

* Atribut: atribut více Vážíme si toho pro hledání.
* hledání: řetězec k vyhledání v atributu.
* Casetype: CaseInsensitive nebo CaseSensitive.

Vrátí index atribut více Vážíme si toho, kde byl nalezen řetězec. 0 je vrácena, jestliže řetězec nebyl nalezen.

**Poznámky:**  
Pro atributy více Vážíme si toho řetězce hledáním podřetězců v hodnotách.  
Pro atributy typu odkaz prohledávaného řetězce musí přesně odpovídat hodnota, která má být považovány za shodné.

**Příklad:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Pokud má atribut proxyAddresses primární e-mailovou adresu (indikován velká písmena "SMTP:"), pak vrátit atribut proxyAddress, jinak vrátí chybu.

- - -
### <a name="convertfrombase64"></a>ConvertFromBase64
**Popis:**  
ConvertFromBase64 funkce převede hodnotu zadaného kódovanou jako base64 regulární řetězec.

**Syntaxe:**  
`str ConvertFromBase64(str source)` -předpokládá Unicode pro kódování  
`str ConvertFromBase64(str source, enum Encoding)`

* Zdroj: Řetězec kódovaný ve formátu Base64  
* Kódování: Unicode, ASCII, UTF-8

**Příklad**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Oba příklady vrátí "*Hello world!*"

- - -
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Popis:**  
ConvertFromUTF8Hex funkce převede zadanou hodnotu kódovaný Hex UTF8 na řetězec.

**Syntaxe:**  
`str ConvertFromUTF8Hex(str source)`

* Zdroj: Kódovaný stingu 2 byte UTF8

**Poznámky:**  
Rozdíl mezi této funkce a ConvertFromBase64([],UTF8), výsledkem je popisný pro atribut rozlišující název.  
Tento formát se používá služba Azure Active Directory jako rozlišující název.

**Příklad:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Vrátí "*Hello world!*"

- - -
### <a name="converttobase64"></a>ConvertToBase64
**Popis:**  
Funkce ConvertToBase64 převede řetězec na řetězec znaků Unicode ve formátu base64.  
Převede hodnotu pole celých čísel na její ekvivalentní řetězcové vyjádření s kódováním base-64 číslic.

**Syntaxe:**  
`str ConvertToBase64(str source)`

**Příklad:**  
`ConvertToBase64("Hello world!")`  
Returns "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

- - -
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Popis:**  
Funkce ConvertToUTF8Hex převede řetězec na hodnotu UTF8 Hex kódování.

**Syntaxe:**  
`str ConvertToUTF8Hex(str source)`

**Poznámky:**  
Výstupní formát této funkce používá Azure Active Directory jako formát rozlišující název atributu.

**Příklad:**  
`ConvertToUTF8Hex("Hello world!")`  
Returns 48656C6C6F20776F726C6421

- - -
### <a name="count"></a>Počet
**Popis:**  
Count – funkce vrátí počet prvků v vícehodnotového atributu

**Syntaxe:**  
`num Count(mvstr attribute)`

- - -
### <a name="cnum"></a>CNum
**Popis:**  
Funkce CNum přebírá řetězec a vrací číselným datovým typem.

**Syntaxe:**  
`num CNum(str value)`

- - -
### <a name="cref"></a>CRef
**Popis:**  
Převede řetězec na atribut typu odkaz

**Syntaxe:**  
`ref CRef(str value)`

**Příklad:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

- - -
### <a name="cstr"></a>CStr
**Popis:**  
CStr – funkce převede na datový typ string.

**Syntaxe:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* Hodnota: Může být číselnou hodnotu, atribut odkaz nebo datový typ Boolean.

**Příklad:**  
`CStr([dn])`  
Můžou se zobrazovat "cn = Joe, dc = contoso, dc = com"

- - -
### <a name="dateadd"></a>Funkce DateAdd
**Popis:**  
Vrátí hodnotu data obsahující datum, do které byl přidán zadaný časový interval.

**Syntaxe:**  
`dt DateAdd(str interval, num value, dt date)`

* interval: Řetězcový výraz představující dobu, kterou chcete přidat. Řetězec musí obsahovat jednu z následujících hodnot:
  * RRRR rok
  * q čtvrtletí
  * m měsíc
  * y den roku
  * d Day
  * w den v týdnu
  * TT týden
  * h Hour
  * n minutách
  * s druhou
* Hodnota: Počet jednotek, které chcete přidat. Může být (Chcete-li získat data v budoucnosti) kladná nebo záporná (Chcete-li získat data v minulosti).
* datum: Data a času představující datum, do kterého se přidá interval.

**Příklad:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Přidá 3 měsíce a vrátí datum a čas představující "2001-04-01".

- - -
### <a name="datefromnum"></a>DateFromNum
**Popis:**  
Funkce DateFromNum převede hodnotu v AD data naformátovat na typ DateTime.

**Syntaxe:**  
`dt DateFromNum(num value)`

**Příklad:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Vrátí datum a čas představující 2012-01-01 23:00:00

- - -
### <a name="dncomponent"></a>DNComponent
**Popis:**  
DNComponent funkce vrátí hodnotu zadané rozlišující název součásti bude zleva.

**Syntaxe:**  
`str DNComponent(ref dn, num ComponentNumber)`

* rozlišující název: atribut odkazu pro interpretaci
* ComponentNumber: Součástí DN vrátit

**Příklad:**  
`DNComponent(CRef([dn]),1)`  
Pokud je název domény "cn = Joe, ou =...," vrátí Joe

- - -
### <a name="dncomponentrev"></a>DNComponentRev
**Popis:**  
DNComponentRev funkce vrátí hodnotu zadané rozlišující název součásti přejdete přímo (ukončit).

**Syntaxe:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* rozlišující název: atribut odkazu pro interpretaci
* ComponentNumber - součástí DN vrátit
* Možnosti: Řadiče domény – ignorovat všechny součásti s "dc ="

**Příklad:**  
Pokud rozlišující název je "cn Joe, ou = Atlanta, ou = GA, ou = = US, dc = contoso, dc = com" pak  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Oba vracejí USA.

- - -
### <a name="error"></a>Chyba
**Popis:**  
Chyba funkce se používá k vrácení vlastní chybu.

**Syntaxe:**  
`void Error(str ErrorMessage)`

**Příklad:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Pokud atribut název účtu není k dispozici, vyvolá chybu u objektu.

- - -
### <a name="escapedncomponent"></a>EscapeDNComponent
**Popis:**  
Funkce EscapeDNComponent vezme jednu součást názvu domény a řídicí sekvence, takže můžou být vyjádřeny v protokolu LDAP.

**Syntaxe:**  
`str EscapeDNComponent(str value)`

**Příklad:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Zajišťuje, že objekt můžete vytvořit v adresáři LDAP i v případě atributu displayName obsahuje znaky, které musí být uvozena v protokolu LDAP.

- - -
### <a name="formatdatetime"></a>formatDateTime
**Popis:**  
Funkce FormatDateTime slouží k formátování datum a čas na řetězec pomocí určeného formátu

**Syntaxe:**  
`str FormatDateTime(dt value, str format)`

* hodnota: hodnota ve formátu data a času
* formát: řetězec představující převést na formát.

**Poznámky:**  
Možné hodnoty pro formát najdete tady: [Vlastní datum a čas pro formátování funkce formátů](https://docs.microsoft.com/dax/custom-date-and-time-formats-for-the-format-function).

**Příklad:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Výsledky v "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Může mít za následek "20140905081453.0Z"

- - -
### <a name="guid"></a>Guid
**Popis:**  
Funkce Guid vygeneruje nový náhodný GUID

**Syntaxe:**  
`str Guid()`

- - -
### <a name="iif"></a>IIF
**Popis:**  
Funkce IIF vrací jednu sadu možných hodnot na základě zadané podmínky.

**Syntaxe:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* Podmínka: Libovolná hodnota nebo výraz, který může být vyhodnocen na hodnotu true nebo false.
* valueIfTrue: Pokud je podmínka vyhodnocena jako true, vrácené hodnoty.
* valueIfFalse: Pokud je podmínka vyhodnocena jako false, vrácené hodnoty.

**Příklad:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Pokud se jedná učně vrátí vrátí alias uživatele s"t" přidá na začátek ho jiný alias uživatele, jako je.

- - -
### <a name="instr"></a>InStr
**Popis:**  
Funkce InStr vyhledá první výskyt podřetězce v řetězci

**Syntaxe:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* prohledávaný_řetězec: řetězec, který má být prohledán
* hledaný_řetězec: řetězec, která se má najít
* Start: počáteční pozice najde dílčí řetězec
* porovnání: vbTextCompare nebo vbBinaryCompare

**Poznámky:**  
Vrátí pozici, kde podřetězec nebyl nalezen nebo 0, pokud není nalezena.

**Příklad:**  
`InStr("The quick brown fox","quick")`  
Hodnoty na 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Vyhodnotí jako 7

- - -
### <a name="instrrev"></a>InStrRev
**Popis:**  
Funkce InStrRev najde poslední výskyt podřetězce v řetězci

**Syntaxe:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* prohledávaný_řetězec: řetězec, který má být prohledán
* hledaný_řetězec: řetězec, která se má najít
* Start: počáteční pozice najde dílčí řetězec
* porovnání: vbTextCompare nebo vbBinaryCompare

**Poznámky:**  
Vrátí pozici, kde podřetězec nebyl nalezen nebo 0, pokud není nalezena.

**Příklad:**  
`InStrRev("abbcdbbbef","bb")`  
Vrátí hodnotu 7

- - -
### <a name="isbitset"></a>IsBitSet
**Popis:**  
Funkce IsBitSet testuje, zda je tento bit nastaven, nebo ne

**Syntaxe:**  
`bool IsBitSet(num value, num flag)`

* hodnota: Číselná hodnota, která je evaluated.flag: Číselná hodnota, která má bit, který se má vyhodnotit

**Příklad:**  
`IsBitSet(&HF,4)`  
Vrátí hodnotu True, protože je nastaven bit "4" v šestnáctkové hodnotě "F"

- - -
### <a name="isdate"></a>IsDate
**Popis:**  
Pokud výraz může být vyhodnocena jako typ DateTime, pak IsDate funkce vyhodnotí jako True.

**Syntaxe:**  
`bool IsDate(var Expression)`

**Poznámky:**  
Umožňuje určit, pokud CDate() může být úspěšné.

- - -
### <a name="iscert"></a>IsCert
**Popis:**  
Vrátí true, pokud lze serializovat nezpracovaných dat do objektů .NET X509Certificate2 certifikátu.

**Syntaxe:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Bajtové pole reprezentace certifikátu X.509. Bajtové pole může být kódovaný binární soubor (DER) nebo data s kódováním Base64 X.509.
- - -
### <a name="isempty"></a>IsEmpty
**Popis:**  
Pokud atribut nachází v CS nebo MV ale vyhodnocen jako prázdný řetězec, Funkce IsEmpty vyhodnotí na hodnotu True.

**Syntaxe:**  
`bool IsEmpty(var Expression)`

- - -
### <a name="isguid"></a>IsGuid
**Popis:**  
Pokud řetězec může převést na identifikátor GUID, funkce IsGuid vyhodnocena na hodnotu true.

**Syntaxe:**  
`bool IsGuid(str GUID)`

**Poznámky:**  
Identifikátor GUID je definovaný jako řetězec za jednu z těchto vzorců: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx nebo {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Umožňuje určit, pokud CGuid() může být úspěšné.

**Příklad:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Pokud StrAttribute má formát identifikátoru GUID, vrátí binární reprezentaci, jinak vrátí hodnotu Null.

- - -
### <a name="isnull"></a>IsNull
**Popis:**  
Pokud je výraz vyhodnocen jako Null, IsNull funkce vrátí hodnotu true.

**Syntaxe:**  
`bool IsNull(var Expression)`

**Poznámky:**  
Pro atribut s hodnotou Null se vyjádří chybí atribut.

**Příklad:**  
`IsNull([displayName])`  
Vrátí hodnotu PRAVDA, pokud atribut není k dispozici v CS nebo MV.

- - -
### <a name="isnullorempty"></a>IsNullOrEmpty
**Popis:**  
Pokud výraz má hodnotu null nebo prázdný řetězec, vrátí funkce IsNullOrEmpty hodnotu true.

**Syntaxe:**  
`bool IsNullOrEmpty(var Expression)`

**Poznámky:**  
Pro atribut tím by vyhodnotí na hodnotu True pokud je atribut chybí nebo je k dispozici ale je prázdný řetězec.  
Inverzní funkce k této funkci jmenuje IsPresent.

**Příklad:**  
`IsNullOrEmpty([displayName])`  
Vrátí hodnotu PRAVDA, pokud atribut není k dispozici nebo je prázdný řetězec v CS nebo MV.

- - -
### <a name="isnumeric"></a>IsNumeric
**Popis:**  
Funkce IsNumeric vrací logickou hodnotu označující, zda může být výraz vyhodnocen jako typ number.

**Syntaxe:**  
`bool IsNumeric(var Expression)`

**Poznámky:**  
Umožňuje určit, pokud CNum() může být úspěšné parsovat výraz.

- - -
### <a name="isstring"></a>IsString
**Popis:**  
Pokud výraz lze vyhodnotit na typ řetězce, funkce IsString vyhodnotí na hodnotu True.

**Syntaxe:**  
`bool IsString(var expression)`

**Poznámky:**  
Umožňuje určit, pokud CStr() může být úspěšné parsovat výraz.

- - -
### <a name="ispresent"></a>IsPresent
**Popis:**  
Pokud je výraz vyhodnocen jako řetězec, který nemá hodnotu Null a není prázdný, vrátí funkce IsPresent hodnotu true.

**Syntaxe:**  
`bool IsPresent(var expression)`

**Poznámky:**  
Inverzní funkce k této funkci jmenuje IsNullOrEmpty.

**Příklad:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

- - -
### <a name="item"></a>Položka
**Popis:**  
Funkce Item vrátí jednu položku z více Vážíme si toho řetězec nebo atributu.

**Syntaxe:**  
`var Item(mvstr attribute, num index)`

* Atribut: vícehodnotového atributu
* index: index s položkou v řetězci více Vážíme si toho.

**Poznámky:**  
Položky funkce je užitečná spolu s obsahuje funkce, protože druhá funkce vrátí index položky v atributu více Vážíme si toho.

Vyvolá chybu, pokud je index mimo rozsah.

**Příklad:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Vrátí primární e-mailovou adresu.

- - -
### <a name="itemornull"></a>ItemOrNull
**Popis:**  
Funkce ItemOrNull vrátí jednu položku z více Vážíme si toho řetězec nebo atributu.

**Syntaxe:**  
`var ItemOrNull(mvstr attribute, num index)`

* Atribut: vícehodnotového atributu
* index: index s položkou v řetězci více Vážíme si toho.

**Poznámky:**  
ItemOrNull funkce je užitečná spolu s obsahuje funkce, protože druhá funkce vrátí index položky v atributu více Vážíme si toho.

Pokud je index mimo rozsah, vrátí hodnotu Null.

- - -
### <a name="join"></a>Spojit
**Popis:**  
Funkce připojení k více Vážíme si toho řetězec a vrátí řetězec s jednou hodnotou pomocí zadaného oddělovače vložen mezi každou položku.

**Syntaxe:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* Atribut: Více Vážíme si toho atribut obsahující řetězce, který se má spojit.
* oddělovač: Libovolný řetězec použitý k oddělení podřetězců ve vráceném řetězci. Pokud tento parametr vynechán, znak mezery ("") se používá. Oddělovač je řetězec nulové délky ("") nebo nic, všechny položky v seznamu jsou spojeny s žádné oddělovače.

**Poznámky**  
Mezi funkce připojení a rozdělení je rovnocenné. Funkce spojení přijímá pole řetězců a připojí je k vrácení jednoho řetězce, pomocí řetězec oddělovače. Funkce rozdělení řetězce a odděluje na oddělovač, jak vrátit pole řetězců. Klíčovým rozdílem je však, že připojení lze zřetězit řetězce s libovolný řetězec oddělovače, rozdělení můžete oddělit pouze řetězce pomocí jednoho znaku oddělovače.

**Příklad:**  
`Join([proxyAddresses],",")`  
Můžou se zobrazovat: "SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

- - -
### <a name="lcase"></a>LCase
**Popis:**  
Funkce LCase převede všechny znaky v řetězci na malá písmena.

**Syntaxe:**  
`str LCase(str value)`

**Příklad:**  
`LCase("TeSt")`  
Vrátí "test".

- - -
### <a name="left"></a>Vlevo
**Popis:**  
Left – funkce vrátí zadaný počet znaků z levé strany řetězce.

**Syntaxe:**  
`str Left(str string, num NumChars)`

* řetězec: řetězec, který se vrátí znaky ze
* NumChars: číslo určující počet znaků, které má vrátit z řetězce začátku (vlevo)

**Poznámky:**  
Řetězec obsahující první numChars znaky v řetězci:

* Pokud numChars = 0, vrátí prázdný řetězec.
* Pokud numChars < 0, vrátí vstupní řetězec.
* Pokud má řetězec hodnotu null, vrátí prázdný řetězec.

Pokud řetězec obsahuje méně znaků, než číslo zadané v poli numChars, je vrácen řetězec shodné s řetězci (který je, který obsahuje všechny znaky v parametru 1).

**Příklad:**  
`Left("John Doe", 3)`  
Vrátí "Joh".

- - -
### <a name="len"></a>Délka
**Popis:**  
Funkce Len vrátí počet znaků v řetězci.

**Syntaxe:**  
`num Len(str value)`

**Příklad:**  
`Len("John Doe")`  
Vrátí 8

- - -
### <a name="ltrim"></a>LTrim
**Popis:**  
LTrim funkce odebere úvodní mezery v řetězci.

**Syntaxe:**  
`str LTrim(str value)`

**Příklad:**  
`LTrim(" Test ")`  
Vrátí "Test"

- - -
### <a name="mid"></a>Mid
**Popis:**  
Část funkce vrátí zadaný počet znaků od určené pozice v řetězci.

**Syntaxe:**  
`str Mid(str string, num start, num NumChars)`

* řetězec: řetězec, který se vrátí znaky ze
* Start: číslo určující počáteční pozici v řetězci vrátit znaků z
* NumChars: číslo určující počet znaků, které má vrátit z pozice v řetězci

**Poznámky:**  
Vrátí numChars znaků od počáteční pozice v řetězci.  
Řetězec obsahující numChars znaků od počáteční pozice v řetězci:

* Pokud numChars = 0, vrátí prázdný řetězec.
* Pokud numChars < 0, vrátí vstupní řetězec.
* Pokud spuštění > délku řetězce, vrátí vstupní řetězec.
* Pokud spuštění < = 0, vrátí vstupní řetězec.
* Pokud má řetězec hodnotu null, vrátí prázdný řetězec.

Pokud již nejsou numChar znaků zbývajících v řetězci od počáteční pozice, jako jsou vráceny co nejvíce znakům.

**Příklad:**  
`Mid("John Doe", 3, 5)`  
Vrátí "hn".

`Mid("John Doe", 6, 999)`  
Vrátí "Doe"

- - -
### <a name="now"></a>Ihned
**Popis:**  
Funkce nyní vrací hodnotu DateTime, zadáte aktuální datum a čas, podle systémového data a času v počítači.

**Syntaxe:**  
`dt Now()`

- - -
### <a name="numfromdate"></a>NumFromDate
**Popis:**  
Funkce NumFromDate vrátí datum ve formátu AD data.

**Syntaxe:**  
`num NumFromDate(dt value)`

**Příklad:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Vrátí 129699324000000000

- - -
### <a name="padleft"></a>padLeft
**Popis:**  
PadLeft funkce vlevo-dotyková zařízení a řetězce na určenou délku pomocí zadané odsazení znaku.

**Syntaxe:**  
`str PadLeft(str string, num length, str padCharacter)`

* řetězec: řetězec pro vyplnění.
* Délka: Celé číslo představující požadovanou délku řetězce.
* padCharacter: Řetězec sestávající z jednoho znaku pro použití jako znak panel

**Poznámky:**

* Pokud délka řetězce je menší než délka, pak padCharacter opakovaně připojí do začátku (vlevo) řetězce dokud má délku rovna délce.
* PadCharacter může být znak mezery, ale nemůže být hodnota null.
* Pokud délka řetězce je roven nebo větší než délka, řetězec je vrácen beze změny.
* Pokud řetězec má délku větší než nebo rovna délce, vrátí se stejný řetězec jako řetězec.
* Pokud délka řetězce je menší než délka, je vrácena nový řetězec má požadovanou délku obsahující řetězce, aby bylo vytvořeno padCharacter.
* Pokud má řetězec hodnotu null, funkce vrátí prázdný řetězec.

**Příklad:**  
`PadLeft("User", 10, "0")`  
Vrátí "000000User".

- - -
### <a name="padright"></a>PadRight –
**Popis:**  
PadRight – funkce vpravo-dotyková zařízení a řetězce na určenou délku pomocí zadané odsazení znaku.

**Syntaxe:**  
`str PadRight(str string, num length, str padCharacter)`

* řetězec: řetězec pro vyplnění.
* Délka: Celé číslo představující požadovanou délku řetězce.
* padCharacter: Řetězec sestávající z jednoho znaku pro použití jako znak panel

**Poznámky:**

* Pokud délka řetězce je menší než délka, pak padCharacter opakovaně připojen na konci (vpravo) řetězce dokud má délku rovna délce.
* PadCharacter může být znak mezery, ale nemůže být hodnota null.
* Pokud délka řetězce je roven nebo větší než délka, řetězec je vrácen beze změny.
* Pokud řetězec má délku větší než nebo rovna délce, vrátí se stejný řetězec jako řetězec.
* Pokud délka řetězce je menší než délka, je vrácena nový řetězec má požadovanou délku obsahující řetězce, aby bylo vytvořeno padCharacter.
* Pokud má řetězec hodnotu null, funkce vrátí prázdný řetězec.

**Příklad:**  
`PadRight("User", 10, "0")`  
Vrátí "User000000".

- - -
### <a name="pcase"></a>PCase
**Popis:**  
Funkce PCase převede první znak každého oddělených mezerami slova v řetězci na velká písmena a všechny ostatní znaky jsou převedeny na malá písmena.

**Syntaxe:**  
`String PCase(string)`

**Poznámky:**

* Tato funkce aktuálně neposkytuje správná velká a malá písmena pro převod, který je zcela uváděny velkými písmeny, například zkratka slova.

**Příklad:**  
`PCase("TEsT")`  
Vrátí "Test".

`PCase(LCase("TEST"))`  
Vrátí "Test"

- - -
### <a name="randomnum"></a>RandomNum
**Popis:**  
Funkce RandomNum Vrátí náhodné číslo v rozsahu zadaného intervalu.

**Syntaxe:**  
`num RandomNum(num start, num end)`

* Start: číslo určující dolní mez náhodnou hodnotu ke generování
* end: číslo určující horní limit počtu náhodnou hodnotu ke generování

**Příklad:**  
`Random(100,999)`  
Může vrátit 734.

- - -
### <a name="removeduplicates"></a>Removeduplicates –
**Popis:**  
Removeduplicates – funkce vezme více Vážíme si toho řetězce a ujistěte se, že každá hodnota je jedinečný.

**Syntaxe:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Příklad:**  
`RemoveDuplicates([proxyAddresses])`  
Vrátí atribut proxyAddress upravený, ve kterém byly odebrány všechny duplicitní hodnoty.

- - -
### <a name="replace"></a>Nahradit
**Popis:**  
Funkce Replace nahradí všechny výskyty řetězce do jiného řetězce.

**Syntaxe:**  
`str Replace(str string, str OldValue, str NewValue)`

* Řetězec: Řetězec k nahrazení hodnoty.
* OldValue: Řetězec, který chcete vyhledat a nahradit.
* NewValue: Řetězec, který má nahradit.

**Poznámky:**  
Funkce rozpoznává následující speciální monikery:

* \n – nový řádek
* \r – CR
* \t – karta

**Příklad:**  
`Replace([address],"\r\n",", ")`  
Nahradí CRLF čárkami a místa a může vést k "Jeden Microsoft způsobem, Redmond, WA, USA"

- - -
### <a name="replacechars"></a>ReplaceChars
**Popis:**  
Funkce ReplaceChars nahradí všechny výskyty znaků v řetězci ReplacePattern nalezen.

**Syntaxe:**  
`str ReplaceChars(str string, str ReplacePattern)`

* Řetězec: Řetězec pro nahrazení znaků.
* ReplacePattern: řetězec obsahující slovníku pomocí znaků pro nahrazení.

Formát je {zdroj1}: {target1}, {zdroj2}: {target2}, {zdrojN}, {targetN} kde zdroj je znak, který má vyhledat a cílit na řetězec, který má nahradit.

**Poznámky:**

* Funkce má každý výskyt definované zdrojů a nahradí je cíle.
* Zdroj musí být právě jednoho znaku (unicode).
* Zdroj nemůže být prázdná ani delší než jeden znak. (Chyba při analýze).
* Cíl může mít více znaků, například ö:oe, β:ss.
* Cílem může být prázdný, která udává, že znak, který má být odebrána.
* Zdroj je velká a malá písmena a musí být přesnou shodou.
* (Čárka) a: (dvojtečka) jsou vyhrazené znaky a nedá se změnit pomocí této funkce.
* Mezery a další bílé znaky v řetězci ReplacePattern jsou ignorovány.

**Příklad:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Vrátí Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Vrátí "ONeil", jeden značek je definována k odebrání.

- - -
### <a name="right"></a>Vpravo
**Popis:**  
Right – funkce vrátí zadaný počet znaků z pravé strany (konec) na řetězec.

**Syntaxe:**  
`str Right(str string, num NumChars)`

* řetězec: řetězec, který se vrátí znaky ze
* NumChars: číslo určující počet znaků, které má vrátit z konci (vpravo) řetězce

**Poznámky:**  
NumChars znaky jsou vráceny z poslední pozici řetězce.

Řetězec obsahující poslední numChars znaky v řetězci:

* Pokud numChars = 0, vrátí prázdný řetězec.
* Pokud numChars < 0, vrátí vstupní řetězec.
* Pokud má řetězec hodnotu null, vrátí prázdný řetězec.

Pokud řetězec obsahuje méně znaků, než číslo zadané v poli NumChars, vrátí se stejný řetězec jako řetězec.

**Příklad:**  
`Right("John Doe", 3)`  
Vrátí "Doe".

- - -
### <a name="rtrim"></a>RTrim
**Popis:**  
Funkce RTrim odebere koncové prázdné znaky v řetězci.

**Syntaxe:**  
`str RTrim(str value)`

**Příklad:**  
`RTrim(" Test ")`  
Vrátí "Test".

- - -
### <a name="select"></a>Vyberte
**Popis:**  
Proces všechny hodnoty ve více Vážíme si toho atribut (nebo výstupní výrazu) založené na zadanou funkci.

**Syntaxe:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* Položka: Reprezentuje element v vícehodnotového atributu
* Atribut: vícehodnotového atributu
* výraz: výraz, který vrací kolekci hodnot
* Podmínka: všechny funkce, která dokáže zpracovávat položky v atributu

**Příklady:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Vrátí všechny hodnoty ve více Vážíme si toho atribut otherPhone po odebrání pomlčky (-).

- - -
### <a name="split"></a>Rozdělit
**Popis:**  
Funkce rozdělení přijímá řetězec oddělené oddělovačem a zpřístupňuje je řetězec více Vážíme si toho.

**Syntaxe:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* hodnota: řetězec s oddělovací znak pro oddělení.
* oddělovač: jeden znak, který se použije jako oddělovač.
* limit: maximální počet hodnot, které můžou vrátit.

**Příklad:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Vrátí řetězec více Vážíme si toho s 2 prvky užitečné pro atribut proxyAddress.

- - -
### <a name="stringfromguid"></a>StringFromGuid
**Popis:**  
Funkce StringFromGuid vezme binární GUID a převede ho na řetězec

**Syntaxe:**  
`str StringFromGuid(bin GUID)`

- - -
### <a name="stringfromsid"></a>StringFromSid
**Popis:**  
Funkce StringFromSid převede bajtové pole obsahující identifikátor zabezpečení na řetězec.

**Syntaxe:**  
`str StringFromSid(bin ObjectSID)`  

- - -
### <a name="switch"></a>Přepínač
**Popis:**  
Přepínač funkce se používá k vrácení jednu hodnotu podle vyhodnocených podmínek.

**Syntaxe:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* expr: Varianty výraz, který chcete vyhodnotit.
* Hodnota: Hodnota má být vrácena, pokud je odpovídající výraz hodnotu True.

**Poznámky:**  
Seznam argumentů funkce přepínače se skládá z dvojice výrazů a hodnoty. Výrazy jsou vyhodnocovány zleva doprava a vrátí se hodnota přidružená k první výraz k vyhodnocení na hodnotu True. Pokud součásti nejsou správně spárované, dojde k chybě za běhu.

Například pokud Výraz1 hodnotu True, vrátí přepínač hodnota1. Pokud je výraz-1, ale výraz-2 má hodnotu True, přepínač vrátí hodnotu 2 a tak dále.

Přepínač vrátí Nothing pokud:

* Žádný z výrazů není pravda.
* První výraz hodnotu True, má odpovídající hodnotu, která má hodnotu Null.

Přepínač vyhodnotí všechny výrazy, přestože se vracel pouze jeden z nich. Z tohoto důvodu je třeba dávat pozor na nežádoucí vedlejší účinky. Například pokud je hodnocení libovolný výraz výsledkem dělení nulou, dojde k chybě.

Hodnota může být také chybovou funkci, což by mohlo vrátit vlastní řetězec.

**Příklad:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Vrátí jazyk používaný v některých hlavních měst, v opačném případě vrátí chybu.

- - -
### <a name="trim"></a>Oříznout
**Popis:**  
Funkce Trim odstraní úvodní a koncové mezery z řetězce.

**Syntaxe:**  
`str Trim(str value)`  

**Příklad:**  
`Trim(" Test ")`  
Vrátí "Test".

`Trim([proxyAddresses])`  
Odebere úvodní a koncové mezery pro každou hodnotu v atribut proxyAddress.

- - -
### <a name="ucase"></a>UCase
**Popis:**  
Funkce UCase převede všechny znaky v řetězci na velká písmena.

**Syntaxe:**  
`str UCase(str string)`

**Příklad:**  
`UCase("TeSt")`  
Vrátí "TEST".

- - -
### <a name="where"></a>Kde

**Popis:**  
Vrátí část hodnoty z více Vážíme si toho atribut (nebo výstup výrazu) na základě konkrétní podmínky.

**Syntaxe:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* Položka: Reprezentuje element v vícehodnotového atributu
* Atribut: vícehodnotového atributu
* Podmínka: libovolný výraz, který lze vyhodnotit na hodnotu true nebo false
* výraz: výraz, který vrací kolekci hodnot

**Příklad:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Návratové hodnoty certifikátu v userCertificate více hodnot atributů, které nejsou vypršela platnost.

- - -
### <a name="with"></a>S
**Popis:**  
Funkce s poskytuje způsob, jak zjednodušit složitý výraz pomocí proměnné k reprezentaci dílčí výraz, který se zobrazí jednu nebo víckrát v složitý výraz.

**Syntaxe:**
`With(var variable, exp subExpression, exp complexExpression)`  
* Proměnná: Představuje dílčí výraz.
* dílčí výraz: dílčí výraz reprezentována proměnné.
* complexExpression: Složitý výraz.

**Příklad:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
Je funkčně ekvivalentní:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Která atributem userCertificate vrací pouze hodnoty certifikátu nevypršela.


- - -
### <a name="word"></a>Word
**Popis:**  
Word vrátí slova obsažené v rámci řetězce, na základě parametrů popisující oddělovače použít a jaké číslo slovo, které má vrátit.

**Syntaxe:**  
`str Word(str string, num WordNumber, str delimiters)`

* řetězec: řetězec, který se vrátí slova.
* WordNumber: číslo identifikující číslo slovo, které by měl vrátit.
* oddělovače: řetězec představující oddělovače, který se má použít k identifikaci slov

**Poznámky:**  
Každý řetězec znaků v řetězci oddělené jedním ze znaků oddělovače jsou označeny jako slova:

* Pokud číslo < 1, vrátí prázdný řetězec.
* Pokud má řetězec hodnotu null, vrátí prázdný řetězec.

Pokud řetězec obsahuje méně než počet slov nebo řetězec neobsahuje žádná slova identifikovaný oddělovače, je vrácen prázdný řetězec.

**Příklad:**  
`Word("The quick brown fox",3," ")`  
Vrátí "brown"

`Word("This,string!has&many separators",3,",!&#")`  
Vrátí "má"

## <a name="additional-resources"></a>Další prostředky
* [Principy výrazů deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Synchronizace služby Azure AD Connect: Přizpůsobení možností synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
