---
title: 'Synchronizace Azure AD Connect: referenční dokumentace funkcí | Microsoft Docs'
description: Odkaz výrazů deklarativního zřizování v synchronizaci Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4814d53a86b0d90cf16f76e75c7044448cf791eb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595151"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Synchronizace Azure AD Connect: odkaz na funkce
Ve službě Azure AD Connect funkce se používají k manipulaci s hodnotou atributu během synchronizace.  
Syntaxe funkce je vyjádřena v následujícím formátu:  
`<output type> FunctionName(<input type> <position name>, ..)`

Pokud funkci je přetížena a přijímá více syntaxe, jsou uvedeny všechny platné syntaxe.  
Funkce jsou silného typu a jejich ověřte, že typ předané odpovídá zdokumentovaných typu.  
Pokud typ neodpovídá, je vyvolána k chybě.

Typy jsou vyjádřeny s následující syntaxí:

* **Koš** – binární
* **BOOL** – Boolean
* **DT** – datum a čas UTC
* **výčet** – výčet známé konstanty
* **Exp** – výraz, který je vyhodnocena jako logická hodnota
* **mvbin** – vícehodnotových binární
* **mvstr** – vícehodnotových řetězců
* **mvref** – vícehodnotových odkaz
* **Poče** – číselné
* **REF** – odkaz
* **Str –** – řetězec
* **var** – hodnotu typu variant (téměř) žádného jiného typu
* **void** – nevrací hodnotu

