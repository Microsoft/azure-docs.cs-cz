---
title: Nasazení clusteru Azure Service Fabric napříč zónami dostupnosti | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit cluster Azure Service Fabric napříč zónami dostupnosti.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: b664c3d655ab45c89a65a0aea31622f57ddc8d9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077452"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Nasazení clusteru Azure Service Fabric napříč zónami dostupnosti
Zóny dostupnosti v Azure je vysoká dostupnost služeb, které chrání vaše aplikace a data z datacenter selhání. Zóny dostupnosti je jedinečný fyzické umístění vybavených nezávislým napájením, chlazení a sítě v rámci oblasti Azure.

Service Fabric podporuje clustery, které jsou rozmístěny napříč zónami dostupnosti nasazením typy uzlů, které jsou připojené do určité zóny. Tím se zajistí vysokou dostupnost vašich aplikací. Zóny dostupnosti Azure jsou dostupné pouze ve vybraných oblastech. Další informace najdete v tématu [Přehled zón dostupnosti Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).

Ukázkové šablony jsou k dispozici: [Service Fabric pro různé šablony zóny dostupnosti](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Doporučená topologie pro primární typ uzlu clusterů Azure Service Fabric pokrývání uzlů napříč zónami dostupnosti
Cluster Service Fabric distribuované napříč zónami dostupnosti zajistí vysokou dostupnost clusteru ve stavu. Cluster Service Fabric rozložit napříč zónami, musíte vytvořit typ primárního uzlu v každé zóně dostupnosti oblast nepodporuje. Počáteční uzly to bude distribuovat rovnoměrně napříč jednotlivých typů primárního uzlu.

Doporučená topologie pro primární typ uzlu vyžaduje prostředcích níže:

* Úroveň spolehlivosti clusteru nastavena na hodnotu Platinum.
* Tři typy uzlů označený jako primární.
    * Každý typ uzlu musí být mapováno na svůj vlastní škálovací sadu virtuálních počítačů umístěné v různých oblastech.
    * Každou škálovací sadu virtuálních počítačů by měl mít aspoň pět uzlů (Silver odolnosti).
* Jeden prostředek veřejné IP pomocí standardní SKU.
* Jeden prostředek nástroje pro vyrovnávání zatížení pomocí standardní SKU.
* Skupina NSG odkazuje na podsíť, ve kterém nasadíte škálovací sady virtuálních počítačů.

>[!NOTE]
> Škálovací sada virtuálních počítačů, které jeden umístění skupiny vlastnost musí být nastavena na hodnotu true, protože Service Fabric nepodporuje jeden virtuální počítač škálovací sady, která zahrnuje zóny.

 ![Architektura zóny dostupnosti prostředků infrastruktury služby Azure][sf-architecture]

## <a name="networking-requirements"></a>Požadavky na síť
### <a name="public-ip-and-load-balancer-resource"></a>Veřejná IP adresa a prostředek nástroje pro vyrovnávání zatížení
Povolit zóny na škálovací sadu virtuálních počítačů nastavenou prostředků, nástroje pro vyrovnávání zatížení a prostředek IP odkazuje této škálovací sadě virtuálních počítačů musí obě být používají *standardní* SKU. Vytvoření nástroje pro vyrovnávání zatížení nebo IP prostředku bez vlastností SKU se vytvoří základní SKU, která nepodporuje zóny dostupnosti. Nástroj pro vyrovnávání zatížení standardní SKU bude blokovat veškerý provoz z vnějšku ve výchozím nastavení; Povolit mimo provoz, se musí nasadit skupinu NSG k podsíti.

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
> Není možné provést změnu na místě skladové položky na veřejné IP adresy a zatížení prostředků nástroje pro vyrovnávání. Pokud provádíte migraci z existujících prostředků, které mají základní SKU, naleznete v části migrace z tohoto článku.

### <a name="virtual-machine-scale-set-nat-rules"></a>Škálovací sady virtuálních počítačů pravidla NAT
Nástroje pro vyrovnávání zatížení příchozích pravidel NAT by měl odpovídat fondy NAT ze škálovací sady virtuálních počítačů. Každá sada škálování virtuálního počítače musí mít jedinečný fondu příchozího překladu adres.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standardní SKU nástroje pro vyrovnávání zatížení odchozí pravidla
Standardní nástroj pro vyrovnávání zatížení a standardní veřejnou IP adresu představí nové možnosti a různé chování odchozí připojení ve srovnání s použitím základní SKU. Pokud chcete odchozí připojení při práci s standardní SKU, je nutné explicitně definovat ho pomocí standardní veřejné IP adresy nebo veřejného Load Balanceru úrovně Standard. Další informace najdete v tématu [odchozí připojení](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) a [Azure Load balancer úrovně Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> Standardní šablona odkazuje na skupinu zabezpečení sítě, které umožňuje veškerý odchozí provoz ve výchozím nastavení. Příchozí provoz je omezený na porty, které jsou požadovány pro operace správy Service Fabric. Pravidla skupiny zabezpečení sítě můžete upravit tak, aby splňovaly vaše požadavky.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Nastavení povolení zóny na škálovací sadu virtuálních počítačů
Chcete-li povolit zóny, na škálovací sadu virtuálních počítačů můžete nastavit musí obsahovat následující tři hodnoty v prostředku škálovací sady virtuálního počítače.

* První hodnota **zóny** vlastnost, která určuje, které zónu dostupnosti, nasadí škálovací sadu virtuálních počítačů.
* Druhá hodnota představuje vlastnost "singlePlacementGroup", který musí být nastaven na hodnotu true.
* Třetí hodnota je vlastnost "faultDomainOverride" v rozšíření škálovací sady virtuálních počítačů Service Fabric. Hodnota této vlastnosti by měly zahrnovat oblasti a zóny, v níž budou umístěny této škálovací sadě virtuálních počítačů. Příklad: "faultDomainOverride": "eastus/az1" všechny škálovací sady virtuálních počítačů prostředky musí být umístěné ve stejné oblasti pro různé oblasti podpory nemají clustery Azure Service Fabric.

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
        "publisher": "Microsoft.Azure.ServiceFabric.Test",
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
            "faultDomainOverride": "eastus/az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Povolení více primární typy uzlů v clusteru Service Fabric prostředku
Pokud chcete nastavit jeden nebo více typů uzlů jako primární v prostředku clusteru, nastavte vlastnost "isPrimary" na hodnotu "true". Při nasazování clusteru Service Fabric napříč zónami dostupnosti, měli byste mít tři typy uzlů v různých oblastech.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrace na použití zón dostupnosti z clusteru pomocí základní SKU nástroje pro vyrovnávání zatížení a základní SKU IP
Chcete-li Migrace clusteru, který se pomocí nástroje pro vyrovnávání zatížení a IP základní skladovou Položku, musíte nejdřív vytvořit zcela nový nástroj pro vyrovnávání zatížení a IP prostředku pomocí standardního SKU. Není možné k aktualizaci těchto prostředků v místě.

Nové LB a IP by měly odkazovat v nové různé typy uzlů zónu dostupnosti, které chcete použít. V příkladu výše, tři nové virtuální počítače škálovací sady prostředků byly přidány do zóny 1, 2 a 3. Tyto virtuální počítače škálovací sady odkaz na nově vytvořený LB a IP a jsou označeny jako typy primárního uzlu v prostředku clusteru Service Fabric.

Pokud chcete začít, je potřeba přidat nové prostředky do existující šablonu Resource Manageru. Tyto prostředky zahrnují:
* Veřejný IP prostředek pomocí standardní SKU.
* Prostředek nástroje pro vyrovnávání zatížení pomocí standardní SKU.
* Skupina NSG odkazuje na podsíť, ve kterém nasadíte škálovací sady virtuálních počítačů.
* Tři typy uzlů označený jako primární.
    * Každý typ uzlu musí být mapováno na svůj vlastní škálovací sadu virtuálních počítačů umístěné v různých oblastech.
    * Každou škálovací sadu virtuálních počítačů by měl mít aspoň pět uzlů (Silver odolnosti).

Příkladem těchto prostředků najdete v [Ukázková šablona](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Po dokončení nasazení prostředky můžete začít zakázat uzlů v primární typ uzlu z původního clusteru. Jako uzly jsou zakázané, systémové služby migrovat na nový typ primárního uzlu, které nasadil v předchozím kroku.

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

Jakmile se uzly jsou všechny zakázané, při primární typ uzlu, který se pak rozdělí mezi zónami poběží systémových služeb. Následně můžete odebrat zakázané uzlů z clusteru. Po odebrání uzlů původní IP nástroje pro vyrovnávání zatížení, můžete odebrat a škálovací sady virtuálních počítačů prostředky.

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

Potom byste měli odebrat odkazy na tyto prostředky z šablony Resource Manageru, který má nasazený.

V posledním kroku bude zahrnovat aktualizuje název DNS a veřejnou IP adresu.

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
