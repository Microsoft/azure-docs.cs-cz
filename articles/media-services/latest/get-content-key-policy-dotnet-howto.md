---
title: Získejte podpisový klíč ze stávající zásady pomocí .NET SDK služby Media Services v3 – Azure | Dokumentace Microsoftu
description: Toto téma ukazuje, jak získat podpisového klíče ze stávající zásady pomocí sady .NET SDK služby Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 04/09/2019
ms.author: juliako
ms.openlocfilehash: 49cc2b8c151053377f8f1da0792f10a06695b332
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471167"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Získejte podpisový klíč ze stávající zásady

Jedním z klíčových principů návrhu rozhraní API v3 je vyšší zabezpečení rozhraní API. rozhraní API v3 nevrátí tajné kódy nebo přihlašovací údaje na **získat** nebo **seznamu** operace. Klíče v odpovědi mají vždy hodnotu null, jsou prázdné nebo upravené. Uživatel musí volat metodu samostatnou akci tajné kódy nebo přihlašovací údaje. **Čtečky** role nelze volat operace, takže ji nelze volat operace, jako je ContentKeyPolicies.GetPolicyPropertiesWithSecrets Asset.ListContainerSas StreamingLocator.ListContentKeys,. S samostatné akce umožňuje nastavit oprávnění zabezpečení podrobnější RBAC v vlastní roli, v případě potřeby.

Další informace najdete v tématu [účty RBAC a Media Services](rbac-overview.md)

V příkladu v tomto článku ukazuje, jak používat .NET k získání podpisového klíče z existující zásady. 
 
## <a name="download"></a>Ke stažení 

Naklonujte úložiště GitHub obsahující úplnou ukázku .NET k počítači pomocí následujícího příkazu:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
ContentKeyPolicy s příkladem tajných kódů se nachází v [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) složky.

## <a name="get-contentkeypolicy-with-secrets"></a>Získat ContentKeyPolicy s tajnými kódy 

Chcete-li získat klíče, použijte **GetPolicyPropertiesWithSecretsAsync**, jak je znázorněno v následujícím příkladu.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Další postup

[Návrh systému s více variantami DRM ochrany obsahu pomocí řízení přístupu](design-multi-drm-system-with-access-control.md) 
