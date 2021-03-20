---
title: Analýza živého datového proudu pomocí Video Indexer
titleSuffix: Azure Media Services
description: Tento článek ukazuje, jak provést analýzu živého datového proudu pomocí Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "74186001"
---
# <a name="live-stream-analysis-with-video-indexer"></a>Analýza živého datového proudu pomocí Video Indexer

Azure Media Services Video Indexer je služba Azure, která je určená k extrakci podrobného přehledu z video a zvukových souborů v režimu offline. Toto je analýza daného mediálního souboru již vytvořeného předem. Nicméně v některých případech je důležité získat z živého informačního kanálu mediální přehledy, abyste mohli rychle uvolnit provozní a jiné případy použití v čase. Například taková rozsáhlá metadata živého streamu můžou použít výrobci obsahu k automatizaci produkčního prostředí.

Řešení popsané v tomto článku umožňuje zákazníkům používat Video Indexer řešení téměř v reálném čase pro živé kanály. Zpoždění při indexování může být pomocí tohoto řešení menší než čtyři minuty, v závislosti na datových blocích indexovaných dat, rozlišení vstupu, typu obsahu a výpočetním prostředí, které se používá pro tento proces.

![Metadata Video Indexer v živém streamu](./media/live-stream-analysis/live-stream-analysis01.png)

*Obrázek 1 – vzorový přehrávač zobrazující metadata Video Indexer v živém streamu*

[Řešení pro analýzu streamování](https://aka.ms/livestreamanalysis) používá Azure functions a dvě Logic Apps ke zpracování živého programu z živého kanálu v Azure Media Services s video indexer a výsledek zobrazuje Azure Media Player, který zobrazuje výsledný stream téměř v reálném čase.

Na vysoké úrovni se skládá ze dvou hlavních kroků. První krok se spustí každých 60 sekund a provede dílčí klip za posledních 60 sekund, vytvoří z něho Asset a indexuje ho pomocí Video Indexer. Druhý krok se pak volá po dokončení indexování. Zachycené přehledy jsou zpracovávány, odesílány do Azure Cosmos DB a dílčí klip indexovaný se odstraní.

Ukázkový přehrávač hraje živý datový proud a získá přehledy z Azure Cosmos DB pomocí vyhrazené funkce Azure Functions. Zobrazuje metadata a miniatury synchronizované s živým videem.

![Dvě aplikace logiky zpracovávají proud živého vysílání každou minutu v cloudu.](./media/live-stream-analysis/live-stream-analysis02.png)

*Obrázek 2 – tyto dvě aplikace logiky zpracovávají proud živého vysílání každou minutu v cloudu.*

## <a name="step-by-step-guide"></a>Podrobný průvodce 

Úplný kód a podrobný průvodce nasazením výsledků najdete v [projektu GitHubu pro analýzu živých médií pomocí video indexer](https://aka.ms/livestreamanalysis). 

## <a name="next-steps"></a>Další kroky

[Přehled Video Indexeru](video-indexer-overview.md)
