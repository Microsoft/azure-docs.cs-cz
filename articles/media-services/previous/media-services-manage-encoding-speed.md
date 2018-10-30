---
title: Správa rychlosti a souběžného zpracování vašeho kódování pomocí Azure Media Services | Dokumentace Microsoftu
description: Tento článek poskytuje stručný přehled o tom, jak můžete spravovat rychlosti a souběžného zpracování úlohy nebo úlohy kódování pomocí Azure Media Services.
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
ms.date: 10/29/2018
ms.author: juliako
ms.openlocfilehash: 3f358be630d9a50d0f048d66c9f79e168c8adcdf
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230195"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Správa rychlosti a souběžného zpracování vašeho kódování

Tento článek poskytuje stručný přehled o tom, jak můžete spravovat rychlosti a souběžného zpracování úlohy nebo úlohy kódování.

## <a name="overview"></a>Přehled

Ve službě Media Services **typu rezervované jednotky** určuje rychlost, pomocí kterého se zpracování vašich úloh zpracování médií. Můžete si vybrat mezi následujícími typy rezervovaných jednotek: **S1**, **S2** nebo **S3**. Například stejná úloha kódování bude rychlejší, když použijete typ rezervované jednotky **S2**, než kdybyste použili typ **S1**. [Škálování jednotek pro kódování](media-services-scale-media-processing-overview.md) téma ukazuje tabulku, která umožňuje rozhodování při výběru mezi různými rychlostmi kódování.

Kromě určení typu rezervované jednotky, můžete zadat účet zřídit s **rezervovaných jednotek**. Počet zřízených rezervovaných jednotek určuje počet úloh médií, které je možné v daném účtu zpracovávat současně. Například pokud má váš účet pak pět média úlohy poběží současně po dobu pěti rezervovaných jednotek, jako jsou úkoly ke zpracování. Ve zbývajících úkolech počká ve frontě a bude získat, vyberou se pro zpracování postupně, kdy spuštěná úloha dokončí. Pokud účet nemá žádné rezervované jednotky zřízené, pak úlohy neexistoval, použije postupně. V takovém případě dobu čekání mezi dokončení úkolů a další nazve spuštění bude záviset na dostupnost prostředků v systému.

Podrobné informace a příklady, které ukazují, jak škálovat jednotky kódování najdete v tématu [to](media-services-scale-media-processing-overview.md) tématu.

## <a name="next-step"></a>Další krok

[Jednotky škálování kódování](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

