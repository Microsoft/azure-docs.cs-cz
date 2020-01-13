---
title: Podpora lokalizace | Mapy Microsoft Azure
description: V tomto článku se dozvíte o podporovaných jazycích pro služby v Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4926cb2bb4cb1aa15b212cc7130e0db995a24ed9
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910438"
---
# <a name="localization-support-in-azure-maps"></a>Podpora lokalizace v Azure Maps

Azure Maps podporuje různé jazyky a zobrazení založené na zemi nebo oblasti. Tento článek poskytuje podporované jazyky a zobrazení, které vám pomůžou s implementací Azure Maps.


## <a name="azure-maps-supported-languages"></a>Azure Maps podporované jazyky

Azure Maps jsou lokalizované v různých jazycích v rámci služeb. Následující tabulka uvádí podporované kódy jazyků pro každou službu.  
  

| ID         | Name (Název)                   |  Mapy | Hledat | Plánování tras | Počasí | Incidenty provozu | JS – ovládací prvek mapy |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | Afrikánština              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | arabština                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| BN-BD      | Bengálština (Bangladéš)    |       |       |         |     ✓    |                   |                |
| bn-IN      | Bengálština (Indie)         |       |       |         |     ✓    |                   |                |
| BS – BA      | Bosenština                 |       |       |         |     ✓    |                   |                |
| EU – ES      | Baskičtina                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Bulharština              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ES certifikační autority      | Katalánština                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | Čínština (zjednodušená)   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Hongkong (zvláštní správní oblast ČLR)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | Čínština (Tchaj-wan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | Chorvatština               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Čeština                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | dánština                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| nl-BE      | Nizozemština (Belgie)        |       |    ✓   |         |      ✓    |                   |                |
| NL-NL      | Nizozemština (Nizozemsko)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| cs AU      | angličtina (Austrálie)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| cs NZ      | angličtina (Nový Zéland)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Angličtina (Velká Británie) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| cs-CZ      | Angličtina (USA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Estonština               |       |    ✓   |         |      ✓    |         ✓         |                |
| náhl-PH     | Filipínština               |       |       |         |     ✓    |                   |                |
| fi-FI      | Finština                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Francouzština                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | Francouzština (Kanada)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Galicijština               |       |    ✓   |         |         |                   |                |
| de-DE      | Němčina                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | Řečtina                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| Gu – IN      | Gudžarátština                |       |       |         |     ✓    |                   |                |
| he-IL      | Hebrejština                 |       |    ✓   |         |     ✓    |         ✓         |                |
| Dobrý den – v      | Hindština                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Maďarština              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| je-IS      | Islandština              |       |       |         |     ✓    |                   |                |
| ID ID      | Indonéština             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| IT-IT      | italština                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japonština               |       |        |         |     ✓    |                   |                |
| KN-IN      | Kannadština                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Kazaština                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Korejština                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Latinskoamerická španělština |       |    ✓   |         |         |                   |                |
| lv-LV      | Lotyština                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Litevština             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| MK-MK      | Makedonie             |       |       |         |     ✓    |                   |                |
| Moje MS      | Malajština (latinka)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| mr-IN      | Maráthština                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norština bokmål       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Neutrální uzemněné – úřední jazyky pro všechny oblasti v místních skriptech, pokud jsou k dispozici |   ✓     |        |         |       |        |      ✓          |
| NGT – Latn   | Neutrální uzemnění – Latinská exonyms Pokud je k dispozici skript latinky, bude použit |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | polština                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Portugalština (Brazílie)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Portugalština (Portugalsko)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| PA – v      | Paňdžábština                 |       |       |         |     ✓    |                   |                |
| ro RO      | Rumunština               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | ruština                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Srbština (cyrilice)     |       |   SR-RS  |         |    SR-RS     |                   |                |
| sr-Latn-RS | Srbština (latinka)        |       |       |         |     SR-Latn    |                   |                |
| sk-SK      | Slovenština             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| sl-SL      | Slovinština              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Španělština                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | Španělština (Mexiko)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | švédština                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ta v      | tamilština (Indie)                 |       |       |         |     ✓    |                   |                |
| te v      | telugština (Indie)                 |       |       |         |     ✓    |                   |                |
| th TH      | Thajština                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | turečtina                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Ukrajinština               |       |    ✓   |         |     ✓    |                   |                |
| ur-PK      | Urdština                 |       |       |         |     ✓    |                   |                |
| uz-Latn-UZ | Uzbečtina                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Vietnamština             |       |    ✓   |         |      ✓    |                  |                |


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


Azure Maps parametr **zobrazení** (také označovaný jako "parametr oblasti uživatele") je dvě číslice země ISO-3166, která zobrazí správná mapování pro danou zemi nebo oblast, která určuje, která sada geopoliticky sporného obsahu se vrátí prostřednictvím služby Azure Maps, včetně ohraničení a popisků zobrazených na mapě. 

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
| MA           | Morocco (Moroccan View)                |   ✓   |   ✓     |     ✓          |
| OM           | Omán (zobrazení arabštiny)                    |   ✓   |        |     ✓          |
| PK           | Pákistán (zobrazení pákistánského Pákistánu)              |   ✓   |    ✓    |     ✓          |
| PS           | Palestinská samospráva (zobrazení arabštiny)    |   ✓   |        |     ✓          |
| QA           | Katar (zobrazení arabštiny)                   |   ✓   |        |     ✓          |
| SA           | Saúdská Arábie (zobrazení arabštiny)            |   ✓   |        |     ✓          |
| SY           | Sýrie (zobrazení arabštiny)                   |   ✓   |        |     ✓          |
| JE           | Jemen (zobrazení arabštiny)                   |   ✓   |        |     ✓          |
| Auto         | Vraťte data mapy na základě IP adresy žádosti.|   ✓   |    ✓   |     ✓          |
| Unifikace      | Sjednocené zobrazení (ostatní)                  |   ✓   |   ✓     |     ✓          |
