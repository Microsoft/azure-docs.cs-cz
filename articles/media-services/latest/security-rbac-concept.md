---
title: Řízení přístupu na základě role pro účty Media Services
description: Tento článek popisuje řízení přístupu na základě role Azure (Azure RBAC) pro účty Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: c086fe6a47227901cd44c4684452c842df5286ae
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281944"
---
# <a name="azure-role-based-access-control-azure-rbac-for-media-services-accounts"></a>Řízení přístupu na základě role Azure (Azure RBAC) pro účty Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V současné době Azure Media Services nedefinuje žádné vlastní role specifické pro danou službu. Aby mohli zákazníci získat úplný přístup k účtu Media Services, můžou použít předdefinované role **vlastníka** nebo **přispěvatele**. Hlavní rozdíl mezi těmito rolemi je: **vlastník** může řídit, kdo má přístup k prostředku a **Přispěvatel** nemůže. Lze také použít integrovanou roli **Čtenář** , ale uživatel nebo aplikace budou mít přístup jen pro čtení k rozhraním API Media Services. 

## <a name="design-principles"></a>Principy návrhu

Jedním z klíčových principů návrhu rozhraní API v3 je vyšší zabezpečení rozhraní API. Rozhraní V3 API nevrací tajné klíče ani přihlašovací údaje na operace **Get** nebo **list** . Klíče v odpovědi mají vždy hodnotu null, jsou prázdné nebo upravené. Pro získání tajných klíčů nebo přihlašovacích údajů musí uživatel zavolat samostatnou metodu akce. Role **Čtenář** nemůže volat operace jako Asset. ListContainerSas, StreamingLocator. ListContentKeys, ContentKeyPolicies. GetPolicyPropertiesWithSecrets. Pokud budete chtít, můžete v případě potřeby nastavit podrobnější oprávnění zabezpečení služby Azure RBAC v vlastní roli pomocí samostatných akcí.

Pokud chcete zobrazit seznam operací, které Media Services podporuje, udělejte toto:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

V článku [předdefinované definice rolí](../../role-based-access-control/built-in-roles.md) se přesně dozvíte, co role uděluje. 

Další informace najdete v následujících článcích:

- [Role správců pro klasický odběr, role Azure a role Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Co je řízení přístupu na základě role v Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
- [Přidání nebo odebrání přiřazení rolí v Azure pomocí REST API](../../role-based-access-control/role-assignments-rest.md)
- [Media Services operace poskytovatele prostředků](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>Další kroky

- [Vývoj s využitím rozhraní API Media Services V3](media-services-apis-overview.md)
- [Získání zásad pro klíč obsahu pomocí Media Services .NET](drm-get-content-key-policy-dotnet-how-to.md)
