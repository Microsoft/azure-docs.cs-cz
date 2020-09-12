---
title: Online streamování FairPlay pro iOS s Azure Media Services V3
description: Toto téma poskytuje přehled a ukazuje, jak použít Azure Media Services k dynamickému šifrování obsahu HTTP Live Streaming (HLS) pomocí nástroje Apple FairPlay v režimu offline.
services: media-services
keywords: HLS, DRM, FairPlay streaming (FPS), offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: c46fe5a4199e73cd22aeef4df62daeed4b58db06
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291309"
---
# <a name="offline-fairplay-streaming-for-ios-with-media-services-v3"></a>Online streamování FairPlay pro iOS s Media Services V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

 Azure Media Services poskytuje sadu dobře navrhovaných [služeb ochrany obsahu](https://azure.microsoft.com/services/media-services/content-protection/) , které zahrnují:

- Microsoft PlayReady
- Google Widevine
    
    Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.
- Apple FairPlay
- Šifrování AES-128

Správa digitálních práv (DRM) šifrování obsahu (DRM)/Advanced Encryption Standard (DRM) se provádí dynamicky na vyžádání různých protokolů streamování. K dispozici jsou také služby doručování licencí DRM/dešifrovacího klíče AES Media Services.

Kromě ochrany obsahu pro online streamování v různých protokolech streamování je také často vyžádaná funkce offline režim pro chráněný obsah. Podpora offline režimu je nutná pro následující scénáře:

* Přehrávání, pokud není k dispozici připojení k Internetu, například během cestování.
* Někteří poskytovatelé obsahu můžou zakázat doručování licencí DRM za hranice země nebo oblasti. Pokud uživatelé chtějí sledovat obsah během cestování mimo zemi nebo oblast, je stažení offline potřeba.
* V některých zemích a oblastech je dostupnost Internetu a/nebo šířka pásma stále omezená. Uživatelé si můžou chtít nejdřív stáhnout, aby mohli sledovat obsah v rámci řešení, které je dostatečně vysoké, aby se mohlo zobrazit uspokojivý zážitek. V takovém případě problém obvykle není dostupnost sítě, ale omezená šířka pásma sítě. Poskytovatelé OTT (OVP) s podporou převzetí služeb při prvním přístupu () s žádostí o podporu offline režimu.

Tento článek popisuje podporu FairPlay streamování (FPS) v režimu offline, která cílí na zařízení se systémem iOS 10 nebo novějším. Tato funkce není podporovaná pro jiné platformy Apple, jako je watchOS, tvOS nebo Safari v macOS.

> [!NOTE]
> Offline DRM se účtuje jenom při vytváření jediné žádosti o licenci při stažení obsahu. Neúčtují se žádné chyby.

## <a name="prerequisites"></a>Požadavky

Před implementací offline DRM pro FairPlay na zařízení s iOS 10 +:

* Kontrola online ochrany obsahu pro FairPlay: 

    - [Konfigurace a licenční požadavky pro Apple FairPlay](fairplay-license-overview.md)
    - [Použití ochrany DRM s dynamickým šifrováním a služby doručování licencí](protect-with-drm.md)
    - Ukázka .NET, která zahrnuje konfiguraci streamování online snímků: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Získejte sadu FPS SDK ze sítě Apple Developer. Sada FPS SDK obsahuje dvě komponenty:

    - Sada SDK serveru pro FPS, která obsahuje modul zabezpečení klíčů (KSM), ukázky klienta, specifikace a sadu testovacích vektorů.
    - Balíček nasazení FPS, který obsahuje specifikaci funkce D, spolu s pokyny, jak vygenerovat certifikát snímků pro sbalení, soukromý klíč specifický pro zákazníka a tajný klíč aplikace. Apple vydá balíček nasazení FPS jenom pro licencované poskytovatele obsahu.
* Klonovat https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git . 

    Pokud chcete přidat konfigurace FairPlay, budete muset změnit kód v části [šifrování pomocí DRM pomocí technologie .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) .  

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurace ochrany obsahu v Azure Media Services

V metodě [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) postupujte takto:

Odkomentujte kód, který konfiguruje možnost zásad FairPlay:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Také Odkomentujte kód, který přidá CBCS ContentKeyPolicyOption do seznamu ContentKeyPolicyOptions

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Povolit offline režim

Pokud chcete povolit offline režim, vytvořte si vlastní StreamingPolicy a při vytváření StreamingLocator v [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L561)použijte jeho název.
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true // This enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true // Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        // Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }
}

