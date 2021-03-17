---
title: Identifikační entity
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: a376b050d79709885e3542d330bb6b1eea48d046
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750744"
---
### <a name="financial-account-identification"></a>Identifikace finančního účtu

Tato kategorie entit zahrnuje finanční informace a oficiální formy identifikace.

#### <a name="category-aba-routing-number"></a>Kategorie: číslo směrování ABA

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        Číslo směrování ABA

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Směrovací čísla asociace (ABA) pro americký bankový přenos
      
    :::column-end:::
:::row-end:::

#### <a name="category-swift-code"></a>Kategorie: kód SWIFT

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        Kód SWIFT

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Kódy SWIFT pro informace o platebních pokynech.
      
    :::column-end:::
:::row-end:::

#### <a name="category-credit-card"></a>Kategorie: platební karta

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        Platební karta

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Čísla platebních karet 
      
    :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>Kategorie: číslo mezinárodního bankovního účtu (IBAN) 

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        Platební karta

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Kódy IBAN pro informace o platebních pokynech.
      
    :::column-end:::
:::row-end:::

### <a name="government-and-countryregion-specific-identification"></a>Identifikace specifická pro státní správu a zemi/oblast

> [!NOTE]
> Následující finanční entity a entity specifické pro danou zemi nejsou vráceny s `domain=phi` parametrem:
> * Čísla Passport
> * Daňové identifikátory

Následující entity jsou seskupené a uvedené podle země:

#### <a name="argentina"></a>Argentina

:::row:::
    :::column span="":::
        **Entita**

        Číslo argentinské National identity (DNI)

    :::column-end:::
:::row-end:::


#### <a name="austria"></a>Rakousko

:::row:::
    :::column span="":::
        **Entita**

        Karta identity v Rakousku

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové identifikační číslo v Rakousku

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Číslo DPH přidané v hodnotě Rakousko

    :::column-end:::
:::row-end:::



#### <a name="australia"></a>Austrálie

:::row:::
    :::column span="":::
        **Entita**

        Číslo bankovního účtu Austrálie

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Australské obchodní číslo

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Číslo společnosti v Austrálii

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Licence k strojvedoucímu v Austrálii  

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo zdravotního účtu Austrálie

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo služby Passport v Austrálii

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Číslo služby Passport v Austrálii

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Číslo daňového souboru v Austrálii

    :::column-end:::

:::row-end:::


#### <a name="belgium"></a>Belgie

:::row:::
    :::column span="":::
        **Entita**

        Národní číslo v Belgii

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo DPH přidané do přidané hodnoty v Belgii

    :::column-end:::

:::row-end:::


#### <a name="brazil"></a>Brazílie 

:::row:::
    :::column span="":::
        **Entita**

        Brazílie – právní číslo entity (CNPJ)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Brazílie – CPF číslo

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Karta National ID Brazílie (RG)

    :::column-end:::
:::row-end:::

#### <a name="canada"></a>Kanada

:::row:::
    :::column span="":::
        **Entita**

        Číslo bankovního účtu v Kanadě

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Číslo licence ovladače Kanady

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo služby Health Service pro Kanadu

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo služby Passport pro Kanadu

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanada – osobní identifikační číslo zdravotního stavu (PHIN)

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Rodné číslo v Kanadě

    :::column-end:::
:::row-end:::

#### <a name="chile"></a>Chile 

:::row:::
    :::column span="":::
        **Entita**

        Číslo karty identity Chile

    :::column-end:::
:::row-end:::

#### <a name="china"></a>Čína

:::row:::
    :::column span="":::
        **Entita**

        Čína – číslo rezidenta identity na kartu (ČLR)

    :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>Evropské unie (EU)

:::row:::
    :::column span="":::
        **Entita**

        Číslo debetní karty v EU

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo licence ovladače EU

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Národní identifikační číslo v EU

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo Passportu EU

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo sociálního pojištění (SSN) nebo ekvivalentní ID pro EU

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové identifikační číslo (DIČ) EU

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Evropské unie GPS – souřadnice

    :::column-end:::
:::row-end:::

#### <a name="france"></a>Francie

