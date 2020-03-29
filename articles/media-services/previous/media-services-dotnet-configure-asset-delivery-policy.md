---
title: Konfigurace zásad doručování datových zdrojů pomocí sady .NET SDK | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak nakonfigurovat různé zásady doručování prostředků pomocí Azure Media Services .NET SDK.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: ab3c40ee408498453bb137c63c440d980b0b7255
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974508"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Konfigurace zásad doručování datových zdrojů pomocí sady .NET SDK
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Přehled
Pokud plánujete doručování šifrovaných datových zdrojů, jedním z kroků v pracovním postupu doručování obsahu služby Media Services je konfigurace zásad doručování datových zdrojů. Zásady doručování datových zdrojů sdělují službě Media Services, jak chcete, aby byl váš datový zdroj dodán: do kterého streamovacího protokolu by měl být dynamicky zabalen (například MPEG DASH, HLS, Smooth Streaming nebo všechny), bez ohledu na to, zda chcete dynamicky šifrovat vašeho majetku a jak (obálka nebo společné šifrování).

Tento článek popisuje, proč a jak vytvořit a nakonfigurovat zásady poskytování majetku.

>[!NOTE]
>Při vytvoření účtu AMS je k vašemu účtu ve stavu **Zastaveno** přidán **výchozí** koncový bod streamování. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 
>
>Aby bylo možné používat dynamické balení a dynamické šifrování, musí váš datový zdroj obsahovat sadu souborů MP4 s adaptivním datovým tokem nebo adaptivní datové toky.

U stejného datového zdroje můžete použít různé zásady. Můžete například použít šifrování PlayReady pro plynulé streamování a šifrování obálky AES na MPEG DASH a HLS. Veškeré protokoly, které nejsou v zásadách doručení definovány (například když přidáte jedinou zásadu, která jako protokol určuje pouze HLS), budou při streamování blokovány. Výjimkou je, pokud nemáte definovány vůbec žádné zásady doručení prostředku. Pak budou všechny protokoly povolené v nešifrované podobě.

Pokud chcete dodat úložiště šifrovaný prostředek, musíte nakonfigurovat zásady doručování datového zdroje. Před datovým proudem datový chod server datových proudů odebere šifrování úložiště a streamuje váš obsah pomocí zadaných zásad doručení. Chcete-li například doručit datový zdroj zašifrovaný pomocí šifrovacího klíče obálky Advanced Encryption Standard (Advanced Encryption Standard) , nastavte typ zásady na **DynamicEnvelopeEncryption**. Chcete-li odebrat šifrování úložiště a streamovat datový zdroj v přehledné, nastavte typ zásady na **NoDynamicEncryption**. Příklady, které ukazují, jak nakonfigurovat tyto typy zásad následovat.

V závislosti na konfiguraci zásad poskytování datových zdrojů můžete dynamicky balit, šifrovat a streamovat následující streamovací protokoly: Plynulé streamování, HLS a MPEG DASH.

V následujícím seznamu jsou uvedeny formáty, které používáte k streamování položek Vyhlazení, HLS a DASH.

Plynulé streamování:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Hls:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Požadavky
* Před odstraněním AssetDeliveryPolicy, měli byste odstranit všechny datové holokazotory přidružené k prostředku. Později můžete vytvořit nové streamovací lokátory, pokud je to žádoucí, s novým AssetDeliveryPolicy.
* Lokátor streamování nelze vytvořit na šifrovaném datovém zdroji úložiště, pokud není nastavena žádná zásada doručování datových zdrojů.  Pokud prostředek není zašifrovaný úložištěm, systém vám umožní vytvořit lokátor a streamovat datový zdroj v přehledné bez zásad poskytování majetku.
* K jednomu majetku můžete mít přidruženo více zásad pro doručování majetku, ale můžete určit pouze jeden způsob zpracování daného protokolu AssetDeliveryProtocol.  To znamená, že pokud se pokusíte propojit dvě zásady doručení, které určují protokol AssetDeliveryProtocol.SmoothStreaming, což bude mít za následek chybu, protože systém neví, který z nich má použít, když klient vytvoří požadavek na plynulé streamování.
* Pokud máte datový zdroj s existujícím lokátorem streamování, nemůžete s ním propojit novou zásadu (můžete buď odpojit existující zásadu od datového zdroje, nebo aktualizovat zásady doručování přidružené k datovému zdroji).  Nejprve je nutné odebrat lokátor streamování, upravit zásady a potom znovu vytvořit lokátor streamování.  Můžete použít stejné locatorId při vytváření lokátoru streamování, ale měli byste zajistit, že nebude způsobovat problémy pro klienty, protože obsah může být uložen do mezipaměti podle původu nebo následné cdn.

