---
title: Horizontální navýšení kapacity Service Fabric typu primárního uzlu Azure
description: Naučte se škálovat Cluster Service Fabric přidáním typu uzlu.
ms.topic: article
ms.date: 08/06/2020
ms.author: pepogors
ms.openlocfilehash: a18a40cc9e467b089ea9d6be3d0ca81a21d2c474
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89228711"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type-by-adding-a-node-type"></a>Navýšení kapacity Service Fabric typ primárního uzlu clusteru přidáním typu uzlu
Tento článek popisuje, jak škálovat typ primárního uzlu clusteru Service Fabric tak, že do clusteru přidáte další typ uzlu. Cluster Service Fabric je sada virtuálních nebo fyzických počítačů připojených k síti, do kterých se vaše mikroslužby nasazují a spravují. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Sady škálování virtuálních počítačů jsou výpočetním prostředkem Azure, který můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definovaný v clusteru Azure, je [nastavený jako samostatná sada škálování](service-fabric-cluster-nodetypes.md). Každý typ uzlu se pak dá spravovat samostatně.

Ukázkové šablony v následujícím kurzu najdete tady: [Service Fabric vzorky škálování typu primárního uzlu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Primary-NodeType-Scaling-Sample) .

> [!WARNING]
> Pokud stav clusteru není v pořádku, nepokoušejte se nepokusit o typ primárního uzlu postup horizontálního navýšení kapacity, protože tento cluster bude dál jenom destabilizovat.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type"></a>Postup upgradu velikosti a operačního systému typu primárního uzlu
Následuje postup aktualizace velikosti a operačního systému virtuálního počítače typu virtuální uzel.  Po upgradu jsou virtuální počítače typu primární uzel standardní D4_V2 a běží na Windows serveru 2019 Datacenter s kontejnery.

> [!WARNING]
> Před pokusem o provedení této procedury v produkčním clusteru doporučujeme prostudovat ukázkové šablony a ověřit proces proti testovacímu clusteru. Cluster může být k dispozici také po krátkou dobu. 

### <a name="deploy-the-initial-service-fabric-cluster"></a>Nasadit počáteční Cluster Service Fabric 
Pokud chcete postupovat spolu s ukázkou, nasaďte počáteční cluster s jedním primárním typem uzlu a jednu sadu škálování [Service Fabric-počáteční cluster](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-1.json). Tento krok můžete vynechat, pokud už máte nasazený existující Service Fabric cluster. 

1. Přihlaste se ke svému účtu Azure. 
```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```
2. Vytvoříte novou skupinu prostředků. 
```powershell
# create a resource group for your cluster deployment
$resourceGroupName = "myResourceGroup"
$location = "WestUS"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location
```
3. Zadejte hodnoty parametrů v souborech šablon. 
4. Nasaďte cluster do skupiny prostředků vytvořené v kroku 2. 
```powershell
# deploy the template files to the resource group created above
$templateFilePath = "C:\AzureDeploy-1.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath
```

### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Přidat do clusteru nový typ primárního uzlu
> [!Note]
> Po dokončení operace škálování se prostředky vytvořené v následujících krocích stanou novým typem primárního uzlu ve vašem clusteru. Ujistěte se, že používáte názvy, které jsou jedinečné od počáteční podsítě, veřejné IP adresy, Load Balancer, sady škálování virtuálních počítačů a typu uzlu. 

Šablonu obsahující všechny následující kroky jsou dokončené tady: [Service Fabric-nový cluster typu Node](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-2.json). Následující kroky obsahují částečné fragmenty prostředků, které zvýrazní změny v nových prostředcích.  

1. Vytvořte v existující Virtual Network novou podsíť.
```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```
2. Vytvořte nový prostředek veřejné IP adresy s jedinečným domainNameLabel. 
```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt2')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```
3. Vytvořte nový prostředek Load Balancer, který závisí na veřejné IP adrese vytvořené výše. 
```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```
4. Vytvořte novou sadu škálování virtuálního počítače, která bude používat novou SKLADOVOU položku virtuálního počítače a SKLADOVOU položku operačního systému, na kterou se má škálovat. 

