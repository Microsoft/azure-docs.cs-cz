---
title: Nakonfigurujte zásady doručení assetu pomocí .NET SDK | Microsoft Docs
description: Toto téma ukazuje, jak nakonfigurovat zásady doručení jiný prostředek Azure Media Services .NET SDK.
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
ms.openlocfilehash: aee2477e0633974cba42ab26e102323cb9606810
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Nakonfigurujte zásady doručení assetu pomocí .NET SDK
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Přehled
Pokud budete chtít doručení šifrované prostředky, jeden z kroků v pracovním postupu doručování obsahu Media Services je konfigurace zásad doručení pro prostředky. Zásady doručení assetu informuje Media Services, jak chcete použít pro váš asset, který bude doručen: do které protokol pro streamování by měl váš asset dynamicky zabalené (pro příklad, MPEG DASH, HLS, technologie Smooth Streaming nebo všechny), zda chcete dynamicky šifrovat. váš asset a jak (obálky nebo common encryption).

Tento článek popisuje důvod, proč a jak vytvořit a nakonfigurovat zásady doručení assetu.

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 
>
>Abyste mohli používat dynamické balením a dynamickým šifrováním také váš asset musí obsahovat sadu s adaptivní přenosovou rychlostí soubory MP4 s rychlostmi nebo soubory technologie Smooth Streaming s adaptivní přenosovou rychlostí.

Různé zásady je možné aplikovat na stejný asset. Můžete například použít šifrování PlayReady na technologie Smooth Streaming a pomocí standardu AES Envelope šifrování a MPEG DASH, HLS. Veškeré protokoly, které nejsou v zásadách doručení definovány (například když přidáte jedinou zásadu, která jako protokol určuje pouze HLS), budou při streamování blokovány. Výjimkou je, pokud nemáte definovány vůbec žádné zásady doručení prostředku. Pak budou všechny protokoly povolené v nešifrované podobě.

Pokud chcete doručovat šifrované asset úložiště, musíte nakonfigurovat zásady doručení assetu. Před asset Streamovat, server datových proudů odebere šifrování úložiště a datové proudy svůj obsah pomocí zadaného doručování zásad. Například k poskytování asset šifrován Advanced Encryption (Standard AES) obálky šifrovací klíč, nastavte typ zásad na **DynamicEnvelopeEncryption**. Pokud chcete odebrat šifrování úložiště a Streamovat prostředek v nešifrované podobě, nastavte typ zásad na **NoDynamicEncryption**. Postupujte podle příklady, které ukazují, jak konfigurovat tyto typy zásad.

V závislosti na tom, jak nakonfigurovat zásady doručení assetu, můžete dynamicky balíčku, šifrování a stream u následujících protokolů streamování: technologie Smooth Streaming, HLS a MPEG DASH.

V následujícím seznamu jsou formáty, které umožňují stream Smooth, HLS a POMLČKY.

Technologie Smooth Streaming:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streamování koncový bod služby media název účtu name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Požadavky
* Před odstraněním AssetDeliveryPolicy, je nutné odstranit všechny streamování lokátorů přidružený asset. Později můžete vytvořit nové lokátory streamování, v případě potřeby s novou AssetDeliveryPolicy.
* Šifrované majetku úložiště nelze vytvořit lokátor streamování, nastavena žádné zásady doručení assetu.  Není-li Asset šifrování úložiště, systém vám umožní vytvořit Lokátor a Streamovat prostředek v nešifrované podobě bez zásady pro doručení assetu.
* Můžete mít více zásady doručení mediálního přidružené jednoho datového zdroje, ale můžete určit pouze jeden způsob, jak zpracovávat dané AssetDeliveryProtocol.  Znamená, pokud se pokusíte propojit dvě zásady doručení, které zadat AssetDeliveryProtocol.SmoothStreaming protokol, který bude výsledkem chyba, protože systém nebude vědět, který jeden se má použít, když klient odešle požadavek technologie Smooth Streaming.
* Pokud máte prostředek s stávající Lokátor streamování, nemůže propojit nové zásady pro daný prostředek (můžete odpojit existující zásady z prostředku, nebo aktualizujete zásady pro doručení přidružený asset).  Nejdřív musíte odstraňte Lokátor streamování, upravit zásady a potom je znovu vytvořit lokátor streamování.  Stejné locatorId můžete použít, když je znovu vytvořit lokátor streamování, ale měli byste zajistit, že vzhledem k tomu, že do mezipaměti obsah počátek nebo podřízené CDN, který nebude způsobovat problémy pro klienty.

## <a name="clear-asset-delivery-policy"></a>Zásady doručení assetu vymazat

Následující **ConfigureClearAssetDeliveryPolicy** metoda určuje nelze použít dynamické šifrování a poskytovat datový proud v některém z těchto protokolů: protokoly MPEG DASH, HLS nebo technologie Smooth Streaming. Můžete chtít tuto zásadu použít pro vaše prostředky šifrování úložiště.

Informace na hodnotách, které můžete zadat při vytváření AssetDeliveryPolicy najdete v tématu [typy používané při definování AssetDeliveryPolicy](#types) části.

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
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Zásady doručení assetu DynamicCommonEncryption

Následující **CreateAssetDeliveryPolicy** metoda vytvoří **AssetDeliveryPolicy** nakonfigurovaný pro použití běžného dynamického šifrování (**DynamicCommonEncryption**) smooth streamování protokolu (jiné protokoly, bude zablokován streamování). Metoda přebírá dva parametry: **Asset** (asset, do které chcete použít zásady doručení) a **IContentKey** (klíč obsahu **CommonEncryption** typu, pro Další informace najdete v tématu: [vytváření klíč obsahu](media-services-dotnet-create-contentkey.md#common_contentkey)).

Informace na hodnotách, které můžete zadat při vytváření AssetDeliveryPolicy najdete v tématu [typy používané při definování AssetDeliveryPolicy](#types) části.

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

Azure Media Services můžete také přidat Widevine šifrování. Následující příklad ukazuje technologie PlayReady a Widevine, který se přidává do zásady doručení assetu.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption 
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
> Při šifrování s technologií Widevine, by pouze možné doručíte pomocí čárka. Nezapomeňte zadat DASH v doručovací protokol assetu.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Zásady doručení assetu DynamicEnvelopeEncryption
Následující **CreateAssetDeliveryPolicy** metoda vytvoří **AssetDeliveryPolicy** nakonfigurovaný pro použití dynamické obálky šifrování (**DynamicEnvelopeEncryption**) pro protokoly technologie Smooth Streaming, HLS a DASH (Pokud se rozhodnete není uveden některé protokoly, že budou Blokovaní z streamování). Metoda přebírá dva parametry: **Asset** (asset, do které chcete použít zásady doručení) a **IContentKey** (klíč obsahu **EnvelopeEncryption** typu Další informace najdete v tématu: [vytváření klíč obsahu](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Informace na hodnotách, které můžete zadat při vytváření AssetDeliveryPolicy najdete v tématu [typy používané při definování AssetDeliveryPolicy](#types) části.   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
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

Následující výčet popisuje hodnoty, které lze nastavit pro doručovací protokol assetu.

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

Následující výčet popisuje hodnoty, které můžete zadat pro typ zásad doručení assetu.  
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

Následující výčet popisuje hodnoty, které můžete použít ke konfiguraci metodu doručení obsahu klíče, který se klient.
  ```csharp  
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }
```
### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

Následující výčet popisuje hodnoty, které můžete nastavit, aby konfigurace klíče, které slouží k získání konkrétní konfigurace pro zásady pro doručení assetu.
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

