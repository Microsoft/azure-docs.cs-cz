---
title: "Chránit obsah s offline Apple FairPlay - Azure HLS | Microsoft Docs"
description: "Toto téma poskytuje přehled a ukazuje, jak pomocí Azure Media Services dynamicky šifrovat obsah HTTP Live Streaming (HLS) s Apple FairPlay v offline režimu."
services: media-services
keywords: "Offline, iOS FairPlay streamování (FPS), HLS, DRM, 10"
documentationcenter: 
author: willzhan
manager: steveng
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: dc38772097dddb7c7135d55598373d7ab544f9ea
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2018
---
# <a name="offline-fairplay-streaming-for-ios"></a>Offline FairPlay streamování pro iOS 
 Azure Media Services poskytuje sadu dobře navrženou [obsahu služby ochrany](https://azure.microsoft.com/services/media-services/content-protection/) této stránky:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- Šifrování AES-128

Správy digitálních práv (DRM) / Advanced Encryption (Standard AES) šifrování obsahu se provádí dynamicky na základě žádosti o různých protokolů streamování. DRM licence nebo AES dešifrování doručení klíče služby k dispozici jsou taky pomocí služby Media Services.

Kromě ochraně obsahu online vysílání datového proudu různých protokolů streamování, je funkce často požadovaný taky offline režim pro chráněný obsah. Podpora offline režimu, je potřeba pro následující scénáře:

* Přehrávání při připojení k Internetu není k dispozici, například během cesta.
* Někteří poskytovatelé obsahu může zakáže doručování licencí DRM mimo hranice této země. Pokud si chcete přehrát obsahu při cestování mimo zemi, aby uživatelé, je potřeba offline stahování.
* V některých zemích je stále omezená dostupnost internet nebo šířky pásma. Uživatelé mohou stáhnout nejprve mohli sledovat obsah v řešení, která je dostatečně vysoký, uspokojivé zobrazení prostředí. V takovém případě problém obvykle není dostupnost sítě, ale omezena šířka pásma sítě. Přes the horní (OTT) / zprostředkovatelé online video platformy (OVP) žádosti o podporu offline režimu.

Tento článek popisuje podporu offline režimu streamování FairPlay (FPS), zacílený zařízení se systémem iOS, 10 nebo novější. Tato funkce není podporována pro jiné Apple platformy, jako je například watchOS, tvOS nebo Safari v systému macOS.

## <a name="preliminary-steps"></a>Předběžné kroky
Před implementací offline DRM pro FairPlay na zařízení s iOS 10 +:

* Seznamte se s online ochrana obsahu pro FairPlay. Další informace najdete v následujících článcích a ukázky:

    - [Apple FairPlay streamování pro Azure Media Services je obecně k dispozici](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [Chránit váš obsah s Apple FairPlay nebo Microsoft PlayReady HLS](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
    - [Ukázka pro streamování online snímků za Sekundu](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* Získáte FPS SDK ze sítě vývojáře Apple. Sada SDK FPS obsahuje dvě součásti:

    - Server SDK FPS, která obsahuje klíč zabezpečení modulu (KSM), klient ukázky, specifikaci a sadu vektory testu.
    - Nasazení Pack FPS, která obsahuje specifikaci funkce D, společně s pokyny, jak vygenerovat certifikát FPS, zákaznické privátní klíč a tajný klíč aplikace. Apple vydává Pack nasazení FPS jenom na licencovanou poskytovatelů obsahu.

## <a name="configuration-in-media-services"></a>Konfigurace služby Media Services
Pro konfiguraci FPS offline režimu prostřednictvím [sady Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices)pomocí sady Media Services .NET SDK verze 4.0.0.4 nebo novější, který poskytuje rozhraní API potřebné ke konfiguraci offline režimu snímků za Sekundu.
Musíte taky kód funkční konfigurace ochrany obsahu FPS online režimu. Po obdržení kód nakonfigurovat obsahu ochranu FPS online režimu, je nutné pouze následující dvě změny.

## <a name="code-change-in-the-fairplay-configuration"></a>Změna kódu v konfiguraci FairPlay
První změna je definovat "Povolit offline režim" Boolean, názvem objDRMSettings.EnableOfflineMode, který má hodnotu true, pokud ji povolí offline scénář DRM. V závislosti na tento ukazatel proveďte následující změny konfigurace FairPlay:

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>Změňte kód v konfigurace zásad doručení assetu
Druhý změnou je přidejte do slovníku < AssetDeliveryPolicyConfigurationKey, řetězec > třetí klíč.
Přidejte AssetDeliveryPolicyConfigurationKey, jak je vidět tady:
 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

Po provedení tohoto kroku obsahuje řetězec < Dictionary_AssetDeliveryPolicyConfigurationKey > v zásady doručení assetu FPS následující tři položky:

* AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl nebo AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, v závislosti na faktorech, jako je například server FPS KSM nebo klíčů používá a jestli znovu použít stejné doručení assetu zásady napříč více prostředků
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Váš účet Media Services je nyní nakonfigurováno pro poskytování offline licence FairPlay.

## <a name="sample-ios-player"></a>Ukázka iOS Player
FPS offline režimu podpora je k dispozici jenom v iOS 10 a novější. Server SDK FPS (verze 3.0 nebo vyšší) obsahuje dokument a ukázkové pro offline režim snímků za Sekundu. Konkrétně FPS Server SDK (verze 3.0 nebo vyšší) obsahuje následující dvě položky související s offline režim:

* Dokument: "Offline přehrávání s FairPlay Streaming a HTTP živé streamování." Společnosti Apple, 14 září 2016. V FPS Server SDK verze 4.0 Tento dokument sloučena do hlavní FPS dokumentu.
* Ukázkový kód: Ukázka HLSCatalog pro offline režim snímků za Sekundu v 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\ \FairPlay streamování Server SDK verze. V HLSCatalog ukázková aplikace se používají následující soubory kódu implementovat funkce offline režim:

    - Souboru kódu AssetPersistenceManager.swift: AssetPersistenceManager je hlavní třídy v této ukázce, které ukazuje, jak:

        - Spravovat stahování datové proudy HLS, jako je rozhraní API slouží ke spuštění a zrušit stahování a odstranit existující prostředky vypnout zařízení.
        - Monitorování průběhu stahování.
    - Soubory kódu AssetListTableViewController.swift a AssetListTableViewCell.swift: AssetListTableViewController je rozhraní pro hlavní této ukázky. Poskytuje seznam prostředků, které ukázky můžete použít k přehrávání stáhnout, odstranit nebo zrušit stahování. 

Tyto kroky ukazují, jak nastavit přehrávač spuštěný iOS. Za předpokladu, že spustíte z ukázkové HLSCatalog v FPS Server SDK verze 4.0.1, proveďte následující změny kódu:

V HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implementovat metodu `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` pomocí následující kód. Nechť "drmUr" proměnné přiřazené k adrese URL HLS.

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

V HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implementovat metodu `requestApplicationCertificate()`. Tato implementace závisí na tom, jestli vložení certifikátu (jenom veřejný klíč) pomocí zařízení nebo hostitele certifikátu na webu. Následující implementace používá certifikát hostované aplikace použitý v testovací ukázky. Nechť "certUrl" proměnné, která obsahuje adresu URL certifikátu aplikace.

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

Pro konečné testování integrované adresa URL videa a adresa URL certifikátu aplikace jsou uvedeny v části "Integrované Test."

HLSCatalog\Shared\Resources\Streams.plist přidejte vaše testovací video adresu URL. Pro obsah klíče ID, použijte adresu URL získání licence FairPlay s protokolem skd jako jedinečných hodnot.

![Offline FairPlay iOS aplikace datové proudy](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Použijte vlastní testovací adresu URL videa, adresa URL pro získání licence FairPlay a adresa URL certifikátu aplikace, pokud jste je nastavit. Nebo můžete pokračovat na další oddíl obsahuje ukázky testu.

## <a name="integrated-test"></a>Integrované testu
Tři testovací ukázky ve službě Media Services zahrnují následující tři scénáře:

* Chráněný, video, zvuk a alternativní zvuk sledovat snímků za Sekundu
* Chráněný, videa a audia, ale žádné alternativní zvuk sledovat snímků za Sekundu
* Chránit pomocí pouze video a žádné zvuk snímků za Sekundu

Můžete najít tyto ukázky v [tento ukázkový web](http://aka.ms/poc#22), s odpovídající certifikátu aplikace hostované ve webové aplikace Azure.
Buď verze 3 nebo verze 4 ukázkové sady SDK serveru FPS Pokud hlavní seznam stop obsahuje alternativní zvuk během offline režimu se přehrávání zvuku pouze. Proto je potřeba pruhu alternativní zvukovém souboru. Jinými slovy druhý a třetí ukázky uvedené dříve fungovat v režimu online a offline. Ukázka uvedené nejprve přehrávání zvuku pouze během offline režimu, při online streamování funguje správně.

## <a name="faq"></a>Nejčastější dotazy
Následující nejčastější dotazy pomoc při řešení problémů:

- **Proč pouze zvuk hrát ale není video během offline režimu?** Toto chování se zdá být návrh ukázkovou aplikaci. Pokud alternativní zvukovou stopu je přítomná (což je případ HLS) během offline režimu, iOS 10 a výchozí nastavení iOS 11 alternativní zvuk sledovat. Toto chování kompenzovat FPS offline režimu, odeberte alternativní zvuk sledovat z datového proudu. To uděláte na Media Services, přidejte dynamické manifestu filtr "pouze = false." Jinými slovy adresu URL pro HLS končí .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Proč ho stále přehrajte zvuk pouze bez video během offline režimu po přidání pouze = false?** V závislosti na doručování obsahu (CDN) mezipaměti klíče návrh sítě může být obsah do mezipaměti. Vyprázdnění mezipaměti.
- **Je FPS podporuje taky offline režim v systému iOS 11 kromě iOS 10?** Ano. FPS offline režimu je podporováno pro iOS 10 a iOS 11.
- **Proč nelze najít v dokumentu "Do offline režimu přehrávání s FairPlay Streaming a HTTP Live Streaming" v sadě SDK FPS serveru?** Tento dokument byl od FPS Server SDK verze 4, sloučí "FairPlay Streaming programování Guide."
- **Co poslední parametr stanoven v následující rozhraní API pro offline režim FPS?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Dokumentaci pro toto rozhraní API najdete v tématu [FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration metoda](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). Parametr představuje dobu trvání offline pronájem, s hodinu v podobě jednotka.
- **Co je strukturu stáhnout offline souborů na zařízeních s iOS?** Struktura stažený soubor na zařízení s iOS vypadá jako na následujícím snímku obrazovky. `_keys` Složky úložiště staženy FPS licence souborem jedno úložiště pro každého hostitele licence služby. `.movpkg` Složce jsou uloženy audio a video obsahu. První složka s názvem, který končí pomlčkou následuje jednu číslici obsahuje obsahu videa. Číselná hodnota je PeakBandwidth video interpretací. Druhá složka s názvem, který končí pomlčkou následuje 0 zvuk obsahem. Třetí složku s názvem "Data" obsahuje hlavní seznam stop FPS obsahu. Nakonec boot.xml poskytuje úplný popis `.movpkg` obsah složky. 

![Offline FairPlay iOS ukázka struktury souboru aplikace](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

Ukázkový soubor boot.xml:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
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

## <a name="summary"></a>Souhrn
Tento dokument obsahuje následující kroky a informace, které můžete použít k implementaci FPS offline režim:

* Konfigurace ochrany obsahu Media Services prostřednictvím Media Services .NET API nakonfiguruje dynamického šifrování FairPlay a doručování licence FairPlay ve službě Media Services.
* IOS player na základě vzorku ze sady SDK serveru FPS nastaví player s iOS, které můžete přehrát FPS obsahu, buď v režimu online streamování nebo v režimu offline.
* Ukázka FPS videa se používá k testování režimu offline a online vysílání datového proudu.
* Časté otázky odpovědi na otázky o FPS offline režimu.
