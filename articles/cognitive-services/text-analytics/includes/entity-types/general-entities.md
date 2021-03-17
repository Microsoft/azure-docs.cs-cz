---
title: Obecné pojmenované entity
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2021
ms.author: aahi
ms.openlocfilehash: c1ff099dd6dffe06e9707ff23fffd57ae753ab64
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500084"
---
Funkce NER pro Analýza textu vrací následující obecné kategorie entit (bez identifikace). například při posílání požadavků do `/entities/recognition/general` koncového bodu.


| Kategorie | Popis                          |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| [Person (Osoba)](#category-person)     | Jména lidí.  |
| [PersonType](#category-persontype) | Typy úloh nebo role držené osobou. |
| [Umístění](#category-location)    | Přírodní a lidský orientačních bodů, struktur, geografických funkcí a geopolitických entit |
| [Organizace](#category-organization)  | Společnosti, politické skupiny, hudební pásma, sportovní klub, státní orgány a veřejné organizace.  |
| [Událost](#category-event)  | Historické, společenské a přirozeně vyskytující se události. |
| [Product](#category-product) (Produkt) | Fyzické objekty různých kategorií. |
| [Tuhle](#category-skill) | Schopnost, dovednosti nebo odbornost.  |
| [Adresa](#category-address) | Úplné poštovní adresy.  |
| [Telefonní číslo](#category-phonenumber) | Telefonní čísla. |
| [E-mail](#category-email) | E-mailové adresy. |
| [Adresa URL](#category-url) | Adresy URL webů. |
| [IP adresa](#category-ip) | Síťové IP adresy. |
| [Datum a čas](#category-datetime) | Data a denní doba. |
| [Množství](#category-quantity) | Číselná měření a jednotky. |


### <a name="category-person"></a>Kategorie: osoba

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        Person (Osoba)

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Jména lidí.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, <br> `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt`-`pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>Kategorie: PersonType

Tato kategorie obsahuje následující entitu:


:::row:::
    :::column span="":::
        **Entita**

        PersonType

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Typy úloh nebo role držené osobou
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-location"></a>Kategorie: umístění

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        Umístění

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Přírodní a lidský orientačních bodů, struktur, geografických funkcí a geopolitických entit.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Podkategorie

Entita v této kategorii může mít následující podkategorie.

:::row:::
    :::column span="":::
        **Podkategorie entity**

        Geopolitická entita (GPE)

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Města, země nebo oblasti, státy.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Strukturované

    :::column-end:::
    :::column span="2":::

        Struktury manmade. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Zeměpisné

    :::column-end:::
    :::column span="2":::

        Geografické a přirozené funkce, jako jsou řeky, oceány a Deserts.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-organization"></a>Kategorie: organizace

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        Organizace

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Společnosti, politické skupiny, hudební pásma, sportovní klub, státní orgány a veřejné organizace. Státní příslušníky a náboženství nejsou zahrnuté do tohoto typu entity.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Podkategorie

Entita v této kategorii může mít následující podkategorie.

:::row:::
    :::column span="":::
        **Podkategorie entity**

        Lékař

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Lékařské společnosti a skupiny.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Burzovní Exchange

    :::column-end:::
    :::column span="2":::

        Burzovní skupiny Exchange. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Organizace související s sportem.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-event"></a>Kategorie: událost

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        Událost

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Historické, společenské a přirozeně vyskytující se události.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`, `es` , `fr` , `de` , `it` , `zh-hans` , `ja` , `ko` `pt-pt` a `pt-br`  
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Podkategorie

Entita v této kategorii může mít následující podkategorie.

:::row:::
    :::column span="":::
        **Podkategorie entity**

        Kulturní

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Kulturní události a svátky.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Len

    :::column-end:::
    :::column span="2":::

        Přirozeně vyskytující se události.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Sportovní události.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-product"></a>Kategorie: produkt

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        Produkt

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Fyzické objekty různých kategorií.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::


#### <a name="subcategories"></a>Podkategorie

Entita v této kategorii může mít následující podkategorie.

:::row:::
    :::column span="":::
        **Podkategorie entity**

        Výpočetní produkty
    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Výpočetní produkty.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-skill"></a>Kategorie: dovednost

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        Tuhle

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Schopnost, dovednosti nebo odbornost.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`  
      
   :::column-end:::
:::row-end:::

### <a name="category-address"></a>Kategorie: adresa

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        Adresa

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Úplná poštovní adresa.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>Kategorie: PhoneNumber

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Telefonní čísla (jenom USA a telefonní čísla EU).
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-email"></a>Kategorie: E-mail

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        E-mail

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        E-mailové adresy.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-url"></a>Kategorie: adresa URL

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        URL

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Adresy URL webů. 
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-ip"></a>Kategorie: IP

Tato kategorie obsahuje následující entitu:

:::row:::
    :::column span="":::
        **Entita**

        IP adresa

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        síťové IP adresy. 
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>Kategorie: DateTime

Tato kategorie obsahuje následující entity:

:::row:::
    :::column span="":::
        **Entita**

        DateTime

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Data a denní doba. 
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

Entity v této kategorii můžou mít následující podkategorie.

#### <a name="subcategories"></a>Podkategorie

Entita v této kategorii může mít následující podkategorie.

:::row:::
    :::column span="":::
        **Podkategorie entity**

        Date (Datum)

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Kalendářní data kalendáře
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Čas

    :::column-end:::
    :::column span="2":::

        Denní dobu.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Rozsah dat

    :::column-end:::
    :::column span="2":::

        Rozsahy dat.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Časový rozsah

    :::column-end:::
    :::column span="2":::

        Časové rozsahy.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Doba trvání

    :::column-end:::
    :::column span="2":::

        Dob trvání.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Nastavit

    :::column-end:::
    :::column span="2":::

        Nastavte opakující se časy.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>Kategorie: množství

Tato kategorie obsahuje následující entity:

:::row:::
    :::column span="":::
        **Entita**

        Množství

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Čísla a číselná množství.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Podkategorie

Entita v této kategorii může mít následující podkategorie.

:::row:::
    :::column span="":::
        **Podkategorie entity**

        Číslo

    :::column-end:::
    :::column span="2":::
        **Podrobnosti**

        Hodnoty.
      
    :::column-end:::
    :::column span="2":::
      **Podporované jazyky dokumentů**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Procento

    :::column-end:::
    :::column span="2":::

        Procenta
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Pořadová čísla

    :::column-end:::
    :::column span="2":::

        Pořadová čísla
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Věk

    :::column-end:::
    :::column span="2":::

        Ve věku.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Měna

    :::column-end:::
    :::column span="2":::

        Měnami
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Dimenze

    :::column-end:::
    :::column span="2":::

        Rozměry a měření.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Teplota

    :::column-end:::
    :::column span="2":::

        Teplot.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