Funkce s typy **mvbin**, **mvstr**, a **mvref** můžete používat jenom u více hodnot atributů. Funkce s **bin**, **str**, a **ref** pracovní jednohodnotové i více hodnot atributů.

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
| [CBool –](#cbool) |[CDate –](#cdate) |[CGuid](#cguid) |[ConvertFromBase64](#convertfrombase64) | |
| [ConvertToBase64](#converttobase64) |[ConvertFromUTF8Hex](#convertfromutf8hex) |[ConvertToUTF8Hex](#converttoutf8hex) |[CNum](#cnum) | |
| [CRef –](#cref) |[CStr](#cstr) |[StringFromGuid](#StringFromGuid) |[StringFromSid](#stringfromsid) | |
| **Datum a čas** | | | | |
| [Funkce DateAdd](#dateadd) |[DateFromNum](#datefromnum) |[FormatDateTime](#formatdatetime) |[Nyní](#now) | |
| [NumFromDate](#numfromdate) | | | | |
| **Adresář** | | | | |
| [DNComponent](#dncomponent) |[DNComponentRev](#dncomponentrev) |[EscapeDNComponent](#escapedncomponent) | | |
| **Vyhodnocení** | | | | |
| [IsBitSet](#isbitset) |[IsDate](#isdate) |[IsEmpty –](#isempty) |[IsGuid](#isguid) | |
| [IsNull](#isnull) |[IsNullOrEmpty](#isnullorempty) |[IsNumeric](#isnumeric) |[IsPresent](#ispresent) | |
| [IsString](#isstring) | | | | |
| **Matematické** | | | | |
| [BitAnd](#bitand) |[BitOr](#bitor) |[RandomNum](#randomnum) | | |
| **Více hodnot** | | | | |
| [Obsahuje](#contains) |[Počet](#count) |[Položka](#item) |[ItemOrNull](#itemornull) | |
| [Spojení](#join) |[Removeduplicates –](#removeduplicates) |[Rozdělení](#split) | | |
| **Tok programu** | | | | |
| [Chyba](#error) |[IIF](#iif) |[Výběr](#select) |[přepínače](#switch) | |
| [kde](#where) |[S](#with) | | | |
| **Text** | | | | |
| [GUID](#guid) |[InStr](#instr) |[InStrRev](#instrrev) |[LCase](#lcase) | |
| [Vlevo](#left) |[Len](#len) |[LTrim](#ltrim) |[Mid –](#mid) | |
| [padLeft](#padleft) |[PadRight –](#padright) |[PCase](#pcase) |[Nahradit](#replace) | |
| [ReplaceChars](#replacechars) |[Vpravo](#right) |[RTrim](#rtrim) |[Uvolnění dočasné paměti](#trim) | |
| [UCase](#ucase) |[Aplikace Word](#word) | | | |

- - -
### <a name="bitand"></a>BitAnd
**Popis:**  
Funkce BitAnd nastaví na hodnotu určeného bits.

**Syntaxe:**  
`num BitAnd(num value1, num value2)`

* Hodnota1, hodnota2: číselných hodnot, které by měly být logickým společně

**Poznámky:**  
Tato funkce převede oba parametry binární reprezentace a nastaví trochu na:

* 0 – Pokud jeden nebo oba odpovídající bity *maska* a *příznak* mají hodnotu 0
* 1 – Pokud jsou obě odpovídající bity 1.

Jinými slovy vrátí hodnotu 0 ve všech případech kromě případů, kdy jsou odpovídající bity oba parametry 1.

**Příklad:**  
`BitAnd(&HF, &HF7)`  
Vrátí hodnotu 7, protože hexadecimální "F" a "F7" vyhodnocení na tuto hodnotu.

- - -
### <a name="bitor"></a>BitOr
**Popis:**  
Funkce BitOr nastaví na hodnotu určeného bits.

**Syntaxe:**  
`num BitOr(num value1, num value2)`

* Hodnota1, hodnota2: číselných hodnot, které by měly být společně sjednocením (OR)

**Poznámky:**  
Tato funkce převede oba parametry binární reprezentace a nastaví trochu 1, pokud jeden nebo oba odpovídající bity maska a příznak je 1 a na 0, pokud jsou obě odpovídající bity 0. Jinými slovy vrátí 1 ve všech případech, s výjimkou případů, kdy odpovídající bity oba parametry mají hodnotu 0.

- - -
### <a name="cbool"></a>CBool –
**Popis:**  
Vrátí logickou hodnotu podle vyhodnocený výraz CBool – funkce

**Syntaxe:**  
`bool CBool(exp Expression)`

**Poznámky:**  
Pokud se výraz vyhodnotí nenulovou hodnotu, pak CBool – vrátí hodnotu True, jinak vrátí hodnotu False.

**Příklad:**  
`CBool([attrib1] = [attrib2])`  

Vrátí hodnotu True, pokud oba atributy mají stejnou hodnotu.

- - -
### <a name="cdate"></a>CDate –
**Popis:**  
CDate – funkce vrátí z řetězce na datum a čas UTC. Data a času není typu nativní atribut synchronizované, ale je používána některé funkce.

**Syntaxe:**  
`dt CDate(str value)`

* Hodnota: Řetězec datum, čas a volitelně časové pásmo

**Poznámky:**  
Vrácený řetězec je vždy ve standardu UTC.

**Příklad:**  
`CDate([employeeStartTime])`  
Vrátí hodnotu DateTime na základě na zaměstnance počáteční čas

`CDate("2013-01-10 4:00 PM -8")`  
Vrátí data a času představující "2013-01-11 12:00:00"


- - -
### <a name="certextensionoids"></a>CertExtensionOids
**Popis:**  
Vrátí hodnoty Oid všech důležitých rozšíření certifikátů objektu.

**Syntaxe:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certformat"></a>CertFormat
**Popis:**  
Vrátí název formátu tento certifikát x.509 v3.

**Syntaxe:**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certfriendlyname"></a>CertFriendlyName
**Popis:**  
Vrátí související aliasu pro certifikát.

**Syntaxe:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certhashstring"></a>CertHashString
**Popis:**  
Vrátí hodnotu hash SHA1 certifikátu x.509 v3 jako řetězec hexadecimálních znaků.

**Syntaxe:**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certissuer"></a>CertIssuer
**Popis:**  
Vrací název certifikační autority, která vystavila certifikát x.509 v3.

**Syntaxe:**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certissuerdn"></a>CertIssuerDN
**Popis:**  
Vrátí rozlišující název vystavitele certifikátu.

**Syntaxe:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certissueroid"></a>CertIssuerOid
**Popis:**  
Vrátí identifikátor vystavitele certifikátu.

**Syntaxe:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Popis:**  
Vrátí informace o algoritmus klíče pro tento certifikát x.509 v3 jako řetězec.

**Syntaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Popis:**  
Vrátí parametry algoritmus klíče pro certifikát x.509 v3 jako řetězec hexadecimálních znaků.

**Syntaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certnameinfo"></a>CertNameInfo
**Popis:**  
Vrátí subjektu a vystavitele názvy z certifikátu.

**Syntaxe:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.
*   X509NameType: X509NameType hodnoty pro předmět.
*   includesIssuerName: hodnota true, mají-li zahrnout název vystavitele; jinak hodnota false.

- - -
### <a name="certnotafter"></a>CertNotAfter
**Popis:**  
Vrátí datum v místní čase, po kterém certifikát je již neplatný.

**Syntaxe:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certnotbefore"></a>CertNotBefore
**Popis:**  
Vrátí datum v místní čase, při kterém certifikát začne platit.

**Syntaxe:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Popis:**  
Vrátí identifikátor veřejné klíče pro certifikát x.509 v3.

**Syntaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Popis:**  
Vrátí identifikátor veřejné parametrů klíče pro certifikát x.509 v3.

**Syntaxe:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certserialnumber"></a>CertSerialNumber
**Popis:**  
Vrátí sériové číslo certifikátu x.509 v3.

**Syntaxe:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Popis:**  
Vrátí identifikátor algoritmus použitý k vytvoření podpisu certifikátu.

**Syntaxe:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certsubject"></a>CertSubject
**Popis:**  
Získá název předmětu rozlišující z certifikátu.

**Syntaxe:**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Popis:**  
Vrátí název předmětu rozlišující z certifikátu.

**Syntaxe:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Popis:**  
Vrátí identifikátor název subjektu z certifikátu.

**Syntaxe:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certthumbprint"></a>CertThumbprint
**Popis:**  
Vrátí kryptografický otisk certifikátu.

**Syntaxe:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="certversion"></a>CertVersion
**Popis:**  
Vrátí verze formátu X.509 certifikátu.

**Syntaxe:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.

- - -
### <a name="cguid"></a>CGuid
**Popis:**  
Funkce CGuid převede řetězcovou reprezentaci identifikátor GUID jeho binární reprezentace.

**Syntaxe:**  
`bin CGuid(str GUID)`

* Řetězec formátu v tomto vzoru: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx nebo {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

- - -
### <a name="contains"></a>Contains
**Popis:**  
Obsahuje funkce najde řetězec uvnitř vícehodnotového atributu

**Syntaxe:**  
`num Contains (mvstring attribute, str search)` -malá a velká písmena  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)` -malá a velká písmena

* Atribut: více hodnot atributů pro vyhledávání.
* hledání: řetězec, který má v atributu najít.
* Casetype: CaseInsensitive nebo CaseSensitive.

Vrátí index atribut více hodnot, kde byl nalezen řetězec. 0 je vrácena, pokud není nalezen řetězec.

**Poznámky:**  
Pro více hodnot řetězec atributy hledáním dílčích řetězců v hodnoty.  
Pro atributy typu odkaz vyhledávaná řetězec musí přesně shodovat hodnota, která má být považovány za shodné.

**Příklad:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Pokud má atribut proxyAddresses primární e-mailovou adresu (indikován velká písmena "SMTP:"), pak vrátit atribut proxyAddress, jinak vrátí chybu.

- - -
### <a name="convertfrombase64"></a>ConvertFromBase64
**Popis:**  
Funkce ConvertFromBase64 převede hodnotu zadaného kódováním base64 regulární řetězec.

**Syntaxe:**  
`str ConvertFromBase64(str source)` -předpokládá pro kódování Unicode  
`str ConvertFromBase64(str source, enum Encoding)`

* Zdroj: řetězec kódování Base64  
* Kódování: Znakové sady Unicode, ASCII, UTF8

**Příklad**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Vrátí oba příklady "*Hello, world!*"

- - -
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Popis:**  
Funkce ConvertFromUTF8Hex převede zadanou hodnotu UTF8 šestnáctkově kódovaný řetězec.

**Syntaxe:**  
`str ConvertFromUTF8Hex(str source)`

* Zdroj: UTF8 2bajtová kódovaného palčivost

**Poznámky:**  
Rozdíl mezi tato funkce a ConvertFromBase64([],UTF8) v tom, že výsledkem je popisný rozlišující název atributu.  
Tento formát se službou Azure Active Directory používá jako rozlišující název.

**Příklad:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Vrátí "*Hello, world!*"

- - -
### <a name="converttobase64"></a>ConvertToBase64
**Popis:**  
Funkce ConvertToBase64 převede řetězec na řetězec ve formátu base64.  
Převede hodnotu pole celých čísel na jeho ekvivalentní řetězcovou reprezentaci, který je zakódován pomocí kódování base-64 číslic.

**Syntaxe:**  
`str ConvertToBase64(str source)`

**Příklad:**  
`ConvertToBase64("Hello world!")`  
Vrátí "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

- - -
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Popis:**  
Funkce ConvertToUTF8Hex převede řetězec na hodnotu UTF8 šestnáctkově kódování.

**Syntaxe:**  
`str ConvertToUTF8Hex(str source)`

**Poznámky:**  
Výstupní formát této funkce se službou Azure Active Directory používá jako atribut formátu DN.

**Příklad:**  
`ConvertToUTF8Hex("Hello world!")`  
Vrátí 48656C6C6F20776F726C6421

- - -
### <a name="count"></a>Počet
**Popis:**  
Funkce Count vrátí počet elementů ve více hodnotami atributu

**Syntaxe:**  
`num Count(mvstr attribute)`

- - -
### <a name="cnum"></a>CNum
**Popis:**  
Funkce CNum přebírá řetězec a vrátí číselným datovým typem.

**Syntaxe:**  
`num CNum(str value)`

- - -
### <a name="cref"></a>CRef –
**Popis:**  
Převede řetězec na atribut typu odkaz

**Syntaxe:**  
`ref CRef(str value)`

**Příklad:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

- - -
### <a name="cstr"></a>CStr
**Popis:**  
Funkci CStr převede na datový typ řetězec.

**Syntaxe:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* hodnota: může být číselnou hodnotu, atribut odkaz nebo logická hodnota.

**Příklad:**  
`CStr([dn])`  
Může vrátit "cn = Jan, dc = contoso, dc = com"

- - -
### <a name="dateadd"></a>Funkce DateAdd
**Popis:**  
Vrátí datum obsahující datum, do které byl přidán zadaného časového intervalu.

**Syntaxe:**  
`dt DateAdd(str interval, num value, dt date)`

* interval: řetězec výraz, který je interval času, který chcete přidat. Řetězec musí mít jednu z následujících hodnot:
  * rrrr roku
  * q čtvrtletí.
  * m měsíc
  * y den roku
  * d den
  * w den v týdnu
  * TT týden
  * h hodinu
  * n minutu
  * s druhou
* hodnota: počet jednotek, které chcete přidat. Může být (Chcete-li získat data v budoucnosti) kladné a záporné (Chcete-li získat data v minulosti).
* datum: data a času představující datum, ke kterému se přidá interval.

**Příklad:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Přidá 3 měsíce a vrátí hodnotu DateTime představující "2001-04-01".

- - -
### <a name="datefromnum"></a>DateFromNum
**Popis:**  
Funkce DateFromNum převádí na hodnotu v AD na datum formátu na typ DateTime.

**Syntaxe:**  
`dt DateFromNum(num value)`

**Příklad:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Vrací hodnotu DateTime představující 2012-01-01 23:00:00

- - -
### <a name="dncomponent"></a>DNComponent
**Popis:**  
Funkce DNComponent vrací hodnotu zadaného rozlišující název součásti budete zleva.

**Syntaxe:**  
`str DNComponent(ref dn, num ComponentNumber)`

* rozlišující název: odkaz na atribut interpretovat
* ComponentNumber: Součástí DN vrátit

**Příklad:**  
`DNComponent(CRef([dn]),1)`  
Pokud je rozlišující název "cn = Jan, ou =...," vrátí Jan

- - -
### <a name="dncomponentrev"></a>DNComponentRev
**Popis:**  
Funkce DNComponentRev vrací hodnotu zadaného rozlišující název složky přecházející z pravé (end).

**Syntaxe:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* rozlišující název: odkaz na atribut interpretovat
* ComponentNumber - součástí DN vrátit
* Možnosti: Řadič domény – ignorovat všechny součásti s "dc ="

**Příklad:**  
Pokud rozlišující název "cn Jan, ou = Atlantu, ou = GA, ou = = USA, řadič domény = contoso, dc = com" pak  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Obě vrací nás.

- - -
### <a name="error"></a>Chyba
**Popis:**  
Chyba funkce se používá k vrácení o vlastní chybě.

**Syntaxe:**  
`void Error(str ErrorMessage)`

**Příklad:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Pokud atribut accountName není k dispozici, vyvolá chybu v objektu.

- - -
### <a name="escapedncomponent"></a>EscapeDNComponent
**Popis:**  
Funkce EscapeDNComponent použije jednu součást názvu domény a řídicí sekvence, může být reprezentován v protokolu LDAP.

**Syntaxe:**  
`str EscapeDNComponent(str value)`

**Příklad:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Zajistí, že objekt lze vytvořit v adresáři LDAP, i v případě, že atribut displayName obsahuje znaky, které je nutné uvést v protokolu LDAP.

- - -
### <a name="formatdatetime"></a>FormatDateTime
**Popis:**  
Funkce FormatDateTime slouží k formátování hodnotu DateTime na řetězec s zadaný formát

**Syntaxe:**  
`str FormatDateTime(dt value, str format)`

* hodnota: hodnotu ve formátu data a času
* formát: řetězec představující převést na formát.

**Poznámky:**  
Možné hodnoty pro formát naleznete zde: [uživatelem definované formátu data a času (formát funkce)](http://msdn2.microsoft.com/library/73ctwf33\(VS.90\).aspx)

**Příklad:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Výsledkem "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Může mít za následek "20140905081453.0Z"

- - -
### <a name="guid"></a>Guid
**Popis:**  
Funkce Guid se generuje nový náhodný identifikátor GUID

**Syntaxe:**  
`str Guid()`

- - -
### <a name="iif"></a>IIF
**Popis:**  
Funkce IIF vrátí jednu z možných hodnot na základě podmínky pro zadanou sadu.

**Syntaxe:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* Podmínka: všechny hodnoty nebo výrazy, které lze vyhodnotit na hodnotu true nebo false.
* valueIfTrue: Pokud je podmínka vyhodnocena jako true, vrácené hodnoty.
* valueIfFalse: Pokud je podmínka vyhodnocena jako false, vrácené hodnoty.

**Příklad:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Pokud je uživatel učně, vrátí vrátí alias uživatele s "t-" přidat na začátek ho jiný alias uživatele, jako je.

- - -
### <a name="instr"></a>InStr
**Popis:**  
Funkce InStr první výskyt je dílčí řetězec najde v řetězci.

**Syntaxe:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* prohledávaný_řetězec: řetězec, který má být prohledán
* hledaný_řetězec: řetězec, který má být nalezena
* spustit: počáteční pozice k vyhledání dílčí řetězec
* porovnání: vbTextCompare nebo vbBinaryCompare

**Poznámky:**  
Vrátí pozice, kdy nebyl nalezen dílčí řetězec, nebo 0, pokud není nalezena.

**Příklad:**  
`InStr("The quick brown fox","quick")`  
Hodnoty 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Vyhodnotí 7

- - -
### <a name="instrrev"></a>InStrRev
**Popis:**  
Funkce InStrRev posledního výskytu dílčí řetězec najde v řetězci

**Syntaxe:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* prohledávaný_řetězec: řetězec, který má být prohledán
* hledaný_řetězec: řetězec, který má být nalezena
* spustit: počáteční pozice k vyhledání dílčí řetězec
* porovnání: vbTextCompare nebo vbBinaryCompare

**Poznámky:**  
Vrátí pozice, kdy nebyl nalezen dílčí řetězec, nebo 0, pokud není nalezena.

**Příklad:**  
`InStrRev("abbcdbbbef","bb")`  
Vrátí hodnotu 7

- - -
### <a name="isbitset"></a>IsBitSet
**Popis:**  
Funkce IsBitSet testů, pokud chvíli je nastavena, nebo ne

**Syntaxe:**  
`bool IsBitSet(num value, num flag)`

* hodnota: číselnou hodnotu, která je evaluated.flag: číselnou hodnotu, která má bit, který se má vyhodnotit

**Příklad:**  
`IsBitSet(&HF,4)`  
Vrátí hodnotu True, protože je nastaven bit "4" v šestnáctkové hodnoty "F"

- - -
### <a name="isdate"></a>IsDate
**Popis:**  
Pokud výraz může být vyhodnotí jako typ DateTime, pak Funkce IsDate vyhodnocen jako True.

**Syntaxe:**  
`bool IsDate(var Expression)`

**Poznámky:**  
Použít k určení, pokud CDate() může být úspěšné.

- - -
### <a name="iscert"></a>IsCert
**Popis:**  
Vrátí hodnotu true Pokud serializovat lze nezpracovaná data do objektu certifikát .NET X509Certificate2.

**Syntaxe:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: reprezentace bajtové pole certifikátu X.509. Bajtové pole může být kódovaný binárního souboru (DER) nebo data s kódováním Base64 X.509.
- - -
### <a name="isempty"></a>IsEmpty –
**Popis:**  
Pokud atribut je k dispozici v CS nebo více hodnot, ale vyhodnocen jako prázdný řetězec, IsEmpty – funkce vyhodnotí na hodnotu True.

**Syntaxe:**  
`bool IsEmpty(var Expression)`

- - -
### <a name="isguid"></a>IsGuid
**Popis:**  
Pokud řetězec může převést na identifikátor GUID, funkce IsGuid vyhodnocena jako true.

**Syntaxe:**  
`bool IsGuid(str GUID)`

**Poznámky:**  
Identifikátor GUID je definován jako jeden z těchto vzorů následující řetězec: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx nebo {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Použít k určení, pokud CGuid() může být úspěšné.

**Příklad:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Pokud StrAttribute má formát identifikátoru GUID, vrátit binární reprezentace, jinak vrátí hodnotu Null.

- - -
### <a name="isnull"></a>IsNull
**Popis:**  
Pokud je výsledkem na hodnotu Null, vrátí hodnotu true Funkce IsNull.

**Syntaxe:**  
`bool IsNull(var Expression)`

**Poznámky:**  
Pro atribut s hodnotou Null se vyjádří chybí atribut.

**Příklad:**  
`IsNull([displayName])`  
Vrátí hodnotu True, pokud atribut neexistuje v CS nebo více hodnot.

- - -
### <a name="isnullorempty"></a>IsNullOrEmpty
**Popis:**  
Pokud výraz má hodnotu null nebo prázdný řetězec, IsNullOrEmpty funkce vrátí hodnotu true.

**Syntaxe:**  
`bool IsNullOrEmpty(var Expression)`

**Poznámky:**  
Pro atribut to by vyhodnotit na hodnotu True pokud atribut chybí nebo je k dispozici, ale prázdný řetězec.  
Inverzní této funkce je s názvem IsPresent.

**Příklad:**  
`IsNullOrEmpty([displayName])`  
Vrátí hodnotu True, pokud atribut neexistuje, nebo je prázdný řetězec v CS nebo více hodnot.

- - -
### <a name="isnumeric"></a>IsNumeric
**Popis:**  
Funkce IsNumeric vrací logickou hodnotu udávající, zda výraz může být vyhodnocen jako typ number.

**Syntaxe:**  
`bool IsNumeric(var Expression)`

**Poznámky:**  
Použít k určení, pokud CNum() může být úspěšné analyzovat výraz.

- - -
### <a name="isstring"></a>IsString
**Popis:**  
Pokud výraz může být vyhodnocen jako typ string, funkce IsString vyhodnocuje na hodnotu True.

**Syntaxe:**  
`bool IsString(var expression)`

**Poznámky:**  
Použít k určení, pokud CStr() může být úspěšné analyzovat výraz.

- - -
### <a name="ispresent"></a>IsPresent
**Popis:**  
Pokud je výsledkem na řetězec, který není Null a není prázdný, vrátí funkce IsPresent hodnotu true.

**Syntaxe:**  
`bool IsPresent(var expression)`

**Poznámky:**  
Inverzní této funkce je s názvem IsNullOrEmpty.

**Příklad:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

- - -
### <a name="item"></a>Položka
**Popis:**  
Funkce Item vrátí jednu položku z více hodnot řetězec nebo atributu.

**Syntaxe:**  
`var Item(mvstr attribute, num index)`

* Atribut: vícehodnotového atributu
* index: index položky v řetězci s více hodnotami.

**Poznámky:**  
Funkce Item je užitečné společně s obsahuje funkce, protože pozdější funkce vrátí index položky v více hodnotami atributu.

Vrátí chybu, pokud index je mimo rozsah.

**Příklad:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Vrátí primární e-mailovou adresu.

- - -
### <a name="itemornull"></a>ItemOrNull
**Popis:**  
Funkce ItemOrNull vrací jednu položku z více hodnot řetězec nebo atributu.

**Syntaxe:**  
`var ItemOrNull(mvstr attribute, num index)`

* Atribut: vícehodnotového atributu
* index: index položky v řetězci s více hodnotami.

**Poznámky:**  
Funkce ItemOrNull je užitečné společně s obsahuje funkce, protože pozdější funkce vrátí index položky v více hodnotami atributu.

Pokud index je mimo rozsah, vrátí hodnotu Null.

- - -
### <a name="join"></a>Spojit
**Popis:**  
Funkce spojení přebírá řetězec s více hodnotami a vrátí řetězec s hodnotou jednoho zadaného oddělovače vložen mezi každou položku.

**Syntaxe:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* Atribut: více hodnot atributů obsahující řetězce, který se má spojit.
* oddělovač: libovolný řetězec, který slouží k oddělení dílčích řetězců v vrácený řetězec. Pokud tento parametr vynechán, znak mezery ("") se používá. Pokud oddělovač obsahuje řetězec nulové délky ("") nebo Nothing, všechny položky v seznamu jsou zřetězeny žádný oddělovač.

**Poznámky**  
Funkce připojení a rozdělení jsou rovnocenné. Funkce spojení trvá pole řetězců a připojí pomocí řetězec oddělovač vrátit jeden řetězec. Funkce rozdělení přebírá řetězec a která ho odděluje v oddělovač vrátit pole řetězců. Klíčovým rozdílem je ale, že připojení lze zřetězení řetězců s libovolným řetězcem oddělovač, rozdělení můžete oddělit pouze řetězců pomocí jednoho znaku oddělovač.

**Příklad:**  
`Join([proxyAddresses],",")`  
Může vrátit: "SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

- - -
### <a name="lcase"></a>LCase
**Popis:**  
Funkce LCase převede všechny znaky v řetězci na malá písmena.

**Syntaxe:**  
`str LCase(str value)`

**Příklad:**  
`LCase("TeSt")`  
Vrátí hodnotu "test".

- - -
### <a name="left"></a>Vlevo
**Popis:**  
Levé funkce vrátí zadaný počet znaků z řetězce levé straně.

**Syntaxe:**  
`str Left(str string, num NumChars)`

* řetězec: řetězec, který vrátí znaky z
* NumChars: číslo určující počet znaků, mají být vráceny od začátku (vlevo) řetězce

**Poznámky:**  
Řetězec obsahující první numChars znaky v řetězci:

* Pokud numChars = 0, vrátí prázdný řetězec.
* Pokud numChars < 0, vrátí vstupní řetězec.
* Pokud má řetězec hodnotu null, vrátí prázdný řetězec.

Pokud řetězec obsahuje méně znaků, než číslo zadané v numChars, je vrácen řetězec identické řetězce, (který je, obsahující všechny znaky v parametru 1).

**Příklad:**  
`Left("John Doe", 3)`  
Vrátí "Joh".

- - -
### <a name="len"></a>Len
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
Funkce LTrim odebere úvodní prázdné znaky v řetězci.

**Syntaxe:**  
`str LTrim(str value)`

**Příklad:**  
`LTrim(" Test ")`  
Vrátí "Test"

- - -
### <a name="mid"></a>Mid –
**Popis:**  
Funkci část vrátí zadaný počet znaků z určené pozice v řetězci.

**Syntaxe:**  
`str Mid(str string, num start, num NumChars)`

* řetězec: řetězec, který vrátí znaky z
* spustit: číslo určující počáteční pozice v řetězci vrátit znaků z
* NumChars: číslo určující počet znaků k návratu z pozice v řetězci

**Poznámky:**  
Vrátí numChars znaků od počáteční pozice v řetězci.  
Řetězec obsahující numChars znaků od počáteční pozice v řetězci:

* Pokud numChars = 0, vrátí prázdný řetězec.
* Pokud numChars < 0, vrátí vstupní řetězec.
* Pokud spuštění > délky řetězce, vrátí vstupní řetězec.
* Pokud spuštění < = 0, vrátí vstupní řetězec.
* Pokud má řetězec hodnotu null, vrátí prázdný řetězec.

Pokud nejsou k dispozici numChar znaků zbývající v řetězci od počáteční pozice, jako jsou vráceny nejdříve mnoho znaků.

**Příklad:**  
`Mid("John Doe", 3, 5)`  
Vrátí "hn proveďte".

`Mid("John Doe", 6, 999)`  
Vrátí "Doe"

- - -
### <a name="now"></a>Ihned
**Popis:**  
Funkce nyní vrací hodnotu DateTime, zadáte aktuální datum a čas podle systémového data a času v počítači.

**Syntaxe:**  
`dt Now()`

- - -
### <a name="numfromdate"></a>NumFromDate
**Popis:**  
Funkce NumFromDate vrátí datum ve formátu data na AD.

**Syntaxe:**  
`num NumFromDate(dt value)`

**Příklad:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Vrátí 129699324000000000

- - -
### <a name="padleft"></a>padLeft
**Popis:**  
PadLeft funkce doleva-dotyková zařízení řetězec na určenou délku pomocí zadané odsazení znaku.

**Syntaxe:**  
`str PadLeft(str string, num length, str padCharacter)`

* řetězec: řetězec k vyplnění.
* Délka: celé číslo představující požadovanou délku řetězce.
* padCharacter: řetězec, který se skládá z jednoho znaku používat jako znak odsazení

**Poznámky:**

* Pokud délka řetězce je menší než délka, pak padCharacter opakovaně připojí se k začátku (vlevo) řetězce, dokud ho má délku rovna délce.
* padCharacter může být znak mezery, ale jeho nesmí mít hodnotu null.
* Pokud délka řetězce je rovna nebo větší než délka, je vrácen řetězec s beze změny.
* Pokud řetězec má délku větší než nebo rovna hodnotě Délka, bude vrácena řetězec identické na řetězec.
* Pokud délka řetězce je menší než délka, bude vrácena nový řetězec má požadovanou délku obsahující řetězec doplněno padCharacter.
* Pokud má řetězec hodnotu null, funkce vrátí prázdný řetězec.

**Příklad:**  
`PadLeft("User", 10, "0")`  
Vrátí "000000User".

- - -
### <a name="padright"></a>PadRight –
**Popis:**  
PadRight – funkce vpravo-dotyková zařízení řetězec na určenou délku pomocí zadané odsazení znaku.

**Syntaxe:**  
`str PadRight(str string, num length, str padCharacter)`

* řetězec: řetězec k vyplnění.
* Délka: celé číslo představující požadovanou délku řetězce.
* padCharacter: řetězec, který se skládá z jednoho znaku používat jako znak odsazení

**Poznámky:**

* Pokud délka řetězce je menší než délka, pak padCharacter opakovaně připojí se k konci (vpravo) řetězec dokud má délku rovna délce.
* padCharacter může být znak mezery, ale jeho nesmí mít hodnotu null.
* Pokud délka řetězce je rovna nebo větší než délka, je vrácen řetězec s beze změny.
* Pokud řetězec má délku větší než nebo rovna hodnotě Délka, bude vrácena řetězec identické na řetězec.
* Pokud délka řetězce je menší než délka, bude vrácena nový řetězec má požadovanou délku obsahující řetězec doplněno padCharacter.
* Pokud má řetězec hodnotu null, funkce vrátí prázdný řetězec.

**Příklad:**  
`PadRight("User", 10, "0")`  
Vrátí "User000000".

- - -
### <a name="pcase"></a>PCase
**Popis:**  
Funkce PCase převede první znak každého slova mezerami řetězce na velká písmena a všechny ostatní znaky jsou převedeny na malá písmena.

**Syntaxe:**  
`String PCase(string)`

**Poznámky:**

* Tato funkce v současné době neobsahuje správné velká a malá písmena převést slovo, které je zcela velká, jako je například zkratka.

**Příklad:**  
`PCase("TEsT")`  
Vrátí hodnotu "Test".

`PCase(LCase("TEST"))`  
Vrátí "Test"

- - -
### <a name="randomnum"></a>RandomNum
**Popis:**  
Funkce RandomNum Vrátí náhodné číslo mezi zadaného intervalu.

**Syntaxe:**  
`num RandomNum(num start, num end)`

* spustit: číslo určující dolní limit náhodná hodnota ke generování
* end: číslo určující horní limit počtu náhodná hodnota ke generování

**Příklad:**  
`Random(100,999)`  
734 může vrátit.

- - -
### <a name="removeduplicates"></a>Removeduplicates –
**Popis:**  
Removeduplicates – funkce přebírá řetězec s více hodnotami a ujistěte se, že každá hodnota je jedinečný.

**Syntaxe:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Příklad:**  
`RemoveDuplicates([proxyAddresses])`  
Vrátí atribut upravený proxyAddress, kde byly odebrány všechny duplicitní hodnoty.

- - -
### <a name="replace"></a>Nahradit
**Popis:**  
Funkce Nahradit nahradí všechny výskyty řetězce jiným řetězcem.

**Syntaxe:**  
`str Replace(str string, str OldValue, str NewValue)`

* řetězec: řetězec tak, aby nahraďte hodnoty v.
* OldValue: Řetězec, Hledat a nahradit.
* NewValue: Řetězec, který chcete nahradit.

**Poznámky:**  
Funkce rozpozná následující zvláštní monikery:

* \n – nový řádek
* \r – znaky CR
* \t – karta

**Příklad:**  
`Replace([address],"\r\n",", ")`  
Nahradí Line FEED čárkami a místa a může vést k "Jeden Microsoft způsob, Redmond, WA, USA"

- - -
### <a name="replacechars"></a>ReplaceChars
**Popis:**  
Funkce ReplaceChars nahradí všechny výskyty znaků v řetězci ReplacePattern nalezena.

**Syntaxe:**  
`str ReplaceChars(str string, str ReplacePattern)`

* řetězec: řetězec pro náhradu znaků v.
* ReplacePattern: řetězec obsahující slovník s znaků, který má nahradit.

Formát je {zdroj1}: {target1}, {zdroj2}: {target2}, {zdrojN}, {targetN} kde zdroj je znak, který má najít a cíle řetězec, který má nahradit.

**Poznámky:**

* Funkce přebírá všechny výskyty definované zdroje a nahradí je cíle.
* Zdroj musí mít přesně jeden znak (unicode).
* Zdroj nemůže být prázdný nebo delší než jeden znak (Chyba analýzy).
* Cíl může mít více znaků, například ö:oe, β:ss.
* Cílem může být prázdný, která určuje, že znak, který má být odebrána.
* Zdroj je malá a velká písmena a musí být přesně shodovat.
* (Čárkou) a: (dvojtečka) jsou vyhrazené znaky a nelze jej nahradit pomocí této funkce.
* Mezery a další bílé znaky v řetězci ReplacePattern se ignorují.

**Příklad:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Vrátí Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Vrátí "ONeil", jeden značek je definována odeberou.

- - -
### <a name="right"></a>Vpravo
**Popis:**  
Správné funkce vrátí zadaný počet znaků zprava (koncového) řetězce.

**Syntaxe:**  
`str Right(str string, num NumChars)`

* řetězec: řetězec, který vrátí znaky z
* NumChars: číslo určující počet znaků k návratu z konci (vpravo) řetězce

**Poznámky:**  
Od poslední pozice řetězce jsou vráceny NumChars znaky.

Řetězec obsahující poslední numChars znaky v řetězci:

* Pokud numChars = 0, vrátí prázdný řetězec.
* Pokud numChars < 0, vrátí vstupní řetězec.
* Pokud má řetězec hodnotu null, vrátí prázdný řetězec.

Pokud řetězec obsahuje méně znaků, než číslo zadané v NumChars, je vrácen řetězec identické na řetězec.

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
Vrátí hodnotu "Test".

- - -
### <a name="select"></a>Vyberte
**Popis:**  
Proces všech hodnot v více hodnot atributů (nebo výstupní výrazu) založený na zadanou funkci.

**Syntaxe:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* Položka: reprezentuje element v více hodnotami atributu
* Atribut: vícehodnotového atributu
* výraz: výraz, který vrátí kolekce hodnot
* podmínky: žádné funkce, který dokáže zpracovat položku v atributu

**Příklady:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Vrátí všechny hodnoty ve více hodnot atributů otherPhone po odebrání pomlčky (-).

- - -
### <a name="split"></a>Rozdělení
**Popis:**  
Funkce rozdělení přebírá řetězec oddělené s oddělovačem a udělá z něj řetězec s více hodnotami.

**Syntaxe:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* hodnota: řetězec s oddělovací znak pro oddělení.
* oddělovač: jeden znak, který má být použit jako oddělovač.
* limit: maximální počet hodnot, které můžou vrátit.

**Příklad:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Vrátí řetězec s více hodnotami s 2 elementy užitečné pro atribut proxyAddress.

- - -
### <a name="stringfromguid"></a>StringFromGuid
**Popis:**  
Funkce StringFromGuid trvá binární GUID a převede jej na řetězec

**Syntaxe:**  
`str StringFromGuid(bin GUID)`

- - -
### <a name="stringfromsid"></a>StringFromSid
**Popis:**  
Funkce StringFromSid převede pole bajtů obsahující identifikátor zabezpečení na řetězec.

**Syntaxe:**  
`str StringFromSid(bin ObjectSID)`  

- - -
### <a name="switch"></a>Přepínač
**Popis:**  
Přepínač funkce slouží k vrátit jednu hodnotu na základě vyhodnocených podmínek.

**Syntaxe:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* Expr: výraz typu Variant, kterou chcete vyhodnotit.
* hodnota: hodnota, která má být vrácen, pokud odpovídající výraz hodnotu True.

**Poznámky:**  
Seznam argumentů funkce přepínače se skládá z dvojic výrazy a hodnoty. Výrazy jsou vyhodnocovány zleva doprava a je vrácena hodnota přidružená k výrazu první vyhodnotit na hodnotu True. Pokud části nejsou spárovány správně, dojde k chybě spuštění.

Například pokud Výraz1 hodnotu True, vrátí přepínač value1. Pokud výraz-1 je False, ale výraz 2 má hodnotu True, vrátí přepínač hodnotu 2 a tak dále.

Přepínač vrátí žádnou pokud:

* Žádný z výrazů mají hodnotu True.
* První výraz, který hodnota True, má odpovídající hodnotu, která má hodnotu Null.

Přepínač vyhodnotí všechny výrazy, i když vrací pouze jeden z nich. Z tohoto důvodu je třeba dávat pozor na nežádoucí vedlejší účinky. Například pokud vyhodnocování všech výrazu je výsledkem dělení nulou, dojde k chybě.

Hodnota může být také chybovou funkci, která by vrátit vlastní řetězec.

**Příklad:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Vrátí jazyk používaný v některé hlavní města, jinak vrátí chybu.

- - -
### <a name="trim"></a>Oříznout
**Popis:**  
Funkce Trim odebere úvodní a koncové mezery z řetězce.

**Syntaxe:**  
`str Trim(str value)`  

**Příklad:**  
`Trim(" Test ")`  
Vrátí hodnotu "Test".

`Trim([proxyAddresses])`  
Odebere úvodní a koncové mezery pro každou hodnotu v atributu proxyAddress.

- - -
### <a name="ucase"></a>UCase
**Popis:**  
Funkce UCase převede všechny znaky v řetězci na velká písmena.

**Syntaxe:**  
`str UCase(str string)`

**Příklad:**  
`UCase("TeSt")`  
Vrátí hodnotu "TEST".

- - -
### <a name="where"></a>Kde

**Popis:**  
Vrátí podmnožinu hodnoty z více hodnot atributů (nebo výstupní výrazu) na základě konkrétní podmínky.

**Syntaxe:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* Položka: reprezentuje element v více hodnotami atributu
* Atribut: vícehodnotového atributu
* podmínky: žádné výraz, který lze vyhodnotit na hodnotu true nebo false
* výraz: výraz, který vrátí kolekce hodnot

**Příklad:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Návratové hodnoty certifikátu v userCertificate více hodnot atributů, které nejsou vypršela platnost.

- - -
### <a name="with"></a>S
**Popis:**  
Funkce s poskytuje způsob, jak pomocí proměnné představují dílčím výrazu, která se zobrazí jeden nebo více časy komplexní výrazu zjednodušit složitý výraz.

**Syntaxe:**
`With(var variable, exp subExpression, exp complexExpression)`  
* Proměnná: představuje dílčím výrazu.
* dílčím výrazu: reprezentována proměnná dílčím výrazu.
* complexExpression: složitý výraz.

**Příklad:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
Je funkčně srovnatelný:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Která vrací pouze hodnoty neprošlé certifikátu v userCertificate atributu.


- - -
### <a name="word"></a>Word
**Popis:**  
Funkce aplikace Word vrací slovo obsažené v řetězci, na základě parametrů popisující oddělovače použití a word číslo, které má vrátit.

**Syntaxe:**  
`str Word(str string, num WordNumber, str delimiters)`

* řetězec: řetězec, který má vrátit slova.
* WordNumber: by měl vrátit číslo identifikující číslo, které aplikace word.
* oddělovače: řetězec představující delimiter(s), který se má použít k identifikaci slova

**Poznámky:**  
Jednotlivé řetězce znaků v řetězci oddělených jeden znaků v oddělovače jsou označeny jako slova:

* Pokud počet < 1, vrátí prázdný řetězec.
* Pokud má řetězec hodnotu null, vrátí prázdný řetězec.

Pokud řetězec obsahuje méně než číslo slova nebo řetězec neobsahuje žádné slova se identifikovanou pomocí oddělovače, je vrácen prázdný řetězec.

**Příklad:**  
`Word("The quick brown fox",3," ")`  
Vrátí "hnědá"

`Word("This,string!has&many separators",3,",!&#")`  
Vrátí "má"

## <a name="additional-resources"></a>Další prostředky
* [Principy výrazů deklarativního zřizování](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Azure AD Connect Sync: Možnosti přizpůsobení synchronizace](active-directory-aadconnectsync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)
