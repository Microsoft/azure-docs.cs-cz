---
title: Pojmenované entity pro osobní údaje
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2019
ms.author: aahi
ms.openlocfilehash: 3aa4da9a9cf3d1d4b664e81f1fd18f2b225d731d
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799764"
---
## <a name="personal-information-entity-types"></a>Typy entit osobních informací:

### <a name="phone-number"></a>Telefonní číslo

Telefonní čísla. 

Jazyky

* Verze Public Preview: `English`

| Název podtypu           | Popis                                           |
|------------------------|-------------------------------------------------------|
| Není dostupné.                    | Telefonní čísla, například `+1 123-123-123`.          |
| Telefonní číslo v EU        | Telefonní čísla specifická pro Evropskou unii.         |
| Číslo mobilního telefonu v EU | Mobilní telefonní čísla specifická pro Evropskou unii. |

### <a name="eu-gps-coordinates"></a>Evropské unie GPS – souřadnice

 Souřadnice GPS pro umístění v rámci Evropské unie. 

Jazyky

* Verze Public Preview: `English`

| Název podtypu | Popis                               |
|--------------|-------------------------------------------|
| Není dostupné.          | Souřadnice GPS v rámci Evropské unie |

### <a name="azure-information"></a>Informace o Azure

Identifikovatelné informace Azure včetně ověřovacích informací a připojovacích řetězců. 

Jazyky

* Verze Public Preview: `English`

| Název podtypu                          | Popis                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Klíč ověření Azure DocumentDB             | Autorizační klíč pro server Azure DocumentDB                           |
| Připojovací řetězec databáze Azure IAAS | Připojovací řetězec pro databázi infrastruktury Azure jako služby (IaaS). |
| Připojovací řetězec SQL Azure           | Připojovací řetězec pro databázi SQL Azure.                                |
| Připojovací řetězec Azure IoT           | Připojovací řetězec pro Azure Internet věcí (IoT)                        |
| Heslo pro nastavení publikování v Azure        | Heslo pro nastavení publikování v Azure                                        |
| Připojovací řetězec Azure Redis Cache   | Připojovací řetězec pro službu Azure cache pro Redis.                             |
| SAS Azure                             | Připojovací řetězec pro službu Azure software as a Service (SAS).                     |
| Připojovací řetězec Azure Service Bus   | Připojovací řetězec pro službu Azure Service Bus.                                |
| Klíč účtu Azure Storage             | Klíč účtu pro účet služby Azure Storage.                                   |
| Klíč účtu Azure Storage (Obecné)   | Klíč obecného účtu pro účet služby Azure Storage.                           |
| Připojovací řetězec SQL Server          | Připojovací řetězec pro systém SQL Server.                                         |

### <a name="identification"></a>Identifikace

Jazyky

* Verze Public Preview: `English`

#### <a name="financial-account-identification"></a>Identifikace finančního účtu

| Název podtypu               | Popis                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA čísla směrování        | Směrovací čísla asociace (ABA) pro americký bankový přenos                  |
| Kód SWIFT                 | Kódy SWIFT pro informace o platebních pokynech.                           |
| Platební karta                | Čísla platebních karet                                                       |
| Kód IBAN                  | Kódy IBAN pro informace o platebních pokynech.                            |

#### <a name="government-and-country-specific-identification"></a>Identifikace specifická pro státní správu a zemi

Níže uvedené entity jsou seskupené podle země:

Argentina
* Číslo národní identity (DNI)

Austrálie
* Číslo daňového souboru 
* ID licence ovladače
* ID služby Passport
* Zdravotní číslo účtu
* čísla bankovních účtů (například kontrola, úspory a debetní účty)

Belgie
* Národní číslo

Brazílie
* Číslo právní entity (CNPJ)
* CPF číslo
* Karta National ID (RG)

Kanada
* ID služby Passport
* ID licence ovladače
* Číslo pojištění stavu
* Číslo ID osobního stavu (PHIN)
* Číslo sociálního pojištění
* čísla bankovních účtů (například kontrola, úspory a debetní účty)

Chile
* Číslo karty identity 

Čína
* Číslo karty identity
* Číslo rezidentního ID karty (ČLR)

Chorvatsko
* Číslo karty ID
* Číslo osobního ID (OIB)

Česká republika
* Číslo karty národního ID

Dánsko
* Číslo osobního ID

Evropské unie (EU)
* Národní identifikační číslo
* ID služby Passport
* ID licence ovladače
* Číslo sociálního pojištění (SSN) nebo ekvivalentní ID
* Daňové identifikační číslo (DIČ) EU
* Číslo debetní karty v EU

Finsko
* Národní identifikační číslo
* ID služby Passport

Francie
* Karta National ID (CNI)
* Číslo sociálního pojištění (INSEE)
* ID služby Passport
* ID licence ovladače

Německo
* Číslo karty ID
* ID služby Passport
* ID licence ovladače

Řecko 
* Číslo karty národního ID

Hongkong
* Číslo karty ID (HKID)

Indie
* Trvalé číslo účtu (PAN)
* Číslo jedinečného ID (Aadhaar)

Indonésie
* Číslo karty ID (KTP)

Irsko
* Číslo osobní veřejné služby (PPS)

Izrael
* Národní ID
* čísla bankovních účtů (například kontrola, úspory a debetní účty)

Itálie
* ID licence ovladače

Japonsko
* Registrační číslo rezidentu
* Číslo karty pro pobyt
* ID licence ovladače
* Číslo sociálního pojištění (SIN)
* ID služby Passport
* čísla bankovních účtů (například kontrola, úspory a debetní účty)

Malajsie
* Číslo karty ID

Nizozemsko
* Číslo služby občana (BSN)

Nový Zéland
* Ministerstvo číslo zdravotního stavu

Norsko
* Číslo karty ID

Filipíny
* Jednotné ID pro více účelů

Polsko
* Číslo karty ID
* Národní ID (PESEL)
* ID služby Passport

Portugalsko 
* Číslo karty občana

Saúdská Arábie
* Národní ID

Singapur
* Číslo karty národního registračního ID (NRIC)

Jižní Afrika
* Číslo ID
* Registrační číslo rezidentu

Jižní Korea
* Registrační číslo rezidentu

Španělsko 
* Rodné číslo (sociální zabezpečení)

Švédsko
* Národní ID
* ID služby Passport

Tchaj-wan 
* Národní ID
* Číslo rezidentního certifikátu (ARC/TARC)
* ID služby Passport

Thajsko
* Identifikační kód populace

Spojené království
* ID služby Passport
* ID licence ovladače
* Národní pojišťovací číslo (NINO)
* Národní číslo Health Service (NHS)

Spojené státy
* Rodné číslo (sociální zabezpečení)
* ID licence ovladače
* ID služby Passport
* Číslo Shrnutí
* Individuální daňové identifikační číslo (ITIN)
* Číslo v úřadu pro vynucování drog (DEA)
* čísla bankovních účtů (například kontrola, úspory a debetní účty)
