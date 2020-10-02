---
title: Osvědčené postupy pro službu Azure Service Fabric Networking
description: Pravidla a faktory návrhu pro správu připojení k síti pomocí Azure Service Fabric.
author: chrpap
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: chrpap
ms.openlocfilehash: b8db69792b31fd82646757423e669e39e8539d06
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630698"
---
# <a name="networking"></a>Sítě

Při vytváření a správě clusterů Azure Service Fabric zajišťujete připojení k síti pro vaše uzly a aplikace. Síťové prostředky zahrnují rozsahy IP adres, virtuální sítě, nástroje pro vyrovnávání zatížení a skupiny zabezpečení sítě. V tomto článku se seznámíte s osvědčenými postupy pro tyto prostředky.

Přečtěte si o [vzorcích sítě Azure Service Fabric](./service-fabric-patterns-networking.md) a Naučte se vytvářet clustery, které používají následující funkce: existující virtuální síť nebo podsíť, statická veřejná IP adresa, nástroj pro vyrovnávání zatížení s interním a externím a externím nástrojem pro vyrovnávání zatížení.

## <a name="infrastructure-networking"></a>Sítě infrastruktury
Vyvoláním vlastnosti *enableAcceleratedNetworking* ve vaší správce prostředků šabloně maximalizujete výkon svého virtuálního počítače pomocí akcelerované sítě. Tento fragment kódu je NetworkInterfaceConfigurations sady virtuálních počítačů, který umožňuje akcelerované síťové služby:

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

Akcelerované sítě se podporují pro skladové položky řady virtuálních počítačů Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 a MS/MMS. Akcelerované síťové služby byly úspěšně testovány pomocí Standard_DS8_v3 SKU v 01/23/2019 pro cluster Service Fabric Windows a použití Standard_DS12_v2 na 01/29/2019 pro cluster Service Fabric Linux.

Pokud chcete povolit akcelerované síťové služby v existujícím clusteru Service Fabric, musíte nejdřív [škálovat Service Fabric clusteru tím, že přidáte sadu škálování virtuálního počítače](./virtual-machine-scale-set-scale-node-type-scale-out.md), abyste provedli následující akce:
1. Zřízení NodeType s povolenými akcelerovanými síťovými službami
2. Migrujte své služby a jejich stav na zřízený NodeType s povolenými akcelerovanými síťovými službami.

