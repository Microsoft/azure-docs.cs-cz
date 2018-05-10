---
title: Kódování assetu pomocí kodéru Media Encoder Standard na portálu Azure | Microsoft Docs
description: Tento kurz vás provede jednotlivými kroky kódování assetu pomocí kodéru Media Encoder Standard na portálu Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: 470eb8613416f441c1becee628acf3c898591c84
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Kódování assetu pomocí kodéru Media Encoder Standard na portálu Azure

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete na stránce [bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Jedním nejběžnějších scénářů při práci se službou Azure Media Services je doručování adaptivní přenosové rychlosti streamování vašim klientům. Služba Media Services podporuje následující adaptivní přenosové rychlosti streamování technologie: Apple HTTP Live Streaming (HLS), technologie Smooth Streaming Microsoft a dynamické adaptivní datové proudy přes protokol HTTP (DASH, označované taky jako MPEG-DASH). Příprava pro adaptivní přenosové rychlosti streamování videa, zakódujte nejprve zdroj videa jako soubory s více přenosovými rychlostmi. Azure Media Encoder Standard můžete použít ke kódování vašich videí.  

Služba Media Services poskytuje dynamické balení. Při dynamickém balení, abyste mohli zajistit vaše soubory MP4 více přenosovými rychlostmi HLS, technologie Smooth Streaming a MPEG-DASH, bez opětovné vytvoření balíčku v těchto formátů streamování. Při použití dynamického balení, můžete ukládat a platit pro soubory ve formátu jedním úložiště. Služba Media Services sestavení a slouží odpovídající odpověď na základě požadavku klienta.

Pokud chcete využít výhod dynamického balení, musíte zdrojový soubor zakódovat do sady souborů MP4 s více přenosovými rychlostmi. Postup kódování je ukázán později v tomto článku.

Další postup škálování zpracování médií najdete v tématu [škálování média zpracování pomocí portálu Azure](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Kódování na portálu Azure

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
* [Monitorování průběhu úlohy kódování](media-services-portal-check-job-progress.md) na portálu Azure.  

