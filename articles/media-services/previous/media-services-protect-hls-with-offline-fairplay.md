---
title: Ochrana obsahu HLS pomocí offline Apple FairPlay – Azure | Microsoft Docs
description: Toto téma poskytuje přehled a ukazuje, jak použít Azure Media Services k dynamickému šifrování obsahu HTTP Live Streaming (HLS) pomocí nástroje Apple FairPlay v režimu offline.
services: media-services
keywords: HLS, DRM, FairPlay streaming (FPS), offline, iOS 10
documentationcenter: ''
author: IngridAtMicrosoft
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: willzhan
ms.reviewer: dwgeo
ms.custom: devx-track-csharp
ms.openlocfilehash: 75f4ee50306a09c04511d8075adc412f7eef0c7c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063385"
---
# <a name="offline-fairplay-streaming-for-ios"></a>Streamování FairPlay pro iOS v offline režimu

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Vyberte verzi Media Services, kterou používáte:"]
> * [Verze 3](../latest/drm-offline-fairplay-for-ios-concept.md)
> * [Verze 2](media-services-protect-hls-with-offline-fairplay.md)

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

Azure Media Services poskytuje sadu dobře navrhovaných [služeb ochrany obsahu](https://azure.microsoft.com/services/media-services/content-protection/) , které zahrnují:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- Šifrování AES-128

Správa digitálních práv (DRM) šifrování obsahu (DRM)/Advanced Encryption Standard (DRM) se provádí dynamicky na vyžádání různých protokolů streamování. K dispozici jsou také služby doručování licencí DRM/dešifrovacího klíče AES Media Services.

Kromě ochrany obsahu pro online streamování v různých protokolech streamování je také často vyžádaná funkce offline režim pro chráněný obsah. Podpora offline režimu je nutná pro následující scénáře:

* Přehrávání, pokud není k dispozici připojení k Internetu, například během cestování.
* Někteří poskytovatelé obsahu můžou zakázat doručování licencí DRM za hranice země nebo oblasti. Pokud uživatelé chtějí sledovat obsah během cestování mimo zemi nebo oblast, je stažení offline potřeba.
* V některých zemích a oblastech je dostupnost Internetu a/nebo šířka pásma stále omezená. Uživatelé si můžou chtít nejdřív stáhnout, aby mohli sledovat obsah v rámci řešení, které je dostatečně vysoké, aby se mohlo zobrazit uspokojivý zážitek. V takovém případě problém obvykle není dostupnost sítě, ale omezená šířka pásma sítě. Poskytovatelé OTT (OVP) s podporou převzetí služeb při prvním přístupu () s žádostí o podporu offline režimu.

Tento článek popisuje podporu FairPlay streamování (FPS) v režimu offline, která cílí na zařízení se systémem iOS 10 nebo novějším. Tato funkce není podporovaná pro jiné platformy Apple, jako je watchOS, tvOS nebo Safari v macOS.

## <a name="preliminary-steps"></a>Předběžné kroky
Před implementací offline DRM pro FairPlay na zařízení s iOS 10 +:

* Seznámení s online ochranou obsahu pro FairPlay Další informace najdete v následujících článcích a ukázkách:

    - [FairPlay streaming pro Azure Media Services je všeobecně k dispozici](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [Chraňte svůj HLS obsah pomocí Apple FairPlay nebo Microsoft PlayReady](./media-services-protect-hls-with-fairplay.md)
    - [Ukázka pro streamování online snímků](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* Získejte sadu FPS SDK ze sítě Apple Developer. Sada FPS SDK obsahuje dvě komponenty:

    - Sada SDK serveru pro FPS, která obsahuje modul zabezpečení klíčů (KSM), ukázky klienta, specifikace a sadu testovacích vektorů.
    - Balíček nasazení FPS, který obsahuje specifikaci funkce D, spolu s pokyny, jak vygenerovat certifikát snímků pro sbalení, soukromý klíč specifický pro zákazníka a tajný klíč aplikace. Apple vydá balíček nasazení FPS jenom pro licencované poskytovatele obsahu.

## <a name="configuration-in-media-services"></a>Konfigurace v Media Services
Pro offline konfiguraci režimu FPS prostřednictvím [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices)použijte sadu Media Services .NET SDK 4.0.0.4 nebo novější, která poskytuje potřebné rozhraní API pro konfiguraci offline režimu FPS.
Pracovní kód budete také potřebovat ke konfiguraci ochrany obsahu v režimu online FPS. Po získání kódu pro konfiguraci ochrany obsahu v režimu online pro FPS budete potřebovat jenom tyto dvě změny.

## <a name="code-change-in-the-fairplay-configuration"></a>Změna kódu v konfiguraci FairPlay
První změnou je určení logické hodnoty "Povolit offline režim", která se označuje jako objDRMSettings. EnableOfflineMode, která je pravdivá, když povolí scénář offline DRM. V závislosti na tomto indikátoru proveďte následující změnu konfigurace FairPlay:

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

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>Změna kódu v konfiguraci zásad doručení assetů
Druhým změnou je přidání třetího klíče do slovníku<AssetDeliveryPolicyConfigurationKey,> řetězců.
Přidejte AssetDeliveryPolicyConfigurationKey, jak je znázorněno zde:
 
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

Po provedení tohoto kroku budou v zásadách doručování prostředků FPS <Dictionary_AssetDeliveryPolicyConfigurationKey> řetězec, který obsahuje následující tři položky:

* AssetDeliveryPolicyConfigurationKey. FairPlayBaseLicenseAcquisitionUrl nebo AssetDeliveryPolicyConfigurationKey. FairPlayLicenseAcquisitionUrl, v závislosti na faktorech, jako je například použití sady FPS KSM/Key Server a zda znovu použijete stejné zásady doručování prostředků napříč několika prostředky.
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Teď je váš účet Media Services nakonfigurovaný tak, aby poskytoval offline licence FairPlay.

## <a name="sample-ios-player"></a>Ukázkový přehrávač pro iOS
Podpora režimu FPS v režimu offline je k dispozici pouze v iOS 10 a novějších. Sada SDK serveru FPS (verze 3,0 nebo novější) obsahuje dokument a ukázku pro offline režim FPS. Konkrétně sada SDK serveru FPS (verze 3,0 nebo novější) obsahuje následující dvě položky týkající se offline režimu:

* Dokument: "přehrávání offline pomocí FairPlay streaming a HTTP Live Streaming." Apple, 14. září 2016. V sadě FPS Server SDK verze 4,0 je tento dokument sloučen do hlavního dokumentu FPS.
* Vzorový kód: Ukázka HLSCatalog pro offline režim přechodu v sadě SDK serveru \FairPlay streaming verze 3.1 \ Development\Client\ HLSCatalog_With_FPS \HLSCatalog\. V ukázkové aplikaci HLSCatalog se pro implementaci funkcí v režimu offline používá následující soubory kódu:

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

![Datové proudy aplikace pro iOS v FairPlay](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Použijte vlastní adresu URL testovacího videa, adresu URL pro získání licence FairPlay a adresu URL certifikátu aplikace, pokud je máte nastavené. Nebo můžete pokračovat k další části, která obsahuje ukázky testů.

## <a name="integrated-test"></a>Integrovaný test
Tři ukázkové testy v Media Services pokrývají následující tři scénáře:

* Chráněná přes FPS s videem, zvukem a alternativním zvukovou stopou
* Chráněná za sekundu, s videem a zvukem, ale bez alternativní zvukové stopy
* Chráněná přes FPS jenom s videem a bez zvuku

Tyto ukázky najdete na [tomto ukázkovém webu](https://aka.ms/poc#22)s odpovídajícím certifikátem aplikace hostovaným ve webové aplikaci Azure.
V případě ukázky verze 3 nebo 4 sady SDK serveru FPS je v případě, že hlavní seznam testů obsahuje alternativní zvuk, v režimu offline, který přehrává jenom zvuk. Proto je potřeba obložení alternativního zvuku. Jinými slovy, druhý a třetí vzorek uvedený dříve pracuje v režimu online a offline. Ukázka uvedená jako první hraje zvuk pouze v režimu offline, zatímco online streamování funguje správně.

## <a name="faq"></a>Časté otázky
Následující nejčastější dotazy poskytují pomoc při řešení potíží:

- **Proč se v režimu offline jenom zvuk hraje, ale ne video?** Zdá se, že se jedná o návrh ukázkové aplikace. Když je v režimu offline k dispozici alternativní zvuková stopa (což je případ pro HLS), v režimu offline se jako výchozí použije alternativní zvuková stopa v systému iOS 10 i iOS 11. Pokud chcete toto chování pro offline režim přechodu na více snímků, odeberte alternativní zvukovou stopu z datového proudu. Chcete-li to provést na Media Services, přidejte dynamický filtr manifestu "pouze" audio = false. " Jinými slovy, HLS adresa URL končí řetězcem. ISM/manifest (Format = M3U8-AAPL, audio-Only = false). 
- **Proč I po přidání zvuku = false zůstane zvuk pouze bez videa v režimu offline?** V závislosti na návrhu klíče mezipaměti Content Delivery Network (CDN) může být obsah uložen do mezipaměti. Vyprázdnit mezipaměť.
- **Podporuje se kromě iOS 10 offline režim FPS i v iOS 11?** Ano. Režim offline režimu FPS je podporován pro iOS 10 a iOS 11.
- **Proč v sadě SDK serveru pro FPS nejde najít dokument "offline přehrávání pomocí FairPlay streaming a HTTP Live Streaming"?** Vzhledem k tomu, že sada FPS Server SDK verze 4, tento dokument se sloučil do Průvodce programováním pro FairPlay streaming.
- **K čemu poslední parametr v následujícím rozhraní API pro offline režim přechodu?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Dokumentaci k tomuto rozhraní API najdete v tématu [Metoda FairPlayConfiguration. CreateSerializedFairPlayOptionConfiguration](/dotnet/api/microsoft.windowsazure.mediaservices.client.fairplay.fairplayconfiguration.createserializedfairplayoptionconfiguration). Parametr představuje dobu trvání pronájmu v režimu offline, přičemž sekunda jako jednotka.
- **Jaká je struktura souborů ke stažení/offline na zařízeních s iOS?** Stažená struktura souborů na zařízení se systémem iOS vypadá jako na následujícím snímku obrazovky. `_keys`Složka uchovává stažené licence na FPS a jeden soubor úložiště pro každého hostitele licenční služby. `.movpkg`Složka obsahuje zvuk a video obsah. První složka s názvem, který končí spojovníkem následovaným číslem, obsahuje obsah videa. Číselná hodnota je PeakBandwidth verze videa. Druhá složka s názvem, který končí čárkou následovanou 0, obsahuje zvukový obsah. Třetí složka s názvem "data" obsahuje hlavní seznam skladeb obsahu FPS. Nakonec boot.xml poskytuje úplný popis `.movpkg` obsahu složky. 

![Struktura souborů ukázkové aplikace v režimu offline FairPlay iOS](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

Vzorový boot.xml soubor:
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

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="summary"></a>Souhrn
Tento dokument obsahuje následující kroky a informace, které můžete použít k implementaci režimu přechodu do režimu offline:

* Media Services konfigurace ochrany obsahu prostřednictvím rozhraní API Media Services .NET nakonfiguruje dynamické šifrování FairPlay a doručování licencí FairPlay v Media Services.
* Přehrávač iOS v závislosti na ukázce ze sady SDK serveru FPS nastaví přehrávač pro iOS, který může přehrávat obsah FPS buď v režimu online streamování, nebo v režimu offline.
* Ukázková videa snímků za sekundu se používají k testování offline režimu a online streamování.
* DOTAZY na dotazy týkající se režimu přechodu na více snímků.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