:::row:::
    :::column span="":::
        **Entita**

        Číslo licence ovladače Francie

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo pojištění stavu Francie

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Karta National ID Francie (CNI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Francouzský pas číslo

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Francouzský rodné číslo (INSEE)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové identifikační číslo pro Francii (numéro SPI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo daně z přidané hodnoty (DPH) pro Francii

    :::column-end:::
:::row-end:::

#### <a name="germany"></a>Německo

:::row:::
    :::column span="":::
        **Entita**

        Číslo licence německého ovladače

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo karty identity v Německu

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo německé služby Passport

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové identifikační číslo pro Německo

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové číslo přidané hodnoty v Německu

    :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>Hongkong

:::row:::
    :::column span="":::
        **Entita**

        Číslo HKID (Hongkong – zvláštní identita)

    :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Maďarsko

:::row:::
    :::column span="":::
        **Entita**

        Osobní identifikační číslo Maďarska

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové identifikační číslo v Maďarsku

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové číslo přidané hodnoty v Maďarsku

    :::column-end:::
:::row-end:::

#### <a name="india"></a>Indie

:::row:::
    :::column span="":::
        **Entita**

        Indie – trvalé číslo účtu (PAN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Jedinečné identifikační číslo Indie (Aadhaar)

    :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonésie

:::row:::
    :::column span="":::
        **Entita**

        KTP (Indonésie identity Card) – číslo

    :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Irsko

:::row:::
    :::column span="":::
        **Entita**

        Číslo pro irské osobní veřejné služby (PPS)

    :::column-end:::
:::row-end:::

#### <a name="israel"></a>Izrael

:::row:::
    :::column span="":::
        **Entita**

        Mezinárodní ID Izraele

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo bankovního účtu Izraele

    :::column-end:::
:::row-end:::

#### <a name="italy"></a>Itálie

:::row:::
    :::column span="":::
        **Entita**

        ID licence ovladače Itálie

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Fiskální kód Itálie

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové číslo přidané hodnoty v Itálii

    :::column-end:::
:::row-end:::


#### <a name="japan"></a>Japonsko

:::row:::
    :::column span="":::
        **Entita**

        Číslo bankovního účtu Japonska

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Číslo licence ovladače Japonska

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japonsko "moje číslo" (osobní)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japonsko "moje číslo" (firemní)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Registrační číslo rezidenta v japonštině

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo karty pro pobyt z Japonska

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo sociálního pojištění Japonska (SIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japonské číslo služby Passport

    :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Lucembursko

:::row:::
    :::column span="":::
        **Entita**

        Národní identifikační číslo v Lucemburku (fyzické osoby)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Národní identifikační číslo v Lucemburku (nefyzické osoby)

    :::column-end:::
:::row-end:::

#### <a name="malta"></a>Malta

:::row:::
    :::column span="":::
        **Entita**

        Malta číslo karty identity

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové identifikační číslo Malty

    :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Nový Zéland

:::row:::
    :::column span="":::
        **Entita**

        Číslo bankovního účtu Nového Zélandu

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo licence ovladače Nového Zélandu

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo Nového Zélandu v vnitrozemských výnosech

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Nové ministerstvo, které má číslo zdravotního Zélandu

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Číslo sociální péče na Novém Zélandu

    :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Filipíny

:::row:::
    :::column span="":::
        **Entita**

        Jednotné ID pro více účelů Filipíny

    :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portugalsko 

:::row:::
    :::column span="":::
        **Entita**

        Číslo karty občanů v Portugalsku

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Daňové identifikační číslo Portugalska

    :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Singapur

:::row:::
    :::column span="":::
        **Entita**

        Číslo karty (NRIC) Singapuru (National registration ID)

    :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>Jižní Afrika

:::row:::
    :::column span="":::
        **Entita**

        Identifikační číslo Jižní Afriky

    :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Jižní Korea

:::row:::
    :::column span="":::
        **Entita**

        Registrační číslo pro rezidentské Jižní Korea

    :::column-end:::
:::row-end:::

#### <a name="spain"></a>Španělsko

:::row:::
    :::column span="":::
        **Entita**

        Španělsko DNI

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Rodné číslo (sociální zabezpečení)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Daňové identifikační číslo pro Španělsko

    :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Švýcarsko

:::row:::
    :::column span="":::
        **Entita**

        Švýcarský sociální rodné číslo AHV

    :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Tchaj-wan 

:::row:::
    :::column span="":::
        **Entita**

        Mezinárodní ID pro Tchaj-wan

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Rezidentský certifikát pro Tchaj-wan (oblouk/TARC)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Číslo služby Passport pro Tchaj-wan

    :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Spojené království

:::row:::
    :::column span="":::
        **Entita**

        britské Číslo řidičského průkazu

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       britské Číslo Shrnutí

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       britské Národní číslo Health Service (NHS)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       britské Národní pojišťovací číslo (NINO)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       britské nebo číslo amerického služby Passport

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

       britské Jedinečné referenční číslo plátce

    :::column-end:::

:::row-end:::


#### <a name="united-states"></a>USA

:::row:::
    :::column span="":::
        **Entita**

        Číslo sociálního pojištění USA (SSN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Číslo licence ovladače USA

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       USA nebo Spojené království Číslo Passport

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Jednotlivá daňové identifikační číslo (ITIN) USA

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Číslo DEA (USA) pro vynucovací orgány

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Číslo účtu banky v USA

    :::column-end:::
:::row-end:::
