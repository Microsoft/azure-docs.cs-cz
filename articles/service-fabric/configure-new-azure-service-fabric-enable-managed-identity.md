---
title: Konfigurace podpory spravovaných identit pro nový cluster Service Fabric
description: Tady je postup povolení podpory spravovaných identit v novém clusteru Azure Service Fabric.
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: dd0cbd3251185a7831852ead47ca0b120126cf55
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81415673"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster"></a>Konfigurace podpory spravovaných identit pro nový cluster Service Fabric

Pokud chcete používat [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) ve vašich aplikacích Service Fabric, nejdřív v clusteru povolte *službu Managed identity token* . Tato služba zodpovídá za ověřování Service Fabric aplikací pomocí svých spravovaných identit a pro získání přístupových tokenů jejich jménem. Jakmile je služba povolená, můžete ji zobrazit v Service Fabric Explorer v části **systém** v levém podokně, která je spuštěná pod názvem **Fabric:/System/ManagedIdentityTokenService** vedle ostatních systémových služeb.

> [!NOTE]
> Aby bylo možné povolit **službu tokenu spravované identity**, je nutné, aby verze modulu runtime Service Fabric 6.5.658.9590 nebo novější.  

## <a name="enable-the-managed-identity-token-service"></a>Povolení služby tokenu spravované identity

Pokud chcete službu Managed identity token Service povolit při vytváření clusteru, přidejte do své Azure Resource Manager šablony clusteru následující fragment kódu:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

## <a name="errors"></a>chyby

Pokud se nasazení v této zprávě nepovede, znamená to, že cluster není na požadované verzi Service Fabric (Minimální podporovaný modul runtime je 6,5 CU2):


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>Související články

* Kontrola [podpory spravovaných identit](./concepts-managed-identity.md) v Azure Service Fabric

* [Povolení podpory spravovaných identit v existujícím clusteru Azure Service Fabric](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Další kroky

* [Nasazení aplikace Azure Service Fabric se spravovanou identitou přiřazenou systémem](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Nasazení aplikace Azure Service Fabric s uživatelem přiřazenou spravovanou identitou](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Využití spravované identity Service Fabric aplikace z kódu služby](./how-to-managed-identity-service-fabric-app-code.md)
* [Udělení přístupu k aplikacím Azure Service Fabric k ostatním prostředkům Azure](./how-to-grant-access-other-resources.md)