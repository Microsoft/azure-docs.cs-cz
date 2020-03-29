---
title: Offline streamování FairPlay pro iOS pomocí Mediálních služeb Azure v3
description: Toto téma poskytuje přehled a ukazuje, jak pomocí Mediálních služeb Azure dynamicky šifrovat obsah http živého streamování (HLS) pomocí Apple FairPlay v offline režimu.
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), Offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 70256046089a59df1de79b78124c5d60fde77080
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705934"
---
# <a name="offline-fairplay-streaming-for-ios-with-media-services-v3"></a>Offline streamování FairPlay pro iOS s mediálními službami v3

 Azure Media Services poskytuje sadu dobře navržených [služeb ochrany obsahu,](https://azure.microsoft.com/services/media-services/content-protection/) které pokrývají:

- Microsoft PlayReady
- Google Widevine
    
    Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.
- Apple FairPlay
- Šifrování AES-128

Šifrování obsahu pro správu digitálních práv (DRM) /Advanced Encryption Standard (AES) se provádí dynamicky na vyžádání pro různé protokoly streamování. Služby pro dešifrování klíčů DRM/AES jsou také poskytovány službou Media Services.

Kromě ochrany obsahu pro online streamování přes různé streamovací protokoly je často žádanou funkcí také režim offline pro chráněný obsah. Podpora offline režimu je potřebná pro následující scénáře:

* Přehrávání, pokud není k dispozici připojení k internetu, například během cesty.
* Někteří poskytovatelé obsahu mohou zakázat doručování licencí DRM za hranicemi země nebo oblasti. Pokud uživatelé chtějí sledovat obsah při cestování mimo zemi nebo oblast, je potřeba stáhnout offline.
* V některých zemích nebo oblastech je dostupnost internetu a/nebo šířka pásma stále omezená. Uživatelé se mohou nejprve stáhnout, aby mohli sledovat obsah v rozlišení, které je dostatečně vysoké pro uspokojivý zážitek ze sledování. V tomto případě problém obvykle není dostupnost sítě, ale omezená šířka pásma sítě. Poskytovatelé over-the-top (OTT)/online video platformy (OVP) požadují podporu offline režimu.

Tento článek popisuje podporu offline režimu FairPlay Streaming (FPS), která se zaměřuje na zařízení se systémem iOS 10 nebo novějším. Tato funkce není podporována pro jiné platformy Apple, jako jsou watchOS, tvOS nebo Safari v macOS.

> [!NOTE]
> Offline DRM se účtuje pouze za vytvoření jedné žádosti o licenci při stahování obsahu. Případné chyby se neúčtují.

## <a name="prerequisites"></a>Požadavky

Před implementací offline DRM pro FairPlay na zařízení se systémem iOS 10+:

* Projděte si online ochranu obsahu pro FairPlay: 

    - [Konfigurace a licenční požadavky pro Apple FairPlay](fairplay-license-overview.md)
    - [Použití ochrany DRM s dynamickým šifrováním a služby doručování licencí](protect-with-drm.md)
    - Ukázka rozhraní .NET, která zahrnuje konfiguraci online streamování FPS: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Získejte sadu FPS SDK ze sítě Apple Developer Network. Sada FPS SDK obsahuje dvě složky:

    - Sada SDK serveru FPS, která obsahuje modul Zabezpečení klíčů (KSM), ukázky klientů, specifikaci a sadu testovacích vektorů.
    - Sada pro nasazení FPS, která obsahuje specifikaci funkce D, spolu s pokyny, jak generovat certifikát FPS, soukromý klíč specifický pro zákazníka a tajný klíč aplikace. Společnost Apple vydává balíček pro nasazení FPS pouze poskytovatelům licencovaného obsahu.
* Klon https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git. 

    Chcete-li přidat konfigurace FairPlay, budete muset upravit kód v [programu Šifrovat pomocí DRM pomocí rozhraní .NET.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM)  

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurace ochrany obsahu ve službě Azure Media Services

V metodě [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) postupujte takto:

Odkomentujte kód, který konfiguruje možnost zásad FairPlay:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Odkomentujte také kód, který přidá cbcs contentkeypolicyoption do seznamu ContentKeyPolicyOptions

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Povolení režimu offline

Chcete-li povolit režim offline, vytvořte vlastní Položku StreamingPolicy a při vytváření služby StreamingLocator v [souboru CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563)použijte jeho název.
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true  //this enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true //Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        //Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }

```

Nyní je váš účet Media Services nakonfigurován tak, aby doručoval offline licence FairPlay.

## <a name="sample-ios-player"></a>Ukázka přehrávače iOS

Podpora offline režimu FPS je k dispozici pouze v systému iOS 10 a novějším. Sada SDK serveru FPS (verze 3.0 nebo novější) obsahuje dokument a ukázku pro offline režim FPS. Konkrétně sada FPS Server SDK (verze 3.0 nebo novější) obsahuje následující dvě položky související s režimem offline:

* Dokument: "Přehrávání offline s FairPlay Streaming a HTTP Live Streaming." Apple, září 14, 2016. V sada 20.0 je sada SDK serveru SDK verze 4.0 sloučena do hlavního dokumentu FPS.
* Ukázkový kód: Ukázka HLSCatalog (součást sady FPS Server SDK společnosti Apple) pro offline režim FPS v souboru \FairPlay Streaming Server SDK verze 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. V ukázkové aplikaci HLSCatalog se k implementaci funkcí offline režimu používají následující soubory kódu:

    - AssetPersistenceManager.swift soubor kódu: AssetPersistenceManager je hlavní třída v této ukázce, která ukazuje, jak:

        - Spravujte stahování datových proudů HLS, jako jsou rozhraní API používaná ke spuštění a zrušení stahování a odstranění existujících datových zdrojů mimo zařízení.
        - Sledujte průběh stahování.
    - AssetListTableViewController.swift a AssetListTableViewCell.swift kód soubory: AssetListTableViewController je hlavní rozhraní této ukázky. Obsahuje seznam datových zdrojů, které ukázka může použít k přehrání, stahování, odstraňování nebo rušení stahování. 

Tyto kroky ukazují, jak nastavit spuštěný přehrávač iOS. Za předpokladu, že začnete z ukázky HLSCatalog v sada CHOd 4.0.1 sady FPS Server SDK verze 4.0.1, proveďte následující změny kódu:

V souboru HLSCatalog\Shared\Managers\ContentKeyDelegate.swift `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` implementujte metodu pomocí následujícího kódu. Nechť "drmUr" je proměnná přiřazená k adrese URL HLS.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

V souboru HLSCatalog\Shared\Managers\ContentKeyDelegate.swift `requestApplicationCertificate()`implementujte metodu . Tato implementace závisí na tom, zda vložíte certifikát (pouze veřejný klíč) se zařízením nebo hostujete certifikát na webu. Následující implementace používá certifikát hostované aplikace použitý v testovacích vzorcích. Nechť "certUrl" je proměnná, která obsahuje adresu URL certifikátu aplikace.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

Pro konečný integrovaný test jsou adresa URL videa i adresa URL certifikátu aplikace uvedena v části "Integrovaný test".

V seznamu HLSCatalog\Shared\Resources\Streams.plist přidejte adresu URL testovacího videa. Pro ID klíče obsahu použijte adresu URL pro získání licence FairPlay s protokolem skd jako jedinečnou hodnotou.

![Offline streamy aplikací FairPlay pro iOS](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Použijte vlastní adresu URL testovacího videa, adresu URL pro získání licence FairPlay a adresu URL certifikátu aplikace, pokud je máte nastavené. Nebo můžete pokračovat do další části, která obsahuje testovací vzorky.

## <a name="integrated-test"></a>Integrovaný test

Tři testovací ukázky ve službě Media Services pokrývají následující tři scénáře:

* Chráněno fps, s video, zvukem a alternativní zvukovou stopou
* Fps chráněn, s videem a zvukem, ale bez alternativní zvukové stopy
* FPS chráněno, pouze s videem a bez zvuku

Tyto ukázky najdete na [tomto ukázkovém webu](https://aka.ms/poc#22)s odpovídajícím certifikátem aplikace hostovaným ve webové aplikaci Azure.
S ukázkou sady FPS Server SDK verze 3 nebo verze 4, pokud hlavní seznam stop obsahuje alternativní zvuk, v režimu offline přehrává pouze zvuk. Proto je třeba odstranit alternativní zvuk. Jinými slovy, druhý a třetí ukázky uvedené dříve pracovat v režimu online a offline. Uvedená ukázka nejprve přehrává zvuk pouze v režimu offline, zatímco online streamování funguje správně.

## <a name="faq"></a>Nejčastější dotazy

Následující nejčastější dotazy poskytují pomoc při řešení potíží:

- **Proč se v režimu offline přehrává pouze zvuk, ale ne video?** Toto chování se zdá být záměrné ukázkové aplikace. Pokud je v režimu offline k dispozici alternativní zvuková stopa (což je případ HLS), iOS 10 i iOS 11 jsou ve výchozím nastavení na alternativní zvukovou stopu. Chcete-li kompenzovat toto chování pro režim offline FPS, odeberte alternativní zvukovou stopu z datového proudu. Chcete-li to provést ve službě Media Services, přidejte filtr dynamického manifestu "audio-only=false". Jinými slovy, adresa URL HLS končí .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Proč se po přidání zvuku=false stále přehrává zvuk pouze bez videa v režimu offline?** V závislosti na návrhu klíče mezipaměti sítě pro doručování obsahu (CDN) může být obsah uložen do mezipaměti. Vyčistěte mezipaměť.
- **Je režim offline FPS podporován také v systému iOS 11 kromě iOS 10?** Ano. Režim offline FPS je podporován pro iOS 10 a iOS 11.
- **Proč nemohu najít dokument "Offline přehrávání s FairPlay Streaming a HTTP Live Streaming" v FPS Server SDK?** Od doby, kdy byla sada FPS Server SDK verze 4, byl tento dokument sloučen do průvodce programováním streamování hry FairPlay.
- **Co je stahovaná/offline struktura souborů na zařízeních se systémem iOS?** Struktura stažených souborů na iOS zařízení vypadá jako na následujícím snímku obrazovky. Složka `_keys` ukládá stažené licence FPS s jedním souborem úložiště pro každého hostitele licenční služby. Složka `.movpkg` ukládá zvukový obsah a videoobsah. První složka s názvem, který končí pomlčkou následovanou číselnou, obsahuje video obsah. Číselná hodnota je PeakBandwidth interpretací videa. Druhá složka s názvem, který končí pomlčkou následovanou 0, obsahuje zvukový obsah. Třetí složka s názvem "Data" obsahuje hlavní seznam skladeb obsahu FPS. Nakonec soubor boot.xml poskytuje úplný `.movpkg` popis obsahu složky. 

![Offline ukázková struktura souborů aplikace FairPlay iOS](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Ukázkový soubor boot.xml:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="next-steps"></a>Další kroky

Podívejte se, jak zajistit [ochranu pomocí AES-128](protect-with-aes128.md).
