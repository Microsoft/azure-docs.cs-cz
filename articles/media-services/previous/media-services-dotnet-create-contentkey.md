---
title: Vytvoření ContentKeys s rozhraním .NET
description: Informace o vytváření obsahu klíče, které zajišťují zabezpečený přístup k prostředkům.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: 53df4c4cef19f6eef99aa15bb265317aa0cd1d58
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="create-contentkeys-with-net"></a>Vytvoření ContentKeys s rozhraním .NET
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Služba Media Services umožňuje vytvářet a doručovat šifrované prostředky. A **ContentKey** zajišťuje zabezpečený přístup k vaší **Asset**s. 

Při vytváření nového prostředku (například před [nahrání souborů](media-services-dotnet-upload-files.md)), můžete zadat následující možnosti šifrování: **StorageEncrypted**, **CommonEncryptionProtected**, nebo **EnvelopeEncryptionProtected**. 

Při předvádění prostředky pro klienty, můžete [konfigurace pro prostředky dynamicky šifrovat](media-services-dotnet-configure-asset-delivery-policy.md) s jedním z následujících dvou šifrování: **DynamicEnvelopeEncryption** nebo  **DynamicCommonEncryption**.

Šifrované prostředky musí být přidružený **ContentKey**s. Tento článek popisuje postup vytvoření klíče k obsahu.

> [!NOTE]
> Při vytváření nového **StorageEncrypted** asset pomocí sady Media Services .NET SDK **ContentKey** je automaticky vytvořené a připojené k assetu.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
Jedna z hodnot musí nastavit při vytváření obsahu klíč je typ obsahu klíče. Vyberte jednu z následujících hodnot. 

```csharp
    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }
```

## <a id="envelope_contentkey"></a>Vytvoření typu obálky ContentKey
Následující fragment kódu vytvoří klíč obsahu typu šifrování obálku. Potom přidruží klíč zadaný prostředek.

```csharp
    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

call

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);
```


## <a id="common_contentkey"></a>Vytvoření stejného typu ContentKey
Následující fragment kódu vytvoří klíč obsahu běžné typu šifrování. Potom přidruží klíč zadaný prostředek.

```csharp
    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
call

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 
```

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

