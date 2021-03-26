---
title: Nasazení clusteru napříč Zóny dostupnosti
description: Naučte se, jak vytvořit cluster Azure Service Fabric napříč Zóny dostupnosti.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: a49fd6f97a9130fa0369d2a36cdc38e59613afc1
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105544379"
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

Diagram znázorňující diagram architektury dostupnosti služby Azure Service Fabric ![ , který zobrazuje architekturu zóny dostupnosti azure Service Fabric.][sf-architecture]

Ukázkový seznam uzlů, který popisuje formáty FD/UD ve zónách pokrývání virtuálních počítačů s možností horizontálního navýšení kapacity

 ![Ukázkový seznam uzlů, který popisuje formáty FD/UD ve zónách pro pokrývání kapacity virtuálních počítačů.][sf-multi-az-nodes]

**Distribuce replik služby mezi zónami**: když je služba nasazená v uzlu NodeType, které jsou pokrývání zón, repliky se umístí, aby se zajistilo, že se budou v samostatných zónách nakládat. To je zajištěno, že doména selhání na uzlech v každém z těchto nodeType je nakonfigurována s informacemi o zóně (tj. FD = FD:/zóna 1/1 atd.). Například: u 5 replik nebo instancí služby bude distribuce 2-2-1 a modul runtime se pokusí zajistit stejnou distribuci v rámci AZs.

**Konfigurace repliky uživatelské služby**: stavové služby uživatelů nasazené u NodeType zóny pro vzájemné dostupnosti by měly být konfigurované pomocí této konfigurace: počet replik s cílem = 9, minimum = 5. Tato konfigurace pomůže službě pracovat i v případě, že jedna zóna přestane fungovat, protože 6 replik bude v ostatních dvou zónách i nadále. Upgrade aplikace v takovém scénáři také projde.

**ReliabilityLevel clusteru**: definuje počet počátečních uzlů v clusteru a také velikost repliky systémových služeb. Vzhledem k tomu, že nastavení zóny pro různé dostupnosti má větší počet uzlů, které jsou rozdělené mezi zóny, aby bylo možné zajistit odolnost zóny, vyšší hodnota spolehlivosti zajistí, že uzel bude mít víc uzlů a repliky systémových služeb a jsou rovnoměrně distribuované mezi zónami, takže v případě selhání zóny zůstane cluster a systémové služby neovlivněné. "ReliabilityLevel = Platinum" zajistí, aby se v každé zóně rozšířily 9 počátečních uzlů do zón se 3 semeny v každé zóně, takže to je doporučení pro nastavení zóny vzájemné dostupnosti.

**Scénář snížení zóny**: když dojde k výpadku zóny, budou se všechny uzly v této zóně zobrazovat jako nefunkční. Repliky služby na těchto uzlech budou taky mimo provoz. Vzhledem k tomu, že existují repliky v ostatních zónách, bude služba nadále odpovídat primárním replikám, které přestanou fungovat v zónách, které fungují. Služby se zobrazí ve stavu upozornění, protože počet cílových replik ještě není dosažený a vzhledem k tomu, že počet virtuálních počítačů je stále více než minimální Cílová velikost repliky. Následně Service Fabric Nástroj pro vyrovnávání zatížení zavede repliky v pracovních zónách tak, aby odpovídaly nakonfigurovanému počtu cílových replik. V tomto okamžiku se služby zobrazí v pořádku. Když zóna, která byla mimo provoz, se zavede na zálohování, budou všechny repliky služby znovu rozloženy napříč všemi zónami.

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

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>Tisk Povolit více zón dostupnosti v jedné sadě škálování virtuálních počítačů

Dřív zmíněné řešení používá jedno nodeType na AZ. Následující řešení umožní uživatelům nasazovat 3 AZ do stejného uzlu nodeType.

**Vzhledem k tomu, že tato funkce je aktuálně ve verzi Preview, není v současné době podporovaná pro produkční scénáře.**

[Zde](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure)je uvedena úplná vzorová šablona.

