---
title: Nasadit pouze stavové uzly v clusteru Service Fabric
description: Naučte se vytvářet a nasazovat bezstavové typy uzlů v clusteru Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: pepogors
ms.openlocfilehash: 74680f7b56ad98851e2839b53c1f9e92b6c6c23a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030003"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types"></a>Nasazení clusteru Azure Service Fabric s bezstavovým typem uzlu
Typy uzlů Service Fabric přicházejí s podstatou předpokladem, že v některých časových okamžikech mohou být stavové služby umístěny na uzlech. Bezstavové typy uzlů rozšiřují tento předpoklad pro typ uzlu, což umožňuje, aby typ uzlu používal jiné funkce, jako je rychlejší operace škálování, podpora automatických upgradů operačního systému při bronzové odolnosti a škálování na více než 100 uzlů v jedné sadě škálování virtuálního počítače.

* Typy primárních uzlů nejde nakonfigurovat tak, aby byly bezstavové.
* Bezstavové typy uzlů jsou podporovány pouze s úrovní bronzové trvanlivosti.
* Bezstavové uzly jsou podporovány pouze Service Fabric v modulu runtime verze 7.1.409 nebo vyšší.


K dispozici jsou ukázkové šablony: [Service Fabric šablona typů uzlů bez stavů](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>Povolení typů bezstavových uzlů v Service Fabric clusteru
Chcete-li nastavit jeden nebo více typů uzlů jako stav bez stavu v prostředku clusteru, nastavte vlastnost **nestavové** vlastnosti na **hodnotu true**. Při nasazování Service Fabric clusteru s bezstavovým uzlem nemusíte mít v prostředku clusteru minimálně jeden typ primárního uzlu.

* ApiVersion prostředku clusteru Service Fabric by měl být "2020-12-01-Preview" nebo vyšší.

```json
{
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
        "isStateless": false, // Primary Node Types cannot be stateless
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Bronze",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": false,
        "isStateless": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    }    
    ],
}
```

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>Konfiguruje se sada škálování virtuálního počítače pro typy bezstavových uzlů.
Pokud chcete povolit bezstavové typy uzlů, měli byste nakonfigurovat základní prostředek sady škálování virtuálního počítače následujícím způsobem:

* Vlastnost Value  **singlePlacementGroup** , která by měla být nastavena na **hodnotu false** , pokud požadujete škálování na více než 100 virtuálních počítačů.
* **UpgradeMode** sady škálování by měla být nastavená na **vracení**.
* Režim postupného upgradu vyžaduje konfiguraci rozšíření stavu aplikace nebo sondy stavu. Nakonfigurujte sondu stavu s výchozí konfigurací pro bezstavové uzly, jak je navrženo níže. Jakmile se aplikace nasadí do typu uzlu, můžete změnit porty sondy stavu nebo rozšíření stavu tak, aby se sledovaly stav aplikace.

>[!NOTE]
> Při použití automatického škálování u bezstavových uzlů NodeType po operaci horizontálního navýšení kapacity se stav uzlu automaticky nevyčistí. Aby bylo možné vyčistit NodeState uzlů během automatického škálování, je doporučeno použití [Service Fabric pomocníka automatického škálování](https://github.com/Azure/service-fabric-autoscale-helper) .

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Rolling",
          "automaticOSUpgradePolicy": {
            "enableAutomaticOSUpgrade": true
          }
        },
        "platformFaultDomainCount": 5
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
            "durabilityLevel": "Bronze",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
        },
        "typeHandlerVersion": "1.0"
    }
    },
    {
        "type": "extensions",
        "name": "HealthExtension",
        "properties": {
            "publisher": "Microsoft.ManagedServices",
            "type": "ApplicationHealthWindows",
            "autoUpgradeMinorVersion": true,
            "typeHandlerVersion": "1.0",
            "settings": {
            "protocol": "tcp",
            "port": "19000"
            }
            }
        },
    ]
}
```

## <a name="configuring-stateless-node-types-with-multiple-availability-zones"></a>Konfigurace bezstavových uzlů typu s více Zóny dostupnosti
Pokud chcete konfigurovat bezstavové NodeType pro různé zóny [dostupnosti, postupujte](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set)podle pokynů v dokumentaci, a to spolu s několika změnami, jak je znázorněno níže:

* Nastavte **singlePlacementGroup** :  **false**  , pokud je nutné povolit více skupin umístění.
* Nastavte  **upgradeMode** : **navracení**   a přidání rozšíření stavu aplikace/sondy stavu, jak je uvedeno výše.
* Nastavte **platformFaultDomainCount** : **5** pro sadu škálování virtuálního počítače.

>[!NOTE]
> Bez ohledu na VMSSZonalUpgradeMode nakonfigurovanou v clusteru vždy probíhá postupně jedna zóna dostupnosti pro bezstavový typ NodeType, který zahrnuje více zón, protože používá režim postupného upgradu.

Pro referenci se podívejte na [šablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-2-NodeTypes-Windows-Stateless-CrossAZ-Secure) pro konfiguraci bezstavových uzlů s více zóny dostupnosti

## <a name="networking-requirements"></a>Požadavky na síť
### <a name="public-ip-and-load-balancer-resource"></a>Prostředek veřejné IP adresy a Load Balancer
Pokud chcete povolit škálování na více než 100 virtuálních počítačů na prostředku sady škálování virtuálních počítačů, musí se vyrovnávání zatížení a prostředek IP, na který odkazuje tato sada škálování virtuálních počítačů, používat *standardní* SKU. Vytvořením nástroje pro vyrovnávání zatížení nebo prostředku IP bez vlastnosti SKU se vytvoří základní skladová položka, která nepodporuje škálování na víc než 100 virtuálních počítačů. Nástroj pro vyrovnávání zatížení Standard SKU bude ve výchozím nastavení blokovat veškerý provoz mimo rámec. aby bylo možné povolení mimo provoz, musí být do podsítě nasazený NSG.

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
> U prostředků veřejné IP adresy a nástroje pro vyrovnávání zatížení není možné provést místní změnu skladové položky. 

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



## <a name="migrate-to-using-stateless-node-types-in-a-cluster"></a>Migrace na použití bezstavových uzlů typu v clusteru
Pro všechny scénáře migrace je nutné přidat nový typ uzlu pouze bez stavu. Existující typ uzlu nejde migrovat jenom na stavový.

K migraci clusteru, který používal Load Balancer a IP adresu se základní SKU, je nutné nejprve vytvořit zcela nový Load Balancer a prostředek IP pomocí standardní SKU. Tyto prostředky není možné aktualizovat místně.

Nová disdoba a IP adresa by měly být odkazovány v nových typech bezstavových uzlů, které chcete použít. V předchozím příkladu se přidá nový prostředek sady škálování virtuálních počítačů, který se použije pro bezstavové typy uzlů. Tyto sady škálování virtuálních počítačů odkazují na nově vytvořenou a IP adresu, která je označena jako bezstavové uzly v prostředku clusteru Service Fabric.

Chcete-li začít, budete muset přidat nové prostředky do existující šablony Správce prostředků. Mezi tyto prostředky patří:
* Prostředek veřejné IP adresy, který používá standardní SKU.
* Load Balancer prostředek pomocí standardní SKU.
* NSG, na který odkazuje podsíť, ve které nasadíte služby Virtual Machine Scale Sets.

Po dokončení nasazení prostředků můžete začít s zakázáním uzlů v typu uzlu, který chcete odebrat z původního clusteru.

## <a name="next-steps"></a>Další kroky 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Typy uzlů a škálovací sady virtuálních počítačů](service-fabric-cluster-nodetypes.md)

