---
title: Konfigurace spravovaného clusteru Service Fabric (Preview)
description: Přečtěte si, jak nakonfigurovat Service Fabric spravovaný cluster pro automatické upgrady operačního systému, pravidla NSG a další.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 44b1b949fe314231cb44f190c31b53903e47a904
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732628"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Service Fabric možností konfigurace spravovaného clusteru (Preview)

Kromě výběru [Service Fabric SKU spravovaného clusteru](overview-managed-cluster.md#service-fabric-managed-cluster-skus) při vytváření clusteru existuje mnoho dalších způsobů, jak ho nakonfigurovat. V aktuální verzi Preview můžete:

* Konfigurace [možností sítě](how-to-managed-cluster-networking.md) pro cluster
* Přidání [rozšíření sady škálování virtuálního počítače](how-to-managed-cluster-vmss-extension.md) do typu uzlu
* Konfigurace [spravované identity](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) na typech uzlů
* Povolit [automatické upgrady operačního systému](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades) pro vaše uzly
* Povolení [šifrování operačního systému a datového disku](how-to-enable-managed-cluster-disk-encryption.md) na uzlech

## <a name="enable-automatic-os-image-upgrades"></a>Povolit automatické upgrady bitových kopií operačního systému

Můžete povolit automatické upgrady bitových kopií operačního systému na virtuální počítače, na kterých běží spravované uzly clusteru. I když se prostředky sady škálování virtuálních počítačů spravují vaším jménem pomocí Service Fabric spravovaných clusterů, je vaše volba Povolit automatické upgrady imagí operačního systému pro uzly clusteru. Stejně jako u [klasických Service Fabric](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) clusterů se ve výchozím nastavení neupgradují uzly spravovaného clusteru, aby se zabránilo nezamýšleným výpadkům clusteru.

Postup povolení automatického upgradu operačního systému:

* Použijte `2021-01-01-preview` verzi (nebo novější) prostředků *Microsoft. ServiceFabric/Managedclusters* a *Microsoft. ServiceFabric/managedclusters/nodetypes* .
* Nastavte vlastnost clusteru `enableAutoOSUpgrade` na *hodnotu true* .
* Nastavte vlastnost prostředku nodeType clusteru `vmImageVersion` na *nejnovější* .

Například:

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

Po povolení Service Fabric začne dotazovat se na verze imagí operačního systému a sledovat je ve spravovaném clusteru. Pokud je k dispozici nová verze operačního systému, budou v jednom okamžiku upgradovány typy uzlů clusteru (Virtual Machine Scale Sets). Upgrady Service Fabric modulu runtime se provádějí až po potvrzení, že neprobíhá upgrade neupgradování imagí operačního systému uzlu clusteru.

Pokud se upgrade nezdaří, Service Fabric se po 24 hodinách opakovat, a to po dobu maximálně tří pokusů. Podobně jako u klasických (nespravovaných) Service Fabric upgrady, poškozené aplikace nebo uzly můžou blokovat upgrade image operačního systému.

Další informace o upgradech imagí najdete v tématu [automatické upgrady imagí operačního systému pomocí Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

## <a name="next-steps"></a>Další kroky

[Přehled spravovaných clusterů Service Fabric](overview-managed-cluster.md)

[Šablony clusteru Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates)