```

Teď je váš účet Media Services nakonfigurovaný tak, aby poskytoval offline licence FairPlay.

## <a name="sample-ios-player"></a>Ukázkový přehrávač pro iOS

Podpora režimu FPS v režimu offline je k dispozici pouze v iOS 10 a novějších. Sada SDK serveru FPS (verze 3,0 nebo novější) obsahuje dokument a ukázku pro offline režim FPS. Konkrétně sada SDK serveru FPS (verze 3,0 nebo novější) obsahuje následující dvě položky týkající se offline režimu:

* Dokument: "přehrávání offline pomocí FairPlay streaming a HTTP Live Streaming." Apple, 14. září 2016. V sadě FPS Server SDK verze 4,0 je tento dokument sloučen do hlavního dokumentu FPS.
* Vzorový kód: HLSCatalog Sample (součást sady SDK pro sadu snímků Apple) pro offline režim přechodu v sadě SDK serveru \FairPlay streaming verze 3.1 \ Development\Client\ HLSCatalog_With_FPS \HLSCatalog\. V ukázkové aplikaci HLSCatalog se pro implementaci funkcí v režimu offline používá následující soubory kódu:

    - Soubor kódu AssetPersistenceManager. SWIFT: AssetPersistenceManager je hlavní třídou v této ukázce, která ukazuje, jak:

        - Spravujte stahování HLS datových proudů, jako jsou rozhraní API používaná ke spuštění a zrušení souborů ke stažení a odstranění existujících prostředků mimo zařízení.
        - Sledujte průběh stahování.
    - Soubory kódu AssetListTableViewController. SWIFT a AssetListTableViewCell. SWIFT: AssetListTableViewController je hlavní rozhraní této ukázky. Poskytuje seznam assetů, které může ukázka použít k přehrání, stažení, odstranění nebo zrušení stahování. 

Tyto kroky ukazují, jak nastavit běžící přehrávač pro iOS. Za předpokladu, že začínáte s HLSCatalog ukázkou v sadě FPS Server SDK verze 4.0.1, proveďte následující změny kódu:

V HLSCatalog\Shared\Managers\ContentKeyDelegate.swift Implementujte metodu `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` pomocí následujícího kódu. Nechejte "drmUr" proměnnou přiřazenou k adrese URL HLS.

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

V HLSCatalog\Shared\Managers\ContentKeyDelegate.swift Implementujte metodu `requestApplicationCertificate()` . Tato implementace závisí na tom, jestli jste do zařízení vložili certifikát (jenom veřejný klíč), nebo hostovat certifikát na webu. Následující implementace používá certifikát hostované aplikace, který je použit v ukázkách testu. Nechte "certUrl" proměnnou, která obsahuje adresu URL certifikátu aplikace.

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

Pro finální integrovaný test je k dispozici adresa URL videa i adresa URL certifikátu aplikace v oddílu "Integrovaný test".

Do HLSCatalog\Shared\Resources\Streams.plist přidejte adresu URL testovacího videa. Pro ID klíče obsahu použijte adresu URL pro získání licence FairPlay s protokolem SKD jako jedinečnou hodnotu.

![Datové proudy aplikace pro iOS v FairPlay](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Použijte vlastní adresu URL testovacího videa, adresu URL pro získání licence FairPlay a adresu URL certifikátu aplikace, pokud je máte nastavené. Nebo můžete pokračovat k další části, která obsahuje ukázky testů.

## <a name="integrated-test"></a>Integrovaný test

Tři ukázkové testy v Media Services pokrývají následující tři scénáře:

* Chráněná přes FPS s videem, zvukem a alternativním zvukovou stopou
* Chráněná za sekundu, s videem a zvukem, ale bez alternativní zvukové stopy
* Chráněná přes FPS jenom s videem a bez zvuku

Tyto ukázky najdete na [tomto ukázkovém webu](https://aka.ms/poc#22)s odpovídajícím certifikátem aplikace hostovaným ve webové aplikaci Azure.
V případě ukázky verze 3 nebo 4 sady SDK serveru FPS je v případě, že hlavní seznam testů obsahuje alternativní zvuk, v režimu offline, který přehrává jenom zvuk. Proto je potřeba obložení alternativního zvuku. Jinými slovy, druhý a třetí vzorek uvedený dříve pracuje v režimu online a offline. Ukázka uvedená jako první hraje zvuk pouze v režimu offline, zatímco online streamování funguje správně.

## <a name="faq"></a>Nejčastější dotazy

Přečtěte si [Nejčastější dotazy týkající se řešení potíží](frequently-asked-questions.md#why-does-only-audio-play-but-not-video-during-offline-mode).

## <a name="next-steps"></a>Další kroky

Podívejte se, jak zajistit [ochranu pomocí AES-128](protect-with-aes128.md).
