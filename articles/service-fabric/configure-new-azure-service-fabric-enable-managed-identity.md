---
title: Konfigurace podpory spravovaných identit pro nový cluster Service Fabric
description: Tady je postup, jak povolit podporu spravovaných identit v novém clusteru Azure Service Fabric.
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: dd0cbd3251185a7831852ead47ca0b120126cf55
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415673"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster"></a>Konfigurace podpory spravovaných identit pro nový cluster Service Fabric

Chcete-li používat [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) ve vašich aplikacích Service Fabric, povolte nejprve *službu Token spravované identity* v clusteru. Tato služba je zodpovědná za ověřování aplikací Service Fabric pomocí jejich spravovaných identit a za získání přístupových tokenů jejich jménem. Jakmile je služba povolena, můžete ji vidět v aplikaci Service Fabric Explorer v části **Systém** v levém podokně, spuštěné pod názvem **fabric:/System/ManagedIdentityTokenService** vedle jiných systémových služeb.

> [!NOTE]
> K povolení **služby Managed Identity Token Service Fabric**verze 6.5.658.9590 nebo vyšší je vyžadována runtime service fabric verze 6.5.658.9590 nebo vyšší .  

## <a name="enable-the-managed-identity-token-service"></a>Povolení služby Token spravované identity

Pokud chcete povolit službu Token spravované identity v době vytvoření clusteru, přidejte do šablony Správce prostředků Azure v clusteru následující úryvek:

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

Pokud se nasazení nezdaří s touto zprávou, znamená to, že cluster není na požadované verzi Service Fabric (minimální podporovaný modul runtime je 6,5 CU2):


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>Související články

* Kontrola [podpory spravovaných identit](./concepts-managed-identity.md) ve službě Azure Service Fabric

* [Povolení podpory spravovaných identit v existujícím clusteru Azure Service Fabric](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Další kroky

* [Nasazení aplikace Azure Service Fabric se spravovanou identitou přiřazenou systémem](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Nasazení aplikace Azure Service Fabric s uživatelem přiřazenou spravovanou identitou](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Využití spravované identity aplikace Service Fabric z kódu služby](./how-to-managed-identity-service-fabric-app-code.md)
* [Udělení přístupu aplikací Azure Service Fabric k dalším prostředkům Azure](./how-to-grant-access-other-resources.md)