---
title: Video Indexer scény, snímků a klíčových snímků – Azure
titlesuffix: Azure Media Services
description: Toto téma poskytuje přehled Video Indexer scény, snímků a klíčových snímků.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: cdabc1b6bfed519098f656710ef49a946e676cf2
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815650"
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

Vybere rámce, které nejlépe reprezentují snímek. Klíčové snímky jsou reprezentativní snímky vybrané z celého videa na základě vlastností estetického zobrazení (například kontrast a stabilita). Video Indexer načte seznam ID klíčových snímků jako součást metadat snímku na základě toho, kteří zákazníci mohou miniatury klíčového snímku extrahovat. 

Klíčové snímky jsou přidruženy k snímkům ve výstupním formátu JSON. 

## <a name="next-steps"></a>Další postup

[Kontrola výstupu Video Indexer vytvořeného rozhraním API](video-indexer-output-json-v2.md#scenes)
