---
title: Online streamování Widevine pro Android
description: V tomto tématu se dozvíte, jak nakonfigurovat účet Azure Media Services V3 pro offline streamování chráněného obsahu Widevine.
services: media-services
author: willzhan
manager: femila
ms.service: media-services
ms.workload: media
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: c19a8dd4f9a4b715b5f81a5a349edbffc2f7dea2
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640691"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>Online streamování Widevine pro Android s Media Services V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Kromě ochrany obsahu pro online streamování, předplatné mediálního obsahu a služby pronájmu nabízí obsah ke stažení, který funguje, když nejste připojení k Internetu. Je možné, že budete muset stáhnout obsah na telefon nebo tablet pro přehrávání v režimu v letadle, když se dolétající z sítě. Další scénáře, ve kterých byste mohli chtít stáhnout obsah:

- Někteří poskytovatelé obsahu můžou zakázat doručování licencí DRM mimo hranici země nebo oblasti. Pokud chce uživatel sledovat obsah během cestování v cizině, je nutné offline stahování.
- V některých zemích nebo oblastech je dostupnost Internetu a/nebo šířka pásma omezená. Uživatelé si můžou stáhnout obsah, abyste si ho mohli prohlédnout dostatečně dostatečně jako v dostatečném rozlišení.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

Tento článek popisuje, jak implementovat přehrávání offline režimu pro ČÁRKOVANý obsah chráněný Widevine na zařízeních s Androidem. Technologie DRM v režimu offline vám umožňuje poskytovat pro svůj obsah modely předplatného, pronájmu a nákupu a umožnit tak zákazníkům služeb, aby při odpojení od internetu mohli obsah snadno přebírat.

Pro sestavování aplikací pro Android Player se vytvoří osnova tří možností:

> [!div class="checklist"]
> * Sestavení přehrávače pomocí rozhraní Java API sady ExoPlayer SDK
> * Sestavení přehrávače pomocí sady Xamarin Binding of ExoPlayer SDK
> * Sestavení přehrávače pomocí rozšíření EME (Encrypted Media Extension) a programu Media source Extension (MSE) v prohlížeči Chrome Mobile Browser v62 nebo novějším

Článek také obsahuje odpovědi na některé běžné otázky týkající se offline streamování chráněného obsahu Widevine.

> [!NOTE]
> Offline DRM se účtuje jenom při vytváření jediné žádosti o licenci při stažení obsahu. Neúčtují se žádné chyby.

## <a name="prerequisites"></a>Požadavky

Před implementací offline DRM pro Widevine v zařízeních s Androidem byste měli nejdřív:

- Seznamte se s koncepty představenými pro online ochranu obsahu pomocí Widevine DRM. Tato informace je podrobně popsaná v následujících dokumentech/ukázkách:
    - [Návrh systému ochrany obsahu s více variantami DRM s využitím řízení přístupu](design-multi-drm-system-with-access-control.md)
    - [Použití ochrany DRM s dynamickým šifrováním a služby doručování licencí](protect-with-drm.md)
- Klonovat https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git .

    Pokud chcete přidat konfigurace Widevine, budete muset změnit kód v části [šifrování pomocí DRM pomocí technologie .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) .  
