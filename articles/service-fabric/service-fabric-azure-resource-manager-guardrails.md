---
title: Svodidla nasazení Azure Resource Manageru Service Fabric
description: Tento článek obsahuje přehled běžných chyb provedených při nasazování clusteru Service Fabric prostřednictvím Správce prostředků Azure a jak se jim vyhnout.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: a61b0cf30ca46eb77837eb09d6a9a0b6f30e89a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368578"
---
# <a name="service-fabric-guardrails"></a>Service Fabric svodidla 
Při nasazování clusteru Service Fabric jsou zavedena svodidla, která se nezdaří nasazení Azure Resource Manager v případě neplatné konfigurace clusteru. Následující části poskytují přehled běžných problémů s konfigurací clusteru a kroků potřebných ke zmírnění těchto problémů. 

## <a name="durability-mismatch"></a>Nesoulad s odolností
### <a name="overview"></a>Přehled
Hodnota odolnosti pro typ uzlu Service Fabric je definována ve dvou různých částech šablony Azure Resource Manager. Část rozšíření Škálovací sada virtuálního počítače prostředku škálovací sady virtuálních strojů a část Typ uzlu prostředku clusteru Service Fabric. Je požadavkem, aby hodnota odolnosti v těchto částech odpovídala, jinak se nasazení prostředků nezdaří.

Následující část obsahuje příklad neshody odolnosti mezi nastavením odolnosti rozšíření sady velikosti virtuálního počítače a nastavením odolnosti typu uzlu service fabric:  

**Nastavení odolnosti sady velikosti virtuálního počítače**
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

**Nastavení odolnosti typu uzlu Service Fabric** 
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
* Neshoda odolnosti sady virtuálních strojů neodpovídá aktuální úrovni odolnosti typu service fabric
* Odolnost sady velikosti virtuálního počítače neodpovídá cílové úrovni odolnosti typu typu service fabric
* Životnost sady velikosti virtuálního počítače odpovídá aktuální úrovni odolnosti service fabric nebo cílové úrovni odolnosti typu service fabric 

### <a name="mitigation"></a>Omezení rizik
Chcete-li opravit nesoulad odolnosti, který je označen některou z výše uvedených chybových zpráv:
1. Aktualizujte úroveň odolnosti v rozšíření sady škálování virtuálních strojů nebo v části Typ uzlu service fabric šablony Azure Resource Manager, abyste zajistili, že se hodnoty shodují.
2. Znovu nasaďte šablonu Azure Resource Manager s aktualizovanými hodnotami.


## <a name="seed-node-deletion"></a>Odstranění uzlu osiva 
### <a name="overview"></a>Přehled
Cluster Service Fabric má vlastnost [úrovně spolehlivosti,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) která se používá k určení počtu replik systémových služeb spuštěných na typu primárního uzlu clusteru. Počet požadovaných replik určí minimální počet uzlů, které musí být udržovány v typu primárního uzlu clusteru. Pokud počet uzlů v typu primárního uzlu klesne pod požadované minimum pro úroveň spolehlivosti, cluster se stane nestabilním.  

### <a name="error-messages"></a>Chybové zprávy 
Operace odebrání osiva uzlu byla zjištěna a bude odmítnuta. 
* Tato operace by {0} měla za následek pouze potenciální uzly, které zůstanou v clusteru, zatímco {1} jsou potřeba minimálně.
* Odebrání {0} uzly osiva by {1} mělo za následek clusteru bude dolů z důvodu ztráty kvora uzlu osiva. Maximální počet semen uzlů, které lze odebrat {2}najednou, je .
 
### <a name="mitigation"></a>Omezení rizik 
Ujistěte se, že váš primární typ uzlu má dostatek virtuálních počítačů pro spolehlivost zadanou v clusteru. Virtuální počítač nebude moct odebrat, pokud se velikostvirtuálního počítače sníží pod minimální počet uzlů pro danou úroveň spolehlivosti.
* Pokud je úroveň spolehlivosti správně zadána, ujistěte se, že máte dostatek uzlů v typu primárního uzlu podle potřeby pro úroveň spolehlivosti. 
* Pokud je úroveň spolehlivosti nesprávná, iniciujte změnu prostředku Service Fabric, abyste nejprve snížili úroveň spolehlivosti před zahájením všech operací sady škálování virtuálního počítače a počkejte na dokončení.
* Pokud je úroveň spolehlivosti Bronzová, postupujte podle těchto [kroků](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) a elegantně zmenšete cluster.

## <a name="next-steps"></a>Další kroky
* Vytvoření clusteru na virtuálních počítačích nebo v počítačích se systémem Windows Server: [Vytvoření clusteru Service Fabric pro Systém Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Vytvoření clusteru na virtuálních počítačích nebo počítačích s [Linuxem: Vytvoření linuxového clusteru](service-fabric-cluster-creation-via-portal.md)
* Poradce při potížích s prostředků na řešení potíží: [Příručky pro řešení potíží](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
