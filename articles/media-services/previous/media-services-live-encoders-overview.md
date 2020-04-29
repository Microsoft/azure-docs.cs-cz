---
title: Konfigurace místních kodérů při použití Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi | Microsoft Docs
description: Toto téma obsahuje seznam místních kodérů, které můžete použít k zachycení živých událostí a odeslání živého datového proudu s jednou přenosovou rychlostí do kanálů AMS (živé kódování povoleno) k dalšímu zpracování. Téma obsahuje odkazy na kurzy, které ukazují, jak nakonfigurovat uvedené kodéry.
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
ms.openlocfilehash: 5a17f98d69cb2196f92e19b66fc23dcd89bce188
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687148"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Konfigurace místních kodérů při použití Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi
Toto téma obsahuje seznam místních kodérů, které můžete použít k zachycení živých událostí a odeslání živého datového proudu s jednou přenosovou rychlostí do kanálů AMS (živé kódování povoleno) k dalšímu zpracování. Téma také obsahuje odkazy na kurzy, které ukazují, jak nakonfigurovat uvedené kodéry.

> [!NOTE]
> Při streamování přes RTMP zkontrolujte nastavení brány firewall nebo proxy serveru, aby se zajistilo, že jsou otevřené odchozí porty TCP 1935 a 1936.

## <a name="haivision-kb-encoder"></a>Kodér Haivision KB
Informace o tom, jak nakonfigurovat kodér [kodéru HAIVISION KB](https://www.haivision.com/products/kb-series/) na odeslání živého datového proudu s jednou přenosovou rychlostí do kanálu AMS, najdete v tématu [konfigurace kodéru Haivision KB](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Wirecastový Stream
Informace o tom, jak nakonfigurovat kodér [Wirecast streamu](https://www.telestream.net/wirecast/overview.htm) na odeslání živého datového proudu s jednou přenosovou rychlostí do kanálu AMS, najdete v tématu [Konfigurace Wirecast](media-services-configure-wirecast-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
Další informace naleznete v tématu [element-Live](https://www.elemental.com/products/aws-elemental-appliances-software/#elemental-live).

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky

[Živé streamování pomocí Azure Media Services k vytváření datových proudů s více přenosovými rychlostmi](media-services-manage-live-encoder-enabled-channels.md).

