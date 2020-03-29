---
title: Konfigurace účtu pro offline streamování obsahu chráněného službou PlayReady – Azure
description: Tento článek ukazuje, jak nakonfigurovat účet Azure Media Services pro streamování PlayReady pro Windows 10 offline.
services: media-services
keywords: DASH, DRM, Widevine Offline režim, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2019
ms.author: willzhan
ms.openlocfilehash: 350b8d111652511627ddf67236f63248a5489015
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74970444"
---
# <a name="offline-playready-streaming-for-windows-10"></a>Streamování PlayReady pro Windows 10 v offline režimu  

> [!div class="op_single_selector" title1="Vyberte verzi služby Media Services, kterou používáte:"]
> * [Verze 3](../latest/offline-plaready-streaming-for-windows-10.md)
> * [Verze 2](offline-playready-streaming-windows-10.md)

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Azure Media Services podporují offline stahování a přehrávání s ochranou DRM. Tento článek popisuje offline podporu Azure Media Services pro klienty S Windows 10/PlayReady. O podpoře offline režimu pro zařízení iOS/FairPlay a Android/Widevine si můžete přečíst v následujících článcích:

- [Streamování FairPlay pro iOS v offline režimu](media-services-protect-hls-with-offline-fairplay.md)
- [Offline widevine streamování pro Android](offline-widevine-for-android.md)

## <a name="overview"></a>Přehled

Tato část poskytuje některé pozadí pro přehrávání offline režimu, zejména proč:

* V některých zemích nebo oblastech je dostupnost internetu nebo šířka pásma stále omezená.Uživatelé se mohou nejprve stáhnout, aby mohli sledovat obsah v dostatečném rozlišení pro uspokojivé zobrazení. V tomto případě se častěji problém není dostupnost sítě, spíše je omezená šířka pásma sítě. Poskytovatelé OTT/OVP žádají o podporu offline režimu.
* Jak bylo zveřejněno na konferenci akcionářů Netflix 2016 Q3, stahování obsahu je "velmi žádanou funkcí" a "jsme jí otevřeni", řekl Reed Hastings, generální ředitel společnosti Netflix.
* Někteří poskytovatelé obsahu mohou zakázat doručování licencí DRM mimo hranice země nebo oblasti. Pokud uživatel potřebuje cestovat do zahraničí a přesto chce sledovat obsah, je potřeba stáhnout offline.
 
Výzvou, které čelíme při implementaci offline režimu, je následující:

* MP4 je podporován mnoha přehrávači, kodérnástroje, ale neexistuje žádná vazba mezi KONTEJNER MP4 a DRM;
* Z dlouhodobého hlediska je cff s CENC tou cestou. Nicméně, dnes, nástroje / hráč podporu ekosystému není tam ještě. Dnes potřebujeme řešení.
 
Myšlenka je: plynulé streamování[(PIFF)](https://docs.microsoft.com/iis/media/smooth-streaming/protected-interoperable-file-format)formát souboru s H264/AAC má vazbu s PlayReady (AES-128 CTR). Individuální plynulé streamování .ismv soubor (za předpokladu, že zvuk je muxed ve videu) je sám fMP4 a může být použit pro přehrávání. Pokud plynulý streamovaný obsah prochází šifrováním PlayReady, stane se každý soubor ISMV fragmentovaným bodem MP4 chráněným službou PlayReady. Můžeme zvolit soubor .ismv s preferovaným přenosovým tokem a přejmenovat jej na .mp4 ke stažení.

Existují dvě možnosti pro hostování PlayReady chráněné MP4 pro postupné stahování:

* Jeden můžete umístit tento MP4 ve stejném kontejneru /mediální služby majetku a využít Azure Media Services streamování koncový bod pro postupné stahování;
* Jeden můžete použít Lokátor SAS pro postupné stahování přímo z Azure Storage, obcházet Azure Media Services.
 
Můžete použít dva typy doručování licencí PlayReady:

* Služba doručování licencí PlayReady ve službě Azure Media Services;
* Licenční servery PlayReady hostované kdekoli.

Níže jsou uvedeny dvě sady testovacích datových zdrojů, první, která používá doručování licencí PlayReady v AMS, zatímco druhá používá můj licenční server PlayReady hostovaný na virtuálním počítači Azure:

#1 aktiv:

* Progresivní stahování URL:[https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (AMS):[https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/](https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/)

#2 aktiv:

* Progresivní stahování URL:[https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (on-prem):[https://willzhan12.cloudapp.net/playready/rightsmanager.asmx](https://willzhan12.cloudapp.net/playready/rightsmanager.asmx)

Pro testování přehrávání jsem použil univerzální aplikaci windows v systému Windows 10. Ve [vzorcích Windows 10 Universal](https://github.com/Microsoft/Windows-universal-samples)je základní ukázka přehrávače s názvem [Adaptivní ukázka streamování](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). Vše, co musíme udělat, je přidat kód pro nás vybrat stažené video a použít jej jako zdroj, místo adaptivního streaming zdroj. Změny jsou v obslužné rutině události kliknutí na tlačítko:

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

 ![Přehrávání fMP4 chráněného přes režim offline režimu](./media/offline-playready/offline-playready1.jpg)

Vzhledem k tomu, že video je pod ochranou PlayReady, snímek obrazovky nebude moci video zahrnout.

Stručně řečeno, ve službě Azure Media Services jsme dosáhli offline režimu:

* Překódování obsahu a šifrování PlayReady lze provést ve službě Azure Media Services nebo jiných nástrojích.
* Obsah může být hostovaný ve Službě Azure Media Services nebo Azure Storage pro postupné stahování;
* Doručování licencí PlayReady může být z Azure Media Services nebo jinde;
* Připravený hladký streaming ový obsah lze stále používat pro online streamování přes DASH nebo hladký s PlayReady jako DRM.

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="next-steps"></a>Další kroky

[Hybridní návrh systému DRM](hybrid-design-drm-sybsystem.md)
