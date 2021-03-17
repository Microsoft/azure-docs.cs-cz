---
title: Konfigurace místních kodérů při použití Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi | Microsoft Docs
description: Toto téma obsahuje seznam místních kodérů, které můžete použít k zachycení živých událostí a odeslání živého datového proudu s jednou přenosovou rychlostí do kanálů AMS (živé kódování povoleno) k dalšímu zpracování. Téma obsahuje odkazy na kurzy, které ukazují, jak nakonfigurovat uvedené kodéry.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d83c81e1a8a7d8d689e18cc8ba46fbb137959481
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014855"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Konfigurace místních kodérů při použití Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

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

