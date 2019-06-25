---
title: Řízení přístupu na základě rolí pro účty Media Services – Azure | Dokumentace Microsoftu
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66236921"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Řízení přístupu na základě rolí (RBAC) pro účty Media Services

Azure Media Services v současné době nedefinuje žádné vlastní role konkrétní službě. Chcete-li získat úplný přístup k účtu Media Services, zákazníci mohou používat předdefinované role **vlastníka** nebo **Přispěvatel**. Hlavní rozdíl mezi těmito rolemi je: **vlastníka** můžete řídit, kdo má přístup k prostředku a **Přispěvatel** nelze. Předdefinované **čtečky** role je také možné, ale uživatel nebo aplikace bude mít pouze pro čtení přístup k rozhraní API služby Media Services. 

## <a name="design-principles"></a>Principy návrhu

Jedním z klíčových principů návrhu rozhraní API v3 je vyšší zabezpečení rozhraní API. rozhraní API v3 nevrátí tajné kódy nebo přihlašovací údaje na **získat** nebo **seznamu** operace. Klíče v odpovědi mají vždy hodnotu null, jsou prázdné nebo upravené. Uživatel musí volat metodu samostatnou akci tajné kódy nebo přihlašovací údaje. **Čtečky** role nejde volat operace, jako je Asset.ListContainerSas StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. S samostatné akce umožňuje nastavit oprávnění zabezpečení podrobnější RBAC v vlastní roli, v případě potřeby.

Seznam operací služby Media Services podporuje, provést:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

[Definic předdefinovaných rolí](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) článku se dozvíte přesně co role uděluje. 

Zobrazit další informace v následujících článcích:

- [Role klasického správce předplatného, role Azure RBAC a role správce Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Co je RBAC pro prostředky Azure?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Ke správě přístupu pomocí RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Operace poskytovatele prostředků služby Media Services](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>Další postup

- [Vývoj s využitím Media Services v3 rozhraní API](media-services-apis-overview.md)
- [Získání obsahu klíče zásad, pomocí Media Services .NET](get-content-key-policy-dotnet-howto.md)
