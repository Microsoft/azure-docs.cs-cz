---
title: Nasazení síťových kontejnerů v síti Azure Virtual Network | Microsoft Docs
description: Naučte se nasazovat v rozhraní Azure Virtual Network modul plug-in kontejnerového rozhraní CNI (container network interface) pro clustery Kubernetes, které nasadíte sami nebo pomocí modulu ACS-Engine, a pro kontejnery Docker.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/18/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 62d19432cba431bce4485aaa2af3e0a23ad8b5f6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970970"
---
# <a name="deploy-the-azure-virtual-network-container-network-interface-plug-in"></a>Nasazení modulu plug-in rozhraní CNI sítě Azure Virtual Network

Modul plug-in rozhraní CNI sítě Azure Virtual Network se instaluje na virtuální počítač Azure a přináší do kontejnerů a Kubernetes Pod a Docker možnosti virtuální sítě. Další informace o modulu plug-in najdete v tématu o [povolení využití funkcí služby Azure Virtual Network v kontejnerech](container-networking-overview.md). Navíc se dá plug-in použít se službou Azure Kubernetes Service (AKS). Stačí vybrat možnost [Advanced Networking](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Pokročilé síťové funkce), která automaticky umístí kontejnery AKS do virtuální sítě.

## <a name="deploy-plug-in-for-acs-engine-kubernetes-cluster"></a>Nasazení modulu plug-in pro cluster Kubernetes ACS-Engine

