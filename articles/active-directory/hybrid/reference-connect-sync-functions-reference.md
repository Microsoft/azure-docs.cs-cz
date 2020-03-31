---
title: 'Synchronizace služby Azure AD Connect: Odkaz na funkce | Dokumenty společnosti Microsoft'
description: Odkaz na deklarativní zřizování výrazy v synchronizaci Azure AD Connect.
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
ms.openlocfilehash: 5c3102480e316c634930c356ae02f769767b7d08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900041"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Synchronizace azure apřipojení: odkaz na funkce
Ve službě Azure AD Connect se funkce používají k manipulaci s hodnotou atributu během synchronizace.  
Syntaxe funkcí je vyjádřena v následujícím formátu:  
`<output type> FunctionName(<input type> <position name>, ..)`

Pokud je funkce přetížena a přijímá více syntaxí, jsou uvedeny všechny platné syntaxe.  
Funkce jsou silně zadali a ověří, že typ předaný v odpovídá zdokumentovanému typu.  
Pokud se typ neshoduje, je vyvolána chyba.

Typy jsou vyjádřeny následující syntaxí:

* **bin** – binární
* **bool** – boolean
* **dt** – Datum a čas uTC
* **výčet** – výčet známých konstant
* **exp** – Výraz, který by měl být vyhodnocen jako logická
* **mvbin** – Binární s více hodnotami
* **mvstr** – řetězec s více hodnotami
* **mvref** – odkaz s více hodnotami
* **num** – číselné
* **ref** – Reference
* **str** – Řetězec
* **var** – Varianta (téměř) jakéhokoli jiného typu
* **void** – nevrátí hodnotu

Funkce s typy **mvbin**, **mvstr**a **mvref** mohou pracovat pouze s vícehodnotami atributy. Funkce s **přihrádkou**, **str**a **ref** pracují na atributech s jednou i s více hodnotami.

## <a name="functions-reference"></a>Reference k funkcím

