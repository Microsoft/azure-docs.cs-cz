---
title: Automatické škálování aplikace běžící v Azure Service Fabric sítě
description: Naučte se konfigurovat zásady automatického škálování pro služby aplikace Service Fabric sítě.
author: georgewallace
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: a707e3601bb24b2d5c2aa9402edff4a2e8803033
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625900"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Vytvoření zásad automatického škálování pro aplikaci Service Fabric sítě

> [!IMPORTANT]
> Náhled sítě Azure Service Fabric je vyřazený. Nová nasazení již nebudou povolena prostřednictvím rozhraní API pro Service Fabric sítě. Podpora stávajících nasazení bude pokračovat do 28. dubna 2021.
> 
> Podrobnosti najdete v tématu [vyřazení náhledu do sítě Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Jednou z hlavních výhod nasazení aplikací do Service Fabric sítě je možnost snadného škálování služeb v systému nebo. Tato služba by se měla používat ke zpracování proměnlivých objemů zatížení vašich služeb nebo ke zlepšení dostupnosti. Můžete ručně škálovat služby v rámci nebo ven nebo nastavit zásady automatického škálování.

[Automatické škálování](service-fabric-mesh-scalability.md#autoscaling-service-instances) umožňuje dynamicky škálovat počet instancí služby (horizontální škálování). Automatické škálování dává velkou pružnost a umožňuje zřizování nebo odebírání instancí služby na základě využití procesoru nebo paměti.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Možnosti vytvoření zásady automatického škálování, triggeru a mechanismu
Zásady automatického škálování se definují pro každou službu, kterou chcete škálovat. Zásady jsou definované v souboru prostředků služby YAML nebo v šabloně nasazení JSON. Každá zásada škálování se skládá ze dvou částí: Trigger a mechanismu škálování.

Aktivační událost definuje, kdy je vyvolána zásada automatického škálování.  Zadejte typ triggeru (průměrné zatížení) a metriku, která se má monitorovat (procesor nebo paměť).  Horní a dolní prahové hodnoty zatížení zadané jako procento. Interval škálování definuje, jak často (v sekundách) kontrolovat zadané využití (například průměrné zatížení procesoru) napříč všemi aktuálně nasazenými instancemi služby.  Mechanismus se aktivuje, když monitorovaná metrika klesne pod spodní prahovou hodnotu nebo se zvýší nad horní prahovou hodnotu.  

Mechanismus škálování definuje, jak provést operaci škálování při aktivaci zásady.  Zadejte druh mechanismu (Přidat nebo odebrat repliku), minimální a maximální počet replik (jako celá čísla).  Počet replik služby nebude nikdy zvětšen pod minimálním počtem nebo nad maximálním počtem.  Také určete přírůstek měřítka jako celé číslo, což je počet replik, které budou přidány nebo odebrány v rámci operace škálování.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Definování zásady automatického škálování v šabloně JSON

Následující příklad ukazuje zásadu automatického škálování v šabloně nasazení JSON.  Zásada automatického škálování je deklarovaná ve vlastnosti služby, která se má škálovat.  V tomto příkladu je definována Průměrná zátěžová aktivační událost procesoru.  Mechanismus se aktivuje, pokud průměrné zatížení procesoru všech nasazených instancí klesne pod 0,2 (20%). nebo se překročí 0,8 (80%).  Zatížení procesoru se kontroluje každých 60 sekund.  Mechanismus škálování se definuje tak, aby se přidaly nebo odebraly instance, pokud se zásady aktivují.  Instance služby se přidají nebo odeberou v přírůstcích po jednom.  Je také definován minimální počet instancí a maximální počet instancí 40.

```json
{
"apiVersion": "2018-09-01-preview",
"name": "WorkerApp",
"type": "Microsoft.ServiceFabricMesh/applications",
"location": "[parameters('location')]",
"dependsOn": [
"Microsoft.ServiceFabricMesh/networks/worker-app-network"
],
"properties": {
"services": [   
    { ... },       
    {
    "name": "WorkerService",
    "properties": {
        "description": "Worker Service",
        "osType": "linux",
        "codePackages": [
        {  ...              }
        ],
        "replicaCount": 1,
        "autoScalingPolicies": [
        {
            "name": "AutoScaleWorkerRule",
            "trigger": {
                "kind": "AverageLoad",
                "metric": {
                    "kind": "Resource",
                    "name": "cpu"
                },
                "lowerLoadThreshold": "0.2",
                "upperLoadThreshold": "0.8",
                "scaleIntervalInSeconds": "60"
            },
            "mechanism": {
                "kind": "AddRemoveReplica",
                "minCount": "1",
                "maxCount": "40",
                "scaleIncrement": "1"
            }
        }
        ],        
        ...
    }
    }
]
}
}
```

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Definování zásad automatického škálování v souboru prostředků Service. yaml
Následující příklad ukazuje zásadu automatického škálování v souboru prostředků služby (YAML).  Zásada automatického škálování je deklarovaná jako vlastnost služby, která se má škálovat.  V tomto příkladu je definována Průměrná zátěžová aktivační událost procesoru.  Mechanismus se aktivuje, pokud průměrné zatížení procesoru všech nasazených instancí klesne pod 0,2 (20%). nebo se překročí 0,8 (80%).  Zatížení procesoru se kontroluje každých 60 sekund.  Mechanismus škálování se definuje tak, aby se přidaly nebo odebraly instance, pokud se zásady aktivují.  Instance služby se přidají nebo odeberou v přírůstcích po jednom.  Je také definován minimální počet instancí a maximální počet instancí 40.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: WorkerApp
  properties:
    services:
      - name: WorkerService
        properties:
          description: WorkerService description.
          osType: Linux
          codePackages:
            ...
          replicaCount: 1
          autoScalingPolicies:
            - name: AutoScaleWorkerRule
              trigger:
                kind: AverageLoad
                metric:
                  kind: Resource
                  name: cpu
                lowerLoadThreshold: 0.2
                upperLoadThreshold: 0.8
                scaleIntervalInSeconds: 60
              mechanism:
                kind: AddRemoveReplica
                minCount: 1
                maxCount: 40
                scaleIncrement: 1
          ...
```

## <a name="next-steps"></a>Další kroky
Naučte se, jak [ručně škálovat službu](service-fabric-mesh-tutorial-template-scale-services.md) .