## <a name="clear-asset-delivery-policy"></a>Vymazat zásady doručování majetku

Následující metoda **ConfigureClearAssetDeliveryPolicy** určuje, že nemá být aplikováno dynamické šifrování a že datový proud bude dodán v některém z následujících protokolů: MPEG DASH, HLS a Smooth Streaming. Tuto zásadu můžete použít u šifrovaných prostředků úložiště.

Informace o tom, jaké hodnoty můžete zadat při vytváření AssetDeliveryPolicy, naleznete [v části Typy použité při definování AssetDeliveryPolicy.](#types)

```csharp
    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
        _context.AssetDeliveryPolicies.Create("Clear Policy",
        AssetDeliveryPolicyType.NoDynamicEncryption,
        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
        
        asset.DeliveryPolicies.Add(policy);
    }
```
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Zásady doručování datových zdrojů DynamicCommonEncryption

Následující metoda **CreateAssetDeliveryPolicy** vytvoří **AssetDeliveryPolicy,** který je nakonfigurován tak, aby u hladkého streamovacího protokolu použil dynamické společné šifrování **(DynamicCommonEncryption**) (ostatní protokoly budou blokovány datovým proudem). Metoda trvá dva parametry: **Asset** (prostředek, na který chcete použít zásady doručování) a **IContentKey** (klíč obsahu **commonencryption** typu, další informace naleznete v [tématu: Vytvoření klíče obsahu](media-services-dotnet-create-contentkey.md#common_contentkey)).

Informace o tom, jaké hodnoty můžete zadat při vytváření AssetDeliveryPolicy, naleznete [v části Typy použité při definování AssetDeliveryPolicy.](#types)

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            };
    
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                    "AssetDeliveryPolicy",
                AssetDeliveryPolicyType.DynamicCommonEncryption,
                AssetDeliveryProtocol.SmoothStreaming,
                assetDeliveryPolicyConfiguration);
    
            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
    
            Console.WriteLine();
            Console.WriteLine("Adding Asset Delivery Policy: " +
                assetDeliveryPolicy.AssetDeliveryPolicyType);
     }
```

Azure Media Services také umožňuje přidat šifrování Widevine. Následující příklad ukazuje, jak PlayReady a Widevine přidávají do zásady doručování majetku.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }
```
> [!NOTE]
> Při šifrování pomocí Widevine, budete moci dodat pouze pomocí DASH. Ujistěte se, že jste v protokolu o dodání majetku zadali dash.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Zásady doručování datových zdrojů DynamicEnvelopeEncryption
Následující metoda **CreateAssetDeliveryPolicy** vytvoří **AssetDeliveryPolicy,** který je nakonfigurován tak, aby apel dynamické šifrování obálek **(DynamicEnvelopeEncryption**) na hladké streamování, HLS a DASH protokoly (pokud se rozhodnete nezadávat některé protokoly, budou blokovány z streamování). Metoda trvá dva parametry: **Asset** (prostředek, na který chcete použít zásady doručení) a **IContentKey** (klíč obsahu **EnvelopeEncryption** typu, další informace naleznete v [tématu: Vytvoření klíče obsahu](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Informace o tom, jaké hodnoty můžete zadat při vytváření AssetDeliveryPolicy, naleznete [v části Typy použité při definování AssetDeliveryPolicy.](#types)   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamic Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }
```

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>Typy používané při definování AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a><a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

Následující výčet popisuje hodnoty, které můžete nastavit pro protokol dodání majetku.

```csharp
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        ProgressiveDownload = 0x10, 
 
        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }
```
### <a name="assetdeliverypolicytype"></a><a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

Následující výčet popisuje hodnoty, které můžete nastavit pro typ zásad doručování majetku.  
```csharp
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
        }
```
### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

Následující výčet popisuje hodnoty, které můžete použít ke konfiguraci metody doručení klíče obsahu klientovi.
  ```csharp  
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquisition protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquisition protocol
        ///
        </summary>
        Widevine = 3

    }
```
### <a name="assetdeliverypolicyconfigurationkey"></a><a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

Následující výčet popisuje hodnoty, které můžete nastavit pro konfiguraci klíčů používaných k získání konkrétní konfigurace pro zásady doručování majetku.
```csharp
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,

        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }
```

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

