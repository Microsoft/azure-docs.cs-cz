---
title: Azure Service Fabric sítě osvědčené postupy | Dokumentace Microsoftu
description: Doporučené postupy pro správu sítě Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: d221b828624e649a0d04a89c4394fe5a7fa857dd
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237326"
---
# <a name="networking"></a>Sítě

Jak vytvořit a spravovat clustery Azure Service Fabric, tím připojení k síti pro uzly a aplikace. Síťové prostředky patří rozsahy adres IP, virtuální sítě, nástroje pro vyrovnávání zatížení a skupiny zabezpečení sítě. V tomto článku se dozvíte, osvědčené postupy pro tyto prostředky.

Projděte si Azure [Service Fabric sítě vzory](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) se naučíte vytvářet clustery, které používají následující funkce: Existující virtuální síť nebo podsíť, statickou veřejnou IP adresu, nástroje pro vyrovnávání zatížení jenom pro interní, nebo interní a externí nástroj pro vyrovnávání zatížení.

## <a name="infrastructure-networking"></a>Síťové infrastruktury
Maximalizovat výkon virtuálního počítače s Akcelerovanými síťovými službami, deklarováním enableAcceleratedNetworking vlastnosti v šabloně Resource Manageru následující fragment kódu je virtuálního počítače Škálovací nastavení NetworkInterfaceConfigurations, který Umožňuje Akcelerovanými síťovými službami:

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
Cluster Service Fabric je možné zřídit na [Linux s Akcelerovanými síťovými službami](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli), a [Windows s Akcelerovanými síťovými službami](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

Akcelerované síťové služby se podporuje pro skladové položky řady virtuálních počítačů Azure: D/DSv2, D/DSv3, E nebo ESv3, F/FS, FSv2 a Ms a Mms. Akcelerované síťové služby byl testován úspěšně Standard_DS8_v3 SKU 1/23/2019 pro Service Fabric Windows Cluster a pomocí Standard_DS12_v2 01/29 nebo 2019 pro Linux clusteru Service Fabric.

Povolit Akcelerovanými síťovými službami v existujícím clusteru Service Fabric, musíte si nejdřív [škálovat cluster Service Fabric out tak, že přidáte Škálovací sady virtuálních počítačů](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), proveďte následující kroky:
1. Zřízení NodeType s Akcelerovanými síťovými službami povoleno
2. Migrace služeb a jejich stav na zřízené NodeType s Akcelerovanými síťovými službami povoleno

Horizontální navýšení kapacity infrastruktury je potřeba povolit Akcelerovanými síťovými službami v existujícím clusteru, protože povoluje Akcelerovanými síťovými službami v místě by způsobilo výpadky, protože se všechny virtuální počítače ve skupině dostupnosti vyžaduje [zastavit a uvolnit před zapnutím akcelerovaných síťových službách na jakékoli existující síťovou kartu](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Síť s clustery

* Clustery Service Fabric je možné nasadit do existující virtuální sítě pomocí kroků uvedených v [Service Fabric sítě vzory](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* Skupiny zabezpečení sítě (Nsg) se doporučují pro typy uzlů, které omezují příchozí a odchozí provoz do svého clusteru. Ujistěte se, že jsou otevřené nezbytné porty v této skupině. Příklad: ![Pravidla NSG Service Fabric][NSGSetup]

* Primární typ uzlu, který obsahuje systémové služby Service Fabric není potřeba zpřístupnit prostřednictvím nástroje pro vyrovnávání zatížení externí a můžou být vystavené [interního nástroje load balancer](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Použití [statickou veřejnou IP adresu](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) pro váš cluster.

## <a name="application-networking"></a>Aplikace sítě

* Ke spuštění úlohy kontejneru Windows, použijte [otevřete síťové režim](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) usnadnění komunikace service-to-service.

* Použít reverzní proxy server, jako [Traefik](https://docs.traefik.io/configuration/backends/servicefabric/) nebo [Service Fabric reverzní proxy server](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) vystavit běžné porty aplikací, například 80 nebo 443.

* Pro kontejnery Windows hostované na kruhová s mezerou air počítače, které není možné si vyžádat základní vrstvy z cloudu Azure storage, přepsat chování cizí vrstvy pomocí [– povolit artefakty nondistributable](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) příznak v démona Dockeru.

## <a name="next-steps"></a>Další postup

* Vytvoření clusteru na virtuální počítače nebo počítače se systémem Windows Server: [Vytvoření clusteru Service Fabric pro Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Vytvoření clusteru na počítače s Linuxem nebo virtuální počítače: [Vytvoření clusteru s Linuxem](service-fabric-cluster-creation-via-portal.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
