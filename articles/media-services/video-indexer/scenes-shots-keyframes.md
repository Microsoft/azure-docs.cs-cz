---
title: Video Indexer scény, snímky a klíčové snímky – Azure
titlesuffix: Azure Media Services
description: Toto téma obsahuje přehled funkce Video Indexer scény, snímky a klíčové snímky.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: dfa41dc695cf6ab357a9cd4cdbd32454b6dd107d
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896311"
---
# <a name="scenes-shots-and-keyframes"></a>Scény, snímky a klíčové snímky

Video Indexer podporuje rozdělení videa do dočasné jednotky na základě strukturální a sémantickou vlastností. Tato funkce umožňuje zákazníkům snadno procházet, spravovat a upravovat obsah videa podle různých přírůstcích. Například na základě scény, snímky a klíčové snímky, které jsou popsané v tomto tématu. **Scény detekce** funkce je momentálně ve verzi preview.   

![Scény, snímky a klíčové snímky](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)

## <a name="scene-detection-preview"></a>Rozpoznávání scény (preview)

Video Indexer Určuje změnu scény ve videu podle vizuální prvky. Znázorňuje scény jedna událost a skládá se z řadě po sobě jdoucích snímků, které jsou sémanticky související. Miniatura scény je první keyframe jeho základní snímek. Video indexer segmenty videa do scény podle barvy koherence mezi po sobě jdoucích snímků a načte začátek a konec jednotlivých scény. Rozpoznávání scény se považuje za náročným úkolem zahrnuje kvantifikace sémantické aspektů videa.

> [!NOTE]
> Vztahuje se na videa, které obsahují alespoň 3 scény.

## <a name="shot-detection"></a>Detekce snímku

Video Indexer Určuje, kdy snímek změny ve videu podle vizuálních podnětů, sledováním náhlé a postupné přechodů v barevném schématu sousední snímků. Na snímku metadata obsahují počáteční a koncový čas, jakož i seznam klíčové snímky, které jsou součástí tohoto snímku. Snímky jsou přijata ze stejné fotoaparátu/kamery ve stejnou dobu po sobě jdoucích snímků.

## <a name="keyframe-detection"></a>Detekce klíčový snímek

Vybere snímků, které nejlépe vystihnou na snímku. Klíčové snímky jsou reprezentativní snímky vybrat z celé video na základě aesthetic vlastností (například kontrast a stableness). Video Indexer načte seznam klíčový snímek ID jako součást metadat na snímku, podle kterých zákazníků může extrahovat miniaturu klíčový snímek. 

Klíčové snímky jsou přidružené snímky ve výstupu JSON. 

## <a name="next-steps"></a>Další postup

[Prozkoumání výstupu funkce Video Indexer vytvořené metodou rozhraní API](video-indexer-output-json-v2.md#scenes)