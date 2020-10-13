---
title: Identifikační entity
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 49a2b285d9494dfc5ca0863c021733b4bc874efa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779339"
---
Tato kategorie entit zahrnuje finanční informace a oficiální formy identifikace. K dispozici počínaje verzí modelu `2019-10-01` . Níže jsou uvedeny podtypy. 

### <a name="financial-account-identification"></a>Identifikace finančního účtu

| Název podtypu               | Description                                                                |
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

Následující entity jsou seskupené a uvedené podle země:

Argentina
* Číslo argentinské National identity (DNI)

Rakousko
* Karta identity v Rakousku
* Daňové identifikační číslo v Rakousku
* Číslo DPH přidané v hodnotě Rakousko

Austrálie
* Číslo bankovního účtu Austrálie
* Australské obchodní číslo
* Číslo australské společnosti
* Číslo licence ovladače Austrálie
* Číslo zdravotního účtu Austrálie
* Číslo služby Passport v Austrálii
* Číslo daňového souboru v Austrálii

Belgie
* Národní číslo v Belgii
* Daňové číslo přidané hodnoty v Belgii

Brazílie 
* Brazílie – právní číslo entity (CNPJ)
* Brazílie – CPF číslo
* Karta National ID Brazílie (RG)

Bulharsko
* Bulharsko jednotné občanské číslo

Kanada
* Číslo bankovního účtu v Kanadě
* Číslo licence ovladače Kanady
* Číslo Health Service pro Kanadu
* Číslo služby Passport pro Kanadu
* Kanada – osobní identifikační číslo zdravotního stavu (PHIN)
* Rodné číslo v Kanadě

Chile
* Číslo karty identity 

Čína
* Čína – číslo rezidenta identity na kartu (ČLR)

Chorvatsko
* Chorvatsko číslo karty identity
* Státní identifikační číslo na kartě Chorvatsko
* Osobní identifikační číslo (OIB) Chorvatsku

Kypr
* Číslo karty Kypru identity
* Daňové identifikační číslo Kypru

Česká republika
* Osobní identifikační číslo v češtině

Dánsko
* Dánsko – osobní identifikační číslo

Estonsko
* Estonsko – osobní identifikační kód

Evropské unie (EU)
* Číslo debetní karty v EU
* Číslo licence ovladače EU
* Národní identifikační číslo v EU
* Číslo Passportu EU
* Číslo sociálního pojištění (SSN) nebo ekvivalentní ID pro EU
* Daňové identifikační číslo (DIČ) EU

Finsko
* Finsko – evropské číslo pojištění pro nákazy
* Národní ID Finska
* Číslo Finska služby Passport

Francie
* Číslo licence ovladače Francie
* Číslo pojištění stavu Francie
* Karta National ID Francie (CNI)
* Francouzský pas číslo
* Francouzský rodné číslo (INSEE)
* Daňové identifikační číslo pro Francii (numéro SPI)
* Daňové číslo přidané hodnoty Francie

Německo
* Číslo licence německého ovladače
* Číslo karty identity v Německu
* Německé číslo služby Passport
* Daňové identifikační číslo pro Německo
* Daňové číslo přidané hodnoty v Německu

Řecko 
* Číslo karty národního ID Řecka
* Daňové identifikační číslo pro Řecko

Hongkong
* Číslo HKID (Hongkong – zvláštní identita)

Maďarsko
* Národní identifikační číslo Maďarska
* Daňové identifikační číslo v Maďarsku
* Daňové číslo přidané hodnoty v Maďarsku

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
* Fiskální kód Itálie
* Daňové číslo přidané hodnoty v Itálii

Japonsko
* Číslo bankovního účtu Japonska
* Číslo licence ovladače Japonska
* Japonské číslo osobní
* Moje číslo společnosti v japonštině
* Registrační číslo rezidenta v japonštině
* Číslo karty japonského pobytu
* Číslo sociálního pojištění Japonska (SIN)
* Japonské číslo služby Passport

Lotyšsko
* Lotyšsko – osobní kód

Litva
* Osobní kód v Litvě

Lucembursko
* Lucembursko, národní identifikační číslo (fyzické osoby)
* Lucembursko národní identifikační číslo (nefyzické osoby)

Malajsie
* Číslo karty identity Malajsie

Malta
* Malta číslo karty identity
* Daňové identifikační číslo Malty

Nizozemsko
* Číslo služby (BSN) nizozemského občana
* Nizozemské daňové identifikační číslo
* Daňové číslo přidané hodnoty v Nizozemsku

Nový Zéland
* Číslo bankovního účtu Nového Zélandu
* Číslo licence ovladače Nového Zélandu
* Číslo Nového Zélandu v vnitrozemských výnosech
* Nové ministerstvo, které má číslo zdravotního Zélandu
* Číslo sociální péče na Novém Zélandu

Norsko
* Číslo identity Norska

Filipíny
* Jednotné ID pro více účelů Filipíny

Polsko
* Karta identity Polska
* Mezinárodní ID Polska (PESEL)
* Číslo Polska Passport
* REGON číslo Polska
* Daňové identifikační číslo Polska

Portugalsko 
* Číslo karty občanů v Portugalsku
* Daňové identifikační číslo Portugalska

Rumunsko
* Rumunsko – osobní číselný kód (CNP)

Rusko
* Ruské Passport číslo (domácí)
* Ruské Passport číslo (mezinárodní)

Saúdská Arábie
* Národní ID Saúdské Arábii

Singapur
* Číslo karty (NRIC) Singapuru (National registration ID)

Slovensko 
* Osobní číslo Slovenska

Slovinsko
* Daňové identifikační číslo pro Slovinsko
* Jedinečné hlavní číslo občana Slovinsko

Jižní Afrika
* Identifikační číslo Jižní Afriky

Jižní Korea
* Registrační číslo pro rezidentské Jižní Korea

Španělsko 
* Španělsko DNI
* Rodné číslo (sociální zabezpečení)
* Daňové identifikační číslo pro Španělsko

Švédsko
* Národní ID Švédska
* Švédský pas číslo
* Daňové identifikační číslo Švédska

Švýcarsko
* Švýcarský sociální rodné číslo AHV

Tchaj-wan 
* Mezinárodní ID pro Tchaj-wan
* Rezidentský certifikát pro Tchaj-wan (oblouk/TARC)
* Číslo služby Passport pro Tchaj-wan

Thajsko
* Thajský identifikační kód naplnění

Turecko
* Národní identifikační číslo v turečtině

Ukrajina
* Ukrajina (domácí) číslo služby Passport
* Ukrajina – číslo pasu (mezinárodní)

Spojené království
* britské Číslo řidičského průkazu
* britské Číslo Shrnutí
* britské Národní číslo Health Service (NHS)
* britské Národní pojišťovací číslo (NINO)
* britské Číslo Passport
* britské Jedinečné referenční číslo plátce

USA
* Číslo sociálního pojištění USA (SSN)
* Číslo licence ovladače USA
* Číslo služby Passport pro USA
* Jednotlivá daňové identifikační číslo (ITIN) USA
* Číslo DEA (USA) pro vynucovací orgány
* Číslo účtu banky v USA
