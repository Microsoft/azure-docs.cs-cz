---
title: Nastavte v účtu pro offline streamování obsahu Widevine chránit – Azure
description: Toto téma ukazuje postup při konfiguraci účtu Azure Media Services pro offline datové proudy Widevine chráněný obsah.
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
ms.date: 02/08/2019
ms.author: willzhan, dwgeo
ms.openlocfilehash: 13042a25a0bf106dd579e7d5e8cf8553a79c8f00
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56002078"
---
# <a name="offline-widevine-streaming-for-android"></a>Offline Widevine streamování pro Android  

Kromě ochrany obsah pro streamování online, obsah média předplatného a pronájem služby nabídky ke stažení obsahu, který funguje, když nejste připojeni k Internetu. Možná budete muset stáhnout veškerý obsah na váš telefon nebo tablet pro přehrávání v případě, že letíte režim v letadle odpojen od sítě. Další scénáře, ve kterých můžete chtít stahovat obsah:

- Někteří poskytovatelé obsahu může zakázat doručování licencí DRM nad rámec ohraničení země. Pokud chce uživatel sledovat obsah v zahraničí cestách, je potřeba ke stažení offline.
- V některých zemích je omezená dostupnost Internetu a/nebo šířky pásma. Uživatelé se můžou rozhodnout ke stahování obsahu se moci podívat v dostatečně vysoká řešení vyhovující zážitek.

Tento článek popisuje, jak implementovat offline režimu přehrávání pro DASH obsah chráněný pomocí Widevine na zařízeních s Androidem. Offline DRM můžete zadat předplatné, pronájem a nákupní modely pro obsah, takže se zákazníci můžou vašich služeb snadno získat obsahu s nimi při odpojení od Internetu.

Pro vytváření aplikací pro Android player, uvádíme tři možnosti:

> [!div class="checklist"]
> * Vytvořit pomocí jazyka Java API ExoPlayer SDK přehrávač
> * Vytvořit přehrávač Xamarin vazbu ExoPlayer SDK
> * Vytvořit přehrávač použitím rozšíření eme (Encrypted Media Extensions) a rozšíření zdroj média (MSE) v62 mobilního prohlížeče Chrome nebo novější

V článku najdete tady také odpovědi některé běžné dotazy týkající se offline streamování Widevine chráněný obsah.

## <a name="requirements"></a>Požadavky 

Před implementací offline DRM pro Widevine na zařízeních s Androidem, měli byste nejdřív:

