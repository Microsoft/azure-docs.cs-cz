---
title: Konfigurace podpory spravované identity v existujícím clusteru Service Fabric
description: Tady je postup povolení podpory spravovaných identit v existujícím clusteru Azure Service Fabric.
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: cb6e4ab00afd80cba41881e46296f7046a905919
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934953"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster-preview"></a>Konfigurace podpory spravované identity v existujícím clusteru Service Fabric (Preview)

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

Aby se změny projevily, budete také muset změnit zásadu upgradu, aby určovala vynucené restartování Service Fabric modulu runtime na každém uzlu, protože upgrade probíhají prostřednictvím clusteru. Tento restart zajistí, že se nově povolená systémová služba spustí a spustí na každém uzlu. V následujícím fragmentu kódu je `forceRestart` základní nastavení. pro zbývající část nastavení použijte existující hodnoty.  

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
> Po úspěšném dokončení upgradu nezapomeňte vrátit nastavení `forceRestart`, abyste minimalizovali dopad následných upgradů. 

## <a name="errors-and-troubleshooting"></a>Chyby a řešení potíží

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