V modulu ACS-Engine se cluster Kubernetes nasadí pomocí šablony Azure Resource Manageru. Konfigurace clusteru se zadává v souboru JSON, který se předá nástroji při generování šablony. Podrobnosti k úplnému výčtu podporovaných nastavení clusteru včetně jejich popisu najdete v tématu [Modul služby Microsoft Azure Container Service – definice clusteru](https://github.com/Azure/acs-engine/blob/master/docs/clusterdefinition.md). Modul plug-in je výchozí síťový plug-in pro clustery vytvářené pomocí modulu ACS-Engine. Při konfiguraci modulu plug-in jsou důležitá následující nastavení konfigurace sítě:

  | Nastavení                              | Popis                                                                                                           |
  |--------------------------------------|------------------------------------------------------------------------------------------------------                 |
  | firstConsecutiveStaticIP             | IP adresa přidělená k hlavnímu uzlu. Jedná se o povinné nastavení.                                     |
  | clusterSubnet v části kubernetesConfig | Blok CIDR podsítě virtuální sítě, kde je cluster nasazen a odkud se přidělují IP adresy jednotlivým kontejnerům Pod.   |
  | vnetSubnetId v části masterProfile     | Určuje ID prostředku podsítě v Azure Resource Manageru, ve kterém se má cluster nasadit.                    |
  | vnetCidr                             | Blok CIDR virtuální sítě, kde je cluster nasazen.                                                             |
  | max-Pods v části kubeletConfig         | Maximální počet kontejnerů Pod na každý virtuální počítač agenta. Pro modul plug-in je výchozí hodnota 30. Můžete zadat limit až 250 kontejnerů.  |

### <a name="example-configuration"></a>Příklad konfigurace

Následující příklad v jazyce json se týká clusteru s následujícími vlastnostmi:
-   Má 1 hlavní uzel a 2 agentské uzly. 
-   Je nasazen v podsíti s názvem *KubeClusterSubnet* (10.0.0.0/20), kde se nachází hlavní uzel i agentské uzly.

```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
        "clusterSubnet": "10.0.0.0/20" --> Subnet allocated for the cluster
      }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "ACSKubeMaster",
      "vmSize": "Standard_A2",
      "vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<Vnet Name>/subnets/KubeClusterSubnet",
      "firstConsecutiveStaticIP": "10.0.1.50", --> IP address allocated to the Master node
"vnetCidr": "10.0.0.0/16" --> Virtual network address space
    },
    "agentPoolProfiles": [
      {
        "name": "k8sagentpoo1",
        "count": 2,
        "vmSize": "Standard_A2_v2",
"vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<VNet Name>/subnets/KubeClusterSubnet",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
    "linuxProfile": {
      "adminUsername": "KubeServerAdmin",
      "ssh": {
        "publicKeys": [
          {…}
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "dd438987-aa12-4754-b47d-375811889714",
      "secret": "azure123"
    }
  }
}
```

## <a name="deploy-plug-in-for-a-kubernetes-cluster"></a>Nasazení modulu plug-in pro cluster Kubernetes

Pokud chcete nainstalovat modul plug-in na všechny virtuální počítače Azure v clusteru Kubernetes, postupujte takto:

1. [Stáhněte a nainstalujte modul plug-in](#download-and-install-the-plug-in).
2. Předem přidělte každému virtuálnímu počítači fond IP adres virtuální sítě, ze kterého se budou přiřazovat IP adresy jednotlivým kontejnerům Pod. Každý virtuální počítač Azure má na každém síťovém rozhraní primární privátní IP adresu virtuální sítě. IP adresy z fondu pro kontejnery Pods se přidávají jako sekundární adresy (*ipconfigs*) v síťovém rozhraní virtuálních počítačům a to pomocí jedné z následujících možností:

   - **CLI**: [Přiřazení více IP adres pomocí Azure CLI](virtual-network-multiple-ip-addresses-cli.md)
   - **PowerShell**: [Přiřazení více IP adres v prostředí PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
   - **Portál**: [Přiřazení více IP adres na webu Azure Portal](virtual-network-multiple-ip-addresses-portal.md)
   - **Šablona Azure Resource Manageru**: [Přiřazení více IP adres pomocí šablon](virtual-network-multiple-ip-addresses-template.md)

   Přidejte dostatek IP adres pro všechny kontejnery Pod, které chcete na virtuální počítač umístit.

3. Při vytváření clusteru předejte Kubeletu možnost příkazového řádku `–network-plugin=cni` a vyberte tak modul plug-in, který bude vašemu clusteru poskytovat síťové funkce. Prostředí Kubernetes ve výchozím nastavení hledá modul plug-in a konfigurační soubor v adresářích, kde jsou již nainstalovány.
4. Pokud chcete kontejnerům Pod zajistit přístup k internetu, přidejte na své virtuální počítače s Linuxem následující pravidlo *iptables*, které zajistí určení zdroje překladu síťových adres (NAT) internetového provozu. V následujícím příkladu je zadaný rozsah IP adres 10.0.0.0/8.

   ```bash
   iptables -t nat -A POSTROUTING -m iprange ! --dst-range 168.63.129.16 -m
   addrtype ! --dst-type local ! -d 10.0.0.0/8 -j MASQUERADE
   ```

   Pravidla provozu NAT, který nesměruje do zadaných rozsahů IP adres. Předpokladem je, že veškerý provoz mimo předchozí rozsahy je internetový provoz. Můžete se rozhodnout zadat rozsahy IP adres virtuální sítě virtuálního počítače, partnerských vztahu virtuálních sítí nebo místních sítí.

  Virtuální počítače s Windows automaticky určují zdroj provozu NAT, který má cíl mimo podsíť, kam virtuální počítač patří. Není možné zadávat vlastní rozsahy IP adres.

Po dokončení předchozích kroků se kontejnerům Pod aktivovaným na virtuálních počítačích agenta Kubernetes automaticky přiřadí privátní IP adresy z virtuální sítě.

## <a name="deploy-plug-in-for-docker-containers"></a>Nasazení modulu plug-in pro kontejnery Docker

1. [Stáhněte a nainstalujte modul plug-in](#download-and-install-the-plug-in).
2. Pomocí následujícího příkazu vytvořte kontejnery Docker:

   ```
   ./docker-run.sh \<container-name\> \<container-namespace\> \<image\>
   ```

Kontejnery začnou automaticky přijímat IP adresy z přiděleného fondu. Pokud chcete u kontejnerů Docker vyrovnávat zatížení, musí být kontejnery umístěny za nástroj pro softwarové vyrovnávání zatížení, u kterého je potřeba nakonfigurovat test paměti stejně, jako vytváříte zásady a testy u virtuálního počítače.

### <a name="cni-network-configuration-file"></a>Konfigurační soubor sítě CNI

Konfigurační soubor sítě CNI je popsán ve formátu JSON. Ve výchozím nastavení je pro Linux k dispozici v adresáři `/etc/cni/net.d` a pro Windows v adresáři `c:\cni\netconf`. Soubor určuje konfiguraci modulu plug-in a v systémech Windows a Linux se liší. Následující kód json představuje ukázkový konfigurační soubor pro Linux, poté následuje vysvětlení některých důležitých nastavení. V souboru není nutné provádět žádné změny:

```json
{
       "cniVersion":"0.3.0",
       "name":"azure",
       "plugins":[
          {
             "type":"azure-vnet",
             "mode":"bridge",
             "bridge":"azure0",
             "ipam":{
                "type":"azure-vnet-ipam"
             }
          },
          {
             "type":"portmap",
             "capabilities":{
                "portMappings":true
             },
             "snat":true
          }
       ]
}
```

#### <a name="settings-explanation"></a>Vysvětlení nastavení

- **cniVersion**: Moduly plug-in rozhraní CNI v síti Azure Virtual Network podporují verze 0.3.0 a 0.3.1 [specifikace CNI](https://github.com/containernetworking/cni/blob/master/SPEC.md).
- **name**: Název sítě. Tuto vlastnost lze nastavit na libovolnou jedinečnou hodnotu.
- **type**: Název modulu plug-in sítě. Nastavte na *azure-vnet*.
- **mode**: Provozní režim. Toto pole je nepovinné. Jediný podporovaný režim je režim síťového mostu. Další informace najdete v tématu o [provozních režimech](https://github.com/Azure/azure-container-networking/blob/master/docs/network.md).
- **bridge**: Název mostu, který se použije k připojení kontejnerů k virtuální síti. Toto pole je nepovinné. Pokud ho nezadáte, modul plug-in automaticky zvolí jedinečný název podle indexu hlavního rozhraní.
- **ipam type**: Name modulu plug-in správy IP adres. Vždy nastaveno na hodnotu *azure-vnet-ipam*.

## <a name="download-and-install-the-plug-in"></a>Stažení a instalace modulu plug-in

Stáhněte modul plug-in z portálu [GitHub](https://github.com/Azure/azure-container-networking/releases). Vyhledejte nejnovější verzi pro platformu, kterou používáte:

- **Linux**: [azure-vnet-cni-linux-amd64-\<č. verze\>.tgz](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-linux-amd64-v1.0.12-rc3.tgz)
- **Windows**: [azure-vnet-cni-windows-amd64-\<č. verze\>.zip](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-windows-amd64-v1.0.12-rc3.zip)

Zkopírujte do svého počítače instalační skript pro [Linux](https://github.com/Azure/azure-container-networking/blob/master/scripts/install-cni-plugin.sh) nebo [Windows](https://github.com/Azure/azure-container-networking/blob/master/scripts/Install-CniPlugin.ps1). Uložte skript v počítači do adresáře `scripts` a soubor pojmenujte `install-cni-plugin.sh` (Linux) nebo `install-cni-plugin.ps1` (Windows). K instalaci modulu plug-in spusťte příslušný skript pro vaši platformu a zadejte při tom verzi plug-inu, který používáte. Můžete tak zadat například *v1.0.12 rc3*:

   ```bash
   \$scripts/install-cni-plugin.sh [version]
   ```

   ```powershell
   scripts\\ install-cni-plugin.ps1 [version]
   ```

Skript nainstaluje modul plug-in do adresáře `/opt/cni/bin` (Linux), resp. `c:\cni\bin` (Windows). Nainstalovaný modul plug-in obsahuje jednoduchý síťový konfigurační soubor, který se po instalaci spustí. Není třeba ho aktualizovat. Další informace o nastaveních v tomto souboru najdete v tématu věnovaném [konfiguračnímu souboru sítě CNI](#cni-network-configuration-file).