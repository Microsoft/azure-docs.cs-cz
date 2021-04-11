---
title: Seznam podporovaných klasifikací
description: Tato stránka obsahuje seznam podporovaných systémových klasifikací v Azure dosah.
author: anmuk601
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: reference
ms.date: 4/1/2021
ms.openlocfilehash: e1d3d495d958465e966701aa7ce91bc2706b48e0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219582"
---
# <a name="supported-classifications-in-azure-purview"></a>Podporované klasifikace v Azure dosah

Tento článek obsahuje seznam podporovaných a definovaných systémových klasifikací v Azure dosah (Preview).


- **Rozdílová prahová hodnota shody**: celkový počet jedinečných datových hodnot, které musí být ve sloupci nalezeny předtím, než skener spustí datový vzor. Rozdílová prahová hodnota pro porovnání nemá žádnou akci s porovnáváním vzorů, ale je předpokladem pro porovnávání vzorů. Naše pravidla klasifikace systému vyžadují, aby v každém sloupci bylo aspoň 8 jedinečných hodnot, aby je bylo možné považovat za klasifikace. Tato hodnota vyžaduje, aby tento sloupec obsahoval dostatek dat, aby ho mohl přesně klasifikovat skener. Například sloupec, který obsahuje více řádků, které obsahují hodnotu 1, nebude klasifikován. Sloupce, které obsahují jeden řádek s hodnotou a zbytek řádků, obsahují hodnoty null ani nebudou klasifikovány. Pokud zadáte více vzorů, bude tato hodnota platit pro každý z nich.

- **Minimální prahová hodnota shody**: Jedná se o minimální procento shody hodnot dat ve sloupci, který musí být nalezen skenerem pro použití klasifikace. Systémová hodnota klasifikace je nastavená na 60%.


## <a name="defined-system-classifications"></a>Definované klasifikace systému

