---
title: Konfigurace zásad doručení assetů pomocí sady .NET SDK | Microsoft Docs
description: V tomto tématu se dozvíte, jak nakonfigurovat různé zásady doručování assetů pomocí Azure Media Services .NET SDK.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 24fd4dcb9b24b6d025ff0327d98fee15a05fb7de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89267713"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Konfigurace zásad doručení assetů pomocí sady .NET SDK

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Přehled
Pokud plánujete doručení šifrovaných prostředků, jeden z kroků v pracovním postupu doručování obsahu Media Services konfiguruje zásady doručování pro prostředky. Zásada doručení assetu oznamuje Media Services, jak chcete, aby se Asset doručil: do kterého by měl být váš Asset dynamicky zabalen (například MPEG POMLČKa, HLS, Smooth Streaming nebo vše), ať už chcete, nebo ne, chcete-li prostředek dynamicky zašifrovat a jak (obálky nebo běžné šifrování).

Tento článek popisuje, proč a jak vytvořit a nakonfigurovat zásady doručení assetu.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno** . Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 
>
>Aby bylo možné použít dynamické balení a dynamické šifrování, vaše Asset musí obsahovat sadu adaptivních přenosů rychlostmi nebo adaptivní přenosové rychlosti Smooth Streaming souborů.

U stejného prostředku můžete použít jiné zásady. Můžete například použít šifrování PlayReady pro Smooth Streaming a šifrování obálek AES na MPEG POMLČKy a HLS. Veškeré protokoly, které nejsou v zásadách doručení definovány (například když přidáte jedinou zásadu, která jako protokol určuje pouze HLS), budou při streamování blokovány. Výjimkou je, pokud nemáte definovány vůbec žádné zásady doručení prostředku. Pak budou všechny protokoly povolené v nešifrované podobě.

Pokud chcete doručovat šifrovaný prostředek úložiště, musíte nakonfigurovat zásady doručování prostředků. Předtím, než bude možné Asset streamovat, server streamování odstraní šifrování úložiště a streamuje obsah pomocí zadaných zásad doručování. Pokud třeba chcete zajistit, aby se Asset zašifroval pomocí šifrovacího klíče standard AES (Advanced Encryption Standard) (AES), nastavte typ zásady na **DynamicEnvelopeEncryption**. Pokud chcete odstranit šifrování úložiště a streamovat prostředek v nejasném případě, nastavte typ zásady na **NoDynamicEncryption**. Příklady, které ukazují, jak nakonfigurovat tyto typy zásad, následují.

V závislosti na tom, jak nakonfigurujete zásady doručení assetu, můžete dynamicky zabalit, šifrovat a streamovat následující protokoly streamování: Smooth Streaming, HLS a MPEG POMLČKa.

Následující seznam obsahuje formáty používané ke streamování hladkého, HLS a POMLČKy.

Smooth Streaming:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Důležité informace
* Před odstraněním AssetDeliveryPolicy byste měli odstranit všechny Lokátory streamování přidružené k assetu. V případě potřeby můžete později vytvořit nové Lokátory streamování s novým AssetDeliveryPolicy.
* Lokátor streamování nejde vytvořit na prostředku šifrovaném úložiště, pokud není nastavená žádná zásada pro doručení prostředků.  Pokud prostředek není šifrovaný jako zašifrovaný, systém vám umožní vytvořit Lokátor a streamovat prostředek ve formě bez zásad doručení assetu.
* K jednomu prostředku můžete mít k dispozici více zásad doručení prostředků, ale můžete zadat jenom jeden způsob, jak zpracovat daný AssetDeliveryProtocol.  To znamená, že se pokusíte propojit dvě zásady doručování, které určují protokol AssetDeliveryProtocol. SmoothStreaming, který bude mít za následek chybu, protože systém neví, který z nich chcete použít, když klient provede požadavek Smooth Streaming.
* Pokud máte Asset s existujícím lokátorem streamování, nemůžete propojit nové zásady s Assetem (můžete buď odpojit stávající zásady od assetu, nebo aktualizovat zásady doručování přidružené k assetu).  Nejdřív musíte odebrat Lokátor streamování, upravit zásady a pak znovu vytvořit Lokátor streamování.  Stejné locatorId můžete použít při opětovném vytvoření lokátoru streamování, ale měli byste zajistit, aby nedošlo k potížím pro klienty, protože obsah může být uložený v mezipaměti od původu nebo přes CDN pro příjem dat.

## <a name="clear-asset-delivery-policy"></a>Vymazat zásady doručení assetu

Následující metoda **ConfigureClearAssetDeliveryPolicy** určuje, že se nemá používat dynamické šifrování a že se má datový proud doručit v jednom z následujících protokolů: MPEG pomlčka, HLS a Smooth Streaming Protocols. Tuto zásadu můžete chtít použít pro šifrované prostředky úložiště.

Informace o tom, jaké hodnoty můžete zadat při vytváření AssetDeliveryPolicy, najdete v tématu [typy používané při definování oddílu AssetDeliveryPolicy](#types) .

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
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Zásady doručování prostředků DynamicCommonEncryption

Následující metoda **CreateAssetDeliveryPolicy** vytvoří **AssetDeliveryPolicy** , který je nakonfigurován pro použití dynamického společného šifrování (**DynamicCommonEncryption**) na protokol pro plynulé streamování (ostatní protokoly budou blokovány ze streamování). Metoda přijímá dva parametry: **Asset** (Asset, pro který chcete použít zásady doručování) a **IContentKey** (klíč obsahu typu **CommonEncryption** , další informace najdete v tématu [vytvoření klíče obsahu](media-services-dotnet-create-contentkey.md#common_contentkey)).

Informace o tom, jaké hodnoty můžete zadat při vytváření AssetDeliveryPolicy, najdete v tématu [typy používané při definování oddílu AssetDeliveryPolicy](#types) .

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

Azure Media Services taky umožňuje přidat šifrování Widevine. Následující příklad ukazuje, že se přidávají PlayReady i Widevine do zásad doručení assetu.

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
> Při šifrování pomocí Widevine byste mohli doručovat jenom POMLČKy. Nezapomeňte zadat POMLČKu v protokolu doručení assetu.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Zásady doručování prostředků DynamicEnvelopeEncryption
Následující metoda **CreateAssetDeliveryPolicy** vytvoří **AssetDeliveryPolicy** , který je nakonfigurován na použití dynamického šifrování obálky (**DYNAMICENVELOPEENCRYPTION**) na Smooth Streaming, HLS a spojovníky (Pokud se rozhodnete, že neurčíte některé protokoly, budou blokovány ze streamování). Metoda přijímá dva parametry: **Asset** (Asset, pro který chcete použít zásady doručování) a **IContentKey** (klíč obsahu typu **EnvelopeEncryption** , další informace najdete v tématu [vytvoření klíče obsahu](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Informace o tom, jaké hodnoty můžete zadat při vytváření AssetDeliveryPolicy, najdete v tématu [typy používané při definování oddílu AssetDeliveryPolicy](#types) .   

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

Následující výčet popisuje hodnoty, které můžete nastavit pro protokol doručení assetu.

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

Následující výčet popisuje hodnoty, které můžete nastavit pro typ zásad doručení assetu.  
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

Následující výčet popisuje hodnoty, které můžete nastavit ke konfiguraci klíčů používaných k získání konkrétní konfigurace pro zásady doručení assetu.
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

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

