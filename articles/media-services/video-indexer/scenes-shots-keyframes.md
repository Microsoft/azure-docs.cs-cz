---
title: Video Indexer scény, snímky a klíčové snímky
titleSuffix: Azure Media Services
description: Toto téma poskytuje přehled Video Indexer scény, snímků a klíčových snímků.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 248799d70e0741efcaea1714c12f4d92a42cef25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041900"
---
# <a name="scenes-shots-and-keyframes"></a>Scény, snímky a klíčové snímky

Video Indexer podporuje segmentace videí na dočasné jednotky na základě strukturálních a sémantických vlastností. Díky této možnosti můžou zákazníci snadno procházet, spravovat a upravovat svůj obsah videa na základě proměnlivých členitosti. Například na základě scén, snímků a klíčových snímků popsaných v tomto tématu.   

![Scény, snímky a klíčové snímky](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Detekce scény  
 
Video Indexer určuje, kdy se scény mění ve videu na základě vizuálních pomůcek. Scéna znázorňuje jednu událost a skládá se z řady po sobě jdoucích snímků, které jsou sémanticky spojeny. Miniatura scény je prvním snímkem svého základního snímku. Video indexer nakládá video na scény na základě provázanosti barev v po sobě jdoucích snímků a načítá počáteční a koncový čas každé scény. Detekce scény se považuje za náročný úkol, protože zahrnuje kvantifikaci sémantických aspektů videí.

> [!NOTE]
> Platí pro videa, která obsahují alespoň 3 scény.

## <a name="shot-detection"></a>Detekce snímku

Video Indexer určuje, kdy se snímek ve videu změní na základě vizuálních pomůcek, sledováním náhlého i postupnýho přechodu v barevném schématu sousedících snímků. Metadata snímku obsahují počáteční a koncový čas a také seznam klíčových snímků zahrnutých do tohoto snímku. Snímky jsou po sobě jdoucí snímky pořízené ze stejné kamery současně.

## <a name="keyframe-detection"></a>Detekce klíčových snímků

Video Indexer vybere rámce, které nejlépe reprezentují každý snímek. Klíčové snímky jsou reprezentativní snímky vybrané z celého videa na základě vlastností estetického zobrazení (například kontrast a stabilita). Video Indexer načte seznam ID klíčových snímků jako součást metadat snímku na základě toho, kteří zákazníci mohou klíčového snímku extrahovat jako obrázek s vysokým rozlišením.  

### <a name="extracting-keyframes"></a>Extrakce klíčových snímků

K extrakci klíčových snímků s vysokým rozlišením pro vaše video musíte nejprve nahrát video a indexovat.

![Klíčové snímky](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>S webem Video Indexer

K extrakci klíčových snímků pomocí Video Indexer webu Nahrajte video a zaindexujte ho. Po dokončení úlohy indexování klikněte na tlačítko **Stáhnout** a vyberte **artefakty (ZIP)**. Tato akce stáhne složku artefaktů do vašeho počítače. 

![Snímek obrazovky, který zobrazuje rozevírací seznam stáhnout s vybranými artefakty](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Rozbalte a otevřete složku. Ve složce *_KeyframeThumbnail* se nacházejí všechny klíčové snímky, které byly extrahovány z vašeho videa. 

#### <a name="with-the-video-indexer-api"></a>S rozhraním API Video Indexer

Pokud chcete získat klíčové snímky pomocí rozhraní Video Indexer API, nahrajte video a zaindexujte ho pomocí volání [Upload video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?) . Po dokončení úlohy indexování zavolejte [získat index videa](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?). Získáte tak všechny přehledy, které Video Indexer extrahovat z vašeho obsahu v souboru JSON.  

V rámci metadat každého snímku se zobrazí seznam ID klíčových snímků. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

Nyní bude nutné spustit každé z těchto ID klíčových snímků v volání metody [získat miniatury](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?) . Tím se do vašeho počítače stáhne všechny image klíčového snímku. 

## <a name="editorial-shot-type-detection"></a>Detekce typu redakčního snímku

Klíčové snímky jsou přidruženy k snímkům ve výstupním formátu JSON. 

Typ snímku přidružený k jednotlivému snímku ve formátu JSON Insights představuje jeho redakční typ. Tyto charakteristiky typu kopie jsou užitečné při úpravách videa do klipů, přípojných vozidel nebo při hledání konkrétního stylu klíčového snímku pro umělecké účely. Různé typy jsou určeny v závislosti na analýze prvního klíčového snímku každého snímku. Snímky jsou označeny měřítkem, velikostí a umístěním ploch zobrazených v prvním klíčovém snímku. 

Velikost a měřítko snímku se určují na základě vzdálenosti mezi fotoaparátem a ploškami, které se zobrazují v rámečku. Pomocí těchto vlastností Video Indexer detekuje následující typy snímku:

* Roztažitelné: zobrazuje tělo celé osoby.
* Střední: zobrazuje horní tělo a obličej osoby.
* Zavřít: hlavně zobrazuje obličej osoby.
* Extrémní sestavování: zobrazuje plochu osoby naplňující obrazovku. 

Typy snímků lze také určit umístěním znaků předmětu s ohledem na střed rámečku. Tato vlastnost definuje následující typy záběrů v Video Indexer:

* Levá strana: osoba se zobrazí na levé straně rámečku.
* Středový obličej: osoba se zobrazí v centrální oblasti rámce.
* Pravá strana: osoba se zobrazí na pravé straně rámečku.
* Venkovní: osoba se zobrazí v nastavení venku.
* Vnitřní: v nastavení vnitřníchy se zobrazí osoba.

Další vlastnosti:

* Dva snímky: zobrazuje stěny střední velikosti dvou osob.
* Více plošek: více než dvě osoby.


## <a name="next-steps"></a>Další kroky

[Kontrola výstupu Video Indexer vytvořeného rozhraním API](video-indexer-output-json-v2.md#scenes)
