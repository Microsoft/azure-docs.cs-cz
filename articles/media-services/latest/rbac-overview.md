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
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 212d7f5352b58ee8f5b2c119bb1f5f828591f6bf
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490771"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Řízení přístupu na základě rolí (RBAC) pro účty Media Services

V současné době Azure Media Services není definováno žádné konkrétní vlastních rolí ve službě. Zákazníci můžou využít integrované role **vlastníka** nebo **Přispěvatel** zobrazíte úplný přístup k účtu Azure Media Services. Hlavní rozdíl mezi těmito rolemi je: **vlastníka** můžete řídit, kdo má přístup k prostředku a **Přispěvatel** nelze. Účet čtečky integrované pouze má přístup pro čtení k účtu Media Services. 

## <a name="design-principles"></a>Principy návrhu

Jedním z klíčových principů návrhu rozhraní API v3 je vyšší zabezpečení rozhraní API. rozhraní API v3 nevrátí tajné kódy nebo přihlašovací údaje na **získat** nebo **seznamu** operace. Klíče v odpovědi mají vždy hodnotu null, jsou prázdné nebo upravené. Uživatel musí volat metodu samostatnou akci tajné kódy nebo přihlašovací údaje. **Čtečky** role nelze volat operace, takže ji nelze volat operace, jako je ContentKeyPolicies.GetPolicyPropertiesWithSecrets Asset.ListContainerSas StreamingLocator.ListContentKeys,. S samostatné akce umožňuje nastavit oprávnění zabezpečení podrobnější RBAC v vlastní roli, v případě potřeby.

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
- [Získání obsahu klíče zásad, pomocí Media Services .NET](get-content-key-policy-dotnet-howto.md)

## <a name="next-steps"></a>Další postup

[Vývoj s využitím Media Services v3 rozhraní API](media-services-apis-overview.md)
