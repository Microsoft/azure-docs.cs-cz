---
title: Kódování datového zdroje pomocí standardu Media Encoder Standard na webu Azure Portal | Dokumenty společnosti Microsoft
description: Tento kurz vás provede kroky kódování datového zdroje pomocí standardu Media Encoder Standard na webu Azure Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 7c147d99eaabee70316521d1f2bdc41933162ab1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69542606"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Kódování aktiva pomocí procesoru Media Encoder Standard na webu Azure Portal

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete v [tématu bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Jedním z nejběžnějších scénářů při práci s Azure Media Services je poskytování adaptivního datového toku streamování vašim klientům. Služba Media Services podporuje následující technologie adaptivního datového toku: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming a Dynamic Adaptive Streaming over HTTP (DASH, nazývaný také MPEG-DASH). Chcete-li připravit videa na adaptivní streamování datového toku, nejprve zakódujte zdrojové video jako soubory s více přenosovou rychlostí. Ke kódování videí můžete použít standard k odpojiní médií.  

Služba Media Services poskytuje dynamické balení. Díky dynamickému balení můžete dodávat vícebitové MP4 v HLS, Smooth Streaming a MPEG-DASH, aniž byste museli přebalovat v těchto formátech streamování. Při použití dynamického balení můžete ukládat a platit za soubory ve formátu s jedním úložištěm. Media Services vytvoří a slouží odpovídající odpověď na základě požadavku klienta.

Pokud chcete využít výhod dynamického balení, musíte zdrojový soubor zakódovat do sady souborů MP4 s více přenosovými rychlostmi. Kroky kódování jsou demonstrovány dále v tomto článku.

Informace o škálování zpracování médií najdete [v tématu Škálování zpracování médií pomocí portálu Azure](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Kódování na webu Azure Portal

Kódování obsahu pomocí standardu Media Encoder Standard:

1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. Vyberte **Položku Nastavení** > **datových zdrojů**. Vyberte prostředek, který chcete kódovat.
3. Vyberte tlačítko **Kódovat**.
4. V podokně **Kódovat prostředek** vyberte procesor **Media Encoder Standard** a jednu z předvoleb. Informace o předvolbách najdete v tématech [Automatické generování žebříčku přenosových rychlostí](media-services-autogen-bitrate-ladder-with-mes.md) a [Předvolby úloh pro Media Encoder Standard](media-services-mes-presets-overview.md). Je důležité, abyste zvolili předvolbu, která je pro vaše vstupní video nejvhodnější. Pokud například víte, že vaše vstupní video má rozlišení 1920 &#215; 1080 pixelů, můžete zvolit předvolbu **H264 Multiple Bitrate 1080p**. Pokud máte video s nízkým rozlišením (640 &#215; 360), neměli byste používat předvolbu **H264 Multiple Bitrate 1080p**.
   
   Pro usnadnění správy prostředků můžete upravit název výstupního prostředku a název úlohy.
   
   ![Kódování assetů](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Vyberte **Vytvořit**.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky
* [Sledujte průběh úlohy kódování](media-services-portal-check-job-progress.md) na webu Azure Portal.  

