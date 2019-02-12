---
title: Kódování prostředku pomocí kodéru Media Encoder Standard na webu Azure Portal | Dokumentace Microsoftu
description: Tento kurz vás provede kroky pro kódování prostředku pomocí kodéru Media Encoder Standard na webu Azure Portal.
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 90a94612248dead5b57ebff67562dda083d8669f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55996130"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Kódování prostředku pomocí kodéru Media Encoder Standard na webu Azure Portal

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete na stránce [bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Jedním z nejběžnějších scénářů při práci s Azure Media Services je doručování adaptivní přenosové rychlosti streamování vašim klientům. Služba Media Services podporuje následující technologie streamování adaptivní přenosové rychlosti: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming a dynamické adaptivní streamování přes HTTP (DASH, také nazývané MPEG-DASH). Příprava vašich videí pro streamování s adaptivní přenosovou rychlostí, nejprve zakódování zdrojového videa zdroje jako soubory s více přenosovými rychlostmi. Azure Media Encoder Standard můžete použít ke kódování vašich videí.  

Služba Media Services poskytuje dynamické balení. S dynamickým vytvářením paketů, můžete dodávat vaše soubory MP4 s více přenosovými rychlostmi HLS, technologie Smooth Streaming a MPEG-DASH, bez opětovné balení do těchto formátů streamování. Při použití dynamického balení můžete ukládat a platíte za soubory ve formátu jedním úložiště. Služba Media Services sestaví a odešle odpovídající odpověď na požadavek klienta na základě.

Pokud chcete využít výhod dynamického balení, musíte zdrojový soubor zakódovat do sady souborů MP4 s více přenosovými rychlostmi. Postup kódování je ukázán později v tomto článku.

Informace o škálování zpracování médií najdete v tématu [škálovat pomocí webu Azure portal zpracování médií](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Kódování na webu Azure Portal

Zakódovat svůj obsah pomocí procesoru Media Encoder Standard:

1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. Vyberte **Nastavení** > **Assety**. Vyberte prostředek, který chcete kódovat.
3. Vyberte tlačítko **Kódovat**.
4. V podokně **Kódovat prostředek** vyberte procesor **Media Encoder Standard** a jednu z předvoleb. Informace o předvolbách najdete v tématech [Automatické generování žebříčku přenosových rychlostí](media-services-autogen-bitrate-ladder-with-mes.md) a [Předvolby úloh pro Media Encoder Standard](media-services-mes-presets-overview.md). Je důležité, abyste zvolili předvolbu, která je pro vaše vstupní video nejvhodnější. Pokud například víte, že vaše vstupní video má rozlišení 1920 &#215; 1080 pixelů, můžete zvolit předvolbu **H264 Multiple Bitrate 1080p**. Pokud máte video s nízkým rozlišením (640 &#215; 360), neměli byste používat předvolbu **H264 Multiple Bitrate 1080p**.
   
   Pro usnadnění správy prostředků můžete upravit název výstupního prostředku a název úlohy.
   
   ![Kódování assetů](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Vyberte **Vytvořit**.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další postup
* [Sledovat průběh úlohy kódování](media-services-portal-check-job-progress.md) na webu Azure Portal.  

