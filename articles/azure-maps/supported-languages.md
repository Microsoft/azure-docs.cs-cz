---
title: Podpora lokalizace pomocí map Microsoft Azure
description: Podívejte se, které oblasti Azure Maps podporuje se službami, jako jsou mapy, hledání, směrování, počasí a incidenty provozu. Přečtěte si, jak nastavit parametr zobrazení.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 50e5d0721eb14d1fcdfad26aaf081bfa370e954e
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904512"
---
# <a name="localization-support-in-azure-maps"></a>Podpora lokalizace v Azure Maps

Azure Maps podporuje různé jazyky a zobrazení založené na zemi nebo oblasti. Tento článek poskytuje podporované jazyky a zobrazení, které vám pomůžou s implementací Azure Maps.


## <a name="azure-maps-supported-languages"></a>Azure Maps podporované jazyky

Azure Maps byly lokalizovány do různých jazyků v rámci svých služeb. Následující tabulka uvádí podporované kódy jazyků pro každou službu.  
  

| ID         | Název                   |  Mapy | Search | Směrování | Počasí | Incidenty provozu | JS – ovládací prvek mapy |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| AF-ZA      | Afrikánština              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Arabština                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| BN-BD      | Bengálština (Bangladéš)    |       |       |         |     ✓    |                   |                |
| BN-IN      | Bengálština (Indie)         |       |       |         |     ✓    |                   |                |
| BS – BA      | Bosenština                 |       |       |         |     ✓    |                   |                |
| EU – ES      | Baskičtina                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Bulharština              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ca-ES      | Katalánština                |       |    ✓   |         |    ✓      |                   |                |
| ZH – HanS    | Čínština (zjednodušená)   |       |  zh-CN |         |     zh-CN   |                   |                |
| ZH – HanT    | Čínština (Hongkong – zvláštní administrativní oblast)  |  |   |    |    zh – HK   |                   |           |
| ZH – HanT    | Čínština (Tchaj-wan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | Chorvatština               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Čeština                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | Dánština                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| NL      | Nizozemština (Belgie)        |       |    ✓   |         |      ✓    |                   |                |
| nl-NL      | nizozemština (Nizozemsko)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| EN-AU      | Angličtina (Austrálie)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| EN-NZ      | Angličtina (Nový Zéland)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Angličtina (Velká Británie) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-US      | Angličtina (USA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Estonština               |       |    ✓   |         |      ✓    |         ✓         |                |
| náhl-PH     | Filipino               |       |       |         |     ✓    |                   |                |
| fi-FI      | Finština                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Francouzština                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr – CA      | Francouzština (Kanada)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Galicijština               |       |    ✓   |         |         |                   |                |
| de-DE      | Němčina                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | Řečtina                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| Gu – IN      | Gudžarátština                |       |       |         |     ✓    |                   |                |
| he-IL      | Hebrejština                 |       |    ✓   |         |     ✓    |         ✓         |                |
| hi-IN      | Hindština                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Maďarština              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| je-IS      | Islandština              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonéština             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Italština                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japonština               |       |        |         |     ✓    |                   |                |
| KN-IN      | Kannadština                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Kazaština                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Korejština                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| ES-419     | Latinskoamerická španělština |       |    ✓   |         |         |                   |                |
| lv-LV      | Lotyština                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Litevština             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| MK-MK      | Makedonie             |       |       |         |     ✓    |                   |                |
| ms-MY      | Malajština (latinka)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| Mr      | Maráthština                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norština bokmål       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Neutrální uzemněné – úřední jazyky pro všechny oblasti v místních skriptech, pokud jsou k dispozici |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | Neutrální uzemnění – Latinská exonyms Pokud je k dispozici skript latinky, bude použit |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Polština                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Portugalština (Brazílie)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | portugalština (Portugalsko)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| PA – v      | Paňdžábština                 |       |       |         |     ✓    |                   |                |
| ro-RO      | Rumunština               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Ruština                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Srbština (cyrilice)     |       |   SR-RS  |         |    SR-RS     |                   |                |
| sr-Latn-RS | Srbština (latinka)        |       |       |         |     SR-Latn    |                   |                |
| sk-SK      | Slovenština             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| SL-SL      | Slovinština              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Španělština                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ES – MX      | Španělština (Mexiko)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | Švédština                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| Ta – IN      | Tamilština (Indie)                 |       |       |         |     ✓    |                   |                |
| te-IN      | Telugština (Indie)                 |       |       |         |     ✓    |                   |                |
| th-TH      | Thajština                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | Turečtina                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Ukrajinština               |       |    ✓   |         |     ✓    |                   |                |
| Vaše PK      | Urdština                 |       |       |         |     ✓    |                   |                |
| uz-Latn-UZ | Uzbečtina                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Vietnamština             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Azure Maps podporovaná zobrazení

> [!Note]
> Od 1. srpna 2019 byl Azure Maps vydán v následujících zemích nebo oblastech:
>  * Argentina
>  * Indie
>  * Maroko
>  * Pákistán
>
> Od 1. srpna 2019 bude parametr **zobrazení** definovat vrácený obsah mapy pro nové oblasti nebo země uvedené výše. Azure Maps parametr **zobrazení** (také označovaný jako "parametr oblasti uživatele") je dvě číslice země ISO-3166, která zobrazí správná mapování pro danou zemi nebo oblast, která určuje, která sada geopoliticky sporného obsahu se vrátí prostřednictvím služby Azure Maps, včetně ohraničení a popisků zobrazených na mapě. 

Ujistěte se, že jste nastavili parametr **zobrazení** požadovaný pro rozhraní REST API a sady SDK, které vaše služby používají.
  

### <a name="rest-apis"></a>Rozhraní REST API
  
Ujistěte se, že jste nastavili parametr zobrazení podle potřeby. Parametr zobrazení určuje, která sada geopolitického obsahu je vrácena prostřednictvím služby Azure Maps Services. 

Ovlivněné služby Azure Maps REST:
    
 * Získat dlaždici mapy
 * Získat obrázek mapy 
 * Získat hledání přibližné
 * Získat POI hledání
 * Získat kategorii hledání POI
 * Získat hledání v okolí
 * Získat adresu pro hledání
 * Získat strukturované adresy hledání
 * Získat reverzní adresu pro hledání
 * Získat opačnou ulici v adrese hledání
 * Po vyhledání v geometrii
 * Vystavit dávku adres hledání
 * Adresa pro vrácení zpět dávky po hledání
 * Vyhledávejte po trase
 * Vyfiltrovat nepřibližnou dávku po hledání

 
### <a name="sdks"></a>Sady SDK

Ujistěte se, že jste nastavili parametr **zobrazení** podle potřeby a máte nejnovější verzi sady web SDK a Android SDK. Ovlivněné sady SDK:

 * Azure Maps Web SDK
 * Azure Maps Android SDK

Ve výchozím nastavení je parametr zobrazení nastavený na **sjednocené**, i když jste ho v žádosti nedefinovali. Určete umístění vašich uživatelů. Potom nastavte pro toto umístění parametr **zobrazení** správně. Případně můžete nastavit možnost zobrazit = automaticky, která vrátí data mapy na základě IP adresy žádosti.  Parametr **zobrazení** v Azure Maps musí být používán v souladu s platnými zákony, včetně zákonů o mapování země nebo oblasti, kde jsou k dispozici mapy, obrázky a další data a obsah třetích stran, ke kterým máte oprávnění pro přístup prostřednictvím Azure Maps.


Následující tabulka poskytuje podporovaná zobrazení.

| Zobrazit         | Description                            |  Mapy | Search | Ovládací prvek Mapa JS |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Spojené arabské emiráty (pohled na arabské písmo)    |   ✓   |        |     ✓          |
| AR           | Argentina (pohled z argentinského)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrajn (zobrazení arabštiny)                 |   ✓   |        |     ✓          |
| IN           | Indie (pohled z Indie)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irák (zobrazení arabštiny)                    |   ✓   |        |     ✓          |
| JO           | Jordánsko (zobrazení arabštiny)                  |   ✓   |        |     ✓          |
| KW           | Kuvajt (zobrazení arabštiny)                  |   ✓   |        |     ✓          |
| LB           | Libanon (zobrazení arabštiny)                 |   ✓   |        |     ✓          |
| MA           | Maroko (pohled na marocký)                |   ✓   |   ✓     |     ✓          |
| OM           | Omán (zobrazení arabštiny)                    |   ✓   |        |     ✓          |
| PK           | Pákistán (zobrazení pákistánského Pákistánu)              |   ✓   |    ✓    |     ✓          |
| PS           | Palestinská samospráva (zobrazení arabštiny)    |   ✓   |        |     ✓          |
| QA           | Katar (zobrazení arabštiny)                   |   ✓   |        |     ✓          |
| SA           | Saúdská Arábie (zobrazení arabštiny)            |   ✓   |        |     ✓          |
| SY           | Sýrie (zobrazení arabštiny)                   |   ✓   |        |     ✓          |
| JE           | Jemen (zobrazení arabštiny)                   |   ✓   |        |     ✓          |
| Auto         | Vraťte data mapy na základě IP adresy žádosti.|   ✓   |    ✓   |     ✓          |
| Sjednocen      | Sjednocené zobrazení (ostatní)                  |   ✓   |   ✓     |     ✓          |
