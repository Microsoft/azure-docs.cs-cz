---
title: Získání podpisového klíče ze zásady .NET
description: V tomto tématu se dozvíte, jak získat podpisový klíč ze stávající zásady pomocí sady Media Services V3 .NET SDK.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 1436561f7c82446038c231fadec3bd62c94d4ff9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955101"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Získání podpisového klíče ze stávajících zásad

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Jedním z klíčových principů návrhu rozhraní API v3 je vyšší zabezpečení rozhraní API. Rozhraní V3 API nevrací tajné klíče ani přihlašovací údaje na operace **Get** nebo **list** . Další informace najdete v podrobném vysvětlení: Další informace najdete v tématu [Azure RBAC a účty Media Services](rbac-overview.md) .

V příkladu v tomto článku se dozvíte, jak pomocí .NET získat podpisový klíč ze stávající zásady. 
 
## <a name="download"></a>Stáhnout 

Naklonujte úložiště GitHub, které obsahuje úplný vzorek .NET, na váš počítač, a to pomocí následujícího příkazu:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Příklad ContentKeyPolicy s tajnými klíči je umístěný ve složce [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) .

## <a name="get-contentkeypolicy-with-secrets"></a>Získání ContentKeyPolicy s tajnými kódy 

K získání klíče použijte **GetPolicyPropertiesWithSecretsAsync**, jak je znázorněno v následujícím příkladu.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Další kroky

[Návrh systému ochrany obsahu s více variantami DRM s využitím řízení přístupu](design-multi-drm-system-with-access-control.md) 
