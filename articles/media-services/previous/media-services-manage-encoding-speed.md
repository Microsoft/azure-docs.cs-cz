---
title: Správa rychlosti a souběžnosti kódování pomocí Azure Media Services | Dokumenty společnosti Microsoft
description: Tento článek poskytuje stručný přehled o tom, jak můžete spravovat rychlost a souběžnost úlohy nebo úlohy kódování pomocí Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6bcaadc8dd61899aff860ad246e30170c99ec0f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463748"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Správa rychlosti a souběžného zpracování vašeho kódování  

Tento článek poskytuje stručný přehled o tom, jak můžete spravovat rychlost a souběžnost úlohy nebo úlohy kódování.

## <a name="overview"></a>Přehled

Ve službě Media Services určuje **typ rezervované jednotky** rychlost zpracování úloh zpracování médií. Můžete si vybrat mezi následujícími typy rezervovaných jednotek: **S1**, **S2** nebo **S3**. Například stejná úloha kódování bude rychlejší, když použijete typ rezervované jednotky **S2**, než kdybyste použili typ **S1**. Téma [jednotky kódování měřítka](media-services-scale-media-processing-overview.md) zobrazuje tabulku, která vám pomůže při rozhodování při výběru mezi různými rychlostmi kódování.

Kromě určení typu rezervované jednotky můžete zadat zřízení účtu pomocí **rezervovaných jednotek**. Počet zřízených rezervovaných jednotek určuje počet úloh médií, které je možné v daném účtu zpracovávat současně. Pokud má například váš účet pět rezervovaných jednotek, bude současně spuštěno pět úloh médií, pokud budou zpracovány úlohy. Zbývající úkoly budou čekat ve frontě a budou po dokončení spuštěné úlohy vyzvednuty pro zpracování postupně. Pokud účet nemá žádné rezervované jednotky zřízena, pak úkoly budou vyzvednuty postupně. V takovém případě bude čekací doba mezi dokončením jednoho úkolu a dalším zahájením záviset na dostupnosti prostředků v systému.

Podrobné informace a příklady, které ukazují, jak škálovat jednotky kódování, naleznete v [tomto](media-services-scale-media-processing-overview.md) tématu.

## <a name="next-step"></a>Další krok

[Jednotky kódování měřítka](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

