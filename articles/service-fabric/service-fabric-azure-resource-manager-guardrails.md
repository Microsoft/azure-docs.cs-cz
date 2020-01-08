---
title: Service Fabric nasazení Azure Resource Manager guardrails
description: Tento článek poskytuje přehled běžných chyb provedených při nasazení Service Fabric clusteru prostřednictvím Azure Resource Manager a jejich předcházení.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: pepogors
ms.openlocfilehash: fe5ff2a5eeb4b2c73165d1577702eb6af7079b61
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426738"
---
# <a name="service-fabric-guardrails"></a>Service Fabric guardrails 
Při nasazování clusteru Service Fabric jsou zavedeny guardrails, což způsobí selhání nasazení Azure Resource Manager v případě neplatné konfigurace clusteru. V následujících částech najdete přehled běžných potíží s konfigurací clusteru a kroky potřebné k tomu, abyste tyto problémy zmírnili. 

## <a name="durability-mismatch"></a>Neshoda odolnosti
### <a name="overview"></a>Přehled
Hodnota trvanlivosti pro Service Fabric typ uzlu je definována ve dvou různých oddílech Azure Resource Manager šablony. Část rozšíření sady škálování virtuálního počítače v prostředku sady škálování virtuálního počítače a typ uzlu prostředku clusteru Service Fabric. Je nutné, aby hodnota trvanlivosti v těchto oddílech odpovídala, jinak se nasazení prostředků nepovede.

Následující část obsahuje příklad neshodné odolnosti mezi nastavením odolnosti rozšíření sady škálování virtuálního počítače a Service Fabric nastavením odolnosti typu uzlu:  

**Nastavení odolnosti virtuálních počítačů s možností nastavení odolnosti**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Nastavení odolnosti typu Service Fabric uzlu** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>Chybové zprávy
* Neshoda trvanlivosti sady škálování virtuálního počítače neodpovídá aktuální úrovni odolnosti typu Service Fabric uzlu.
* Odolnost sady škálování virtuálního počítače se neshoduje s cílovou Service Fabricou úrovní odolnosti typu uzlu.
* Odolnost sady škálování virtuálního počítače odpovídá aktuální Service Fabric úrovně trvanlivosti nebo cílové Service Fabric typu uzlu. 


### <a name="mitigation"></a>Omezení rizik
Chcete-li opravit neshodu trvanlivosti, která je označena některou z výše uvedených chybových zpráv:
1. Úroveň trvanlivosti aktualizujte buď v části rozšíření sady škálování virtuálního počítače, nebo Service Fabric typu uzlu Azure Resource Manager šablony, aby se zajistilo, že se hodnoty shodují.
2. Znovu nasaďte šablonu Azure Resource Manager s aktualizovanými hodnotami.

## <a name="next-steps"></a>Další kroky
* Vytvoření clusteru na virtuálních počítačích nebo počítačích se systémem Windows Server: [Service Fabric vytvoření clusteru pro Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Vytvoření clusteru na virtuálních počítačích nebo počítačích se systémem Linux: [Vytvoření clusteru se systémem Linux](service-fabric-cluster-creation-via-portal.md)
* Řešení potíží s Service Fabric: [Průvodci odstraňováním potíží](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
