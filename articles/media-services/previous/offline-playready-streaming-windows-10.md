---
title: Konfigurace účtu pro offline streamování obsahu chráněného technologií PlayReady – Azure
description: Tento článek ukazuje, jak nakonfigurovat účet Azure Media Services pro streamování PlayReady pro Windows 10 v režimu offline.
services: media-services
keywords: POMLČKa, DRM, režim offline, Widevine, ExoPlayer, Android
documentationcenter: ''
author: IngridAtMicrosoft
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: 828895894464eafc53f74e7c8b5798db4db03533
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016130"
---
# <a name="offline-playready-streaming-for-windows-10"></a>Streamování PlayReady pro Windows 10 v offline režimu

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Vyberte verzi Media Services, kterou používáte:"]
> * [Verze 3](../latest/offline-plaready-streaming-for-windows-10.md)
> * [Verze 2](offline-playready-streaming-windows-10.md)

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

Azure Media Services podporuje stahování a přehrávání offline s ochranou DRM. Tento článek popisuje offline podporu Azure Media Services pro klienty s Windows 10 a PlayReady. Informace o podpoře offline režimu pro zařízení s iOS/FairPlay a Androidem/Widevine najdete v následujících článcích:

- [Streamování FairPlay pro iOS v offline režimu](media-services-protect-hls-with-offline-fairplay.md)
- [Online streamování Widevine pro Android](offline-widevine-for-android.md)

## <a name="overview"></a>Přehled

Tato část poskytuje některé pozadí při přehrávání offline režimu, zejména proč:

* V některých zemích a oblastech je dostupnost Internetu a/nebo šířka pásma stále omezená. Uživatelé si můžou nejdřív stáhnout, aby mohli sledovat obsah s dostatečně vysokým rozlišením, aby se mohlo zobrazit uspokojivý zážitek. V takovém případě častěji problém není síťová dostupnost, ale je omezená šířka pásma sítě. Poskytovatelé OTT/OVP žádají o podporu offline režimu.
* Jak jsme zavřeli na konferenci Netflix 2016 Q3 akcionář, stažení obsahu je funkce, kterou požadujeme, a my na ni jsme na ni otevřeli Reed Hastings, Netflix pro generálního ředitele.
* Někteří poskytovatelé obsahu můžou zakázat doručování licencí DRM mimo hranici země nebo oblasti. Pokud uživatel potřebuje cestovat do zahraničí a pořád chce sledovat obsah, je potřeba stáhnout offline.
 
Výzva, kterou čelíme v implementaci offline režimu, je následující:

* MP4 podporuje spousta hráčů, nástroje kodéru, ale neexistuje žádná vazba mezi kontejnerem MP4 a DRM.
* V dlouhodobém horizontu CFF s CENC je způsob, jak jít. V současné době ale ekosystém podpory nástrojů/přehrávače ještě není. Musíme ještě dnes řešení.
 
Nápad je: formát[PIFF](/iis/media/smooth-streaming/protected-interoperable-file-format)(vyhlazené streamování) s H264/AAC má vazbu s PLAYREADY (AES-128 centrum). Jednotlivý plynulý soubor streamování. ISMV (za předpokladu, že zvuk je muxed ve videu) je to fMP4 a dá se použít k přehrávání. Pokud dojde k hladkému streamování obsahu prostřednictvím šifrování PlayReady, každý soubor. ISMV se bude nacházet s fragmentovaným MP4 chráněným jako PlayReady. Můžeme zvolit soubor. ISMV s upřednostňovanou přenosovou rychlostí a přejmenovat ho jako. MP4 ke stažení.

K dispozici jsou dvě možnosti pro hostování MP4 chráněného MP4 pro progresivní stažení:

* Ten může tento MP4 vložit do stejného kontejneru jako kontejner nebo Media Service assetu a využít koncový bod streamování Azure Media Services pro progresivní stahování;
* Jeden může použít Lokátor SAS pro progresivní stahování přímo z Azure Storage, obcházení Azure Media Services.
 
Můžete použít dva typy doručování licencí PlayReady:

* Služba doručování licencí PlayReady v Azure Media Services;
* Servery licencí PlayReady hostované kdekoli.

Níže jsou uvedeny dvě sady testovacích prostředků, první z nich pomocí doručování licencí PlayReady v AMS a druhá z nich pomocí mého licenčního serveru aplikace PlayReady hostovaného na virtuálním počítači Azure:

#1 assetu:

* Adresa URL postupného stahování: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* LA_URL PlayReady (AMS): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/`

#2 assetu:

* Adresa URL postupného stahování: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (on-Prem): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx`

V případě testování přehrávání jsem používal univerzální aplikaci pro Windows ve Windows 10. V [univerzálních ukázkách Windows 10](https://github.com/Microsoft/Windows-universal-samples)je k dispozici základní ukázka přehrávače označované jako [Ukázka adaptivního streamování](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). Musíme to udělat tak, že před přidáním kódu pro nás vybíráte stažené video a použijete ho jako zdroj namísto adaptivního zdroje streamování. Změny jsou v obslužné rutině události kliknutí na tlačítko:

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

 ![Přehrávání režimu offline chráněných fMP4 PlayReady](./media/offline-playready/offline-playready1.jpg)

Vzhledem k tomu, že je video pod ochranou PlayReady, snímek obrazovky nebude moct video zahrnout.

Ve shrnutí jsme dosáhli offline režimu na Azure Media Services:

* Překódování obsahu a šifrování PlayReady se dá provádět v Azure Media Services nebo jiných nástrojích.
* Obsah je možné hostovat ve Azure Media Services nebo Azure Storage pro progresivní stahování;
* Doručování licencí PlayReady může být z Azure Media Services nebo jinde;
* Připravený plynulý obsah streamování se dá dál používat pro online streamování prostřednictvím POMLČKy nebo hladkého použití s technologií PlayReady jako DRM.

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="next-steps"></a>Další kroky

[Hybridní návrh systému DRM](hybrid-design-drm-sybsystem.md)
