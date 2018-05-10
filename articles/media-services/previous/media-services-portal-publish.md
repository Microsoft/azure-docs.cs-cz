---
title: Publikovat obsah na webu Azure portal | Microsoft Docs
description: Tento kurz vás provede jednotlivými kroky publikovat obsah na webu Azure portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 92c364eb-5a5f-4f4e-8816-b162c031bb40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: d93bfc548134f730f4fad49a37593c861d6b6cbb
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="publish-content-in-the-azure-portal"></a>Publikovat obsah na portálu Azure
> [!div class="op_single_selector"]
> * [Azure Portal](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> 
> 

## <a name="overview"></a>Přehled
> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete na stránce [bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Pokud chcete uživatelům poskytnout adresu URL, kterou můžou použít ke streamování nebo stažení vašeho obsahu, musíte prostředek nejprve publikovat vytvořením lokátoru. Lokátory zajišťují přístup k souborům asset. Služba Azure Media Services podporuje dva typy lokátorů: 

* **Lokátory streamování (OnDemandOrigin)**. Lokátory streamování se používají pro adaptivní streamování. Adaptivní datové proudy příklady Apple HTTP Live Streaming (HLS), technologie Smooth Streaming Microsoft a dynamické adaptivní datové proudy přes protokol HTTP (DASH, označované taky jako MPEG-DASH). Pokud chcete vytvořit lokátor streamování, váš prostředek musí obsahovat soubor .ism. Například, http://amstest.streaming.mediaservices.windows.net/61b3da1d-96c7-489e-bd21-c5f8a7494b03/scott.ism/manifest.
* **Progresivní lokátory (sdílený přístupový podpis)**. Progresivní lokátory se používají k doručování videa přes progresivní stahování.

Pokud chcete vytvořit adresu URL streamování HLS, připojte na konec adresy *(format=m3u8-aapl)*:

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

Pokud chcete vytvořit adresu URL streamování pro přehrávání prostředků Smooth Streaming, použijte následující formát adresy URL:

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest

Pokud chcete vytvořit adresu URL streamování MPEG-DASH, připojte na konec adresy *(format=mpd-time-csf)*:

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

Adresa URL sdíleného přístupového podpisu má následující formát:

    {blob container name}/{asset name}/{file name}/{shared access signature}

Další informace najdete v tématu [doručování obsahu přehled](media-services-deliver-content-overview.md).

> [!NOTE]
> Lokátory vytvořené na webu Azure Portal před březnem 2015 mají datum vypršení platnosti dva roky.  
> 
> 

Pokud chcete aktualizovat datum vypršení platnosti lokátoru, můžete použít [REST API](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) nebo [.NET API](http://go.microsoft.com/fwlink/?LinkID=533259). 

> [!NOTE]
> Při aktualizaci data vypršení platnosti lokátoru sdíleného přístupového podpisu se změní adresa URL.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Postup publikování assetu pomocí portálu
1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. Vyberte **Nastavení** > **Assety**. Vyberte asset, který chcete publikovat.
3. Vyberte tlačítko **Publikovat**.
4. Vyberte typ lokátoru.
5. Vyberte **Přidat**.
   
    ![Publikování videa](./media/media-services-portal-vod-get-started/media-services-publish1.png)

Adresa URL se přidá do seznamu **publikovaných adres URL**.

## <a name="play-content-in-the-portal"></a>Přehrávání obsahu na portálu
Video můžete otestovat v přehrávači obsahu na webu Azure Portal.

Vyberte video a pak vyberte tlačítko **Přehrát**.

![Přehrání videa na webu Azure Portal](./media/media-services-portal-vod-get-started/media-services-play.png)

Musí být splněny určité předpoklady:

* Zkontrolujte, že bylo video publikováno.
* Přehrávač médií na webu Azure Portal přehrává z výchozího koncového bodu streamování. Pokud chcete přehrávat z jiného než výchozího koncového bodu streamování, vyberte a zkopírujte adresu URL a vložte ji do jiného přehrávače. Video můžete otestovat například v [Přehrávači médií Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
* Koncový bod streamování, ze kterého jsou datové proudy musí být spuštěna.  

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další postup
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