![Architektura zón dostupnosti služby Azure Service Fabric][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>Konfigurace zón v sadě škálování virtuálního počítače
Pokud chcete povolit zóny v sadě škálování virtuálního počítače, musíte do prostředku sady škálování virtuálního počítače zahrnout následující tři hodnoty.

* První hodnotou je vlastnost **Zones** , která určuje zóny dostupnosti přítomná v sadě škálování virtuálního počítače.
* Druhá hodnota je vlastnost "singlePlacementGroup", která musí být nastavena na hodnotu true. **Sada škálování rozložené přes 3 AZ může škálovat virtuální počítače až 300 i s "singlePlacementGroup = true".**
* Třetí hodnota je "zoneBalance", která zajišťuje striktní vyrovnávání zóny. Tato hodnota by měla být "true". Tím se zajistí, že distribuce virtuálních počítačů napříč zónami nebudou vyvážené, takže pokud dojde k výpadku jedné ze zón, mají ostatní dvě zóny dostatek virtuálních počítačů, aby se zajistilo, že cluster bude nadále běžet bez přerušení. Cluster s nevyváženou distribucí virtuálních počítačů nemusí zůstat v situaci, kdy by tato zóna mohla mít většinu virtuálních počítačů. Nevyvážená distribuce virtuálních počítačů mezi zónami taky vede k problémům souvisejícím s umístěním služby & aktualizace infrastruktury se zablokují. Přečtěte si o [zoneBalancing](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing).
* Přepsání FaultDomain a UpgradeDomain není nutné konfigurovat.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1", "2", "3"],
    "properties": {
        "singlePlacementGroup": "true",
        "zoneBalance": true
    }
}
```

>[!NOTE]
> * **Clustery Service Fabric musí mít minimálně jeden primární typ nodeType. DurabilityLevel primárních nodeType by měly být stříbrné nebo vyšší.**
> * Skupina AZ pokrývá virtuální počítač Scale by měla být nakonfigurovaná s minimálně 3 zónami dostupnosti bez ohledu na durabilityLevel.
> * AZ pokrývání sady škálování virtuálních počítačů s trvanlivostí stříbra (nebo vyšší) by měl mít minimálně 15 virtuálních počítačů.
> * AZ pokrývání škály virtuálních počítačů s bronzovou trvanlivostí by měl mít minimálně 6 virtuálních počítačů.

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>Povolení podpory pro více zón v Service Fabric nodeType
Pro podporu více zón dostupnosti musí být povolený Service Fabric nodeType.

* První hodnota je **multipleAvailabilityZones** , která by měla být pro NodeType nastavena na hodnotu true.
* Druhá hodnota je **sfZonalUpgradeMode** a je volitelná. Tuto vlastnost nelze upravit, pokud je v clusteru již přítomen typ NodeType s více AZ 's.
  Vlastnost řídí logické seskupení virtuálních počítačů v upgradovacích doménách.
  **Pokud je hodnota nastavena na Parallel (paralelní):** Virtuální počítače pod uzlem NodeType budou seskupeny na UDs ignorování informací o zóně v 5 UDs. Výsledkem bude, že se UD0 napříč všemi zónami, aby se Upgradoval ve stejnou dobu. Tento režim nasazení je rychlejší pro upgrady, ale nedoporučuje se, protože se jedná o pravidla SDP, ve kterých je uvedeno, že by se aktualizace měly používat jenom v jedné zóně.
  **Pokud je hodnota vynechána nebo nastavena na "hierarchické":** Virtuální počítače se seskupí tak, aby odrážely rozdělení do více než 15 UDs. Každá ze 3 zón bude mít 5 UDs. Tím se zajistí, že se zóna aktualizace směřuje do další zóny až po dokončení 5 UDs v první zóně, pomalu přes 15 UDs (3 zóny, 5 UDs), což je bezpečnější z perspektivy clusteru a aplikace uživatele.
  Tato vlastnost definuje pouze chování upgradu pro ServiceFabric aplikace a upgrady kódu. Základní upgrady sady škálování virtuálního počítače budou pořád paralelně ve všech AZ 's.
  Tato vlastnost nebude mít žádný vliv na distribuci UD pro typy uzlů, u kterých není povoleno více zón.
* Třetí hodnota je **vmssZonalUpgradeMode = Parallel**. Jedná se o *povinnou* vlastnost, která se má nakonfigurovat v clusteru, pokud je přidaný typ NodeType s více AZs. Tato vlastnost definuje režim upgradu pro aktualizace sady škálování virtuálních počítačů, ke kterým dojde paralelně ve všech AZ 's in.
  Tato vlastnost se teď dá nastavit jenom paralelně.
* ApiVersion prostředku clusteru Service Fabric by měl být "2020-12-01-Preview" nebo vyšší.
* Verze kódu clusteru by měla být "7.2.445" nebo vyšší.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "location": "[parameters('clusterLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
    ],
    "properties": {
        "reliabilityLevel": "Platinum",
        "SFZonalUpgradeMode": "Hierarchical",
        "VMSSZonalUpgradeMode": "Parallel",
        "nodeTypes": [
          {
                "name": "[parameters('vmNodeType0Name')]",
                "multipleAvailabilityZones": true,
          }
        ]
}
```

