---
title: Přizpůsobení modelu značek v video indexeru – Azure
titleSuffix: Azure Media Services
description: Tento článek poskytuje přehled o tom, co je model značky v Video Indexer a jak jej přizpůsobit.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: ca3d825fb2f4184448cc279d9408f47ad4ad004a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838362"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Přizpůsobení modelu značek v videoindexeru

Video Indexer podporuje detekci značky z řeči a vizuálního textu během indexování a reindexingu video a audio obsahu. Funkce detekce značek identifikuje zmínky o produktech, službách a společnostech navržených databází značek Bingu. Pokud je například společnost Microsoft uvedena ve videonebo zvukovém obsahu nebo pokud se ve videu zobrazuje ve vizuálním textu, video indexer jej v obsahu rozpozná jako značku. Značky jsou vyloučeny z jiných termínů pomocí kontextu.

Detekce značky je užitečná v široké škále obchodních scénářů, jako je archiv a zjišťování obsahu, kontextová reklama, analýza sociálních médií, analýza maloobchodních soutěžit a mnoho dalších. Detekce značky Video Indexer umožňuje indexovat zmínky o značce v řeči a vizuálním textu pomocí databáze značek Bing u také s přizpůsobením vytvořením vlastního modelu značek pro každý účet Video Indexer. Funkce modelu vlastní značky umožňuje vybrat, zda video indexer detekuje značky z databáze značek Bing, vyloučí určité značky z detekce (v podstatě vytváří černou listinu značek) a zahrnuje značky, které by měly být součástí vašeho model, který nemusí být v databázi značek Bingu (v podstatě vytvoření bílého seznamu značek). Vlastní značky modelu, který vytvoříte bude k dispozici pouze v účtu, ve kterém jste vytvořili model.

## <a name="out-of-the-box-detection-example"></a>Příklad detekce po vybalení

V microsoft [buildu 2017 den 2](https://www.videoindexer.ai/media/ed6ede78ad/) prezentace, značka "Microsoft Windows" se zobrazí vícekrát. Někdy v přepisu, někdy jako vizuální text a nikdy tak doslovně. Video Indexer detekuje s vysokou přesností, že termín je skutečně značka založená na kontextu, pokrývající více než 90k značek po vybalení z krabice, a neustále aktualizuje. V 02:25, Video Indexer detekuje značku z řeči a pak znovu v 02:40 z vizuálního textu, který je součástí loga windows.

![Přehled značek](./media/content-model-customization/brands-overview.png)

Mluvit o oknech v kontextu výstavby nedetekuje slovo "Windows" jako značku a totéž pro Box, Apple, Fox atd., založené na pokročilých algoritmech strojového učení, které vědí, jak rozptýlit kontext. Detekce značky funguje pro všechny naše podporované jazyky. Klikněte zde pro [plné Microsoft Build 2017 Den 2 programovém prohlášení video a index](https://www.videoindexer.ai/media/ed6ede78ad/).

Chcete-li přinést své vlastní značky, podívejte se na další kroky.

## <a name="next-steps"></a>Další kroky

[Přizpůsobení modelu značek pomocí api](customize-brands-model-with-api.md)

[Přizpůsobení modelu značek pomocí webu](customize-brands-model-with-website.md)
