---
title: Získání podpisového klíče ze zásady pomocí Azure Media Services v3 .NET
description: Toto téma ukazuje, jak získat podpisový klíč ze stávajících zásad pomocí sady Media Services v3 .NET SDK.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2dea262fadb61adc9e219b76f9ac048c11e650d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065967"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Získání podpisového klíče ze stávajících zásad

Jedním z klíčových principů návrhu rozhraní API v3 je vyšší zabezpečení rozhraní API. v3 API nevracejí tajné klíče nebo pověření na **get** nebo **list** operace. Podrobné vysvětlení naleznete zde: Další informace naleznete v [tématu RBAC a media services účty](rbac-overview.md)

Příklad v tomto článku ukazuje, jak pomocí rozhraní .NET získat podpisový klíč z existující zásady. 
 
## <a name="download"></a>Stáhnout 

Klonujte úložiště GitHub, které obsahuje úplnou ukázku .NET, do počítače pomocí následujícího příkazu:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Příklad ContentKeyPolicy s tajnými klíči je umístěn ve složce [EncryptWithDRM.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM)

## <a name="get-contentkeypolicy-with-secrets"></a>Získat ContentKeyPolicy s tajnými kódy 

Chcete-li získat klíč, použijte **GetPolicyPropertiesWithSecretsAsync**, jak je znázorněno v příkladu níže.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Další kroky

[Návrh systému ochrany obsahu s více variantami DRM s využitím řízení přístupu](design-multi-drm-system-with-access-control.md) 
