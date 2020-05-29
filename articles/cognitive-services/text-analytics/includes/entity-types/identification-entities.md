---
title: Identifikační entity
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: f07b71bf8996612798b87d32a21a15ec72db0b32
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140911"
---
Tato kategorie entit zahrnuje finanční informace a oficiální formy identifikace. K dispozici počínaje verzí modelu `2019-10-01` . Níže jsou uvedeny podtypy. 

### <a name="financial-account-identification"></a>Identifikace finančního účtu

| Název podtypu               | Popis                                                                |
|----------------------------|----------------------------------------------------------------------------|
| Směrovací číslo ABA        | Směrovací čísla asociace (ABA) pro americký bankový přenos                  |
| Kód SWIFT                 | Kódy SWIFT pro informace o platebních pokynech.                           |
| Platební karta                | Čísla platebních karet                                                       |
| Číslo IBAN                  | Kódy IBAN pro informace o platebních pokynech.                            |


### <a name="government-and-countryregion-specific-identification"></a>Identifikace specifická pro státní správu a zemi/oblast

> [!NOTE]
> Následující finanční entity a entity specifické pro danou zemi nejsou vráceny s `domain=phi` parametrem:
> * Čísla Passport
> * Daňové identifikátory

Níže uvedené entity jsou seskupené podle země:

Argentina
* Číslo argentinské National identity (DNI)

Austrálie
* Číslo služby Passport v Austrálii
* Číslo daňového souboru v Austrálii
* Číslo licence ovladače Austrálie
* Číslo zdravotního účtu Austrálie
* Číslo bankovního účtu Austrálie

Belgie
* Národní číslo v Belgii

Brazílie 
* Brazílie – právní číslo entity (CNPJ)
* Brazílie – CPF číslo
* Karta National ID Brazílie (RG)

Kanada
* Rodné číslo v Kanadě
* Číslo licence ovladače Kanady
* Číslo bankovního účtu v Kanadě
* Číslo služby Passport pro Kanadu
* Kanada – osobní identifikační číslo zdravotního stavu (PHIN)
* Číslo Health Service pro Kanadu

Chile
* Číslo karty identity 

Čína
* Čína – číslo rezidenta identity na kartu (ČLR)

Chorvatsko
* Chorvatsko číslo karty identity
* Osobní identifikační číslo (OIB) Chorvatsku

Česká republika
* Osobní identifikační číslo v češtině

Dánsko
* Dánsko – osobní identifikační číslo

Evropské unie (EU)
* Národní identifikační číslo v EU
* Číslo Passportu EU
* Číslo licence ovladače EU
* Číslo sociálního pojištění (SSN) nebo ekvivalentní ID pro EU
* Daňové identifikační číslo (DIČ) EU
* Číslo debetní karty v EU

Finsko
* Národní ID Finska
* Číslo Finska služby Passport

Francie
* Karta National ID Francie (CNI)
* Francouzský rodné číslo (INSEE)
* Francouzský pas číslo
* Číslo licence ovladače Francie

Německo
* Číslo karty identity v Německu
* Německé číslo služby Passport
* Číslo licence německého ovladače

Řecko 
* Číslo karty národního ID Řecka

Hongkong
* Číslo HKID (Hongkong – zvláštní identita)

Indie
* Indie – trvalé číslo účtu (PAN)
* Jedinečné identifikační číslo Indie (Aadhaar)

Indonésie
* KTP (Indonésie identity Card) – číslo

Irsko
* Číslo pro irské osobní veřejné služby (PPS)

Izrael
* Mezinárodní ID Izraele
* Číslo bankovního účtu Izraele

Itálie
* ID licence ovladače Itálie

Japonsko
* Registrační číslo rezidenta v japonštině
* Číslo karty japonského pobytu
* Číslo licence ovladače Japonska
* Číslo sociálního pojištění (SIN)
* Japonské číslo služby Passport
* Číslo bankovního účtu Japonska

Malajsie
* Číslo karty identity Malajsie

Nizozemsko
* Číslo služby (BSN) nizozemského občana

Nový Zéland
* Nové ministerstvo, které má číslo zdravotního Zélandu

Norsko
* Číslo identity Norska

Filipíny
* Jednotné ID pro více účelů Filipíny

Polsko
* Karta identity Polska
* Mezinárodní ID Polska (PESEL)
* Polsko Passport

Portugalsko 
* Číslo karty občanů v Portugalsku

Saúdská Arábie
* Národní ID Saúdské Arábii

Singapur
* Číslo karty (NRIC) Singapuru (National registration ID)

Jižní Afrika
* Identifikační číslo Jižní Afriky

Jižní Korea
* Registrační číslo pro rezidentské Jižní Korea

Španělsko 
* Rodné číslo (sociální zabezpečení)

Švédsko
* Národní ID Švédska
* Švédský pas číslo

Tchaj-wan 
* Mezinárodní ID pro Tchaj-wan
* Rezidentský certifikát pro Tchaj-wan (oblouk/TARC)
* Číslo služby Passport pro Tchaj-wan

Thajsko
* Thajský identifikační kód naplnění

Spojené království
* ID služby Passport
* britské Číslo řidičského průkazu
* britské Národní pojišťovací číslo (NINO)
* britské Národní číslo Health Service
* britské Číslo Shrnutí
* USA A VELKÁ BRITÁNIE Číslo Passport

USA
* Číslo sociálního pojištění USA (SSN)
* Číslo licence ovladače USA
* USA A VELKÁ BRITÁNIE Číslo Passport
* Jednotlivá daňové identifikační číslo (ITIN) USA
* Číslo v úřadu pro vynucování drog (DEA)
* Číslo účtu banky v USA
