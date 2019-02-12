---
title: Ochrana HLS obsahu s offline Apple FairPlay – Azure | Dokumentace Microsoftu
description: Toto téma poskytuje přehled a ukazuje, jak pomocí Azure Media Services můžete dynamicky šifrovat obsah HTTP Live Streaming (HLS) technologií Apple FairPlay v offline režimu.
services: media-services
keywords: HLS, technologie DRM, FairPlay Streaming (FPS), Offline, Iosu 10
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
ms.date: 02/10/2019
ms.author: willzhan, dwgeo
ms.openlocfilehash: 710ec72e9867ad180afcae8273a093f48933112a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991493"
---
# <a name="offline-fairplay-streaming-for-ios"></a>Offline FairPlay Streaming pro iOS 
 Azure Media Services nabízí sadu dobře navržené [obsahu služby ochrany](https://azure.microsoft.com/services/media-services/content-protection/) této cover:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- Šifrování AES-128

Správa digitálních práv (DRM) nebo na vyžádání pro různých protokolů streamování se dynamicky provádí šifrování Advanced Encryption (Standard AES) obsahu. DRM licence/AES dešifrování doručení klíče služby jsou také poskytované službou Media Services.

Kromě ochrany obsahu online vysílání datového proudu různých protokolů pro streamování, offline režimu pro chráněný obsah je také často požadované funkce. Podporu offline režimu, je potřeba pro následující scénáře:

* Přehrávání při připojení k Internetu není k dispozici, například při cestování.
* Někteří poskytovatelé obsahu může zakázat doručování licencí DRM nad rámec ohraničení země. Pokud uživatelé chtějí sledovat obsah při cestování mimo zemi, je potřeba ke stažení offline.
* V některých zemích je stále omezena dostupnost Internetu a/nebo šířky pásma. Uživatelé zvolit nejprve stáhnout moct dál sledovat obsah, který je dostatečně vysoká, uspokojivé zobrazení prostředí rozlišení. V takovém případě problém obvykle není dostupnost sítě, ale omezena šířka pásma sítě. Over-the-top (OTT) / poskytovatelů online video platformy (OVP) požádat o podporu offline režimu.

Tento článek popisuje podporu offline režimu FairPlay Streaming (FPS), která cílí na zařízení s Iosem 10 nebo novější. Tato funkce není podporována pro jiné Apple platformy, jako je například watchOS, tvOS nebo Safari v systému macOS.

## <a name="preliminary-steps"></a>Přípravné kroky
Před implementací offline DRM pro FairPlay. na zařízení s iOS 10 +:

* Seznamte se s online ochrana obsahu pro FairPlay. Další informace najdete v následující články a ukázky:

    - [Apple FairPlay Streaming pro Azure Media Services je obecně dostupná](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [Chránit obsah pomocí Apple FairPlay nebo Microsoft PlayReady HLS](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
    - [Ukázky pro online streamování snímků za Sekundu](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* Získáte sadu SDK snímků za Sekundu z webu Apple Developer Network. Tato sada SDK snímků za Sekundu obsahuje dvě součásti:

    - Server SDK snímků za Sekundu, který obsahuje klíč zabezpečení modulu (KSM), klient ukázky, specifikace a sadu testů vektory.
    - Nasazení balíčku snímků za Sekundu, který bude obsahovat specifikaci funkce D, společně s pokyny o tom, jak vygenerovat certifikát snímků za Sekundu, zákaznického privátní klíč a tajný klíč aplikace. Apple vydá balíčku nasazení snímků za Sekundu pouze licencovaného poskytovatele obsahu.

## <a name="configuration-in-media-services"></a>Konfigurace ve službě Media Services
Ke konfiguraci offline režimu snímků za Sekundu prostřednictvím [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices), používat Media Services .NET SDK verze 4.0.0.4 nebo novější, který poskytuje rozhraní API potřebné ke konfiguraci offline režimu snímků za Sekundu.
Budete také potřebovat funkční kód konfigurace ochrany obsahu online režim snímků za Sekundu. Po obdržení kódu ke konfiguraci režimu online ochrana obsahu pro snímků za Sekundu, potřebujete pouze následující dvě změny.

## <a name="code-change-in-the-fairplay-configuration"></a>Změna kódu v konfigurace FairPlay
První změna je definování "Povolit offline režim" logickou hodnotu, volá objDRMSettings.EnableOfflineMode, který má hodnotu true, pokud ji povolí offline scénáři DRM. V závislosti na tento ukazatel proveďte následující změnu konfigurace FairPlay:

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

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>Kód změnit v konfigurace zásad doručení assetu
Druhý změnu je přidejte do slovníku < AssetDeliveryPolicyConfigurationKey, řetězec > s třetím klíčem.
Přidáte AssetDeliveryPolicyConfigurationKey, jak je znázorněno zde:
 
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

Po provedení tohoto kroku obsahuje řetězec < Dictionary_AssetDeliveryPolicyConfigurationKey > zásady doručení assetu snímků za Sekundu následující tři položky:

* AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl nebo AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, v závislosti na faktorech jako server KSM snímků za Sekundu/klíčů používá a určuje, zda je znovu použít stejné doručování prostředků zásady napříč více assetů
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Váš účet Media Services je teď nakonfigurované v režimu offline licencí FairPlay.

## <a name="sample-ios-player"></a>Ukázkový přehrávače pro iOS
Podpora pro offline režim snímků za Sekundu je k dispozici jenom v iOS 10 a novější. Server SDK snímků za Sekundu (verze 3.0 nebo vyšší) obsahuje dokument a ukázky pro offline režim snímků za Sekundu. Konkrétně snímků za Sekundu Server SDK (verze 3.0 nebo vyšší) obsahuje následující dvě položky související s offline režim:

* Dokument: "Offline přehrávání díky FairPlay Streaming a HTTP Live Streaming." Apple, 14. září 2016. V snímků za Sekundu Server SDK verze 4.0 Tento dokument je sloučen do hlavní dokument snímků za Sekundu.
* Ukázkový kód: Ukázka HLSCatalog pro offline režim snímků za Sekundu v 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\ \FairPlay streamování Server SDK verze. V ukázkové aplikaci HLSCatalog následující soubory kódu slouží k implementaci funkce v offline režimu:

    - Soubor kódu AssetPersistenceManager.swift: AssetPersistenceManager je hlavní třída v této ukázce, který ukazuje, jak:

        - Spravujte stahování datové proudy HLS, jako je rozhraní API používá ke spuštění a zrušit stahování a odstranit stávající prostředky zařízení.
        - Sledujte průběh stahování.
    - Soubory kódu AssetListTableViewController.swift a AssetListTableViewCell.swift: AssetListTableViewController představuje hlavní rozhraní této ukázky. Poskytuje seznam prostředků, které ukázky můžete použít k přehrávání, stažení, odstranit nebo zrušit stahování. 

Tyto kroky ukazují, jak nastavit spuštění přehrávače iOS. Za předpokladu, že spustíte z HLSCatalog ukázku v sadě SDK serveru snímků za Sekundu verze 4.0.1, proveďte následující změny kódu:

V HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implementovat metodu `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` pomocí následujícího kódu. Nechť "drmUr" Proměnná přiřazená adresa URL HLS.

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

V HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implementovat metodu `requestApplicationCertificate()`. Tato implementace závisí na Určuje, zda se zařízením pro vložení certifikátu (jenom veřejný klíč) nebo hostovat certifikátu na webu. Následující implementace používá hostované aplikace certifikát používaný ve vzorcích testu. Nechť "certUrl" Proměnná, která obsahuje adresu URL certifikátu aplikace.

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

Pro konečné testování integrované adresa URL videa a adresa URL certifikátu aplikace jsou k dispozici v části "Integrované testy."

V HLSCatalog\Shared\Resources\Streams.plist přidejte adresu URL videa vašeho testu. Pro obsah klíče ID, použijte URL pro získání licence FairPlay s protokolem skd jako jedinečnou hodnotu.

![IOS FairPlay offline aplikací datových proudů](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Použijte vlastní testovací adresa URL videa, adresu URL pro získání licence FairPlay a adresa URL certifikátu aplikace, pokud je nastavení máte. Nebo můžete pokračovat k další části, která obsahuje ukázky testu.

## <a name="integrated-test"></a>Integrované testy
Tři testovací ukázky ve službě Media Services zahrnují následující tři scénáře:

* Chránit pomocí videa, zvuku a alternativní zvukové stopy snímků za Sekundu
* Chráněný, videa a zvuku, ale žádný alternativní zvukové stopy snímků za Sekundu
* Chránit pomocí pouze video a zvuk snímků za Sekundu

Můžete najít v těchto ukázkách [tento ukázkový web](https://aka.ms/poc#22), s odpovídající certifikát aplikace hostované ve službě Azure web app.
Buď verze 3 nebo verze 4 ukázkový Server SDK snímků za Sekundu Pokud hlavní seznam testů obsahuje alternativní zvuk v offline režimu se přehrává zvuk pouze. Proto je nutné odstranit alternativní zvuk. Jinými slovy druhý a třetí ukázky, které jsou uvedeny dříve fungovat v režimu online i offline. Ukázka uvedená nejprve přehrává zvuk pouze v režimu offline, zatímco online streamování funguje správně.

## <a name="faq"></a>Nejčastější dotazy
Následující nejčastější dotazy poskytovat pomoc při řešení potíží:

- **Proč pouze zvuku přehrát ale není videa v offline režimu?** Toto chování se zdá být záměrné ukázkovou aplikaci. Když alternativní zvukové stopy je k dispozici (což se stává pro HLS) v režimu offline, s iOS 10 nebo iOS 11 výchozí alternativní zvukové stopy. Odpovídajícím způsobem upravit toto chování pro offline režim snímků za Sekundu, odeberte alternativní zvukovou stopu z datového proudu. Chcete-li to provést v Media Services, přidejte filtr dynamických manifestů "pouze se zvukem = false." Jinými slovy adresu URL pro HLS končí .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Proč ho stále přehrajte zvuk pouze bez video v režimu offline po přidání pouze se zvukem = false?** V závislosti na klíče návrhu mezipaměti content delivery network (CDN) může do mezipaměti obsah. Vymazání mezipaměti.
- **Snímků za Sekundu offline režimu také podporováno od Iosu 11 kromě iOS 10?** Ano. Snímků za Sekundu offline režimu se podporuje pro zařízení s iOS 10 a iOS 11.
- **Proč nelze nalézt dokument "Do offline režimu přehrávání s FairPlay Streaming a HTTP Live Streaming" v sadě SDK serveru snímků za Sekundu?** Od snímků za Sekundu Server SDK verze 4 Tento dokument se nesloučila do "FairPlay Streaming programování průvodce."
- **Co poslední parametr nebudete při tom muset následující rozhraní API pro offline režim snímků za Sekundu?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Dokumentaci pro toto rozhraní API najdete v tématu [FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration metoda](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). Parametr představuje dobu trvání offline pronájem s hodiny za jednotku.
- **Co je struktura stažení offline souborů na zařízeních s Iosem?** Struktura staženého souboru na zařízení s Iosem vypadá jako na následujícím snímku obrazovky. `_keys` Složky úložiště stáhne společně s jedno úložiště souborů pro každého hostitele služby licence licence snímků za Sekundu. `.movpkg` Složka obsahuje audio a video obsahu. První složka s názvem, který končí dash, za nímž následuje číselnou obsahuje obsah videa. Číselná hodnota je PeakBandwidth videa interpretace. Druhá složka s názvem, který končí dash následovaný 0 obsahuje zvukový obsah. Třetí složku s názvem "Data" obsahuje hlavní seznam stop obsahu snímků za Sekundu. Nakonec boot.xml poskytuje úplný popis `.movpkg` obsah složky. 

![Offline FairPlay iOS ukázková struktura souborů aplikace](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

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
Tento dokument obsahuje následující kroky a informace, které můžete použít k implementaci snímků za Sekundu offline režim:

* Konfigurace ochrany obsahu Media Services pomocí rozhraní API služby Media Services .NET nakonfiguruje dynamického šifrování FairPlay a naším vlastním doručováním licencí FairPlay ve službě Media Services.
* Přehrávače iOS podle ukázky ze sady SDK serveru snímků za Sekundu nastaví iOS přehrávač, který můžete přehrát snímků za Sekundu obsahu v režimu online streamování nebo v režimu offline.
* Ukázková videa snímků za Sekundu se použily k testování režimu offline a online streamování.
* Nejčastější dotazy s častými otázkami offline režimu snímků za Sekundu.
