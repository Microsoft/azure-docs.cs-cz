---
title: Spravovat rychlost a souběžnost kódování službou Azure Media Services | Microsoft Docs
description: Tento článek poskytuje stručný přehled o tom, jak můžete spravovat rychlost a souběžnost kódování úlohy nebo úkoly službou Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: juliako
ms.openlocfilehash: d7e3d6d0c176d0a903c3027ab4feddb332557566
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788274"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Spravovat rychlost a souběžnost vaše kódování

Tento článek poskytuje stručný přehled o tom, jak můžete spravovat rychlost a souběžnosti kódování úlohy a úkoly.

## <a name="overview"></a>Přehled

Ve službě Media Services **vyhrazený typ jednotky** určuje rychlost, ke které se zpracovávají médiu zpracování úlohy. Můžete si vybrat mezi následujícími typy rezervovaných jednotek: **S1**, **S2** nebo **S3**. Například stejná úloha kódování bude rychlejší, když použijete typ rezervované jednotky **S2**, než kdybyste použili typ **S1**. [Škálování kódování jednotky](media-services-scale-media-processing-overview.md) téma ukazuje tabulku, která pomáhá zajistit rozhodnutí při výběru mezi různými rychlostmi kódování.

Kromě určení typu jednotku rezervovanou, můžete zadat ke zřízení účtu s **jednotek rezervovaných**. Počet zřízených rezervovaných jednotek určuje počet úloh médií, které je možné v daném účtu zpracovávat současně. Například pokud potom pět média úlohy bude běžet současně, pokud má váš účet jednotky rezervované pro pět, jako jsou úlohy, které mají být zpracovány. Ve zbývajících úkolech bude čekat ve frontě a bude získat zachyceny pro zpracování postupně po dokončení spuštěná úloha. Pokud účet nemá žádné jednotky rezervované pro zřízení, pak úlohy bude být zachyceny postupně. V takovém případě dobu čekání mezi jeden úkol nepřipojujte a další jeden počáteční bude záviset na dostupnost prostředků v systému.

Podrobné informace a příklady, které ukazují, jak kódování jednotek škálování najdete v tématu [to](media-services-scale-media-processing-overview.md) tématu.

## <a name="next-step"></a>Další krok

[Kódování jednotky škálování](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

