---
title: Řízení přístupu na základě rolí pro účty Mediálních služeb – Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje řízení přístupu na základě rolí (RBAC) pro účty Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 93b2cd3a2565b14ea07d6db6b14dd146e4223528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66236921"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Řízení přístupu na základě rolí (RBAC) pro účty mediálních služeb

V současné době Azure Media Services nedefinuje žádné vlastní role specifické pro službu. Chcete-li získat úplný přístup k účtu Mediální služby, mohou zákazníci používat předdefinované role **vlastníka** nebo **přispěvatele**. Hlavní rozdíl mezi těmito rolemi je: **Vlastník** může řídit, kdo má přístup k prostředku a **přispěvatel** nemůže. Integrovanou roli **čtečky** lze také použít, ale uživatel nebo aplikace bude mít pouze přístup pro čtení k rozhraním API mediálních služeb. 

## <a name="design-principles"></a>Principy návrhu

Jedním z klíčových principů návrhu rozhraní API v3 je vyšší zabezpečení rozhraní API. v3 API nevracejí tajné klíče nebo pověření na **get** nebo **list** operace. Klíče v odpovědi mají vždy hodnotu null, jsou prázdné nebo upravené. Uživatel musí volat samostatnou metodu akce získat tajné klíče nebo pověření. Role **Čtečka** nemůže volat operace jako Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. S samostatné akce umožňuje nastavit podrobnější oprávnění zabezpečení RBAC ve vlastní roli v případě potřeby.

Chcete-li uvést operace, které služba Media Services podporuje, proveďte následující akce:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

Předdefinovaný článek [definice rolí](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) přesně říká, co role uděluje. 

Další informace naleznete v následujících článcích:

- [Role klasického správce předplatného, role Azure RBAC a role správce Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Co je RBAC pro prostředky Azure?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Použití RBAC ke správě přístupu](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Operace zprostředkovatele prostředků mediálních služeb](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>Další kroky

- [Vývoj pomocí mediálních služeb v3 API](media-services-apis-overview.md)
- [Získat zásady klíče obsahu pomocí služby Media Services .NET](get-content-key-policy-dotnet-howto.md)
