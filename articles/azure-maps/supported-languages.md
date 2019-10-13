---
title: Podpora lokalizace v Azure Maps | Microsoft Docs
description: Přečtěte si o podporovaných jazycích pro služby v Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b3382cffdc41685f8329a640aaf6c6c526375a83
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299836"
---
# <a name="localization-support-in-azure-maps"></a>Podpora lokalizace v Azure Maps

Azure Maps podporuje různé jazyky a zobrazení založené na zemi nebo oblasti. Tento článek poskytuje podporované jazyky a zobrazení, které vám pomůžou s implementací Azure Maps.


## <a name="azure-maps-supported-languages"></a>Azure Maps podporované jazyky

Azure Maps jsou lokalizované v různých jazycích v rámci služeb. Následující tabulka uvádí podporované kódy jazyků pro každou službu.  
  

| ID         | Name (Název)                   |  Mapy | Hledat | Plánování tras | Incidenty provozu | JS – ovládací prvek mapy | Časové pásmo |
|------------|------------------------|:-----:|:------:|:-------:|:-----------------:|:--------------:|:---------:|
| AF-ZA      | Afrikánština              |       |    ✓   |    ✓    |                   |                |     ✓     |
| ar – SA      | arabština                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| EU – ES      | Baskičtina                 |       |    ✓   |         |                   |                |     ✓     |
| BG-BG      | Bulharština              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| CA – ES      | Katalánština                |       |    ✓   |         |                   |                |     ✓     |
| ZH – HanS    | Čínština (zjednodušená)   |       |  zh-CN |         |                   |                |     ✓     |
| ZH – HanT    | Čínština (tradiční)  | zh – TW |  zh – TW |  zh – TW  |                   |      zh – TW     |     ✓     |
| HR – HR      | Chorvatština               |       |    ✓   |         |                   |                |     ✓     |
| cs-CZ      | Čeština                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| da – DK      | dánština                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl – NL      | Holandština                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NL      | Holandština (Belgie)        |       |    ✓   |         |                   |                |     ✓     |
| EN-AU      | Angličtina (Austrálie)    |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| EN-NZ      | Angličtina (Nový Zéland)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-GB      | Angličtina (Velká Británie) |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| EN-US      | Angličtina (USA)          |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| et – EE      | Estonština               |       |    ✓   |         |         ✓         |                |     ✓     |
| Fi – FI      | Finština                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-FR      | Francouzština                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr – CA      | Francouzština (Kanada)      |       |    ✓   |         |                   |                |     ✓     |
| HK – ES      | Galicijština               |       |    ✓   |         |                   |                |     ✓     |
| de-DE      | Němčina                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| El-GR      | Řečtina                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| He-IL      | Hebrejština                 |       |    ✓   |         |         ✓         |                |     ✓     |
| Dobrý den      | Hindština                  |       |        |         |                   |                |     ✓     |
| hu – HU      | Maďarština              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ID – ID      | Indonéština             |   ✓   |    ✓    |    ✓    |         ✓         |        ✓       |     ✓     |
| IT oddělení IT      | italština                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ja-JP      | Japonština               |       |        |         |                   |                |     ✓     |
| kk-KZ      | Kazaština                 |       |    ✓   |         |                   |                |     ✓     |
| ko – KR      | Korejština                 |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| ES-419     | Latinskoamerická španělština |       |    ✓   |         |                   |                |     ✓     |
| LV – LV      | Lotyština                |       |    ✓   |         |         ✓         |                |     ✓     |
| lt – LT      | Litevština             |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| MS-MY      | Malajština (latinka)          |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| NB – ne      | Norština, Bokmål       |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NGT        | Neutrální uzemněné – úřední jazyky pro všechny oblasti v místních skriptech, pokud jsou k dispozici |   ✓     |        |         |                   |      ✓          |         |
| NGT – Latn   | Neutrální uzemnění – Latinská exonyms Pokud je k dispozici skript latinky, bude použit |   ✓     |        |         |                   |        ✓         |          |
| pl-PL      | polština                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| pt – BR      | Portugalština (Brazílie)    |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| pt-PT      | Portugalština (Portugalsko)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| RO-RO      | Rumunština               |       |    ✓    |         |         ✓         |                |     ✓     |
| ru-RU      | ruština                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sr-Cyrl-RS | Srbština (cyrilice)     |       |    Srbština (cyrilice) – SR-RS   |         |                   |                |     ✓     |
| SR-Latn-RS | Srbština (latinka)        |       |        |         |                   |                |     ✓     |
| SK-SK      | Slovenština              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| SL-SL      | Slovinština              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| ES-ES      | Španělština                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ES – MX      | Španělština (Mexiko)       |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| sv-SE     | švédština                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| Th-tou      | Thajština                   |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| tr – TR      | turečtina                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| Spojené království – UA      | Ukrajinština               |       |    ✓   |         |                   |                |     ✓     |
| VI – VN      | Vietnamština             |       |    ✓   |         |                   |                |     ✓     |


