---
title: Service Fabric nasazení Azure Resource Manager guardrails
description: Tento článek poskytuje přehled běžných chyb provedených při nasazení Service Fabric clusteru prostřednictvím Azure Resource Manager a jejich předcházení.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: aa1499c57ead28bfcee90a2f224ef9c3bb1d7f58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86247820"
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


## <a name="seed-node-deletion"></a>Odstranění uzlu počáteční hodnoty 
### <a name="overview"></a>Přehled
Cluster Service Fabric má vlastnost [úrovně spolehlivosti](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) , která se používá k určení počtu replik systémových služeb, které běží na primárním uzlu typu clusteru. Počet požadovaných replik bude určovat minimální počet uzlů, které musí být udržovány v primárním typu uzlu clusteru. Pokud počet uzlů v primárním uzlu překročí požadované minimum pro úroveň spolehlivosti, cluster přestane být stabilní.  

### <a name="error-messages"></a>Chybové zprávy 
Byla zjištěna operace odebrání uzlu počáteční hodnoty a bude odmítnuta. 
* Výsledkem této operace může být {0} , že v clusteru zůstanou jenom potenciální počáteční uzly, které {1} jsou potřeba jako minimum.
* Odebrání {0} počátečních uzlů z {1} důvodu ztráty kvora uzlu dosazení má za následek výpadek clusteru. Maximální počet počátečních uzlů, které lze odebrat současně {2} .
 
### <a name="mitigation"></a>Omezení rizik 
Ujistěte se, že váš typ primárního uzlu má dostatek Virtual Machines pro spolehlivost určenou v clusteru. Virtuální počítač nebudete moct odebrat, pokud by se sada škálování virtuálního počítače nastavila pod minimálním počtem uzlů pro danou úroveň spolehlivosti.
* Pokud je úroveň spolehlivosti správně zadaná, ujistěte se, že je v primárním uzlu typu podle potřeby pro úroveň spolehlivosti k dispozici dostatek uzlů. 
* Pokud je úroveň spolehlivosti nesprávná, zahajte změnu v prostředku Service Fabric tak, aby se před zahájením operací s škálováním sady virtuálních počítačů snížila úroveň spolehlivosti, a počkejte, než se dokončí.
* Pokud je úroveň spolehlivosti bronzová, postupujte podle těchto [kroků](./service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) a proveďte řádné škálování clusteru.

## <a name="next-steps"></a>Další kroky
* Vytvoření clusteru na virtuálních počítačích nebo počítačích se systémem Windows Server: [Service Fabric vytvoření clusteru pro Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Vytvoření clusteru na virtuálních počítačích nebo počítačích se systémem Linux: [Vytvoření clusteru se systémem Linux](service-fabric-cluster-creation-via-portal.md)
* Řešení potíží s Service Fabric: [Průvodci odstraňováním potíží](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
