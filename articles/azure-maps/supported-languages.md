---
title: Podpora lokalizace | Mapy Microsoft Azure
description: V tomto článku se dozvíte o podporovaných jazycích pro služby v Mapách Microsoft Azure.
author: philmea
ms.author: philmea
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3b3733dfc32dae21ddcf4c5f73cddf9ad6b7fc59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334074"
---
# <a name="localization-support-in-azure-maps"></a>Podpora lokalizace v Mapách Azure

Azure Maps podporuje různé jazyky a zobrazení na základě země nebo oblasti. Tento článek obsahuje podporované jazyky a zobrazení, které vám pomohou řídit implementaci Azure Maps.


## <a name="azure-maps-supported-languages"></a>Podporované jazyky Azure Maps

Azure Maps byly lokalizovány v různých jazycích napříč svými službami. V následující tabulce jsou uvedeny kódy podporovaných jazyků pro každou službu.  
  

| ID         | Name (Název)                   |  Maps | Search | Směrování | Weather | Dopravní nehody | Ovládací prvek mapy JS |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | Afrikánština              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Arabština                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| bn-BD      | Bangla (Bangladéš)    |       |       |         |     ✓    |                   |                |
| mld-IN      | Bangla (Indie)         |       |       |         |     ✓    |                   |                |
| bs-BA      | Bosenština                 |       |       |         |     ✓    |                   |                |
| eu-ES      | Baskičtina                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Bulharština              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ca-ES      | Katalánština                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | Čínština (zjednodušená)   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Čínština (Hongkong – zvláštní správní oblast ČLR)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | Čínština (Tchaj-wan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | Chorvatština               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Čeština                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | Dánština                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| nl-BE      | Nizozemština (Belgie)        |       |    ✓   |         |      ✓    |                   |                |
| nl-NL      | nizozemština (Nizozemsko)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-AU      | Angličtina (Austrálie)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-NZ      | Angličtina (Nový Zéland)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-CZ      | Angličtina (Velká Británie) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| cs-CZ      | Angličtina (USA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Estonština               |       |    ✓   |         |      ✓    |         ✓         |                |
| fil-PH     | Filipino               |       |       |         |     ✓    |                   |                |
| fi-FI      | Finština                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Francouzština                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | Francouzština (Kanada)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Galicijština               |       |    ✓   |         |         |                   |                |
| de-DE      | Němčina                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | Řečtina                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| gu-IN      | Gudžarátština                |       |       |         |     ✓    |                   |                |
| he-IL      | Hebrejština                 |       |    ✓   |         |     ✓    |         ✓         |                |
| hi-IN      | Hindština                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Maďarština              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| is-IS      | Islandština              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonéština             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Italština                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japonština               |       |        |         |     ✓    |                   |                |
| kn-IN      | Kannadština                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Kazaština                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Korejština                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419 (es-419)     | Latinskoamerická španělština |       |    ✓   |         |         |                   |                |
| lv-LV      | Lotyština                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Litevština             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| mk-MK      | Makedonština             |       |       |         |     ✓    |                   |                |
| ms-MY      | Malajština (latinka)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| mr-IN      | Maráthština                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norština bokmål       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Neutrální pozemní pravda - Úřední jazyky pro všechny regiony v místních skriptech, pokud jsou k dispozici |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | Neutrální pozemní pravda - latinské exonyms. Latinka bude použita, pokud je k dispozici |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Polština                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Portugalština (Brazílie)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | portugalština (Portugalsko)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| pa-IN      | Paňdžábština                 |       |       |         |     ✓    |                   |                |
| ro-RO      | Rumunština               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Ruština                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Srbština (cyrilice)     |       |   sr-RS  |         |    sr-RS     |                   |                |
| sr-Latn-RS | Srbština (latinka)        |       |       |         |     sr-latn    |                   |                |
| sk-SK      | Slovenština             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| sl-SL      | Slovinština              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Španělština                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | Španělština (Mexiko)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | Švédština                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ta-IN      | Tamil (Indie)                 |       |       |         |     ✓    |                   |                |
| te-IN      | Telugu (Indie)                 |       |       |         |     ✓    |                   |                |
| th-TH      | Thajština                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | Turečtina                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Ukrajinština               |       |    ✓   |         |     ✓    |                   |                |
| vaše-PK      | Urdština                 |       |       |         |     ✓    |                   |                |
| uz-Latn-UZ | Uzbečtina                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Vietnamština             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Podporovaná zobrazení Azure Maps

> [!Note]
> 1. srpna 2019 byla vydána služba Azure Maps v následujících zemích nebo oblastech:
>  * Argentina
>  * Indie
>  * Maroko
>  * Pákistán
>
> srpna 2019 parametr **View** definuje vrácený obsah mapy pro výše uvedené nové oblasti nebo země. Parametr Azure Maps **View** (označovaný také jako "parametr oblasti uživatele") je dvoupísmenný kód země ISO-3166, který zobrazí správné mapy pro danou zemi nebo oblast určující, která sada geopoliticky sporného obsahu se vrátí prostřednictvím služeb Azure Maps, včetně ohraničení a popisků zobrazených na mapě. 

Ujistěte se, že jste nastavili **parametr View** podle potřeby pro rozhraní REST API a sady SDK, které vaše služby používají.
>  
>
>  **Zbytek API:**
>  
>  Ujistěte se, že jste nastavili parametr View podle potřeby. Parametr View určuje, která sada geopoliticky sporného obsahu se vrací prostřednictvím služeb Azure Maps. 
>
>  Ovlivněné služby Azure Maps REST:
>    
>    * Získat dlaždici mapy
>    * Získat obrázek mapy 
>    * Získat přibližné vyhledávání
>    * Získat Poi.
>    * Kategorie Vyhledávání po zem
>    * Získat vyhledávání v okolí
>    * Získat vyhledávací adresu
>    * Získat strukturovanou adresu vyhledávání
>    * Získat adresu vyhledávání zpět
>    * Získat vyhledávací adresu Reverse Cross Street
>    * Post Search Uvnitř geometrie
>    * Náhled dávky zaúčtovat vyhledávací adresu
>    * Náhled zpětné dávky zaúčtovat adresu s obrácenou adresou
>    * Po hledání podél trasy
>    * Náhled přibližné dávky při hledání příspěvku
>
>    
>  **Sady sdk:**
>
>  Ujistěte se, že jste nastavili **view** parametr podle potřeby a máte nejnovější verzi sady Web SDK a Android SDK. Ovlivněné sady SDK:
>
>    * Web Ovádk Azure Maps
>    * Azure Maps Android SDK

Ve výchozím nastavení je parametr View nastaven na **hodnotu Sjednoceno**, i když jste jej v požadavku nedefinovali. Určete umístění uživatelů. Potom nastavte **správně Zobrazit** parametr pro toto umístění. Případně můžete nastavit "View=Auto", které vrátí mapová data na základě IP adresy požadavku.  Parametr **View** v Mapách Azure se musí používat v souladu s platnými zákony, včetně zákonů o mapování země, kde jsou mapy, obrázky a další data a obsah třetích stran, ke kterým máte oprávnění, zpřístupnit.


Následující tabulka obsahuje podporovaná zobrazení.

| Zobrazení         | Popis                            |  Maps | Search | Ovládací prvek mapy JS |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Spojené arabské emiráty (arabský pohled)    |   ✓   |        |     ✓          |
| AR           | Argentina (argentinský pohled)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrajn (arabský pohled)                 |   ✓   |        |     ✓          |
| IN           | Indie (indický pohled)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irák (arabský pohled)                    |   ✓   |        |     ✓          |
| JO           | Jordánsko (arabský pohled)                  |   ✓   |        |     ✓          |
| KW           | Kuvajt (arabský pohled)                  |   ✓   |        |     ✓          |
| LB           | Libanon (arabský pohled)                 |   ✓   |        |     ✓          |
| MA           | Maroko (marocký pohled)                |   ✓   |   ✓     |     ✓          |
| OM           | Omán (arabský pohled)                    |   ✓   |        |     ✓          |
| PK           | Pákistán (pákistánský pohled)              |   ✓   |    ✓    |     ✓          |
| PS           | Palestinská samospráva (arabský pohled)    |   ✓   |        |     ✓          |
| QA           | Katar (arabský pohled)                   |   ✓   |        |     ✓          |
| SA           | Saúdská Arábie (arabský pohled)            |   ✓   |        |     ✓          |
| SY           | Sýrie (arabský pohled)                   |   ✓   |        |     ✓          |
| VY           | Jemen (arabský pohled)                   |   ✓   |        |     ✓          |
| Auto         | Vraťte mapová data na základě IP adresy požadavku.|   ✓   |    ✓   |     ✓          |
| Jednotný      | Jednotné zobrazení (ostatní)                  |   ✓   |   ✓     |     ✓          |
