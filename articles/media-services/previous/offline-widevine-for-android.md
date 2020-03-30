---
title: Konfigurace účtu pro offline streamování obsahu chráněného přes Widevine – Azure
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
ms.date: 04/16/2019
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: f3bd7bc78eeb62cc33a01ed31bb04d94078cae4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294335"
---
# <a name="offline-widevine-streaming-for-android"></a>Streamování Widevine pro Android v offline režimu  

> [!div class="op_single_selector" title1="Vyberte verzi služby Media Services, kterou používáte:"]
> * [Verze 3](../latest/offline-widevine-for-android.md)
> * [Verze 2](offline-widevine-for-android.md)

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

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

## <a name="requirements"></a>Požadavky 

Před implementací offline DRM pro Widevine na zařízeních se systémem Android byste měli nejprve:

- Seznamte se s koncepty zavedenými pro ochranu obsahu online pomocí widevine DRM. To je podrobně popsáno v následujících dokumentech/vzorcích:
    - [Doručování licencí DRM nebo klíčů AES pomocí služby Azure Media Services](media-services-deliver-keys-and-licenses.md)
    - [CENC využívající Multi-DRM a Access Control: Referenční návrh a implementace v prostředí Azure a Azure Media Services](media-services-cenc-with-multidrm-access-control.md)
    - [Použití funkce PlayReady a/nebo Widevine Dynamic Common Encryption s rozhraním .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Doručování licencí PlayReady a/nebo Widevine pomocí rozhraní .NET pomocí služby Azure Media Services](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Seznamte se s Google ExoPlayer SDK pro Android, open-source video přehrávač SDK schopné podporovat offline Widevine DRM přehrávání. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [Průvodce pro vývojáře exoplayeru](https://google.github.io/ExoPlayer/guide.html)
    - [Blog pro vývojáře eoPlayer](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Konfigurace ochrany obsahu ve službě Azure Media Services

Při konfiguraci Widevine ochranu prostředku ve službě Media Services, je třeba vytvořit ContentKeyAuthorizationPolicyOption, který specifikoval následující tři věci:

1. Drm systém (Widevine)
2. ContentKeyAuthorizationPolicyRestriction určující, jak je doručování klíčů obsahu autorizováno ve službě doručování licencí (open nebo autorizace tokenu)
3. Šablona licence DRM (Widevine)

Chcete-li povolit **režim offline** pro licence Widevine, je třeba nakonfigurovat [šablonu licence Widevine](media-services-widevine-license-template-overview.md). V **objektu policy_overrides** nastavte vlastnost **can_persist** na **hodnotu true** (výchozí hodnota je false). 

Následující ukázka kódu používá rozhraní .NET k povolení **režimu offline** pro licence Widevine. Kód je založen na [použití PlayReady a/nebo Widevine Dynamic Common Encryption s](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) ukázkou rozhraní .NET. 

```
private static string ConfigureWidevineLicenseTemplateOffline(Uri keyDeliveryUrl)
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            //can_renew = true,                                //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),    //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
            can_renew = false,
            //rental_duration_seconds = 1209600,
            //playback_duration_seconds = 1209600,
            //license_duration_seconds = 1209600
        }
        };

    string configuration = Newtonsoft.Json.JsonConvert.SerializeObject(template);
    return configuration;
}
```

## <a name="configuring-the-android-player-for-offline-playback"></a>Konfigurace přehrávače Android pro přehrávání offline

Nejjednodušší způsob, jak vytvořit nativní přehrávač aplikace pro zařízení se systémem Android je použít [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), open-source video přehrávač SDK. ExoPlayer podporuje funkce, které nejsou v současné době podporovány nativním rozhraním MediaPlayer API systému Android, včetně protokolů MPEG-DASH a Microsoft Smooth Streaming.

ExoPlayer verze 2.6 a vyšší obsahuje mnoho tříd, které podporují přehrávání Widevine DRM offline. Třída OfflineLicenseHelper zejména poskytuje funkce nástrojů usnadňujících použití defaultDrmSessionManager pro stahování, obnovu a uvolňování offline licencí. Třídy uvedené ve složce SDK "knihovna / jádro / src / main / java / com / google / android / exoplayer2 / offline / " podporují stahování offline video obsahu.

Následující seznam tříd usnadňuje režim offline v exoplayer sdk pro Android:

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

Vývojáři by měli odkazovat na [ExoPlayer Developer Guide](https://google.github.io/ExoPlayer/guide.html) a odpovídající [Developer Blog](https://medium.com/google-exoplayer) během vývoje aplikace. Google nevydala plně zdokumentované referenční implementace nebo ukázkový kód pro aplikaci ExoPlayer podporující Widevine offline v tomto okamžiku, takže informace jsou omezeny na vývojáře průvodce a blog. 

### <a name="working-with-older-android-devices"></a>Práce se staršími zařízeními Android

U některých starších zařízení se systémem Android je nutné nastavit hodnoty pro následující **vlastnosti policy_overrides** (definované v [šabloně licence Widevine:](media-services-widevine-license-template-overview.md) **rental_duration_seconds**, **playback_duration_seconds**a **license_duration_seconds**. Případně je můžete nastavit na nulu, což znamená nekonečnou/neomezenou dobu trvání.  

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

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="question"></a>Otázka

Jak mohu dodat trvalé licence (offline povoleno) pro některé klienty / uživatele a netrvalé licence (offline zakázáno) pro ostatní? Musím duplikovat obsah a používat samostatný klíč obsahu?

### <a name="answer"></a>Odpověď
Není nutné duplikovat obsah. Můžete jednoduše použít jednu kopii obsahu a jeden ContentKeyAuthorizationPolicy, ale dva samostatné ContentKeyAuthorizationPolicyOption je:

1. IContentKeyAuthorizationPolicyOption 1: používá trvalou licenci a ContentKeyAuthorizationPolicyRestriction 1, která obsahuje deklaraci, například license_type = "Persistent"
2. IContentKeyAuthorizationPolicyOption 2: Používá netrvalou licenci a ContentKeyAuthorizationPolicyRestriction 2, která obsahuje deklaraci, například license_type = "Netrvalý"

Tímto způsobem, když žádost o licenci pochází z klientské aplikace, z žádosti o licenci není žádný rozdíl. Však pro různé koncového uživatele/zařízení, STS by měl mít obchodní logiku vydávat různé Tokeny JWT obsahující různé deklarace identity (jeden z výše uvedených dvou license_type). Hodnota deklarací identity v tokenu JWT se použije k rozhodnutí licenční služby o vydání typu licence: trvalé nebo trvalé.

To znamená, že služba Secure Token Service (STS) musí mít obchodní logiku a informace o klientovi nebo zařízení, aby mohla přidat odpovídající hodnotu deklarace do tokenu.

### <a name="question"></a>Otázka

Pro úrovně zabezpečení Widevine, v [dokumentaci dokumentu Google Widevine DRM Architecture Overview](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) definuje tři různé úrovně zabezpečení. V [dokumentaci k Mediální službě Azure v šabloně licence Widevine](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview)je však uvedeno pět různých úrovní zabezpečení. Jaký je vztah nebo mapování mezi dvěma různými sadami úrovní zabezpečení?

### <a name="answer"></a>Odpověď

V [přehledu architektury Widevine DRM společnosti](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)Google definuje následující tři úrovně zabezpečení:

1.  Úroveň zabezpečení 1: Veškeré zpracování obsahu, kryptografie a řízení se provádějí v prostředí trusted execution environment (TEE). V některých modelech implementace může být zpracování zabezpečení prováděno v různých čipech.
2.  Úroveň zabezpečení 2: Provádí kryptografii (ale ne zpracování videa) v rámci TEE: dešifrované vyrovnávací paměti jsou vráceny do domény aplikace a zpracovány prostřednictvím samostatného grafického hardwaru nebo softwaru. Na úrovni 2 jsou však kryptografické informace stále zpracovávány pouze v rámci TEE.
3.  Úroveň zabezpečení 3 nemá v zařízení TEE. Mohou být přijata vhodná opatření k ochraně kryptografických informací a dešifrovaného obsahu v hostitelském operačním systému. Implementace úrovně 3 může také obsahovat hardwarový kryptografický modul, ale to pouze zvyšuje výkon, nikoli zabezpečení.

Současně v [dokumentaci k Mediální službě Azure na šabloně licence Widevine](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview)může mít vlastnost security_level content_key_specs následujících pět různých hodnot (požadavky na odolnost klientů pro přehrávání):

1.  Je vyžadováno softwarové šifrování whitebox.
2.  Je vyžadováno softwarové krypto a zamlžený dekodér.
3.  Klíčové materiálové a kryptografické operace musí být prováděny v rámci tee podporovaného hardwarem.
4.  Šifrování a dekódování obsahu musí být provedeno v rámci hardwarového tee.
5.  Šifrování, dekódování a veškerá manipulace s médii (komprimovaná a nekomprimovaná) musí být zpracována v rámci hardwarově podporované tee.

Obě úrovně zabezpečení jsou definovány společností Google Widevine. Rozdíl je v jeho úrovni využití: úroveň architektury nebo úroveň rozhraní API. Pět úrovní zabezpečení se používá v rozhraní WIDEVINE API. Objekt content_key_specs, který obsahuje security_level je deserializován a předán službě widevine globální doručovací služby služby Azure Media Services Widevine licence. V následující tabulce je zobrazeno mapování mezi dvěma sadami úrovní zabezpečení.

| **Úrovně zabezpečení definované v rozsáhlé architektuře** |**Úrovně zabezpečení používané v rozhraní Widevine API**|
|---|---| 
| **Úroveň zabezpečení 1**: Veškeré zpracování obsahu, kryptografie a řízení se provádějí v prostředí trusted execution environment (TEE). V některých modelech implementace může být zpracování zabezpečení prováděno v různých čipech.|**security_level=5**: S kryptografickým, dekódovacím a veškerým zpracováním médií (komprimovaných a nekomprimovaných) musí být nakládáno v rámci tee podporovaného hardwarem.<br/><br/>**security_level=4**: Šifrování a dekódování obsahu musí být provedeno v rámci tee podporovaného hardwarem.|
**Úroveň zabezpečení 2**: Provádí kryptografii (ale ne zpracování videa) v rámci TEE: dešifrované vyrovnávací paměti jsou vráceny do domény aplikace a zpracovány prostřednictvím samostatného grafického hardwaru nebo softwaru. Na úrovni 2 jsou však kryptografické informace stále zpracovávány pouze v rámci TEE.| **security_level=3**: Klíčové materiálové a kryptografické operace musí být prováděny v rámci TEE podporovaného hardwarem. |
| **Úroveň zabezpečení 3**: Nemá v zařízení TEE. Mohou být přijata vhodná opatření k ochraně kryptografických informací a dešifrovaného obsahu v hostitelském operačním systému. Implementace úrovně 3 může také obsahovat hardwarový kryptografický modul, ale to pouze zvyšuje výkon, nikoli zabezpečení. | **security_level=2**: Je vyžadováno softwarové krypto a zamlžený dekodér.<br/><br/>**security_level=1**: Je vyžadováno softwarové šifrování whiteboxu.|

### <a name="question"></a>Otázka

Proč stahování obsahu trvá tak dlouho?

### <a name="answer"></a>Odpověď

Rychlost stahování lze zlepšit dvěma způsoby:

1.  Povolte CDN, aby koncoví uživatelé s větší pravděpodobností stiskli cdn místo koncového bodu původu/streamování pro stahování obsahu. Pokud uživatel narazí na koncový bod streamování, každý segment HLS nebo dash fragment je dynamicky zabalen a šifrován. I když je tato latence v milisekundovém měřítku pro každý segment nebo fragment, pokud máte hodinové video, akumulovaná latence může být velká, což způsobuje delší stahování.
2.  Poskytněte koncovým uživatelům možnost selektivně stahovat vrstvy kvality videa a zvukové stopy namísto veškerého obsahu. Pro režim offline nemá smysl stahovat všechny vrstvy kvality. Existují dva způsoby, jak toho dosáhnout:
    1.  Klientřízeno: buď přehrávač aplikace automaticky vybere nebo uživatel vybere vrstvu kvality videa a zvukové stopy ke stažení;
    2.  Řízeno službou: pomocí funkce dynamického manifestu ve službě Azure Media Services můžete vytvořit (globální) filtr, který omezuje seznam stop HLS nebo DASH MPD na jednu vrstvu kvality videa a vybrané zvukové stopy. Pak bude tento filtr obsahovat adresa URL pro stažení prezentovaná koncovým uživatelům.

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="summary"></a>Souhrn

Tento článek popisuje, jak implementovat přehrávání offline režimu pro obsah DASH chráněný Widevine na zařízeních Android.  Odpověděl také na některé běžné otázky týkající se offline streamování chráněného obsahu Widevine.
