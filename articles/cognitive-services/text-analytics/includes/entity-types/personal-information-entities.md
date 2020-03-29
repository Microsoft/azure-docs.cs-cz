---
title: Pojmenované entity pro osobní údaje
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: d678a29de9dea8a5a2f6d0259a452ca4c69feb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77086907"
---
## <a name="personal-information-entity-types"></a>Typy subjektů osobních údajů:

### <a name="person"></a>Person (Osoba)
Rozpoznání jmen osob v textu.

Jazyky:
* Veřejná verze preview:`English`

| Název podtypu | Popis                                               | K dispozici počínaje verzí modelu |
|--------------|-----------------------------------------------------------|----------------------------------------|
| Není dostupné.          | Rozpoznané názvy, například `Bill Gates`,`Marie Curie` | `2020-02-01`                           |

### <a name="organization"></a>Organizace  

Uznat organizace, korporace, agentury, společnosti, kluby a další skupiny lidí.

Jazyky: 

* Veřejná verze preview:`English`

| Název podtypu | Popis                                                                                       | K dispozici počínaje verzí modelu|
|--------------|---------------------------------------------------------------------------------------------------|--------------|
| Není dostupné.          | organizace, například `Microsoft` `NASA`,`National Oceanic and Atmospheric Administration` | `2020-02-01` |

### <a name="phone-number"></a>Telefonní číslo

Telefonní čísla (pouze telefonní čísla USA). 

Jazyky:

* Veřejná verze preview:`English`

| Název podtypu | Popis                                    | K dispozici počínaje verzí modelu |
|--------------|------------------------------------------------|----------------------------------------|
| Není dostupné.          | Telefonní čísla v USA, například`(312) 555-0176` | `2020-02-01`                           |

### <a name="email"></a>E-mail

E-mailovou adresu 

Jazyky:

* Veřejná verze preview:`English`

| Název podtypu | Popis                                      | K dispozici počínaje verzí modelu |
|--------------|--------------------------------------------------|----------------------------------------|
| Není dostupné.          | E-mailová adresa, například`support@contoso.com` | `2020-02-01`                           |

### <a name="url"></a>zprostředkovatele identity

Adresy URL internetu.

Jazyky:

* Veřejná verze preview:`English`

| Název podtypu | Popis                                          | K dispozici počínaje verzí modelu |
|--------------|------------------------------------------------------|----------------------------------------|
| Není dostupné.          | Adresy URL pro webové stránky, například`https://www.bing.com` | `2020-02-01`                           |

### <a name="ip-address"></a>IP adresa

Adresa internetového protokolu

Jazyky:

* Veřejná verze preview:`English`

| Název podtypu | Popis                              | K dispozici počínaje verzí modelu |
|--------------|------------------------------------------|----------------------------------------|
| Není dostupné.          | Síťová adresa například`10.0.0.101` | `2020-02-01`                           |

### <a name="quantity"></a>Množství 

Číselná množství

Jazyky:

* Veřejná verze preview:`English`

| Název podtypu | Popis                   | K dispozici počínaje verzí modelu |
|--------------|-------------------------------|----------------------------------------|
| Věk          | `90 days old`, `30 years old` | `2020-02-01`                           |

### <a name="datetime"></a>DateTime

Entity data a času

Jazyky:

* Veřejná verze preview:`English`

| Název podtypu | Popis                   | K dispozici počínaje verzí modelu |
|--------------|-------------------------------|----------------------------------------|
| Datum         | `May 2nd, 2017`, `05/02/2017` | `2020-02-01`                           |

### <a name="eu-gps-coordinates"></a>GPS souřadnice EU

 GPS souřadnice pro místa v rámci Evropské unie. 

Jazyky:

* Veřejná verze preview:`English`

| Název podtypu | Popis                               | K dispozici počínaje verzí modelu |
|--------------|-------------------------------------------|----------------------------------------|
| Není dostupné.          | GPS souřadnice v rámci Evropské unie | `2019-10-01`                           |

### <a name="azure-information"></a>Informace o Azure

Identifikovatelné informace Azure včetně ověřovacích informací a připojovacích řetězců. 

* K dispozici počínaje `2019-10-01`verzí modelu .

Jazyky:

* Veřejná verze preview:`English`

| Název podtypu                          | Popis                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Auth klíč Azure DocumentDB             | Autorizační klíč pro server Azure DocumentDB.                           |
| Připojovací řetězec databáze Azure IAAS | Připojovací řetězec pro databázi Infrastruktury Azure jako služby (IaaS). |
| Připojovací řetězec Azure SQL           | Připojovací řetězec pro databázi Azure SQL.                                |
| Připojovací řetězec Azure IoT           | Připojovací řetězec pro Azure Internet věcí (IoT).                        |
| Heslo pro nastavení publikování Azure        | Heslo pro nastavení publikování Azure.                                        |
| Připojovací řetězec mezipaměti Azure Redis   | Připojovací řetězec pro azure mezipaměť pro Redis.                             |
| Azure SAS                             | Připojovací řetězec pro Software Azure jako služba (SAS).                     |
| Řetězec připojení azure service bus   | Připojovací řetězec pro servisní sběrnici Azure.                                |
| Klíč účtu úložiště Azure             | Klíč účtu pro účet úložiště Azure.                                   |
| Klíč účtu úložiště Azure (obecný)   | Obecný klíč účtu pro účet úložiště Azure.                           |
| Připojovací řetězec serveru SQL Server          | Připojovací řetězec pro sql server.                                         |

