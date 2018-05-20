---
title: Postup upgradu nebo migrace verze SKU pro PrimaryNodeType do vyšších identifikátorů SKU | Microsoft Docs
description: Postup upgradu nebo migrace verze SKU pro PrimaryNodeType do vyšších identifikátorů SKU pomocí příkazů prostředí PowerShell a rozhraní příkazového řádku.
services: service-fabric
documentationcenter: .net
author: v-rachiw
manager: navya
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2018
ms.author: v-rachiw
ms.openlocfilehash: 8d5b560068a9e0bc0169bfdb98c5e939e34a3b8c
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="upgrademigrate-the-sku-for-primary-node-type-to-higher-sku"></a>Upgrade nebo migraci verze SKU pro primární uzel typ vyšší skladová položka

Tento článek popisuje postup upgradu nebo migrace SKU z primární uzel typ cluster service fabric na vyšší skladová položka pomocí Azure PowerShell

## <a name="add-a-new-virtual-machine-scale-set"></a>Přidat novou sadu škálování virtuálního počítače 

Nasaďte nové sady škálování virtuálního počítače a nástroj pro vyrovnávání zatížení. Konfigurace rozšíření Service Fabric (zejména typ uzlu) nové sady škálování virtuálního počítače by měla být stejná hodnota jako nastavenou staré měřítka, ke kterému se pokoušíte upgradovat. V Průzkumníku SF ověřte, že jsou k dispozici nové uzly. 

