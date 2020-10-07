---
title: Nasazení clusteru napříč Zóny dostupnosti
description: Naučte se, jak vytvořit cluster Azure Service Fabric napříč Zóny dostupnosti.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 56f7224d93293a0a26d09692996d2c4a4ace344b
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803734"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Nasazení clusteru Azure Service Fabric napříč Zóny dostupnosti
Zóny dostupnosti v Azure je nabídka s vysokou dostupností, která chrání vaše aplikace a data při selhání datacentra. Zóna dostupnosti je jedinečné fyzické umístění vybavené nezávislým napájením, chlazením a sítí v oblasti Azure.

Service Fabric podporuje clustery, které jsou rozloženy mezi Zóny dostupnosti nasazením typů uzlů, které jsou připnuté na konkrétní zóny. Tím zajistíte vysokou dostupnost vašich aplikací. Zóny dostupnosti Azure jsou k dispozici pouze ve vybraných oblastech. Další informace najdete v tématu [přehled zóny dostupnosti Azure](../availability-zones/az-overview.md).

K dispozici jsou ukázkové šablony: [Service Fabric šablonu zóny pro různé dostupnosti](https://github.com/Azure-Samples/service-fabric-cluster-templates) .

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Doporučená topologie pro typ primárního uzlu clusterů Azure Service Fabric rozložená mezi Zóny dostupnosti
Cluster Service Fabric distribuovaný napříč Zóny dostupnosti zajišťuje vysokou dostupnost stavu clusteru. Pokud chcete Service Fabric cluster mezi zónami, musíte v každé zóně dostupnosti, kterou oblast podporuje, vytvořit primární typ uzlu. Tím dojde k rovnoměrné distribuci dosazení uzlů napříč každým primárním typem uzlu.

Doporučená topologie pro typ primárního uzlu vyžaduje prostředky uvedené níže:

* Úroveň spolehlivosti clusteru je nastavena na hodnotu Platina.
* Tři typy uzlů označené jako primární.
    * Každý typ uzlu by měl být namapován na svou vlastní sadu škálování virtuálního počítače umístěnou v různých zónách.
    * Každá sada škálování virtuálního počítače musí mít aspoň pět uzlů (odolnost stříbrného).
* Jeden prostředek veřejné IP adresy, který používá standardní SKU.
* Jeden Load Balancer prostředek pomocí standardní SKU.
* NSG, na který odkazuje podsíť, ve které nasadíte služby Virtual Machine Scale Sets.

>[!NOTE]
> Vlastnost skupiny s jedním umístěním sady škálování virtuálního počítače musí být nastavená na hodnotu true, protože Service Fabric nepodporuje jednu sadu škálování virtuálního počítače, která zahrnuje zóny.

 ![Architektura zón dostupnosti služby Azure Service Fabric][sf-architecture]

## <a name="networking-requirements"></a>Požadavky na síť
### <a name="public-ip-and-load-balancer-resource"></a>Prostředek veřejné IP adresy a Load Balancer
Pokud chcete povolit vlastnost Zones na prostředku sady škálování virtuálních počítačů, musí se vyrovnávání zatížení a prostředek IP, na který odkazuje tato sada škálování virtuálních počítačů, používat *standardní* SKU. Vytvořením nástroje pro vyrovnávání zatížení nebo prostředku IP bez vlastnosti SKU se vytvoří základní skladová položka, která nepodporuje Zóny dostupnosti. Nástroj pro vyrovnávání zatížení Standard SKU bude ve výchozím nastavení blokovat veškerý provoz mimo rámec. aby bylo možné povolení mimo provoz, musí být do podsítě nasazený NSG.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> U prostředků veřejné IP adresy a nástroje pro vyrovnávání zatížení není možné provést místní změnu skladové položky. Pokud migrujete ze stávajících prostředků, které mají základní SKU, přečtěte si část migrace v tomto článku.

### <a name="virtual-machine-scale-set-nat-rules"></a>Pravidla překladu adres NAT služby Virtual Machine Scale set
Příchozí pravidla NAT nástroje pro vyrovnávání zatížení by se měla shodovat s fondy NAT ze sady škálování virtuálních počítačů. Každá sada škálování virtuálního počítače musí mít jedinečný fond příchozího překladu adres (NAT).

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Odchozí pravidla Load Balancer Standard SKU
Standard Load Balancer a standardní veřejná IP adresa přináší do odchozího připojení nové možnosti a různá chování při porovnání s použitím základních SKU. Pokud chcete odchozí připojení při práci se standardními SKU, musíte ho explicitně definovat buď se standardními veřejnými IP adresami, nebo se standardními veřejnými Load Balancer. Další informace najdete v tématu [odchozí připojení](../load-balancer/load-balancer-outbound-connections.md) a [Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md).

>[!NOTE]
> Standardní šablona odkazuje na NSG, který ve výchozím nastavení povoluje veškerý odchozí provoz. Příchozí provoz je omezen na porty, které jsou požadovány pro Service Fabric operace správy. Pravidla NSG se dají upravit tak, aby splňovala vaše požadavky.

>[!NOTE]
> Všechny Service Fabric clustery, které využívají standardní SKU, vyžadují, aby každý typ uzlu měl pravidlo umožňující odchozí přenosy na portu 443. To je nezbytné pro dokončení instalace clusteru a jakékoli nasazení bez takového pravidla selže.


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Povolení zón v sadě škálování virtuálního počítače
Pokud chcete povolit zónu, v sadě škálování virtuálního počítače musíte do prostředku sady škálování virtuálního počítače zahrnout následující tři hodnoty.

* První hodnotou je vlastnost **zóny** , která určuje, do které zóny dostupnosti bude sada škálování virtuálního počítače nasazena.
* Druhá hodnota je vlastnost "singlePlacementGroup", která musí být nastavena na hodnotu true.
* Třetí hodnotou je vlastnost "faultDomainOverride" v rozšíření sady škálování virtuálního počítače v Service Fabric. Hodnota této vlastnosti by měla zahrnovat pouze zónu, ve které bude tato sada škálování virtuálního počítače umístěna. Příklad: "faultDomainOverride": "az1" všechny prostředky sady škálování virtuálního počítače musí být umístěné ve stejné oblasti, protože clustery Azure Service Fabric nepodporují mezioblasti.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Povolení více typů primárních uzlů v prostředku Service Fabric clusteru
Chcete-li v prostředku clusteru nastavit jeden nebo více typů uzlů jako primární, nastavte vlastnost "primární" na hodnotu "true". Při nasazování clusteru Service Fabric v rámci Zóny dostupnosti byste měli mít tři typy uzlů v různých zónách.

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrace na použití Zóny dostupnosti z clusteru s použitím základní SKU Load Balancer a základní IP adresy SKU
K migraci clusteru, který používal Load Balancer a IP adresu se základní SKU, je nutné nejprve vytvořit zcela nový Load Balancer a prostředek IP pomocí standardní SKU. Tyto prostředky není možné aktualizovat místně.

Nový typ a IP adresa by měly být odkazovány v nových typech uzlů pro oblast vzájemné dostupnosti, které byste chtěli použít. V předchozím příkladu byly přidány tři nové prostředky sady škálování virtuálních počítačů do zón 1, 2 a 3. Tyto sady škálování virtuálních počítačů odkazují na nově vytvořenou odinstalaci a IP adresu a v prostředku clusteru Service Fabric jsou označené jako primární typy uzlů.

Chcete-li začít, budete muset přidat nové prostředky do existující šablony Správce prostředků. Mezi tyto prostředky patří:
* Prostředek veřejné IP adresy, který používá standardní SKU.
* Load Balancer prostředek pomocí standardní SKU.
* NSG, na který odkazuje podsíť, ve které nasadíte služby Virtual Machine Scale Sets.
* Tři typy uzlů označené jako primární.
    * Každý typ uzlu by měl být namapován na svou vlastní sadu škálování virtuálního počítače umístěnou v různých zónách.
    * Každá sada škálování virtuálního počítače musí mít aspoň pět uzlů (odolnost stříbrného).

Příklad těchto prostředků najdete v [ukázkové šabloně](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Po dokončení nasazení prostředků můžete začít s zakázáním uzlů v primárním uzlu typu z původního clusteru. Po zakázání uzlů budou systémové služby migrovány na nový typ primárního uzlu, který byl nasazen v kroku výše.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Po zakázání všech uzlů budou systémové služby spuštěny na primárním typu uzlu, který je rozložen mezi zóny. Zakázané uzly pak můžete odebrat z clusteru. Po odebrání uzlů můžete odebrat původní IP, Load Balancer a prostředky sady škálování virtuálního počítače.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Měli byste pak odebrat odkazy na tyto prostředky ze šablony Správce prostředků, kterou jste nasadili.

Poslední krok bude zahrnovat aktualizaci názvu DNS a veřejné IP adresy.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
