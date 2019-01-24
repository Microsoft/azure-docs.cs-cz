---
title: Konfigurace zásad doručování prostředků pomocí sady .NET SDK | Dokumentace Microsoftu
description: Toto téma ukazuje, jak konfigurace zásad doručování různých prostředků pomocí Azure Media Services .NET SDK.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: cfowler
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/05/2018
ms.author: juliako
ms.openlocfilehash: d96ed28be2fcb3941591854662f9aa20faf04e5d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810155"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Konfigurace zásad doručování prostředků pomocí sady .NET SDK
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Přehled
Pokud máte v plánu k prostředkům šifrované doručování, jeden z kroků v postupu Media Services content delivery je konfigurace zásad doručování prostředků. Zásady doručení assetu říká Media Services způsob pro váš prostředek, který bude doručen: do datových proudů protokol, který by měl váš asset dynamicky balí (pro příklad, MPEG DASH, HLS, technologie Smooth Streaming nebo všechny), jestli chcete šifrovat dynamicky váš asset a jak (Obálka nebo používat standard common encryption).

Tento článek popisuje, proč a jak vytvořit a nakonfigurovat zásady doručení assetu.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 
>
>Abyste mohli používat dynamické balení a dynamického šifrování také, váš asset musí obsahovat sadu s adaptivní přenosovou rychlostí soubory MP4 rychlostmi nebo soubory technologie Smooth Streaming s adaptivní přenosovou rychlostí.

Do stejného assetu můžete použít různé zásady. Může například použít šifrování PlayReady na technologie Smooth Streaming a standardu AES Envelope šifrování a MPEG DASH, HLS. Veškeré protokoly, které nejsou v zásadách doručení definovány (například když přidáte jedinou zásadu, která jako protokol určuje pouze HLS), budou při streamování blokovány. Výjimkou je, pokud nemáte definovány vůbec žádné zásady doručení prostředku. Pak budou všechny protokoly povolené v nešifrované podobě.

Pokud chcete dodávat šifrované prostředků úložiště, musíte nakonfigurovat zásady doručení assetu. Předtím, než můžete Streamovat prostředek, server streamování zruší šifrování úložiště a streamuje obsah pomocí zadaného doručování zásad. Například k poskytování asset zašifrovaná pomocí šifrování AES (Advanced Standard) obálky šifrovací klíč, nastavte typ zásad na **DynamicEnvelopeEncryption**. Pokud chcete odstranit úložiště šifrování a streamování majetku v nezašifrované podobě, nastavte typ zásad na **NoDynamicEncryption**. Postupujte podle příkladů, které ukazují, jak nakonfigurovat tyto typy zásad.

V závislosti na tom, jak nakonfigurovat zásady doručení assetu můžete dynamicky balíček, šifrování a streamování následující streamovacích protokolů: Technologie Smooth Streaming, HLS a MPEG DASH.

Následující seznam uvádí formáty, které používáte ke streamování protokol Smooth, HLS a DASH.

Technologie Smooth Streaming:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Požadavky
* Před odstraněním AssetDeliveryPolicy, je nutné odstranit všechny lokátory streamování přidružený k assetu. Později můžete vytvořit nové streamovací lokátory, v případě potřeby s novou AssetDeliveryPolicy.
* Šifrované prostředků úložiště nelze vytvořit lokátor streamování, když nejsou nastavené žádné zásady doručení assetu.  Pokud prostředek není šifrování úložiště, systém vám umožní vytvořit Lokátor a streamování prostředků v nezašifrované podobě bez zásad doručení prostředku.
* Můžete mít více zásad doručování prostředků přidružené k jedné assetu, ale můžete zadat pouze jeden způsob, jak zpracovat daný AssetDeliveryProtocol.  To znamená, pokud se pokusíte k propojení dvou zásady doručování, které určují AssetDeliveryProtocol.SmoothStreaming protokol, který způsobí chybu, protože systém neví, což je ta, ve které chcete použít, když klient odešle požadavek technologie Smooth Streaming.
* Pokud máte prostředek s stávající Lokátor streamování, nelze propojit novou zásadu assetu (můžete odpojit existující zásady z prostředku, nebo aktualizujete zásady pro doručení přidružený asset).  Nejprve musíte odebrat Lokátor streamování, upravte zásady a pak znovu vytvořte Lokátor streamování.  Když znovu vytvořit lokátor streamování, ale měli byste zajistit, že, který nebude způsobovat problémy pro klienty od mezipaměti obsahu počátek nebo příjem dat CDN, můžete použít stejné locatorId.

## <a name="clear-asset-delivery-policy"></a>Zásady doručení assetu vymazat

Následující **ConfigureClearAssetDeliveryPolicy** metody určuje k dynamické šifrování se nedá použít a k poskytování datový proud v některém z těchto protokolů:  Protokoly, MPEG DASH, HLS a Smooth Streaming. Můžete chtít použít tyto zásady pro vaše prostředky šifrování úložiště.

Informace o hodnoty, můžete zadat při vytváření AssetDeliveryPolicy, najdete v článku [typy používané při definování AssetDeliveryPolicy](#types) oddílu.

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
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption zásady doručení assetu

Následující **CreateAssetDeliveryPolicy** metoda vytvoří **AssetDeliveryPolicy** , který je nakonfigurovaný na použití běžného dynamického šifrování (**DynamicCommonEncryption**) technologie smooth streaming protokolu (jiné protokoly budou při streamování blokovány). Tato metoda přebírá dva parametry: **Asset** (do které chcete použít zásady doručení assetu) a **IContentKey** (klíč obsahu **CommonEncryption** typ, pro další informace najdete v tématu: [Vytvoření klíče k obsahu](media-services-dotnet-create-contentkey.md#common_contentkey)).

Informace o hodnoty, můžete zadat při vytváření AssetDeliveryPolicy, najdete v článku [typy používané při definování AssetDeliveryPolicy](#types) oddílu.

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

Azure Media Services také umožňuje přidat Widevine šifrování. Následující příklad ukazuje PlayReady a Widevine přidávaný do zásady doručení assetu.

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
> Při šifrování pomocí Widevine, by pouze možné doručit pomocí DASH. Nezapomeňte zadat POMLČKU v doručovací protokol assetu.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption zásady doručení assetu
Následující **CreateAssetDeliveryPolicy** metoda vytvoří **AssetDeliveryPolicy** , který je nakonfigurovaný na použití obálky dynamického šifrování (**DynamicEnvelopeEncryption**) protokoly technologie Smooth Streaming, HLS a DASH (Pokud se rozhodnete pro nezadání některé protokoly se budou při streamování blokovány). Tato metoda přebírá dva parametry: **Asset** (do které chcete použít zásady doručení assetu) a **IContentKey** (klíč obsahu **EnvelopeEncryption** typ, pro další informace najdete v tématu: [Vytvoření klíče k obsahu](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Informace o hodnoty, můžete zadat při vytváření AssetDeliveryPolicy, najdete v článku [typy používané při definování AssetDeliveryPolicy](#types) oddílu.   

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

## <a id="types"></a>Typy používané při definování AssetDeliveryPolicy

### <a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

Následující výčet popisuje hodnoty, které můžete nastavit pro doručovací protokol assetu.

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
### <a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

Následující výčet popisuje hodnoty, které lze zadat pro typ zásady doručení assetu.  
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
### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

Následující výčet popisuje hodnoty, které vám umožní nakonfigurovat metodu doručení klíče k obsahu do klienta.
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
### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

Následující výčet popisuje hodnoty, které lze nastavit na konfiguraci klíče, použít k získání konkrétní konfigurace zásad doručení prostředku.
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
## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

