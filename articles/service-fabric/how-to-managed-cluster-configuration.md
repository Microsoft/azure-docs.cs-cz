---
title: Konfigurace spravovaného clusteru Service Fabric (Preview)
description: Přečtěte si, jak nakonfigurovat Service Fabric spravovaný cluster pro automatické upgrady operačního systému, pravidla NSG a další.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 90ba5057f06cf8841e278b8d921d812286459c49
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642402"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Service Fabric možností konfigurace spravovaného clusteru (Preview)

Kromě výběru [Service Fabric SKU spravovaného clusteru](overview-managed-cluster.md#service-fabric-managed-cluster-skus) při vytváření clusteru existuje mnoho dalších způsobů, jak ho nakonfigurovat. V aktuální verzi Preview můžete:

* Přidání [rozšíření sady škálování virtuálního počítače](how-to-managed-cluster-vmss-extension.md) do typu uzlu
* Povolit [automatické upgrady operačního systému](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades) pro vaše uzly
* Povolení [šifrování operačního systému a datového disku](how-to-enable-managed-cluster-disk-encryption.md) na uzlech
* O [síťových konfiguracích](how-to-managed-cluster-configuration.md#networking-configurations)
* Konfigurace [spravované identity](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) na typech uzlů

## <a name="networking-configurations"></a>Síťové konfigurace

Service Fabric spravované clustery se vytvoří s výchozí konfigurací sítě. Tato konfigurace se skládá z povinných pravidel pro základní funkce clusteru a několik volitelných pravidel, která jsou určená k usnadnění konfigurace zákazníků.

Kromě výchozí konfigurace sítě můžete pravidla sítě upravit tak, aby vyhovovala potřebám vašeho scénáře. 

### <a name="nsg-rules-guidance"></a>Doprovodné materiály k pravidlům NSG

* Service Fabric spravované clustery vyhrazuje prioritu pravidla NSG rozsah 0 až 999 pro základní funkce. Nemůžete vytvořit vlastní pravidla NSG s hodnotou priority menší než 1000. 
* Service Fabric spravované clustery vyhrazují pro vytváření volitelných pravidel NSG rozsah priority 3001 až 4000. Tato pravidla jsou automaticky přidána, aby konfigurace byly rychlé a snadné. Tato pravidla můžete přepsat přidáním vlastních pravidel NSG v rozsahu priority 1000 až 3000. 
* Vlastní pravidla NSG by měla mít prioritu v rozsahu 1000 až 3000. 


### <a name="apply-nsg-rules"></a>Použití pravidel NSG

U klasických (nespravovaných) Service Fabric clusterů musíte deklarovat a spravovat samostatný prostředek *Microsoft. Network/networkSecurityGroups* , aby bylo možné [použít pravidla skupiny zabezpečení sítě (NSG) pro váš cluster](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-nsg-cluster-65-node-3-nodetype). Service Fabric spravované clustery umožňují přiřadit pravidla NSG přímo do prostředku clusteru šablony nasazení.

K přiřazení pravidel NSG použijte vlastnost [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) prostředku *Microsoft. ServiceFabric/managedclusters* (verze `2021-01-01-preview` nebo novější). Příklad:

```json
            "apiVersion": "2021-01-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                ...
                "networkSecurityRules" : [
                    {
                        "name": "AllowCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33000-33499",
                        "access": "Allow",
                        "priority": 2001,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "AllowARM",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "AzureResourceManager",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33500-33699",
                        "access": "Allow",
                        "priority": 2002,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "DenyCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33700-33799",
                        "access": "Deny",
                        "priority": 2003,
                        "direction": "Outbound"
                    },
                    {
                        "name": "DenyRDP",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "*",
                        "destinationAddressPrefix": "VirtualNetwork",
                        "destinationPortRange": "3389",
                        "access": "Deny",
                        "priority": 2004,
                        "direction": "Inbound",
                        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
                    }
                ],
                "fabricSettings": [
                ...
                ]
            }
```

### <a name="rdp-ports"></a>Porty protokolu RDP 

Service Fabric spravované clustery nepovolují ve výchozím nastavení přístup k portům RDP. Porty protokolu RDP na Internet můžete otevřít nastavením následující vlastnosti u prostředku spravovaného clusteru Service Fabric. 

```json
"allowRDPAccess": true 
```

Pokud je vlastnost allowRDPAccess nastavena na hodnotu true, do nasazení clusteru bude přidáno následující pravidlo NSG.  

```json
{
    "name": "SFMC_AllowRdpPort", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

### <a name="clientconnection-and-httpgatewayconnection-ports"></a>Porty ClientConnection a HttpGatewayConnection 

**Pravidlo NSG: SFMC_AllowServiceFabricGatewayToSFRP** Přidá se výchozí pravidlo NSG, které poskytovateli prostředků Service Fabric umožní přístup k clientConnectionPort a httpGatewayConnectionPort clusteru. Toto pravidlo umožňuje přístup k portům prostřednictvím značky služby ' ServiceFabric '.

>[!NOTE]
>Toto pravidlo je vždy přidáno a nelze ho přepsat. 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayToSFRP", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.", 
        "protocol": "TCP", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "ServiceFabric", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 500, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

**Pravidlo NSG: SFMC_AllowServiceFabricGatewayPorts** Toto je volitelné pravidlo NSG, které umožní přístup k clientConnectionPort a httpGatewayPort z Internetu. Toto pravidlo umožňuje zákazníkům přístup k SFX, připojení ke clusteru pomocí prostředí PowerShell a použití Service Fabric koncových bodů rozhraní API clusteru mimo. 

>[!NOTE]
>Toto pravidlo nebude přidáno, pokud existuje vlastní pravidlo se stejným přístupem, směrem a hodnotami protokolu pro stejný port. Toto pravidlo můžete přepsat vlastními NSG pravidly. 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayPorts", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.", 
        "protocol": "tcp", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "*", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 3001, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

### <a name="load-balancer-ports"></a>Porty nástroje pro vyrovnávání zatížení 

Service Fabric spravované clustery vytvoří pravidlo NSG ve výchozím rozsahu priority pro všechny porty s porty, které jsou nakonfigurované v části "loadBalancingRules" v části vlastnosti ManagedCluster. Toto pravidlo otevírá porty pro příchozí provoz z Internetu.  

>[!NOTE]
>Toto pravidlo se přidá do rozsahu volitelné priority a dá se přepsat přidáním vlastních pravidel NSG. 

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*", 
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

### <a name="load-balancer-probes"></a>Sondy pro vyrovnávání zatížení

Service Fabric spravované clustery automaticky vytvoří sondy nástroje pro vyrovnávání zatížení pro porty brány Fabric a také všechny porty nakonfigurované v části "loadBalancingRules" vlastností spravovaného clusteru.

```json
{ 
  "value": [ 
    { 
        "name": "FabricTcpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19000, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>"
                } 
            ] 
        }, 
        "type": "Microsoft.Network/loadBalancers/probes" 
    }, 
    { 
        "name": "FabricHttpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>" 
                } 
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes" 
    },
    {
        "name": "probe1_tcp_8080", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 8080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
            { 
                "id": "<>" 
            } 
        ] 
      }, 
      "type": "Microsoft.Network/loadBalancers/probes" 
    } 
  ] 
} 
```

## <a name="enable-automatic-os-image-upgrades"></a>Povolit automatické upgrady bitových kopií operačního systému

Můžete povolit automatické upgrady bitových kopií operačního systému na virtuální počítače, na kterých běží spravované uzly clusteru. I když se prostředky sady škálování virtuálních počítačů spravují vaším jménem pomocí Service Fabric spravovaných clusterů, je vaše volba Povolit automatické upgrady imagí operačního systému pro uzly clusteru. Stejně jako u [klasických Service Fabric](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) clusterů se ve výchozím nastavení neupgradují uzly spravovaného clusteru, aby se zabránilo nezamýšleným výpadkům clusteru.

Postup povolení automatického upgradu operačního systému:

* Použijte `2021-01-01-preview` verzi (nebo novější) prostředků *Microsoft. ServiceFabric/Managedclusters* a *Microsoft. ServiceFabric/managedclusters/nodetypes* .
* Nastavte vlastnost clusteru `enableAutoOSUpgrade` na *hodnotu true* .
* Nastavte vlastnost prostředku nodeType clusteru `vmImageVersion` na *nejnovější* .

Příklad:

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

[Odkaz na vzorovou šablonu]

[Přehled spravovaného clusteru]
