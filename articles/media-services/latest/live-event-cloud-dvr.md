---
title: Použití časového posunu a živých výstupů k vytvoření přehrávání videa na vyžádání
titleSuffix: Azure Media Services
description: Tento článek popisuje, jak používat posun času a živé výstupy k záznamu živých datových proudů a vytváření přehrávání na vyžádání.
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
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: 4c7618b60e5fd86a9b8b3f22fb3333c00cfdfa61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74899800"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>Použití časového posunu a živých výstupů k vytvoření přehrávání videa na vyžádání

Ve službě Azure Media Services je objekt [živého výstupu](https://docs.microsoft.com/rest/api/media/liveoutputs) jako digitální videorekordér, který zachytí a zaznamená váš živý přenos do datového zdroje ve vašem účtu Mediálních služeb. Zaznamenaný obsah se uchovává do kontejneru definovaného prostředek [prostředků](https://docs.microsoft.com/rest/api/media/assets) asset (kontejner je v účtu Azure Storage připojené k vašemu účtu). Živý výstup také umožňuje řídit některé vlastnosti odchozího živého datového proudu, například kolik datového proudu je uloženo v záznamu archivu (například kapacita cloudového DVR) nebo když diváci mohou začít sledovat živý přenos. Archiv na disku je cyklický archiv "okno", který obsahuje pouze množství obsahu, který je zadán v **archiveWindowLength** vlastnost live výstup. Obsah, který spadá mimo toto okno, je automaticky zahozen z kontejneru úložiště a nelze jej obnovit. Hodnota archiveWindowLength představuje dobu trvání časovým rozpětí ISO-8601 (například PTHH:MM:SS), která určuje kapacitu DVR. Hodnotu lze nastavit od minimálně tří minut do maximálně 25 hodin.

Vztah mezi živou událostí a jejími živými výstupy je podobný tradičnímu televiznímu vysílání v tom, že kanál (Živá událost) představuje konstantní proud videa a záznam (Živý výstup) je vymezen na určitý časový segment (například večerní zprávy 18:30 AŽ 19:00). Jakmile budete mít datový proud proudící do živé události, můžete začít streamování události vytvořením datového zdroje, live výstup a streamování lokátoru. Živý výstup bude archivovat datový proud a zpřístupnit jej divákům prostřednictvím [koncového bodu streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints). Na živé události s různými délkami a nastaveními archivu můžete vytvořit více živých výstupů (maximálně tři). Informace o pracovním postupu živého streamování naleznete v části [Obecné kroky.](live-streaming-overview.md#general-steps)

## <a name="using-a-dvr-during-an-event"></a>Použití dvr během události

Tato část popisuje, jak používat DVR během události k řízení, jaké části datového proudu je k dispozici pro 'převinout zpět'.

Hodnota `archiveWindowLength` určuje, jak daleko zpět v čase může divák přejít z aktuální živé pozice. Hodnota `archiveWindowLength` také určuje, jak dlouho může růst manifestů klienta.

Předpokládejme, že streamujete fotbalový `ArchiveWindowLength` zápas a má pouze 30 minut. Divák, který začne sledovat vaši událost 45 minut po zahájení hry, může hledat zpět maximálně 15minutovou známku. Vaše živé výstupy pro hru budou pokračovat, dokud nebude živá událost zastavena. Obsah, který spadá mimo archiveWindowLength je průběžně zahozen z úložiště a je neobnovitelný. V tomto příkladu by bylo video mezi začátkem události a 15minutovou značkou vymazáno z vašeho DVR a z kontejneru v úložišti objektů blob pro prostředek. Archiv není obnovitelný a odebere se z kontejneru v úložišti objektů blob Azure.

Živá událost podporuje až tři souběžně spuštěné živé výstupy (můžete vytvořit maximálně 3 nahrávky / archivy z jednoho živého streamu současně). Tato podpora umožňuje publikovat a archivovat různé části události podle potřeby. Předpokládejme, že potřebujete vysílat 24x7 živý lineární kanál a vytvářet "nahrávky" různých programů po celý den, které budou zákazníkům nabízeny jako obsah na vyžádání pro sledování. V tomto scénáři nejprve vytvoříte primární živý výstup s krátkým archivem 1 hodinu nebo méně – toto je primární živý přenos, který by diváci naladili. Pro tento živý výstup byste vytvořili lokátor streamování a publikovali byste ho ve své aplikaci nebo na webu jako kanál "Live". Během spuštění živé události můžete programově vytvořit druhý souběžný živý výstup na začátku programu (nebo o 5 minut dříve, abyste mohli poskytnout některé úchyty k oříznutí později). Tento druhý živý výstup lze odstranit 5 minut po ukončení programu. Pomocí tohoto druhého datového zdroje můžete vytvořit nový lokátor streamování a publikovat tento program jako datový zdroj na vyžádání v katalogu vaší aplikace. Tento proces můžete opakovat vícekrát pro jiné hranice programu nebo zvýraznění, které chcete sdílet jako videa na vyžádání, a to vše při vysílání kanálu "Live" z prvního živého výstupu v lineárním přenosu.

## <a name="creating-an-archive-for-on-demand-playback"></a>Vytvoření archivu pro přehrávání na vyžádání

Datový zdroj, který živý výstup archivuje, se automaticky stane datovým zdrojem na vyžádání při odstranění živého výstupu. Před zastavením živé události je nutné odstranit všechny živé výstupy. Můžete použít volitelný příznak [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) automaticky odebrat živé výstupy na stop.

I po zastavení a odstranění události mohou uživatelé streamovat archivovaný obsah jako video na vyžádání, pokud datový zdroj neodstraníte. Datový zdroj by neměl být odstraněn, pokud je používán událostí; událost musí být nejprve odstraněna.

Pokud jste zveřejnili datový zdroj živého výstupu pomocí lokátoru streamování, živá událost (až do délky okna DVR) bude i nadále zobrazitelná, dokud nevyprší nebo smaže lokátor streamování, podle toho, co nastane dříve.

Další informace naleznete v tématu:

- [Přehled živého streamování](live-streaming-overview.md)
- [Živé streamování tutorial](stream-live-tutorial-with-api.md)

> [!NOTE]
> Když odstraníte živý výstup, neodstraníte podkladový datový zdroj a obsah v datovém zdroji.

## <a name="next-steps"></a>Další kroky

* [Subclip vaše videa](subclip-video-rest-howto.md).
* [Definujte filtry pro své datové zdroje](filters-dynamic-manifest-rest-howto.md).
