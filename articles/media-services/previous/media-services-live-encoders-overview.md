---
title: Konfigurace místních kodérů při použití služby Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi | Dokumentace Microsoftu
description: Toto téma obsahuje seznam s místními kodéry, které můžete použít k živé události zaznamenávat a odesílat živý datový proud s jednou přenosovou rychlostí do AMS kanálů, (, které jsou živé kódování, povolené) pro další zpracování. Téma obsahuje odkazy na kurzy, které ukazují, jak nakonfigurovat uvedené kodérů.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: juliako
ms.openlocfilehash: c0c7e2b8962cb757141b654c2956ed3ff5600c88
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53787643"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Konfigurace místních kodérů při použití služby Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi
Toto téma obsahuje seznam s místními kodéry, které můžete použít k živé události zaznamenávat a odesílat živý datový proud s jednou přenosovou rychlostí do AMS kanálů, (, které jsou živé kódování, povolené) pro další zpracování. Téma obsahuje také odkazy na kurzy, které ukazují, jak nakonfigurovat uvedené kodérů.

> [!NOTE]
> Při streamování přes RTMP, zkontrolujte nastavení brány firewall nebo proxy serveru pro potvrzení, že jsou otevřené odchozí porty TCP 1935 a 1936.

## <a name="flash-media-live-encoder"></a>Flash Media Live Encoder
Informace o tom, jak nakonfigurovat [Flash Media Live Encoder](http://www.adobe.com/products/flash-media-encoder.html) naleznete v tématu encoder (FMLE) odesílat živý datový proud s jednou přenosovou rychlostí do kanálu AMS [konfigurace FMLE](media-services-configure-fmle-live-encoder.md).

## <a name="haivision-kb-encoder"></a>Kodér Haivision KB
Informace o tom, jak nakonfigurovat [kodér Haivision KB](https://www.haivision.com/products/kb-series/) naleznete v tématu kodér odesílat živý datový proud s jednou přenosovou rychlostí do kanálu AMS [konfigurace kodér Haivision KB](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Telestream Wirecast
Informace o tom, jak nakonfigurovat [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) naleznete v tématu kodér odesílat živý datový proud s jednou přenosovou rychlostí do kanálu AMS [konfigurace Wirecast](media-services-configure-wirecast-live-encoder.md).

## <a name="newtek-tricaster"></a>Kodér NewTek TriCaster
Informace o tom, jak nakonfigurovat [Tricaster](http://newtek.com/products/tricaster-40.html) naleznete v tématu kodér odesílat živý datový proud s jednou přenosovou rychlostí do kanálu AMS [konfigurace Tricaster](media-services-configure-tricaster-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
Další informace najdete v tématu [Elemental Live](http://www.elementaltechnologies.com/products/elemental-live).

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další postup

[Živé streamování využívající službu Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi](media-services-manage-live-encoder-enabled-channels.md).

