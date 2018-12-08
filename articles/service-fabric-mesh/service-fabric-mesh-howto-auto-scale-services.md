---
title: Automatické škálování, kterým je aplikace spuštěná ve službě Azure Service Fabric mřížky | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat zásady automatického škálování služby Service Fabric mřížky aplikace.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 94b1b4cfbc5e7a96be389f315a1c58dc311c60a0
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104920"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Vytvořit zásady automatického škálování pro aplikaci Service Fabric mřížky
Jednou z hlavních výhod nasazení aplikací do služby Service Fabric Mesh je možnost snadného horizontálního snížení nebo navýšení kapacity služeb. Tuto možnost byste měli používat ke zvládnutí různých objemů zatížení vašich služeb nebo ke zlepšení dostupnosti. Můžete ručně škálovat služby snížení nebo navýšení kapacity nebo nastavení zásad pro automatické škálování.

[Automatické škálování](service-fabric-mesh-scalability.md#autoscaling-service-instances) umožňuje dynamicky škálovat počet instancí služby (horizontální škálování). Automatické škálování poskytuje skvělé pružnost a umožňuje zřizování nebo odebrání instancí služby na základě využití procesoru nebo paměti.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Možnosti pro vytvoření automatického škálování zásad, aktivační události a mechanismus
Automatické škálování zásad je definována pro každou službu, kterou chcete škálovat. Zásady je definována v souboru prostředků služby YAML nebo JSON šablonu nasazení. Každé zásady škálování se skládá ze dvou částí: aktivační události a škálování mechanismus.

Aktivační událost definuje, kdy je vyvolána zásadu automatického škálování.  Zadejte typ aktivační události (průměrné zatížení) a metriku k monitorování (CPU, paměť).  Horní a dolní zatížení prahovými hodnotami určenými v procentech. Interval škálování definuje, jak často (v sekundách) Zkontrolujte zadaný využití (jako je průměrné zatížení CPU) napříč všemi instancemi aktuálně nasazená služba.  Mechanismu, který se aktivuje, když monitorovaných metrika poklesne pod nižší prahovou hodnotu nebo vyšší než horní prahová hodnota se zvyšuje.  

Škálování mechanismu, který definuje, jak škálování operaci provést, pokud zásada se aktivuje.  Určit druh mechanismu (Přidat/odebrat repliky), replika minimální a maximální počty (jako celá čísla).  Počet replik služby bude škálovat nikdy pod minimální počet nebo vyšší než maximální počet.  Zvýšení škálování můžete také zadejte jako celé číslo, což je počet replik, které budou přidány nebo odebrány při operaci škálování.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Definovat automaticky zásady škálování v šabloně JSON

Následující příklad ukazuje zásadu automatického škálování v nasazení šablony JSON.  Zásady automatického škálování je deklarován ve vlastnosti služby škálování.  V tomto příkladu je definován trigger průměrné zatížení CPU.  Mechanismu, který se aktivuje, pokud se průměrné zatížení CPU všechny nasazené instance zhoršení pod 0.2 (20 %) nebo přejde nad 0,8 (80 %).  Zatížení procesoru se kontroluje každých 60 sekund.  Škálování mechanismu, který je definován přidávala nebo odebírala instance, pokud zásada se aktivuje.  Instance služby budou přidány nebo odebrány v přírůstcích po jedné.  Minimální počet instancí jedné, maximální počet instancí 40 je také definován.

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

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Definovat zásady automatického škálování v souboru prostředků service.yaml
Následující příklad ukazuje zásadu automatického škálování v souboru prostředků (YAML) služby.  Zásady automatického škálování je deklarován jako vlastnost službu škálovat.  V tomto příkladu je definován trigger průměrné zatížení CPU.  Mechanismu, který se aktivuje, pokud se průměrné zatížení CPU všechny nasazené instance zhoršení pod 0.2 (20 %) nebo přejde nad 0,8 (80 %).  Zatížení procesoru se kontroluje každých 60 sekund.  Škálování mechanismu, který je definován přidávala nebo odebírala instance, pokud zásada se aktivuje.  Instance služby budou přidány nebo odebrány v přírůstcích po jedné.  Minimální počet instancí jedné, maximální počet instancí 40 je také definován.

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
                metricName: cpu
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

## <a name="next-steps"></a>Další postup
Zjistěte, jak [ruční škálování služby](service-fabric-mesh-tutorial-template-scale-services.md)
