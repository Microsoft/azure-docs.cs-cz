---
title: Nastavte v účtu pro offline streamování obsahu PlayReady chránit – Azure
description: Tento článek ukazuje postup při konfiguraci účtu Azure Media Services pro streamování PlayReady pro Windows 10 v offline režimu.
services: media-services
keywords: Android Offline režimu, ExoPlayer, DASH, technologie DRM, Widevine
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/01/2019
ms.author: willzhan
ms.openlocfilehash: aab0e5eab0edcbc0a26b673730f9fef5a5b01fde
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54122274"
---
# <a name="offline-playready-streaming-for-windows-10"></a>PlayReady offline Streaming pro Windows 10

Azure Media Services podporují offline stažení/přehrávání s ochranou DRM. Tento článek se zaměřuje na offline podporu Azure Media Services pro Windows 10/PlayReady klientů. Si můžete přečíst o podporu offline režimu pro iOS/FairPlay a zařízení s Androidem/Widevine v následujících článcích:

- [Offline FairPlay Streaming pro iOS](offline-fairplay-for-ios.md)
- [Offline Widevine streamování pro Android](offline-widevine-for-android.md)

## <a name="overview"></a>Přehled

Tato část obsahuje základní informace v offline režimu přehrávání, zejména proč:

* V některých zemích je stále omezena dostupnost Internetu a/nebo šířky pásma. Uživatelé se můžou rozhodnout nejprve stáhnout moct dál sledovat obsah v dostatečně vysoká řešení vyhovující zážitek. V takovém případě častěji, problém není dostupnost sítě, místo toho je omezena šířka pásma sítě. Poskytovatelé OTT/OVP žádáme pro podporu offline režimu.
* Jak zveřejněn na konferenci společníka Netflix 2016 3. čtvrtletí, stahování obsahu je "napravo požadované funkce" a "jsme se otevřený, aby ho" říká, že podle Reed Hastingsu, generální ředitel Netflix.
* Někteří poskytovatelé obsahu může zakázat doručování licencí DRM nad rámec ohraničení země. Pokud uživatel musí projít, v zahraničí a stále se chce sledovat obsah, je potřeba ke stažení offline.
 
Výzvy, se kterými jsme setkat při implementaci offline režimu je následující:

* MP4 podporuje mnoho hráči kodér nástroje, ale neexistuje žádná vazba mezi MP4 kontejneru a DRM;
* V dlouhodobém horizontu vývojového diagramu křížového procesu s CENC je způsob, jak přejít. Ale v současné době ekosystému podpory nástroje/player existuje ještě není. Řešení, potřebujeme ještě dnes.
 
Spočívá v: technologie smooth streaming ([PIFF](http://go.microsoft.com/?linkid=9682897)) formát souboru s H264/AAC má vazbu pomocí technologie PlayReady (AES-128 KONT). Jednotlivé smooth streamování .ismv soubor (za předpokladu, že zvuk se muxed ve videu) je sama o sobě fMP4 a lze použít pro přehrávání. V případě technologie smooth streaming obsahu pomocí šifrování PlayReady, každý soubor .ismv stane PlayReady chráněné fragmentovaného MP4. Můžete zvolit soubor .ismv s upřednostňované přenosové rychlosti a přejmenujte ho jako MP4 ke stažení.

Existují dvě možnosti pro hostování PlayReady chráněné MP4 pro progresivní stahování:

* Jeden můžete umístit na stejný prostředek služby kontejneru/media tento MP4 a využít koncový bod pro progresivní stahování; streamování služby Azure Media Services
* Jedna slouží lokátoru SAS pro progresivní stahování přímo ze služby Azure Storage, bez použití služby Azure Media Services.
 
Můžete používat dva typy doručování licencí technologií PlayReady:

* Službu doručování licencí PlayReady ve službě Azure Media Services;
* PlayReady licenční servery hostované kdekoli.

Níže jsou uvedeny dvě sady testovací prostředky, první z nich naším vlastním doručováním licencí PlayReady pomocí v AMS při druhém pomocí serveru licencí PlayReady hostované na Virtuálním počítači Azure:

Asset #1:

* Adresa URL progresivního stahování: [http://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](http://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4")
* PlayReady LA_URL (AMS): [https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/](https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/)

Asset #2:

* Adresa URL progresivního stahování: [http://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](http://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (v místním prostředí): [https://willzhan12.cloudapp.net/playready/rightsmanager.asmx](https://willzhan12.cloudapp.net/playready/rightsmanager.asmx)

Pro účely testování přehrávání jsme použili univerzální aplikace pro Windows ve Windows 10. V [univerzální pro Windows 10 ukázky](https://github.com/Microsoft/Windows-universal-samples), nachází přehrávače příklad volá [adaptivní streamování ukázka](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). Všechno, co musíme udělat, je přidejte kód, abychom mohli vybrat stahování videa a použít ho jako zdroj, namísto adaptivní streamování zdroje. Změny jsou v tlačítko klikněte na tlačítko obslužné rutiny události:

```csharp
private async void LoadUri_Click(object sender, RoutedEventArgs e)
{
    //Uri uri;
    //if (!Uri.TryCreate(UriBox.Text, UriKind.Absolute, out uri))
    //{
    // Log("Malformed Uri in Load text box.");
    // return;
    //}
    //LoadSourceFromUriTask = LoadSourceFromUriAsync(uri);
    //await LoadSourceFromUriTask;

    //willzhan change start
    // Create and open the file picker
    FileOpenPicker openPicker = new FileOpenPicker();
    openPicker.ViewMode = PickerViewMode.Thumbnail;
    openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".ismv");
    //openPicker.FileTypeFilter.Add(".mkv");
    //openPicker.FileTypeFilter.Add(".avi");

    StorageFile file = await openPicker.PickSingleFileAsync();

    if (file != null)
    {
       //rootPage.NotifyUser("Picked video: " + file.Name, NotifyType.StatusMessage);
       this.mediaPlayerElement.MediaPlayer.Source = MediaSource.CreateFromStorageFile(file);
       this.mediaPlayerElement.MediaPlayer.Play();
       UriBox.Text = file.Path;
    }
    else
    {
       // rootPage.NotifyUser("Operation cancelled.", NotifyType.ErrorMessage);
    }

    // On small screens, hide the description text to make room for the video.
    DescriptionText.Visibility = (ActualHeight < 500) ? Visibility.Collapsed : Visibility.Visible;
}
```

![PlayReady offline režimu přehrávání chráněné fMP4](./media/offline-playready-for-windows/offline-playready1.jpg)


Vzhledem k tomu, že je video ochranou PlayReady, nebude možné zahrnout videa na snímku obrazovky.

Stručně řečeno jsme dosáhli režimu offline v Azure Media Services:

* Převod kódování obsahu a šifrování PlayReady můžete udělat v Azure Media Services nebo jiné nástroje;
* Obsah je možné hostovat v Azure Media Services nebo služby Azure Storage pro progresivní stahování;
* Naším vlastním doručováním licencí PlayReady může být z Azure Media Services nebo jinde;
* Připravený obsah smooth streamování je stále možné online vysílání datového proudu prostřednictvím DASH nebo smooth pomocí technologie PlayReady jako DRM.

## <a name="next-steps"></a>Další postup

[Návrh systému s více variantami DRM ochrany obsahu pomocí řízení přístupu](design-multi-drm-system-with-access-control.md)
