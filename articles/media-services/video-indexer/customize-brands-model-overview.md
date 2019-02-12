---
title: Přizpůsobení modelu značky v Video Indexer – Azure
titlesuffix: Azure Media Services
description: Tento článek poskytuje přehled o jaký je model značky v Video Indexer a jak ji přizpůsobit.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: b01de55003f620363ed27b95a2bd27bc39fdee3a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991205"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Přizpůsobení modelu značky v Video Indexer

Video Indexer podporuje značky zjišťování z pro zpracování řeči a vizuální textu během indexování a přeindexování z obrazový a zvukový obsah. Funkce detekce značka identifikuje zmínky produktů, služeb, a společnosti navrhl Bingu značky databáze. Například pokud Microsoft je uveden v videa nebo zvukový obsah nebo pokud se zobrazí ve visual textu ve videu, Video Indexer rozpozná jako značku v obsahu. Značky jsou jednoznačně rozlišit z jiné podmínky použití kontextu.

Detekce značky je užitečné pro širokou škálu obchodních scénářů, jako je například obsah archivu a zjišťování, kontextové reklamy, analýza sociálních médií, maloobchodní soutěžit analýzy a mnoho dalších. Detekce značky video Indexer umožňuje zmínky značky index pro zpracování řeči a vizuální text, pomocí Bingu značky databáze a přizpůsobení vytvořením vlastního modelu značky pro každý účet služby Video Indexer. Funkce modelu vlastní značky vám umožní vybrat, zda Video Indexer bude zjišťovat značky z Bingu značky databáze, vyloučit určité značky nebudou zjištěny (v podstatě vytváření černého seznam značek) a zahrnují značek, které by měla být součástí vaší model, který nemusí být v databázi značky Bingu (v podstatě vytvoří prázdný seznam značek).

## <a name="out-of-the-box-detection-example"></a>Nedostatek detekce příklad pole

V [2. den Microsoft Build 2017](https://www.videoindexer.ai/media/ed6ede78ad/) prezentace, značky "Microsoft Windows", zobrazí se více než jednou. Někdy v přepisu, někdy jako vizuální text a nikdy jako doslovný. Video Indexer zjistí s vysokou přesností, že termín, který je skutečně značky na základě kontextu, pokrývající více než 90k značky z pole a neustále aktualizuje. 02:25 Video Indexer zjistí značky z řeči a potom znovu na 02:40 z visual textu, které je součástí sady windows logo.

![Přehled značek](./media/content-model-customization/brands-overview.png)

Mluví o časových obdobích v kontextu konstrukce nerozpozná slovo "Windows" jako značek a stejné pole, Apple, Fox, atd., podle pokročilých algoritmů strojového učení, které už víte, jak k rozlišení z kontextu. Značka zjišťování funguje pro všechny naše podporované jazyky. Kliknutím sem získáte [úplná video s hlavním Vystoupením 2. den Microsoft Build 2017 a index](http://www.videoindexer.ai/media/ed6ede78ad/).

Chcete-li přineste vlastní značky, přečtěte si další kroky.

## <a name="next-steps"></a>Další postup

[Upravit značky modelu s použitím rozhraní API](customize-brands-model-with-api.md)

[Upravit značky model na webu](customize-brands-model-with-website.md)
