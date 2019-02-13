---
title: Začínáme s doručováním videa na vyžádání pomocí webu Azure Portal | Dokumentace Microsoftu
description: Tento kurz vás provede jednotlivými kroky implementace základní služby pro doručování video obsahu na vyžádání pomocí aplikace Azure Media Services na webu Azure Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: d3c175bc4d2e5b25c8ce6d4a9b128f961e0af4aa
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210743"
---
# <a name="get-started-with-delivering-content-on-demand-by-using-the-azure-portal"></a>Začínáme s doručováním obsahu na vyžádání pomocí webu Azure Portal

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

Tento kurz vás provede jednotlivými kroky implementace základní služby pro doručování video obsahu na vyžádání pomocí aplikace Azure Media Services na webu Azure Portal.

## <a name="prerequisites"></a>Požadavky
K dokončení kurzu potřebujete následující položky:

* Účet Azure. Podrobnosti najdete na stránce [bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
* Účet Media Services. Pokud chcete vytvořit účet Media Services, přečtěte si článek [Jak vytvořit účet Media Services](media-services-portal-create-account.md).

Tento kurz sestává z následujících úloh:

1. Spuštění koncového bodu streamování
2. Nahrání videosouboru
3. Zakódování zdrojového souboru do sady souborů MP4 s adaptivní přenosovou rychlostí
4. Publikování prostředku a získání adres URL streamování a progresivního stahování  
5. Přehrání obsahu

## <a name="start-the-streaming-endpoint"></a>Spuštění koncového bodu streamování

Jedním z nejběžnějších scénářů při práci se službou Azure Media Services je doručování videa prostřednictvím streamování s adaptivní přenosovou rychlostí. Služba Media Services poskytuje dynamické balení. Pomocí dynamického balení můžete doručovat obsah s adaptivní přenosovou rychlostí s kódováním MP4 ve formátech streamování za běhu podporovaných službou Media Services. Mezi příklady patří Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming a dynamické adaptivní streamování přes HTTP (DASH, označované také jako MPEG-DASH). Díky streamování s adaptivní přenosovou rychlostí služby Media Services můžete doručovat videa bez ukládání předem zabalených verzí pro každý z těchto formátů streamování.

> [!NOTE]
> Po vytvoření účtu Media Services se do vašeho účtu přidá výchozí koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 

Spuštění koncového bodu streamování:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte **Nastavení** > **Koncové body streamování**. 
3. Vyberte výchozí koncový bod streamování. Zobrazí se okno **VÝCHOZÍ KONCOVÝ BOD STREAMOVÁNÍ – PODROBNOSTI**.
4. Vyberte ikonu **Spustit**.
5. Vyberte tlačítko **Uložit**.

## <a name="upload-files"></a>Nahrání souborů
Pokud chcete streamovat videa pomocí služby Media Services, musíte nahrát zdrojová videa, zakódovat je do více přenosových rychlostí a pak publikovat výsledek. První krok pokrývá tato část. 

1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. Vyberte **Nastavení** > **Assety**. Pak vyberte tlačítko **Nahrát**.
   
    ![Nahrání souborů](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Zobrazí se okno **Nahrát asset videa**.
   
   > [!NOTE]
   > Služba Media Services pro nahrávání videí neomezuje velikost souboru.
   > 
   > 
3. Na svém počítači přejděte k videu, které chcete nahrát. Vyberte video a pak vyberte **OK**.  
   
    Spustí se nahrávání. Průběh můžete sledovat pod názvem souboru.  

Po dokončení nahrávání bude nový prostředek uvedený v podokně **Prostředky**. 

## <a name="encode-assets"></a>Kódování assetů
Pokud chcete využít výhod dynamického balení, musíte zdrojový soubor zakódovat do sady souborů MP4 s více přenosovými rychlostmi. Postup kódování je ukázán v této části.

### <a name="encode-assets-in-the-portal"></a>Kódování prostředků na portálu
Kódování obsahu pomocí procesoru Media Encoder Standard na webu Azure Portal:

1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. Vyberte **Nastavení** > **Assety**. Vyberte prostředek, který chcete kódovat.
3. Vyberte tlačítko **Kódovat**.
4. V podokně **Kódovat prostředek** vyberte procesor **Media Encoder Standard** a jednu z předvoleb. Informace o předvolbách najdete v tématech [Automatické generování žebříčku přenosových rychlostí](media-services-autogen-bitrate-ladder-with-mes.md) a [Předvolby úloh pro Media Encoder Standard](media-services-mes-presets-overview.md). Je důležité, abyste zvolili předvolbu, která je pro vaše vstupní video nejvhodnější. Pokud například víte, že vaše vstupní video má rozlišení 1920 &#215; 1080 pixelů, můžete zvolit předvolbu **H264 Multiple Bitrate 1080p**. Pokud máte video s nízkým rozlišením (640 &#215; 360), neměli byste používat předvolbu **H264 Multiple Bitrate 1080p**.
   
   Pro usnadnění správy prostředků můžete upravit název výstupního prostředku a název úlohy.
   
   ![Kódování assetů](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Vyberte **Vytvořit**.

### <a name="monitor-encoding-job-progress"></a>Monitorování průběhu úlohy kódování
Pokud chcete monitorovat průběh úlohy kódování, vyberte v horní části stránky **Nastavení** a pak vyberte **Úlohy**.

![Úlohy](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Publikování obsahu
Pokud chcete uživatelům poskytnout adresu URL, kterou můžou použít ke streamování nebo stažení vašeho obsahu, musíte prostředek nejprve publikovat vytvořením lokátoru. Lokátory zajišťují přístup k souborům v prostředku. Služba Azure Media Services podporuje dva typy lokátorů: 

* **Lokátory streamování (OnDemandOrigin)**. Lokátory streamování se používají pro adaptivní streamování. Mezi příklady adaptivního streamování patří HLS, Smooth Streaming a MPEG-DASH. Pokud chcete vytvořit lokátor streamování, váš prostředek musí obsahovat soubor .ism. 
* **Progresivní lokátory (sdílený přístupový podpis)**. Progresivní lokátory se používají k doručování videa přes progresivní stahování.

Pokud chcete vytvořit adresu URL streamování HLS, připojte na konec adresy *(format=m3u8-aapl)*:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

Pokud chcete vytvořit adresu URL streamování pro přehrávání prostředků Smooth Streaming, použijte následující formát adresy URL:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest

Pokud chcete vytvořit adresu URL streamování MPEG-DASH, připojte na konec adresy *(format=mpd-time-csf)*:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

Adresa URL sdíleného přístupového podpisu má následující formát:

    {blob container name}/{asset name}/{file name}/{shared access signature}

> [!NOTE]
> Lokátory vytvořené na webu Azure Portal před březnem 2015 mají datum vypršení platnosti dva roky.  
> 
> 

K aktualizaci data vypršení platnosti lokátoru můžete použít rozhraní [REST API](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) nebo [.NET API](https://go.microsoft.com/fwlink/?LinkID=533259). 

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

## <a name="play-content-from-the-portal"></a>Přehrávání obsahu z portálu
Video můžete otestovat v přehrávači obsahu na webu Azure Portal.

Vyberte video a pak vyberte tlačítko **Přehrát**.

![Přehrání videa na webu Azure Portal](./media/media-services-portal-vod-get-started/media-services-play.png)

Musí být splněny určité předpoklady:

* Pokud chcete začít streamovat, spusťte výchozí koncový bod streamování.
* Zkontrolujte, že bylo video publikováno.
* Přehrávač médií na webu Azure Portal přehrává z výchozího koncového bodu streamování. Pokud chcete přehrávat z jiného než výchozího koncového bodu streamování, vyberte a zkopírujte adresu URL a vložte ji do jiného přehrávače. Video můžete otestovat například v [Přehrávači médií Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další postup
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
