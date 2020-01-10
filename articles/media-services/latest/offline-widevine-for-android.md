---
title: Online streamování Widevine pro Android s Azure Media Services V3
description: V tomto tématu se dozvíte, jak nakonfigurovat účet Azure Media Services pro offline streamování chráněného obsahu Widevine.
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
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 17416fce887f5f5b952232e284873e539fd68337
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779905"
---
# <a name="offline-widevine-streaming-for-android"></a>Online streamování Widevine pro Android

Kromě ochrany obsahu pro online streamování, předplatné mediálního obsahu a služby pronájmu nabízí obsah ke stažení, který funguje, když nejste připojení k Internetu. Je možné, že budete muset stáhnout obsah na telefon nebo tablet pro přehrávání v režimu v letadle, když se dolétající z sítě. Další scénáře, ve kterých byste mohli chtít stáhnout obsah:

- Někteří poskytovatelé obsahu můžou zakázat doručování licencí DRM mimo hranici země nebo oblasti. Pokud chce uživatel sledovat obsah během cestování v cizině, je nutné offline stahování.
- V některých zemích nebo oblastech je dostupnost Internetu a/nebo šířka pásma omezená. Uživatelé si můžou stáhnout obsah, abyste si ho mohli prohlédnout dostatečně dostatečně jako v dostatečném rozlišení.

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
- Klonovat https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git.

    Pokud chcete přidat konfigurace Widevine, budete muset změnit kód v části [šifrování pomocí DRM pomocí technologie .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) .  
- Seznamte se se sadou Google ExoPlayer SDK pro Android, což je open source sada pro Video Player, která podporuje offline přehrávání Widevine DRM. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
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
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/DefaultDrmSessionManager. Java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/offline/SegmentDownloader. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/offline/DownloaderConstructorHelper. Java 
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/offline/stahovací. Java
- Library/pomlčka/src/Main/Java/com/Google/Android/exoplayer2/source/pomlčka/offline/DashDownloader. Java 