- Seznamte se se sadou Google ExoPlayer SDK pro Android, což je open source sada pro Video Player, která podporuje offline přehrávání Widevine DRM. 
    - [Sada ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [Příručka pro vývojáře ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog pro vývojáře EoPlayer](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurace ochrany obsahu v Azure Media Services

V metodě [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) jsou k dispozici následující kroky:

1. Určete, jakým způsobem je doručování klíče obsahu autorizováno ve službě doručování licencí: 

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
2. Nakonfigurovat šablonu licence Widevine:  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. Vytvořit ContentKeyPolicyOptions:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Povolit offline režim

Pokud chcete povolit **offline** režim pro licence Widevine, musíte nakonfigurovat [šablonu licence Widevine](widevine-license-template-overview.md). V objektu **policy_overrides** nastavte vlastnost **can_persist** na **hodnotu true** (výchozí hodnota je false), jak je znázorněno v [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Konfigurace přehrávače pro Android pro přehrávání offline

Nejjednodušší způsob, jak vyvíjet nativní aplikaci pro hráče pro zařízení s Androidem, je použití [sady Google ExoPlayer SDK](https://github.com/google/ExoPlayer), open source sady Video Player SDK. ExoPlayer podporuje funkce, které nejsou aktuálně podporované nativním rozhraním MediaPlayer API Androidu, včetně protokolů MPEG-POMLČKy a Microsoft Smooth Streaming Delivery.

ExoPlayer verze 2,6 a vyšší zahrnuje mnoho tříd, které podporují přehrávání offline Widevine DRM. Konkrétně třída OfflineLicenseHelper poskytuje funkce nástroje pro usnadnění použití DefaultDrmSessionManager ke stažení, obnovení a uvolňování licencí offline. Třídy poskytované ve složce SDK "Library/Core/src/Main/Java/com/Google/Android/exoplayer2/offline/" podporují stahování obsahu offline videa.

Následující seznam tříd usnadňuje offline režim v sadě ExoPlayer SDK pro Android:

- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/OfflineLicenseHelper. Java  
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/DefaultDrmSession. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/DefaultDrmSessionManager. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/DrmSession. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/ErrorStateDrmSession. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/ExoMediaDrm. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/offline/SegmentDownloader. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/offline/DownloaderConstructorHelper. Java 
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/offline/stahovací. Java
- Library/pomlčka/src/Main/Java/com/Google/Android/exoplayer2/source/pomlčka/offline/DashDownloader. Java 

Vývojáři by měli v průběhu vývoje aplikace odkazovat na [příručku pro vývojáře ExoPlayer](https://google.github.io/ExoPlayer/guide.html) a na odpovídajícím [blogu pro vývojáře](https://medium.com/google-exoplayer) . Google nevydal plně dokumentovaný odkaz na implementaci nebo vzorový kód pro aplikaci ExoPlayer podporující Widevine v režimu offline, takže informace jsou omezené na průvodce a blog pro vývojáře. 

### <a name="working-with-older-android-devices"></a>Práce se staršími zařízeními s Androidem

U některých starších zařízení s Androidem je potřeba nastavit hodnoty pro následující vlastnosti **policy_overrides** (definované v [šabloně licence Widevine](widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds** a **license_duration_seconds**. Případně je můžete nastavit na hodnotu nula, což znamená nekonečné nebo neomezené trvání.  

Hodnoty musí být nastaveny, aby nedocházelo k chybě přetečení celého čísla. Další vysvětlení tohoto problému naleznete v tématu https://github.com/google/ExoPlayer/issues/3150 a https://github.com/google/ExoPlayer/issues/3112 . <br/>Pokud hodnoty explicitně nenastavíte, budou přiřazeny velmi velké hodnoty pro  **PlaybackDurationRemaining** a **LicenseDurationRemaining** (například 9223372036854775807, což je maximální kladná hodnota pro 64 celé číslo). V důsledku toho se zdá, že licence Widevine vypršela, a proto se k dešifrování nestane. 

K tomuto problému nedochází na Androidu 5,0, nebo novějším, protože Android 5,0 je první verze Androidu, která je navržená tak, aby plně podporovala ARMv8 ([Advanced RISC počítač](https://en.wikipedia.org/wiki/ARM_architecture)) a 64-bitové platformy, zatímco Android 4,4 KitKat byl původně navržený tak, aby podporoval ARMv7 a 32 platformy jako jiné starší verze Androidu.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Použití Xamarin k sestavení aplikace pro přehrávání v Androidu

Vazby Xamarin pro ExoPlayer můžete najít pomocí následujících odkazů:

- [Knihovna vazeb Xamarin pro knihovnu Google ExoPlayer](https://github.com/martijn00/ExoPlayerXamarin)
- [Vazby Xamarin pro ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Podívejte se také na následující vlákno: [vytvoření vazby Xamarin](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Aplikace pro Chrome Player pro Android

Od verze [Chrome pro Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates)se podporuje trvalá licence v EME. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) se teď podporuje i v Chrome pro Android. To vám umožní vytvářet offline aplikace pro přehrávání v Chrome, Pokud koncoví uživatelé mají tuto (nebo vyšší) verzi Chrome. 

Kromě toho společnost Google vytvořila ukázku progresivní webové aplikace (PWA) a open-source IT: 

- [Zdrojový kód](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Hostovaná verze Google](https://biograf-155113.appspot.com/ttt/episode-2/) (funguje jenom v Chrome v 62 a vyšších na zařízeních s Androidem)

Pokud upgradujete mobilní prohlížeč Chrome na v62 (nebo vyšší) na telefonu s Androidem a otestujete výše uvedenou ukázkovou aplikaci, uvidíte, že online streamování a offline přehrávání funguje.

Výše uvedená Open Source aplikace PWA je vytvořená v Node.js. Pokud chcete hostovat svou vlastní verzi na serveru Ubuntu, pamatujte na následující běžné problémy, které můžou bránit přehrávání:

1. Problém CORS: ukázkové video v ukázkové aplikaci je hostované v https://storage.googleapis.com/biograf-video-files/videos/ . Google nastavila CORS pro všechny testovací vzorky hostované v sadě Google Cloud Storage. Jsou obsluhovány pomocí hlaviček CORS a explicitně specifikují položku CORS: `https://biograf-155113.appspot.com` (doména, ve které hostuje vaše ukázka) znemožňuje přístup jakýmkoli jiným webům. Pokud se pokusíte, zobrazí se následující chyba protokolu HTTP: `Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Problém s certifikátem: od verze Chrome v 58 EME pro Widevine vyžaduje protokol HTTPS. Proto je třeba hostovat ukázkovou aplikaci přes protokol HTTPS s certifikátem x509. Běžný testovací certifikát nefunguje v důsledku následujících požadavků: potřebujete získat certifikát splňující následující minimální požadavky:
    - Chrome a Firefox vyžadují, aby v certifikátu existovalo nastavení alternativního názvu SAN-Subject.
    - Certifikát musí mít důvěryhodnou certifikační autoritu a vývojový certifikát podepsaný svým držitelem nefunguje.
    - Certifikát musí obsahovat CN, který odpovídá názvu DNS webového serveru nebo brány.

## <a name="more-information"></a>Další informace

Další informace najdete v tématu [Widevine v kolekci otázek](questions-collection.md#widevine-streaming-for-android).

Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.
