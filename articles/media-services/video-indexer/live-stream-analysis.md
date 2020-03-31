---
title: Analýza živého přenosu pomocí video indexeru
titleSuffix: Azure Media Services
description: Tento článek ukazuje, jak provést analýzu živého datového proudu pomocí video indexeru.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186001"
---
# <a name="live-stream-analysis-with-video-indexer"></a>Analýza živého přenosu pomocí videoindexeru

Azure Media Services Video Indexer je služba Azure navržená k extrahování podrobných informací z video a zvukových souborů offline. To je analyzovat daný mediální soubor již vytvořený předem. V některých případech použití je však důležité získat přehledy médií z živého kanálu co nejrychleji odemknout provozní a jiné případy použití stisknuté v čase. Například tato bohatá metadata v živém streamu mohou producenti obsahu používat k automatizaci televizní produkce.

Řešení popsané v tomto článku umožňuje zákazníkům používat Video Indexer v téměř reálném čase rozlišení na živé kanály. Zpoždění v indexování může být tak nízké, jak čtyři minuty pomocí tohoto řešení, v závislosti na bloky dat indexovaných, vstupní rozlišení, typ obsahu a výpočetní napájený používá pro tento proces.

![Metadata video indexeru v živém streamu](./media/live-stream-analysis/live-stream-analysis01.png)

*Obrázek 1 – Ukázkový přehrávač zobrazující metadata videoindexeru v živém streamu*

[Řešení analýzy datového proudu](https://aka.ms/livestreamanalysis) po ruce, používá Azure Functions a dvě logic apps ke zpracování živého programu z živého kanálu ve službě Azure Media Services pomocí Video Indexer a zobrazí výsledek s Azure Media Player zobrazující téměř v reálném čase výsledkem datového proudu.

Na vysoké úrovni se skládá ze dvou hlavních kroků. První krok běží každých 60 sekund a trvá podklip posledních 60 sekund přehrávaných, vytvoří z něj datový zdroj a indexuje jej prostřednictvím video indexeru. Pak druhý krok se nazývá po dokončení indexování. Zachycené přehledy jsou zpracovány, odeslány do Azure Cosmos DB a indexovaný dílčí klip se odstraní.

Ukázkový přehrávač přehrává živý přenos a získává přehledy z Azure Cosmos DB pomocí vyhrazené funkce Azure. Zobrazuje metadata a miniatury synchronizované s živým videem.

![Dvě aplikace logiky, které každou minutu zpracovávají živý přenos v cloudu](./media/live-stream-analysis/live-stream-analysis02.png)

*Obrázek 2 – dvě aplikace logiky zpracování živého přenosu každou minutu v cloudu.*

## <a name="step-by-step-guide"></a>Podrobný průvodce 

Úplný kód a podrobný návod k nasazení výsledků najdete v [projektu GitHub pro analýzu živých médií s Video Indexerem](https://aka.ms/livestreamanalysis). 

## <a name="next-steps"></a>Další kroky

[Přehled Video Indexeru](video-indexer-overview.md)
