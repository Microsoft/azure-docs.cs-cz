---
title: Azure Media Services živá událost obsahu a cloudového DVR | Dokumentace Microsoftu
description: Tento článek vysvětluje, co je Live výstup a jak pomocí cloudového DVR.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/14/2019
ms.author: juliako
ms.openlocfilehash: 4dd14587ec7e1473953981c1ef8c32c59eb9a1d6
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888518"
---
# <a name="using-a-cloud-dvr"></a>Použití cloudového DVR

A [Live výstup](https://docs.microsoft.com/rest/api/media/liveoutputs) vám umožňuje řídit vlastnosti odchozí živého datového proudu, například kolik datového proudu je zaznamenán (např. kapacita cloudového DVR) a zda prohlížeče můžete spustit sledování živého datového proudu. Vztah mezi **živá událost** a jeho **Live výstup**s je podobná tradičním televizní vysílání, kterým kanál (**živá událost**) představuje konstantu datový proud videa a nahrávání (**Live výstup**) působí na určité časové segmentů (například večer novinky od 18:30:00 do 19:00:00). Můžete zaznamenat televizoru pomocí záznamu pro digitální Video (DVR) – ekvivalentní funkce v živé události se spravuje přes vlastnost ArchiveWindowLength. Je formátu ISO 8601 časový interval doba trvání (například PTHH:MM:SS), která určuje kapacitu DVR a můžete nastavit na minimálně 3 minuty, které maximálně 25 hodin.

## <a name="live-output"></a>Live výstupu

**ArchiveWindowLength** hodnota určuje, jak daleko mohou zpět v čase a prohlížeč hledání od aktuální živé pozice.  **ArchiveWindowLength** také určuje, jak dlouho můžou růst manifesty klientů.

Předpokládejme, že jsou streamování hru fotbalového a má **ArchiveWindowLength** jenom 30 minut. Prohlížeč, který zahajuje sledování události 45 minut po spuštění hru můžete vyhledat zpět maximálně označit 15 minut. Vaše **Live výstup**s hry bude pokračovat až do **živá událost** zastavená, ale obsahu, který spadá mimo **ArchiveWindowLength** průběžně vyřazena z úložiště a je neobnovitelná. V tomto příkladu video mezi začátkem události a označit 15 minut by se možná vyprázdnila z vaší DVR a z kontejneru v úložišti objektů blob pro [Asset](https://docs.microsoft.com/rest/api/media/assets). Archiv se nedá vrátit zpátky a bude odebrán z kontejneru ve službě Azure blob storage.

Každý **Live výstup** je přidružený **Asset**, kterou používá pro záznam videa do kontejneru úložiště objektů blob v Azure přidružené. Publikování výstupu za provozu, musíte vytvořit **Lokátor streamování** pro daný **Asset**. Po vytvoření [Lokátor streamování](https://docs.microsoft.com/rest/api/media/streaminglocators), můžete vytvořit adresu URL streamování, který zadáte pro vaše uživatele.

A **živá událost** podporuje až tři souběžně běžících **Live výstup**s, takže můžete vytvořit maximálně 3 záznamy/archivů z jednoho živého datového proudu. To vám umožní podle potřeby publikovat a archivovat různé části události. Předpokládejme, že budete potřebovat k vysílání 24 x 7 živé lineární kanál a vytvořit různé programy v průběhu dne nabídnout zákazníkům jako obsah na vyžádání pro zobrazení můžete projít "záznam". V tomto scénáři vytvoříte nejprve primární výstup živého s krátkou archivačního okna 1 hodina nebo méně – Toto je primární živého datového proudu, který by vaši uživatelé naladit. Vytvořte **Lokátor streamování** pro tento **Live výstup** a publikujete ho vaše aplikace nebo webu jako kanál "Live". Zatímco **živá událost** je spuštěná, můžete prostřednictvím kódu programu vytvořit druhý souběžných **Live výstup** na začátku programu (nebo 5 minut, než již v rané fázi poskytují některé popisovače mají být odebrány později). Tento druhý **Live výstup** je možné odstranit 5 minut po ukončení programu. Pomocí této druhé **Asset**, můžete vytvořit nový **Lokátor streamování** publikovat tento program jako prostředek na vyžádání v katalogu vaší aplikace. Tento proces může opakovat více než jednou pro ostatní meze programů nebo vybraná vystoupení, které chcete sdílet jako videa na vyžádání, zatímco "Live" informačního kanálu z první **Live výstup** pokračuje k vysílání lineární kanál. 

> [!NOTE]
> **Live výstup**s spuštění při vytvoření a zastavení při odstranění. Když odstraníte **Live výstup**, nejsou odstranění podkladové **Asset** a obsahu v prostředku. 
>
> Pokud jste publikovali **Live výstup** pomocí asset **Lokátor streamování**, **živá událost** (až do délky okna DVR) bude dál zobrazit až **Lokátor streamování**na vypršení platnosti nebo odstranění, co nastane dříve.

## <a name="next-steps"></a>Další postup

- [Přehled živého streamování](live-streaming-overview.md)
- [Živé streamování kurz](stream-live-tutorial-with-api.md)

