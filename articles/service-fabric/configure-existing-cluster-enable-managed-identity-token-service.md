---
title: Azure Service Fabric – konfigurace stávajícího clusteru Azure Service Fabric pro povolení podpory spravovaných identit
description: V tomto článku se dozvíte, jak nakonfigurovat existující cluster Azure Service Fabric tak, aby povoloval podporu spravovaných identit.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 13b8b38a206b0dae0877263a5cda56a134d4788d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351612"
---
# <a name="configure-an-existing-azure-service-fabric-cluster-to-enable-managed-identity-support-preview"></a>Konfigurace stávajícího clusteru Azure Service Fabric pro povolení podpory spravované identity (Preview)
Aby bylo možné získat přístup k funkci Managed identity pro aplikace Service Fabric Azure, musíte nejdřív v clusteru povolit **službu Managed identity token** . Tato služba zodpovídá za ověřování Service Fabric aplikací pomocí svých spravovaných identit a pro získání přístupových tokenů jejich jménem. Jakmile je služba povolená, můžete ji zobrazit v Service Fabric Explorer v části **systém** v levém podokně, která běží pod názvem **Fabric:/System/ManagedIdentityTokenService**.

> [!NOTE]
> Aby bylo možné povolit **službu tokenu spravované identity**, je nutné, aby verze modulu runtime Service Fabric 6.5.658.9590 nebo novější.  
> 
> Service Fabric verzi clusteru můžete najít z Azure Portal otevřením prostředku clusteru a kontrolou vlastnosti **Service Fabric verze** v části **Essentials** .
> 
> Pokud je cluster v režimu **ručního** upgradu, budete ho muset nejdřív upgradovat na 6.5.658.9590 nebo novější.


## <a name="enable-the-managed-identity-token-service-in-an-existing-cluster"></a>Povolení služby spravovaného tokenu identity v existujícím clusteru
Chcete-li povolit službu spravovaných tokenů identity v existujícím clusteru, bude nutné iniciovat upgrade clusteru s určením dvou změn: povolení služby spravovaného tokenu identity a vyžádání restartování každého uzlu. Provedete to tak, že do šablony Azure Resource Manager přidáte následující dva fragmenty kódu:

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