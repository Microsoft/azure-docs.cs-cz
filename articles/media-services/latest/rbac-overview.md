---
title: Řízení přístupu na základě role pro účty Media Services – Azure | Microsoft Docs
description: Tento článek popisuje řízení přístupu na základě role (RBAC) pro účty Azure Media Services.
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
ms.openlocfilehash: 1872d0dd56a0459988e65852340c36493ee53f9f
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132241"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Řízení přístupu na základě role (RBAC) pro účty Media Services

V současné době Azure Media Services nedefinuje žádné vlastní role specifické pro danou službu. Aby mohli zákazníci získat úplný přístup k účtu Media Services, můžou použít předdefinované role **vlastníka** nebo **přispěvatele**. Hlavní rozdíl mezi těmito rolemi je: **vlastník** může řídit, kdo má přístup k prostředku a **Přispěvatel** nemůže. Lze také použít integrovanou roli **Čtenář** , ale uživatel nebo aplikace budou mít přístup jen pro čtení k rozhraním API Media Services. 

## <a name="design-principles"></a>Principy návrhu

Jedním z klíčových principů návrhu rozhraní API v3 je vyšší zabezpečení rozhraní API. Rozhraní V3 API nevrací tajné klíče ani přihlašovací údaje na operace **Get** nebo **list** . Klíče v odpovědi mají vždy hodnotu null, jsou prázdné nebo upravené. Pro získání tajných klíčů nebo přihlašovacích údajů musí uživatel zavolat samostatnou metodu akce. Role **Čtenář** nemůže volat operace jako Asset. ListContainerSas, StreamingLocator. ListContentKeys, ContentKeyPolicies. GetPolicyPropertiesWithSecrets. Samostatné akce umožňují nastavit podrobnější oprávnění zabezpečení RBAC v vlastní roli v případě potřeby.

Pokud chcete zobrazit seznam operací, které Media Services podporuje, udělejte toto:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

V článku [předdefinované definice rolí](../../role-based-access-control/built-in-roles.md) se přesně dozvíte, co role uděluje. 

Další informace najdete v následujících článcích:

- [Role klasického správce předplatného, role Azure RBAC a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Co je řízení přístupu na základě role Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
- [Správa přístupu pomocí RBAC](../../role-based-access-control/role-assignments-rest.md)
- [Media Services operace poskytovatele prostředků](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>Další kroky

- [Vývoj s využitím rozhraní API Media Services V3](media-services-apis-overview.md)
- [Získání zásad pro klíč obsahu pomocí Media Services .NET](get-content-key-policy-dotnet-howto.md)
