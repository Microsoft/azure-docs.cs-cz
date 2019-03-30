---
title: Škálování clusteru Service Fabric v Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak škálovat cluster Service Fabric v Azure.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 40e372b779d06656b111ad3d7de435b99c401dc3
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669499"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Kurz: Škálování clusteru Service Fabric v Azure

Tento kurz je třetí část série a ukazuje, jak škálovat existující cluster navýšení nebo snížení kapacity. Po dokončení budete vědět, jak škálovat cluster a jak vyčistit všechny zbylé prostředky.  Další informace o škálování clusteru běžícího v Azure, najdete v článku [clusterů Service Fabric škálování](service-fabric-cluster-scaling.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání a odebrání uzlů (horizontální navýšení kapacity a horizontální snížení kapacity)
> * Přidat nebo odebrat typy uzlů (horizontální navýšení kapacity a horizontální snížení kapacity)
> * Zvýšení uzel zdroje (vertikální navýšení)

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného [clusteru Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure pomocí šablony
> * [Monitorování clusteru](service-fabric-tutorial-monitor-cluster.md)
> * Horizontální snížení nebo navýšení kapacity clusteru
> * [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)
> * [Odstranění clusteru](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte si [modul Azure PowerShell verze 4.1 nebo novější ](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) nebo [Azure CLI](/cli/azure/install-azure-cli).
* Vytvoření zabezpečeného [clusteru Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure

## <a name="important-considerations-and-guidelines"></a>Důležité informace a pokyny

Zatížení aplikace v průběhu času měnit, potřebují stávající služby prostředků (méně nebo více)?  [Přidávání a odebírání uzlů](#add-nodes-to-or-remove-nodes-from-a-node-type) z typu uzlu na zvýšení nebo snížení prostředků clusteru.

Je potřeba přidat více než 100 uzlů do clusteru?  Jeden typ/škálovací sady uzlu Service Fabric nemůže obsahovat více než 100 uzly nebo virtuální počítače.  Škálování clusteru nad 100 uzlů [přidat dalšího uzlu typy](#add-nodes-to-or-remove-nodes-from-a-node-type).

Vaše aplikace mít několik služeb, a některý z nich musí být public nebo připojení k Internetu?  Typická aplikace obsahují služba front-end bránu, která přijímá vstup z klienta a jeden nebo více back endovým službám, které komunikují s front-endové služby. V takovém případě vám doporučujeme [přidejte aspoň dva typy uzlů](#add-nodes-to-or-remove-nodes-from-a-node-type) do clusteru.  

Mají služby potřeby různé infrastruktury, jako je například větší paměť RAM nebo vyšší cyklů procesoru? Vaše aplikace například obsahuje front-endové služby a back-end služby. Front-end služba může běžet na menších virtuálních počítačů (velikosti virtuálních počítačů jako D2), které mají porty otevřené Internetu. Back-end služby, ale je náročné na výpočetní prostředky a je potřeba spustit na větších virtuálních počítačů s (s velikostí virtuálních počítačů jako D4, D6, D15), které nejsou internet otočena směrem. V takovém případě je vhodné, který jste [přidejte nejmíň dva typy uzlů](#add-nodes-to-or-remove-nodes-from-a-node-type) do clusteru. To umožňuje u každého typu uzlu mít různé vlastnosti, jako je připojení k Internetu nebo velikost virtuálního počítače. Počet virtuálních počítačů je možné škálovat nezávisle na sobě, stejně.

Při škálování clusteru Azure, mějte podle následujících pokynů:

* Jeden typ/škálovací sady uzlu Service Fabric nemůže obsahovat více než 100 uzly nebo virtuální počítače.  Škálování nad rámec 100 uzlů clusteru, přidejte typy dalších uzlů.
* Zpracovává produkční zátěž s typy primárního uzlu by měl mít [úroveň odolnosti] [ durability] Gold a Silver a vždy mít pět nebo více uzlů.
* typy jiné než primární uzel spuštěná stavová produkční úlohy by měl vždy mít pět nebo více uzlů.
* typy uzlů neprimární spuštěná Bezstavová produkční úlohy by měl mít vždy dva nebo víc uzlů.
* Jakýkoli typ uzlu [úroveň odolnosti] [ durability] Gold a Silver by měli vždy mít pět nebo více uzlů.
* Změna měřítka ve (odebrání uzlů z)-li typ primárního uzlu, měl by se nikdy snížit počet instancí na hodnotu menší než co [úroveň spolehlivosti] [ reliability] vyžaduje.

Další informace najdete v článku [clusteru kapacity pokyny](service-fabric-cluster-capacity.md).

## <a name="export-the-template-for-the-resource-group"></a>Vyexportování šablony pro skupinu prostředků

Po vytvoření zabezpečeného [clusteru Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) a nastavení vaší skupiny prostředků úspěšně, export šablony Resource Manageru pro skupinu prostředků. Export šablony umožňuje automatizovat budoucí nasazení clusteru a jeho prostředky, protože šablona obsahuje kompletní infrastrukturu.  Další informace o exportování šablony najdete v článku [skupiny prostředků spravovat Azure Resource Manageru pomocí webu Azure portal](/azure/azure-resource-manager/manage-resource-groups-portal).

1. V [webu Azure portal](https://portal.azure.com), přejděte do skupiny prostředků obsahující cluster (**sfclustertutorialgroup**, pokud jsou projdete tímto kurzem). 

2. V levém podokně vyberte **nasazení**, nebo vyberte propojení v rámci **nasazení**. 

3. Vyberte ze seznamu poslední úspěšné nasazení.

4. V levém podokně vyberte **šablony** a pak vyberte **Stáhnout** vyexportujte šablonu do souboru ZIP.  Uložte šablonu a parametry do místního počítače.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Přidávat a odebírat uzly z typu uzlu

Dovnitř a ven vertikální nebo horizontální škálování, změně počtu uzlů v clusteru. Při horizontálním snížení nebo navýšení kapacity přidáte do škálovací sady více instancí virtuálních počítačů. Tyto instance se stanou uzly, které bude Service Fabric používat. Service Fabric to pozná, když se do škálovací sady přidají další instance (díky horizontálnímu navýšení kapacity), a automaticky zareaguje. Je možné škálovat cluster v okamžiku, i když spouštění úloh v clusteru.

### <a name="update-the-template"></a>Aktualizace šablony

[Exportovat soubor šablony a parametrů](#export-the-template-for-the-resource-group) ze skupiny prostředků pro aktuální nasazení.  Otevřít *parameters.json* souboru.  Pokud jste nasadili cluster pomocí [Ukázková šablona] [ template] v tomto kurzu jsou tři typy uzlů v clusteru a tři parametry, které nastavit počet uzlů pro každý typ uzlu:  *nt0InstanceCount*, *nt1InstanceCount*, a *nt2InstanceCount*.  *Nt1InstanceCount* parametru, například nastaví počet instancí pro druhý typ uzlu a nastaví počet virtuálních počítačů ve škálovací sadě přidružený virtuální počítač.

Ano, stačí aktualizovat hodnotu *nt1InstanceCount* změnit počet uzlů v druhý typ uzlu.  Nezapomeňte, že už nebude možné škálovat na více než 100 uzlů typ uzlu navýšení kapacity.  typy jiné než primární uzel spuštěná stavová produkční úlohy by měl vždy mít pět nebo více uzlů. typy uzlů neprimární spuštěná Bezstavová produkční úlohy by měl mít vždy dva nebo víc uzlů.

Pokud se Škálováním v odebrání uzlů z uzlu typu bronzová [úroveň odolnosti] [ durability] musíte [ručně odebrat stav těchto uzlů](service-fabric-cluster-scale-up-down.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set).  Pro stříbrný a zlatá úroveň odolnosti jsou tyto kroky automaticky provádí platformu.

### <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony
Uložte všechny změny *template.json* a *parameters.json* soubory.  Pokud chcete nasadit aktualizovanou šablonu, spusťte následující příkaz:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Nebo následující příkaz rozhraní příkazového řádku Azure:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Do clusteru přidat typ uzlu

Každý typ uzlu, který je definován v clusteru Service Fabric běžící v Azure je nastavený jako [oddělení škálovací sadu virtuálních počítačů](service-fabric-cluster-nodetypes.md). Každý typ uzlu je pak spravovat samostatně. Můžete nezávisle na sobě horizontální i vertikální škálování každého typu uzlu, mají různé sady otevřených portů a použít různé metriky kapacity. Můžete také nezávisle na sobě změnit skladová položka operačního systému, které běží na všech uzlech clusteru, ale mějte na paměti, že nemůže mít kombinaci Windows a Linuxu spuštěné v clusteru vzorku. Typ/škálovací sady jednoho uzlu nemůže obsahovat více než 100 uzlů.  Přidáním dalšího uzlu typy nebo škálovacích sad můžete škálovat horizontálně do víc než 100 uzlů clusteru. Je možné škálovat cluster v okamžiku, i když spouštění úloh v clusteru.

### <a name="update-the-template"></a>Aktualizace šablony

[Exportovat soubor šablony a parametrů](#export-the-template-for-the-resource-group) ze skupiny prostředků pro aktuální nasazení.  Otevřít *parameters.json* souboru.  Pokud jste nasadili cluster pomocí [Ukázková šablona] [ template] v tomto kurzu, existují tři typy uzlů v clusteru.  V této části přidáte čtvrtý typ uzlu aktualizace a nasazení šablony Resource Manageru. 

Kromě nový typ uzlu také přidat přidružený virtuální počítač škálovací sady (který se spouští v samostatné podsíti ve virtuální síti) a skupiny zabezpečení sítě.  Můžete přidat nové nebo stávající veřejnou IP adresu a prostředků nástroje pro vyrovnávání zatížení Azure pro novou škálovací sadu.  Má nový typ uzlu [úroveň odolnosti] [ durability] stříbrný a velikosti "Standard_D2_V2".

V *template.json* přidejte následující nové parametry:
```json
"nt3InstanceCount": {
    "defaultValue": 5,
    "type": "Int",
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType3Size": {
    "defaultValue": "Standard_D2_V2",
    "type": "String"
},
```

V *template.json* přidejte následující nové proměnné:
```json
"lbID3": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
"lbIPConfig3": "[concat(variables('lbID3'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
"lbPoolID3": "[concat(variables('lbID3'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
"lbProbeID3": "[concat(variables('lbID3'),'/probes/FabricGatewayProbe')]",
"lbHttpProbeID3": "[concat(variables('lbID3'),'/probes/FabricHttpGatewayProbe')]",
"lbNatPoolID3": "[concat(variables('lbID3'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
"vmNodeType3Name": "[toLower(concat('NT4', variables('vmName')))]",
"vmStorageAccountName3": "[toLower(concat(uniqueString(resourceGroup().id), '1', '3' ))]",
"nt3applicationStartPort": "20000",
"nt3applicationEndPort": "30000",
"nt3ephemeralStartPort": "49152",
"nt3ephemeralEndPort": "65534",
"nt3fabricTcpGatewayPort": "19000",
"nt3fabricHttpGatewayPort": "19080",
"nt3reverseProxyEndpointPort": "19081",
"subnet3Name": "Subnet-3",
"subnet3Prefix": "10.0.3.0/24",
"subnet3Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet3Name'))]",
```

V *template.json* přidejte novou podsíť prostředku virtuální sítě:
```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            ...
            {
                "name": "[variables('subnet3Name')]",
                "properties": {
                    "addressPrefix": "[variables('subnet3Prefix')]",
                    "networkSecurityGroup": {
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', variables('subnet3Name')))]"
                    }
                }
            }
        ]
    },
    "dependsOn": [
        ...
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', variables('subnet3Name')))]"
    ]
},
```

V *template.json* souboru, přidat nové veřejné IP adresy a zatížení nástroje pro vyrovnávání prostředky:
```json
{
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "dnsSettings": {
            "domainNameLabel": "[concat(variables('dnsName'),'-','nt4')]"
        },
        "publicIPAllocationMethod": "Dynamic"
    }
},
        {
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "frontendIPConfigurations": [
            {
                "name": "LoadBalancerIPConfig",
                "properties": {
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
                    }
                }
            }
        ],
        "backendAddressPools": [
            {
                "name": "LoadBalancerBEAddressPool",
                "properties": {}
            }
        ],
        "loadBalancingRules": [
            {
                "name": "LBRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "LBHttpRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbHttpProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule1",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort1')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort1')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe1')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule2",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort2')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort2')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe2')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            {
                "name": "FabricGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricTcpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "FabricHttpGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricHttpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe1",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort1')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe2",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort2')]",
                    "protocol": "tcp"
                }
            }
        ],
        "inboundNatPools": [
            {
                "name": "LoadBalancerBEAddressNatPool",
                "properties": {
                    "backendPort": "3389",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPortRangeEnd": "4500",
                    "frontendPortRangeStart": "3389",
                    "protocol": "tcp"
                }
            }
        ]
    },
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
    ]
},
```

V *template.json* přidejte nové sítě zabezpečení virtuálního počítače a skupiny prostředků škálovací sady.  NodeTypeRef vlastnosti v rámci vlastnosti rozšíření škálovací sady virtuálních počítačů Service Fabric mapuje typ zadaný uzel do škálovací sady.

```json
{
    "type": "Microsoft.Network/networkSecurityGroups",
    "name": "[concat('nsg', variables('subnet3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[resourceGroup().location]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "securityRules": [
            {
                "name": "allowSvcFabSMB",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "445",
                    "direction": "Inbound",
                    "priority": 3950,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow SMB traffic within the net, used by fabric to move packages around"
                }
            },
            {
                "name": "allowSvcFabCluser",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "1025-1027",
                    "direction": "Inbound",
                    "priority": 3920,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow ports within vnet that are used by the fabric to talk between nodes"
                }
            },
            {
                "name": "allowSvcFabEphemeral",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3ephemeralStartPort'), '-', variables('nt3ephemeralEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3930,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow fabric ephemeral ports within the vnet"
                }
            },
            {
                "name": "allowSvcFabPortal",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricHttpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3900,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster web portal"
                }
            },
            {
                "name": "allowSvcFabClient",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricTcpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3910,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used by the fabric client (includes powershell)"
                }
            },
            {
                "name": "allowSvcFabApplication",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3applicationStartPort'), '-', variables('nt3applicationEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3940,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow fabric application ports within the vnet"
                }
            },
            {
                "name": "blockAll",
                "properties": {
                    "access": "Deny",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "*",
                    "direction": "Inbound",
                    "priority": 4095,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "block all traffic except what we've explicitly allowed"
                }
            },
            {
                "name": "allowVNetRDP",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "3389",
                    "direction": "Inbound",
                    "priority": 3960,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow RDP within the net"
                }
            },
            {
                "name": "allowSvcFabReverseProxy",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3reverseProxyEndpointPort')]",
                    "direction": "Inbound",
                    "priority": 3980,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster using reverse proxy"
                }
            },
            {
                "name": "allowAppPort1",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort1')]",
                    "direction": "Inbound",
                    "priority": 2001,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 1"
                }
            },
            {
                "name": "allowAppPort2",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort2')]",
                    "direction": "Inbound",
                    "priority": 2002,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 2"
                }
            }
        ]
    }
},
{
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "sku": {
        "name": "[parameters('vmNodeType3Size')]",
        "capacity": "[parameters('nt3InstanceCount')]",
        "tier": "Standard"
    },
    "name": "[variables('vmNodeType3Name')]",
    "apiVersion": "2018-10-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
            "mode": "Automatic"
        },
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                    {
                        "name": "[concat(variables('vmNodeType3Name'),'OMS')]",
                        "properties": {
                            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                            "type": "MicrosoftMonitoringAgent",
                            "typeHandlerVersion": "1.0",
                            "autoUpgradeMinorVersion": true,
                            "settings": {
                                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                            },
                            "protectedSettings": {
                                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                            }
                        }
                    },
                    {
                        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "ServiceFabricNode",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                            },
                            "publisher": "Microsoft.Azure.ServiceFabric",
                            "settings": {
                                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                "nodeTypeRef": "[variables('vmNodeType3Name')]",
                                "dataPath": "D:\\SvcFab",
                                "durabilityLevel": "Silver",
                                "enableParallelJobs": true,
                                "nicPrefixOverride": "[variables('subnet3Prefix')]",
                                "certificate": {
                                    "thumbprint": "[parameters('certificateThumbprint')]",
                                    "x509StoreName": "[parameters('certificateStoreValue')]"
                                }
                            },
                            "typeHandlerVersion": "1.0"
                        }
                    },
                    {
                        "name": "[concat('VMDiagnosticsVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "IaaSDiagnostics",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "storageAccountName": "[variables('applicationDiagnosticsStorageAccountName')]",
                                "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                "storageAccountEndPoint": "https://core.windows.net/"
                            },
                            "publisher": "Microsoft.Azure.Diagnostics",
                            "settings": {
                                "WadCfg": {
                                    "DiagnosticMonitorConfiguration": {
                                        "overallQuotaInMB": "50000",
                                        "EtwProviders": {
                                            "EtwEventSourceProviderConfiguration": [
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Actors",
                                                    "scheduledTransferKeywordFilter": "1",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableActorEventTable"
                                                    }
                                                },
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Services",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                    }
                                                }
                                            ],
                                            "EtwManifestProviderConfiguration": [
                                                {
                                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                    "scheduledTransferLogLevelFilter": "Information",
                                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricSystemEventTable"
                                                    }
                                                }
                                            ]
                                        }
                                    }
                                },
                                "StorageAccount": "[variables('applicationDiagnosticsStorageAccountName')]"
                            },
                            "typeHandlerVersion": "1.5"
                        }
                    },
                    {
                        "name": "[concat('VMIaaSAntimalware','_vmNodeType3Name')]",
                        "properties": {
                            "publisher": "Microsoft.Azure.Security",
                            "type": "IaaSAntimalware",
                            "typeHandlerVersion": "1.5",
                            "settings": {
                                "AntimalwareEnabled": "true",
                                "Exclusions": {
                                    "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                                    "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
                                },
                                "RealtimeProtectionEnabled": "true",
                                "ScheduledScanSettings": {
                                    "isEnabled": "true",
                                    "scanType": "Quick",
                                    "day": "7",
                                    "time": "120"
                                }
                            },
                            "protectedSettings": null
                        }
                    }
                ]
            },
            "networkProfile": {
                "networkInterfaceConfigurations": [
                    {
                        "name": "[concat(variables('nicName'), '-2')]",
                        "properties": {
                            "ipConfigurations": [
                                {
                                    "name": "[concat(variables('nicName'),'-',2)]",
                                    "properties": {
                                        "loadBalancerBackendAddressPools": [
                                            {
                                                "id": "[variables('lbPoolID3')]"
                                            }
                                        ],
                                        "loadBalancerInboundNatPools": [
                                            {
                                                "id": "[variables('lbNatPoolID3')]"
                                            }
                                        ],
                                        "subnet": {
                                            "id": "[variables('subnet3Ref')]"
                                        }
                                    }
                                }
                            ],
                            "primary": true
                        }
                    }
                ]
            },
            "osProfile": {
                "adminPassword": "[parameters('adminPassword')]",
                "adminUsername": "[parameters('adminUsername')]",
                "computernamePrefix": "[variables('vmNodeType3Name')]",
                "secrets": [
                    {
                        "sourceVault": {
                            "id": "[parameters('sourceVaultValue')]"
                        },
                        "vaultCertificates": [
                            {
                                "certificateStore": "[parameters('certificateStoreValue')]",
                                "certificateUrl": "[parameters('certificateUrlValue')]"
                            }
                        ]
                    }
                ]
            },
            "storageProfile": {
                "imageReference": {
                    "publisher": "[parameters('vmImagePublisher')]",
                    "offer": "[parameters('vmImageOffer')]",
                    "sku": "[parameters('vmImageSku')]",
                    "version": "[parameters('vmImageVersion')]"
                },
                "osDisk": {
                    "caching": "ReadOnly",
                    "createOption": "FromImage",
                    "managedDisk": {
                        "storageAccountType": "[parameters('storageAccountType')]"
                    }
                }
            }
        }
    },
    "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
    ]
},
```

V *template.json* , provést upgrade prostředku clusteru a přidejte nový typ uzlu:
```json
{
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "apiVersion": "2018-02-01",
    "location": "[parameters('clusterLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "nodeTypes": [
            ...
            {
                "name": "[variables('vmNodeType3Name')]",
                "applicationPorts": {
                    "endPort": "[variables('nt3applicationEndPort')]",
                    "startPort": "[variables('nt3applicationStartPort')]"
                },
                "clientConnectionEndpointPort": "[variables('nt3fabricTcpGatewayPort')]",
                "durabilityLevel": "Silver",
                "ephemeralPorts": {
                    "endPort": "[variables('nt3ephemeralEndPort')]",
                    "startPort": "[variables('nt3ephemeralStartPort')]"
                },
                "httpGatewayEndpointPort": "[variables('nt3fabricHttpGatewayPort')]",
                "isPrimary": false,
                "reverseProxyEndpointPort": "[variables('nt3reverseProxyEndpointPort')]",
                "vmInstanceCount": "[parameters('nt3InstanceCount')]"
            }
        ],    
    }
}                
```

V *parameters.json* přidejte následující nové parametry a hodnoty:
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony
Uložte všechny změny *template.json* a *parameters.json* soubory.  Pokud chcete nasadit aktualizovanou šablonu, spusťte následující příkaz:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Nebo následující příkaz rozhraní příkazového řádku Azure:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Odebrat typ uzlu z clusteru
Po vytvoření clusteru Service Fabric, můžete horizontálně škálovat cluster odebráním typu uzlu (škálovací sady virtuálních počítačů) a všechny jeho uzly. Je možné škálovat cluster v okamžiku, i když spouštění úloh v clusteru. Škálování clusteru, vaše aplikace automaticky škálovat směrem také.

> [!WARNING]
> Pomocí Remove-azurermservicefabricnodetype: Odebere typ uzlu z clusteru pro produkční prostředí se nedoporučuje používat často. Odstraní prostředek sady škálování virtuálního počítače za typ uzlu je nebezpečný příkaz. 

Pokud chcete odebrat typ uzlu, spusťte [Remove-azurermservicefabricnodetype:](/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) rutiny.  Typ uzlu musí být stříbrné nebo zlaté úrovně [úroveň odolnosti] [ durability] rutina umožňuje odstranit škálovací sadu přidružený k typu uzlu a nějakou dobu trvá.  Spusťte [odebrat ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) rutiny na každém uzlu k odebrání, které odstraní stav uzlu a odebere uzly z clusteru. Pokud v uzlech služby, potom služby jsou nejprve přesunout do jiného uzlu. Pokud správce clusteru nelze najít uzel repliky nebo službu, tato operace je zpožděné/zablokováno.

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzureRmServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="increase-node-resources"></a>Zvýšení uzel zdroje 
Po vytvoření clusteru Service Fabric, je možné škálovat typ uzlu clusteru svisle (změnit prostředky z uzlů) nebo upgradovat operační systém typu uzlu virtuálních počítačů.  

> [!WARNING]
> Doporučujeme vám, pokud je spuštěná na stříbrné odolnosti nebo vyšší není změnit skladovou Položku virtuálního počítače škálovací sady/uzel typu. Změna velikosti skladové položky virtuálních počítačů je operace destruktivní dat místní infrastrukturu. Bez některé možnosti zpoždění nebo sledovat tuto změnu je možné, že operace může způsobit ztrátu dat pro stavové služby nebo způsobit jiné nepředvídatelné provozní problémy, dokonce i u bezstavových zatížení.

> [!WARNING]
> Doporučujeme neměnit skladovou Položku virtuálního počítače z primární typ uzlu, který je nebezpečné operace a nepodporovaný.  Pokud potřebujete víc kapacity clusteru, můžete přidat další instance virtuálních počítačů nebo typy dalších uzlů.  Pokud to není možné, můžete vytvořit nový cluster a [obnovit stav aplikace](service-fabric-reliable-services-backup-restore.md) (Pokud je k dispozici) z původního clusteru.  Pokud to není možné, můžete si [změnit skladovou Položku virtuálního počítače z primární typ uzlu](service-fabric-scale-up-node-type.md).

### <a name="update-the-template"></a>Aktualizace šablony

[Exportovat soubor šablony a parametrů](#export-the-template-for-the-resource-group) ze skupiny prostředků pro aktuální nasazení.  Otevřít *parameters.json* souboru.  Pokud jste nasadili cluster pomocí [Ukázková šablona] [ template] v tomto kurzu, existují tři typy uzlů v clusteru.  

Velikost virtuálního počítače v druhé typ uzlu je nastavena v *vmNodeType1Size* parametru.  Změnit *vmNodeType1Size* hodnoty parametru z Standard_D2_V2 k [Standard_D3_V2](/azure/virtual-machines/windows/sizes-general#dv2-series), který zdvojnásobuje prostředků jednotlivých instancí virtuálních počítačů.

Skladovou Položku virtuálního počítače pro všechny typy třemi uzly je nastavena v *vmImageSku* parametru.  Znovu změna skladové položky virtuálních počítačů typu uzlu by měl být dosaženy opatrně a nedoporučuje se používat pro primární typ uzlu.

### <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony
Uložte všechny změny *template.json* a *parameters.json* soubory.  Pokud chcete nasadit aktualizovanou šablonu, spusťte následující příkaz:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Nebo následující příkaz rozhraní příkazového řádku Azure:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání a odebrání uzlů (horizontální navýšení kapacity a horizontální snížení kapacity)
> * Přidat nebo odebrat typy uzlů (horizontální navýšení kapacity a horizontální snížení kapacity)
> * Zvýšení uzel zdroje (vertikální navýšení)

Dále se v následujícím kurzu dozvíte, jak upgradovat modul runtime clusteru.
> [!div class="nextstepaction"]
> [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
