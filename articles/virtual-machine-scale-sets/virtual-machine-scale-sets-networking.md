---
title: Síťové služby pro škálovací sady virtuálních počítačů Azure
description: Jak nakonfigurovat některé pokročilejší síťové vlastnosti pro Azure Virtual Machine Scale Sets.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 06/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 9ad761f289805d15d316fc6f528a0049adb36b30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97722313"
---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Síťové služby pro škálovací sady virtuálních počítačů Azure

Když nasadíte škálovací sadu virtuálních počítačů Azure prostřednictvím portálu, pro určité vlastnosti sítě se nastaví výchozí hodnoty, jako například příchozí pravidla NAT pro Azure Load Balancer. Tento článek popisuje, jak používat některé pokročilejší síťové funkce, které můžete nakonfigurovat se škálovacími sadami.

Všechny funkce popsané v tomto článku můžete nakonfigurovat pomocí šablon Azure Resource Manageru. Pro vybrané funkce jsou zahrnuté také příklady Azure CLI a PowerShellu.

## <a name="accelerated-networking"></a>Akcelerované síťové služby
Akcelerované síťové služby Azure zlepšují výkon sítě tím, že na virtuálním počítači povolují rozhraní SR-IOV (single-root I/O virtualization). Další informace o akcelerovaných síťových službách najdete v tématech věnovaných akcelerovaným síťovým službám pro [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) nebo [Linux](../virtual-network/create-vm-accelerated-networking-cli.md). Pokud chcete používat akcelerované síťové služby se škálovacími sadami, v nastavení networkInterfaceConfigurations vaší škálovací sady nastavte enableAcceleratedNetworking na hodnotu **true**. Například:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
      "name": "niconfig1",
      "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
          ...
        ]
      }
    }
   ]
}
```

## <a name="azure-virtual-machine-scale-sets-with-azure-load-balancer"></a>Azure Virtual Machine Scale Sets s Azure Load Balancer
Další informace o tom, jak nakonfigurovat Standard Load Balancer s Virtual Machine Scale Sets na základě vašeho scénáře, najdete v tématu [Azure Load Balancer a Virtual Machine Scale Sets](../load-balancer/load-balancer-standard-virtual-machine-scale-sets.md) .

## <a name="create-a-scale-set-that-references-an-application-gateway"></a>Vytvoření škálovací sady, která odkazuje na aplikační bránu
Pokud chcete vytvořit škálovací sadu, která používá aplikační bránu, odkažte v sekci ipConfigurations této škálovací sady na fond adres back-endu aplikační brány jako v této konfiguraci šablony ARM:

```json
"ipConfigurations": [{
  "name": "{config-name}",
  "properties": {
  "subnet": {
    "id": "{subnet-id}"
  },
  "ApplicationGatewayBackendAddressPools": [{
    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/applicationGateways/{gateway-name}/backendAddressPools/{pool-name}"
  }]
}]
```

>[!NOTE]
> Aplikační brána musí být ve stejné virtuální síti jako škálovací sada, ale v jiné podsíti než škálovací sada.


## <a name="configurable-dns-settings"></a>Konfigurovatelná nastavení DNS
Ve výchozím nastavení škálovací sady přebírají konkrétní nastavení DNS virtuální sítě a podsítě, ve kterých byly vytvořeny. Nastavení DNS pro škálovací sadu ale můžete nakonfigurovat i přímo.

### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>Vytvoření škálovací sady s konfigurovatelnými servery DNS
Pokud chcete vytvořit škálovací sadu s vlastní konfigurací DNS pomocí Azure CLI, přidejte do příkazu **vmss create** argument **--dns-servers** následovaný mezerami oddělenými IP adresami serverů. Například:

```bash
--dns-servers 10.0.0.6 10.0.0.5
```

Pokud chcete nakonfigurovat vlastní servery DNS v šabloně Azure, přidejte do části networkInterfaceConfigurations škálovací sady vlastnost dnsSettings. Například:

```json
"dnsSettings":{
    "dnsServers":["10.0.0.6", "10.0.0.5"]
}
```

### <a name="creating-a-scale-set-with-configurable-virtual-machine-domain-names"></a>Vytvoření škálovací sady s konfigurovatelnými názvy domén virtuálních počítačů
Pokud chcete vytvořit škálovací sadu s vlastním názvem DNS pro virtuální počítače pomocí rozhraní příkazového řádku, přidejte do příkazu **virtual machine scale set create** argument **--vm-domain-name** následovaný řetězcem představujícím název domény.

Pokud chcete nastavit název domény v šabloně Azure, přidejte do části **networkInterfaceConfigurations** sady škálování vlastnost **dnsSettings** . Například:

```json
"networkProfile": {
  "networkInterfaceConfigurations": [
    {
    "name": "nic1",
    "properties": {
      "primary": true,
      "ipConfigurations": [
      {
        "name": "ip1",
        "properties": {
          "subnet": {
            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
          },
          "publicIPAddressconfiguration": {
            "name": "publicip",
            "properties": {
            "idleTimeoutInMinutes": 10,
              "dnsSettings": {
                "domainNameLabel": "[parameters('vmssDnsName')]"
              }
            }
          }
        }
      }
    ]
    }
}
```

Výstup pro každý jednotlivý název DNS virtuálního počítače by měl následující podobu:

```output
<vm><vmindex>.<specifiedVmssDomainNameLabel>
```

## <a name="public-ipv4-per-virtual-machine"></a>Veřejná IPv4 adresa na virtuální počítač
Obecně platí, že virtuální počítače Azure ve škálovací sadě nevyžadují vlastní veřejné IP adresy. Ve většině scénářů je výhodnější a zabezpečený přidružit veřejnou IP adresu k nástroji pro vyrovnávání zatížení nebo jednotlivým virtuálním počítačům (označovaným také jako JumpBox), která pak směruje příchozí připojení k virtuálním počítačům s nastaveným škálováním podle potřeby (například prostřednictvím příchozích pravidel NAT).

Některé scénáře však vyžadují, aby virtuální počítače ve škálovací sadě měly vlastní veřejné IP adresy. Příkladem jsou hry, kdy konzola potřebuje navázat přímé připojení ke cloudovému virtuálnímu počítači, který provádí zpracování herní fyziky. Dalším příkladem je situace, kdy virtuální počítače mezi sebou potřebují vytvořit externí připojení napříč oblastmi v distribuované databázi.

### <a name="creating-a-scale-set-with-public-ip-per-virtual-machine"></a>Vytvoření škálovací sady s veřejnou IP adresou na virtuální počítač
Pokud chcete pomocí rozhraní příkazového řádku vytvořit škálovací sadu, která každému virtuálnímu počítači přiřadí veřejnou IP adresu, přidejte do příkazu **vmss create** parametr **--public-ip-per-vm**. 

Pokud chcete vytvořit sadu škálování pomocí šablony Azure, ujistěte se, že je verze API prostředku Microsoft. COMPUTE/virtualMachineScaleSets aspoň **2017-03-30**, a přidejte do oddílu IPConfiguration sady škálování **publicIpAddressConfiguration** vlastnost JSON. Například:

```json
"publicIpAddressConfiguration": {
    "name": "pub1",
    "properties": {
      "idleTimeoutInMinutes": 15
    }
}
```

Ukázková šablona: [201-vmss-public-ip-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-public-ip-linux)

### <a name="querying-the-public-ip-addresses-of-the-virtual-machines-in-a-scale-set"></a>Dotazování veřejných IP adres virtuálních počítačů ve škálovací sadě
Pokud chcete zobrazit seznam veřejných IP adres přiřazených k virtuálním počítačům ve škálovací sadě pomocí rozhraní příkazového řádku, použijte příkaz **az vmss list-instance-public-ips**.

Pokud chcete zobrazit seznam veřejných IP adres sady škálování pomocí PowerShellu, použijte příkaz _Get-AzPublicIpAddress_ . Například:

```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -VirtualMachineScaleSetName myvmss
```

Veřejné IP adresy můžete také zjistit přímo z ID prostředku nakonfigurované veřejné IP adresy. Například:

```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -Name myvmsspip
```

Veřejné IP adresy přiřazené k virtuálním počítačům ve škálovací sadě můžete také zobrazit prostřednictvím dotazování [Azure Resource Exploreru](https://resources.azure.com) nebo rozhraní Azure REST API verze **2017-03-30** nebo novější.

Dotazování [Azure Resource Exploreru:](https://resources.azure.com)

1. Ve webovém prohlížeči otevřete [Azure Resource Explorer](https://resources.azure.com).
1. Rozbalte *subscriptions* (Předplatná) na levé straně tím, že kliknete na *+* vedle tohoto uzlu. Pokud v uzlu *subscriptions* (Předplatná) máte pouze jednu položku, možná už bude rozbalená.
1. Rozbalte své předplatné.
1. Rozbalte svou skupinu prostředků.
1. Rozbalte *providers* (Poskytovatelé).
1. Rozbalte položku *Microsoft. COMPUTE*.
1. Rozbalte *virtualMachineScaleSets*.
1. Rozbalte svou škálovací sadu.
1. Klikněte na *publicipaddresses*.

Dotazování rozhraní Azure REST API:

```bash
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

Příklad výstupu z [Azure Resource Exploreru](https://resources.azure.com) a rozhraní Azure REST API:

```json
{
  "value": [
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/pipvmss/virtualMachines/0/networkInterfaces/pipvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"a64060d5-4dea-4379-a11d-b23cd49a3c8d\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "ee8cb20f-af8e-4cd6-892f-441ae2bf701f",
        "ipAddress": "13.84.190.11",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/0/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    },
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"5f6ff30c-a24c-4818-883c-61ebd5f9eee8\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "036ce266-403f-41bd-8578-d446d7397c2f",
        "ipAddress": "13.84.159.176",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    }
```

## <a name="multiple-ip-addresses-per-nic"></a>Několik IP adres na síťové rozhraní
Každé síťové rozhraní připojené k virtuálnímu počítači ve škálovací sadě může mít přidruženu jednu nebo několik konfigurací IP. Každá konfigurace má přiřazenu jednu privátní IP adresu. Každá konfigurace také může mít přiřazen jeden prostředek veřejné IP adresy. Informace o tom, kolik IP adres může mít síťové rozhraní přiřazených a kolik veřejných IP adres můžete použít v předplatném Azure, najdete v [omezeních Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="multiple-nics-per-virtual-machine"></a>Několik síťových rozhraní na virtuální počítač
Na jeden virtuální počítač můžete mít v závislosti na jeho velikosti až 8 síťových rozhraní. Maximální počet síťových rozhraní na počítač najdete v [článku o velikostech virtuálních počítačů](../virtual-machines/sizes.md). Všechny síťové karty připojené k instanci virtuálního počítače musí být připojené ke stejné virtuální síti. Síťové karty se mohou připojovat k různým podsítím, ale všechny podsítě musí být součástí stejné virtuální sítě.

V následujícím příkladu je profil sítě škálovací sady ukazující několik záznamů síťových rozhraní a několik veřejných IP adres na virtuální počítač:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
        "name": "nic1",
        "properties": {
            "primary": true,
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        },
        {
        "name": "nic2",
        "properties": {
            "primary": false,
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        }
    ]
}
```

## <a name="nsg--asgs-per-scale-set"></a>Skupiny zabezpečení sítě a aplikace na škálovací sadu
[Skupiny zabezpečení sítě](../virtual-network/network-security-groups-overview.md) umožňují filtrovat provoz do a z prostředků Azure ve virtuální síti Azure pomocí pravidel zabezpečení. [Skupiny zabezpečení aplikací](../virtual-network/network-security-groups-overview.md#application-security-groups) umožňují správu zabezpečení sítě u prostředků Azure a jejich seskupování jako rozšíření struktury aplikace.

Skupiny zabezpečení sítě se můžou použít přímo na škálovací sadu přidáním odkazu do části konfigurace síťového rozhraní ve vlastnostech virtuálního počítače ve škálovací sadě.

Skupiny zabezpečení aplikace se můžou použít přímo na škálovací sadu přidáním odkazu do části konfigurace IP adresy síťového rozhraní ve vlastnostech virtuálního počítače ve škálovací sadě.

Například:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": true,
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "applicationSecurityGroups": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/applicationSecurityGroups/', variables('asgName'))]"
                                }
                            ],
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

Pokud chcete ověřit přidružení skupiny zabezpečení sítě se škálovací sadou, použijte příkaz `az vmss show`. Následující příklad pomocí parametru `--query` filtruje výsledky a zobrazí pouze relevantní část výstupu.

```azurecli
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].networkSecurityGroup

[
  {
    "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/nsgName",
    "resourceGroup": "myResourceGroup"
  }
]
```

Pokud chcete ověřit přidružení skupiny zabezpečení aplikace se škálovací sadou, použijte příkaz `az vmss show`. Následující příklad pomocí parametru `--query` filtruje výsledky a zobrazí pouze relevantní část výstupu.

```azurecli
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].ipConfigurations[].applicationSecurityGroups

[
  [
    {
      "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationSecurityGroups/asgName",
      "resourceGroup": "myResourceGroup"
    }
  ]
]
```

## <a name="make-networking-updates-to-specific-instances"></a>Proveďte aktualizace sítě pro konkrétní instance.

U konkrétních instancí sady škálování virtuálních počítačů můžete vytvářet síťové aktualizace. 

V případě `PUT` instance můžete aktualizovat konfiguraci sítě. Dá se použít k provádění akcí, jako je přidání nebo odebrání síťových rozhraní (nic), nebo odebrání instance z back-endu fondu.

```
PUT https://management.azure.com/subscriptions/.../resourceGroups/vmssnic/providers/Microsoft.Compute/virtualMachineScaleSets/vmssnic/virtualMachines/1/?api-version=2019-07-01
```

Následující příklad ukazuje, jak přidat další konfiguraci IP adresy do síťového adaptéru.

1. `GET` Podrobnosti konkrétní instance sady škálování virtuálních počítačů.
    
    ``` 
    GET https://management.azure.com/subscriptions/.../resourceGroups/vmssnic/providers/Microsoft.Compute/virtualMachineScaleSets/vmssnic/virtualMachines/1/?api-version=2019-07-01
    ```

    *V následujícím příkladu se zjednodušilo zobrazení pouze parametrů sítě pro tento příklad.*

    ```json
    {
      ...
      "properties": {
        ...
        "networkProfileConfiguration": {
          "networkInterfaceConfigurations": [
            {
              "name": "vmssnic-vnet-nic01",
              "properties": {
                "primary": true,
                "enableAcceleratedNetworking": false,
                "networkSecurityGroup": {
                  "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/networkSecurityGroups/basicNsgvmssnic-vnet-nic01"
                },
                "dnsSettings": {
                  "dnsServers": []
                },
                "enableIPForwarding": false,
                "ipConfigurations": [
                  {
                    "name": "vmssnic-vnet-nic01-defaultIpConfiguration",
                    "properties": {
                      "publicIPAddressConfiguration": {
                        "name": "publicIp-vmssnic-vnet-nic01",
                        "properties": {
                          "idleTimeoutInMinutes": 15,
                          "ipTags": [],
                          "publicIPAddressVersion": "IPv4"
                        }
                      },
                      "primary": true,
                      "subnet": {
                        "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/virtualNetworks/vmssnic-vnet/subnets/default"
                      },
                      "privateIPAddressVersion": "IPv4"
                    }
                  }
                ]
              }
            }
          ]
        },
        ...
      }
    }
    ```
 
2. `PUT` v případě instance aktualizuje a přidá další konfiguraci IP adresy. To je podobné pro přidání dalších `networkInterfaceConfiguration` .

    
    ```
    PUT https://management.azure.com/subscriptions/.../resourceGroups/vmssnic/providers/Microsoft.Compute/virtualMachineScaleSets/vmssnic/virtualMachines/1/?api-version=2019-07-01
    ```

    *V následujícím příkladu se zjednodušilo zobrazení pouze parametrů sítě pro tento příklad.*

    ```json
      {
      ...
      "properties": {
        ...
        "networkProfileConfiguration": {
          "networkInterfaceConfigurations": [
            {
              "name": "vmssnic-vnet-nic01",
              "properties": {
                "primary": true,
                "enableAcceleratedNetworking": false,
                "networkSecurityGroup": {
                  "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/networkSecurityGroups/basicNsgvmssnic-vnet-nic01"
                },
                "dnsSettings": {
                  "dnsServers": []
                },
                "enableIPForwarding": false,
                "ipConfigurations": [
                  {
                    "name": "vmssnic-vnet-nic01-defaultIpConfiguration",
                    "properties": {
                      "publicIPAddressConfiguration": {
                        "name": "publicIp-vmssnic-vnet-nic01",
                        "properties": {
                          "idleTimeoutInMinutes": 15,
                          "ipTags": [],
                          "publicIPAddressVersion": "IPv4"
                        }
                      },
                      "primary": true,
                      "subnet": {
                        "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/virtualNetworks/vmssnic-vnet/subnets/default"
                      },
                      "privateIPAddressVersion": "IPv4"
                    }
                  },
                  {
                    "name": "my-second-config",
                    "properties": {
                      "subnet": {
                        "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/virtualNetworks/vmssnic-vnet/subnets/default"
                      },
                      "privateIPAddressVersion": "IPv4"
                    }
                  }
                ]
              }
            }
          ]
        },
        ...
      }
    }
    ```



## <a name="next-steps"></a>Další kroky
Další informace o virtuálních sítích Azure najdete v [přehledu virtuálních sítí Azure](../virtual-network/virtual-networks-overview.md).
