---
title: Vytvoření ContentKeys s využitím .NET
description: Tento článek ukazuje, jak vytvořit klíče obsahu pomocí .NET. Tyto klíče poskytují zabezpečený přístup k prostředkům.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 05bf928490e94f43b755e1958213899e9e1e98e9
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014165"
---
# <a name="create-contentkeys-with-net"></a>Vytvoření ContentKeys s využitím .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Media Services umožňuje vytváření a doručování šifrovaných prostředků. **ContentKey** zajišťuje zabezpečený přístup k vašim **assetům** s. 

Při vytváření nového prostředku (například před [odesláním souborů](media-services-dotnet-upload-files.md)) můžete zadat následující možnosti šifrování: **StorageEncrypted**, **CommonEncryptionProtected** nebo **EnvelopeEncryptionProtected**. 

Při doručování prostředků klientům můžete [nakonfigurovat, aby bylo možné dynamicky šifrovat prostředky](media-services-dotnet-configure-asset-delivery-policy.md) pomocí jednoho z následujících dvou šifrování: **DynamicEnvelopeEncryption** nebo **DynamicCommonEncryption**.

Šifrované prostředky musí být přidruženy k **ContentKey** s. Tento článek popisuje, jak vytvořit klíč obsahu.

> [!NOTE]
> Při vytváření nového prostředku **StorageEncrypted** pomocí sady Media Services .NET SDK se **ContentKey** automaticky vytvoří a propojí s Assetem.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
Jedna z hodnot, které je třeba nastavit při vytváření klíče obsahu, je typ klíče obsahu. Vyberte jednu z následujících hodnot. 

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

## <a name="create-envelope-type-contentkey"></a><a id="envelope_contentkey"></a>Vytvořit ContentKey typu obálky
Následující fragment kódu vytvoří klíč obsahu typu šifrování obálky. Pak přidruží klíč ke specifikovanému prostředku.

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


## <a name="create-common-type-contentkey"></a><a id="common_contentkey"></a>Vytvořit Common Type ContentKey
Následující fragment kódu vytvoří klíč obsahu pro běžný typ šifrování. Pak přidruží klíč ke specifikovanému prostředku.

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

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

