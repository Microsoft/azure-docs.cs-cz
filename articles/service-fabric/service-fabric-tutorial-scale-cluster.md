---
title: Škálování clusteru Service Fabric v Azure
description: V tomto kurzu se dozvíte, jak škálovat cluster Service Fabric v Azure ven a dovnitř a jak vyčistit zbývající prostředky.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: f1b813576a94541cdc2ab0a67fea71b6f49696c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78251801"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Kurz: Škálování clusteru Service Fabric v Azure

Tento kurz je třetí část řady a ukazuje, jak škálovat stávající cluster ven a dovnitř. Po dokončení budete vědět, jak škálovat cluster a jak vyčistit všechny zbylé prostředky.  Další informace o škálování clusteru spuštěného v Azure najdou [nawebu, který načte škálování service fabric clusterů](service-fabric-cluster-scaling.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání a odebrání uzlů (horizontální navýšení kapacity a škálování v)
> * Přidání a odebrání typů uzlů (horizontální navýšení kapacity a škálování v)
> * Zvýšení prostředků uzlu (škálování nahoru)

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného [clusteru Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure pomocí šablony
> * [Sledování clusteru](service-fabric-tutorial-monitor-cluster.md)
> * Horizontální snížení nebo navýšení kapacity clusteru
> * [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)
> * [Odstranění clusteru](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Nainstalujte [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) nebo [Azure CLI](/cli/azure/install-azure-cli).
* Vytvoření zabezpečeného [clusteru Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure

## <a name="important-considerations-and-guidelines"></a>Důležité aspekty a pokyny

Úlohy aplikací se v průběhu času mění, potřebují vaše stávající služby více (nebo méně) prostředků?  [Přidáním nebo odebráním uzlů](#add-nodes-to-or-remove-nodes-from-a-node-type) z typu uzlu zvýšíte nebo snížíte prostředky clusteru.

Potřebujete do clusteru přidat více než 100 uzlů?  Jeden typ nebo škálovací sada uzlu Service Fabric nesmí obsahovat více než 100 uzlů/virtuálních mích.  Chcete-li škálovat cluster nad 100 uzlů, [přidejte další typy uzlů](#add-nodes-to-or-remove-nodes-from-a-node-type).

Má vaše aplikace více služeb a musí být kterákoli z nich veřejná nebo internetová?  Typické aplikace obsahují službu front-endbrány, která přijímá vstup y od klienta a jednu nebo více back-endových služeb, které komunikují s front-endovými službami. V takovém případě doporučujeme přidat do clusteru [alespoň dva typy uzlů.](#add-nodes-to-or-remove-nodes-from-a-node-type)  

Mají vaše služby různé potřeby infrastruktury, jako je větší paměť RAM nebo vyšší cykly procesoru? Například aplikace obsahuje službu front-end a back-end služby. Front-endová služba může běžet na menších virtuálních počítačích (velikosti virtuálních počítačů, jako je D2), které mají porty otevřené pro internet. Back-endová služba je však náročná na výpočty a potřebuje běžet na větších virtuálních počítačích (s velikostmi virtuálních zařízení, jako jsou D4, D6, D15), které nejsou orientované na internet. V takovém případě doporučujeme přidat do clusteru [dva nebo více typů uzlů.](#add-nodes-to-or-remove-nodes-from-a-node-type) To umožňuje každému typu uzlu mít odlišné vlastnosti, jako je připojení k internetu nebo velikost virtuálního počítače. Počet virtuálních mkénců lze škálovat nezávisle, také.

Při škálování clusteru Azure mějte na paměti následující pokyny:

* Jeden typ nebo škálovací sada uzlu Service Fabric nesmí obsahovat více než 100 uzlů/virtuálních mích.  Chcete-li škálovat cluster nad 100 uzlů, přidejte další typy uzlů.
* Primární typy uzlů, které spouštějí produkční úlohy, by měly mít [úroveň odolnosti][durability] zlaťáků nebo stříbra a vždy mít pět nebo více uzlů.
* Jiné než primární typy uzlů se spuštěnou stavovou produkční úlohou by měly mít vždy pět nebo více uzlů.
* Jiné než primární typy uzlů, ve kterých běží bezstavové produkční úlohy, by měly mít vždy dva nebo více uzlů.
* Jakýkoli typ uzlu [úrovně odolnosti][durability] zlata nebo stříbra by měl mít vždy pět nebo více uzlů.
* Pokud škálování v (odebrání uzlů z) primární typ uzlu, nikdy byste neměli snížit počet instancí na méně, než vyžaduje [úroveň spolehlivosti.][reliability]

Další informace naleznete v [pokynech k kapacitě clusteru](service-fabric-cluster-capacity.md).

## <a name="export-the-template-for-the-resource-group"></a>Vyexportování šablony pro skupinu prostředků

Po úspěšném vytvoření zabezpečeného [clusteru windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) a úspěšném nastavení skupiny prostředků exportujte šablonu Správce prostředků pro skupinu prostředků. Export šablony umožňuje automatizovat budoucí nasazení clusteru a jeho prostředků, protože šablona obsahuje veškerou úplnou infrastrukturu.  Další informace o exportu šablon najdete v části [Správa skupin prostředků Azure Resource Manageru pomocí portálu Azure .](/azure/azure-resource-manager/manage-resource-groups-portal)

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na skupinu prostředků obsahující cluster **(sfclustertutorialgroup**, pokud se řídíte tímto kurzem). 

2. V levém podokně vyberte **Nasazení**nebo vyberte odkaz v části **Nasazení**. 

3. Vyberte poslední úspěšné nasazení ze seznamu.

4. V levém podokně vyberte **Šablona** a pak vyberte **Stáhnout,** chcete-li šablonu exportovat jako soubor ZIP.  Uložte šablonu a parametry do místního počítače.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Přidání uzlů do uzlů nebo odebrání uzlů z typu uzlu

Změna měřítka dovnitř a ven nebo vodorovné škálování změní počet uzlů v clusteru. Při škálování v nebo mimo, přidáte další instance virtuálního počítače do škálovací sady. Tyto instance se stanou uzly, které bude Service Fabric používat. Service Fabric to pozná, když se do škálovací sady přidají další instance (díky horizontálnímu navýšení kapacity), a automaticky zareaguje. Cluster můžete kdykoli škálovat, i když jsou v clusteru spuštěny úlohy.

### <a name="update-the-template"></a>Aktualizace šablony

[Exportujte soubor šablony a parametrů](#export-the-template-for-the-resource-group) ze skupiny prostředků pro poslední nasazení.  Otevřete soubor *parameters.json.*  Pokud jste v tomto kurzu nasadili cluster pomocí [ukázkové šablony,][template] jsou v clusteru tři typy uzlů a tři parametry, které nastavují počet uzlů pro každý typ uzlu: *nt0InstanceCount*, *nt1InstanceCount*a *nt2InstanceCount*.  Parametr *nt1InstanceCount* například nastaví počet instancí pro druhý typ uzlu a nastaví počet virtuálních počítačů v přidružené škálovací sadě virtuálních strojů.

Takže aktualizací hodnoty *nt1InstanceCount* změníte počet uzlů v druhém typu uzlu.  Nezapomeňte, že nelze škálovat typ uzlu na více než 100 uzlů.  Jiné než primární typy uzlů se spuštěnou stavovou produkční úlohou by měly mít vždy pět nebo více uzlů. Jiné než primární typy uzlů, ve kterých běží bezstavové produkční úlohy, by měly mít vždy dva nebo více uzlů.

Pokud měníte měřítko, odeberete uzly z, typ uzlu [bronzová úroveň trvanlivosti,][durability] musíte [ručně odebrat stav těchto uzlů](service-fabric-cluster-scale-up-down.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set).  U úrovně odolnosti stříbra a zlaťáků jsou tyto kroky prováděny automaticky platformou.

### <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony
Uložte všechny změny do souborů *template.json* a *parameters.json.*  Chcete-li nasadit aktualizovanou šablonu, spusťte následující příkaz:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Nebo následující příkaz k příkazu Azure CLI:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Přidání typu uzlu do clusteru

Každý typ uzlu, který je definován v clusteru Service Fabric spuštěném v Azure, se nastavuje jako [samostatná škálovací sada virtuálních strojů](service-fabric-cluster-nodetypes.md). Každý typ uzlu lze pak spravovat samostatně. Můžete nezávisle škálovat každý typ uzlu nahoru nebo dolů, mít různé sady portů otevřené a používat různé metriky kapacity. Můžete také nezávisle změnit sku operačního systému spuštěnou na každém uzlu clusteru, ale všimněte si, že v ukázkovém clusteru nelze mít spuštěnou kombinaci Windows a Linuxu. Jeden typ uzlu/škálovací sada nemůže obsahovat více než 100 uzlů.  Můžete škálovat cluster vodorovně na více než 100 uzlů přidáním dalších typů uzlů nebo škálovacích sad. Cluster můžete kdykoli škálovat, i když jsou v clusteru spuštěny úlohy.

### <a name="update-the-template"></a>Aktualizace šablony

[Exportujte soubor šablony a parametrů](#export-the-template-for-the-resource-group) ze skupiny prostředků pro poslední nasazení.  Otevřete soubor *parameters.json.*  Pokud jste nasadili cluster pomocí [ukázkové šablony][template] v tomto kurzu, existují tři typy uzlů v clusteru.  V této části přidáte čtvrtý typ uzlu aktualizací a nasazením šablony Správce prostředků. 

Kromě nového typu uzlu přidáte také přidruženou škálovací sadu virtuálních strojů (která běží v samostatné podsíti virtuální sítě) a skupinu zabezpečení sítě.  Můžete přidat nové nebo existující veřejné IP adresy a azure vyrovnávání zatížení prostředky pro nové škálovací sady.  Nový typ uzlu má [úroveň odolnosti][durability] stříbra a velikost "Standard_D2_V2".

Do souboru *template.json* přidejte následující nové parametry:
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

Do souboru *template.json* přidejte následující nové proměnné:
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

V souboru *template.json* přidejte novou podsíť do prostředku virtuální sítě:
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

Do souboru *template.json* přidejte nové veřejné IP adresy a prostředky pro vyrovnávání zatížení:
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

Do souboru *template.json* přidejte nové prostředky skupiny zabezpečení sítě a škálovací sady virtuálních strojů.  Vlastnost NodeTypeRef v rámci vlastností rozšíření Service Fabric škálovací sady virtuálního počítače mapuje zadaný typ uzlu na škálovací sadu.

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

V souboru *template.json* aktualizujte prostředek clusteru a přidejte nový typ uzlu:
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

Do souboru *parameters.json* přidejte následující nové parametry a hodnoty:
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony
Uložte všechny změny do souborů *template.json* a *parameters.json.*  Chcete-li nasadit aktualizovanou šablonu, spusťte následující příkaz:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Nebo následující příkaz k příkazu Azure CLI:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Odebrání typu uzlu z clusteru
Po vytvoření clusteru Service Fabric můžete škálovat cluster vodorovně odebráním typu uzlu (škálovací sada virtuálního počítače) a všech jeho uzlů. Cluster můžete kdykoli škálovat, i když jsou v clusteru spuštěny úlohy. Při škálování clusteru se automaticky škálují také vaše aplikace.

> [!WARNING]
> Použití Remove-AzServiceFabricNodeType odebrat typ uzlu z produkčního clusteru se nedoporučuje používat často. Jedná se o nebezpečný příkaz, protože odstraní prostředek škálovací sady virtuálních strojů za typem uzlu. 

Chcete-li odebrat typ uzlu, spusťte rutinu [Remove-AzServiceFabricNodeType.](/powershell/module/az.servicefabric/remove-azservicefabricnodetype)  Typ uzlu musí být stříbrná nebo zlatá [úroveň trvanlivosti][durability] Rutina odstraní škálovací sadu přidruženou k typu uzlu a trvá nějakou dobu.  Potom spusťte rutinu [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) na každém z uzlů, které chcete odebrat, který odstraní stav uzlu a odebere uzly z clusteru. Pokud jsou služby na uzly, pak služby jsou nejprve přesunuty do jiného uzlu. Pokud správce clusteru nemůže najít uzel pro repliku nebo službu, je operace zpožděna nebo blokována.

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

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

## <a name="increase-node-resources"></a>Zvýšení prostředků uzlu 
Po vytvoření clusteru Service Fabric můžete škálovat typ uzlu clusteru svisle (změnit prostředky uzlů) nebo upgradovat operační systém virtuálních zařízení typu uzlu.  

> [!WARNING]
> Doporučujeme neměnit skladovou položku virtuálního měn typu škálovací sady nebo uzlu, pokud není spuštěna s odolností stříbra nebo větší. Změna velikosti skladové položky virtuálního počítače je operace infrastruktury na místě destruktivní pro data. Bez určité schopnosti zpoždění nebo sledování této změny je možné, že operace může způsobit ztrátu dat pro stavové služby nebo způsobit jiné nepředvídané provozní problémy, a to i pro bezstavové úlohy.

> [!WARNING]
> Doporučujeme neměnit skladovou položku virtuálního řezu typu primárního uzlu, což je nebezpečná operace a nepodporovaná.  Pokud potřebujete větší kapacitu clusteru, můžete přidat další instance virtuálních počítačů nebo další typy uzlů.  Pokud to není možné, můžete vytvořit nový cluster a [obnovit stav aplikace](service-fabric-reliable-services-backup-restore.md) (pokud je to možné) ze starého clusteru.  Pokud to není možné, můžete [změnit skladovou položku virtuálního řezu primárního typu uzlu](service-fabric-scale-up-node-type.md).

### <a name="update-the-template"></a>Aktualizace šablony

[Exportujte soubor šablony a parametrů](#export-the-template-for-the-resource-group) ze skupiny prostředků pro poslední nasazení.  Otevřete soubor *parameters.json.*  Pokud jste nasadili cluster pomocí [ukázkové šablony][template] v tomto kurzu, existují tři typy uzlů v clusteru.  

Velikost virtuálních počítače v druhém typu uzlu je nastavena v parametru *vmNodeType1Size.*  Změňte hodnotu parametru *vmNodeType1Size* z Standard_D2_V2 na [Standard_D3_V2](../virtual-machines/dv2-dsv2-series.md), která zdvojnásobí prostředky každé instance virtuálního počítače.

Skladová položka virtuálního počítače pro všechny tři typy uzlů je nastavena v parametru *vmImageSku.*  Znovu změna skladové položky virtuálního uživatele typu uzlu by měla být přístupná s opatrností a nedoporučuje se pro typ primárního uzlu.

### <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony
Uložte všechny změny do souborů *template.json* a *parameters.json.*  Chcete-li nasadit aktualizovanou šablonu, spusťte následující příkaz:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Nebo následující příkaz k příkazu Azure CLI:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání a odebrání uzlů (horizontální navýšení kapacity a škálování v)
> * Přidání a odebrání typů uzlů (horizontální navýšení kapacity a škálování v)
> * Zvýšení prostředků uzlu (škálování nahoru)

Dále se v následujícím kurzu dozvíte, jak upgradovat modul runtime clusteru.
> [!div class="nextstepaction"]
> [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json

> * Přidání a odebrání typů uzlů (horizontální navýšení kapacity a škálování v)
> * Zvýšení prostředků uzlu (škálování nahoru)

Dále se v následujícím kurzu dozvíte, jak upgradovat modul runtime clusteru.
> [!div class="nextstepaction"]
> [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
