---
title: Osvědčené postupy pro vytváření sítí Azure Service Fabric
description: Doporučené postupy a aspekty návrhu pro správu připojení k síti pomocí Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: de2a74ad2d61de18d2150b72be3251e5b5583f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551790"
---
# <a name="networking"></a>Síťové služby

Při vytváření a správě clusterů Azure Service Fabric poskytujete připojení k síti pro vaše uzly a aplikace. Síťové prostředky zahrnují rozsahy IP adres, virtuální sítě, nástroje pro vyrovnávání zatížení a skupiny zabezpečení sítě. V tomto článku se dozvíte osvědčené postupy pro tyto prostředky.

Projděte si síťové vzory Azure Service Fabric, abyste se [dozvěděli,](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) jak vytvořit clustery, které používají následující funkce: Existující virtuální síť nebo podsíť, statická veřejná IP adresa, interní nástroj pro vyrovnávání zatížení nebo interní a externí nástroj pro vyrovnávání zatížení.

## <a name="infrastructure-networking"></a>Síťoviny infrastruktury
Maximalizujte výkon virtuálního počítače pomocí akcelerovaných sítí tím, že deklarujete vlastnost enableAcceleratedNetworking ve vaší šabloně Správce prostředků, následující fragment je virtuálního počítače škálovatelné konfigurace networkinterface, který umožňuje zrychlené vytváření sítí:

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
Cluster Service Fabric lze zřídit na [Linuxu se zrychlenou sítí](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)a [Windows s akcelerační sítí](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

Akcelerované sítě jsou podporované pro skutě sítí Azure řady virtuálních strojů: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 a Ms/Mms. Akcelerovaná síť byla úspěšně testována pomocí Standard_DS8_v3 Skladové ku na 1/ 23 / 2019 pro service fabric Cluster Windows a pomocí Standard_DS12_v2 na 01 /29/2019 pro cluster Service Fabric Linux.

Chcete-li povolit akcelerované sítě v existujícím clusteru Service Fabric, je třeba [nejprve škálovat cluster service fabric přidáním škálovací sady virtuálních strojů](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), abyste provedli následující:
1. Zřízení typu NodeType s povolenou zrychlenou sítí
2. Migrace služeb a jejich stavu do zřízeného typu NodeType s povoleným akcelerovaným připojením k síti

Horizontální navýšení kapacity infrastruktury je nutné povolit akcelerované sítě na existující clusteru, protože povolení akcelerované sítě na místě by způsobit výpadky, protože vyžaduje, aby všechny virtuální počítače v dostupnosti nastavit [zastavit a navrátit před povolením akcelerované sítě na jakékoli existující síťové karty](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Síť clusteru

* Clustery Service Fabric lze nasadit do existující virtuální sítě podle kroků popsaných v [síťových vzorcích Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* Skupiny zabezpečení sítě (NSG) se doporučují pro typy uzlů, které omezují příchozí a odchozí provoz do clusteru. Ujistěte se, že potřebné porty jsou otevřeny v nsg. Například: ![Service Fabric NSG Pravidla][NSGSetup]

* Primární typ uzlu, který obsahuje systémové služby Service Fabric, nemusí být vystaven prostřednictvím externího nástroje pro vyrovnávání zatížení a může být vystaven [interním nástrojem pro vyrovnávání zatížení.](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Pro cluster použijte [statickou veřejnou IP adresu.](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1)

## <a name="application-networking"></a>Síť aplikací

* Chcete-li spustit úlohy kontejnerů systému Windows, usnadněte komunikaci mezi službami pomocí [otevřeného síťového režimu.](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode)

* Použijte reverzní proxy server, jako je [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) nebo [service fabric reverzní proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) vystavit běžné porty aplikace, jako je například 80 nebo 443.

* Pro kontejnery Windows hostované na počítačích se vzduchovými mezerami, které nemohou vytáhnout základní vrstvy z cloudového úložiště Azure, přepište chování cizí vrstvy pomocí příznaku [--allow-nondistributable-artifacts](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) v démonovi Dockeru.

## <a name="next-steps"></a>Další kroky

* Vytvoření clusteru na virtuálních počítačích nebo v počítačích se systémem Windows Server: [Vytvoření clusteru Service Fabric pro Systém Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Vytvoření clusteru na virtuálních počítačích nebo počítačích s [Linuxem: Vytvoření linuxového clusteru](service-fabric-cluster-creation-via-portal.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
