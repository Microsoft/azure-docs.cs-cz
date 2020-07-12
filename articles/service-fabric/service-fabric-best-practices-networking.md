---
title: Osvědčené postupy pro službu Azure Service Fabric Networking
description: Osvědčené postupy a pokyny k návrhu pro správu připojení k síti pomocí Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 853e53d32f87f81e5db587de2654f83037930da7
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261135"
---
# <a name="networking"></a>Sítě

Při vytváření a správě clusterů Azure Service Fabric zajišťujete připojení k síti pro vaše uzly a aplikace. Síťové prostředky zahrnují rozsahy IP adres, virtuální sítě, nástroje pro vyrovnávání zatížení a skupiny zabezpečení sítě. V tomto článku se seznámíte s osvědčenými postupy pro tyto prostředky.

Přečtěte si o [vzorcích sítě Azure Service Fabric](./service-fabric-patterns-networking.md) a Naučte se vytvářet clustery, které používají následující funkce: existující virtuální síť nebo podsíť, statická veřejná IP adresa, nástroj pro vyrovnávání zatížení s interním a externím a externím nástrojem pro vyrovnávání zatížení.

## <a name="infrastructure-networking"></a>Sítě infrastruktury
Vyvoláním vlastnosti enableAcceleratedNetworking ve vaší Správce prostředků šabloně maximalizujete výkon svého virtuálního počítače pomocí akcelerované sítě. Tento fragment kódu je NetworkInterfaceConfigurations sady virtuálních počítačů, který umožňuje akcelerované síťové služby:

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
Cluster Service Fabric můžete zřídit v systému [Linux s akcelerovanými síťovými](../virtual-network/create-vm-accelerated-networking-cli.md)službami a [s využitím akcelerovaných sítí](../virtual-network/create-vm-accelerated-networking-powershell.md).

Akcelerované sítě se podporují pro skladové položky řady virtuálních počítačů Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 a MS/MMS. Akcelerované síťové služby byly úspěšně testovány pomocí Standard_DS8_v3 SKU v 1/23/2019 pro cluster Service Fabric Windows a použití Standard_DS12_v2 na 01/29/2019 pro cluster Service Fabric Linux.

Pokud chcete povolit akcelerované síťové služby v existujícím clusteru Service Fabric, musíte nejdřív [škálovat Service Fabric clusteru tím, že přidáte sadu škálování virtuálního počítače](./virtual-machine-scale-set-scale-node-type-scale-out.md), abyste provedli následující akce:
1. Zřízení NodeType s povolenými akcelerovanými síťovými službami
2. Migrujte své služby a jejich stav na zřízený NodeType s povolenými akcelerovanými síťovými službami.

Aby bylo možné zrychlit síťové služby v existujícím clusteru, je potřeba škálovat infrastrukturu, protože povolení akcelerovaných síťových služeb by způsobilo výpadky, protože vyžaduje, aby všechny virtuální počítače ve skupině dostupnosti byly [zastaveny a navráceny před povolením akcelerovaných sítí na jakémkoli existujícím síťovém adaptéru](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Sítě clusteru

* Clustery Service Fabric můžete nasadit do existující virtuální sítě podle postupu popsaného v článku [vzory Service Fabric sítě](./service-fabric-patterns-networking.md).

* Skupiny zabezpečení sítě (skupin zabezpečení sítě) se doporučují pro typy uzlů, které omezují příchozí a odchozí provoz do jejich clusteru. Ujistěte se, že jsou v NSG otevřené potřebné porty. Příklad: ![ Service Fabric NSG pravidla][NSGSetup]

* Typ primárního uzlu, který obsahuje Service Fabric systémové služby, nemusí být vystavený přes externí nástroj pro vyrovnávání zatížení a může být vystavený [interním nástrojem pro vyrovnávání zatížení](./service-fabric-patterns-networking.md#internal-only-load-balancer) .

* Použijte pro svůj cluster [statickou veřejnou IP adresu](./service-fabric-patterns-networking.md#static-public-ip-address-1) .

## <a name="application-networking"></a>Aplikační síť

* Pokud chcete spouštět úlohy kontejnerů Windows, použijte [režim otevřené sítě](./service-fabric-networking-modes.md#set-up-open-networking-mode) a usnadněte si tak komunikaci mezi službami.

* K vystavování běžných aplikačních portů, jako je například 80 nebo 443, použijte reverzní proxy server, jako je [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) nebo [reverzní proxy Service Fabric](./service-fabric-reverseproxy.md) .

* Pro kontejnery Windows hostované na vzduchem gapped počítače, které nemůžou získat základní vrstvy z cloudového úložiště Azure, přepište chování cizí vrstvy pomocí příznaku [--Allow-undistribuovatelný-artefakts](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) v Docker démon.

## <a name="next-steps"></a>Další kroky

* Vytvoření clusteru na virtuálních počítačích nebo počítačích se systémem Windows Server: [Service Fabric vytvoření clusteru pro Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Vytvoření clusteru na virtuálních počítačích nebo počítačích se systémem Linux: [Vytvoření clusteru se systémem Linux](service-fabric-cluster-creation-via-portal.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
