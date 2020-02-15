---
title: Podpora lokalizace | Mapy Microsoft Azure
description: V tomto článku se dozvíte o podporovaných jazycích pro služby v Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5de04b4e37e79a47f89bb235aad2b0f9cab69e6a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208144"
---
# <a name="localization-support-in-azure-maps"></a>Podpora lokalizace v Azure Maps

Azure Maps podporuje různé jazyky a zobrazení založené na zemi nebo oblasti. Tento článek poskytuje podporované jazyky a zobrazení, které vám pomůžou s implementací Azure Maps.


## <a name="azure-maps-supported-languages"></a>Azure Maps podporované jazyky

Azure Maps byly lokalizovány do různých jazyků v rámci svých služeb. Následující tabulka uvádí podporované kódy jazyků pro každou službu.  
  

| ID         | Název                   |  Maps | Hledat | Směrování | Počasí | Incidenty provozu | JS – ovládací prvek mapy |
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
| nl-BE      | Holandština (Belgie)        |       |    ✓   |         |      ✓    |                   |                |
| NL-NL      | Nizozemština (Nizozemsko)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| cs AU      | angličtina (Austrálie)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| cs NZ      | angličtina (Nový Zéland)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Angličtina (Velká Británie) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-US      | Angličtina (USA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et – EE      | Estonština               |       |    ✓   |         |      ✓    |         ✓         |                |
| náhl-PH     | Filipínština               |       |       |         |     ✓    |                   |                |
| fi-FI      | Finština                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Francouzština                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | Francouzština (Kanada)      |       |    ✓   |         |     ✓     |                   |                |
| HK – ES      | Galicijština               |       |    ✓   |         |         |                   |                |
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
| KN-IN      | Kannada                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Kazaština                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Korejština                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Latinskoamerická španělština |       |    ✓   |         |         |                   |                |
| lv-LV      | Lotyština                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Litevština             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| MK-MK      | Makedonie             |       |       |         |     ✓    |                   |                |
| Moje MS      | Malajština (latinka)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| mr-IN      | Maráthština                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norština, Bokmål       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Neutrální uzemněné – úřední jazyky pro všechny oblasti v místních skriptech, pokud jsou k dispozici |   ✓     |        |         |       |        |      ✓          |
| NGT – Latn   | Neutrální uzemnění – Latinská exonyms Pokud je k dispozici skript latinky, bude použit |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | polština                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Portugalština (Brazílie)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Portugalština (Portugalsko)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| PA – v      | Paňdžábština                 |       |       |         |     ✓    |                   |                |
| ro RO      | Rumunština               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Ruština                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Srbština (cyrilice)     |       |   SR-RS  |         |    SR-RS     |                   |                |
| SR-Latn-RS | Srbština (latinka)        |       |       |         |     SR-Latn    |                   |                |
| sk-SK      | Slovenština             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| sl-SL      | Slovinština              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Španělština                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | Španělština (Mexiko)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | švédština                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ta v      | tamilština (Indie)                 |       |       |         |     ✓    |                   |                |
| te v      | telugština (Indie)                 |       |       |         |     ✓    |                   |                |
| th TH      | Thajština                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | turečtina                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| Spojené království – UA      | Ukrajinština               |       |    ✓   |         |     ✓    |                   |                |
| ur-PK      | Urdština                 |       |       |         |     ✓    |                   |                |
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
>  Ujistěte se, že jste nastavili parametr **zobrazení** podle potřeby a máte nejnovější verzi sady web SDK a Android SDK. Ovlivněné sady SDK:
>
>    * Azure Maps Web SDK
>    * Azure Maps Android SDK

Ve výchozím nastavení je parametr zobrazení nastavený na **sjednocené**, i když jste ho v žádosti nedefinovali. Určete umístění vašich uživatelů. Potom nastavte pro toto umístění parametr **zobrazení** správně. Případně můžete nastavit možnost zobrazit = automaticky, která vrátí data mapy na základě IP adresy žádosti.  Parametr **zobrazení** v Azure Maps musí být použit v souladu s platnými zákony, včetně zákonů o mapování země, kde jsou k dispozici mapy, obrázky a další data a obsah třetích stran, ke kterým máte oprávnění pro přístup prostřednictvím Azure Maps.


Následující tabulka poskytuje podporovaná zobrazení.

| Zobrazení         | Popis                            |  Maps | Hledat | Ovládací prvek Mapa JS |
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
| Autom.         | Vraťte data mapy na základě IP adresy žádosti.|   ✓   |    ✓   |     ✓          |
| Sjednocen      | Sjednocené zobrazení (ostatní)                  |   ✓   |   ✓     |     ✓          |