### <a name="identification"></a>Identifikace

* K dispozici počínaje `2019-10-01`verzí modelu .

Jazyky:

* Veřejná verze preview:`English`

#### <a name="financial-account-identification"></a>Identifikace finančního účtu

| Název podtypu               | Popis                                                                |
|----------------------------|----------------------------------------------------------------------------|
| Směrovací čísla ABA        | Čísla tranzitních tras American Banker Association (ABA).                  |
| Kód SWIFT                 | SWIFT kódy pro informace o příkazech k platbě.                           |
| Platební karta                | Čísla kreditních karet.                                                       |
| Kód IBAN                  | Kódy IBAN pro informace o příkazech k platbě.                            |

#### <a name="government-and-country-specific-identification"></a>Identifikace pro jednotlivé vlády a země

Níže uvedené subjekty jsou seskupeny a uvedeny podle zemí:

Argentina
* Číslo národní identity (DNI)

Austrálie
* Číslo daňového souboru 
* ID řidičského průkazu
* ID pasu
* Číslo lékařského účtu
* čísla bankovních účtů (například šekové, spořicí a debetní účty)

Belgie
* Národní číslo

Brazílie
* Číslo právnické osoby (CNPJ)
* Číslo CPF
* Národní průkaz (RG)

Kanada
* ID pasu
* ID řidičského průkazu
* Číslo zdravotního pojištění
* Id osobního zdraví (PHIN)
* Číslo sociálního pojištění
* čísla bankovních účtů (například šekové, spořicí a debetní účty)

Chile
* Číslo průkazu totožnosti 

Čína
* Číslo průkazu totožnosti
* Číslo rezidentního průkazu (ČLR)

Chorvatsko
* Číslo průkazu
* Číslo osobního id (OIB)

Česká republika
* Číslo průkazu občana

Dánsko
* Osobní číslo

Evropská unie (EU)
* Národní id číslo
* ID pasu
* ID řidičského průkazu
* Číslo sociálního zabezpečení (SSN) nebo ekvivalentní ID
* Daňové identifikační číslo EU (DIČ)
* Číslo debetní karty EU

Finsko
* Národní id číslo
* ID pasu

Francie
* Národní průkaz (CNI)
* Číslo sociálního pojištění (INSEE)
* ID pasu
* ID řidičského průkazu

Německo
* Číslo průkazu
* ID pasu
* ID řidičského průkazu

Řecko 
* Číslo průkazu občana

Hongkong
* Číslo průkazu (HKID)

Indie
* Číslo trvalého účtu (PAN)
* Číslo jedinečného ID (Aadhaar)

Indonésie
* Číslo průkazu (KTP)

Irsko
* Číslo osobní veřejné služby (PPS)

Izrael
* Národní ID
* čísla bankovních účtů (například šekové, spořicí a debetní účty)

Itálie
* ID řidičského průkazu

Japonsko
* Rezidentní registrační číslo
* Číslo pobytové karty
* ID řidičského průkazu
* Číslo sociálního pojištění (SIN)
* ID pasu
* čísla bankovních účtů (například šekové, spořicí a debetní účty)

Malajsie
* Číslo průkazu

Nizozemsko
* Číslo služby občana (BSN)

Nový Zéland
* Číslo ministerstva zdravotnictví

Norsko
* Číslo průkazu

Filipíny
* Jednotné víceúčelové číslo ID

Polsko
* Číslo průkazu
* Národní ID (PESEL)
* ID pasu

Portugalsko 
* Číslo občanské karty

Saúdská Arábie
* Národní ID

Singapur
* Číslo národního registračního průkazu (NRIC)

Jižní Afrika
* ID číslo
* Rezidentní registrační číslo

Jižní Korea
* Rezidentní registrační číslo

Španělsko 
* Číslo sociálního pojištění (SSN)

Švédsko
* Národní ID
* ID pasu

Tchaj-wan 
* Národní ID
* Číslo rezidentního certifikátu (ARC/TARC)
* ID pasu

Thajsko
* Identifikační kód populace

Spojené království
* ID pasu
* ID řidičského průkazu
* Číslo národního pojištění (NINO)
* Číslo Národní zdravotní služby (NHS)

Spojené státy
* Číslo sociálního pojištění (SSN)
* ID řidičského průkazu
* ID pasu
* Číslo seznamu voličů
* Individuální dIČ (ITIN)
* Číslo Protidrogové agentury (DEA)
* čísla bankovních účtů (například šekové, spořicí a debetní účty)
