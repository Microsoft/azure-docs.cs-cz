---
title: Kódování assetu pomocí Media Encoder Standard v Azure Portal | Microsoft Docs
description: Tento kurz vás provede jednotlivými kroky při kódování assetu pomocí Media Encoder Standard v Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 5f4bb3c9b23ffd68939f1088b1252c6e31c1dad7
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010503"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Kódování aktiva pomocí procesoru Media Encoder Standard na webu Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete v článku [bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Jedním z nejběžnějších scénářů při práci s Azure Media Services je doručování streamování s adaptivní přenosovou rychlostí vašim klientům. Media Services podporuje následující technologie adaptivní přenosové rychlosti streamování: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming a dynamické adaptivní streamování přes HTTP (POMLČKa, označované také jako MPEG-POMLČKa). Pokud chcete připravit videa pro streamování s adaptivní přenosovou rychlostí, napřed si zdrojové video zakódovat jako soubory s více přenosovými rychlostmi. K zakódování videí můžete použít Media Encoder Standard.  

Služba Media Services poskytuje dynamické balení. Pomocí dynamického balení můžete doručovat rychlostmi s více přenosovými rychlostmi v HLS, Smooth Streaming a MPEG-POMLČKy, aniž byste je museli znovu zabalit do těchto formátů streamování. Při použití dynamického balení můžete ukládat a platit soubory ve formátu jednoho úložiště. Media Services sestaví a obsluhuje odpovídající reakci na základě žádosti klienta.

Pokud chcete využít výhod dynamického balení, musíte zdrojový soubor zakódovat do sady souborů MP4 s více přenosovými rychlostmi. Postup kódování je znázorněný dále v tomto článku.

Informace o tom, jak škálovat zpracování médií, najdete v tématu [škálování zpracování médií pomocí Azure Portal](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Kódování v Azure Portal

Kódování obsahu pomocí Media Encoder Standard:

1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. Vyberte **Nastavení**  >  **prostředky**. Vyberte prostředek, který chcete kódovat.
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
* [Monitorujte průběh úlohy kódování](media-services-portal-check-job-progress.md) v Azure Portal.  

