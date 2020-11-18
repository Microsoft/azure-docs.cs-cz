---
title: Škálování clusteru Service Fabric v Azure
description: V tomto kurzu se dozvíte, jak škálovat Cluster Service Fabric v Azure na úrovni a jak vyčistit prostředky zbylé.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7f92ca28afd9d1894867aaa2c18df3a02ee0bd79
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842681"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Kurz: Škálování clusteru Service Fabric v Azure

Tento kurz je třetí částí série, kde se dozvíte, jak škálovat stávající cluster na úrovni a v nástroji. Po dokončení budete vědět, jak škálovat cluster a jak vyčistit všechny zbylé prostředky.  Další informace o škálování clusteru běžícího v Azure najdete v tématu [škálování Service Fabric clusterů](service-fabric-cluster-scaling.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidávání a odebírání uzlů (horizontální navýšení kapacity a horizontální navýšení kapacity)
> * Přidávání a odebírání typů uzlů (horizontální navýšení kapacity a horizontální navýšení kapacity)
> * Zvýšení prostředků uzlu (horizontální navýšení kapacity)

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného [clusteru s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure pomocí šablony
> * [Monitorování clusteru](service-fabric-tutorial-monitor-cluster.md)
> * Horizontální snížení nebo navýšení kapacity clusteru
> * [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)
> * [Odstranění clusteru](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

Než začnete s tímto kurzem:

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
* Nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps) nebo rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli).
* Vytvoření zabezpečeného [clusteru s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure

## <a name="important-considerations-and-guidelines"></a>Důležité informace a pokyny

Úlohy aplikací se v průběhu času mění, takže stávající služby vyžadují více (nebo méně) prostředků?  [Přidáním nebo odebráním uzlů](#add-nodes-to-or-remove-nodes-from-a-node-type) z typu uzlu můžete zvýšit nebo snížit prostředky clusteru.

Potřebujete do clusteru přidat víc než 100 uzlů?  Jeden nebo více než jedna sada uzlů typu Service Fabric nebo škálování nemůže obsahovat více než 100 uzlů/virtuálních počítačů.  Chcete-li škálovat cluster nad rámec 100 uzlů, [přidejte další typy uzlů](#add-nodes-to-or-remove-nodes-from-a-node-type).

Má vaše aplikace více služeb a některé z nich musí být veřejné nebo internetové?  Typické aplikace obsahují front-endové službu brány, která přijímá vstup od klienta a jednu nebo více back-endové služby, které komunikují s front-end službami. V takovém případě doporučujeme přidat do clusteru [alespoň dva typy uzlů](#add-nodes-to-or-remove-nodes-from-a-node-type) .  

Mají vaše služby různé požadavky na infrastrukturu, jako je například větší paměť RAM nebo vyšší cykly procesoru? Například vaše aplikace obsahuje front-end službu a back-endové služby. Front-end služba může běžet na menších virtuálních počítačích (velikosti virtuálních počítačů jako D2), které mají porty otevřené na internetu. Back-end služba je však náročné na výpočetní výkon a musí běžet na větších virtuálních počítačích (s velikostí virtuálních počítačů, jako je D4, D6, D15), které nejsou na internetu. V takovém případě doporučujeme přidat do clusteru [dva nebo více typů uzlů](#add-nodes-to-or-remove-nodes-from-a-node-type) . To umožňuje, aby každý typ uzlu měl různé vlastnosti, jako je například připojení k Internetu nebo velikost virtuálního počítače. Počet virtuálních počítačů je možné škálovat nezávisle.

Při škálování clusteru Azure mějte na paměti následující pokyny:

* Jeden nebo více než jedna sada uzlů typu Service Fabric nebo škálování nemůže obsahovat více než 100 uzlů/virtuálních počítačů.  Chcete-li škálovat cluster nad rámec 100 uzlů, přidejte další typy uzlů.
* Typy primárních uzlů, které spouštějí provozní úlohy, by měly mít [úroveň životnosti][durability] Gold nebo stříbrné a mají vždy pět nebo více uzlů.
* Neprimární typy uzlů, na kterých běží stavová provozní zatížení, by měly mít vždycky pět nebo více uzlů.
* Neprimární typy uzlů, na kterých běží Bezstavová provozní zatížení, by měly mít vždycky dva nebo více uzlů.
* Každý typ uzlu [úrovně trvanlivosti][durability] Gold nebo stříbrného by měl mít vždy pět nebo více uzlů.
* Pokud při škálování (odebírání uzlů z) primární typ uzlu, neměli byste nikdy snížit počet instancí na méně, než jakou vyžaduje [úroveň spolehlivosti][reliability] .

Další informace najdete v tématu [doprovodné materiály ke kapacitě clusteru](service-fabric-cluster-capacity.md).

## <a name="export-the-template-for-the-resource-group"></a>Vyexportování šablony pro skupinu prostředků

Po vytvoření zabezpečeného [clusteru s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) a úspěšném nastavení skupiny prostředků exportujte šablonu správce prostředků pro skupinu prostředků. Export šablony vám umožní automatizovat budoucí nasazení clusteru a jeho prostředků, protože šablona obsahuje veškerou kompletní infrastrukturu.  Další informace o exportu šablon najdete v tématu [správa Azure Resource Manager skupin prostředků pomocí Azure Portal](../azure-resource-manager/management/manage-resource-groups-portal.md).

1. V [Azure Portal](https://portal.azure.com)přejdete do skupiny prostředků obsahující cluster (**sfclustertutorialgroup**, pokud budete postupovat podle tohoto kurzu). 

2. V levém podokně vyberte **nasazení** nebo vyberte odkaz v části **nasazení**. 

3. Vyberte ze seznamu poslední úspěšné nasazení.

4. V levém podokně vyberte **šablonu** a pak vyberte **Stáhnout** a EXPORTUJTE šablonu jako soubor zip.  Uložte šablonu a parametry do svého místního počítače.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Přidání uzlů do nebo odebírání uzlů z typu uzlu

Změna velikosti a zmenšení nebo horizontální škálování mění počet uzlů v clusteru. Při horizontálním navýšení kapacity můžete do sady škálování přidat další instance virtuálních počítačů. Tyto instance se stanou uzly, které bude Service Fabric používat. Service Fabric to pozná, když se do škálovací sady přidají další instance (díky horizontálnímu navýšení kapacity), a automaticky zareaguje. Cluster můžete škálovat kdykoli, a to i v případě, že úlohy běží v clusteru.

### <a name="update-the-template"></a>Aktualizace šablony

[Exportujte šablonu a soubor parametrů](#export-the-template-for-the-resource-group) ze skupiny prostředků pro nejnovější nasazení.  Otevřete *parameters.jsv* souboru.  Pokud jste nasadili cluster pomocí [ukázkové šablony][template] v tomto kurzu, existují tři typy uzlů v clusteru a tři parametry, které nastaví počet uzlů pro každý typ uzlu: *nt0InstanceCount*, *nt1InstanceCount* a *nt2InstanceCount*.  Parametr *nt1InstanceCount* například nastaví počet instancí pro druhý typ uzlu a nastaví počet virtuálních počítačů v přidružené sadě škálování virtuálního počítače.

Pokud tedy aktualizujete hodnotu *nt1InstanceCount* , změníte počet uzlů v druhém typu uzlu.  Nezapomeňte, že nemůžete škálovat typ uzlu na více než 100 uzlů.  Neprimární typy uzlů, na kterých běží stavová provozní zatížení, by měly mít vždycky pět nebo více uzlů. Neprimární typy uzlů, na kterých běží Bezstavová provozní zatížení, by měly mít vždycky dva nebo více uzlů.

Pokud provádíte horizontální [navýšení][durability] kapacity, při odebírání uzlů z, typu na bronzové úrovni trvanlivosti musíte [ručně odebrat stav těchto uzlů](service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set).  Pro stříbro a zlatou úroveň odolnosti se tyto kroky provádí automaticky na platformě.

### <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony
Uloží všechny změny *template.jsv* a *parameters.jsna* soubory.  Aktualizovanou šablonu nasadíte spuštěním následujícího příkazu:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Nebo následující příkaz Azure CLI:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Přidání typu uzlu do clusteru

Každý typ uzlu, který je definovaný v clusteru Service Fabric spuštěném v Azure, je nastavený jako [samostatná sada škálování virtuálního počítače](service-fabric-cluster-nodetypes.md). Každý typ uzlu se pak dá spravovat samostatně. Jednotlivé typy uzlů je možné nezávisle škálovat, můžou mít různé sady portů otevřené a používají různé metriky kapacity. Můžete také nezávisle změnit SKLADOVOU položku operačního systému spuštěnou na každém uzlu clusteru, ale nemůžete mít v ukázkovém clusteru spuštěnou kombinaci systémů Windows a Linux. Jedna sada typů nebo škálování s jedním uzlem nemůže obsahovat více než 100 uzlů.  Cluster můžete škálovat vodorovně na více než 100 uzlů přidáním dalších typů uzlů/sad škálování. Cluster můžete škálovat kdykoli, a to i v případě, že úlohy běží v clusteru.

### <a name="update-the-template"></a>Aktualizace šablony

[Exportujte šablonu a soubor parametrů](#export-the-template-for-the-resource-group) ze skupiny prostředků pro nejnovější nasazení.  Otevřete *parameters.jsv* souboru.  Pokud jste cluster nasadili pomocí [ukázkové šablony][template] v tomto kurzu, existují v clusteru tři typy uzlů.  V této části přidáte čtvrtý typ uzlu aktualizací a nasazením šablony Správce prostředků. 

Kromě nového typu uzlu přidáte také přidruženou sadu škálování virtuálního počítače (která běží v samostatné podsíti virtuální sítě) a skupině zabezpečení sítě.  Pro novou sadu škálování se můžete rozhodnout pro přidání nové nebo existující veřejné IP adresy a prostředků nástroje pro vyrovnávání zatížení Azure.  Nový typ uzlu má [úroveň odolnosti][durability] stříbrné a velikost "Standard_D2_V2".

Do *template.jsv* souboru přidejte následující nové parametry:
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

Do *template.jsv* souboru přidejte následující nové proměnné:
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

V *template.jsv* souboru přidejte novou podsíť do prostředku virtuální sítě:
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

Do *template.jsv* souboru přidejte novou veřejnou IP adresu a prostředky nástroje pro vyrovnávání zatížení:
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

Do *template.jsv* souboru přidejte novou skupinu zabezpečení sítě a prostředky sady škálování virtuálních počítačů.  Vlastnost NodeTypeRef v rámci vlastností rozšíření Service Fabric v sadě škálování virtuálního počítače mapuje zadaný typ uzlu na sadu škálování.

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

V *template.jsv* souboru aktualizujte prostředek clusteru a přidejte nový typ uzlu:
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

Do *parameters.jsv* souboru přidejte následující nové parametry a hodnoty:
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony
Uloží všechny změny *template.jsv* a *parameters.jsna* soubory.  Aktualizovanou šablonu nasadíte spuštěním následujícího příkazu:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Nebo následující příkaz Azure CLI:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Odebrání typu uzlu z clusteru
Po vytvoření clusteru Service Fabric můžete škálovat cluster vodorovně odebráním typu uzlu (sada škálování virtuálního počítače) a všech jeho uzlů. Cluster můžete škálovat kdykoli, a to i v případě, že úlohy běží v clusteru. I když se cluster škáluje, vaše aplikace se automaticky škálují.

> [!WARNING]
> Použití Remove-AzServiceFabricNodeType k odebrání typu uzlu z produkčního clusteru se nedoporučuje používat na časté bázi. Jedná se o nebezpečný příkaz, protože odstraňuje prostředek sady škálování virtuálního počítače za typem uzlu. 

Chcete-li odebrat typ uzlu, spusťte rutinu [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) .  Typ uzlu musí být stříbrná nebo zlatá [úroveň odolnosti][durability]  . rutina odstraní sadu škálování přidruženou k typu uzlu a její dokončení nějakou dobu trvá.  Pak na všech uzlech spusťte rutinu [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) , která se má odebrat. tím se odstraní stav uzlu a z clusteru se odeberou uzly. Pokud na uzlech existují služby, pak se služby nejprve přesunou na jiný uzel. Pokud Správce clusteru nenašel uzel pro repliku nebo službu, operace je zpožděná nebo blokovaná.

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
Po vytvoření clusteru Service Fabric můžete škálovat typ uzlu clusteru vertikálně (změnit prostředky uzlů) nebo upgradovat operační systém typu virtuálních počítačů tak, že nahradíte původní typ uzlu novým typem uzlu (s aktualizovanou SKU virtuálního počítače nebo image operačního systému). Další podrobnosti najdete v tématu věnovaném [horizontálnímu navýšení kapacity typu uzlu Azure Service Fabric](service-fabric-scale-up-primary-node-type.md).

> [!IMPORTANT]
> Nikdy nepokusit se o místní změnu pro SKU virtuálního počítače nebo image operačního systému, což je nebezpečná operace, která není podporována.

Pokud to možné není, můžete vytvořit nový cluster a [Obnovit stav aplikace](service-fabric-reliable-services-backup-restore.md) (Pokud je k dispozici) z původního clusteru. Nemusíte obnovovat žádný stav systémové služby. Při nasazení aplikací do nového clusteru se znovu vytvoří. Pokud jste ve svém clusteru právě spustili bezstavové aplikace, pak všechny vaše aplikace nasadíte do nového clusteru, takže nemusíte nic obnovovat.

### <a name="update-the-template"></a>Aktualizace šablony

[Exportujte šablonu a soubor parametrů](#export-the-template-for-the-resource-group) ze skupiny prostředků pro nejnovější nasazení.  Otevřete *parameters.jsv* souboru.  Pokud jste cluster nasadili pomocí [ukázkové šablony][template] v tomto kurzu, existují v clusteru tři typy uzlů.  

Velikost virtuálních počítačů v druhém typu uzlu je nastavena v parametru *vmNodeType1Size* .  Změňte hodnotu parametru *vmNodeType1Size* z Standard_D2_V2 na [Standard_D3_V2](../virtual-machines/dv2-dsv2-series.md), což zdvojnásobuje prostředky každé instance virtuálního počítače.

SKU virtuálního počítače pro všechny tři typy uzlů je nastaveno v parametru *vmImageSku* .  Změnou SKU virtuálního počítače typu uzel by se měla přecházet s opatrností a nedoporučuje se pro primární typ uzlu.

### <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony
Uloží všechny změny *template.jsv* a *parameters.jsna* soubory.  Aktualizovanou šablonu nasadíte spuštěním následujícího příkazu:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Nebo následující příkaz Azure CLI:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidávání a odebírání uzlů (horizontální navýšení kapacity a horizontální navýšení kapacity)
> * Přidávání a odebírání typů uzlů (horizontální navýšení kapacity a horizontální navýšení kapacity)
> * Zvýšení prostředků uzlu (horizontální navýšení kapacity)

Dále se v následujícím kurzu dozvíte, jak upgradovat modul runtime clusteru.
> [!div class="nextstepaction"]
> [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
