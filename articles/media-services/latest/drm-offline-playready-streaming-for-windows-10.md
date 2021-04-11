---
title: Konfigurace offline PlayReady streamování
description: V tomto článku se dozvíte, jak nakonfigurovat účet Azure Media Services V3 pro streamování PlayReady pro Windows 10 v režimu offline.
services: media-services
keywords: POMLČKa, DRM, režim offline, Widevine, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: f720549d0e9edba0865254d6427c19995d70fd8f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106069011"
---
# <a name="offline-playready-streaming-for-windows-10-with-media-services-v3"></a>Online streamování PlayReady pro Windows 10 s Media Services V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services podporuje stahování a přehrávání offline s ochranou DRM. Tento článek popisuje offline podporu Azure Media Services pro klienty s Windows 10 a PlayReady. Informace o podpoře offline režimu pro zařízení s iOS/FairPlay a Androidem/Widevine najdete v následujících článcích:

- [Streamování FairPlay pro iOS v offline režimu](drm-offline-fairplay-for-ios-concept.md)
- [Online streamování Widevine pro Android](drm-offline-widevine-for-android.md)

> [!NOTE]
> Offline DRM se účtuje jenom při vytváření jediné žádosti o licenci při stažení obsahu. Neúčtují se žádné chyby.

## <a name="background-on-offline-mode-playback"></a>Přehrávání na pozadí v režimu offline

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

## <a name="asset-1"></a>#1 assetu

* Adresa URL postupného stahování: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* LA_URL PlayReady (AMS): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/`

## <a name="asset-2"></a>#2 assetu

* Adresa URL postupného stahování: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* LA_URL PlayReady (místní): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx`

V případě testování přehrávání jsme použili univerzální aplikaci pro Windows ve Windows 10. V [univerzálních ukázkách Windows 10](https://github.com/Microsoft/Windows-universal-samples)je k dispozici základní ukázka přehrávače označované jako [Ukázka adaptivního streamování](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). Musíme to udělat tak, že před přidáním kódu pro nás vybíráte stažené video a použijete ho jako zdroj namísto adaptivního zdroje streamování. Změny jsou v obslužné rutině události kliknutí na tlačítko:

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

![Přehrávání režimu offline chráněných fMP4 PlayReady](./media/offline-playready-for-windows/offline-playready1.jpg)

Vzhledem k tomu, že je video pod ochranou PlayReady, snímek obrazovky nebude moct video zahrnout.

Ve shrnutí jsme dosáhli offline režimu na Azure Media Services:

* Překódování obsahu a šifrování PlayReady se dá provádět v Azure Media Services nebo jiných nástrojích.
* Obsah je možné hostovat ve Azure Media Services nebo Azure Storage pro progresivní stahování;
* Doručování licencí PlayReady může být z Azure Media Services nebo jinde;
* Připravený plynulý obsah streamování se dá dál používat pro online streamování prostřednictvím POMLČKy nebo hladkého použití s technologií PlayReady jako DRM.
