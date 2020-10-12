---
title: Přizpůsobení modelu značek v Video Indexer – Azure
titleSuffix: Azure Media Services
description: Tento článek poskytuje přehled toho, co je model značek v Video Indexer a jak ho přizpůsobit.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: e0df698cb431e5e26c69047a9f464904f0546487
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89294233"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Přizpůsobení modelu značek v Video Indexer

Video Indexer podporuje rozpoznávání značek od mluvené řeči a vizuálního textu během indexování a přeindexování videa a zvukového obsahu. Funkce detekce značky identifikuje zmínky o produktech, službách a společnostech, které navrhla databáze značek Bingu. Například pokud se Microsoft zmiňuje v obrazovém nebo zvukovém obsahu nebo pokud se zobrazuje v vizuálním textu ve videu, Video Indexer ho detekuje jako značku v obsahu. Značky jsou nejednoznačné z jiných podmínek pomocí kontextu.

Detekce značek je užitečná v nejrůznějších podnikových scénářích, jako jsou obsah archivace a zjišťování, kontextové reklamy, analýza sociálních médií, analýza maloobchodních soutěží a spousta dalších. Video Indexer rozpoznávání značek vám umožní indexovat značky v podobě řeči a vizuálního textu pomocí služby Bingu a také pomocí přizpůsobení tím, že vytváří vlastní model značek pro každý účet Video Indexer. Funkce vlastního modelu značek umožňuje vybrat, jestli Video Indexer detekuje značky z databáze značek Bingu, vyloučí některé značky z detekce (v podstatě vytvoří seznam neschválených značek) a zahrne značky, které by měly být součástí vašeho modelu, které nemusí být v databázi značek Bingu (v podstatě vytváření seznamu schválených značek). Vlastní model značek, který vytvoříte, bude k dispozici pouze v účtu, ve kterém jste model vytvořili.

## <a name="out-of-the-box-detection-example"></a>Příklad detekce v poli

V prezentaci [Microsoft Build 2017 Day 2](https://www.videoindexer.ai/media/ed6ede78ad/) se značka "Microsoft Windows" objevuje víckrát. Někdy v přepisu, někdy jako vizuální text a nikdy jako doslovné. Video Indexer detekuje s vysokou přesností, že termín je ve skutečnosti značkou na základě kontextu, který překrývá přes 90k a průběžně aktualizuje. V 02:25 Video Indexer detekuje značku od rozpoznávání řeči a pak ji znovu v 02:40 z vizuálního textu, který je součástí loga Windows.

![Přehled značek](./media/content-model-customization/brands-overview.png)

V souvislosti se systémem Windows v kontextu konstrukce nebude rozpoznáno slovo "Windows" jako značka a totéž pro box, Apple, Fox atd., a to na základě pokročilých Machine Learning algoritmů, které znají, jak jednoznačně odstranit z kontextu. Detekce značek funguje pro všechny naše podporované jazyky. Kliknutím sem získáte [kompletní video a index Microsoft Build 2017 dne 2 vystoupení](https://www.videoindexer.ai/media/ed6ede78ad/).

Chcete-li uvést vlastní značky, Projděte si další kroky.

## <a name="next-steps"></a>Další kroky

[Přizpůsobení modelu značek pomocí rozhraní API](customize-brands-model-with-api.md)

[Přizpůsobení modelu značek pomocí webu](customize-brands-model-with-website.md)
