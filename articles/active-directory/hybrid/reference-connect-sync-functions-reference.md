---
title: 'Azure AD Connect synchronizace: referenční informace o funkcích | Microsoft Docs'
description: Odkaz na deklarativní zřizovací výrazy v Azure AD Connect synchronizaci
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
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6c8be064ade8182355c320e948b3b60b846033d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348054"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Azure AD Connect Sync: Reference k funkcím
V Azure AD Connect jsou funkce použity k manipulaci s hodnotou atributu během synchronizace.  
Syntaxe funkcí je vyjádřena v následujícím formátu:  
`<output type> FunctionName(<input type> <position name>, ..)`

Pokud je funkce přetížena a přijímá více syntaxí, jsou uvedeny všechny platné syntaxe.  
Funkce jsou silného typu a ověřují, zda předaný typ odpovídá dokumentovanému typu.  
Pokud typ neodpovídá, je vyvolána chyba.

Typy jsou vyjádřeny pomocí následující syntaxe:

* **bin** – binární
* **bool** – logická hodnota
* **DT** – datum a čas UTC
* **Enum** – výčet známých konstant
* **exp** – výraz, který se očekává pro vyhodnocení na logickou hodnotu
* **mvbin** – binární soubor s více hodnotami
* **mvstr** – Vícehodnotový řetězec
* **mvref** – reference s více hodnotami
* **číslo** – číslice
* **ref** – referenční informace
* **str** – řetězec
* **var** – varianta (téměř) jakéhokoli jiného typu
* **void** – nevrací hodnotu

Funkce s typy **mvbin**, **mvstr** a **mvref** mohou pracovat pouze s více hodnotami atributů. Funkce s funkcí **bin**, **str** a **ref** jak v atributech s jednou hodnotou, tak i s více hodnotami.

## <a name="functions-reference"></a>Reference k funkcím

