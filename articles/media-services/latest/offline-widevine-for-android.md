---
title: Streamovat Widevine Android offline pomocí Azure Media Services v3
description: Toto téma ukazuje, jak nakonfigurovat účet Azure Media Services pro offline streamování obsahu chráněného widevine.
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
ms.date: 04/07/2020
ms.author: willzhan
ms.openlocfilehash: 94edec8261d9916b7575fb247e1698273f244130
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887193"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>Offline Widevine streamování pro Android s Mediální služby v3

Kromě ochrany obsahu pro online streamování nabízejí služby předplatného mediálního obsahu a pronájmu obsah ke stažení, který funguje, když nejste připojeni k internetu. Při letu odpojeném od sítě může být nutné stáhnout obsah do telefonu nebo tabletu pro přehrávání v režimu letadlo. Další scénáře, ve kterých můžete stahovat obsah:

- Někteří poskytovatelé obsahu mohou zakázat doručování licencí DRM mimo hranice země nebo oblasti. Pokud chce uživatel sledovat obsah na cestách do zahraničí, je potřeba stáhnout offline obsah.
- V některých zemích nebo oblastech je dostupnost internetu nebo šířka pásma omezena. Uživatelé se mohou rozhodnout stáhnout obsah, aby jej mohli sledovat v dostatečném rozlišení pro uspokojivý zážitek ze sledování.

Tento článek popisuje, jak implementovat přehrávání offline režimu pro obsah DASH chráněný Widevine na zařízeních Se systémem Android. Offline DRM umožňuje poskytovat modely předplatného, pronájmu a nákupu obsahu, což zákazníkům vašich služeb umožňuje snadno si s sebou po odpojení od internetu snadno vzít obsah.

Pro vytváření aplikací pro Android player nastíníme tři možnosti:

> [!div class="checklist"]
> * Vytvoření přehrávače pomocí Java API sady ExoPlayer SDK
> * Sestavte si hráče pomocí xamarinové vazby exoplayerové sady SDK
> * Vytvoření přehrávače pomocí rozšíření encrypted media Extension (EME) a rozšíření Media Source Extension (MSE) v mobilním prohlížeči Chrome verze 62 nebo novějším

Článek také odpovídá na některé běžné otázky týkající se offline streamování chráněného obsahu Widevine.

> [!NOTE]
> Offline DRM se účtuje pouze za vytvoření jedné žádosti o licenci při stahování obsahu. Případné chyby se neúčtují.

## <a name="prerequisites"></a>Požadavky 

Před implementací offline DRM pro Widevine na zařízeních se systémem Android byste měli nejprve:

- Seznamte se s koncepty zavedenými pro ochranu obsahu online pomocí widevine DRM. To je podrobně popsáno v následujících dokumentech/vzorcích:
    - [Návrh systému ochrany obsahu s více variantami DRM s využitím řízení přístupu](design-multi-drm-system-with-access-control.md)
    - [Použití ochrany DRM s dynamickým šifrováním a služby doručování licencí](protect-with-drm.md)
- Klon https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git.

    Budete muset upravit kód v [šifrovat s DRM pomocí rozhraní .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) přidat Konfigurace Widevine.  
