---
title: Konfigurace účtu pro offline datové proudy Widevine chráněný obsah, Azure
description: Toto téma ukazuje postup konfigurace účtu Azure Media Services pro offline datové proudy Widevine chráněný obsah.
services: media-services
keywords: DASH, DRM, Widevine Offline režimu, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: 158b58c13aee4d6241900db4a5e2b3fe8a45cc3c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32178460"
---
# <a name="offline-widevine-streaming-for-android"></a>Offline Widevine streamování pro Android

Kromě ochrany obsahu pro online streamování, média obsahu předplatné a pronájem služby nabídka ke stažení obsahu, který funguje, když nejsou připojeni k Internetu. Možná budete muset stáhnout obsah do vašeho telefonu nebo tabletu pro přehrávání v režim v letadle při plující pod odpojen od sítě. Další scénáře, ve kterých můžete chtít stahování obsahu:

- Někteří poskytovatelé obsahu může zakáže doručování licencí DRM mimo hranice této země. Pokud si chcete přehrát obsahu při cestování v zahraničí chce uživatel, je potřeba offline stahování.
- V některých zemích je omezená dostupnost Internet nebo šířky pásma. Uživatelé mohou rozhodnout pro stažení obsahu, abyste mohli sledovat dostatečně vysoký rozlišení uspokojivé zobrazení prostředí.

Tento článek popisuje, jak implementovat offline režimu přehrávání obsahu DASH chráněn Widevine na zařízeních s Androidem. Offline DRM umožňuje poskytovat předplatné, pronájem a nákup modely pro obsah, povolení zákazníkům vašich služeb snadno získat obsahu s nimi při odpojení od Internetu.

Pro vytvoření aplikace pro Android player, jsme popisují tři možnosti:

> [!div class="checklist"]
> * Vytvořit přehrávač pomocí rozhraní API Java z ExoPlayer SDK
> * Vytvořit vazbu Xamarin SDK ExoPlayer přehrávač
> * Vytvořit přehrávač pomocí šifrované rozšíření média (EME) a rozšíření zdrojového média (MSE) v v62 mobilní prohlížeče Chrome nebo vyšší

V článku také odpovídá na některé běžné dotazy týkající se offline streamování Widevine chráněný obsah.

## <a name="requirements"></a>Požadavky 

Před implementací offline DRM pro Widevine na zařízeních s Androidem, měli byste nejdřív:

- Seznamte s koncepty představenými pro online ochranu obsahu pomocí Widevine DRM. To je podrobně popsány v následující dokumenty nebo ukázky:
    - [Pomocí Azure Media Services pro doručování licencí DRM nebo klíčů AES](media-services-deliver-keys-and-licenses.md)
    - [CENC využívající Multi-DRM a Access Control: Referenční návrh a implementace v prostředí Azure a Azure Media Services](media-services-cenc-with-multidrm-access-control.md)
    - [Použití běžného šifrování PlayReady nebo Widevine dynamické s rozhraním .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Pomocí Azure Media Services pro doručování licencí PlayReady nebo Widevine s rozhraním .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Seznamte se s Google ExoPlayer SDK pro Android, přehrávání videa na open source SDK podporuje offline přehrávání Widevine DRM. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [Příručka vývojáře ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog vývojářů EoPlayer](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Konfigurace ochrany obsahu ve službě Azure Media Services

Když konfigurujete ochranu Widevine prostředek ve službě Media Services, budete muset vytvořit ContentKeyAuthorizationPolicyOption, které určuje následující tři věci:

1. Systém DRM (Widevine)
2. Určení jak obsahu doručení klíče ContentKeyAuthorizationPolicyRestriction oprávněný v službu doručování licencí (otevřené nebo tokenu autorizace)
3. Šablona licence DRM (Widevine)

Chcete-li povolit **offline** režim pro licence na Widevine, budete muset nakonfigurovat [šablona licence Widevine](media-services-widevine-license-template-overview.md). V **policy_overrides** objektu, nastavte **can_persist** vlastnost **true** (výchozí hodnota je false). 

Následující příklad kódu používá rozhraní .NET a umožňuje **offline** režim pro licence na Widevine. Kód je založen na [ pomocí PlayReady nebo Widevine běžného dynamického šifrování s .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) ukázka. 

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
            //can_renew = true,                             //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),   //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
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

## <a name="configuring-the-android-player-for-offline-playback"></a>Konfigurace Android player pro offline přehrávání

Nejjednodušší způsob, jak vyvíjet player nativní aplikace pro zařízení se systémem Android se má používat [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), přehrávání videa na open source SDK. ExoPlayer podporuje funkce není aktuálně podporovaná na Android nativní Media Player API, včetně protokolů doručení MPEG-DASH a Microsoft technologie Smooth Streaming.

ExoPlayer verze 2.6 a vyšší zahrnuje mnoho tříd, které podporují offline přehrávání Widevine DRM. Konkrétně třída OfflineLicenseHelper poskytuje nástroj funkce pro usnadnění používání DefaultDrmSessionManager pro stahování, obnovení a uvolněním offline licencí. Třídy ve složce SDK "knihovny nebo základní nebo src/hlavní/java/com nebo google/android/exoplayer2 nebo jsou offline nebo" podporu offline video obsahu stahování.

Následující seznam tříd usnadnění offline režimu v ExoPlayer SDK pro Android:

- Library/Core/src/Main/Java/COM/Google/Android/exoplayer2/DRM/OfflineLicenseHelper.Java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- Library/Core/src/Main/Java/COM/Google/Android/exoplayer2/DRM/DefaultDrmSessionManager.Java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- Library/Core/src/Main/Java/COM/Google/Android/exoplayer2/offline/SegmentDownloader.Java
- Library/Core/src/Main/Java/COM/Google/Android/exoplayer2/offline/DownloaderConstructorHelper.Java 
- Library/Core/src/Main/Java/COM/Google/Android/exoplayer2/offline/Downloader.Java
- Library/Dash/src/Main/Java/COM/Google/Android/exoplayer2/Source/Dash/offline/DashDownloader.Java 

Vývojáři by měla odkazovat [Příručka vývojáře ExoPlayer](https://google.github.io/ExoPlayer/guide.html) a odpovídající [Blog vývojářů](https://medium.com/google-exoplayer) během vývoje aplikace. Google nevydala plně zdokumentovaných odkaz implementace a ukázkový kód pro aplikace ExoPlayer nepodporuje Widevine do režimu offline v současné době, a proto je omezený na blog Příručka pro vývojáře a informace. 

### <a name="working-with-older-android-devices"></a>Práce s starší zařízení se systémem Android

Pro některá starší zařízení s Androidem, musíte nastavit hodnoty pro následující **policy_overrides** vlastnosti (definovaná v [šablona licence Widevine](media-services-widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds**, a **license_duration_seconds**. Případně je můžete nastavit na nulu, což znamená nekonečné/neomezenou dobu.  

Hodnoty musí být nastavena, aby nedošlo přetečení chyb celé číslo. Další informace o problému naleznete v části https://github.com/google/ExoPlayer/issues/3150 a https://github.com/google/ExoPlayer/issues/3112. <br/>Pokud nenastavíte explicitně, hodnoty velmi velkých hodnot pro **PlaybackDurationRemaining** a **LicenseDurationRemaining** přiřazen, (například 9223372036854775807, což je maximální kladnou hodnotu pro 64bitové celé číslo). V důsledku toho zobrazí vypršela platnost licence Widevine a proto nedojde k ní dešifrování. 

Tento problém se nevyskytuje v typu Lupa Android 5.0 nebo novějším vzhledem k tomu, že Android 5.0 je první verzi systému Android, které byly navržené tak, aby plně podporoval ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) a 64bitová verze platformy, při Android 4.4 KitKat původně navržený pro podporu ARMv7 a 32bitové platformy jako s jiné starší verze Android.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Pomocí Xamarin sestavit aplikaci pro Android přehrávání

Vazby Xamarin pro ExoPlayer můžete najít pomocí následujících odkazů:

- [Knihovna vazby Xamarin pro knihovnu Google ExoPlayer](https://github.com/martijn00/ExoPlayerXamarin)
- [Vazby Xamarin pro ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Další informace naleznete v následujících vlákno: [Xamarin vazby](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Přehrávač aplikace pro Chrome pro Android

Od verze služby [Chrome pro Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), trvalé licence v EME je podporována. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) je nyní podporováno také v Chrome pro Android. To umožňuje vytvářet offline přehrávání aplikace v prohlížeči Chrome, pokud vaši koncoví uživatelé mají tato (nebo vyšší) verzi Chrome. 

Kromě toho má Google vytvořeného ukázku progresivní webové aplikace (aplikace PWA) a open source ho: 

- [Zdrojový kód](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Verze Google hostované](https://biograf-155113.appspot.com/ttt/episode-2/) (funguje jen v Chrome v 62 a vyšší na zařízeních s Androidem)

Pokud upgradujete mobilní prohlížeč Chrome v62 (nebo vyšší) na telefon se systémem Android a testovací výše hostované ukázkové aplikace, zobrazí se tento online streamování a offline přehrávání fungovat.

Výše uvedené aplikace open-source aplikace PWA je vytvořené v Node.js. Pokud chcete hostovat vlastní verzi na serveru Ubuntu, mějte na paměti následující běžné došlo k problémy, které mohou bránit přehrávání:

1. Problém CORS: ukázkové video v ukázkové aplikace je hostován v https://storage.googleapis.com/biograf-video-files/videos/. CORS pro všechny své testovací vzorky hostované v úložiště v cloudu Google sady nastavil Google. Se zpracovávají s hlavičky CORS, zadání explicitně položka CORS: https://biograf-155113.appspot.com (doménou, ve které google hostuje jejich ukázkové) brání přístupu pomocí jiných webů. Když zkusíte, zobrazí se následující chyba HTTP: Nepodařilo se načíst https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: žádné záhlaví 'přístup-Control-Allow-Origin, se nachází na požadovaný prostředek. Původ 'https://13.85.80.81:8080, není proto povoleno přístup. Pokud odpověď neprůhledného slouží vašim potřebám, nastavte režim žádosti na, ne cors' načíst prostředek s CORS zakázána.
2. Certifikát problém: od Chrome v 58, EME pro Widevine vyžaduje protokol HTTPS. Proto musíte pro hostování ukázkové aplikace prostřednictvím protokolu HTTPS s X509 certifikátu. Obvyklé testovací certifikát nefunguje kvůli následující požadavky: je potřeba získat certifikát splňuje následující požadavky:
    - Chrome a Firefox vyžadují nastavení alternativní název subjektu SAN existovat v certifikátu
    - Musí mít certifikát důvěryhodné certifikační Autority a certifikát podepsaný svým držitelem vývoj nefunguje.
    - Certifikát musí mít CN, odpovídající názvu DNS z webového serveru nebo brány

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="question"></a>Otázka

Jak můžete poskytovat trvalé licence (offline povolit) pro některé klienty nebo uživatele a dočasnou licence (offline zakázáno) pro ostatní? Je nutné se duplicitní obsah a použít samostatnou klíč obsahu?

### <a name="answer"></a>Odpověď
Není nutné duplicitní obsah. Jednoduše můžete použít jednu kopii obsahu a jeden ContentKeyAuthorizationPolicy, ale dvě samostatné ContentKeyAuthorizationPolicyOption na:

1. IContentKeyAuthorizationPolicyOption 1: používá trvalé licence a ContentKeyAuthorizationPolicyRestriction 1, která obsahuje deklaraci identity například license_type = "Trvalá"
2. IContentKeyAuthorizationPolicyOption 2: používá dočasnou licence a ContentKeyAuthorizationPolicyRestriction 2, který obsahuje deklaraci identity například license_type = "Nonpersistent"

Tímto způsobem, když požadavek na licenční pochází z klientské aplikace, z požadavek na licenční není žádný rozdíl. Pro jiné koncový uživatel nebo zařízení, ale služba tokenů zabezpečení musí mít obchodní logiky k vydávání různých tokeny JWT obsahující různé deklarace (jeden z výše uvedených dvou license_type). Hodnota deklarace identity v JWT token se použije k rozhodování o službou licence k vydávání jaký typ licence: trvalé nebo dočasné.

To znamená, že musí mít obchodní logiky a klientů a zařízení informace o přidání odpovídající hodnota deklarace do tokenu zabezpečení Token Service (Služba tokenů zabezpečení).

### <a name="question"></a>Otázka

Úrovně zabezpečení Widevine, Google [přehled architektury Widevine DRM doc](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) dokumentace, definuje tři různé úrovně zabezpečení. Ale v [dokumentace Azure Media Services na šablonu licence Widevine](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), jsou popsané pěti různé úrovně zabezpečení. Co je vztah nebo mapování mezi dvěma sadami různé úrovně zabezpečení?

### <a name="answer"></a>Odpověď

V Google [přehled architektury Widevine DRM](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf), definuje následující úrovně tři zabezpečení:

1.  Úroveň zabezpečení 1: Zpracování obsahu, šifrování a řízení se provádí v rámci důvěryhodné spouštěcí prostředí (typu t). V některých modelů implementace může provést v různých čipy zabezpečení zpracování.
2.  Úroveň zabezpečení 2: Provádí šifrování (ale ne video zpracování) v rámci typu t: dešifrovaný vyrovnávací paměti jsou vráceny do domény aplikace a zpracovat prostřednictvím samostatné video hardwaru nebo softwaru. Na úrovni 2, ale kryptografické informace jsou stále zpracovávány pouze v rámci typu t.
3.  Úroveň zabezpečení 3 nemá typu t na zařízení. K ochraně kryptografických informace a dešifrovaný obsah na hostitelský operační systém, mohou být přijata příslušné opatření. Úroveň 3 implementace může také zahrnovat kryptografický modul hardwaru, ale který jenom zvyšuje výkon, není zabezpečení.

Současně, v [dokumentace Azure Media Services na šablonu licence Widevine](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), vlastnost security_level content_key_specs může mít následující pěti různých hodnot (robustnost požadavky na klienta pro přehrávání):

1.  Na základě softwaru whitebox kryptografických je povinný.
2.  Kryptografických softwaru a zakódovaná dekodér je vyžadován.
3.  Kryptografické operace a materiál klíče, musí provést v rámci hardwaru zálohovaný typu t.
4.  Kryptografické a dekódování obsahu, musí provést v rámci hardwaru zálohovaný typu t.
5.  Kryptografický dekódování rozděleny a všechny zpracování média (komprimovaným a nekomprimovaným) musí být v rámci hardwaru zálohovaný typu t.

Obě úrovně zabezpečení jsou definovány Google Widevine. Rozdíl je v jeho úroveň využití: architektura úrovni nebo rozhraní API. Úrovně pět zabezpečení se používají v rozhraní API Widevine. Content_key_specs objekt, který obsahuje security_level je deserializovat a předaný službu Widevine globální doručování licencí službou Azure Media Services Widevine. Následující tabulka uvádí mapování mezi dvěma sadami úrovní zabezpečení.

| **Úrovně zabezpečení, které jsou definované v architektuře Widevine** |**Úrovně zabezpečení používané v Widevine rozhraní API**|
|---|---| 
| **Úroveň zabezpečení 1**: zpracování obsahu, šifrování a řízení se provádí v rámci důvěryhodné spouštěcí prostředí (typu t). V některých modelů implementace může provést v různých čipy zabezpečení zpracování.|**security_level = 5**: kryptografický dekódování rozděleny a všechny zpracování média (komprimovaným a nekomprimovaným) musí být v rámci hardwaru zálohovaný typu t.<br/><br/>**security_level = 4**: kryptografie a dekódování obsahu, musí provést v rámci hardwaru zálohovaný typu t.|
**Úroveň zabezpečení 2**: provádí šifrování (ale ne video zpracování) v rámci typu t: dešifrovaný vyrovnávací paměti jsou vráceny do domény aplikace a zpracovat prostřednictvím samostatné video hardwaru nebo softwaru. Na úrovni 2, ale kryptografické informace jsou stále zpracovávány pouze v rámci typu t.| **security_level = 3**: materiál klíče a kryptografické operace musí provést v rámci hardwaru zálohovaný typu t. |
| **Úroveň zabezpečení 3**: nemá typu t na zařízení. K ochraně kryptografických informace a dešifrovaný obsah na hostitelský operační systém, mohou být přijata příslušné opatření. Úroveň 3 implementace může také zahrnovat kryptografický modul hardwaru, ale který jenom zvyšuje výkon, není zabezpečení. | **security_level = 2**: kryptografických softwaru a zakódovaná dekodér je vyžadován.<br/><br/>**security_level = 1**: na základě softwaru whitebox kryptografických je vyžadován.|

### <a name="question"></a>Otázka

Proč stahování obsahu trvá tak dlouho?

### <a name="answer"></a>Odpověď

Aby se zvýšila rychlost stahování dvěma způsoby:

1.  Povolte CDN tak, aby koncoví uživatelé budou s větší pravděpodobností narazí CDN místo původu nebo streamování koncový bod pro stahování obsahu. Pokud uživatel dotkne koncový bod streamování, každý HLS segment nebo DASH fragment je dynamicky zabalené a šifrována. I když tato čekací doba v milisekundách škálování pro každý segment/fragment, když máte dlouho video hodinu, může být velký, způsobí delší stažení Akumulovaná latence.
2.  Poskytnout tak koncovým uživatelům možnost selektivně stáhnout vrstvy kvalitu videa a audia sleduje místo veškerý obsah. Pro režim offline neexistuje žádný bod ke stažení všechny vrstvy kvality. Chcete-li dosáhnout dvěma způsoby:
    1.  Klient řídí: player aplikace automaticky vybere nebo uživatel vybere vrstvu kvalitu videa a audia sleduje ke stažení;
    2.  Služba řídí: jeden můžete použít funkci dynamické Manifest ve službě Azure Media Services k vytvoření (globální) filtr, který omezuje seznam stop HLS nebo DASH MPD do vrstvy jeden kvalitu videa a vybrané zvukových stop. Adresa URL pro stahování uvedené koncovým uživatelům pak bude obsahovat tento filtr.

## <a name="summary"></a>Souhrn

Tento článek popsané postupy pro implementaci offline režimu přehrávání obsahu DASH chráněn Widevine na zařízeních s Androidem.  Uživatel také odpovídá některé běžné dotazy týkající se offline streamování Widevine chráněný obsah.