## <a name="azure-maps-supported-views"></a>Azure Maps podporovaná zobrazení

> [!Note]
> Azure Maps byla vydána v následujících zemích nebo oblastech od 1. srpna 2019:
>  * Argentina
>  * Indie
>  * Maroko
>  * Pákistán
>
> Až od 1. srpna 2019 bude nastavení parametrů **zobrazení** definovat vrácený obsah mapy pro nové oblasti nebo země uvedené výše. Doporučujeme, abyste se ujistili, že jste nastavili parametr zobrazení požadovaný pro rozhraní REST API a sady SDK, které vaše služby používají.
>  
>
>  **Rozhraní REST API:**
>  
>  Ujistěte se, že jste nastavili parametr zobrazení podle potřeby. Parametr zobrazení určuje, která sada geopolitického obsahu je vrácena prostřednictvím služby Azure Maps Services. 
>
>  Ovlivněné služby Azure Maps REST:
>    
>    * Získat dlaždici mapy
>    * Získat obrázek mapy 
>    * Získat hledání přibližné
>    * Získat POI hledání
>    * Získat kategorii hledání POI
>    * Získat hledání v okolí
>    * Získat adresu pro hledání
>    * Získat strukturované adresy hledání
>    * Získat reverzní adresu pro hledání
>    * Získat opačnou ulici v adrese hledání
>    * Po vyhledání v geometrii
>    * Adresa pro vystavení dávky ve verzi Preview
>    * Adresa pro zpětný náhled dávky
>    * Vyhledávejte po trase
>    * Vyúčtování přibližné dávky ve verzi Preview
>
>    
>  **Sady SDK**
>
>  Ujistěte se, že jste nastavili parametr zobrazení podle potřeby a máte nejnovější verzi sady web SDK a Android SDK. Ovlivněné sady SDK:
>
>    * Azure Maps Web SDK
>    * Azure Maps Android SDK


Parametr **zobrazení** Azure Maps (také označovaný jako "parametr uživatelské oblasti") je dva číslice kódu země ISO-3166, které zobrazí správná mapování pro danou zemi nebo oblast určující, která sada geopolitického obsahu se bude vracet prostřednictvím Azure Maps služby, včetně ohraničení a popisků zobrazených na mapě. 

Ve výchozím nastavení je parametr zobrazení nastaven na hodnotu **sjednocené**, i když jste ho v žádosti nedefinovali. Je vaší zodpovědností určit umístění uživatelů a pak pro toto umístění nastavit parametr zobrazení správně. Případně můžete nastavit možnost zobrazit = automaticky, která vrátí data mapy na základě IP adresy žádosti.  Parametr zobrazení v Azure Maps musí být používán v souladu s platnými zákony, včetně těch, které se týkají mapování země, kde jsou k dispozici mapy, obrázky a další data a obsah třetích stran, ke kterým máte oprávnění přistupovat prostřednictvím Azure Maps.


Následující tabulka poskytuje podporovaná zobrazení.

| Zobrazit         | Popis                            |  Mapy | Hledat | Ovládací prvek Mapa JS |
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
| Automaticky         | Vraťte data mapy na základě IP adresy žádosti.|   ✓   |    ✓   |     ✓          |
| Unifikace      | Sjednocené zobrazení (ostatní)                  |   ✓   |   ✓     |     ✓          |