- Seznamte se s Google ExoPlayer SDK pro Android, open-source video přehrávač SDK schopné podporovat offline Widevine DRM přehrávání. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [Průvodce pro vývojáře exoplayeru](https://google.github.io/ExoPlayer/guide.html)
    - [Blog pro vývojáře eoPlayer](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurace ochrany obsahu ve službě Azure Media Services

V [getOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) metoda jsou k dispozici následující nezbytné kroky:

1. Určete, jak je doručování klíčů obsahu povoleno ve službě doručování licencí: 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. Konfigurace šablony licence Widevine:  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. Vytvořit contentkeypolicyoptions:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Povolení režimu offline

Chcete-li povolit **režim offline** pro licence Widevine, je třeba nakonfigurovat [šablonu licence Widevine](widevine-license-template-overview.md). V **objektu policy_overrides** nastavte vlastnost **can_persist** na **hodnotu true** (výchozí hodnota je nepravdivá), jak je znázorněno v [části ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Konfigurace přehrávače Android pro přehrávání offline

Nejjednodušší způsob, jak vytvořit nativní přehrávač aplikace pro zařízení se systémem Android je použít [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), open-source video přehrávač SDK. ExoPlayer podporuje funkce, které nejsou v současné době podporovány nativním rozhraním MediaPlayer API systému Android, včetně protokolů MPEG-DASH a Microsoft Smooth Streaming.

ExoPlayer verze 2.6 a vyšší obsahuje mnoho tříd, které podporují přehrávání Widevine DRM offline. Třída OfflineLicenseHelper zejména poskytuje funkce nástrojů usnadňujících použití defaultDrmSessionManager pro stahování, obnovu a uvolňování offline licencí. Třídy uvedené ve složce SDK "knihovna / jádro / src / main / java / com / google / android / exoplayer2 / offline / " podporují stahování offline video obsahu.

Následující seznam tříd usnadňuje režim offline v exoPlayer SDK pro Android:

- knihovna/jádro/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- knihovna/jádro/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- knihovna/jádro/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- knihovna/jádro/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- knihovna/jádro/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- knihovna/jádro/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- knihovna/jádro/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- knihovna/jádro/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- knihovna/jádro/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- knihovna/pomlčka/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

Vývojáři by měli odkazovat na [ExoPlayer Developer Guide](https://google.github.io/ExoPlayer/guide.html) a odpovídající [Developer Blog](https://medium.com/google-exoplayer) během vývoje aplikace. Společnost Google v tuto chvíli nevydala plně zdokumentovanou referenční implementaci nebo ukázkový kód pro aplikaci ExoPlayer podporující widevine offline, takže informace jsou omezeny na průvodce vývojářůa a blog. 

### <a name="working-with-older-android-devices"></a>Práce se staršími zařízeními Android

U některých starších zařízení se systémem Android je nutné nastavit hodnoty pro následující **vlastnosti policy_overrides** (definované v [šabloně licence Widevine:](widevine-license-template-overview.md) **rental_duration_seconds**, **playback_duration_seconds**a **license_duration_seconds**. Případně je můžete nastavit na nulu, což znamená nekonečnou/neomezenou dobu trvání.  

Hodnoty musí být nastaveny, aby se zabránilo chybě přetečení celého čísla. Další vysvětlení tohoto problému https://github.com/google/ExoPlayer/issues/3150 naleznete https://github.com/google/ExoPlayer/issues/3112v tématu a . <br/>Pokud explicitně nenastavíte hodnoty, budou přiřazeny velmi velké hodnoty pro **PlaybackDurationRemaining** a **LicenseDurationRemaining** (například 9223372036854775807, což je maximální kladná hodnota pro 64bitové celé číslo). V důsledku toho se zobrazí licence Widevine vypršela, a proto se dešifrování nestane. 

K tomuto problému nedochází v systému Android 5.0 Lollipop nebo novějším, protože Android 5.0 je první verze androida, která byla navržena tak, aby plně podporovala ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) a 64bitové platformy, zatímco Android 4.4 KitKat byl původně navržen tak, aby podporoval ARMv7 a 32bitové platformy jako u jiných starších verzí Androidu.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Použití Xamarinu k vytvoření aplikace pro přehrávání androida

Vázání Xamarin pro ExoPlayer naleznete pomocí následujících odkazů:

- [Knihovna vazeb Xamarin pro knihovnu Google ExoPlayer](https://github.com/martijn00/ExoPlayerXamarin)
- [Xamarin vazby pro ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Viz také následující vlákno: [Vazba Xamarin](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Aplikace pro přehrávač Chrome pro Android

Počínaje vydáním [Chromu pro Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates)je podporována trvalá licence v EME. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) je nyní podporován také v Chromu pro Android. To vám umožní vytvářet aplikace pro přehrávání offline v Chromu, pokud vaši koncoví uživatelé mají tuto (nebo vyšší) verzi Chromu. 

Kromě toho společnost Google vytvořila ukázku progresivní webové aplikace (PWA) a získala ji s otevřeným zdrojovým kódem: 

- [Zdrojový kód](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Google hostované verze](https://biograf-155113.appspot.com/ttt/episode-2/) (funguje pouze v Chrome v 62 a vyšší na zařízeních Android)

Pokud upgradujete svůj mobilní prohlížeč Chrome na v62 (nebo vyšší) na telefonu Android a otestujete výše uvedenou hostovanou ukázkovou aplikaci, uvidíte, že jak online streamování, tak přehrávání offline fungují.

Výše uvedená aplikace aplikace PWA s otevřeným zdrojovým kódem je napsána v souboru Node.js. Pokud chcete hostovat vlastní verzi na serveru Ubuntu, mějte na paměti následující běžné problémy, které mohou zabránit přehrávání:

1. Cors problém: Ukázkové video v ukázkové https://storage.googleapis.com/biograf-video-files/videos/aplikaci je hostované v . Google nastavil CORS pro všechny své testovací vzorky hostované v kbelíku SCloud Storage Google. Jsou obsluhovány hlavičkami CORS, které výslovně `https://biograf-155113.appspot.com` specifikují položku CORS: (doména, ve které google hostuje jejich vzorek) brání přístupu jiných webů. Pokud se pokusíte, zobrazí se následující chyba PROTOKOLU HTTP:`Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Vydání certifikátu: Počínaje Chrome m. 58 vyžaduje EME pro Widevine protokol HTTPS. Proto je třeba hostovat ukázkovou aplikaci přes protokol HTTPS s certifikátem X509. Obvyklý zkušební certifikát nefunguje z důvodu následujících požadavků: Je třeba získat certifikát splňující následující minimální požadavky:
    - Chrome a Firefox vyžadují, aby v certifikátu existovalo nastavení alternativního názvu sítě SAN-Subject
    - Certifikát musí mít důvěryhodnou certifikační autoritu a certifikát vývoje podepsaný svým držitelem nefunguje.
    - Certifikát musí mít cn odpovídající názvu DNS webového serveru nebo brány.

## <a name="faqs"></a>Nejčastější dotazy

Další informace naleznete v [nejčastějších dotazech k widevine](frequently-asked-questions.md#widevine-streaming-for-android).

## <a name="additional-notes"></a>Další poznámky

Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="summary"></a>Souhrn

Tento článek popisuje, jak implementovat přehrávání offline režimu pro obsah DASH chráněný Widevine na zařízeních Android.  Odpověděl také na některé běžné otázky týkající se offline streamování chráněného obsahu Widevine.