Azure dosah klasifikuje data podle [regulárního výrazu](https://wikipedia.org/wiki/Regular_expression) a [filtru Bloom](https://wikipedia.org/wiki/Bloom_filter). Následující seznamy popisují formát, vzor a klíčová slova pro klasifikace systému definované v Azure dosah.

Každý název klasifikace je předponou společnosti MICROSOFT.

## <a name="bloom-filter-classifications"></a>Klasifikace filtru Bloom

## <a name="city-country-and-place"></a>Město, země a místo

Filtry města, země a místa byly připraveny pomocí nejlepších datových sad, které jsou k dispozici pro přípravu dat.

## <a name="person"></a>Person (Osoba)

Filtr person Bloom se připravil pomocí níže uvedených dvou datových sad.

- [2010. data pro příjmení USA (položky 162-K)](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [Oblíbené názvy dětí (z SSN), s využitím všech roků 1880-2019 (položky 98-K)](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>Klasifikace regulárního výrazu

## <a name="aba-routing"></a>ABA směrování

### <a name="format"></a>Formát

Devět číslic, které mohou být ve formátovaném nebo neformátovaném vzoru

### <a name="pattern"></a>Vzor

Formátu

- čtyři číslice začínající 0, 1, 2, 3, 6, 7 nebo 8
- spojovník
- čtyři číslice
- spojovník
- číslice

Nezformátováno: devět po sobě jdoucích číslic počínaje 0, 1, 2, 3, 6, 7 nebo 8

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_aba_routing"></a>Keyword_aba_routing

```
amba number
aba #
aba
abarouting#
abaroutingnumber
americanbankassociationrouting#
americanbankassociationroutingnumber
bank routing#
bankroutingnumber
routing #
routing no
routing number
routing transit number
routing#
RTN
```

## <a name="argentina-national-identity-dni-number"></a>Číslo argentinské National identity (DNI)

### <a name="format"></a>Formát

Osm číslic s tečkami nebo bez nich

### <a name="pattern"></a>Vzor

Osm číslic:

- dvě číslice
- volitelné období
- tři číslice
- volitelné období
- tři číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_argentina_national_id"></a>Keyword_argentina_national_id

```
Argentina National Identity number
cedula
cédula
dni
documento nacional de identidad
documento número
documento numero
registro nacional de las personas
rnp
```

## <a name="australia-bank-account-number"></a>Číslo bankovního účtu Austrálie

### <a name="format"></a>Formát

Šest až 10 číslic s nebo bez čísla pobočky bankového stavu

### <a name="pattern"></a>Vzor

Číslo účtu je šest až 10 číslic.

Číslo pobočky stavového banky v Austrálii:

- tři číslice
- spojovník
- tři číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_australia_bank_account_number"></a>Keyword_australia_bank_account_number

```
swift bank code
correspondent bank
base currency
usa account
holder address
bank address
information account
fund transfers
bank charges
bank details
banking information
full names
iaea
```

## <a name="australia-drivers-license-number"></a>Číslo licence ovladače Austrálie

### <a name="format"></a>Formát
Devět písmen a číslic

### <a name="pattern"></a>Vzor
devět písmen a číslic:

- dvě číslice nebo písmena (nerozlišují se malá a velká písmena)
- dvě číslice
- pět číslic nebo písmen (nerozlišuje malá a velká písmena)

NEBO

- jedna až dvě volitelná písmena (nerozlišují se malá a velká písmena)
- čtyři až devět číslic

NEBO

- devět číslic nebo písmen (nerozlišuje malá a velká písmena)

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_australia_drivers_license_number"></a>Keyword_australia_drivers_license_number

```
international driving permits
australian automobile association
international driving permit
DriverLicence
DriverLicences
Driver Lic
Driver License
Driver Licenses
DriversLic
DriversLicence
DriversLicences
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
DriverLic#
DriverLics#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
```

#### <a name="keyword_australia_drivers_license_number_exclusions"></a>Keyword_australia_drivers_license_number_exclusions

```
aaa
DriverLicense
DriverLicenses
Driver License
Driver Licenses
DriversLicense
DriversLicenses
Drivers License
Drivers Licenses
Driver'License
Driver'Licenses
Driver' License
Driver' Licenses
Driver'sLicense
Driver'sLicenses
Driver's License
Driver's Licenses
DriverLicense#
DriverLicenses#
Driver License#
Driver Licenses#
DriversLicense#
DriversLicenses#
Drivers License#
Drivers Licenses#
Driver'License#
Driver'Licenses#
Driver' License#
Driver' Licenses#
Driver'sLicense#
Driver'sLicenses#
Driver's License#
Driver's Licenses#
```

## <a name="australian-medicare-number"></a>Číslo australského Medicareu

### <a name="format"></a>Formát

10-11 číslic

### <a name="pattern"></a>Vzor

10-11 číslic:

- první číslice je v rozsahu 2-6
- Devátá číslice je kontrolní číslice.
- desátá číslice je číslice problému.
- Jedenáctá číslice (volitelné) je individuální číslo.

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_australia_medicare_number"></a>Keyword_Australia_Medicare_Number

```
bank account details
medicare payments
mortgage account
bank payments
information branch
credit card loan
department of human services
local service
medicare
```

## <a name="australia-passport-number"></a>Číslo služby Passport v Austrálii

### <a name="format"></a>Formát

Písmeno následované sedmi číslicemi

### <a name="pattern"></a>Vzor

Písmeno (nerozlišuje malá a velká písmena) následovaný sedmi číslicemi

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_passport"></a>Klíčové slovo \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

#### <a name="keyword_australia_passport_number"></a>\_ \_ Číslo služby Passport pro klíčové slovo Austrálie \_

```
passport
passport details
immigration and citizenship
commonwealth of australia
department of immigration
residential address
department of immigration and citizenship
visa
national identity card
passport number
travel document
issuing authority
```

## <a name="australia-tax-file-number"></a>Číslo daňového souboru v Austrálii

### <a name="format"></a>Formát

osm až devět číslic

### <a name="pattern"></a>Vzor

osm až devět číslic obvykle prezentuje mezery následujícím způsobem:

- tři číslice
- volitelné místo
- tři číslice
- volitelné místo
- dvě až tři číslice, kde je poslední číslice kontrolní číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_australia_tax_file_number"></a>\_ \_ \_ Číslo souboru daně \_ z klíčového slova Austrálie

```
australian business number
marginal tax rate
medicare levy
portfolio number
service veterans
withholding tax
individual tax return
tax file number
tfn
```

## <a name="belgium-national-number"></a>Národní číslo v Belgii

### <a name="format"></a>Formát

11 číslic plus volitelné oddělovače

### <a name="pattern"></a>Vzor

11 číslic plus oddělovače:

- šest číslic a dvou volitelných teček ve formátu YY. MM. DD pro datum narození
- Volitelný oddělovač z tečky, pomlčky, mezery
- tři sekvenční číslice (liché pro samci, dokonce i u samic)
- Volitelný oddělovač z tečky, pomlčky, mezery
- dvě kontrolní číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_belgium_national_number"></a>\_ \_ Národní číslo Belgie \_ pro klíčové slovo

```
be lasting aantal
bnn#
bnn
carte d'identité
identifiant national
identifiantnational#
identificatie
identification
identifikation
identifikationsnummer
identifizierung
identité
identiteit
identiteitskaart
identity
inscription
national number
national register
national number#
national number
nif#
nif
numéro d'assuré
numéro de registre national
numéro de sécurité
numéro d'identification
numéro d'immatriculation
numéro national
numéronational#
personal ID number
personalausweis
personalidnumber#
registratie
registration
registrationsnumme
registrierung
social security number
ssn#
ssn
steuernummer
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="brazil-cpf-number"></a>Brazílie – CPF číslo

### <a name="format"></a>Formát

11 číslic, které obsahují kontrolní číslici a můžou být naformátované nebo neformátované

### <a name="pattern"></a>Vzor

Formátu

- tři číslice
- tečka
- tři číslice
- tečka
- tři číslice
- spojovník
- dvě číslice, které jsou kontrolní číslice

Neformátovaný tvar:

- 11 číslic, kde jsou poslední dvě číslice kontrolní číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_brazil_cpf"></a>Klíčové slovo \_ Brazílie \_ CPF

```
CPF
Identification
Registration
Revenue
Cadastro de Pessoas Físicas
Imposto
Identificação
Inscrição
Receita
```

## <a name="brazil-legal-entity-number-cnpj"></a>Brazílie – právní číslo entity (CNPJ)

### <a name="format"></a>Formát

14 číslic, které obsahují registrační číslo, číslo větve a kontrolní číslice, oddělovače plus

### <a name="pattern"></a>Vzor

14 číslic, plus oddělovače:

- dvě číslice
- tečka
- tři číslice
- tečka
- tři číslice (Tato prvních osm číslic jsou registrační číslo)
- lomítko
- číslo větve se čtyřmi číslicemi
- spojovník
- dvě číslice, které jsou kontrolní číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_brazil_cnpj"></a>Klíčové slovo \_ Brazílie \_ CNPJ

```
CNPJ
CNPJ/MF
CNPJ-MF
National Registry of Legal Entities
Taxpayers Registry
Legal entity
Legal entities
Registration Status
Business
Company
CNPJ
Cadastro Nacional da Pessoa Jurídica
Cadastro Geral de Contribuintes
CGC
Pessoa jurídica
Pessoas jurídicas
Situação cadastral
Inscrição
Empresa
```

## <a name="brazil-national-identification-card-rg"></a>Brazílie, národní identifikační karta (RG)

### <a name="format"></a>Formát

:::no-loc text="Registro Geral (old format): Nine digits":::

:::no-loc text="Registro de Identidade (RIC) (new format): 11 digits":::

### <a name="pattern"></a>Vzor

:::no-loc text="Registro Geral (old format):":::

- dvě číslice
- tečka
- tři číslice
- tečka
- tři číslice
- spojovník
- jedna číslice, což je kontrolní číslice

:::no-loc text="Registro de Identidade (RIC) (new format):":::

- 10 číslic
- spojovník
- jedna číslice, což je kontrolní číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_brazil_rg"></a>Klíčové slovo \_ Brazílie \_ RG

```
Cédula de identidade
identity card
national ID
número de rregistro
registro de Iidentidade
registro geral
RG (this keyword is case-sensitive)
RIC (this keyword is case-sensitive)
```

## <a name="canada-bank-account-number"></a>Číslo bankovního účtu v Kanadě

### <a name="format"></a>Formát

7 nebo 12 číslic

### <a name="pattern"></a>Vzor

Číslo bankovního účtu v Kanadě je 7 nebo 12 číslic.

Číslo tranzitní účtu bank Kanady je:

- pět číslic
- spojovník
- tři číslice nebo
- nula &quot; 0&quot;
- osm číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_canada_bank_account_number"></a>\_ \_ \_ Číslo bankovního účtu \_ pro klíčové slovo Kanada

```
canada savings bonds
canada revenue agency
canadian financial institution
direct deposit form
canadian citizen
legal representative
notary public
commissioner for oaths
child care benefit
universal child care
canada child tax benefit
income tax benefit
harmonized sales tax
social insurance number
income tax refund
child tax benefit
territorial payments
institution number
deposit request
banking information
direct deposit
```

## <a name="canada-drivers-license-number"></a>Číslo licence ovladače Kanady

### <a name="format"></a>Formát

Liší se podle provincie

### <a name="pattern"></a>Vzor

Různé vzory zahrnující Alberta, British Columbia, Manitoba, New Brunswick, Newfoundland/Labrador, Nova Skotsko, Ontario, Prince Edward ostrov, Quebec a Saskatchewan

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_province_name_drivers_license_name"></a>Klíčové slovo \_ [provincie \_ name] \_ \_ název licence Drivers \_

- Zkratka provincie, například AB
- Název provincie, například Alberta

#### <a name="keyword_canada_drivers_license"></a>Licence pro ovladače pro klíčové slovo \_ Kanada \_ \_

```
DL
DLS
CDL
CDLS
DriverLic
DriverLics
DriverLicense
DriverLicenses
DriverLicence
DriverLicences
Driver Lic
Driver Lics
Driver License
Driver Licenses
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicence
DriversLicences
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
Driver's License
Driver's Licenses
Permis de Conduire
ID
IDs
ID card number
ID card numbers
ID card #
ID card #s
ID card
ID cards
ID card
identification number
identification numbers
identification #
identification #s
identification card
identification cards
identification
DL#
DLS#
CDL#
CDLS#
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
Driver's License#
Driver's Licenses#
Permis de Conduire#
ID#
IDs#
ID card#
ID cards#
ID card#
identification card#
identification cards#
identification#
```

## <a name="canada-health-service-number"></a>Číslo služby Health Service pro Kanadu

### <a name="format"></a>Formát

10 číslic

### <a name="pattern"></a>Vzor

10 číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_canada_health_service_number"></a>Klíčové slovo \_ Kanada – \_ \_ číslo služby Health \_

```
personal health number
patient information
health services
specialty services
automobile accident
patient hospital
psychiatrist
workers compensation
disability
```

## <a name="canada-passport-number"></a>Číslo služby Passport pro Kanadu

### <a name="format"></a>Formát

dvě velká písmena následovaná šesti číslicemi

### <a name="pattern"></a>Vzor

dvě velká písmena následovaná šesti číslicemi

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_canada_passport_number"></a>Klíčové slovo \_ Kanada – \_ číslo služby Passport \_

```
canadian citizenship
canadian passport
passport application
passport photos
certified translator
canadian citizens
processing times
renewal application
```

#### <a name="keyword_passport"></a>Klíčové slovo \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="canada-personal-health-identification-number-phin"></a>Kanada – osobní identifikační číslo zdravotního stavu (PHIN)

### <a name="format"></a>Formát

devět číslic

### <a name="pattern"></a>Vzor

devět číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_canada_phin"></a>Klíčové slovo \_ Kanada \_ Phin

```
social insurance number
health information act
income tax information
manitoba health
health registration
prescription purchases
benefit eligibility
personal health
power of attorney
registration number
personal health number
practitioner referral
wellness professional
patient referral
health and wellness
```

#### <a name="keyword_canada_provinces"></a>Provincie pro klíčové slovo \_ Kanada \_

```
Nunavut
Quebec
Northwest Territories
Ontario
British Columbia
Alberta
Saskatchewan
Manitoba
Yukon
Newfoundland and Labrador
New Brunswick
Nova Scotia
Prince Edward Island
Canada
```

## <a name="canada-social-insurance-number"></a>Rodné číslo v Kanadě

### <a name="format"></a>Formát

devět číslic s nepovinnými spojovníky nebo mezerami

### <a name="pattern"></a>Vzor

Formátu

- tři číslice
- pomlčka nebo mezera
- tři číslice
- pomlčka nebo mezera
- tři číslice

Nezformátováno: devět číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_sin"></a>Klíčové slovo \_ Sin

```
sin
social insurance
numero d'assurance sociale
sins
ssn
ssns
social security
numero d'assurance social
national identification number
national ID
sin#
soc ins
social ins
```

#### <a name="keyword_sin_collaborative"></a>Klíčové slovo \_ Sin – \_ spolupráce

```
driver's license
drivers license
driver's license
drivers license
DOB
Birthdate
Birthday
Date of Birth
```

## <a name="chile-identity-card-number"></a>Číslo karty identity Chile

### <a name="format"></a>Formát

7 až osm číslic plus oddělovače, číslice nebo písmeno

### <a name="pattern"></a>Vzor

7 až osm číslic plus oddělovač:

- jedna až dvě číslice
- volitelné období
- tři číslice
- volitelné období
- tři číslice
- pomlčka
- jedna číslice nebo písmeno (nerozlišuje malá a velká písmena), což je kontrolní číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_chile_id_card"></a>\_ \_ Karta ID Chile klíčového slova \_

```
cédula de identidad
identificación
national identification
national identification number
national ID
número de identificación nacional
rol único nacional
rol único tributario
RUN
RUT
tarjeta de identificación
Rol Unico Nacional
Rol Unico Tributario
RUN#
RUT#
nationaluniqueroleID#
nacional identidad
número identificación
identidad número
numero identificacion
identidad numero
Chilean identity no.
Chilean identity number
Chilean identity #
Unique Tax Registry
Unique Tributary Role
Unique Tax Role
Unique Tributary Number
Unique National Number
Unique National Role
National unique role
Chile identity no.
Chile identity number
Chile identity #
```

## <a name="china-resident-identity-card-prc-number"></a>Čína – číslo rezidentní identity (ČLR)

### <a name="format"></a>Formát

18 číslic

### <a name="pattern"></a>Vzor

18 číslic:

- šest číslic, což je kód adresy
- osm číslic ve formě RRRRMMDD, což jsou datum narození
- tři číslice, což je kód objednávky
- jedna číslice, což je kontrolní číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_china_resident_id"></a>Klíčové slovo \_ Čína – \_ ID rezidenta \_

```
Resident Identity Card
PRC
National Identification Card
身份证
居民身份证
居民身份证
鉴定
身分證
居民身份證
鑑定
```

## <a name="credit-card-number"></a>Číslo platební karty

### <a name="format"></a>Formát

14 až 16 číslic, které mohou být naformátovány nebo neformátovány (dddddddddddddddd) a které musí projít testem Luhn.

### <a name="pattern"></a>Vzor

Složitý a robustní vzor, který detekuje karty ze všech hlavních značek po celém světě, včetně víz, MasterCard, karty Discover, JCB, American Express, dárkových karet a Diner karet

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_cc_verification"></a>Klíčové slovo \_ CC – \_ ověření

```
card verification
card identification number
cvn
cid
cvc2
cvv2
pin block
security code
security number
security no
issue number
issue no
cryptogramme
numéro de sécurité
numero de securite
kreditkartenprüfnummer
kreditkartenprufnummer
prüfziffer
prufziffer
sicherheits Kode
sicherheitscode
sicherheitsnummer
verfalldatum
codice di verifica
cod. sicurezza
cod sicurezza
n autorizzazione
código
codigo
cod. seg
cod seg
código de segurança
codigo de seguranca
codigo de segurança
código de seguranca
cód. segurança
cod. seguranca
cod. segurança
cód. seguranca
cód segurança
cod seguranca
cod segurança
cód seguranca
número de verificação
numero de verificacao
ablauf
gültig bis
gültigkeitsdatum
gultig bis
gultigkeitsdatum
scadenza
data scad
fecha de expiracion
fecha de venc
vencimiento
válido hasta
valido hasta
vto
data de expiração
data de expiracao
data em que expira
validade
valor
vencimento
transaction
transaction number
reference number
セキュリティコード
セキュリティ コード
セキュリティナンバー
セキュリティ ナンバー
セキュリティ番号
```

#### <a name="keyword_cc_name"></a>\_Název kopie klíčového slova \_

```
amex
Americans express
Americans express
americano espresso
Visa
Mastercard
master card
mc
master cards
master cards
diner's Club
diners club
diners club
discover
discover card
discover card
discover cards
JCB
BrandSmart
japanese card bureau
carte blanche
carteblanche
credit card
cc#
cc#:
expiration date
exp date
expiry date
date d'expiration
date d'exp
date expiration
bank card
bank card
card number
card num
card number
card numbers
card numbers
credit card
credit cards
credit cards
ccn
card holder
cardholder
card holders
cardholders
check card
check card
check cards
check cards
debit card
debit card
debit cards
debit cards
atm card
atmcard
atm cards
atmcards
enroute
en route
card type
Cardmember Acct
cardmember account
Card no
Corporate Card
Corporate cards
Type of card
card account number
card member account
Cardmember Acct.
card no.
card no
card number
carte bancaire
carte de crédit
carte de credit
numéro de carte
numero de carte
nº de la carte
nº de carte
kreditkarte
karte
karteninhaber
karteninhabers
kreditkarteninhaber
kreditkarteninstitut
kreditkartentyp
eigentümername
kartennr
kartennummer
kreditkartennummer
kreditkarten-nummer
carta di credito
carta credito
n. carta
n carta
nr. carta
nr carta
numero carta
numero della carta
numero di carta
tarjeta credito
tarjeta de credito
tarjeta crédito
tarjeta de crédito
tarjeta de atm
tarjeta atm
tarjeta debito
tarjeta de debito
tarjeta débito
tarjeta de débito
nº de tarjeta
no. de tarjeta
no de tarjeta
numero de tarjeta
número de tarjeta
tarjeta no
tarjetahabiente
cartão de crédito
cartão de credito
cartao de crédito
cartao de credito
cartão de débito
cartao de débito
cartão de debito
cartao de debito
débito automático
debito automatico
número do cartão
numero do cartão
número do cartao
numero do cartao
número de cartão
numero de cartão
número de cartao
numero de cartao
nº do cartão
nº do cartao
nº. do cartão
no do cartão
no do cartao
no. do cartão
no. do cartao
クレジットカード番号
クレジットカードナンバー
クレジットカード＃
クレジットカード
クレジット
クレカ
カード番号
カードナンバー
カード＃
アメックス
アメリカンエクスプレス
アメリカン エクスプレス
Visaカード
Visa カード
マスターカード
マスター カード
マスター
ダイナースクラブ
ダイナース クラブ
ダイナース
有効期限
期限
キャッシュカード
キャッシュ カード
カード名義人
カードの名義人
カードの名義
デビット カード
デビットカード
```

## <a name="croatia-drivers-license-number"></a>Číslo licence ovladače Chorvatska

Tato entita typu citlivých informací je k dispozici pouze v typu s citlivými informacemi o licenčním čísle ovladače Evropské unie.

### <a name="format"></a>Formát

osm číslic bez mezer a oddělovačů

### <a name="pattern"></a>Vzor

osm číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keywords_eu_drivers_license_number"></a>Klíčová slova \_ \_ pro \_ licenční \_ číslo ovladače EU

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_croatia_eu_drivers_license_number"></a>Klíčová slova \_ chorvatská \_ \_ licence ovladače Evropské unie \_ \_

```
vozačka dozvola
vozačke dozvole
```

## <a name="croatia-identity-card-number"></a>Chorvatsko číslo karty identity

Tato entita typu citlivých informací je součástí typu důvěrné informace o národním identifikačním čísle EU a je dostupná jako entita typu samostatné citlivé informace.

### <a name="format"></a>Formát

devět číslic

### <a name="pattern"></a>Vzor

devět po sobě jdoucích číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_croatia_id_card"></a>\_Karta pro Chorvatsko \_ ID \_ pro klíčové slovo

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="croatia-personal-identification-oib-number"></a>Osobní identifikační číslo (OIB) Chorvatsku

### <a name="format"></a>Formát

11 číslic

### <a name="pattern"></a>Vzor

11 číslic:

- 10 číslic
- Koncová číslice je kontrolní číslice.

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_croatia_oib_number"></a>\_ \_ OIB \_ číslo pro klíčové slovo Chorvatsko

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax Id number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="denmark-personal-identification-number"></a>Dánsko – osobní identifikační číslo

### <a name="format"></a>Formát

10 číslic obsahujících spojovník

### <a name="pattern"></a>Vzor

10 číslic:

- šest číslic ve formátu DDMMYY, což jsou datum narození.
- spojovník
- čtyři číslice, kde je koncová číslice kontrolní číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_denmark_id"></a>ID pro klíčové slovo \_ Dánsko \_

```
centrale person register
civilt registreringssystem
cpr
cpr#
gesundheitskarte nummer
gesundheitsversicherungkarte nummer
health card
health insurance card number
health insurance number
identification number
identifikationsnummer
identifikationsnummer#
identity number
krankenkassennummer
national ID#
national number#
national number
personalidnumber#
personalidentityno#
personal ID number
personnummer
personnummer#
reisekrankenversicherungskartenummer
rejsesygesikringskort
ssn
ssn#
skat ID
skat kode
skat nummer
skattenummer
social security number
sundhedsforsikringskort
sundhedsforsikringsnummer
sundhedskort
sundhedskortnummer
sygesikring
sygesikringkortnummer
tax code
travel health insurance card
uniqueidentityno#
tax number
tax registration number
tax ID
tax identification number
tax ID#
tax number#
tax no
tax no#
tax number
tax identification no
tin#
tax ID no#
tax ID number#
tax no#
tin ID
tin no
cpr.nr
cprnr
cprnummer
personnr
person register
sygesikringsbevis
sygesikringsbevisnr
sygesikringsbevisnummer
sygesikringskort
sygesikringskortnr
sygesikringskortnummer
sygesikringsnr
sygesikringsnummer
```

## <a name="eu-debit-card-number"></a>Číslo debetní karty v EU

### <a name="format"></a>Formát

16 číslic

### <a name="pattern"></a>Vzor

Složitý a robustní vzor

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_eu_debit_card"></a>Klíčové slovo – \_ \_ debetní \_ karta EU

```
account number
card number
card no.
security number
cc#
```

#### <a name="keyword_card_terms_dict"></a>Výrazy na kartě klíčového slova \_ \_ \_ dict –

```
acct nbr
acct num
acct no
Americans express
Americans express
americano espresso
amex
atm card
atm cards
atm kaart
atmcard
atmcards
atmkaart
atmkaarten
ban contact
bank card
bankkaart
card holder
card holders
card num
card number
card numbers
card type
cardano numerico
cardholder
cardholders
card number
card numbers
carta bianca
carta credito
carta di credito
cartao de credito
cartao de crédito
cartao de debito
cartao de débito
carte bancaire
carte blanche
carte bleue
carte de credit
carte de crédit
carte di credito
carteblanche
cartão de credito
cartão de crédito
cartão de debito
cartão de débito
cb
ccn
check card
check cards
check card
check cards
chequekaart
cirrus
cirrus-edc-maestro
controlekaart
controlekaarten
credit card
credit cards
credit card
credit cards
debetkaart
debetkaarten
debit card
debit cards
debit card
debit cards
debito automatico
diners club
diners club
discover
discover card
discover cards
discover card
discover cards
débito automático
edc
eigentümername
european debit card
hoofdkaart
hoofdkaarten
in viaggio
japanese card bureau
japanse kaartdienst
jcb
kaart
kaart num
kaartaantal
kaartaantallen
kaarthouder
kaarthouders
karte
karteninhaber
karteninhabers
kartennr
kartennummer
kreditkarte
kreditkarten-nummer
kreditkarteninhaber
kreditkarteninstitut
kreditkartennummer
kreditkartentyp
maestro
master card
master cards
Mastercard
master cards
mc
mister cash
n carta
carta
no de tarjeta
no do cartao
no do cartão
no. de tarjeta
no. do cartao
no. do cartão
nr carta
nr. carta
numeri di scheda
numero carta
numero de cartao
numero de carte
numero de cartão
numero de tarjeta
numero della carta
numero di carta
numero di scheda
numero do cartao
numero do cartão
numéro de carte
nº carta
nº de carte
nº de la carte
nº de tarjeta
nº do cartao
nº do cartão
nº. do cartão
número de cartao
número de cartão
número de tarjeta
número do cartao
scheda dell'assegno
scheda dell'atmosfera
scheda dell'atmosfera
scheda della banca
scheda di controllo
scheda di debito
scheda matrice
schede dell'atmosfera
schede di controllo
schede di debito
schede matrici
scoprono la scheda
scoprono le schede
solo
supporti di scheda
supporto di scheda
switch
tarjeta atm
tarjeta credito
tarjeta de atm
tarjeta de credito
tarjeta de debito
tarjeta debito
tarjeta no
tarjetahabiente
tipo della scheda
ufficio giapponese della
scheda
v pay
v-pay
visa
visa plus
visa electron
visto
visum
vpay
```

#### <a name="keyword_card_security_terms_dict"></a>Výrazy zabezpečení na kartě klíčového slova \_ \_ \_ \_ dict –

```
card identification number
card verification
cardi la verifica
cid
cod seg
cod seguranca
cod segurança
cod sicurezza
cod. seg
cod. seguranca
cod. segurança
cod. sicurezza
codice di sicurezza
codice di verifica
codigo
codigo de seguranca
codigo de segurança
crittogramma
cryptogram
cryptogramme
cv2
cvc
cvc2
cvn
cvv
cvv2
cód seguranca
cód segurança
cód. seguranca
cód. segurança
código
código de seguranca
código de segurança
de kaart controle
geeft nr uit
issue no
issue number
kaartidentificatienummer
kreditkartenprufnummer
kreditkartenprüfnummer
kwestieaantal
no. dell'edizione
no. di sicurezza
numero de securite
numero de verificacao
numero dell'edizione
numero di identificazione della
scheda
numero di sicurezza
numero van veiligheid
numéro de sécurité
nº autorizzazione
número de verificação
perno il blocco
pin block
prufziffer
prüfziffer
security code
security no
security number
sicherheits kode
sicherheitscode
sicherheitsnummer
speldblok
veiligheid nr
veiligheidsaantal
veiligheidscode
veiligheidsnummer
verfalldatum
```

#### <a name="keyword_card_expiration_terms_dict"></a>\_ \_ Podmínka vypršení platnosti karty klíčového slova \_ \_ dict –

```
ablauf
data de expiracao
data de expiração
data del exp
data di exp
data di scadenza
data em que expira
data scad
data scadenza
date de validité
datum afloop
datum van exp
de afloop
espira
espira
exp date
exp datum
expiration
expire
expires
expiry
fecha de expiracion
fecha de venc
gultig bis
gultigkeitsdatum
gültig bis
gültigkeitsdatum
la scadenza
scadenza
valable
validade
valido hasta
valor
venc
vencimento
vencimiento
verloopt
vervaldag
vervaldatum
vto
válido hasta
```

## <a name="eu-drivers-license-number"></a>Číslo licence ovladače EU

Jedná se o entity v typu citlivých informací o licenci ovladače EU.

- Rakousko
- Belgie
- Bulharsko
- Chorvatsko
- Kypr
- Čeština
- Dánsko
- Estonsko
- Finsko
- Francie
- Německo
- Řecko
- Maďarsko
- Irsko
- Itálie
- Lotyšsko
- Litva
- Lucembursko
- Malta
- Nizozemsko
- Polsko
- Portugalsko
- Rumunsko
- Slovensko
- Slovinsko
- Španělsko
- Švédsko
- britské

## <a name="eu-national-identification-number"></a>Národní identifikační číslo v EU

Jedná se o entity v národním identifikačním čísle EU typ s citlivými informacemi.

- Rakousko
- Belgie
- Bulharsko
- Chorvatsko
- Kypr
- Čeština
- Dánsko
- Estonsko
- Finsko
- Francie
- Německo
- Řecko
- Maďarsko
- Irsko
- Itálie
- Lotyšsko
- Litva
- Lucembursko
- Malta
- Nizozemsko
- Polsko
- Portugalsko
- Rumunsko
- Slovensko
- Slovinsko
- Španělsko
- britské

## <a name="eu-passport-number"></a>Číslo Passportu EU

Jedná se o entity v rámci EU Number důvěrné informace typeThese jsou entity v rámci sady čísel Passport v EU.

- Rakousko
- Belgie
- Bulharsko
- Chorvatsko
- Kypr
- Čeština
- Dánsko
- Estonsko
- Finsko
- Francie
- Německo
- Řecko
- Maďarsko
- Irsko
- Itálie
- Lotyšsko
- Litva
- Lucembursko
- Malta
- Nizozemsko
- Polsko
- Portugalsko
- Rumunsko
- Slovensko
- Slovinsko
- Španělsko
- Švédsko
- britské

## <a name="eu-social-security-number-or-equivalent-identification"></a>Číslo sociálního pojištění (EU) nebo ekvivalentní identifikace

Jedná se o entity, které jsou v rodném čísle v rámci Evropské unie nebo v ekvivalentních typech citlivých informací o identifikaci.

- Rakousko
- Belgie
- Chorvatsko
- Čeština
- Dánsko
- Finsko
- Francie
- Německo
- Řecko
- Maďarsko
- Portugalsko
- Španělsko
- Švédsko

## <a name="eu-tax-identification-number"></a>Daňové identifikační číslo v EU

Tyto entity jsou v typu citlivých informací o daňovém identifikačním čísle EU.

- Rakousko
- Belgie
- Bulharsko
- Chorvatsko
- Kypr
- Čeština
- Dánsko
- Estonsko
- Finsko
- Francie
- Německo
- Řecko
- Maďarsko
- Irsko
- Itálie
- Lotyšsko
- Litva
- Lucembursko
- Malta
- Nizozemsko
- Polsko
- Portugalsko
- Rumunsko
- Slovensko
- Slovinsko
- Španělsko
- Švédsko
- britské



## <a name="finland-national-id"></a>Národní ID Finska

### <a name="format"></a>Formát

šest číslic plus znak, který označuje století plus tři číslice plus kontrolní číslice

### <a name="pattern"></a>Vzor

Vzor musí zahrnovat všechny následující:

- šest číslic ve formátu DDMMYY, což jsou datum narození.
- století – značka (-, + nebo a)
- osobní identifikační číslo se třemi číslicemi
- číslice nebo písmeno (nerozlišuje velikost písmen), což je kontrolní číslice

### <a name="keywords"></a>Klíčová slova

```
ainutlaatuinen henkilökohtainen tunnus
henkilökohtainen tunnus
henkilötunnus
henkilötunnusnumero#
henkilötunnusnumero
hetu
ID no
ID number
identification number
identiteetti numero
identity number
ID number
kansallinen henkilötunnus
kansallisen henkilökortin
national ID card
national ID no.
personal ID
personal identity code
personalidnumber#
personbeteckning
personnummer
social security number
sosiaaliturvatunnus
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
tunnistenumero
tunnus numero
tunnusluku
tunnusnumero
verokortti
veronumero
verotunniste
verotunnus
```

## <a name="finland-passport-number"></a>Číslo Finska služby Passport

Tato entita typu citlivých informací je k dispozici v rámci EU typu s důvěrnými informacemi o číslech a je dostupná jako entita typu samostatné citlivé informace.

### <a name="format"></a>Formát

kombinace devíti písmen a číslic

### <a name="pattern"></a>Vzor

kombinace devíti písmen a číslic:

- dvě písmena (nerozlišují se malá a velká písmena)
- sedm číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keywords_eu_passport_number_common"></a>Klíčová slova \_ \_ Common Passport – \_ \_ společné číslo

```
passport#
passport #
passport ID
passports
passport no
passport no
passport number
passport number
passport numbers
passport numbers
```

#### <a name="keyword_finland_passport_number"></a>Klíčové slovo \_ Finsko – \_ číslo služby Passport \_

```
suomalainen passi
passin numero
passin numero.#
passin numero#
passin numero.
passi#
passi number
```

## <a name="france-drivers-license-number"></a>Číslo licence ovladače Francie

Tato entita typu citlivých informací je k dispozici v typu s citlivými informacemi o licenčním čísle ovladače EU a je k dispozici jako entita typu samostatné citlivé informace.

### <a name="format"></a>Formát

12 číslic

### <a name="pattern"></a>Vzor

12 číslic s ověřením pro zlevnění podobných vzorů, jako jsou Francouzská telefonní čísla

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_french_drivers_license"></a>Licence pro klíčová slova \_ Francouzská verze \_ \_

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
permis de conduire
license number
license number
license numbers
license numbers
numéros de license
```

## <a name="france-national-id-card-cni"></a>Karta National ID Francie (CNI)

### <a name="format"></a>Formát

12 číslic

### <a name="pattern"></a>Vzor

12 číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keywords_france_eu_national_id_card"></a>Klíčová slova \_ Francie \_ Evropské \_ \_ karty National ID \_

```
card number
carte nationale d'identité
carte nationale d'idenite no
cni#
cni
compte bancaire
national identification number
national identity
nationalidno#
numéro d'assurance maladie
numéro de carte vitale
```

## <a name="france-passport-number"></a>Francouzský pas číslo

Tato entita typu citlivých informací je k dispozici v rámci EU typu s důvěrnými informacemi o číslech a je dostupná jako entita typu samostatné citlivé informace.

### <a name="format"></a>Formát

devět číslic a písmen

### <a name="pattern"></a>Vzor

devět číslic a písmen:

- dvě číslice
- dvě písmena (nerozlišují se malá a velká písmena)
- pět číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_passport"></a>Klíčové slovo \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="france-social-security-number-insee-or-equivalent-identification"></a>Francouzský rodné číslo (INSEE) nebo ekvivalentní identifikace

Tato entita typu citlivých informací je zahrnutá v čísle sociálního pojištění EU a ekvivalentním typu informací o citlivých ID a je k dispozici jako entita typu samostatné citlivé informace.

### <a name="format"></a>Formát

15 číslic

### <a name="pattern"></a>Vzor

Musí odpovídat jednomu ze dvou vzorů:

- 13 číslic následovaný mezerou a dvěma číslicemi nebo
- 15 po sobě jdoucích číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_fr_insee"></a>Klíčové slovo \_ fr \_ INSEE

```
insee
securité sociale
securite sociale
national ID
national identification
numéro d'identité
no d'identité
no. d'identité
numero d'identite
no d'identite
no. d'identite
social security number
social security code
social insurance number
le numéro d'identification nationale
d'identité nationale
numéro de sécurité sociale
le code de la sécurité sociale
numéro d'assurance sociale
numéro de sécu
code sécu
```

## <a name="germany-drivers-license-number"></a>Číslo licence ovladače pro Německo

Tato entita typu citlivých informací je součástí typu s citlivými informacemi z ovladače EU a je dostupná jako entita typu samostatné citlivé informace.

### <a name="format"></a>Formát

kombinace 11 číslic a písmen

### <a name="pattern"></a>Vzor

11 číslic a písmen (nerozlišuje velká a malá písmena):

- číslice nebo písmeno
- dvě číslice
- šest číslic nebo písmen
- číslice
- číslice nebo písmeno

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_german_drivers_license_number"></a>\_ \_ Číslo licence pro ovladače pro německé klíčové \_ slovo \_

```
ausstellungsdatum
ausstellungsort
ausstellende behöde
ausstellende behorde
ausstellende behoerde
führerschein
fuhrerschein
fuehrerschein
führerscheinnummer
fuhrerscheinnummer
fuehrerscheinnummer
führerschein-
fuhrerschein-
fuehrerschein-
führerscheinnummernr
fuhrerscheinnummernr
fuehrerscheinnummernr
führerscheinnummerklasse
fuhrerscheinnummerklasse
fuehrerscheinnummerklasse
nr-führerschein
nr-fuhrerschein
nr-fuehrerschein
no-führerschein
no-fuhrerschein
no-fuehrerschein
n-führerschein
n-fuhrerschein
n-fuehrerschein
permis de conduire
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dlno
```

## <a name="germany-identity-card-number"></a>Číslo karty identity v Německu

### <a name="format"></a>Formát

od 1. listopadu 2010: devět písmen a číslic

od 1. dubna 1987 do 31. října 2010:10 číslic

### <a name="pattern"></a>Vzor

od 1. listopadu 2010:

- jedno písmeno (nerozlišuje malá a velká písmena)
- osm číslic

od 1. dubna 1987 do 31. října 2010:

- 10 číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_germany_id_card"></a>\_ \_ Karta ID pro Německo-klíčové slovo \_

```
ausweis
gpid
identification
identifikation
identifizierungsnummer
identity card
identity number
id-nummer
personal ID
personalausweis
persönliche ID nummer
persönliche identifikationsnummer
persönliche-id-nummer
```

## <a name="germany-passport-number"></a>Číslo německé služby Passport

Tato entita typu citlivých informací je zahrnutá v poli typ důvěrné informace o číslech služby Passport pro EU a je dostupná jako entita typu samostatné citlivé informace.

### <a name="format"></a>Formát

10 číslic nebo písmen

### <a name="pattern"></a>Vzor

Vzor musí zahrnovat všechny následující:

- první znak je číslice nebo písmeno z této sady (C, F, G, H, J, K)
- tři číslice
- pět číslic nebo písmen z této sady (C,-H, J-N, P, R, T, V-Z)
- číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_german_passport"></a>Klíčové slovo \_ německé \_ služby Passport

```
reisepasse
reisepassnummer
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
passeport no.
passeport no
```

## <a name="greece-national-id-card"></a>Karta National ID Řecka

### <a name="format"></a>Formát

Kombinace 7-8 písmen a číslic a pomlčky

### <a name="pattern"></a>Vzor

Sedm písmen a číslic (starý formát):

- Jedno písmeno (libovolné písmeno řecké abecedy)
- Pomlčka
- Šest číslic

Osm písmen a číslic (nový formát):

- Dvě písmena, jejichž velká písmena se vyskytují v řeckém i abecedním znaku (ABEZHIKMNOPTYX)
- Pomlčka
- Šest číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_greece_id_card"></a>Karta pro identifikaci klíčového slova \_ Řecku \_ \_

```
greek ID
greek national ID
greek personal ID card
greek police ID
identity card
tautotita
ταυτότητα
ταυτότητας
```

## <a name="hong-kong-identity-card-hkid-number"></a>Číslo HKID (Hongkong – zvláštní identita)

### <a name="format"></a>Formát

Kombinace 8-9 písmen a číslic a volitelných závorek kolem posledního znaku

### <a name="pattern"></a>Vzor

Kombinace 8-9 písmen:

- 1-2 písmen (nerozlišuje malá a velká písmena)
- Šest číslic
- Konečný znak (jakákoli číslice nebo písmeno A), což je kontrolní číslice a volitelně je uzavřen v závorkách.

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_hong_kong_id_card"></a>Klíčové slovo – Hongkong – zvláštní \_ \_ \_ ID \_

```
hkid
Hong Kong identity card
HKIDC
ID card
identity card
hk identity card
Hong Kong ID
香港身份證
香港永久性居民身份證
身份證
身份証
身分證
身分証
香港身份証
香港身分證
香港身分証
香港身份證
香港居民身份證
香港居民身份証
香港居民身分證
香港居民身分証
香港永久性居民身份証
香港永久性居民身分證
香港永久性居民身分証
香港永久性居民身份證
香港非永久性居民身份證
香港非永久性居民身份証
香港非永久性居民身分證
香港非永久性居民身分証
香港特別行政區永久性居民身份證
香港特別行政區永久性居民身份証
香港特別行政區永久性居民身分證
香港特別行政區永久性居民身分証
香港特別行政區非永久性居民身份證
香港特別行政區非永久性居民身份証
香港特別行政區非永久性居民身分證
香港特別行政區非永久性居民身分証
```

## <a name="ip-address"></a>IP adresa

### <a name="format"></a>Formát

#### <a name="ipv4"></a>IPv4

Komplexní vzor, který má formátované (tečky) a neformátované (bez teček) verze IPv4 adres

#### <a name="ipv6"></a>Protokolů

Komplexní vzor účtů pro formátovaná čísla IPv6 (která zahrnují dvojtečky)

### <a name="pattern"></a>Vzor

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_ipaddress"></a>IPAddress – klíčové slovo \_

```
IP (this keyword is case-sensitive)
ip address
ip addresses
internet protocol
IP-כתובת ה
```

## <a name="india-permanent-account-number-pan"></a>Indie – trvalé číslo účtu (PAN)

### <a name="format"></a>Formát

10 písmen nebo číslic

### <a name="pattern"></a>Vzor

10 písmen nebo číslic:

- Tři písmena (nerozlišují se malá a velká písmena)
- Písmeno v C, P, H, F, A, T, B, L, J, G (nerozlišuje malá a velká písmena)
- Písmeno
- Čtyři číslice
- Písmeno (nerozlišuje malá a velká písmena)

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_india_permanent_account_number"></a>Klíčové slovo \_ Indie – \_ trvalé \_ \_ číslo účtu

```
Permanent Account Number
PAN
```

## <a name="india-unique-identification-aadhaar-number"></a>Jedinečné identifikační číslo Indie (Aadhaar)

### <a name="format"></a>Formát

12 číslic, které obsahují volitelné mezery nebo pomlčky

### <a name="pattern"></a>Vzor

12 číslic:

- Číslice, která není 0 nebo 1.
- Tři číslice
- Volitelný mezera nebo pomlčka
- Čtyři číslice
- Volitelný mezera nebo pomlčka
- Poslední číslice, což je kontrolní číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_india_aadhar"></a>Klíčové slovo \_ Indie \_ aadhar

```
aadhaar
aadhar
aadhar#
uid
आधार
uidai
```

## <a name="indonesia-identity-card-ktp-number"></a>KTP (Indonésie identity Card) – číslo

### <a name="format"></a>Formát

16 číslic, které obsahují volitelné tečky

### <a name="pattern"></a>Vzor

16 číslic:

- Kód provincie se dvěma číslicemi
- Tečka (volitelné)
- Dvoumístný Regency nebo kód města
- Kód podoblasti se dvěma číslicemi
- Tečka (volitelné)
- Šest číslic ve formátu DDMMYY, což jsou datum narození.
- Tečka (volitelné)
- Čtyři číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_indonesia_id_card"></a>\_ \_ Karta ID pro klíčová slova Indonésie \_

```
KTP
Kartu Tanda Penduduk
Nomor Induk Kependudukan
```

## <a name="international-banking-account-number-iban"></a>Mezinárodní číslo internetového účtu (IBAN)

### <a name="format"></a>Formát

Kód země (dvě písmena) plus kontrolní číslice (dvě číslice) plus :::no-loc text="bban"::: číslo (až 30 znaků)

### <a name="pattern"></a>Vzor

Vzor musí zahrnovat všechny následující:

- Kód země se dvěma písmeny
- Dvě kontrolní číslice (za kterými následuje volitelné místo)
- 1-7 skupin se čtyřmi písmeny a číslicemi (můžou být oddělené mezerami)
- 1-3 písmen nebo číslic

Formát každé země se mírně liší. Typ citlivých informací IBAN pokrývá tyto země 60:

```
ad, ae, al, at, az, ba, be, bg, bh, ch, cr, cy, cz, de, dk, do, ee, es, fi, fo, fr, gb, ge, gi, gl, gr, hr, hu, that is, il, is, it, kw, kz, lb, li, lt, lu, lv, mc, md, me, mk, mr, mt, mu, nl, no, pl, pt, ro, rs, sa, se, si, sk, sm, tn, tr, vg
```

### <a name="keywords"></a>Klíčová slova

Žádné

## <a name="ireland-personal-public-service-pps-number"></a>Číslo pro irské osobní veřejné služby (PPS)

### <a name="format"></a>Formát

Starý formát (do 31. prosince 2012):

- sedm číslic následovaných 1-2 písmeny

Nový formát (1. ledna 2013 a pozdější):

- sedm číslic následovaných dvěma písmeny

### <a name="pattern"></a>Vzor

Starý formát (do 31. prosince 2012):

- sedm číslic
- jedno až dvě písmena (nerozlišuje velká a malá písmena)

Nový formát (1. ledna 2013 a pozdější):

- sedm číslic
- písmeno (nerozlišuje malá a velká písmena), což je abecední znaková kontrolní číslice
- Volitelné písmeno v rozsahu A – I nebo &quot; W&quot;

### <a name="keywords"></a>Klíčová slova

#### <a name="keywords_ireland_eu_national_id_card"></a>Klíčová slova \_ Irsko \_ EU – \_ \_ karta National ID \_

```
client identity service
identification number
personal ID number
personal public service number
personal service no
phearsanta seirbhíse poiblí
pps no
pps number
pps num
pps service no
ppsn
ppsno#
ppsno
psp
public service no
publicserviceno#
publicserviceno
revenue and social insurance number
rsi no
rsi number
rsin
seirbhís aitheantais cliant
uimh
uimhir aitheantais chánach
uimhir aitheantais phearsanta
uimhir phearsanta seirbhíse poiblí
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="israel-bank-account-number"></a>Číslo bankovního účtu Izraele

### <a name="format"></a>Formát

13 číslic

### <a name="pattern"></a>Vzor

Formátu

- dvě číslice
- pomlčka
- tři číslice
- pomlčka
- osm číslic

Neformátovaný tvar:

- 13 po sobě jdoucích číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_israel_bank_account_number"></a>Klíčové \_ slovo \_ Izrael \_ číslo bankovního účtu \_

```
Bank Account Number
Bank Account
Account Number
מספר חשבון בנק
```

## <a name="israel-national-identification-number"></a>Izrael – národní identifikační číslo

### <a name="format"></a>Formát

devět číslic

### <a name="pattern"></a>Vzor

devět po sobě jdoucích číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_israel_national_id"></a>Klíčové slovo \_ Izrael – \_ národní \_ ID

```
מספר זהות
מספר זיה וי
מספר זיהוי ישר אלי
זהותישר אלית
هو ية اسرائيل ية عدد
هوية إسرائ يلية
رقم الهوية
عدد هوية فريدة من نوعها
ID number#
ID number
identity no
identity number#
identity number
israeliidentitynumber
personal ID
unique ID
```

## <a name="italy-drivers-license-number"></a>Číslo licence ovladače Itálie

Tato entita typu citlivých informací je součástí typu s citlivými informacemi z ovladače EU a je dostupná jako entita typu samostatné citlivé informace.

### <a name="format"></a>Formát

kombinace 10 písmen a číslic

### <a name="pattern"></a>Vzor

kombinace 10 písmen a číslic:

- jedno písmeno (nerozlišuje malá a velká písmena)
- písmeno &quot; a &quot; nebo &quot; V &quot; (nerozlišuje malá a velká písmena)
- sedm číslic
- jedno písmeno (nerozlišuje malá a velká písmena)

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_italy_drivers_license_number"></a>Klíčové \_ slovo \_ \_ licenční \_ číslo ovladačů pro Itálii

```
numero di patente
patente di guida
patente guida
patenti di guida
patenti guida
```

## <a name="japan-bank-account-number"></a>Číslo bankovního účtu Japonska

### <a name="format"></a>Formát

sedm nebo osm číslic

### <a name="pattern"></a>Vzor

číslo bankovního účtu:

- sedm nebo osm číslic
- kód větve bankovních účtů:
- čtyři číslice
- mezera nebo pomlčka (volitelné)
- tři číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_jp_bank_account"></a>Klíčové slovo \_ JP \_ bankovní \_ účet

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
口座番号
銀行口座
銀行口座番号
総合口座
普通預金口座
普通口座
当座預金口座
当座口座
預金口座
振替口座
銀行
バンク
```

#### <a name="keyword_jp_bank_branch_code"></a>Klíčové slovo \_ JP \_ bank – \_ \_ kód větve

```
支店番号
支店コード
店番号
```

## <a name="japan-drivers-license-number"></a>Číslo licence ovladače Japonska

### <a name="format"></a>Formát

12 číslic

### <a name="pattern"></a>Vzor

12 po sobě jdoucích číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_jp_drivers_license_number"></a>Klíčové slovo \_ JP \_ Drivers \_ License \_ Number

```
driver license
drivers license
driver'license
drivers licenses
driver'licenses
driver licenses
dl#
dls#
lic#
lics#
運転免許証
運転免許
免許証
免許
運転免許証番号
運転免許番号
免許証番号
免許番号
運転免許証ナンバー
運転免許ナンバー
免許証ナンバー
運転免許証no
運転免許no
免許証no
免許no
運転経歴証明書番号
運転経歴証明書
運転免許証No.
運転免許No.
免許証No.
免許No.
運転免許証#
運転免許#
免許証#
免許#
```

## <a name="japan-passport-number"></a>Japonské číslo služby Passport

### <a name="format"></a>Formát

dvě písmena následovaná sedmi číslicemi

### <a name="pattern"></a>Vzor

dvě písmena (nerozlišuje velká a malá písmena) následovaná sedmi číslicemi

#### <a name="keyword_jp_passport"></a>Klíčové slovo \_ JP \_ Passport

```
Passport
Passport Number
Passport No.
Passport #
パスポート
パスポート番号
パスポートナンバー
パスポート＃
パスポート#
パスポートNo.
旅券番号
旅券番号＃
旅券番号♯
旅券ナンバー
```

## <a name="japan-residence-card-number"></a>Číslo karty pro pobyt z Japonska

### <a name="format"></a>Formát

12 písmen a číslic

### <a name="pattern"></a>Vzor

12 písmen a číslic:

- dvě písmena (nerozlišují se malá a velká písmena)
- osm číslic
- dvě písmena (nerozlišují se malá a velká písmena)

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_jp_residence_card_number"></a>Klíčové slovo \_ JP \_ pobytu – \_ \_ číslo karty

```
Residence card number
Residence card no
Residence card #
在留カード番号
在留カード
在留番号
```

## <a name="japan-resident-registration-number"></a>Japonsko – registrační číslo rezidenta

### <a name="format"></a>Formát

11 číslic

### <a name="pattern"></a>Vzor

11 po sobě jdoucích číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_jp_resident_registration_number"></a>Klíčové slovo \_ JP \_ rezidentního \_ registračního \_ čísla

```
Resident Registration Number
Residents Basic Registry Number
Resident Registration No.
Resident Register No.
Residents Basic Registry No.
Basic Resident Register No.
外国人登録証明書番号
証明書番号
登録番号
外国人登録証
```

## <a name="japan-social-insurance-number-sin"></a>Číslo sociálního pojištění Japonska (SIN)

### <a name="format"></a>Formát

7-12 číslic

### <a name="pattern"></a>Vzor

7-12 číslic:

- čtyři číslice
- spojovník (volitelné)
- šest číslic nebo
- 7-12 po sobě jdoucích číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_jp_sin"></a>Klíčové slovo \_ JP \_ Sin

```
Social Insurance No.
Social Insurance Num
Social Insurance Number
健康保険被保険者番号
健保番号
基礎年金番号
雇用保険被保険者番号
雇用保険番号
保険証番号
社会保険番号
社会保険No.
社会保険
介護保険
介護保険被保険者番号
健康保険被保険者整理番号
雇用保険被保険者整理番号
厚生年金
厚生年金被保険者整理番号
```

## <a name="malaysia-identification-card-number"></a>Identifikační číslo karty Malajsie

### <a name="format"></a>Formát

12 číslic obsahujících volitelné spojovníky

### <a name="pattern"></a>Vzor

12 číslic:

- šest číslic ve formátu rrmmdd, což jsou datum narození.
- pomlčka (nepovinná)
- rodné číslo na místě
- pomlčka (nepovinná)
- tři náhodné číslice
- Jednociferné číslo pohlaví

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_malaysia_id_card_number"></a>Klíčové \_ slovo \_ ID Malajsie – \_ \_ číslo karty

```
digital application card
i/c
i/c no
ic
ic no
ID card
identification Card
identity card
k/p
k/p no
kad akuan diri
kad aplikasi digital
kad pengenalan malaysia
kp
kp no
mykad
mykas
mykid
mypr
mytentera
malaysia identity card
malaysian identity card
nric
personal identification card
```

## <a name="netherlands-citizens-service-bsn-number"></a>Číslo služby (BSN) nizozemského občana

### <a name="format"></a>Formát

osm až devět číslic, které obsahují volitelné mezery

### <a name="pattern"></a>Vzor

osm – devět číslic:

- tři číslice
- mezera (volitelné)
- tři číslice
- mezera (volitelné)
- dvě tři číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keywords_netherlands_eu_national_id_card"></a>Klíčová slova \_ nizozemské \_ EU – \_ \_ karta National ID \_

```
bsn#
bsn
burgerservicenummer
citizen service number
person number
personal number
personal numeric code
person-related number
persoonlijk nummer
persoonlijke numerieke code
persoonsgebonden
persoonsnummer
sociaal-fiscaal nummer
social-fiscal number
sofi
sofinummer
uniek identificatienummer
uniek identiteitsnummer
unique identification number
unique identity number
uniqueidentityno#
```

## <a name="new-zealand-ministry-of-health-number"></a>Nové ministerstvo, které má číslo zdravotního Zélandu

### <a name="format"></a>Formát

tři písmena, mezera (volitelné) a čtyři číslice

### <a name="pattern"></a>Vzor

- tři písmena (bez rozlišení velkých a malých písmen), s výjimkou písmen I a O
- mezera (volitelné)
- čtyři číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_nz_terms"></a>Klíčové slovo \_ NZ – \_ výrazy

```
NHI
New Zealand
Health
treatment
National Health Index Number
nhi number
nhi no.
NHI#
National Health Index No.
National Health Index Id
National Health Index #
```

## <a name="norway-identification-number"></a>Identifikační číslo Norska

### <a name="format"></a>Formát

11 číslic

### <a name="pattern"></a>Vzor

11 číslic:

- šest číslic ve formátu DDMMYY, což jsou datum narození.
- samostatné číslo pro jednotlivé číslice
- dvě kontrolní číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_norway_id_number"></a>\_Číslo ID norského klíčového slova \_ \_

```
Personal identification number
Norwegian ID Number
ID Number
Identification
Personnummer
Fødselsnummer
```

## <a name="philippines-unified-multi-purpose-identification-number"></a>Jednotně sjednocené identifikační číslo pro Filipíny

### <a name="format"></a>Formát

12 číslic oddělených pomlčkami

### <a name="pattern"></a>Vzor

12 číslic:

- čtyři číslice
- spojovník
- sedm číslic
- spojovník
- jedna číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_philippines_id"></a>Klíčové slovo \_ Filipíny \_ ID

```
Unified Multi-Purpose ID
UMID
Identity Card
Pinag-isang Multi-Layunin ID
```

## <a name="poland-identity-card"></a>Karta identity Polska

### <a name="format"></a>Formát

tři písmena a šest číslic

### <a name="pattern"></a>Vzor

tři písmena (nerozlišují se malá a velká písmena) následovaná šesti číslicemi

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_poland_national_id_passport_number"></a>Klíčové slovo \_ Polsko \_ National \_ ID \_ pas \_ číslo

```
Dowód osobisty
Numer dowodu osobistego
Nazwa i numer dowodu osobistego
Nazwa i nr dowodu osobistego
Nazwa i nr dowodu tożsamości
Dowód Tożsamości
dow. os.
```

## <a name="poland-national-id-pesel"></a>Mezinárodní ID Polska (PESEL)

### <a name="format"></a>Formát

11 číslic

### <a name="pattern"></a>Vzor

- 6 číslic představující datum narození ve formátu rrmmdd
- 4 číslice
- 1 kontrolní číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_pesel_identification_number"></a>\_PESEL \_ identifikační \_ číslo pro klíčové slovo

```
dowód osobisty
dowódosobisty
niepowtarzalny numer
niepowtarzalnynumer
nr.-pesel
nr-pesel
numer identyfikacyjny
pesel
tożsamości narodowej
```

## <a name="poland-passport-number"></a>Číslo Polska Passport

Tato entita typu citlivých informací je zahrnutá v poli typ důvěrné informace o číslech služby Passport pro EU a je dostupná jako entita typu samostatné citlivé informace.

### <a name="format"></a>Formát

dvě písmena a sedm číslic

### <a name="pattern"></a>Vzor

Dvě písmena (nerozlišuje velká a malá písmena) následovaná sedmi číslicemi

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_poland_national_id_passport_number"></a>Klíčové slovo \_ Polsko \_ National \_ ID \_ pas \_ číslo

```
Numer paszportu
Nr. Paszportu
Paszport
```

## <a name="portugal-citizen-card-number"></a>Číslo karty občanů v Portugalsku

### <a name="format"></a>Formát

osm číslic

### <a name="pattern"></a>Vzor

osm číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_portugal_citizen_card"></a>\_ \_ Karta občana Portugalska pro Portugalsko \_

```
bilhete de identidade
cartão de cidadão
citizen card
document number
documento de identificação
ID number
identification no
identification number
identity card no
identity card number
national ID card
nic
número bi de portugal
número de identificação civil
número de identificação fiscal
número do documento
portugal bi number
```

## <a name="portugal-drivers-license-number"></a>Číslo licence ovladače Portugalska

Tato entita typu citlivých informací je k dispozici pouze v typu s citlivými informacemi o licenčním čísle ovladače Evropské unie.

### <a name="format"></a>Formát

dva vzory – dvě písmena následovaná 5-8 číslicemi se speciálními znaky

### <a name="pattern"></a>Vzor

Vzor 1: dvě písmena následovaná 5/6 se speciálními znaky:

- Dvě písmena (bez rozlišení velkých a malých písmen)
- Pomlčka
- Pět nebo šest číslic
- Mezera
- Jedna číslice

Vzor 2: jedno písmeno následované 6/8 číslicemi se speciálními znaky:

- Jedno písmeno (bez rozlišování velkých a malých písmen)
- Pomlčka
- Šest nebo osm číslic
- Mezera
- Jedna číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keywords_eu_drivers_license_number"></a>Klíčová slova \_ \_ pro \_ licenční \_ číslo ovladače EU

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_portugal_eu_drivers_license_number"></a>Klíčová slova \_ Portugalsko – \_ \_ \_ licenční \_ číslo ovladače

```
carteira de motorista
carteira motorista
carteira de habilitação
carteira habilitação
número de licença
número licença
permissão de condução
permissão condução
Licença condução Portugal
carta de condução
```

## <a name="saudi-arabia-national-id"></a>Národní ID Saúdské Arábii

### <a name="format"></a>Formát

10 číslic

### <a name="pattern"></a>Vzor

10 po sobě jdoucích číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_saudi_arabia_national_id"></a>Klíčové slovo \_ Saúdská \_ Arábie – \_ národní \_ ID

```
Identification Card
I card number
ID number
الوطنية الهوية بطاقة رقم
```

## <a name="singapore-national-registration-identity-card-nric-number"></a>Singapur National Registration identity Card (NRIC) – číslo

### <a name="format"></a>Formát

devět písmen a číslic

### <a name="pattern"></a>Vzor

- devět písmen a číslic:
- písmeno &quot; F &quot; , &quot; G &quot; , &quot; S &quot; nebo &quot; T &quot; (nerozlišuje malá a velká písmena)
- sedm číslic
- Abecední kontrolní číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_singapore_nric"></a>Klíčové slovo \_ Singapur \_ NRIC

```
National Registration Identity Card
Identity Card Number
NRIC
IC
Foreign Identification Number
FIN
身份证
身份證
```

## <a name="south-africa-identification-number"></a>Identifikační číslo Jižní Afriky

### <a name="format"></a>Formát

13 číslic, které mohou obsahovat mezery

### <a name="pattern"></a>Vzor

13 číslic:

- šest číslic ve formátu rrmmdd, což jsou datum narození.
- čtyři číslice
- jednořádkový indikátor občanství
- číslice &quot; 8 &quot; nebo &quot; 9&quot;
- jedna číslice, což je číslice kontrolního součtu

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_south_africa_identification_number"></a>\_ \_ \_ Identifikační číslo Jižní Afriky \_ pro klíčové slovo

```
Identity card
ID
Identification
```

## <a name="south-korea-resident-registration-number"></a>Registrační číslo pro rezidentské Jižní Korea

### <a name="format"></a>Formát

13 číslic obsahujících spojovník

### <a name="pattern"></a>Vzor

13 číslic:

- šest číslic ve formátu rrmmdd, což jsou datum narození.
- spojovník
- jedna číslice určená století a pohlaví
- kód oblasti narození na základě čtyřmístného čísla
- jedna číslice používaná k odlišení lidí, pro které jsou předchozí čísla shodná
- kontrolní číslice.

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_south_korea_resident_number"></a>Klíčové slovo \_ Jižní \_ Korea – \_ rezidentní \_ číslo

```
National ID card
Citizen's Registration Number
Jumin deungnok beonho
RRN
주민등록번호
```

## <a name="spain-social-security-number-ssn"></a>Rodné číslo (sociální zabezpečení)

Tato entita typu citlivých informací je zahrnutá v čísle sociálního pojištění EU nebo ekvivalentním typu s citlivým ID a je k dispozici jako entita typu samostatné citlivé informace.

### <a name="format"></a>Formát

11-12 číslic

### <a name="pattern"></a>Vzor

11-12 číslic:

- dvě číslice
- lomítko (volitelné)
- sedm až osm číslic
- lomítko (volitelné)
- dvě číslice

### <a name="keywords"></a>Klíčová slova

Žádné

## <a name="sweden-national-id"></a>Národní ID Švédska

### <a name="format"></a>Formát

10 nebo 12 číslic a volitelné oddělovače

### <a name="pattern"></a>Vzor

10 nebo 12 číslic a volitelné oddělovače:

- dvě číslice (volitelné)
- Šest číslic ve formátu data rrmmdd
- oddělovač &quot; - &quot; nebo &quot; + &quot; (volitelné)
- čtyři číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keywords_swedish_national_identifier"></a>Klíčová slova \_ švédská \_ národní \_ identifikátor

```
ID no
ID number
ID#
identification no
identification number
identifikationsnumret#
identifikationsnumret
identitetshandling
identity document
identity no
identity number
id-nummer
personal ID
personnummer#
personnummer
skatteidentifikationsnummer
```

## <a name="sweden-passport-number"></a>Švédský pas číslo

Tato entita typu citlivých informací je zahrnutá v poli typ důvěrné informace o číslech služby Passport pro EU a je dostupná jako entita typu samostatné citlivé informace.

### <a name="format"></a>Formát

osm číslic

### <a name="pattern"></a>Vzor

osm po sobě jdoucích číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_sweden_passport"></a>Klíčové slovo \_ Švédsko \_ Passport

```
visa requirements
Alien Registration Card
Schengen visas
Schengen visa
Visa Processing
Visa Type
Single Entry
Multiple Entries
G3 Processing Fees
```

#### <a name="keyword_passport"></a>Klíčové slovo \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="swift-code"></a>Kód SWIFT

### <a name="format"></a>Formát

čtyři písmena následovaná 5-31 písmeny nebo číslicemi

### <a name="pattern"></a>Vzor

čtyři písmena následovaná 5-31 písmeny nebo číslicemi:

- kód banky se čtyřmi číslicemi (nerozlišuje velká a malá písmena)
- volitelné místo
- 4-28 písmen nebo číslic (číslo účtu Basic bank (BBAN))
- volitelné místo
- jedna až tři písmena nebo číslice (zbytek BBAN)

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_swift"></a>Klíčové slovo \_ SWIFT

```
international organization for standardization 9362
iso 9362
iso9362
swift#
swift code
swift number
swiftroutingnumber
swift code
swift number #
swift routing number
bic number
bic code
bic #
bic#
bank identifier code
Organization internationale de normalization 9362
rapide #
code SWIFT
le numéro de swift
swift numéro d'acheminement
le numéro BIC
#BIC
code identificateur de banque
SWIFTコード
SWIFT番号
BIC番号
BICコード
SWIFT コード
SWIFT 番号
BIC 番号
BIC コード
金融機関識別コード
金融機関コード
銀行コード
```

## <a name="taiwan-national-identification-number"></a>Národní identifikační číslo pro Tchaj-wan

### <a name="format"></a>Formát

jedno písmeno (v angličtině) následované devíti číslicemi

### <a name="pattern"></a>Vzor

jedno písmeno (v angličtině) následované devíti číslicemi:

- jedno písmeno (v angličtině, bez rozlišení velkých a malých písmen)
- číslice &quot; 1 &quot; nebo &quot; 2&quot;
- osm číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_taiwan_national_id"></a>Klíčové slovo pro \_ Tchaj-wan \_ národní \_ ID

```
身份證字號
身份證
身份證號碼
身份證號
身分證字號
身分證
身分證號碼
身份證號
身分證統一編號
國民身分證統一編號
簽名
蓋章
簽名或蓋章
簽章
```

## <a name="taiwan-passport-number"></a>Číslo služby Passport pro Tchaj-wan

### <a name="format"></a>Formát

- biometrické číslo služby Passport: devět číslic
- číslo biometrické služby Passport: devět číslic

### <a name="pattern"></a>Vzor

číslo biometrické služby Passport:

- znak &quot; 3&quot;
- osm číslic

číslo služby Passport pro jiné než biometrické číslo:

- devět číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_taiwan_passport"></a>Klíčové slovo \_ Tchaj-wanu \_ Passport

```
ROC passport number
Passport number
Passport no
Passport Num
Passport #
护照
中華民國護照
Zhōnghuá Mínguó hùzhào
```

## <a name="taiwan-resident-certificate-arctarc-number"></a>Číslo (ARC/TARC) pro Tchaj-wan

### <a name="format"></a>Formát

10 písmen a číslic

### <a name="pattern"></a>Vzor

10 písmen a číslic:

- dvě písmena (nerozlišují se malá a velká písmena)
- osm číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_taiwan_resident_certificate"></a>\_ \_ Rezidentní certifikát pro klíčové slovo Tchaj-wan \_

```
Resident Certificate
Resident Cert
Resident Cert.
Identification card
Alien Resident Certificate
ARC
Taiwan Area Resident Certificate
TARC
居留證
外僑居留證
台灣地區居留證
```

## <a name="uk-drivers-license-number"></a>britské číslo řidičského průkazu

Tato entita typu citlivých informací je součástí typu s citlivými informacemi z ovladače EU a je dostupná jako entita typu samostatné citlivé informace.

### <a name="format"></a>Formát

Kombinace 18 písmen a číslic v zadaném formátu

### <a name="pattern"></a>Vzor

18 písmen a číslic:

- pět písmen (nerozlišuje malá a velká písmena) nebo číslice &quot; 9 &quot; místo písmen
- jedna číslice
- pět číslic ve formátu data MMDDY pro datum narození (sedm znaků se zvýší o 50, pokud je ovladač žena, tj. 51 až 62 místo 01 až 12)
- dvě písmena (bez rozlišení velkých a malých písmen) nebo číslice &quot; 9 &quot; místo písmen
- pět číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_uk_drivers_license"></a>License pro ovladače pro klíčová \_ Británie \_ \_

```
DVLA
light vans
quad bikes
motor cars
125cc
sidecar
tricycles
motorcycles
photo card license
learner drivers
license holder
license holders
driving licenses
driving license
dual control car
```

## <a name="uk-electoral-roll-number"></a>britské číslo Shrnutí

### <a name="format"></a>Formát

dvě písmena následovaná 1-4 číslicemi

### <a name="pattern"></a>Vzor

dvě písmena (nerozlišuje velká a malá písmena) následovaná 1-4 čísly

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_uk_electoral"></a>Klíčové slovo \_ UK – \_ volební

- jmenování Rady
- nominální tvar
- Rejstřík voleb
- volební Shrnutí

## <a name="uk-national-health-service-number"></a>britské národní číslo zdravotní služby

### <a name="format"></a>Formát

10-17 číslic oddělených mezerami

### <a name="pattern"></a>Vzor

10-17 číslic:

- buď tři, nebo 10 číslic
- mezera
- tři číslice
- mezera
- čtyři číslice

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_uk_nhs_number"></a>\_ \_ NHS \_ číslo klíčového slova UK

```
national health service
nhs
health services authority
health authority
```

#### <a name="keyword_uk_nhs_number1"></a>Klíčové slovo \_ UK \_ NHS \_ Číslo1

```
patient ID
patient identification
patient no
patient number
```

#### <a name="keyword_uk_nhs_number_dob"></a>Klíčové slovo \_ UK \_ NHS \_ number \_ dob

```
GP
DOB
D.O.B
Date of Birth
Birth Date
```

## <a name="uk-national-insurance-number-nino"></a>britské Národní pojišťovací číslo (NINO)

Tato entita typu citlivých informací je součástí typu důvěrné informace o národním identifikačním čísle EU a je dostupná jako entita typu samostatné citlivé informace.

### <a name="format"></a>Formát

sedm znaků nebo devíti znaků oddělených mezerami nebo pomlčkami

### <a name="pattern"></a>Vzor

dva možné vzory:

- dvě písmena (platný NINOs používají pouze určité znaky v této předponě, které tento vzor ověřuje; nerozlišuje velká a malá písmena.)
- šest číslic
- znak "A", "B", "C" nebo d "(jako je například předpona, jsou v příponě povoleny pouze určité znaky; nerozlišuje velká a malá písmena)

NEBO

- dvě písmena
- mezera nebo pomlčka
- dvě číslice
- mezera nebo pomlčka
- dvě číslice
- mezera nebo pomlčka
- dvě číslice
- mezera nebo pomlčka
- buď "A", "B", "C" nebo "d"


### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_uk_nino"></a>Klíčové slovo \_ UK \_ Nino

```
national insurance number
national insurance contributions
protection act
insurance
social security number
insurance application
medical application
social insurance
medical attention
social security
Great Britain
NI Number
NI No.
NI #
NI#
insurance#
insurance number
national insurance#
nationalinsurancenumber
```

## <a name="uk-unique-taxpayer-reference-number"></a>britské Jedinečné referenční číslo plátce

Tento typ citlivých informací je k dispozici pouze pro:

- Zásady ochrany před únikem informací
- zásady dodržování předpisů komunikace
- zásady správného řízení informací
- Správa záznamů
- Microsoft Cloud App Security

### <a name="format"></a>Formát

10 číslic bez mezer a oddělovačů

### <a name="pattern"></a>Vzor

10 číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keywords_uk_eu_tax_file_number"></a>Klíčová slova \_ Velká Británie (EU) – \_ \_ \_ počet daňových souborů \_

```
tax number
tax file
tax ID
tax identification no
tax identification number
tax no#
tax no
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="us-bank-account-number"></a>Číslo účtu banky v USA

### <a name="format"></a>Formát

6-17 číslic

### <a name="pattern"></a>Vzor

6-17 po sobě jdoucích číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_usa_bank_account"></a>Klíčové slovo \_ USA – \_ bankovní \_ účet

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account.
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
```

## <a name="us-drivers-license-number"></a>Číslo licence ovladače USA

### <a name="format"></a>Formát

Závisí na stavu

### <a name="pattern"></a>Vzor

závisí na stavu – například Praha:

- devět číslic formátovaných jako DDD bude odpovídat.
- devět číslic, jako je ddddddddd, se neshodují.

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_us_drivers_license_abbreviations"></a>Klíčové slovo \_ US \_ Drivers \_ License \_ zkratky

```
DL
DLS
CDL
CDLS
ID
IDs
DL#
DLS#
CDL#
CDLS#
ID#
IDs#
ID number
ID numbers
LIC
LIC#
```

#### <a name="keyword_us_drivers_license"></a>Licence pro ovladače pro klíčové slovo \_ US \_ \_

```
DriverLic
DriverLics
DriverLicense
DriverLicenses
Driver Lic
Driver Lics
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
identification number
identification numbers
identification #
ID card
ID cards
identification card
identification cards
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
ID card#
ID cards#
identification card#
identification cards#
```

#### <a name="keyword_state_name_drivers_license_name"></a>Klíčové slovo \_ [ \_ název stavu \_ ] \_ ovladače \_ název licence

- zkratka stavu (například &quot; ny &quot; )
- název stavu (například &quot; Praha &quot; )

## <a name="us-individual-taxpayer-identification-number-itin"></a>Jednotlivá daňové identifikační číslo (ITIN) USA

### <a name="format"></a>Formát

devět číslic, které začínají na &quot; 9 &quot; a obsahují &quot; 7 &quot; nebo &quot; 8 &quot; jako čtvrtou číslici, volitelně naformátované mezerami nebo pomlčkami

### <a name="pattern"></a>Vzor

formátu

- číslice &quot; 9&quot;
- dvě číslice
- mezera nebo pomlčka
- a &quot; 7 &quot; nebo &quot; 8&quot;
- číslice
- mezera nebo pomlčka
- čtyři číslice

neformátovaný

- číslice &quot; 9&quot;
- dvě číslice
- a &quot; 7 &quot; nebo &quot; 8&quot;
- pět číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_itin"></a>Klíčové slovo \_ itin

```
taxpayer
tax ID
tax identification
itin
i.t.i.n.
ssn
tin
social security
tax payer
itins
tax ID
individual taxpayer
```

## <a name="us-social-security-number-ssn"></a>Číslo sociálního pojištění USA (SSN)

### <a name="format"></a>Formát

devět číslic, které mohou být ve formátovaném nebo neformátovaném vzoru

>[!Note]
> Pokud byla vydaná před střední 2011, má rodné číslo silné formátování, kde určité části čísla musí spadat do určitých rozsahů, které jsou platné (ale nejsou žádné kontrolní součty).
>

### <a name="pattern"></a>Vzor

čtyři funkce hledají čísla sociálního zabezpečení ve čtyřech různých vzorcích:

- Func \_ SSN vyhledá čísla sociálního zabezpečení se silným formátováním před 2011, které se formátuje pomocí pomlček nebo mezer (DDD-DD-dddd nebo DDD DD dddd).
- Funkce \_ neformátovaného \_ rodného kódu vyhledává čísla sociálního zabezpečení s před2011m silným formátováním, které není formátované jako devět po sobě jdoucích číslic (ddddddddd).
- Funced \_ \_ formátovaného \_ rodného programu najde post-2011 čísla sociálního zabezpečení, které jsou formátovány pomlčkami nebo mezerami (DDD-DD-dddd nebo DDD DD dddd).
- Func \_ Randomized \_ unformátovaled \_ SSN najde post-2011 čísla sociálního zabezpečení, které nejsou formátované jako devět po sobě jdoucích číslic (ddddddddd).

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_ssn"></a>Klíčové slovo \_ SSN

```
SSA Number
social security number
social security #
social security#
social security no
Social Security#
Soc Sec
SSN
SSNS
SSN#
SS#
SSID
```

## <a name="us--uk-passport-number"></a>USA A VELKÁ BRITÁNIE číslo Passport

Spojené království typ důvěrné informace o čísle služby Passport je k dispozici v rámci EU typu s důvěrnými informacemi o číslech a je k dispozici jako entita typu samostatné citlivé informace.

### <a name="format"></a>Formát

devět číslic

### <a name="pattern"></a>Vzor

devět po sobě jdoucích číslic

### <a name="keywords"></a>Klíčová slova

#### <a name="keyword_passport"></a>Klíčové slovo \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```
