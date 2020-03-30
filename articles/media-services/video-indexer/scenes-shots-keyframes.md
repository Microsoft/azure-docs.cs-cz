---
title: Video Indexer scény, záběry a klíčové snímky
titleSuffix: Azure Media Services
description: Toto téma poskytuje přehled scén, snímků a klíčových snímků videoindexeru.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: a833fd808049cfce95b182910e50e38d3c39f4e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245938"
---
# <a name="scenes-shots-and-keyframes"></a>Scény, snímky a klíčové snímky

Video Indexer podporuje segmentace videí do dočasných jednotek na základě strukturálních a sémantických vlastností. Tato funkce umožňuje zákazníkům snadno procházet, spravovat a upravovat video obsah na základě různých podrobností. Například na základě scén, snímků a klíčových snímků popsaných v tomto tématu.   

![Scény, snímky a klíčové snímky](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Detekce scény  
 
Video Indexer určuje, kdy se scéna změní ve videu na základě vizuálních podnětů. Scéna zobrazuje jednu událost a skládá se z řady po sobě jdoucích snímků, které jsou sémanticky příbuzné. Miniatura scény je prvním klíčovým snímkem jeho základního snímku. Indexer videa rozdělí video do scén na základě barevné soudržnosti mezi po sobě jdoucími snímky a načte počáteční a koncový čas každé scény. Detekce scény je považována za náročný úkol, protože zahrnuje kvantifikaci sémantických aspektů videí.

> [!NOTE]
> Platí pro videa, která obsahují alespoň 3 scény.

## <a name="shot-detection"></a>Detekce střel

Video Indexer určuje, kdy se snímek změní ve videu na základě vizuálních podnětů, sledováním náhlých i postupných přechodů v barevném schématu sousedních snímků. Metadata snímku zahrnují počáteční a koncový čas a také seznam klíčových snímků zahrnutých do tohoto snímku. Snímky jsou po sobě jdoucí snímky pořízené ze stejného fotoaparátu ve stejnou dobu.

## <a name="keyframe-detection"></a>Detekce klíčových snímků

Video Indexer vybere snímek,, který nejlépe reprezentuje každý snímek. Klíčové snímky jsou reprezentativní snímky vybrané z celého videa na základě estetických vlastností (například kontrastu a stabilní vlastnosti). Video Indexer načte seznam ID klíčových snímků jako součást metadat snímku, na základě kterých mohou zákazníci extrahovat klíčový snímek jako obraz s vysokým rozlišením.  

### <a name="extracting-keyframes"></a>Extrahování klíčových snímků

Chcete-li extrahovat klíčové snímky ve vysokém rozlišení pro video, musíte video nejprve nahrát a indexovat.

![Klíčových snímků](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>Na webu Video Indexer

Chcete-li extrahovat klíčové snímky pomocí webu Video Indexer, nahrajte a indexujte video. Po dokončení úlohy indexování klikněte na tlačítko **Stáhnout** a vyberte **artefakty (ZIP).** Tím se stáhne složka artefaktů do počítače. 

![Klíčových snímků](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Rozbalte a otevřete složku. Ve složce *_KeyframeThumbnail* najdete všechny klíčové snímky, které byly extrahovány z videa. 

#### <a name="with-the-video-indexer-api"></a>S rozhraním API videoindexeru

Chcete-li získat klíčové snímky pomocí rozhraní API pro indexer videa, nahrajte a indexujte video pomocí [videohovoru Nahrát.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?) Po dokončení úlohy indexování volejte [Get Video Index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?). To vám poskytne všechny poznatky, které Video Indexer extrahoval z vašeho obsahu v souboru JSON.  

Jako součást metadat každého snímku získáte seznam ID klíčových snímků. 

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

Nyní budete muset spustit každý z těchto klíčových id na [get thumbnails](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?) volání. Tím se stáhnou všechny obrázky klíčových snímků do počítače. 

## <a name="editorial-shot-type-detection"></a>Redakční detekce typu snímku

Klíčové snímky jsou spojeny s snímky ve výstupu JSON. 

Typ snímku spojený s jednotlivými výstřely v postřehy JSON představuje jeho redakční typ. Tyto vlastnosti typu snímku můžete považovat za užitečné při úpravách videí do klipů, upoutávek nebo při hledání určitého stylu klíčového snímku pro umělecké účely. Různé typy jsou určeny na základě analýzy prvního klíčového snímku každého snímku. Snímky jsou označeny měřítkem, velikostí a umístěním ploch, které se zobrazují v prvním klíčovém snímku. 

Velikost a měřítko snímku jsou určeny na základě vzdálenosti mezi kamerou a plochami, které se objevují v rámečku. Pomocí těchto vlastností video indexer detekuje následující typy záběrů:

* Široký: ukazuje celé tělo člověka.
* Střední: ukazuje osoby horní části těla a obličeje.
* Zblízka: hlavně ukazuje tvář člověka.
* Extrémní detail: zobrazuje obličej člověka, který vyplňuje obrazovku. 

Typy záběrů lze také určit podle umístění znaků předmětu vzhledem ke středu rámečku. Tato vlastnost definuje následující typy záběrů v video indexeru:

* Levá plocha: na levé straně rámečku se objeví osoba.
* Středová plocha: Osoba se zobrazí v centrální oblasti rámečku.
* Pravá tvář: na pravé straně rámečku se objeví osoba.
* Venkovní: osoba se objeví ve venkovním prostředí.
* Vnitřní: osoba se objeví ve vnitřním prostředí.

Další charakteristiky:

* Dva výstřely: ukazuje tváře dvou osob střední velikosti.
* Více tváří: více než dvě osoby.


## <a name="next-steps"></a>Další kroky

[Prohlédněte si výstup videoindexeru vytvořený rozhraním API](video-indexer-output-json-v2.md#scenes)
