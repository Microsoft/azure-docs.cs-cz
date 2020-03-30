---
title: Automatické škálování aplikace spuštěné v síti Azure Service Fabric Mesh
description: Zjistěte, jak nakonfigurovat zásady automatického škálování pro služby aplikace Service Fabric Mesh.
author: dkkapur
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: fb72806dd7ba838ba7170bda409715bc074e1d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461974"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Vytvoření zásad automatického škálování pro aplikaci Service Fabric Mesh
Jednou z hlavních výhod nasazení aplikací do sítě Service Fabric mesh je možnost snadnéškálování služeb v aplikaci nebo venku. To by mělo být použito pro zpracování různé množství zatížení vašich služeb nebo zlepšení dostupnosti. Můžete ručně škálovat služby v nebo out nebo nastavit zásady automatického škálování.

[Automatické škálování](service-fabric-mesh-scalability.md#autoscaling-service-instances) umožňuje dynamicky škálovat počet instancí služby (horizontální škálování). Automatické škálování poskytuje velkou pružnost a umožňuje zřizování nebo odebrání instancí služby na základě využití procesoru nebo paměti.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Možnosti pro vytvoření zásadautomatického škálování, aktivační události a mechanismu
Zásady automatického škálování jsou definovány pro každou službu, kterou chcete škálovat. Zásada je definována v souboru prostředků služby YAML nebo v šabloně nasazení JSON. Každá zásada škálování se skládá ze dvou částí: aktivační událost a mechanismus škálování.

Aktivační událost definuje, když je vyvolána zásada automatického škálování.  Určete druh aktivační události (průměrné zatížení) a metriku, kterou chcete sledovat (procesor nebo paměť).  Horní a dolní prahy zatížení zadané v procentech. Interval škálování definuje, jak často kontrolovat (v sekundách) zadané využití (například průměrné zatížení procesoru) ve všech aktuálně nasazených instancích služby.  Mechanismus se spustí, když monitorovaná metrika klesne pod dolní prahovou hodnotu nebo se zvýší nad horní prahovou hodnotu.  

Mechanismus škálování definuje, jak provést operaci škálování při aktivaci zásady.  Zadejte druh mechanismu (přidat nebo odebrat repliku), minimální a maximální počet replik (jako celá čísla).  Počet replik služby nikdy nebude škálovat pod minimální počet nebo vyšší než maximální počet.  Také určete přírůstek měřítka jako celé číslo, což je počet replik, které budou přidány nebo odebrány v operaci škálování.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Definování zásad automatického škálování v šabloně JSON

Následující příklad ukazuje zásady automatického škálování v šabloně nasazení JSON.  Zásady automatického škálování je deklarována ve vlastnosti služby, která má být škálována.  V tomto příkladu je definována aktivační událost průměrného zatížení procesoru.  Mechanismus se spustí, pokud průměrné zatížení procesoru všech nasazených instancí klesne pod 0,2 (20 %) nebo jde nad 0,8 (80 %).  Zatížení procesoru je kontrolováno každých 60 sekund.  Mechanismus škálování je definován pro přidání nebo odebrání instancí, pokud je zásada spuštěna.  Instance služby budou přidány nebo odebrány v přírůstcích po jednom.  Minimální počet instancí jeden a maximální počet instancí 40 je také definován.

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

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Definování zásad automatického škálování v souboru prostředků service.yaml
Následující příklad ukazuje zásady automatického škálování v souboru prostředků služby (YAML).  Zásady automatického škálování je deklarována jako vlastnost služby, která má být škálována.  V tomto příkladu je definována aktivační událost průměrného zatížení procesoru.  Mechanismus se spustí, pokud průměrné zatížení procesoru všech nasazených instancí klesne pod 0,2 (20 %) nebo jde nad 0,8 (80 %).  Zatížení procesoru je kontrolováno každých 60 sekund.  Mechanismus škálování je definován pro přidání nebo odebrání instancí, pokud je zásada spuštěna.  Instance služby budou přidány nebo odebrány v přírůstcích po jednom.  Minimální počet instancí jeden a maximální počet instancí 40 je také definován.

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
Přečtěte si, jak [ručně škálovat službu](service-fabric-mesh-tutorial-template-scale-services.md)
