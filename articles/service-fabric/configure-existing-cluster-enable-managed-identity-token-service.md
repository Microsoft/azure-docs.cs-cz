---
title: Konfigurace podpory spravované identity v existujícím clusteru Service Fabric
description: Tady je postup povolení podpory spravovaných identit v existujícím clusteru Azure Service Fabric.
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: dc341688cae6d98f53be10351e4e4572a3539e4e
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790035"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster"></a>Konfigurace podpory spravované identity v existujícím clusteru Service Fabric

Pokud chcete používat [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) ve vašich aplikacích Service Fabric, nejdřív v clusteru povolte *službu Managed identity token* . Tato služba zodpovídá za ověřování Service Fabric aplikací pomocí svých spravovaných identit a pro získání přístupových tokenů jejich jménem. Jakmile je služba povolená, můžete ji zobrazit v Service Fabric Explorer v části **systém** v levém podokně, která běží pod názvem **Fabric:/System/ManagedIdentityTokenService**.

> [!NOTE]
> Aby bylo možné povolit **službu tokenu spravované identity**, je nutné, aby verze modulu runtime Service Fabric 6.5.658.9590 nebo novější.  
>
> Service Fabric verzi clusteru můžete najít z Azure Portal otevřením prostředku clusteru a kontrolou vlastnosti **Service Fabric verze** v části **Essentials** .
>
> Pokud je cluster v režimu **ručního** upgradu, budete ho muset nejdřív upgradovat na 6.5.658.9590 nebo novější.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>Povolení *služby spravovaného tokenu identity* v existujícím clusteru

Chcete-li povolit službu spravovaných tokenů identity v existujícím clusteru, bude nutné zahájit upgrade clusteru s určením dvou změn: (1) povolení služby spravovaného tokenu identity a (2) požadavky na restartování každého uzlu. Nejdřív přidejte následující fragment kódu, který Azure Resource Manager šablona clusteru:

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

Aby se změny projevily, budete také muset změnit zásadu upgradu, aby určovala vynucené restartování Service Fabric modulu runtime na každém uzlu, protože upgrade probíhají prostřednictvím clusteru. Tento restart zajistí, že se nově povolená systémová služba spustí a spustí na každém uzlu. V následujícím fragmentu kódu `forceRestart` je základní nastavení pro povolení restartu. Pro zbývající parametry použijte hodnoty popsané níže nebo použijte existující vlastní hodnoty, které už jsou pro prostředek clusteru zadané. Vlastní nastavení zásad upgradu prostředků infrastruktury (upgradeDescription) se dají zobrazit na webu Azure Portal výběrem možnosti upgrady prostředků infrastruktury na prostředku Service Fabric nebo resources.azure.com. Výchozí možnosti pro zásady upgradu (upgradeDescription) není možné zobrazit z PowerShellu ani resources.azure.com. Další informace najdete v tématu [ClusterUpgradePolicy](/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy) .  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> Po úspěšném dokončení upgradu nezapomeňte nastavení vrátit zpátky `forceRestart` , abyste minimalizovali dopad následných upgradů. 

## <a name="errors-and-troubleshooting"></a>Chyby a řešení problémů

Pokud se nasazení nepovede s následující zprávou, znamená to, že cluster neběží na dostatečně vysokém Service Fabric verzi:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Další kroky
* [Nasazení aplikace Azure Service Fabric se spravovanou identitou přiřazenou systémem](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Nasazení aplikace Azure Service Fabric s uživatelem přiřazenou spravovanou identitou](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Využití spravované identity Service Fabric aplikace z kódu služby](./how-to-managed-identity-service-fabric-app-code.md)
* [Udělení přístupu k aplikacím Azure Service Fabric k ostatním prostředkům Azure](./how-to-grant-access-other-resources.md)