- Seznamte se s koncepty představenými pro online ochrany obsahu pomocí Widevine DRM. To je podrobně popsána v následujících dokumentech/samples:
    - [Používání Azure Media Services k doručování licencí DRM nebo AES klíče](media-services-deliver-keys-and-licenses.md)
    - [CENC využívající Multi-DRM a Access Control: Referenční návrh a implementace v prostředí Azure a Azure Media Services](media-services-cenc-with-multidrm-access-control.md)
    - [Použití běžného šifrování PlayReady nebo Widevine dynamické pomocí .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Doručování licencí PlayReady nebo Widevine s .NET pomocí Azure Media Services](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Seznamte se s Google ExoPlayer SDK pro Android, přehrávače videa na open-source SDK schopný zajistit podporu offline přehrávání Widevine DRM. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [Příručka pro vývojáře ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog pro vývojáře EoPlayer](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Konfigurace ochrany obsahu ve službě Azure Media Services

Když konfigurujete ochranu Widevine prostředku ve službě Media Services, je potřeba vytvořit ContentKeyAuthorizationPolicyOption, které zadali následující tři věci:

1. Systému DRM (Widevine)
2. Určení jak klíčů aplikace pro doručování ContentKeyAuthorizationPolicyRestriction je ověřen ve službu doručování licencí (otevřené nebo autorizační token)
3. Šablona licence DRM (Widevine)

Povolit **offline** režim pro licencí Widevine, je nutné nakonfigurovat [šabloně licence Widevine](media-services-widevine-license-template-overview.md). V **policy_overrides** objektu, nastaven **can_persist** vlastnost **true** (výchozí hodnota je false). 

Následující vzorový kód používá rozhraní .NET umožňuje **offline** režimu pro licence na Widevine. Kód je založen na [ pomocí PlayReady nebo Widevine běžného dynamického šifrování s využitím .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) vzorku. 

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

Nejjednodušší způsob, jak vyvíjet aplikace s nativní přehrávač pro zařízení s Androidem je použít [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), přehrávače videa na open source sady SDK. ExoPlayer podporuje funkce aktuálně se nepodporuje pro Android native MediaPlayer rozhraní API, včetně MPEG-DASH a Microsoft Smooth Streaming doručovací protokoly.

ExoPlayer verzi 2.6 nebo novější obsahuje mnoho tříd, které podporují přehrávání offline Widevine DRM. Zejména OfflineLicenseHelper třída poskytující funkce nástroje pro usnadnění použití DefaultDrmSessionManager pro stahování, obnovení a uvolnění offline licencí. Třídy k dispozici ve složce SDK "knihovny/core/src/main/java/com/google/android/exoplayer2/offline /" podporu offline video obsahu stahování.

Následující seznam tříd usnadnění offline režimu v ExoPlayer SDK pro Android:

- Library/Core/src/Main/Java/COM/Google/Android/exoplayer2/DRM/OfflineLicenseHelper.Java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- Library/Core/src/Main/Java/COM/Google/Android/exoplayer2/offline/SegmentDownloader.Java
- Library/Core/src/Main/Java/COM/Google/Android/exoplayer2/offline/DownloaderConstructorHelper.Java 
- Library/Core/src/Main/Java/COM/Google/Android/exoplayer2/offline/Downloader.Java
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

Vývojáři by měly odkazovat [ExoPlayer – Příručka pro vývojáře](https://google.github.io/ExoPlayer/guide.html) a odpovídající [Blog pro vývojáře](https://medium.com/google-exoplayer) během vývoje aplikace. Google nevydala plně zdokumentovaných referenční implementace nebo ukázkový kód pro ExoPlayer aplikace podporu Widevine offline v tuto chvíli proto informace je omezena na blog a příručka pro vývojáře. 

### <a name="working-with-older-android-devices"></a>Práce s starší zařízení s Androidem

Pro některá starší zařízení s Androidem je potřeba nastavit hodnoty pro následující **policy_overrides** vlastnosti (definované v [šabloně licence Widevine](media-services-widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds**, a **license_duration_seconds**. Alternativně je můžete nastavit na hodnotu nula, což znamená, že na dobu neurčitou/nekonečno.  

Hodnoty musí být nastaveny, aby se zabránilo chybu celočíselného přetečení. Podrobnější vysvětlení týkající se problému, najdete v části https://github.com/google/ExoPlayer/issues/3150 a https://github.com/google/ExoPlayer/issues/3112. <br/>Pokud nenastavíte explicitně, hodnoty velmi velké hodnoty **PlaybackDurationRemaining** a **LicenseDurationRemaining** se přiřadí, (například 9223372036854775807, což je maximální počet kladnou hodnotu pro 64bitové celé číslo). V důsledku toho licencování Widevine se zobrazí vypršela platnost a proto se dešifrování neprovede. 

Tento problém se nevyskytuje v Androidu 5.0 Lollipop nebo novějším s Androidem 5.0 je první verze Androidu, která byla navržena pro úplnou podporu ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) a 64bitové platformy, zatímco se systémem Android 4.4 KitKat původně určené pro podporu ARMv7 a 32bitové platformy jako ostatní starší verze Androidu.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Pomocí Xamarin pro vytváření aplikací pro Android přehrávání

Xamarinové vazby sady pro ExoPlayer můžete najít pomocí následujících odkazů:

- [Knihovna vazeb Xamarin Google ExoPlayer knihovny](https://github.com/martijn00/ExoPlayerXamarin)
- [Xamarinové vazby sady pro ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Další informace naleznete v následujících vlákna: [Xamarin vazby](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Aplikace pro Chrome přehrávače pro Android

Počínaje vydáním [Chrome pro Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), trvalou licenci v EME je podporována. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) je nyní podporováno také v Chrome pro Android. To vám pomůže vytvářet aplikace v režimu offline přehrávání v prohlížeči Chrome, pokud koncoví uživatelé měli to umožňuje (nebo vyšší) verzi Chrome. 

Kromě toho Google je vytvořen ukázkový progresivní Web App (PWA) open source a je: 

- [Zdrojový kód](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Verze Google hostované](https://biograf-155113.appspot.com/ttt/episode-2/) (funguje pouze v Chrome v 62 a vyšší na zařízeních s Androidem)

Pokud upgradujete mobilního prohlížeče Chrome v62 (nebo vyšší) na telefon s Androidem a test výše uvedené hostované ukázkovou aplikaci, zobrazí se přehrávání online streamování a offline práci.

Výše uvedené aplikace PWA open source se vytváří v Node.js. Pokud chcete hostovat vlastní verzi na serveru se systémem Ubuntu, mějte na paměti následující běžné došlo k chybě problémy, které může zabránit přehrávání:

1. Problém CORS: Ukázkové video v ukázkové aplikaci je hostován v https://storage.googleapis.com/biograf-video-files/videos/. Google má nastavení CORS pro všechny své ukázky test hostované v intervalu Google Cloud Storage. Jsou poskytovány pomocí hlavičky CORS explicitním zadáním položky CORS: https://biograf-155113.appspot.com (doménou, ve které google hostitelem jejich ukázkové) brání přístupu podle jakýchkoli jiných lokalit. Pokud se pokusíte, zobrazí se následující chyba HTTP: Nepovedlo se načíst https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: žádné záhlaví 'Přístup-Control-Allow-Origin' je k dispozici u požadovaného prostředku. Původní "https://13.85.80.81:8080' není proto povolený přístup. Pokud odpověď neprůhledné slouží vašim potřebám, nastavte režim požadavek na "no-cors" Načíst prostředek s CORS zakázán.
2. Problém certifikátu: Rozšíření EME pro Widevine od Chrome v 58, vyžaduje protokol HTTPS. Proto je nutné pro hostování ukázkové aplikace prostřednictvím protokolu HTTPS s x X509 certifikátu. Certifikát obvykle testů nefunguje kvůli následující požadavky: Musíte získat certifikát splňuje následující požadavky:
    - Chrome a Firefox vyžadují nastavení alternativní název předmětu SAN existovat v certifikátu
    - Musí mít certifikát důvěryhodné certifikační Autority a certifikát podepsaný svým držitelem vývoj nefunguje.
    - Certifikát musí mít CN odpovídající názvu DNS na webovém serveru nebo brány

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="question"></a>Otázka

Jak může poskytovat trvalé licence (offline podporou) pro některé klienty a uživatele a dočasné licence (offline zakázána) pro ostatní uživatele? Budu muset duplicitní obsah a použijte samostatný klíč obsahu?

### <a name="answer"></a>Odpověď
Není nutné duplikovat obsah. Můžete jednoduše použít jednu kopii obsahu a jeden ContentKeyAuthorizationPolicy, ale dvě samostatné ContentKeyAuthorizationPolicyOption společnosti:

1. IContentKeyAuthorizationPolicyOption 1: používá trvalou licenci a ContentKeyAuthorizationPolicyRestriction 1, která obsahuje deklaraci identity jako je například license_type = "Trvalé"
2. IContentKeyAuthorizationPolicyOption 2: používá dočasné licence a ContentKeyAuthorizationPolicyRestriction 2, která obsahuje deklaraci identity jako je například license_type = "Nonpersistent"

Tímto způsobem, když požadavek licence pocházejí z klientské aplikace z licencí požadavku není žádný rozdíl. Pro různé koncové uživatele nebo zařízení, ale služba tokenů zabezpečení by měl mít obchodní logiku pro vydávání různých tokeny JWT obsahující různé deklarace identity (jedna z výše uvedených dvou license_type). Hodnota deklarace identity v tokenu JWT se použije k rozhodnout licence služby pro vydávání typ licence: trvalé nebo dočasné.

To znamená, že musí mít obchodní logiky a klientů a zařízení informace o přidání odpovídající hodnota deklarace do tokenu zabezpečení Token Service (Služba tokenů zabezpečení).

### <a name="question"></a>Otázka

Pro úrovně zabezpečení Widevine, v Googlu [přehled architektury Widevine DRM doc](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) dokumentaci, definuje tři různé úrovně zabezpečení. Ale v [dokumentace ke službě Azure Media Services v šabloně licence Widevine](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), jsou uvedeny pět různé úrovně zabezpečení. Co je relace nebo mapování mezi dvě různé sady úrovně zabezpečení?

### <a name="answer"></a>Odpověď

V Googlu [přehled architektury Widevine DRM](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf), definuje následující úrovně tři zabezpečení:

1.  Úroveň zabezpečení 1: Zpracování obsahu, šifrování a řízení jsou prováděny v rámci důvěryhodné spuštění prostředí (TEE). V některých modelů rychlou implementaci zpracování zabezpečení mohou být provedeny v různých čipy.
2.  Zabezpečení úrovně 2: Provádí šifrování (ale ne videa zpracování) v rámci TEE: dešifrovaný vyrovnávací paměti jsou vráceny do domény aplikace a zpracovává prostřednictvím samostatných grafický hardware nebo software. Na úrovni 2 ale informace o kryptografických je stále zpracovávána pouze v rámci TEE.
3.  Úroveň zabezpečení 3 nemá TEE na zařízení. Může být přijata vhodná opatření k ochraně kryptografických informace a dešifrovaným obsah na hostitelském operačním systému. Na úroveň 3 implementace může také obsahovat kryptografický modul hardwaru, ale pouze, která vylepšuje výkon, zabezpečení není.

Současně, v [dokumentace ke službě Azure Media Services v šabloně licence Widevine](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), vlastnost security_level content_key_specs může mít následující pěti různých hodnot (odolnosti požadavky na klienta pro přehrávání):

1.  Crypto softwarových whitebox je povinný.
2.  Je vyžadována crypto softwaru a dekodér zastaralý.
3.  Materiál klíče a kryptografické operace se musí provádět v rámci TEE zajištěné hardwaru.
4.  Kryptografie a dekódování obsahu se musí provádět v rámci TEE zajištěné hardwaru.
5.  Kryptografických dekódování a všechny zpracování médií (komprimovaným a nekomprimovaným formátem) musí zpracovat v rámci TEE zajištěné hardwaru.

Obě úrovně zabezpečení je definována v Google Widevine. Rozdíl je v jeho úroveň využití: architektura úrovni nebo rozhraní API. Pět zabezpečení úrovně se používají v rozhraní API Widevine. Content_key_specs objekt, který obsahuje security_level je deserializovat a předat službě Widevine globální doručování pomocí Licenční službu Azure Media Services Widevine. Následující tabulka uvádí mapování mezi těmito dvěma sadami úrovní zabezpečení.

| **Úrovně zabezpečení, které jsou definovány v architektuře Widevine** |**Úrovně zabezpečení používané ve Widevine rozhraní API**|
|---|---| 
| **Úroveň zabezpečení 1**: Zpracování obsahu, šifrování a řízení jsou prováděny v rámci důvěryhodné spuštění prostředí (TEE). V některých modelů rychlou implementaci zpracování zabezpečení mohou být provedeny v různých čipy.|**security_level=5**: Kryptografických dekódování a všechny zpracování médií (komprimovaným a nekomprimovaným formátem) musí zpracovat v rámci TEE zajištěné hardwaru.<br/><br/>**security_level=4**: Kryptografie a dekódování obsahu se musí provádět v rámci TEE zajištěné hardwaru.|
**Úroveň zabezpečení 2**: Provádí šifrování (ale ne videa zpracování) v rámci TEE: dešifrovaný vyrovnávací paměti jsou vráceny do domény aplikace a zpracovává prostřednictvím samostatných grafický hardware nebo software. Na úrovni 2 ale informace o kryptografických je stále zpracovávána pouze v rámci TEE.| **security_level=3**: Materiál klíče a kryptografické operace se musí provádět v rámci TEE zajištěné hardwaru. |
| **Úroveň zabezpečení 3**: Nemá TEE na zařízení. Může být přijata vhodná opatření k ochraně kryptografických informace a dešifrovaným obsah na hostitelském operačním systému. Na úroveň 3 implementace může také obsahovat kryptografický modul hardwaru, ale pouze, která vylepšuje výkon, zabezpečení není. | **security_level=2**: Je vyžadována crypto softwaru a dekodér zastaralý.<br/><br/>**security_level=1**: Crypto softwarových whitebox je povinný.|

### <a name="question"></a>Otázka

Proč stahování obsahu trvá tak dlouho?

### <a name="answer"></a>Odpověď

Existují dva způsoby, jak zlepšit rychlost stahování:

1.  Povolte CDN, aby koncoví uživatelé budou pravděpodobně přístupů CDN místo původu/koncový bod pro stahování obsahu. Pokud uživatel narazí na koncový bod streamování, každý HLS segment nebo DASH fragment se dynamicky zabalené a šifrována. I když latence v milisekundách škálování pro každý segment/fragment, když máte hodinu dlouho videa, mohou být velké, způsobí delší dobu stahování celkové latence.
2.  Poskytnout koncovým uživatelům umožňuje stáhnout kvalita videa vrstvy a zvukové stopy místo veškerý obsah. Pro offline režim neexistuje žádný bod ke stažení všechny vrstvy kvality. Existují dva způsoby, jak toho dosáhnout:
    1.  Klient řízené: vybere automaticky aplikace přehrávače nebo uživatel vybere kvalita videa vrstvy a zvukové stopy stáhnout;
    2.  Služba řídí: jeden pomocí dynamické Manifest funkce ve službě Azure Media Services můžete vytvářet filtr (globální), který omezuje stop HLS nebo DASH MPD do vrstvy jedné kvalita videa a vybrali zvukové stopy. Adresa URL pro stahování budou zobrazovat koncovým uživatelům pak bude obsahovat tento filtr.

## <a name="summary"></a>Souhrn

Tento článek byl věnován implementace offline režimu přehrávání pro DASH obsah chráněný pomocí Widevine na zařízeních s Androidem.  Odpovědi také některé běžné dotazy týkající se offline streamování Widevine chráněný obsah.