>[!NOTE]
> * Veřejné IP adresy a Load Balancer prostředky by měly používat standardní SKU, jak je popsáno výše v článku.
> * vlastnost "multipleAvailabilityZones" v nodeType lze definovat pouze v době vytváření nodeType a nelze ji změnit později. Proto se pomocí této vlastnosti nedají konfigurovat existující nodeType.
> * Pokud je hodnota "sfZonalUpgradeMode" vynechána nebo nastavena na "hierarchické", nasazení clusteru a aplikací bude pomalejší, protože v clusteru jsou k dispozici další domény upgradu. Je důležité správně upravit časový limit zásad upgradu, aby zahrnoval dobu trvání upgradu pro 15 domén upgradu. Zásady upgradu pro aplikaci i cluster by se měly aktualizovat, aby se zajistilo, že nasazení nepřekračuje časový limit nasazení 12hours prostředků Azure Serbice. To znamená, že nasazení by nemělo mít více než 12hours pro 15UDs, tj. nemělo by to trvat více než 40 min/UD.
> * Nastavte cluster **reliabilityLevel = Platinum** , aby se zajistilo, že cluster bude zachován v rámci scénáře s jednou zónou.

>[!NOTE]
> Pro osvědčené postupy doporučujeme sfZonalUpgradeMode nastavit na hierarchické nebo vynechat. Nasazení bude následovat po rozbalení virtuálních počítačů, které mají vliv na menší množství replik a/nebo instancí, čímž budou bezpečnější.
> SfZonalUpgradeMode sadu použijte k paralelnímu nastavení, pokud je rychlost nasazení nastavena jako priorita, nebo pokud je v typu uzlu spuštěno pouze bezstavové úlohy s více než jednou funkcí AZ. Výsledkem bude, že UD probíhají paralelně ve všech AZ 's.

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>Migrace na typ uzlu s více Zóny dostupnosti
Pro všechny scénáře migrace je nutné přidat nový uzel nodeType, který bude mít podporu více zón dostupnosti. Existující uzel nodeType nejde migrovat na podporu více zón.
V [tomto](./service-fabric-scale-up-primary-node-type.md) článku se dozvíte o podrobných krocích při přidávání nového NodeType a také o přidání dalších prostředků potřebných pro nový typ NodeType, jako jsou prostředky IP a disrovnávání zatížení sítě. Stejný článek také popisuje, jak teď vyřadit existující uzel nodeType po přidání uzlu nodeType s více zónami dostupnosti do clusteru.

* Migrace z protokolu nodeType, který používá základní prostředky a a IP: Tento postup je již [zde](#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip) popsán pro řešení s jedním typem uzlu na AZ. 
    V případě nového typu uzlu jediným rozdílem je, že je k dispozici pouze 1 sada škálování virtuálního počítače a 1 uzel NodeType pro všechny AZ 's 1 a New na AZ.
* Migrace z uzlu nodeType, který používá standardní skladové položky SKU a prostředků IP s NSG: postupujte stejným způsobem jako v případě, že není nutné přidávat nové prostředky s jednotkou, IP a NSG a že se stejné prostředky dají znovu použít v novém uzlu nodeType.


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sf-multi-az-nodes]: ./media/service-fabric-cross-availability-zones/sf-multi-az-nodes.png