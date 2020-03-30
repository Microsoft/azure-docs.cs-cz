---
title: Nasazení clusteru napříč zónami dostupnosti
description: Zjistěte, jak vytvořit cluster Azure Service Fabric napříč zónami dostupnosti.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 6da9517f822c9c157d26a1bda8dab2c694b08b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609974"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Nasazení clusteru Azure Service Fabric napříč zónami dostupnosti
Zóny dostupnosti v Azure je nabídka s vysokou dostupností, která chrání vaše aplikace a data před selháními datových center. Zóna dostupnosti je jedinečné fyzické umístění vybavené nezávislým napájením, chlazením a sítí v rámci oblasti Azure.

Service Fabric podporuje clustery, které se rozprostírají napříč zónami dostupnosti nasazením typů uzlů, které jsou připnuté k určitým zónám. Tím zajistíte vysokou dostupnost vašich aplikací. Zóny dostupnosti Azure jsou k dispozici jenom ve vybraných oblastech. Další informace najdete v [tématu Přehled zón dostupnosti Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).

Ukázkové šablony jsou k dispozici: [Šablona zóny křížové dostupnosti Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Doporučená topologie pro primární typ uzlu clusterů Azure Service Fabric zahrnující chod y zón y dostupnosti
Cluster Service Fabric distribuovaný napříč zónami dostupnosti zajišťuje vysokou dostupnost stavu clusteru. Chcete-li prosáknout cluster Service Fabric napříč zónami, musíte vytvořit primární typ uzlu v každé zóně dostupnosti podporované oblastí. Tím se budou rovnoměrně distribuovat uzly osiva napříč jednotlivými typy primárních uzlů.

Doporučená topologie pro typ primárního uzlu vyžaduje níže uvedené prostředky:

* Úroveň spolehlivosti clusteru nastavená na platinu.
* Tři typy uzlů označené jako primární.
    * Každý typ uzlu by měl být mapován na vlastní škálovací sadu virtuálních strojů umístěnou v různých zónách.
    * Každá škálovací sada virtuálního počítače by měla mít alespoň pět uzlů (silver durability).
* Jeden veřejný prostředek IP pomocí standardní sku.
* Jeden prostředek pro vyrovnávání zatížení pomocí standardní skladové položky.
* Skupina nsg odkazuje podsítě, ve kterém nasadíte škálovací sady virtuálních strojů.

>[!NOTE]
> Vlastnost skupiny škálování virtuálního počítače musí být nastavena na hodnotu true, protože service fabric nepodporuje váhovou sadu jednoho virtuálního počítače, která zahrnuje zóny.

 ![Architektura zóny dostupnosti infrastruktury služby Azure][sf-architecture]

## <a name="networking-requirements"></a>Požadavky na vytváření sítí
### <a name="public-ip-and-load-balancer-resource"></a>Veřejný prostředek pro vyrovnávání zatížení a vyrovnávání zatížení
Chcete-li povolit vlastnost zóny na prostředku škálovací sady virtuálních počítačů, musí nástroj pro vyrovnávání zatížení a prostředek IP, na který odkazuje tato škálovací sada virtuálního počítače, používat *standardní* skladovou položku. Vytvořením prostředku pro vyrovnávání zatížení nebo prostředku IP bez vlastnosti skladové položky vytvoříte základní skladovou položku, která nepodporuje zóny dostupnosti. Standardní skladová položka pro vyrovnávání zatížení bude ve výchozím nastavení blokovat veškerý provoz zvenčí. aby byl povolen vnější provoz, musí být do podsítě nasazen soubor NSG.

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
> Není možné provést změnu skladové položky na místě ve veřejné IP a prostředky vykladače zatížení. Pokud migrujete z existujících prostředků, které mají základní skladovou položku, naleznete v části migrace v tomto článku.

### <a name="virtual-machine-scale-set-nat-rules"></a>Pravidla škálování virtuálních strojů nasadace nařeč
Pravidla příchozího nat nástroje pro vyrovnávání zatížení by měla odpovídat fondům NAT ze škálovací sady virtuálních strojů. Každá škálovací sada virtuálních strojů musí mít jedinečný fond příchozích NAT.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standardní odchozí pravidla souku pro vyrovnávání zatížení
Standardní vyrovnávání zatížení a standardní veřejné IP zavést nové schopnosti a různé chování odchozí připojení ve srovnání s použitím základních skum. Pokud chcete odchozí připojení při práci se standardními sku, musíte explicitně definovat buď se standardními veřejnými IP adresami nebo standardním veřejným vyvyčažantem zatížení. Další informace naleznete [v tématu Odchozí připojení](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) a Azure Standard Load [Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> Standardní šablona odkazuje na soubor zabezpečení sítě, který ve výchozím nastavení umožňuje veškerý odchozí provoz. Příchozí provoz je omezen na porty, které jsou požadovány pro operace správy Service Fabric. Pravidla nsg lze upravit tak, aby vyhovovala vašim požadavkům.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Povolení zón ve škálovací sadě virtuálních strojů
Chcete-li povolit zónu, musíte na škálovací sadě virtuálních strojů zahrnout následující tři hodnoty do prostředku škálovací sady virtuálních strojů.

* První hodnota je **vlastnost zóny,** která určuje, do které zóny dostupnosti bude škálovací sada virtuálního počítače nasazena.
* Druhá hodnota je vlastnost "singlePlacementGroup", která musí být nastavena na hodnotu true.
* Třetí hodnota je vlastnost "faultDomainOverride" v rozšíření škálovací sady virtuálního počítače Service Fabric. Hodnota pro tuto vlastnost by měla zahrnovat oblast a zónu, do které bude umístěna tato škálovací sada virtuálního počítače. Příklad: "faultDomainOverride": "eastus/az1" Všechny prostředky škálovací sady virtuálních strojů musí být umístěny ve stejné oblasti, protože clustery Azure Service Fabric nemají podporu pro oblast mezi oblastmi.

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Povolení více typů primárních uzlů v prostředku clusteru Service Fabric
Chcete-li nastavit jeden nebo více typů uzlů jako primární v prostředku clusteru, nastavte vlastnost "isPrimary" na hodnotu true. Při nasazování clusteru Service Fabric napříč zónami dostupnosti byste měli mít tři typy uzlů v různých zónách.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrace na použití zón dostupnosti z clusteru pomocí základního vykladače zatížení skladové položky a základní ip adresy skladové položky
Chcete-li migrovat cluster, který používal vykladač zatížení a IP adresu se základní skladovou položkou, musíte nejprve vytvořit zcela nový prostředek pro vyrovnávání zatížení a prostředek IP pomocí standardní skladové položky. Tyto prostředky není možné aktualizovat na místě.

Nové LB a IP by měl odkazovat v nové typy uzlů zóny křížové dostupnosti, které chcete použít. Ve výše uvedeném příkladu byly přidány tři nové prostředky škálovací sady virtuálních strojů v zónách 1,2 a 3. Tyto škálovací sady virtuálních počítačů odkazují na nově vytvořené LB a IP adresy a jsou označeny jako primární typy uzlů v prostředku clusteru Service Fabric.

Chcete-li začít, budete muset přidat nové prostředky do existující šablony Správce prostředků. Mezi tyto zdroje patří:
* Veřejný prostředek IP pomocí standardní skladové položky.
* Prostředek pro vyrovnávání zatížení pomocí standardní skladové položky.
* Skupina nsg odkazuje podsítě, ve kterém nasadíte škálovací sady virtuálních strojů.
* Tři typy uzlů označené jako primární.
    * Každý typ uzlu by měl být mapován na vlastní škálovací sadu virtuálních strojů umístěnou v různých zónách.
    * Každá škálovací sada virtuálního počítače by měla mít alespoň pět uzlů (silver durability).

Příklad těchto zdrojů naleznete v [vzorové šabloně](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Po dokončení nasazení prostředků můžete začít zakázat uzly v primárním typu uzlu z původního clusteru. Vzhledem k tomu, že uzly jsou zakázány, systémové služby budou migrovat na nový typ primárního uzlu, který byl nasazen v kroku výše.

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

Jakmile jsou všechny uzly zakázány, systémové služby budou spuštěny na typu primárního uzlu, který je rozložen mezi zóny. Zakázané uzly pak můžete z clusteru odebrat. Po odebrání uzlů můžete odebrat původní prostředky ip, nástroje pro vyrovnávání zatížení a škálovací sady virtuálních počítačů.

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

Potom byste měli odebrat odkazy na tyto prostředky ze šablony Správce prostředků, kterou jste nasadili.

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