Vývojáři by měli v průběhu vývoje aplikace odkazovat na [příručku pro vývojáře ExoPlayer](https://google.github.io/ExoPlayer/guide.html) a na odpovídajícím [blogu pro vývojáře](https://medium.com/google-exoplayer) . Google nevydal plně dokumentovaný odkaz na implementaci nebo vzorový kód pro aplikaci ExoPlayer podporující Widevine v režimu offline, takže informace jsou omezené na průvodce a blog pro vývojáře. 

### <a name="working-with-older-android-devices"></a>Práce se staršími zařízeními s Androidem

U některých starších zařízení s Androidem je potřeba nastavit hodnoty pro následující vlastnosti **policy_overrides** (definované v [šabloně licence Widevine](widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds**a **license_duration_seconds**. Případně je můžete nastavit na hodnotu nula, což znamená nekonečné nebo neomezené trvání.  

Hodnoty musí být nastaveny, aby nedocházelo k chybě přetečení celého čísla. Další vysvětlení tohoto problému najdete v tématu https://github.com/google/ExoPlayer/issues/3150 a https://github.com/google/ExoPlayer/issues/3112. <br/>Pokud hodnoty explicitně nenastavíte, budou přiřazeny velmi velké hodnoty pro **PlaybackDurationRemaining** a **LicenseDurationRemaining** (například 9223372036854775807, což je maximální kladná hodnota pro 64 celé číslo). V důsledku toho se zdá, že licence Widevine vypršela, a proto se k dešifrování nestane. 

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

Výše uvedená Open Source aplikace PWA je vytvořená v Node. js. Pokud chcete hostovat svou vlastní verzi na serveru Ubuntu, pamatujte na následující běžné problémy, které můžou bránit přehrávání:

1. Problém CORS: ukázkové video v ukázkové aplikaci je hostované v https://storage.googleapis.com/biograf-video-files/videos/. Google nastavila CORS pro všechny testovací vzorky hostované v sadě Google Cloud Storage. Jsou obsluhovány pomocí hlaviček CORS a explicitně specifikují položku CORS: https://biograf-155113.appspot.com (doména, ve které hostuje vaše ukázka) znemožňuje přístup jakýmkoli jiným webům. Pokud se pokusíte, zobrazí se následující chyba protokolu HTTP: Nepodařilo se načíst https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: není k dispozici hlavička Access-Control-Allow-Origin v požadovaném prostředku. Počátek https:\//13.85.80.81:8080 není proto povolený přístup. Pokud neprůhledná odpověď vyhovuje vašim potřebám, nastavte režim žádosti na No-CORS, aby bylo možné načíst prostředek s zakázáním CORS.
2. Problém s certifikátem: od verze Chrome v 58 EME pro Widevine vyžaduje protokol HTTPS. Proto je třeba hostovat ukázkovou aplikaci přes protokol HTTPS s certifikátem x509. Běžný testovací certifikát nefunguje v důsledku následujících požadavků: potřebujete získat certifikát splňující následující minimální požadavky:
    - Chrome a Firefox vyžadují, aby v certifikátu existovalo nastavení alternativního názvu subjektu sítě SAN.
    - Certifikát musí mít důvěryhodnou certifikační autoritu a vývojový certifikát podepsaný svým držitelem nefunguje.
    - Certifikát musí obsahovat CN, který odpovídá názvu DNS webového serveru nebo brány.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="question"></a>Otázka

Jak mohu doručovat trvalé licence (offline) pro některé klienty/uživatele a netrvalé licence (offline – zakázáno) pro jiné? Musím duplikovat obsah a použít samostatný klíč obsahu?

### <a name="answer"></a>Odpověď
Vzhledem k tomu, že Media Services V3 umožňuje Assetu mít více StreamingLocators. Můžete mít

1.  Jeden ContentKeyPolicy s license_type = "persistent", ContentKeyPolicyRestriction s deklarací "persistent" a jeho StreamingLocator;
2.  Další ContentKeyPolicy s license_type = "nestálo", ContentKeyPolicyRestriction s deklarací "netrvalá" a jeho StreamingLocator.
3.  Obě StreamingLocators mají různé ContentKey.

V závislosti na obchodní logice vlastní služby STS se v tokenu JWT vydávají různé deklarace identity. U tokenu lze získat pouze odpovídající licenci a přehrát lze pouze odpovídající adresu URL.

### <a name="question"></a>Otázka

V případě Widevine úrovní zabezpečení v dokumentaci k dokumentaci k [Widevine architektury DRM](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) společnosti Google, definuje tři různé úrovně zabezpečení. V [Azure Media Services dokumentaci k šabloně licence Widevine](widevine-license-template-overview.md)je ale k disřádku pět různých úrovní zabezpečení. Jaký je vztah nebo mapování mezi dvěma různými sadami úrovní zabezpečení?

### <a name="answer"></a>Odpověď

V [přehledu architektury DRM](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)společnosti Google v Widevine se definuje následující tři úrovně zabezpečení:

1.  Úroveň zabezpečení 1: zpracování, kryptografie a řízení obsahu se provádí v prostředí pro důvěryhodné spouštění (TEE). V některých implementačních modelech může být zpracování zabezpečení provedeno v různých čipy.
2.  Úroveň zabezpečení 2: provádí kryptografii (ale ne zpracování videa) v rámci TEE: dešifrované vyrovnávací paměti jsou vraceny do aplikační domény a zpracovávány prostřednictvím samostatného grafického hardwaru nebo softwaru. Na úrovni 2 se však kryptografické informace stále zpracovávají pouze v rámci TEE.
3.  Úroveň zabezpečení 3 nemá na zařízení TEE. K ochraně kryptografických informací a dešifrovaného obsahu v hostitelském operačním systému můžou být podniknuta vhodná opatření. Implementace úrovně 3 může zahrnovat také hardwarový šifrovací modul, ale vylepšuje výkon, ne zabezpečení.

Ve stejnou dobu v [Azure Media Services dokumentaci k šabloně licence Widevine](widevine-license-template-overview.md)může mít vlastnost security_level content_key_specs následující pět různých hodnot (požadavky na odolnost klienta pro přehrávání):

1.  Je vyžadováno šifrování WhiteBOX založené na softwaru.
2.  Vyžaduje se softwarová kryptografie a zakódováný dekodér.
3.  Klíčové operace a kryptografické operace se musí provádět v rámci zálohovaných TEE hardwaru.
4.  Kryptografické a dekódování obsahu se musí provádět v rámci zálohovaných TEE hardwaru.
5.  Šifrování, dekódování a veškerá manipulace s médii (komprimovaná a nekomprimovaná) musí být zpracovávány v TEE hardwaru.

Obě úrovně zabezpečení jsou definované Google Widevine. Rozdíl je ve své úrovni použití: úroveň architektury nebo úroveň rozhraní API. V rozhraní API Widevine se používá pět úrovní zabezpečení. Objekt content_key_specs, který obsahuje security_level, je rekonstruován a předán službě Widevine Global Delivery Service pomocí služby Azure Media Services Widevine License Service. Následující tabulka ukazuje mapování mezi dvěma sadami úrovní zabezpečení.

| **Úrovně zabezpečení definované v architektuře Widevine** |**Úrovně zabezpečení používané v rozhraní Widevine API**|
|---|---| 
| **Úroveň zabezpečení 1**: zpracování, kryptografie a řízení obsahu se provádí v prostředí pro důvěryhodné spouštění (TEE). V některých implementačních modelech může být zpracování zabezpečení provedeno v různých čipy.|**security_level = 5**: šifrování, dekódování a veškerá manipulace s médii (komprimovaná a nekomprimovaná) musí být zpracovávány v Tee hardwaru.<br/><br/>**security_level = 4**: šifrování a dekódování obsahu se musí provádět v rámci Tee zálohovaného hardwaru.|
**Úroveň zabezpečení 2**: provádí kryptografii (ale ne zpracování videa) v rámci Tee: dešifrované vyrovnávací paměti jsou vraceny do aplikační domény a zpracovávány prostřednictvím samostatného grafického hardwaru nebo softwaru. Na úrovni 2 se však kryptografické informace stále zpracovávají pouze v rámci TEE.| **security_level = 3**: klíč materiálu a kryptografické operace se musí provádět v rámci Tee zálohovaného hardwaru. |
| **Úroveň zabezpečení 3**: nemá na zařízení Tee. K ochraně kryptografických informací a dešifrovaného obsahu v hostitelském operačním systému můžou být podniknuta vhodná opatření. Implementace úrovně 3 může zahrnovat také hardwarový šifrovací modul, ale vylepšuje výkon, ne zabezpečení. | **security_level = 2**: je vyžadováno šifrování softwaru a vypsaný dekodér.<br/><br/>**security_level = 1**: je vyžadováno šifrování WhiteBOX založené na softwaru.|

### <a name="question"></a>Otázka

Proč stahování obsahu trvá tak dlouho?

### <a name="answer"></a>Odpověď

Existují dva způsoby, jak zvýšit rychlost stahování:

1.  Povolte CDN, aby koncoví uživatelé měli více místa na síti CDN místo počátečního/streamování pro stažení obsahu. Pokud uživatel zaznamená datový koncový bod streamování, každý segment HLS nebo odspojovníkový fragment se dynamicky zabalí a zašifruje. I když je tato latence v milisekundách pro každý segment nebo fragment, pokud máte dlouhé video, může být kumulovaná latence velká, což by mohlo trvat delší dobu stahování.
2.  Poskytněte koncovým uživatelům možnost selektivně stahovat vrstvy kvality videa a zvukové stopy místo veškerého obsahu. V režimu offline není žádný bod ke stažení všech vrstev kvality. Toho můžete dosáhnout dvěma způsoby:
    1.  Řízená klientem: buď hráč aplikace Player automaticky vybere, nebo uživatel vybere vrstvu kvality videa a zvukové stopy ke stažení;
    2.  Řízená služba: jedna může použít funkci dynamického manifestu v Azure Media Services k vytvoření (globálního) filtru, který omezí HLSý seznam testů nebo POMLČKy na jednu vrstvu kvality videa a vybrané zvukové stopy. Pak bude tento filtr zahrnovat adresa URL pro stažení prezentovaná koncovým uživatelům.

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="summary"></a>Souhrn

Tento článek popisuje, jak implementovat přehrávání offline režimu pro ČÁRKOVANý obsah chráněný Widevine na zařízeních s Androidem.  Také si odpověděli na některé běžné otázky týkající se offline streamování chráněného obsahu Widevine.