* **Certifikát**
  * [CertExtensionOids](#certextensionoids)
  * [CertFormat](#certformat)
  * [CertFriendlyName](#certfriendlyname)
  * [CertHashString](#certhashstring)
  * [CertIssuer](#certissuer)
  * [CertIssuerDN](#certissuerdn)
  * [CertIssuerOid](#certissueroid)
  * [CertKeyAlgorithm](#certkeyalgorithm)
  * [CertKeyAlgorithmParams](#certkeyalgorithmparams)
  * [CertNameInfo](#certnameinfo)
  * [CertNotAfter](#certnotafter)
  * [CertNotBefore](#certnotbefore)
  * [CertPublicKeyOid](#certpublickeyoid)
  * [CertPublicKeyParametersOid](#certpublickeyparametersoid)
  * [CertSerialNumber](#certserialnumber)
  * [CertSignatureAlgorithmOid](#certsignaturealgorithmoid)
  * [CertSubject](#certsubject)
  * [CertSubjectNameDN](#certsubjectnamedn)
  * [CertSubjectNameOid](#certsubjectnameoid)
  * [CertThumbprint](#certthumbprint)
  * [CertVersion](#certversion)
  * [Certifikát](#iscert)
* **Převod**
  * [CBool](#cbool)
  * [CDate](#cdate)
  * [CGuid](#cguid)
  * [ConvertFromBase64](#convertfrombase64)
  * [ConvertToBase64](#converttobase64)
  * [ConvertFromUTF8Hex](#convertfromutf8hex)
  * [ConvertToUTF8Hex](#converttoutf8hex)
  * [CNum](#cnum)
  * [CRef](#cref)
  * [CStr](#cstr)
  * [StringFromGuid](#stringfromguid)
  * [StringFromSid](#stringfromsid)
* **Datum a čas**
  * [DateAdd](#dateadd)
  * [DateFromNum](#datefromnum)
  * [FormatDateTime](#formatdatetime)
  * [Současné](#now)
  * [NumFromDate](#numfromdate)
* **Adresář**
  * [DNComponent](#dncomponent)
  * [DNComponentRev](#dncomponentrev)
  * [EscapeDNComponent](#escapedncomponent)
* **Hodnocení**
  * [IsBitSet](#isbitset)
  * [Datum a čas](#isdate)
  * [IsEmpty](#isempty)
  * [Identifikátor GUID](#isguid)
  * [IsNull](#isnull)
  * [IsNullOrEmpty](#isnullorempty)
  * [IsNumeric](#isnumeric)
  * [K dispozici](#ispresent)
  * [Řetězec](#isstring)
* **Matematické**
  * [BitAnd](#bitand)
  * [BitOr](#bitor)
  * [RandomNum](#randomnum)
* **Vícenásobný * oceněný**
  * [Zobrazí](#contains)
  * [Výpočtu](#count)
  * [Položka](#item)
  * [ItemOrNull](#itemornull)
  * [Join](#join) (Spojení)
  * [RemoveDuplicates –](#removeduplicates)
  * [Rozdělení](#split)
* **Tok programu**
  * [Chyba](#error)
  * [IIF](#iif)
  * [Výběr](#select)
  * [Přepnutí](#switch)
  * [,](#where)
  * [Řetězce](#with)
* **Text**
  * [HLAVNÍCH](#guid)
  * [InStr](#instr)
  * [InStrRev](#instrrev)
  * [LCase](#lcase)
  * [Zbývá](#left)
  * [Funkce](#len)
  * [LTrim](#ltrim)
  * [Středně](#mid)
  * [PadLeft](#padleft)
  * [PadRight](#padright)
  * [PCase](#pcase)
  * [Náhrady](#replace)
  * [ReplaceChars](#replacechars)
  * [Kliknutím](#right)
  * [RTrim](#rtrim)
  * [Sklon](#trim)
  * [UCase](#ucase)
  * [Word](#word)

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
`BitAnd(&HF, &HF7)`  
Vrátí hodnotu 7, protože hexadecimální hodnota "F" a "F7" je vyhodnocena.

---
### <a name="bitor"></a>BitOr
**Popis:**  
Funkce BitOr nastaví v hodnotě zadané bity na hodnotu.

**Syntaktick**  
`num BitOr(num value1, num value2)`

* Hodnota1, hodnota2: číselné hodnoty, které by měly být OR'ed společně

**Mark**  
Tato funkce převede oba parametry do binární reprezentace a nastaví bit na 1, pokud je jeden nebo oba odpovídající bity v masce a příznakem 1, a na hodnotu 0, pokud jsou obě odpovídající bity 0. Jinými slovy, vrátí hodnotu 1 ve všech případech s výjimkou toho, že odpovídající bity obou parametrů jsou 0.

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
### <a name="cdate"></a>CDate
**Popis:**  
Funkce CDate vrátí hodnotu DateTime UTC z řetězce. Hodnota DateTime není typem nativního atributu v synchronizaci, ale je používána některými funkcemi.

**Syntaktick**  
`dt CDate(str value)`

* Hodnota: řetězec s datem, časem a volitelně časovou zónou

**Mark**  
Vrácený řetězec je vždy ve formátu UTC.

**Příklad:**  
`CDate([employeeStartTime])`  
Vrátí hodnotu DateTime založenou na počátečním čase zaměstnance.

`CDate("2013-01-10 4:00 PM -8")`  
Vrátí hodnotu DateTime představující "2013-01-11 12:00 dop".


---
### <a name="certextensionoids"></a>CertExtensionOids
**Popis:**  
Vrátí hodnoty OID všech kritických rozšíření objektu certifikátu.

**Syntaktick**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certformat"></a>CertFormat
**Popis:**  
Vrátí název formátu tohoto certifikátu X. 509 v3.

**Syntaktick**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certfriendlyname"></a>CertFriendlyName
**Popis:**  
Vrátí přidružený alias pro certifikát.

**Syntaktick**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certhashstring"></a>CertHashString
**Popis:**  
Vrátí hodnotu hash SHA1 pro certifikát X. 509 v3 jako šestnáctkový řetězec.

**Syntaktick**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certissuer"></a>CertIssuer
**Popis:**  
Vrátí název certifikační autority, která vystavila certifikát X. 509 v3.

**Syntaktick**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certissuerdn"></a>CertIssuerDN
**Popis:**  
Vrátí rozlišující název vystavitele certifikátu.

**Syntaktick**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certissueroid"></a>CertIssuerOid
**Popis:**  
Vrátí identifikátor OID vystavitele certifikátu.

**Syntaktick**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Popis:**  
Vrátí informace o algoritmu klíče pro tento certifikát X. 509 v3 jako řetězec.

**Syntaktick**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Popis:**  
Vrátí parametry algoritmu klíče pro certifikát X. 509 v3 jako šestnáctkový řetězec.

**Syntaktick**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certnameinfo"></a>CertNameInfo
**Popis:**  
Vrátí název předmětu a vystavitele z certifikátu.

**Syntaktick**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.
*   X509NameType: hodnota X509NameType pro předmět.
*   includesIssuerName: true pro zahrnutí názvu vystavitele; v opačném případě false.

---
### <a name="certnotafter"></a>CertNotAfter
**Popis:**  
Vrátí datum v místním čase, po kterém certifikát již není platný.

**Syntaktick**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certnotbefore"></a>CertNotBefore
**Popis:**  
Vrátí datum v místním čase, kdy bude certifikát platný.

**Syntaktick**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Popis:**  
Vrátí identifikátor OID veřejného klíče pro certifikát X. 509 v3.

**Syntaktick**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Popis:**  
Vrátí identifikátor OID parametrů veřejného klíče pro certifikát X. 509 v3.

**Syntaktick**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certserialnumber"></a>CertSerialNumber
**Popis:**  
Vrátí sériové číslo certifikátu X. 509 v3.

**Syntaktick**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Popis:**  
Vrátí identifikátor OID algoritmu použitého k vytvoření podpisu certifikátu.

**Syntaktick**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certsubject"></a>CertSubject
**Popis:**  
Získá rozlišující název subjektu z certifikátu.

**Syntaktick**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Popis:**  
Vrátí rozlišující název subjektu z certifikátu.

**Syntaktick**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Popis:**  
Vrátí identifikátor OID názvu subjektu z certifikátu.

**Syntaktick**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certthumbprint"></a>CertThumbprint
**Popis:**  
Vrátí kryptografický otisk certifikátu.

**Syntaktick**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="certversion"></a>CertVersion
**Popis:**  
Vrátí verzi formátu X. 509 certifikátu.

**Syntaktick**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.

---
### <a name="cguid"></a>CGuid
**Popis:**  
Funkce CGuid převede řetězcovou reprezentaci identifikátoru GUID na jeho binární reprezentaci.

**Syntaktick**  
`bin CGuid(str GUID)`

* Řetězec formátovaný v tomto vzoru: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx nebo {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

---
### <a name="contains"></a>Contains
**Popis:**  
Funkce Contains vyhledá řetězec uvnitř vícehodnotového atributu.

**Syntaktick**  
`num Contains (mvstring attribute, str search)` – rozlišuje velká a malá písmena  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)` – rozlišuje velká a malá písmena

* atribut: vícehodnotový atribut, který má být hledán.
* Search: řetězec, který se má najít v atributu.
* Casetype: CaseInsensitive nebo CaseSensitive.

Vrátí index v atributu s více hodnotami, kde byl řetězec nalezen. hodnota 0 se vrátí, pokud řetězec nebyl nalezen.

**Mark**  
Pro řetězcové atributy s více hodnotami hledá hledání v hodnotách podřetězce.  
V případě referenčních atributů musí hledaný řetězec přesně odpovídat hodnotě, která má být považována za shodu.

**Příklad:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Pokud má atribut proxyAddresses primární e-mailovou adresu (označenou jako velká písmena "SMTP:"), vrátí atribut proxyAddress, jinak vrátí chybu.

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
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Popis:**  
Funkce ConvertFromUTF8Hex převede zadanou hodnotu kódovanou v šestnáctkovém formátu UTF8 na řetězec.

**Syntaktick**  
`str ConvertFromUTF8Hex(str source)`

* Zdroj: UTF8 2-Byte kódovaný Sting

**Mark**  
Rozdíl mezi touto funkcí a ConvertFromBase64 ([], UTF8) v tom, že výsledek je uživatelsky přívětivý pro atribut DN.  
Tento formát používá Azure Active Directory jako DN.

**Příklad:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Vrátí "*Hello World!*"

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
### <a name="cnum"></a>CNum
**Popis:**  
Funkce CNum přebírá řetězec a vrací číselný datový typ.

**Syntaktick**  
`num CNum(str value)`

---
### <a name="cref"></a>CRef
**Popis:**  
Převede řetězec na atribut reference.

**Syntaktick**  
`ref CRef(str value)`

**Příklad:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

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
### <a name="dateadd"></a>DateAdd
**Popis:**  
Vrátí datum obsahující datum, ke kterému byl přidán zadaný časový interval.

**Syntaktick**  
`dt DateAdd(str interval, num value, dt date)`

* interval: řetězcový výraz, který je časový interval, který chcete přidat. Řetězec musí mít jednu z následujících hodnot:
  * RRRR Year
  * q – čtvrtletí
  * m měsíc
  * y den roku
  * d den
  * w Weekday
  * WW týden
  * h hodina
  * n minuta
  * s sekundou
* hodnota: počet jednotek, které chcete přidat. Může to být kladné (pro získání dat v budoucnosti) nebo záporné (pro získání dat v minulosti).
* Date: DateTime představuje datum, do kterého se přidá interval.

**Příklad:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Přidá 3 měsíce a vrátí hodnotu DateTime představující "2001-04-01".

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
### <a name="dncomponentrev"></a>DNComponentRev
**Popis:**  
Funkce DNComponentRev vrací hodnotu zadané složky DN z pravého (konce).

**Syntaktick**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* DN: atribut reference, který se má interpretovat
* ComponentNumber – komponenta v rozlišujícím názvu, která se má vrátit
* Možnosti: DC – ignorovat všechny komponenty s "DC ="

**Příklad:**  
Pokud je DN "CN = Jan, OU = Brno, OU = GA, OU = US, DC = contoso, DC = com", pak  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Jak nás vrátíme.

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
### <a name="escapedncomponent"></a>EscapeDNComponent
**Popis:**  
Funkce EscapeDNComponent přebírá jednu komponentu DN a řídí ji, aby ji bylo možné znázornit v protokolu LDAP.

**Syntaktick**  
`str EscapeDNComponent(str value)`

**Příklad:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Zajistí, že objekt může být vytvořen v adresáři LDAP, i když má atribut DisplayName znaky, které musí být uvozeny v protokolu LDAP.

---
### <a name="formatdatetime"></a>FormatDateTime
**Popis:**  
Funkce FormatDateTime slouží k formátování hodnoty DateTime na řetězec se zadaným formátem.

**Syntaktick**  
`str FormatDateTime(dt value, str format)`

* Value: hodnota ve formátu data a času
* Format: řetězec představující formát, na který se má převést.

**Mark**  
Možné hodnoty pro tento formát najdete tady: [vlastní formáty data a času pro funkci Format](/dax/custom-date-and-time-formats-for-the-format-function).

**Příklad:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Má za následek "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Může mít za následek "20140905081453.0 Z"

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
### <a name="instrrev"></a>InStrRev
**Popis:**  
Funkce InStrRev najde poslední výskyt podřetězce v řetězci.

**Syntaktick**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: řetězec, který má být prohledán
* stringmatch: řetězec, který se má najít
* Start: počáteční pozice pro vyhledání podřetězce
* porovnání: vbTextCompare nebo vbBinaryCompare

**Mark**  
Vrátí pozici, kde byl dílčí řetězec nalezen, nebo 0, pokud nebyl nalezen.

**Příklad:**  
`InStrRev("abbcdbbbef","bb")`  
Vrátí hodnotu 7

---
### <a name="isbitset"></a>IsBitSet
**Popis:**  
Funkce IsBitSet testuje, jestli je nastavený bit nebo ne.

**Syntaktick**  
`bool IsBitSet(num value, num flag)`

* Value (hodnota): číselná hodnota, která je vyhodnocena. příznak: číselná hodnota, která má bit k vyhodnocení

**Příklad:**  
`IsBitSet(&HF,4)`  
Vrátí hodnotu true, protože bit "4" je nastaven v hexadecimální hodnotě "F".

---
### <a name="isdate"></a>Datum a čas
**Popis:**  
Pokud je možné výraz vyhodnotit jako typ DateTime, je funkce-Date vyhodnocena jako true.

**Syntaktick**  
`bool IsDate(var Expression)`

**Mark**  
Slouží k určení, zda může být akce CDate () úspěšná.

---
### <a name="iscert"></a>Certifikát
**Popis:**  
Vrátí hodnotu true, pokud lze nezpracovaná data serializovat do objektu certifikátu .NET X509Certificate2.

**Syntaktick**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: bajtová reprezentace pole certifikátu X. 509. Bajtové pole může být binární (DER) kódovaný nebo X. 509 data s kódováním base64.
---
### <a name="isempty"></a>IsEmpty
**Popis:**  
Pokud je atribut přítomen v CS nebo MV, ale vyhodnotí se jako prázdný řetězec, vyhodnotí se funkce Empty na hodnotu true.

**Syntaktick**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>Identifikátor GUID
**Popis:**  
Pokud je možné řetězec převést na identifikátor GUID, je funkce identifikátoru ". GUID" vyhodnocena na hodnotu true.

**Syntaktick**  
`bool IsGuid(str GUID)`

**Mark**  
Identifikátor GUID je definován jako řetězec za jedním z těchto vzorů: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx nebo {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Slouží k určení, zda může být CGuid () úspěšné.

**Příklad:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Pokud má StrAttribute formát GUID, vrátí binární reprezentaci, jinak vrátí hodnotu null.

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
### <a name="isnumeric"></a>IsNumeric
**Popis:**  
Funkce-numeric vrátí logickou hodnotu, která označuje, zda lze výraz vyhodnotit jako typ čísla.

**Syntaktick**  
`bool IsNumeric(var Expression)`

**Mark**  
Slouží k určení, zda může být CNum () úspěšné k analýze výrazu.

---
### <a name="isstring"></a>Řetězec
**Popis:**  
Pokud je možné výraz vyhodnotit na typ řetězce, pak je funkce typu String vyhodnocena jako true.

**Syntaktick**  
`bool IsString(var expression)`

**Mark**  
Slouží k určení, zda může být CStr () úspěšné k analýze výrazu.

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
### <a name="itemornull"></a>ItemOrNull
**Popis:**  
Funkce ItemOrNull vrací jednu položku z násobných řetězců nebo atributů s více hodnotami.

**Syntaktick**  
`var ItemOrNull(mvstr attribute, num index)`

* atribut: vícehodnotový atribut
* index: index položky v řetězci s více hodnotami.

**Mark**  
Funkce ItemOrNull je užitečná společně s funkcí Contains, protože druhá funkce vrací index na položku v vícehodnotovém atributu.

Pokud je index mimo rozsah, pak vrátí hodnotu null.

---
### <a name="join"></a>Spojit
**Popis:**  
Funkce Join vezme řetězec s více hodnotami a vrátí řetězec s jednou hodnotou se zadaným oddělovačem vloženým mezi každou položkou.

**Syntaktick**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* atribut: vícehodnotový atribut obsahující řetězce, které mají být spojeny.
* oddělovač: libovolný řetězec, který slouží k oddělení podřetězců ve vráceném řetězci. Je-li tento parametr vynechán, je použit znak mezery (""). Pokud oddělovač je řetězec nulové délky ("") nebo nic, všechny položky v seznamu se zřetězí bez oddělovačů.

**Poznámky**  
Mezi funkcemi JOIN a Split je parita. Funkce Join přebírá pole řetězců a spojuje je pomocí řetězce oddělovače, aby vrátila jediný řetězec. Funkce Split přebírá řetězec a odděluje ho na oddělovači, aby vracelo pole řetězců. Klíčovým rozdílem je však, že spojení může zřetězit řetězce s libovolným řetězcem oddělovače, rozdělit může pouze oddělit řetězce pomocí jediného oddělovače znaků.

**Příklad:**  
`Join([proxyAddresses],",")`  
Může vrátit: " SMTP:john.doe@contoso.com , smtp:jd@contoso.com "

---
### <a name="lcase"></a>LCase
**Popis:**  
Funkce LCase převede všechny znaky v řetězci na malá písmena.

**Syntaktick**  
`str LCase(str value)`

**Příklad:**  
`LCase("TeSt")`  
Vrátí "test".

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
Vrátí "Joh".

---
### <a name="len"></a>Len
**Popis:**  
Funkce len vrátí počet znaků v řetězci.

**Syntaktick**  
`num Len(str value)`

**Příklad:**  
`Len("John Doe")`  
Vrátí 8

---
### <a name="ltrim"></a>LTrim
**Popis:**  
Funkce LTrim odebere z řetězce úvodní prázdné znaky.

**Syntaktick**  
`str LTrim(str value)`

**Příklad:**  
`LTrim(" Test ")`  
Vrátí "test"

---
### <a name="mid"></a>Mid
**Popis:**  
Funkce Mid vrátí zadaný počet znaků ze zadané pozice v řetězci.

**Syntaktick**  
`str Mid(str string, num start, num NumChars)`

* řetězec: řetězec, ze kterého se mají vracet znaky
* začátek: číslo identifikující počáteční pozici v řetězci, ze které se mají vracet znaky
* NumChars: číslo určující počet znaků, které se mají vrátit z pozice v řetězci

**Mark**  
Vrátí numChars znaky od pozice začátek v řetězci.  
Řetězec obsahující numChars znaky z pozice začíná v řetězci:

* Pokud numChars = 0, vrátí prázdný řetězec.
* Pokud numChars < 0, vrátí se vstupní řetězec.
* Pokud se spustí > délka řetězce, vrátí se vstupní řetězec.
* Pokud je hodnota Start <= 0, vrátí se vstupní řetězec.
* Pokud má řetězec hodnotu null, vrátí prázdný řetězec.

Pokud nejsou v řetězci od začátku pozice numChar znaky, co je vráceno, kolik znaků je možné.

**Příklad:**  
`Mid("John Doe", 3, 5)`  
Vrátí "HN do".

`Mid("John Doe", 6, 999)`  
Vrátí "Chvojková"

---
### <a name="now"></a>Now
**Popis:**  
Funkce Now vrací hodnotu DateTime určující aktuální datum a čas podle systémového data a času v počítači.

**Syntaktick**  
`dt Now()`

---
### <a name="numfromdate"></a>NumFromDate
**Popis:**  
Funkce NumFromDate vrací datum ve formátu data AD.

**Syntaktick**  
`num NumFromDate(dt value)`

**Příklad:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Vrátí 129699324000000000

---
### <a name="padleft"></a>PadLeft
**Popis:**  
Funkce PadLeft opustila řetězec na zadanou délku pomocí zadaného ohraničujícího znaku.

**Syntaktick**  
`str PadLeft(str string, num length, str padCharacter)`

* řetězec: řetězec, který se má vyblokovat.
* Délka: celé číslo představující požadovanou délku řetězce.
* padCharacter: řetězec skládající se z jednoho znaku, který se má použít jako znak plochy.

**Mark**

* Pokud je délka řetězce menší než délka, pak je padCharacter opakovaně připojen k začátku (vlevo) řetězce, dokud nemá délku, která se rovná délce.
* PadCharacter může být znak mezery, ale nemůže být hodnota null.
* Pokud délka řetězce je větší nebo rovna délce, řetězec se vrátí beze změny.
* Pokud má řetězec délku větší nebo rovnou délce, vrátí se řetězec shodný s řetězcem.
* Pokud je délka řetězce menší než délka, pak je vrácen nový řetězec požadované délky obsahující řetězec, který je doplněn na padCharacter.
* Pokud má řetězec hodnotu null, funkce vrátí prázdný řetězec.

**Příklad:**  
`PadLeft("User", 10, "0")`  
Vrátí "000000User".

---
### <a name="padright"></a>PadRight
**Popis:**  
Funkce PadRight napraví řetězec na zadanou délku pomocí zadaného ohraničujícího znaku.

**Syntaktick**  
`str PadRight(str string, num length, str padCharacter)`

* řetězec: řetězec, který se má vyblokovat.
* Délka: celé číslo představující požadovanou délku řetězce.
* padCharacter: řetězec skládající se z jednoho znaku, který se má použít jako znak plochy.

**Mark**

* Pokud je délka řetězce menší než délka, pak je padCharacter opakovaně připojen ke konci (vpravo) řetězce, dokud nemá délku, která se rovná délce.
* padCharacter může být znak mezery, ale nemůže být hodnota null.
* Pokud délka řetězce je větší nebo rovna délce, řetězec se vrátí beze změny.
* Pokud má řetězec délku větší nebo rovnou délce, vrátí se řetězec shodný s řetězcem.
* Pokud je délka řetězce menší než délka, pak je vrácen nový řetězec požadované délky obsahující řetězec, který je doplněn na padCharacter.
* Pokud má řetězec hodnotu null, funkce vrátí prázdný řetězec.

**Příklad:**  
`PadRight("User", 10, "0")`  
Vrátí "User000000".

---
### <a name="pcase"></a>PCase
**Popis:**  
Funkce PCase převede první znak všech slov oddělených mezerou v řetězci na velká písmena a všechny ostatní znaky jsou převedeny na malá písmena.

**Syntaktick**  
`String PCase(string)`

**Mark**

* Tato funkce v současné době neposkytuje správné používání velkých a malých písmen, jako je například akronym.

**Příklad:**  
`PCase("TEsT")`  
Vrátí "test".

`PCase(LCase("TEST"))`  
Vrátí "test"

---
### <a name="randomnum"></a>RandomNum
**Popis:**  
Funkce RandomNum vrací náhodné číslo mezi zadaným intervalem.

**Syntaktick**  
`num RandomNum(num start, num end)`

* začátek: číslo určující spodní limit náhodné hodnoty, který se má vygenerovat
* end: číslo identifikující horní mez náhodné hodnoty, která se má generovat

**Příklad:**  
`Random(100,999)`  
Může vrátit 734.

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
**Popis:**  
Funkce Replace nahradí všechny výskyty řetězce na jiný řetězec.

**Syntaktick**  
`str Replace(str string, str OldValue, str NewValue)`

* řetězec: řetězec, ve kterém mají být nahrazeny hodnoty.
* OldValue: řetězec, který má být hledán a který má být nahrazen.
* NewValue: řetězec, který má být nahrazen.

**Mark**  
Funkce rozpoznává následující speciální monikery:

* \n – nový řádek
* \r – návrat na začátek řádku
* \t – karta

**Příklad:**  
`Replace([address],"\r\n",", ")`  
Nahrazuje znak CRLF čárkou a mezerou a může vést k "One Microsoft Way, Redmond, WA, USA".

---
### <a name="replacechars"></a>ReplaceChars
**Popis:**  
Funkce ReplaceChars nahradí všechny výskyty znaků nalezených v řetězci ReplacePattern.

**Syntaktick**  
`str ReplaceChars(str string, str ReplacePattern)`

* String: řetězec, ve kterém mají být nahrazeny znaky.
* ReplacePattern: řetězec obsahující slovník se znaky, které mají být nahrazeny.

Formát je {source1}: {Target1}, {SOURCE2}: {TARGET2}, {sourceN}, {targetN}, kde Source je znak, který se má najít, a cílit na řetězec, který se má nahradit.

**Mark**

* Funkce přebírá všechny výskyty definovaných zdrojů a nahrazuje je cíli.
* Zdroj musí být přesně jeden znak (Unicode).
* Zdroj nemůže být prázdný nebo delší než jeden znak (Chyba analýzy).
* Cíl může mít více znaků, například ö: OE, β: ss.
* Cíl může být prázdný, což znamená, že by měl být odebraný znak.
* Zdroj rozlišuje velká a malá písmena a musí být přesná shoda.
* Oddělovače, (čárka) a: (dvojtečka) jsou vyhrazené znaky a nelze je nahradit pomocí této funkce.
* Mezery a jiné prázdné znaky v řetězci ReplacePattern jsou ignorovány.

**Příklad:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Vrátí Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Vrátí "ONeil", jedna značka je definována pro odebrání.

---
### <a name="right"></a>Vpravo
**Popis:**  
Funkce Right vrátí zadaný počet znaků od pravého (konce) řetězce.

**Syntaktick**  
`str Right(str string, num NumChars)`

* řetězec: řetězec, ze kterého se mají vracet znaky
* NumChars: číslo určující počet znaků, které se mají vrátit od konce (vpravo) řetězce

**Mark**  
NumChars znaky jsou vráceny z poslední pozice řetězce.

Řetězec obsahující poslední numChars znaky v řetězci:

* Pokud numChars = 0, vrátí prázdný řetězec.
* Pokud numChars < 0, vrátí se vstupní řetězec.
* Pokud má řetězec hodnotu null, vrátí prázdný řetězec.

Pokud řetězec obsahuje méně znaků než číslo zadané v NumChars, vrátí se řetězec shodný s řetězcem.

**Příklad:**  
`Right("John Doe", 3)`  
Vrátí "Chvojková".

---
### <a name="rtrim"></a>RTrim
**Popis:**  
Funkce RTrim odebere na konci řetězce koncové prázdné znaky.

**Syntaktick**  
`str RTrim(str value)`

**Příklad:**  
`RTrim(" Test ")`  
Vrátí "test".

---
### <a name="select"></a>Vyberte
**Popis:**  
Zpracuje všechny hodnoty v vícehodnotovém atributu (nebo výstupu výrazu) na základě zadané funkce.

**Syntaktick**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* Item: představuje element v vícehodnotovém atributu.
* atribut: vícehodnotový atribut
* výraz: výraz, který vrací kolekci hodnot
* podmínka: jakákoli funkce, která může zpracovat položku v atributu.

**Příklady:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Vrátí všechny hodnoty v vícehodnotovém atributu otherPhone po odebrání spojovníků (-).

---
### <a name="split"></a>Rozdělení
**Popis:**  
Funkce Split přebírá řetězec oddělený oddělovačem a vytváří jej jako řetězec s více hodnotami.

**Syntaktick**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* hodnota: řetězec se znakem oddělovače, který se má oddělit.
* oddělovač: jeden znak, který se má použít jako oddělovač.
* limit: maximální počet hodnot, které mohou vracet.

**Příklad:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Vrací Vícehodnotový řetězec se dvěma prvky, které jsou užitečné pro atribut proxyAddress.

---
### <a name="stringfromguid"></a>StringFromGuid
**Popis:**  
Funkce StringFromGuid přebírá binární identifikátor GUID a převede ho na řetězec.

**Syntaktick**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>StringFromSid
**Popis:**  
Funkce StringFromSid převede pole bajtů obsahující identifikátor zabezpečení na řetězec.

**Syntaktick**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>Přepínač
**Popis:**  
Funkce Switch slouží k vrácení jedné hodnoty na základě vyhodnocených podmínek.

**Syntaktick**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* výraz: variantní výraz, který chcete vyhodnotit.
* Value: hodnota, která se vrátí, pokud má odpovídající výraz hodnotu true.

**Mark**  
Seznam argumentů funkce switch se skládá ze párů výrazů a hodnot. Výrazy jsou vyhodnocovány zleva doprava a je vrácena hodnota přidružená k prvnímu výrazu, který má být vyhodnocen na hodnotu true. Pokud tyto části nejsou správně spárovány, dojde k chybě za běhu.

Například pokud má argument Výraz1 hodnotu true, přepínač vrátí hodnota1. Pokud je výraz-1 false, ale výraz 2 má hodnotu true, přepínač vrátí hodnotu-2 a tak dále.

Přepínač vrátí prázdnou hodnotu, pokud:

* Žádné výrazy nejsou pravdivé.
* První výraz true má odpovídající hodnotu null.

Přepínač vyhodnocuje všechny výrazy, i když vrátí pouze jeden z nich. Z tohoto důvodu byste se měli podívat na nežádoucí vedlejší účinky. Například pokud vyhodnocení jakéhokoli výrazu způsobí chybu dělení nulou, dojde k chybě.

Hodnota může být také funkce Error, která by vrátila vlastní řetězec.

**Příklad:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Vrátí jazyk používaný v některých hlavních městech, jinak vrátí chybu.

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
### <a name="ucase"></a>UCase
**Popis:**  
Funkce UCase převede všechny znaky v řetězci na velká písmena.

**Syntaktick**  
`str UCase(str string)`

**Příklad:**  
`UCase("TeSt")`  
Vrátí "TEST".

---
### <a name="where"></a>Kde

**Popis:**  
Vrací podmnožinu hodnot z vícehodnotového atributu (nebo výstup výrazu) na základě konkrétní podmínky.

**Syntaktick**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* Item: představuje element v vícehodnotovém atributu.
* atribut: vícehodnotový atribut
* podmínka: libovolný výraz, který lze vyhodnotit na hodnotu true nebo false.
* výraz: výraz, který vrací kolekci hodnot

**Příklad:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Vrátí hodnoty certifikátů v vícehodnotovém atributu userCertificate, jejichž platnost ještě nevypršela.

---
### <a name="with"></a>With
**Popis:**  
Funkce with poskytuje způsob, jak zjednodušit složitý výraz pomocí proměnné pro reprezentaci dílčího výrazu, který se ve složitém výrazu objevuje jednou nebo vícekrát.

**Syntaktick**
`With(var variable, exp subExpression, exp complexExpression)`  
* proměnná: představuje dílčí výraz.
* dílčí výraz: dílčí výraz reprezentovaný proměnnou.
* complexExpression: složitý výraz.

**Příklad:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
Je funkčně ekvivalentní:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Který vrátí pouze hodnoty certifikátů, jejichž platnost vypršela, v atributu userCertificate.


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

## <a name="additional-resources"></a>Další materiály
* [Principy výrazů deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect synchronizace: přizpůsobení možností synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)