### <a name="azure-powershell"></a>Azure PowerShell
Následující příklad používá prostředí Azure PowerShell k nasazení aktualizované šablony Resource Manageru *template.json* pomocí skupinu prostředků s názvem *myResourceGroup*:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile \template\template.json 
```

Najdete v následujícím příkladu k úpravě šablony json s typem primárního uzlu ve stávajícím clusteru přidat nový prostředek sady škálování virtuálního počítače

```json
        {
            "apiVersion": "[variables('vmssApiVersion')]",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[parameters('vmNodeType2Name')]",
            "location": "[parameters('computeLocation')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType2Name')))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "overprovision": "[parameters('overProvision')]",
                "upgradePolicy": {
                    "mode": "Automatic"
                },
                "virtualMachineProfile": {
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "[concat(parameters('vmNodeType2Name'),'_ServiceFabricNode')]",
                                "properties": {
                                    "type": "ServiceFabricNode",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                        "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                                    },
                                    "publisher": "Microsoft.Azure.ServiceFabric",
                                    "settings": {
                                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                                        "dataPath": "D:\\\\SvcFab",
                                        "durabilityLevel": "Bronze",
                                        "enableParallelJobs": true,
                                        "nicPrefixOverride": "[parameters('subnet0Prefix')]",
                                        "certificate": {
                                            "thumbprint": "[parameters('certificateThumbprint')]",
                                            "x509StoreName": "[parameters('certificateStoreValue')]"
                                        }
                                    },
                                    "typeHandlerVersion": "1.0"
                                }
                            },
                            {
                                "name": "[concat('VMDiagnosticsVmExt','_vmNodeType2Name')]",
                                "properties": {
                                    "type": "IaaSDiagnostics",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
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
                                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                                    },
                                    "typeHandlerVersion": "1.5"
                                }
                            }
                        ]
                    },
                    "networkProfile": {
                        "networkInterfaceConfigurations": [
                            {
                                "name": "[concat(parameters('nicName'), '-2')]",
                                "properties": {
                                    "ipConfigurations": [
                                        {
                                            "name": "[concat(parameters('nicName'),'-',2)]",
                                            "properties": {
                                                "loadBalancerBackendAddressPools": [
                                                    {
                                                        "id": "[variables('lbPoolID2')]"
                                                    }
                                                ],
                                                "loadBalancerInboundNatPools": [
                                                    {
                                                        "id": "[variables('lbNatPoolID2')]"
                                                    }
                                                ],
                                                "subnet": {
                                                    "id": "[variables('subnet0Ref')]"
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
                        "computernamePrefix": "[parameters('vmNodeType2Name')]",
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
            "sku": {
                "name": "[parameters('vmNodeType2Size')]",
                "capacity": "[parameters('nt2InstanceCount')]",
                "tier": "Standard"
            },
            "tags": {
                "resourceType": "Service Fabric",
                "clusterName": "[parameters('clusterName')]"
            }
        },
```


## <a name="remove-old-virtual-machine-scale-set"></a>Odebrat starý škálovací sadu virtuálních počítačů

Vypněte virtuální počítač instancí sad se záměrem odebrat uzel staré škálování virtuálního počítače. Tato operace může trvat dlouhou dobu pro dokončení. Najednou můžete zakázat a budete pro ně být zařazeny do fronty. Počkejte, dokud nejsou všechny uzly. 

### <a name="azure-powershell"></a>Azure PowerShell
Následující příklad používá Azure Service Fabric prostředí PowerShell zakázat instanci uzel s názvem *NTvm1_0* z původního škálování virtuálních počítačů nastavení s názvem *NTvm1*:

```powershell
Disable-ServiceFabricNode -NodeName NTvm1_0 -Intent RemoveNode 
```

Odeberte sadu dokončení škálování. Počkejte, dokud škálování nastavit stav zřizování je úspěšné. 

### <a name="azure-powershell"></a>Azure PowerShell
Následující příklad používá prostředí Azure PowerShell k odebrání sad s názvem dokončení škálování *NTvm1* ze skupiny prostředků s názvem *myResourceGroup*:

```powershell
Remove-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName NTvm1
```

## <a name="remove-load-balancer-related-to-old-scale-set"></a>Odebrat související s původní sadě škálování nástroj pro vyrovnávání zatížení

Odeberte nástroj pro vyrovnávání zatížení související s původní sadě škálování. Tento krok způsobí, že na krátkou dobu výpadku pro cluster

### <a name="azure-powershell"></a>Azure PowerShell
Následující příklad používá prostředí Azure PowerShell k odebrání nástroj pro vyrovnávání zatížení s názvem *LB-myCluster-NTvm1* související s původním škálování nastavit ze skupiny prostředků s názvem *myResourceGroup*:

```powershell
Remove-AzureRmLoadBalancer -Name LB-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

## <a name="remove-public-ip-related-to-old-scale-set"></a>Odstranit veřejnou IP adresu související s původní sadě škálování

Nastavení DNS úložiště veřejnou IP adresu související s původním škálování nastavit do proměnné a pak odeberte veřejnou IP adresu

### <a name="azure-powershell"></a>Azure PowerShell
Následující příklad používá prostředí Azure PowerShell k ukládání nastavení DNS veřejné IP adresy s názvem *LBIP-myCluster-NTvm1* do proměnné a odebere IP adresu:

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1  -ResourceGroupName myResourceGroup
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn
Remove-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

## <a name="update-public-ip-address-related-to-new-scale-set"></a>Aktualizace veřejnou IP adresu související s novou sadu škálování

Aktualizace nastavení DNS související s novou škálování s veřejnou IP adresu související s původní sadě škálování nastavení DNS veřejné IP adresy

### <a name="azure-powershell"></a>Azure PowerShell
Následující příklad používá prostředí Azure PowerShell za účelem aktualizace nastavení DNS veřejné IP adresy s názvem *LBIP-myCluster-NTvm3* s nastavením DNS, které jsou uložené v proměnné v předchozím kroku:

```powershell
$PublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1  -ResourceGroupName myResourceGroup
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP 
```

## <a name="remove-knowledge-of-service-fabric-node-from-fm"></a>Odebrání FM znalosti uzel service fabric

Oznámit Service Fabric, že uzly, které jsou dolů byly odebrány z clusteru. (Spuštění tohoto příkazu pro virtuální počítač všechny instance starého škálovací sadu virtuálních počítačů) (Pokud úroveň odolnosti staré škálovací sadu virtuálních počítačů byla silver nebo zlatý, tento krok není potřeba. Vzhledem k tomu, že tento krok se provádí systém automaticky.)

### <a name="azure-powershell"></a>Azure PowerShell
Následující příklad používá Azure Service Fabric prostředí PowerShell oznámit service fabric, která uzel s názvem *NTvm1_0* byla odebrána:

```powershell
Remove-ServiceFabricNodeState -NodeName NTvm1_0
```