Typ uzlu ref 
```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

Skladová položka virtuálního počítače
```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

SKU OPERAČNÍHO SYSTÉMU 
```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

Následující fragment kódu je příkladem nového prostředku sady škálování virtuálních počítačů, který se používá k vytvoření nového typu uzlu pro cluster Service Fabric. Měli byste zajistit, abyste měli všechna další rozšíření, která jsou potřebná pro vaše zatížení. 

```json
    {
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeType1Name')]",
      "location": "[variables('computeLocation')]",
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType1Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
      ],
      "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Automatic"
        },
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
              {
                "name": "[concat('ServiceFabricNodeVmExt_',variables('vmNodeType1Name'))]",
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
                    "nodeTypeRef": "[variables('vmNodeType1Name')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "enableParallelJobs": true,
                    "nicPrefixOverride": "[variables('subnet1Prefix')]",
                    "certificate": {
                      "thumbprint": "[parameters('certificateThumbprint')]",
                      "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.0"
                }
              }
            ]
          },
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[concat(variables('nicName'), '-1')]",
                "properties": {
                  "ipConfigurations": [
                    {
                      "name": "[concat(variables('nicName'),'-',1)]",
                      "properties": {
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[variables('lbPoolID1')]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[variables('lbNatPoolID1')]"
                          }
                        ],
                        "subnet": {
                          "id": "[variables('subnet1Ref')]"
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
            "computernamePrefix": "[variables('vmNodeType1Name')]",
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
              "publisher": "[parameters('vmImagePublisher1')]",
              "offer": "[parameters('vmImageOffer1')]",
              "sku": "[parameters('vmImageSku1')]",
              "version": "[parameters('vmImageVersion1')]"
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
      "sku": {
        "name": "[parameters('vmNodeType1Size')]",
        "capacity": "[parameters('nt1InstanceCount')]",
        "tier": "Standard"
      },
      "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
      }
    },

```

5. Do clusteru přidejte nový typ uzlu, který odkazuje na sadu škálování virtuálního počítače, která byla vytvořena výše. Vlastnost **Primary** v tomto typu uzlu by měla být nastavena na hodnotu true. 
```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```
6. Nasaďte aktualizovanou šablonu ARM. 
```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-2.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

Po dokončení nasazení budou mít Cluster Service Fabric nyní dva typy uzlů. 

### <a name="remove-the-existing-node-type"></a>Odebrat existující typ uzlu 
Po dokončení nasazení prostředků můžete začít s zakázáním uzlů v původním typu primárního uzlu. Po zakázání uzlů budou systémové služby migrovány na nový typ primárního uzlu, který byl nasazen v kroku výše.

1. Nastavte vlastnost typ primárního uzlu v prostředku clusteru Service Fabric na hodnotu false (NEPRAVDA). 
```json
{
    "name": "[variables('vmNodeType0Name')]",
    "applicationPorts": {
        "endPort": "[variables('nt0applicationEndPort')]",
        "startPort": "[variables('nt0applicationStartPort')]"
    },
    "clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
    "durabilityLevel": "Bronze",
    "ephemeralPorts": {
        "endPort": "[variables('nt0ephemeralEndPort')]",
        "startPort": "[variables('nt0ephemeralStartPort')]"
    },
    "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
    "isPrimary": false,
    "reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
    "vmInstanceCount": "[parameters('nt0InstanceCount')]"
}
```
2. Nasaďte šablonu s aktualizovanou primární vlastností na původním typu uzlu. Šablonu s primárním příznakem nastaveným na hodnotu false v původním typu uzlu můžete najít tady: [Service Fabric-Primary Node Type false](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-3.json).

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-3.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

3. Zakáže uzly v typu uzlu 0. 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterConnectionEndpoint `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"


$nodeType = "nt1vm" # specify the name of node type
$nodes = Get-ServiceFabricNode 

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```
* Pro bronzovou odolnost počkejte, než se všechny uzly dostanou do stavu zakázáno.
* V případě odolnosti stříbrného a zlata budou některé uzly v neaktivním stavu a zbytek bude zakázán. Zkontrolujte kartu Podrobnosti uzlů v části zakázání stavu, pokud jsou všechny zablokované na zajištění kvora pro oddíly služby infrastruktury, a pak je bezpečné pokračovat.

> [!Note]
> Dokončení tohoto kroku může chvíli trvat. 

4. Zastavuje data na uzlu typu 0. 
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```
5. Zruší přidělení uzlů v původní sadě škálování virtuálního počítače. 
```powershell
$scaleSetName="nt1vm"
$scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```
> [!Note]
> Kroky 6 a 7 jsou volitelné, pokud jste již používali veřejnou IP adresu Standard SKU a službu Load Balancer Standard SKU. V takovém případě můžete mít v rámci stejného nástroje pro vyrovnávání zatížení více typů uzlů nebo uzlů pro škálování virtuálního počítače. 

6. Nyní můžete odstranit původní IP adresy a prostředky Load Balancer. V tomto kroku si taky aktualizujete název DNS. 

```powershell
$lbname="LB-cluster-name-nt1vm"
$lbResourceType="Microsoft.Network/loadBalancers"
$ipResourceType="Microsoft.Network/publicIPAddresses"
$oldPublicIpName="PublicIP-LB-FE-nt1vm"
$newPublicIpName="PublicIP-LB-FE-nt2vm"

$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbname -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
``` 

7. Aktualizujte koncový bod správy v clusteru, aby odkazoval na novou IP adresu. 
```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```
8. Odebere stav uzlu z uzlu typu 0.
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```
9. Odeberte odkaz na typ původního uzlu z prostředku Service Fabric v šabloně ARM. 
```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```
V případě vysoké a vyšší odolnosti clusterů aktualizujte prostředek clusteru v šabloně a nakonfigurujte zásady stavu tak, aby ignorovaly stav aplikací Fabric:/System, a to přidáním applicationDeltaHealthPolicies do vlastností prostředku clusteru, jak je uvedeno níže. Níže uvedené zásady by měly ignorovat existující chyby, ale nedovolují nové chyby v oblasti stavu.
```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```
10. Odeberte všechny ostatní prostředky související s typem původního uzlu ze šablony ARM. V tématu [Service Fabric – nový cluster typů uzlů](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-4.json) pro šablonu, ze které byly odebrány všechny původní prostředky.

11. Nasaďte upravenou šablonu Azure Resource Manager. * * Tento krok může trvat delší dobu, obvykle až dvě hodiny. Tento upgrade změní nastavení na InfrastructureService; Proto je nutné restartovat uzel. V tomto případě se forceRestart ignoruje. Parametr upgradeReplicaSetCheckTimeout určuje maximální dobu, po kterou Service Fabric čeká na vystavení oddílu v bezpečném stavu, pokud ještě není v bezpečném stavu. Jakmile kontroly bezpečnosti projde pro všechny oddíly v uzlu, Service Fabric pokračuje s upgradem v tomto uzlu. Hodnota parametru upgradeTimeout může být snížena na 6 hodin, ale v případě maximálního zabezpečení 12 hodin by se měla použít.
Pak ověřte, že se prostředek Service Fabric na portálu zobrazuje jako připravený. 

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-4.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

Typ primárního uzlu clusteru byl nyní upgradován. Ověřte, že všechny nasazené aplikace fungují správně a že stav clusteru je OK.

## <a name="next-steps"></a>Další kroky
* Naučte se, jak [Přidat typ uzlu do clusteru](virtual-machine-scale-set-scale-node-type-scale-out.md) .
* Přečtěte si o [škálovatelnosti aplikací](service-fabric-concepts-scalability.md).
* Horizontální navýšení nebo navýšení [kapacity clusteru Azure](service-fabric-tutorial-scale-cluster.md)
* [Škálujte cluster Azure pomocí programu](service-fabric-cluster-programmatic-scaling.md) Fluent Azure COMPUTE SDK.
* Horizontální navýšení [nebo zmenšení kapacity samostatného clusteru](service-fabric-cluster-windows-server-add-remove-nodes.md)
