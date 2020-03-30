---
title: Konfigurace místních kodérů při použití Služby Azure Media Services k vytváření datových proudů s více přenosovými rychlostmi | Dokumenty společnosti Microsoft
description: Toto téma uvádí místní živé kodéry, které můžete použít k zachycení živých událostí a odeslání jednoho datového toku živého datového proudu do kanálů AMS (které jsou povoleno živé kódování) pro další zpracování. Téma odkazuje na kurzy, které ukazují, jak nakonfigurovat uvedené kodéry.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 71a31228602ef161158eaa05c80d50f65de98a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77133267"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Jak nakonfigurovat místní kodéry při použití Azure Media Services k vytváření datových proudů s více přenosovými rychlostmi
Toto téma uvádí místní živé kodéry, které můžete použít k zachycení živých událostí a odeslání jednoho datového toku živého datového proudu do kanálů AMS (které jsou povoleno živé kódování) pro další zpracování. Toto téma také odkazuje na kurzy, které ukazují, jak konfigurovat uvedené kodéry.

> [!NOTE]
> Při streamování přes RTMP zkontrolujte nastavení brány firewall nebo proxy serveru, aby se zajistilo, že jsou otevřené odchozí porty TCP 1935 a 1936.

## <a name="haivision-kb-encoder"></a>Kodér Haivision KB
Informace o konfiguraci [kodéru kodéru kodére Haivision KB](https://www.haivision.com/products/kb-series/) pro odesílání jednoho datového toku živého datového proudu na kanál AMS naleznete [v tématu Konfigurace kodéru Haivision KB Encoder](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Telestream Wirecast
Informace o konfiguraci kodéru [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) pro odesílání jednoho datového proudu datového toku do kanálu AMS naleznete v [tématu Konfigurace wirecastu](media-services-configure-wirecast-live-encoder.md).

## <a name="newtek-tricaster"></a>NewTek TriCaster
Informace o konfiguraci [kodéru Tricaster](https://newtek.com/products/tricaster-40.html) pro odesílání jednoho datového toku živého datového proudu do kanálu AMS naleznete v [tématu Konfigurace trikastru](media-services-configure-tricaster-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
Další informace naleznete v tématu [Elemental Live](https://www.elementaltechnologies.com/products/elemental-live).

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky

[Živé streamování pomocí Služby Azure Media Services k vytváření datových proudů s více přenosovými rychlostmi](media-services-manage-live-encoder-enabled-channels.md).