Aby bylo možné zrychlit síťové služby v existujícím clusteru, je potřeba škálovat infrastrukturu, protože povolení akcelerovaných síťových služeb by způsobilo výpadky, protože vyžaduje, aby všechny virtuální počítače ve skupině dostupnosti byly [zastaveny a navráceny před povolením akcelerovaných sítí na jakémkoli existujícím síťovém adaptéru](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Sítě clusteru

* Clustery Service Fabric můžete nasadit do existující virtuální sítě podle postupu popsaného v článku [vzory Service Fabric sítě](./service-fabric-patterns-networking.md).

* Skupiny zabezpečení sítě (skupin zabezpečení sítě) se doporučují pro typy uzlů, které omezují příchozí a odchozí provoz do svého clusteru. Ujistěte se, že jsou v NSG otevřené potřebné porty. 

* Typ primárního uzlu, který obsahuje Service Fabric systémové služby, nemusí být vystavený přes externí nástroj pro vyrovnávání zatížení a může být vystavený [interním nástrojem pro vyrovnávání zatížení](./service-fabric-patterns-networking.md#internal-only-load-balancer) .

* Použijte pro svůj cluster [statickou veřejnou IP adresu](./service-fabric-patterns-networking.md#static-public-ip-address-1) .

## <a name="network-security-rules"></a>Pravidla zabezpečení sítě

Základní pravidla jsou minimální pro uzamčení zabezpečení clusteru spravovaného Service Fabric Azure. Nepodaří-li se otevřít následující porty nebo schválit IP adresu nebo adresu URL, zabráníte správnému fungování clusteru a nemusí být podporované. Pomocí této sady pravidel je naprosto potřeba použít [automatické upgrady imagí operačního systému](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md), jinak bude potřeba otevřít další porty.

### <a name="inbound"></a>Příchozí 
|Priorita   |Název               |Port        |Protokol  |Zdroj             |Cíl       |Akce   
|---        |---                |---         |---       |---                |---               |---
|3900       |Azure              |19080       |TCP       |Internet           |VirtualNetwork    |Povolit
|3910       |Klient             |19000       |TCP       |Internet           |VirtualNetwork    |Povolit
|3920       |Cluster            |1025-1027   |TCP       |VirtualNetwork     |VirtualNetwork    |Povolit
|3930       |Dočasné          |49152-65534 |TCP       |VirtualNetwork     |VirtualNetwork    |Povolit
|3940       |Aplikace        |20000-30000 |TCP       |VirtualNetwork     |VirtualNetwork    |Povolit
|3950       |SMB                |445         |TCP       |VirtualNetwork     |VirtualNetwork    |Povolit
|3960       |Protokol RDP                |3389-3488   |TCP       |Internet           |VirtualNetwork    |Odepřít
|3970       |SSH                |22          |TCP       |Internet           |VirtualNetwork    |Odepřít
|3980       |Vlastní koncový bod    |80          |TCP       |Internet           |VirtualNetwork    |Povolit
|4100       |Blokovat příchozí      |443         |Všechny       |Všechny                |Všechny               |Povolit

Další informace o příchozích pravidlech zabezpečení:

* **Azure**. Tento port používá Service Fabric Explorer k procházení a správě clusteru a používá ho poskytovatel prostředků Service Fabric k dotazování na informace o vašem clusteru, aby se dala zobrazit v Azure Portál pro správu. Pokud tento port není dostupný od poskytovatele prostředků Service Fabric pak zobrazí se v Azure Portal zpráva, například uzly nenalezeny nebo UpgradeServiceNotReachable, a seznam uzlů a aplikací se zobrazí jako prázdný. To znamená, že pokud si přejete mít přehled o clusteru v Azure Portál pro správu pak váš nástroj pro vyrovnávání zatížení musí vystavit veřejnou IP adresu a služba NSG musí umožňovat příchozí přenos 19080.  

* **Klient**. Koncový bod připojení klienta pro rozhraní API, jako je REST/PowerShell/CLI. 

* **Cluster**. Používá se pro komunikaci mezi uzly; by nikdy neměl být zablokovaný.

* **Dočasné**. Service Fabric používá součást těchto portů jako aplikační porty a zbývající jsou k dispozici pro operační systém. Tento rozsah je také namapován na stávající rozsah, který je přítomný v operačním systému, takže pro všechny účely můžete použít rozsahy uvedené v ukázce. Zajistěte, aby byl rozdíl mezi počátečním a koncovým portem minimálně 255. V případě, že je tento rozdíl příliš nízký, můžete spustit konflikty, protože tento rozsah je sdílen s operačním systémem. Pokud chcete zobrazit nakonfigurovaný rozsah dynamických portů, spusťte *příkaz netsh int ipv4 Zobrazit dynamický port TCP*. Tyto porty nejsou pro clustery se systémem Linux potřeba.

* **Aplikace**. Rozsah portů aplikace by měl být dostatečně velký, aby pokryl požadavky vašich aplikací na koncový bod. Tento rozsah by měl být exkluzivní z rozsahu dynamických portů v počítači, to znamená ephemeralPorts rozsah nastavený v konfiguraci. Service Fabric tyto porty používá, kdykoli jsou vyžadovány nové porty a postará se o otevření brány firewall pro tyto porty na uzlech.

* Protokol **SMB**. Protokol SMB používá služba úložiště bitových kopií pro dva scénáře. Tento port je potřeba ke stažení balíčků z úložiště bitových kopií pomocí uzlů i k jejich replikaci mezi replikami. 

* Protokol **RDP**. Volitelné, pokud se vyžaduje protokol RDP z Internetu nebo VirtualNetwork pro scénáře JumpBox. 

* **SSH**. Volitelné, pokud je vyžadován SSH z Internetu nebo VirtualNetwork pro scénáře JumpBox.

* **Vlastní koncový bod**. Příklad vaší aplikace pro povolení koncového bodu, který je přístupný pro Internet

### <a name="outbound"></a>Odchozí

|Priorita   |Název               |Port        |Protokol  |Zdroj             |Cíl       |Akce   
|---        |---                |---         |---       |---                |---               |---
|3900       |Síť            |Všechny         |TCP       |VirtualNetwork     |VirtualNetwork    |Povolit
|3910       |Poskytovatel prostředků  |443         |TCP       |VirtualNetwork     |ServiceFabric     |Povolit
|3920       |Upgrade            |443         |TCP       |VirtualNetwork     |Internet          |Povolit
|3950       |Blokovat odchozí     |Všechny         |Všechny       |Všechny                |Všechny               |Odepřít

Další informace o odchozích pravidlech zabezpečení:

* **Síť**. Komunikační kanál pro podsítě a jiné virtuální sítě.

* **Poskytovatel prostředků**. Připojení pomocí UpgradeService ke spuštění všech nasazení ARM poskytovatelem prostředků Service Fabric.

* **Upgrade**. Služba upgradu s použitím adresy download.microsoft.com k získání bitů, to je nutné pro upgrady instalačního programu, opětovného vytvoření bitové kopie a za běhu. Služba funguje s dynamickými IP adresami. Ve scénáři "interního" nástroje pro vyrovnávání zatížení musí být do šablony přidán další externí nástroj pro vyrovnávání zatížení s pravidlem, které povoluje odchozí přenosy pro port 443. Volitelně můžete tento port po úspěšné instalaci zablokovat, ale v takovém případě se balíček s upgradem musí distribuovat do uzlů nebo se má port otevřít po krátkou dobu, potom se vyžaduje ruční upgrade.

Pomocí Azure Firewall s [protokolem toku NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) a [analýzou provozu](../network-watcher/traffic-analytics.md) můžete sledovat problémy s uzamčením zabezpečení. Dobrým příkladem pro spuštění je šablona ARM [Service Fabric NSG](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) . 


## <a name="application-networking"></a>Aplikační síť

* Pokud chcete spouštět úlohy kontejnerů Windows, použijte [režim otevřené sítě](./service-fabric-networking-modes.md#set-up-open-networking-mode) a usnadněte si tak komunikaci mezi službami.

* K vystavování běžných aplikačních portů, jako je například 80 nebo 443, použijte reverzní proxy server, jako je [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) nebo [reverzní proxy Service Fabric](./service-fabric-reverseproxy.md) .

* Pro kontejnery Windows hostované na vzduchem gapped počítače, které nemůžou získat základní vrstvy z cloudového úložiště Azure, přepište chování cizí vrstvy pomocí příznaku [--Allow-undistribuovatelný-artefakts](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) v Docker démon.

## <a name="next-steps"></a>Další kroky

* Vytvoření clusteru na virtuálních počítačích nebo počítačích se systémem Windows Server: [Service Fabric vytvoření clusteru pro Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Vytvoření clusteru na virtuálních počítačích nebo počítačích se systémem Linux: [Vytvoření clusteru se systémem Linux](service-fabric-cluster-creation-via-portal.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
