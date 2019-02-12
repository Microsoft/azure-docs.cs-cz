---
title: Vytvoření klíčů ContentKeys s využitím .NET
description: Zjistěte, jak vytvořit symetrické klíče, které zajišťují zabezpečený přístup k prostředkům.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2019
ms.author: juliako
ms.openlocfilehash: af8c2b547e1ce1b15410ee20eb59934559ac41ac
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992156"
---
# <a name="create-contentkeys-with-net-legacy"></a>Vytvoření klíčů ContentKeys s využitím .NET (starší verze)
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Služba Media Services umožňuje vytváření a doručování šifrované prostředků. A **ContentKey** zajišťuje zabezpečený přístup k vaší **Asset**s. 

Při vytváření nového prostředku (například před [nahrávání souborů](media-services-dotnet-upload-files.md)), můžete zadat následující možnosti šifrování: **StorageEncrypted**, **CommonEncryptionProtected**, nebo **EnvelopeEncryptionProtected**. 

Při doručování prostředků pro vaše klienty, je možné [konfigurace pro prostředky dynamicky šifrovat](media-services-dotnet-configure-asset-delivery-policy.md) s jedním z následujících dvou šifrování: **DynamicEnvelopeEncryption** nebo **DynamicCommonEncryption**.

Šifrované prostředky mají přidruženo **ContentKey**s. Tento článek popisuje postup vytvoření klíče k obsahu.

> [!NOTE]
> Při vytváření nového **StorageEncrypted** prostředku pomocí Media Services .NET SDK **ContentKey** je automaticky vytvořen a spojen assetu.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
Jedna z hodnot, je nutné nastavit při vytváření obsahu je klíč obsahu typ klíče. Vyberte jednu z následujících hodnot. 

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

## <a id="envelope_contentkey"></a>Vytvoření obálky typu ContentKey
Následující fragment kódu vytvoří klíč obsahu šifrovací typu obálky. Pak přiřadí klíč zadaný assetu.

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


## <a id="common_contentkey"></a>Vytvoření ContentKey společný typ.
Následující fragment kódu vytvoří typu běžné šifrovací klíč k obsahu. Pak přiřadí klíč zadaný assetu.

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

