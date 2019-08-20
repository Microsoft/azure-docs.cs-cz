---
title: Azure Service Fabric – nasazení nového clusteru Azure Service Fabric s podporou pro spravovanou identitu | Microsoft Docs
description: V tomto článku se dozvíte, jak vytvořit nový cluster Service Fabric se zapnutou spravovanou identitou.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: 80fb06c96f727d2b78faf4f100d59341aeaeff42
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624959"
---
# <a name="create-a-new-azure-service-fabric-cluster-with-managed-identity-support-preview"></a>Vytvoření nového clusteru Azure Service Fabric s podporou spravované identity (Preview)

Aby bylo možné získat přístup k funkci Managed identity pro aplikace Service Fabric Azure, musíte nejdřív v clusteru povolit službu Managed identity token. Tato služba zodpovídá za ověřování Service Fabric aplikací pomocí svých spravovaných identit a pro získání přístupových tokenů jejich jménem. Jakmile je služba povolená, můžete ji zobrazit v Service Fabric Explorer v části **systém** v levém podokně, která je spuštěná pod názvem **Fabric:/System/ManagedIdentityTokenService** vedle ostatních systémových služeb.

> [!NOTE]
> Aby bylo možné povolit **službu tokenu spravované identity**, je nutné, aby verze modulu runtime Service Fabric 6.5.658.9590 nebo novější.  

## <a name="enable-the-managed-identity-token-service"></a>Povolení služby tokenu spravované identity 
Pokud chcete povolit službu spravovaných tokenů identity při vytváření clusteru, můžete použít následující fragment kódu v šabloně Azure Resource Manager:

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

## <a name="errors"></a>Chyby

Pokud se nasazení v této zprávě nepovede, znamená to, že cluster není na požadované verzi Service Fabric (Minimální podporovaný modul runtime je 6,5 CU2):



```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Další postup
* [Nasazení aplikace Azure Service Fabric se spravovanou identitou přiřazenou systémem](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Nasazení aplikace Azure Service Fabric s uživatelem přiřazenou spravovanou identitou](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Využití spravované identity Service Fabric aplikace z kódu služby](./how-to-managed-identity-service-fabric-app-code.md)
* [Udělení přístupu k aplikacím Azure Service Fabric k ostatním prostředkům Azure](./how-to-grant-access-other-resources.md)

## <a name="related-articles"></a>Související články
* Kontrola [podpory spravovaných identit](./concepts-managed-identity.md) v Azure Service Fabric

* [Povolení podpory spravovaných identit v existujícím clusteru Azure Service Fabric](./configure-existing-cluster-enable-managed-identity-token-service.md)
