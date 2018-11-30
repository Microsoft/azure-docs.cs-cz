---
title: Azure Media Services Livestream obsahu a cloudového DVR | Dokumentace Microsoftu
description: Tento článek vysvětluje, co je LiveOutput a jak pomocí cloudového DVR.
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
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 5de7496d73ebe1c89ce27ef27df73b197f34e7c7
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642288"
---
# <a name="using-a-cloud-dvr"></a>Použití cloudového DVR

A [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) vám umožňuje řídit vlastnosti odchozí živého datového proudu, například kolik datového proudu je zaznamenán (např. kapacita cloudového DVR) a zda prohlížeče můžete spustit sledování živého datového proudu. Vztah mezi **Livestream** a jeho **LiveOutput**vztah s je podobná tradičním televizní vysílání, kterým kanál (**Livestream**) představuje nepřetržitý datový proud videa a nahrávání (**LiveOutput**) působí na určité časové segmentů (například večer novinky od 18:30:00 do 19:00:00). Můžete zaznamenat televizoru pomocí záznamu pro digitální Video (DVR) – ekvivalentní funkce v LiveEvents se spravuje přes vlastnost ArchiveWindowLength. Je formátu ISO 8601 časový interval doba trvání (například PTHH:MM:SS), která určuje kapacitu DVR a můžete nastavit na minimálně 3 minuty, které maximálně 25 hodin.

## <a name="liveoutput"></a>LiveOutput

**ArchiveWindowLength** hodnota určuje, jak daleko mohou zpět v čase a prohlížeč hledání od aktuální živé pozice.  **ArchiveWindowLength** také určuje, jak dlouho můžou růst manifesty klientů.

Předpokládejme, že jsou streamování hru fotbalového a má **ArchiveWindowLength** jenom 30 minut. Prohlížeč, který zahajuje sledování události 45 minut po spuštění hru můžete vyhledat zpět maximálně označit 15 minut. Vaše **LiveOutput**s hry bude pokračovat až do **Livestream** zastavená, ale obsahu, který spadá mimo **ArchiveWindowLength** průběžně vyřazena z úložiště a je neobnovitelná. V tomto příkladu video mezi začátkem události a označit 15 minut by se možná vyprázdnila z vaší DVR a z kontejneru v úložišti objektů blob pro [Asset](https://docs.microsoft.com/rest/api/media/assets). Archiv se nedá vrátit zpátky a bude odebrán z kontejneru ve službě Azure blob storage.

Každý **LiveOutput** je přidružený **Asset**, kterou používá pro záznam videa do kontejneru úložiště objektů blob v Azure přidružené. K publikování LiveOutput, musíte vytvořit **StreamingLocator** pro daný **Asset**. Po vytvoření [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), můžete vytvořit adresu URL streamování, který zadáte pro vaše uživatele.

A **Livestream** podporuje až tři souběžně běžících **LiveOutput**s, takže můžete vytvořit maximálně 3 záznamy/archivů z jednoho živého datového proudu. To vám umožní podle potřeby publikovat a archivovat různé části události. Předpokládejme, že budete potřebovat k vysílání 24 x 7 živé lineární kanál a vytvořit různé programy v průběhu dne nabídnout zákazníkům jako obsah na vyžádání pro zobrazení můžete projít "záznam". V tomto scénáři je nejprve vytvořit primární LiveOutput s krátkou archivačního okna 1 hodina nebo méně – Toto je primární živého datového proudu, který by vaši uživatelé naladit. Vytvoříte **StreamingLocator** to **LiveOutput** a publikujete ho vaše aplikace nebo webu jako kanál "Live". Zatímco **Livestream** je spuštěná, můžete prostřednictvím kódu programu vytvořit druhý souběžných **LiveOutput** na začátku programu (nebo 5 minut, než již v rané fázi poskytují některé popisovače mají být odebrány později). Tento druhý **LiveOutput** je možné odstranit 5 minut po ukončení programu. Pomocí této druhé **Asset**, můžete vytvořit nový **StreamingLocator** publikovat tento program jako prostředek na vyžádání v katalogu vaší aplikace. Tento proces může opakovat více než jednou pro ostatní meze programů nebo vybraná vystoupení, které chcete sdílet jako videa na vyžádání, zatímco "Live" informačního kanálu z první **LiveOutput** pokračuje k vysílání lineární kanál. 

> [!NOTE]
> **LiveOutput**s spuštění při vytvoření a zastavení při odstranění. Když odstraníte **LiveOutput**, nejsou odstranění podkladové **Asset** a obsahu v prostředku.  

## <a name="next-steps"></a>Další postup

- [Přehled živého streamování](live-streaming-overview.md)
- [Živé streamování kurz](stream-live-tutorial-with-api.md)

