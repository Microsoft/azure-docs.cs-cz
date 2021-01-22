---
title: Porovnání ukázek migrace Media Services v2 až V3 | Microsoft Docs
description: Sada ukázek, které vám pomůžou porovnat rozdíly v kódu mezi Azure Media Services V2 a v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: 7705443bbe810866cd5b52946b310b5b79bfa072
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690370"
---
# <a name="media-services-migration-code-sample-comparison"></a>Porovnání vzorového kódu Media Services migrace

![logo Průvodce migrací](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

Můžete použít některé z našich ukázek kódu a porovnat tak způsob, jakým jsou mezi sadami SDK provedeny věci.

## <a name="samples-for-comparison"></a>Ukázky pro porovnání

Následující tabulka obsahuje seznam vzorků pro porovnání mezi v2 a v3 pro běžné scénáře.

|Scenario|V2 API|rozhraní V3 API|
|---|---|---|
|Vytvoření assetu a nahrání souboru |[Příklad v2 .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[Příklad v3 .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Odeslat úlohu|[Příklad v2 .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[Příklad v3 .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Ukazuje, jak nejdřív vytvořit transformaci a pak odeslat úlohu.|
|Publikování assetu pomocí šifrování AES |1. vytvoření `ContentKeyAuthorizationPolicyOption`<br/>2. vytvořit `ContentKeyAuthorizationPolicy`<br/>3. vytvořit `AssetDeliveryPolicy`<br/>4. vytvoření `Asset` a nahrání obsahu nebo odeslání `Job` a použití `OutputAsset`<br/>5. přidružte `AssetDeliveryPolicy` k `Asset`<br/>6. vytvořit `ContentKey`<br/>7. připojit `ContentKey` k `Asset`<br/>8. Create `AccessPolicy`<br/>9. vytvořit `Locator`<br/><br/>[Příklad v2 .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. vytvoření `ContentKeyPolicy`<br/>2. vytvořit `Asset`<br/>3. nahrání obsahu nebo použití `Asset` jako `JobOutput`<br/>4. vytvořit `StreamingLocator`<br/><br/>[Příklad v3 .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Získat podrobnosti o úloze a spravovat úlohy |[Správa úloh s v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Správa úloh pomocí v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="next-steps"></a>Další kroky

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