| Seznam funkcí |  |  |  |  |
| --- | --- | --- | --- | --- |
| **Certifikát** | | | | |
| [CertExtensionOidy](#certextensionoids) |[Formát certifikátu](#certformat) |[Název certifikátu](#certfriendlyname) |[CertHashString](#certhashstring) | |
| [CertIssuer](#certissuer) |[CertIssuerDN](#certissuerdn) |[CertIssuerOid](#certissueroid) |[Algoritmus CertKey](#certkeyalgorithm) | |
| [CertKeyAlgorithmParams](#certkeyalgorithmparams) |[Informace o názvu certifikátu](#certnameinfo) |[CertNotAfter](#certnotafter) |[CertNotPřed](#certnotbefore) | |
| [CertPublicKeyOid](#certpublickeyoid) |[CertPublicKeyParametersOid](#certpublickeyparametersoid) |[Číslo CertSerial](#certserialnumber) |[CertifikátSignatureAlgorithmOid](#certsignaturealgorithmoid) | |
| [CertSubject](#certsubject) |[CertSubjectNameDN](#certsubjectnamedn) |[CertSubjectNameOid](#certsubjectnameoid) |[CertThumbprint](#certthumbprint) | |
[Verze certifikátu](#certversion) |[IsCert](#iscert) | | | |
| **Převod** | | | | |
| [CBool](#cbool) |[Datum c.](#cdate) |[CGuid (Fr.)](#cguid) |[PřevéstFromBase64](#convertfrombase64) | |
| [Převést na základnu64](#converttobase64) |[PřevéstFromUTF8Hex](#convertfromutf8hex) |[PřevéstToUTF8Hex](#converttoutf8hex) |[CNum](#cnum) | |
| [CRef](#cref) |[Cstr](#cstr) |[ŘetězecFromGuid](#stringfromguid) |[StringFromSid](#stringfromsid) | |
| **Datum a čas** | | | | |
| [Dateadd](#dateadd) |[DateFromNum](#datefromnum) |[Formatdatetime](#formatdatetime) |[Nwo](#now) | |
| [Datum numfrom](#numfromdate) | | | | |
| **Adresář** | | | | |
| [Součást DN](#dncomponent) |[DNComponentRev](#dncomponentrev) |[EscapeDNComponent](#escapedncomponent) | | |
| **Hodnocení** | | | | |
| [Sada IsbitSet](#isbitset) |[Isdate](#isdate) |[Isempty](#isempty) |[IsGuid (IsGuid)](#isguid) | |
| [Isnull](#isnull) |[IsnullorPrázdný](#isnullorempty) |[IsNumeric](#isnumeric) |[Ispresent](#ispresent) | |
| [IsString](#isstring) | | | | |
| **Matematické** | | | | |
| [BitAnd](#bitand) |[Bitor](#bitor) |[RandomNum](#randomnum) | | |
| **S více hodnotami** | | | | |
| [Obsahuje](#contains) |[Počet](#count) |[Položka](#item) |[ItemorNull](#itemornull) | |
| [Připojit](#join) |[Removeduplicates](#removeduplicates) |[Rozdělit](#split) | | |
| **Tok programu** | | | | |
| [Chyba](#error) |[Iif](#iif) |[Vybrat](#select) |[Přepnout](#switch) | |
| [Kde](#where) |[With](#with) | | | |
| **Text** | | | | |
| [Identifikátor guid](#guid) |[Instr](#instr) |[InStrRev](#instrrev) |[LCase](#lcase) | |
| [Vlevo](#left) |[Len](#len) |[Ltrim](#ltrim) |[Polovině](#mid) | |
| [PadLeft](#padleft) |[PadRight](#padright) |[PCase](#pcase) |[Nahradit](#replace) | |
| [Nahradit Chars](#replacechars) |[Právo](#right) |[Rtrim](#rtrim) |[Trim](#trim) | |
| [UCase](#ucase) |[Word](#word) | | | |

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
`BitAnd(&HF, &HF7)`  
Vrátí hodnotu 7, protože šestnáctkové "F" a "F7" vyhodnotit na tuto hodnotu.

---
### <a name="bitor"></a>Bitor
**Popis:**  
Funkce BitOr nastaví zadané bity na hodnotu.

**Syntaxe:**  
`num BitOr(num value1, num value2)`

* value1, value2: číselné hodnoty, které by měly být OR'ed společně

**Poznámky:**  
Tato funkce převede oba parametry na binární reprezentaci a nastaví bit na 1, pokud jeden nebo oba odpovídající bity v masce a příznaku jsou 1 a 0, pokud oba odpovídající bity jsou 0. Jinými slovy vrátí 1 ve všech případech s výjimkou, kde odpovídající bity obou parametrů jsou 0.

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
### <a name="cdate"></a>Datum c.
**Popis:**  
Funkce CDate vrátí čas DateTime u utc z řetězce. DateTime není nativní typ atributu v sync, ale je používán některými funkcemi.

**Syntaxe:**  
`dt CDate(str value)`

* Hodnota: Řetězec s datem, časem a volitelně časovým pásmem

**Poznámky:**  
Vrácený řetězec je vždy v UTC.

**Příklad:**  
`CDate([employeeStartTime])`  
Vrátí datetime na základě počátečního času zaměstnance.

`CDate("2013-01-10 4:00 PM -8")`  
Vrátí datetime představující "2013-01-11 12:00 AM"


---
### <a name="certextensionoids"></a>CertExtensionOidy
**Popis:**  
Vrátí hodnoty Oid všech kritických rozšíření objektu certifikátu.

**Syntaxe:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certformat"></a>Formát certifikátu
**Popis:**  
Vrátí název formátu tohoto certifikátu X.509v3.

**Syntaxe:**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certfriendlyname"></a>Název certifikátu
**Popis:**  
Vrátí přidružený alias certifikátu.

**Syntaxe:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certhashstring"></a>CertHashString
**Popis:**  
Vrátí hodnotu hash SHA1 pro certifikát X.509v3 jako šestnáctkový řetězec.

**Syntaxe:**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certissuer"></a>CertIssuer
**Popis:**  
Vrátí název certifikační autority, která vydala certifikát X.509v3.

**Syntaxe:**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certissuerdn"></a>CertIssuerDN
**Popis:**  
Vrátí rozlišující název vystavittele certifikátu.

**Syntaxe:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certissueroid"></a>CertIssuerOid
**Popis:**  
Vrátí oid vystavittele certifikátu.

**Syntaxe:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certkeyalgorithm"></a>Algoritmus CertKey
**Popis:**  
Vrátí informace o algoritmu klíče pro tento certifikát X.509v3 jako řetězec.

**Syntaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Popis:**  
Vrátí parametry klíčového algoritmu pro certifikát X.509v3 jako šestnáctkový řetězec.

**Syntaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certnameinfo"></a>Informace o názvu certifikátu
**Popis:**  
Vrátí názvy předmětů a vystavitptorů z certifikátu.

**Syntaxe:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.
*   X509NameType: Hodnota X509NameType pro předmět.
*   includesIssuerName: true zahrnout název vystavittele; jinak false.

---
### <a name="certnotafter"></a>CertNotAfter
**Popis:**  
Vrátí datum v místním čase, po kterém již certifikát není platný.

**Syntaxe:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certnotbefore"></a>CertNotPřed
**Popis:**  
Vrátí datum v místním čase, kdy se certifikát stane platným.

**Syntaxe:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Popis:**  
Vrátí oid veřejného klíče pro certifikát X.509v3.

**Syntaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Popis:**  
Vrátí oid parametrů veřejného klíče pro certifikát X.509v3.

**Syntaxe:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certserialnumber"></a>Číslo CertSerial
**Popis:**  
Vrátí sériové číslo certifikátu X.509v3.

**Syntaxe:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certsignaturealgorithmoid"></a>CertifikátSignatureAlgorithmOid
**Popis:**  
Vrátí oid algoritmus použitý k vytvoření podpisu certifikátu.

**Syntaxe:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certsubject"></a>CertSubject
**Popis:**  
Získá rozlišující název subjektu z certifikátu.

**Syntaxe:**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Popis:**  
Vrátí rozlišující název subjektu od certifikátu.

**Syntaxe:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Popis:**  
Vrátí Oid názvu subjektu z certifikátu.

**Syntaxe:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certthumbprint"></a>CertThumbprint
**Popis:**  
Vrátí kryptografický otisk certifikátu.

**Syntaxe:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="certversion"></a>Verze certifikátu
**Popis:**  
Vrátí verzi certifikátu ve formátu X.509.

**Syntaxe:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.

---
### <a name="cguid"></a>CGuid (Fr.)
**Popis:**  
CGuid funkce převede řetězcové reprezentace GUID na jeho binární reprezentaci.

**Syntaxe:**  
`bin CGuid(str GUID)`

* Řetězec formátovaný v tomto vzoru: xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx nebo {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx}

---
### <a name="contains"></a>Contains
**Popis:**  
Funkce Contains najde řetězec uvnitř atributu s více hodnotami.

**Syntaxe:**  
`num Contains (mvstring attribute, str search)`- malá a velká písmena  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)`- malá a velká písmena

* atribut: vícehodnotový atribut pro vyhledávání.
* hledání: řetězec najít v atributu.
* Typ velkých a malých písmen: Malá a velká písmena nebo malá a velká písmena.

Vrátí index v atributu s více hodnotami, kde byl řetězec nalezen. 0 je vrácena, pokud řetězec nebyl nalezen.

**Poznámky:**  
U atributů řetězce s více hodnotami hledání vyhledá podřetězce v hodnotách.  
Pro referenční atributy musí hledaný řetězec přesně odpovídat hodnotě, která má být považována za shodu.

**Příklad:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Pokud má atribut proxyAddresses primární e-mailovou adresu (označenou velkými písmeny "SMTP:"), vraťte atribut proxyAddress, jinak vraťte chybu.

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
### <a name="convertfromutf8hex"></a>PřevéstFromUTF8Hex
**Popis:**  
Funkce ConvertFromUTF8Hex převede zadanou hodnotu kódování UTF8 Hex na řetězec.

**Syntaxe:**  
`str ConvertFromUTF8Hex(str source)`

* zdroj: UTF8 2-byte kódované sting

**Poznámky:**  
Rozdíl mezi touto funkcí a ConvertFromBase64([],UTF8) v tom, že výsledek je přátelský pro atribut DN.  
Tento formát používá služba Azure Active Directory jako DN.

**Příklad:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Vrací *"Hello world!*"

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
### <a name="cnum"></a>CNum
**Popis:**  
Funkce CNum přebírá řetězec a vrací číselný datový typ.

**Syntaxe:**  
`num CNum(str value)`

---
### <a name="cref"></a>CRef
**Popis:**  
Převede řetězec na atribut odkazu.

**Syntaxe:**  
`ref CRef(str value)`

**Příklad:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

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
### <a name="dateadd"></a>DateAdd
**Popis:**  
Vrátí hodnotu a Date obsahující datum, ke kterému byl přidán zadaný časový interval.

**Syntaxe:**  
`dt DateAdd(str interval, num value, dt date)`

* Interval: Řetězcový výraz, který je časovým intervalem, který chcete přidat. Řetězec musí mít jednu z následujících hodnot:
  * rok yyyy
  * q Čtvrtletí
  * m Měsíc
  * y Den v roce
  * d Den
  * w Den v týdnu
  * ww týden
  * h Hodina
  * n Minuta
  * s Druhý
* Hodnota: Počet jednotek, které chcete přidat. To může být pozitivní (získat data v budoucnu) nebo negativní (získat data v minulosti).
* Datum: DateTime představující datum, ke kterému je interval přidán.

**Příklad:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Přidá 3 měsíce a vrátí DateTime představující "2001-04-01".

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
### <a name="dncomponentrev"></a>DNComponentRev
**Popis:**  
Funkce DNComponentRev vrátí hodnotu zadané součásti DN, která bude zprava (konec).

**Syntaxe:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* dn: referenční atribut pro interpretaci
* ComponentNumber - Součást v DN vrátit
* Možnosti: DC – Ignorovat všechny komponenty s "dc="

**Příklad:**  
Pokud dn je "cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com" pak  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Oba vrátí USA.

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
### <a name="escapedncomponent"></a>EscapeDNComponent
**Popis:**  
Funkce EscapeDNComponent přebírá jednu součást DN a unikne, aby mohla být reprezentována v protokolu LDAP.

**Syntaxe:**  
`str EscapeDNComponent(str value)`

**Příklad:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Zajišťuje, že objekt lze vytvořit v adresáři LDAP i v případě, že atribut displayName obsahuje znaky, které musí být v protokolu LDAP uvozeny.

---
### <a name="formatdatetime"></a>Formatdatetime
**Popis:**  
Funkce FormatDateTime se používá k formátování datačasu na řetězec se zadaným formátem.

**Syntaxe:**  
`str FormatDateTime(dt value, str format)`

* hodnota: hodnota ve formátu DateTime
* formát: řetězec představující formát, do který se má převést.

**Poznámky:**  
Možné hodnoty formátu naleznete zde: [Vlastní formáty data a času pro funkci FORMAT](https://docs.microsoft.com/dax/custom-date-and-time-formats-for-the-format-function).

**Příklad:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Výsledky v "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Může mít za následek "20140905081453.0Z"

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
### <a name="instrrev"></a>InStrRev
**Popis:**  
Funkce InStrRev vyhledá poslední výskyt podřetězce v řetězci.

**Syntaxe:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: řetězec, který má být prohledán
* stringmatch: řetězec, který má být nalezen
* start: počáteční pozice pro nalezení podřetězce
* porovnat: vbTextCompare nebo vbBinaryCompare

**Poznámky:**  
Vrátí pozici, kde byl nalezen podřetězec nebo 0, pokud nebyl nalezen.

**Příklad:**  
`InStrRev("abbcdbbbef","bb")`  
Vrátí 7

---
### <a name="isbitset"></a>Sada IsbitSet
**Popis:**  
Funkce IsBitSet testuje, pokud je bit nastaven nebo ne

**Syntaxe:**  
`bool IsBitSet(num value, num flag)`

* hodnota: číselná hodnota, která je vyhodnocena.

**Příklad:**  
`IsBitSet(&HF,4)`  
Vrátí hodnotu True, protože bit "4" je nastaven v šestnáctkové hodnotě "F".

---
### <a name="isdate"></a>Isdate
**Popis:**  
Pokud výraz lze vyhodnotit jako DateTime typ, pak IsDate funkce vyhodnotí true.

**Syntaxe:**  
`bool IsDate(var Expression)`

**Poznámky:**  
Slouží k určení, zda CDate() může být úspěšný.

---
### <a name="iscert"></a>IsCert
**Popis:**  
Vrátí hodnotu true, pokud lze nezpracovaná data serializovat do objektu certifikátu .NET X509Certificate2.

**Syntaxe:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Reprezentace bajtového pole certifikátu X.509. Bajtové pole může být binární (DER) kódované nebo Base64 kódované X.509 data.
---
### <a name="isempty"></a>IsEmpty
**Popis:**  
Pokud je atribut přítomen v CS nebo MV, ale vyhodnotí se na prázdný řetězec, pak IsEmpty funkce vyhodnotí True.

**Syntaxe:**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>IsGuid (IsGuid)
**Popis:**  
Pokud řetězec lze převést na identifikátor GUID, pak isguid funkce vyhodnocena na true.

**Syntaxe:**  
`bool IsGuid(str GUID)`

**Poznámky:**  
Identifikátor GUID je definován jako řetězec následující po jednom z těchto vzorů: xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxx xxx nebo {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Slouží k určení, zda CGuid() může být úspěšný.

**Příklad:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Pokud StrAttribute má formát GUID, vrátí binární reprezentaci, jinak vrátí Null.

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
### <a name="isnumeric"></a>IsNumeric
**Popis:**  
Funkce IsNumeric vrátí logickou hodnotu označující, zda lze výraz vyhodnotit jako typ čísla.

**Syntaxe:**  
`bool IsNumeric(var Expression)`

**Poznámky:**  
Slouží k určení, pokud CNum() může být úspěšné analyzovat výraz.

---
### <a name="isstring"></a>IsString
**Popis:**  
Pokud výraz lze vyhodnotit na typ řetězce, pak IsString funkce vyhodnotí True.

**Syntaxe:**  
`bool IsString(var expression)`

**Poznámky:**  
Slouží k určení, pokud CStr() může být úspěšné analyzovat výraz.

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
### <a name="itemornull"></a>ItemorNull
**Popis:**  
Funkce ItemOrNull vrátí jednu položku z vícehodnotového řetězce/atributu.

**Syntaxe:**  
`var ItemOrNull(mvstr attribute, num index)`

* atribut: atribut s více hodnotami
* index: index na položku v řetězci s více hodnotami.

**Poznámky:**  
Funkce ItemOrNull je užitečná společně s funkcí Contains, protože druhá funkce vrátí index k položce v atributu s více hodnotami.

Pokud index je mimo hranice, vrátí hodnotu Null.

---
### <a name="join"></a>Spojit
**Popis:**  
Funkce Join přebírá řetězec s více hodnotami a vrátí řetězec s jednou hodnotou se zadaným oddělovačem vloženým mezi každou položku.

**Syntaxe:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* Atribut s více hodnotami obsahující řetězce, které mají být spojeny.
* oddělovač: Libovolný řetězec, který se používá k oddělení podřetězců ve vráceném řetězci. Pokud je vynechán, je použit znak mezery (" "). Pokud oddělovač je řetězec nulové délky ("") nebo Nothing, všechny položky v seznamu jsou zřetězeny bez oddělovačů.

**Poznámky**  
Mezi funkcemi Join a Split existuje parita. Funkce Join přebírá pole řetězců a spojuje je pomocí řetězce oddělovače, aby se vrátil jeden řetězec. Split funkce trvá řetězec a odděluje jej na oddělovač, vrátit pole řetězců. Klíčovým rozdílem je však to, že Join může zřetězit řetězce s libovolným řetězcem oddělovače, Split může oddělit pouze řetězce pomocí oddělovače jednoho znaku.

**Příklad:**  
`Join([proxyAddresses],",")`  
Mohl bySMTP:john.doe@contoso.comsesmtp:jd@contoso.comvrátit: " , "

---
### <a name="lcase"></a>LCase
**Popis:**  
Funkce LCase převede všechny znaky v řetězci na malá písmena.

**Syntaxe:**  
`str LCase(str value)`

**Příklad:**  
`LCase("TeSt")`  
Vrátí "test".

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
Vrátí "Joh".

---
### <a name="len"></a>Len
**Popis:**  
Funkce Len vrátí počet znaků v řetězci.

**Syntaxe:**  
`num Len(str value)`

**Příklad:**  
`Len("John Doe")`  
Vrátí 8

---
### <a name="ltrim"></a>Ltrim
**Popis:**  
Funkce LTrim odebere úvodní mezery z řetězce.

**Syntaxe:**  
`str LTrim(str value)`

**Příklad:**  
`LTrim(" Test ")`  
Vrátí "Test"

---
### <a name="mid"></a>Mid
**Popis:**  
Funkce Mid vrátí zadaný počet znaků ze zadané pozice v řetězci.

**Syntaxe:**  
`str Mid(str string, num start, num NumChars)`

* řetězec: řetězec pro vrácení znaků z
* start: číslo identifikující počáteční pozici v řetězci pro vrácení znaků
* NumChars: číslo identifikující počet znaků, které se mají vrátit z pozice v řetězci

**Poznámky:**  
Vrátí znaky numChars počínaje pozicí začínající řetězcem.  
Řetězec obsahující znaky numChars z pozice začíná v řetězci:

* Pokud numChars = 0, vrátí prázdný řetězec.
* Pokud numChars < 0, vrátí vstupní řetězec.
* Pokud začínáte > délku řetězce, vraťte vstupní řetězec.
* Pokud start <= 0, vrátí vstupní řetězec.
* Pokud je řetězec null, vrátí prázdný řetězec.

Pokud nejsou numChar znaky zbývající v řetězci od začátku pozice, jsou vráceny co nejvíce znaků.

**Příklad:**  
`Mid("John Doe", 3, 5)`  
Vrátí vrátí hodnotu "hn Do".

`Mid("John Doe", 6, 999)`  
Vrátí "Doe"

---
### <a name="now"></a>Now
**Popis:**  
Funkce Nyní vrátí datum DataTime určující aktuální datum a čas podle systémového data a času počítače.

**Syntaxe:**  
`dt Now()`

---
### <a name="numfromdate"></a>Datum numfrom
**Popis:**  
Funkce NumFromDate vrátí datum ve formátu data ad.

**Syntaxe:**  
`num NumFromDate(dt value)`

**Příklad:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Vrátí hodnotu 1296993240000000000.

---
### <a name="padleft"></a>PadLeft
**Popis:**  
Funkce PadLeft levé podložky řetězec na zadanou délku pomocí zadaného znaku odsazení.

**Syntaxe:**  
`str PadLeft(str string, num length, str padCharacter)`

* řetězec: řetězec na podložku.
* Délka: Celé číslo představující požadovanou délku řetězce.
* padCharacter: Řetězec skládající se z jednoho znaku, který se má použít jako znak podložky

**Poznámky:**

* Pokud délka řetězce je menší než délka, pak padCharacter je opakovaně připojen k začátku (vlevo) řetězce, dokud nemá délku rovnající se délce.
* PadCharacter může být znak mezery, ale nemůže být nulovou hodnotou.
* Pokud délka řetězce je rovna nebo větší než délka, řetězec je vrácena beze změny.
* Pokud řetězec má délku větší nebo rovna délce, je vrácen řetězec identický s řetězcem.
* Pokud délka řetězce je menší než délka, pak je vrácen nový řetězec požadované délky obsahující řetězec doplněný padCharacter.
* Pokud je řetězec null, funkce vrátí prázdný řetězec.

**Příklad:**  
`PadLeft("User", 10, "0")`  
Vrátí hodnotu "000000User".

---
### <a name="padright"></a>PadRight
**Popis:**  
Funkce PadRight pravá podložky řetězec na zadanou délku pomocí zadaného odsazení znaku.

**Syntaxe:**  
`str PadRight(str string, num length, str padCharacter)`

* řetězec: řetězec na podložku.
* Délka: Celé číslo představující požadovanou délku řetězce.
* padCharacter: Řetězec skládající se z jednoho znaku, který se má použít jako znak podložky

**Poznámky:**

* Pokud délka řetězce je menší než délka, pak padCharacter je opakovaně připojen na konec (vpravo) řetězce, dokud nemá délku rovnající se délce.
* padCharacter může být znak mezery, ale nemůže být nulovou hodnotou.
* Pokud délka řetězce je rovna nebo větší než délka, řetězec je vrácena beze změny.
* Pokud řetězec má délku větší nebo rovna délce, je vrácen řetězec identický s řetězcem.
* Pokud délka řetězce je menší než délka, pak je vrácen nový řetězec požadované délky obsahující řetězec doplněný padCharacter.
* Pokud je řetězec null, funkce vrátí prázdný řetězec.

**Příklad:**  
`PadRight("User", 10, "0")`  
Vrátí hodnotu "User000000".

---
### <a name="pcase"></a>PCase
**Popis:**  
Funkce PCase převede první znak každého slova odděleného mezerou v řetězci na velká písmena a všechny ostatní znaky se převedou na malá písmena.

**Syntaxe:**  
`String PCase(string)`

**Poznámky:**

* Tato funkce v současné době neposkytuje správné písmeno pro převod slova, které je zcela velké, například zkratka.

**Příklad:**  
`PCase("TEsT")`  
Vrátí "Test".

`PCase(LCase("TEST"))`  
Vrátí "Test"

---
### <a name="randomnum"></a>RandomNum
**Popis:**  
Funkce RandomNum vrátí náhodné číslo mezi zadaným intervalem.

**Syntaxe:**  
`num RandomNum(num start, num end)`

* start: číslo identifikující dolní hranici náhodné hodnoty, která má být generována
* konec: číslo identifikující horní hranici náhodné hodnoty, která má být generována

**Příklad:**  
`Random(100,999)`  
Může vrátit 734.

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
**Popis:**  
Funkce Nahradit nahradí všechny výskyty řetězce jiným řetězcem.

**Syntaxe:**  
`str Replace(str string, str OldValue, str NewValue)`

* řetězec: Řetězec, ve kterých mají být nahrazeny hodnoty.
* OldValue: Řetězec pro vyhledávání a nahrazení.
* NewValue: Řetězec nahradit.

**Poznámky:**  
Funkce rozpozná následující speciální zástupné názvy:

* \n – Nový řádek
* \r – Návrat vozíku
* \t – karta

**Příklad:**  
`Replace([address],"\r\n",", ")`  
Nahradí CRLF čárkou a mezerou a může vést k "One Microsoft Way, Redmond, WA, USA"

---
### <a name="replacechars"></a>Nahradit Chars
**Popis:**  
Funkce ReplaceChars nahrazuje všechny výskyty znaků nalezených v řetězci ReplacePattern.

**Syntaxe:**  
`str ReplaceChars(str string, str ReplacePattern)`

* Řetězec: Řetězec, ve který chcete nahradit znaky.
* ReplacePattern: řetězec obsahující slovník se znaky nahradit.

Formát je {source1}:{target1},{source2}:{target2},{sourceN},{targetN}, kde zdroj je znak, který má být vyhledán a cílí na řetězec, který má být nahrazen.

**Poznámky:**

* Funkce přebírá každý výskyt definovaných zdrojů a nahrazuje je cíli.
* Zdroj musí být přesně jeden znak (unicode).
* Zdroj nemůže být prázdný nebo delší než jeden znak (chyba analýzy).
* Cíl může mít více znaků, například ö:oe, β:ss.
* Cíl může být prázdný označující, že znak by měl být odebrán.
* Zdroj rozlišuje malá a velká písmena a musí se přesně shodovat.
* Znaky , (čárka) a : (dvojtečka) jsou vyhrazené znaky a nelze je pomocí této funkce nahradit.
* Mezery a jiné bílé znaky v řetězci ReplacePattern jsou ignorovány.

**Příklad:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Vrátí Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Vrátí "ONeil", jeden tick je definován, které mají být odstraněny.

---
### <a name="right"></a>Vpravo
**Popis:**  
Funkce Right vrátí zadaný počet znaků zprava (konce) řetězce.

**Syntaxe:**  
`str Right(str string, num NumChars)`

* řetězec: řetězec pro vrácení znaků z
* NumChars: číslo identifikující počet znaků, které mají být vráceny z konce (vpravo) řetězce

**Poznámky:**  
Znaky NumChars jsou vráceny z poslední pozice řetězce.

Řetězec obsahující poslední znaky numChars v řetězci:

* Pokud numChars = 0, vrátí prázdný řetězec.
* Pokud numChars < 0, vrátí vstupní řetězec.
* Pokud je řetězec null, vrátí prázdný řetězec.

Pokud řetězec obsahuje méně znaků než číslo zadané v NumChars, je vrácen řetězec identický s řetězcem.

**Příklad:**  
`Right("John Doe", 3)`  
Vrátí "Doe".

---
### <a name="rtrim"></a>Rtrim
**Popis:**  
Funkce RTrim odebere koncové mezery z řetězce.

**Syntaxe:**  
`str RTrim(str value)`

**Příklad:**  
`RTrim(" Test ")`  
Vrátí "Test".

---
### <a name="select"></a>Vyberte
**Popis:**  
Zpracujte všechny hodnoty v atributu s více hodnotami (nebo výstupu výrazu) na základě zadané funkce.

**Syntaxe:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* položka: Představuje prvek v atributu s více hodnotami.
* atribut: atribut s více hodnotami
* Výraz: výraz, který vrací kolekci hodnot
* podmínka: libovolná funkce, která dokáže zpracovat položku v atributu

**Příklady:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Vrátit všechny hodnoty v atributu s více hodnotami otherPhone po odstranění spojovníků (-).

---
### <a name="split"></a>Rozdělení
**Popis:**  
Funkce Split přebírá řetězec oddělený oddělovačem a činí z něj řetězec s více hodnotami.

**Syntaxe:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* hodnota: řetězec se znakem oddělovače k oddělení.
* oddělovač: jeden znak, který se použije jako oddělovač.
* limit: maximální počet hodnot, které se mohou vrátit.

**Příklad:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Vrátí řetězec s více hodnotami se 2 prvky užitečnými pro atribut proxyAddress.

---
### <a name="stringfromguid"></a>ŘetězecFromGuid
**Popis:**  
Funkce StringFromGuid převezme binární identifikátor GUID a převede jej na řetězec.

**Syntaxe:**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>StringFromSid
**Popis:**  
Funkce StringFromSid převede bajtové pole obsahující identifikátor zabezpečení na řetězec.

**Syntaxe:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>Přepínač
**Popis:**  
Funkce Switch se používá k vrácení jedné hodnoty na základě vyhodnocených podmínek.

**Syntaxe:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* expr: Varianta výraz, který chcete vyhodnotit.
* hodnota: Hodnota, která má být vrácena, pokud je odpovídající výraz True.

**Poznámky:**  
Seznam argumentů funkce Switch se skládá z dvojic výrazů a hodnot. Výrazy jsou vyhodnocovány zleva doprava a je vrácena hodnota přidružená k prvnímu výrazu, který má být vyhodnocen jako True. Pokud součásti nejsou správně spárovány, dojde k chybě za běhu.

Například pokud expr1 je True, Přepínač vrátí hodnotu1. Pokud expr-1 je False, ale expr-2 je True, Switch vrátí hodnotu-2 a tak dále.

Přepínač vrátí hodnotu Nothing, pokud:

* Žádný z výrazů není True.
* První True výraz má odpovídající hodnotu, která je Null.

Přepínač vyhodnotí všechny výrazy, i když vrátí pouze jeden z nich. Z tohoto důvodu byste měli sledovat nežádoucí vedlejší účinky. Například pokud vyhodnocení libovolného výrazu má za následek dělení nulovou chybou, dojde k chybě.

Hodnota může být také Error funkce, která by vrátila vlastní řetězec.

**Příklad:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Vrátí jazyk používaný v některých velkých městech, jinak vrátí error.

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
### <a name="ucase"></a>UCase
**Popis:**  
Funkce UCase převede všechny znaky v řetězci na velká písmena.

**Syntaxe:**  
`str UCase(str string)`

**Příklad:**  
`UCase("TeSt")`  
Vrátí "TEST".

---
### <a name="where"></a>Kde

**Popis:**  
Vrátí podmnožinu hodnot z vícehodnotového atributu (nebo výstupu výrazu) na základě určité podmínky.

**Syntaxe:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* položka: Představuje prvek v atributu s více hodnotami.
* atribut: atribut s více hodnotami
* podmínka: jakýkoli výraz, který lze vyhodnotit jako pravdivý nebo nepravdivý
* Výraz: výraz, který vrací kolekci hodnot

**Příklad:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Vraťte hodnoty certifikátu v multi-valued atribut userCertificate, které nejsou vypršela.

---
### <a name="with"></a>With
**Popis:**  
Funkce With poskytuje způsob zjednodušení složitého výrazu pomocí proměnné představující podvýraz, který se ve složitém výrazu zobrazuje jednou nebo vícekrát.

**Syntaxe:**
`With(var variable, exp subExpression, exp complexExpression)`  
* proměnná: Představuje podvýraz.
* subExpression: dílčí výraz reprezentované proměnnou.
* complexExpression: Komplexní výraz.

**Příklad:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
Je funkčně ekvivalentní:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Který vrátí pouze hodnoty certifikátu, jejichž platnost nevypršela v atributu userCertificate.


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

## <a name="additional-resources"></a>Další zdroje
* [Principy výrazů deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Synchronizace připojení Azure AD: Přizpůsobení možností synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
