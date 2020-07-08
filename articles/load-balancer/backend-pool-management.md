---
title: Správa fondu back-endu
description: Průvodce konfigurací back-end fondu Load Balancer
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: overview
ms.date: 07/06/2020
ms.author: errobin
ms.openlocfilehash: 6d9700e134a9e3d6c53524d15c8b3503cf5773c7
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050184"
---
# <a name="backend-pool-management"></a>Správa fondu back-endu
Back-end fond je základní součástí Load Balancer, který definuje skupinu výpočetních prostředků, která bude obsluhovat provoz pro dané pravidlo vyrovnávání zatížení. Když nakonfigurujete fond back-end serveru správně, budete mít k dispozici skupinu oprávněných počítačů pro obsluhu provozu. Existují dva způsoby konfigurace fondu back-endu, síťové karty a kombinace IP adresy a Virtual Network (VNET) ID prostředku. 

Ve většině scénářů, které zahrnují Virtual Machines a Virtual Machine Scale Sets, se doporučuje nakonfigurovat fond back-end podle síťových adaptérů, protože tato metoda vytvoří nejvíce přímé propojení mezi vaším prostředkem a back-end fondem. V případě scénářů týkajících se kontejnerů a Kubernetes lusků, které nemají síťovou kartu ani pro předběžné přidělení rozsahu IP adres pro back-end prostředky, můžete nakonfigurovat fond back-end podle kombinace IP adresy a ID virtuální sítě.

Při konfiguraci buď pomocí síťového rozhraní nebo IP adresy a ID virtuální sítě prostřednictvím portálu vás uživatelské rozhraní provede jednotlivými kroky a všechny aktualizace konfigurace se budou zpracovávat v back-endu. Konfigurační oddíly tohoto článku se soustředí na šablony Azure PowerShell, CLI, REST API a ARM, které vám poskytnou přehled o tom, jak jsou pro jednotlivé možnosti konfigurace strukturované back-endové fondy.

## <a name="configuring-backend-pool-by-nic"></a>Konfigurace fondu back-endu podle síťové karty
Když konfigurujete fond back-end podle síťových adaptérů, je důležité mít na paměti, že back-end fond je vytvořen jako součást operace Load Balancer a členové budou přidáni do fondu back-endu jako součást vlastnosti konfigurace protokolu IP jejich síťového rozhraní během operace síťového rozhraní. Následující příklady jsou zaměřené na operace vytvoření a naplnění fondu back-end pro zdůraznění tohoto pracovního postupu a vztahu.

  >[!NOTE] 
  >Je důležité si uvědomit, že back-endové fondy nakonfigurované přes síťové rozhraní se nedají aktualizovat v rámci operace ve fondu back-endu. Jakékoli přidání nebo odstranění prostředků back-endu se musí objevit v síťovém rozhraní prostředku.

### <a name="powershell"></a>PowerShell
Vytvořit nový back-end fond: 
```powershell
$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup   -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName  
```

Vytvořte nové síťové rozhraní a přidejte ho do fondu back-end:
```powershell
$nic = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic' -LoadBalancerBackendAddressPool $bepool -Subnet $vnet.Subnets[0]
```

Načtěte informace o fondu back-endu pro Load Balancer a ověřte, že se toto síťové rozhraní přidalo do back-endu fondu:
```powershell
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup  -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName -BackendAddressPool  $bePool  
```

Vytvořte nový virtuální počítač a připojte síťové rozhraní k umístění do back-endu fondu:
```powershell
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM1' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM1' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM1.Id
 
# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $rgName -Zone 1 -Location $location -VM $vmConfig
```


  
### <a name="cli"></a>Rozhraní příkazového řádku
Vytvořte back-end fond:
```bash
az network lb address-pool create --resourceGroup myResourceGroup --lb-name myLB --name myBackendPool 
```

Vytvořte nové síťové rozhraní a přidejte ho do fondu back-end:
```bash
az network nic create --resource-group myResourceGroup --name myNic --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup --lb-name myLB --lb-address-pools myBackEndPool
```

Načtěte back-end fond pro potvrzení, že se IP adresa přidala správně:
```bash
az network lb address-pool show -g MyResourceGroup --lb-name MyLb -n MyBackendPool
```

Vytvořte nový virtuální počítač a připojte síťové rozhraní k umístění do back-endu fondu:
```bash
az vm create --resource-group myResourceGroup --name myVM --nics myNic --image UbuntuLTS --admin-username azureuser --generate-ssh-keys
```

### <a name="rest-api"></a>REST API
Vytvořte back-end fond:
```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Vytvořte síťové rozhraní a přidejte ho do back-endového fondu, který jste vytvořili prostřednictvím vlastnosti konfigurace protokolu IP síťového rozhraní:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/networkInterfaces/{nic-name}?api-version=2020-05-01
```

Text požadavku JSON:
```json
{
  "properties": {
    "enableAcceleratedNetworking": true,
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "subnet": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
          },
          "loadBalancerBackendAddressPools": {
                                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}"
          }
        }
      }
    ]
  },
  "location": "eastus"
}
```

Načtěte informace o fondu back-endu pro Load Balancer a ověřte, že se toto síťové rozhraní přidalo do back-endu fondu:

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name/providers/Microsoft.Network/loadBalancers/{load-balancer-name/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Vytvořte virtuální počítač a připojte síťovou kartu odkazující na fond back-end:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Compute/virtualMachines/{vm-name}?api-version=2019-12-01
```

Text požadavku JSON:
```JSON
{
  "location": "easttus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    }
  }
}
```

### <a name="arm-template"></a>Šablona ARM
Pomocí této [rychlé zprovoznění šablony ARM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create/) nasaďte Load Balancer a Virtual Machines a přidejte Virtual Machines do fondu back-end prostřednictvím síťového rozhraní.

## <a name="configuring-backend-pool-by-ip-address-and-virtual-network"></a>Konfiguruje se back-end fond podle IP adresy a Virtual Network
Pokud vyrovnáváte zatížení prostředků kontejnerů nebo předvyplnění back-end fondu s rozsahem IP adres, můžete využít IP adresu a Virtual Network k směrování na libovolný platný prostředek bez ohledu na to, jestli má síťové rozhraní. Při konfiguraci přes IP adresu a virtuální síť se veškerá správa back-end fondu provádí přímo na objektu back-endu, který je zvýrazněný v níže uvedených příkladech.

  >[!IMPORTANT] 
  >Tato funkce je aktuálně ve verzi Preview a má následující omezení:
  >* Limit 100 IP adres, které se přidávají
  >* Back-endové prostředky musí být ve stejném Virtual Network jako Load Balancer
  >* Tato funkce momentálně není v uživatelském prostředí portálu podporována.
  >* Toto je dostupné jenom pro standardní nástroje pro vyrovnávání zatížení.
  
### <a name="powershell"></a>PowerShell
Vytvořit nový back-end fond: 
```powershell
$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup   -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPooName  
```

Aktualizujte tento fond back-end s novou IP adresou z existující virtuální sítě:  
```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup 
 
$ip1 = New-AzLoadBalancerBackendAddressConfig -IpAddress "10.0.0.5" -Name "TestVNetRef" -VirtualNetwork $virtualNetwork  
 
$backendPool.LoadBalancerBackendAddresses.Add($ip1) 

Set-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup  -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName -BackendAddressPool  $backendPool  
```

Načtěte informace o fondu back-endu pro Load Balancer a ověřte, že se back-endové adresy přidávají do back-endu.
```powershell
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup  -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName -BackendAddressPool  $backendPool  
```
Vytvořte síťové rozhraní a přidejte ho do fondu back-end nastavením IP adresy na jednu z back-endové adresy:
```
$nic = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic' -PrivateIpAddress 10.0.0.4 -Subnet $vnet.Subnets[0]
```

Vytvořte virtuální počítač a připojte síťovou kartu s IP adresou ve fondu back-endu:
```powershell
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nic.Id
 
# Create a virtual machine using the configuration
$vm = New-AzVM -ResourceGroupName $rgName -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>Rozhraní příkazového řádku
Pomocí rozhraní příkazového řádku můžete buď naplnit back-end fond prostřednictvím parametrů příkazového řádku nebo pomocí konfiguračního souboru JSON. 

Vytvořte a naplňte back-end fond prostřednictvím parametrů příkazového řádku:
```bash
az network lb address-pool create --lb-name myLB --name myBackendPool --vnet {VNET resource ID} --backend-address name=addr1 ip-address=10.0.0.4 --backend-address name=addr2 ip-address=10.0.0.5
```

Vytvořte a naplňte back-end fond prostřednictvím konfiguračního souboru JSON:
```bash
az network lb address-pool create --lb-name myLB --name myBackendPool --vnet {VNET resource ID} --backend-address-config-file @config_file.json
```

Konfigurační soubor JSON:
```JSON
        [
          {
            "name": "address1",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.4"
          },
          {
            "name": "address2",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.5"
          }
        ]
```

Načtěte informace o fondu back-endu pro Load Balancer a ověřte, že se back-endové adresy přidávají do back-endu.
```bash
az network lb address-pool show -g MyResourceGroup --lb-name MyLb -n MyBackendPool
```

Vytvořte síťové rozhraní a přidejte ho do fondu back-end nastavením IP adresy na jednu z back-endové adresy:
```bash
az network nic create \
  --resource-group myResourceGroup \
  --name myNic \
  --vnet-name myVnet \
  --subnet mySubnet \
  --network-security-group myNetworkSecurityGroup \
  --lb-name myLB \
  --private-ip-address 10.0.0.4
```

Vytvořte virtuální počítač a připojte síťovou kartu s IP adresou ve fondu back-endu:
```bash
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --nics myNic \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

### <a name="rest-api"></a>REST API


Vytvořte back-end fond a definujte back-end adresy prostřednictvím žádosti o back-end fondu. Nakonfigurujte back-end adresy, které chcete přidat přes název adresy, IP adresu a ID Virtual Network v těle JSON žádosti o vložení:

```
PUT https://management.azure.com/subscriptions/subid/resourceGroups/testrg/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/backend?api-version=2020-05-01
```

Text požadavku JSON:
```JSON
{
  "properties": {
    "loadBalancerBackendAddresses": [
      {
        "name": "address1",
        "properties": {
          "virtualNetwork": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}"
          },
          "ipAddress": "10.0.0.4"
        }
      },
      {
        "name": "address2",
        "properties": {
          "virtualNetwork": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}"
          },
          "ipAddress": "10.0.0.5"
        }
      }
    ]
  }
}
```

Načtěte informace o fondu back-endu pro Load Balancer a ověřte, že se back-endové adresy přidávají do back-endu.
```
GET https://management.azure.com/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Vytvořte síťové rozhraní a přidejte ho do fondu back-end nastavením IP adresy na jednu z back-endové adresy:
```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/networkInterfaces/{nic-name}?api-version=2020-05-01
```

Text požadavku JSON:
```JSON
{
  "properties": {
    "enableAcceleratedNetworking": true,
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "privateIPAddress": "10.0.0.4",
          "subnet": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
          }
        }
      }
    ]
  },
  "location": "eastus"
}
```

Vytvořte virtuální počítač a připojte síťovou kartu s IP adresou ve fondu back-endu:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Compute/virtualMachines/{vm-name}?api-version=2019-12-01
```

Text požadavku JSON:
```JSON
{
  "location": "eastus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    }
  }
}
```

### <a name="arm-template"></a>Šablona ARM
Vytvořte Load Balancer, back-end fond a naplňte back-end fond s back-end adresami:
```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "loadBalancers_myLB_location": {
            "type": "SecureString"
        },
        "loadBalancers_myLB_location_1": {
            "type": "SecureString"
        },
        "backendAddressPools_myBackendPool_location": {
            "type": "SecureString"
        },
        "backendAddressPools_myBackendPool_location_1": {
            "type": "SecureString"
        },
        "loadBalancers_myLB_name": {
            "defaultValue": "myLB",
            "type": "String"
        },
        "virtualNetworks_myVNET_externalid": {
            "defaultValue": "/subscriptions/6bb4a28a-db84-4e8a-b1dc-fabf7bd9f0b2/resourceGroups/ErRobin4/providers/Microsoft.Network/virtualNetworks/myVNET",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2020-04-01",
            "name": "[parameters('loadBalancers_myLB_name')]",
            "location": "eastus",
            "sku": {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LoadBalancerFrontEnd",
                        "properties": {
                            "privateIPAddress": "10.0.0.7",
                            "privateIPAllocationMethod": "Dynamic",
                            "subnet": {
                                "id": "[concat(parameters('virtualNetworks_myVNET_externalid'), '/subnets/Subnet-1')]"
                            },
                            "privateIPAddressVersion": "IPv4"
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "myBackendPool",
                        "properties": {
                            "loadBalancerBackendAddresses": [
                                {
                                    "name": "addr1",
                                    "properties": {
                                        "ipAddress": "10.0.0.4",
                                        "virtualNetwork": {
                                            "location": "[parameters('loadBalancers_myLB_location')]"
                                        }
                                    }
                                },
                                {
                                    "name": "addr2",
                                    "properties": {
                                        "ipAddress": "10.0.0.5",
                                        "virtualNetwork": {
                                            "location": "[parameters('loadBalancers_myLB_location_1')]"
                                        }
                                    }
                                }
                            ]
                        }
                    }
                ],
                "loadBalancingRules": [],
                "probes": [],
                "inboundNatRules": [],
                "outboundRules": [],
                "inboundNatPools": []
            }
        },
        {
            "type": "Microsoft.Network/loadBalancers/backendAddressPools",
            "apiVersion": "2020-04-01",
            "name": "[concat(parameters('loadBalancers_myLB_name'), '/myBackendPool')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLB_name'))]"
            ],
            "properties": {
                "loadBalancerBackendAddresses": [
                    {
                        "name": "addr1",
                        "properties": {
                            "ipAddress": "10.0.0.4",
                            "virtualNetwork": {
                                "location": "[parameters('backendAddressPools_myBackendPool_location')]"
                            }
                        }
                    },
                    {
                        "name": "addr2",
                        "properties": {
                            "ipAddress": "10.0.0.5",
                            "virtualNetwork": {
                                "location": "[parameters('backendAddressPools_myBackendPool_location_1')]"
                            }
                        }
                    }
                ]
            }
        }
    ]
}
```

Vytvořte virtuální počítač a připojené síťové rozhraní. Nastavte IP adresu síťového rozhraní na jednu z back-endové adresy:
```
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "String",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "storageAccountDomain": {
      "type": "String",
      "metadata": {
        "description": "The domain of the storage account to be created."
      }
    },
    "adminUsername": {
      "type": "String",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "SecureString",
      "metadata": {
        "description": "Admin password"
      }
    },
    "vmName": {
      "defaultValue": "myVM",
      "type": "String",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "String",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "defaultValue": "WindowsServer",
      "type": "String",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "defaultValue": "2012-R2-Datacenter",
      "type": "String",
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "defaultValue": "myLB",
      "type": "String",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicName": {
      "defaultValue": "nic",
      "type": "String",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "privateIpAddress": {
      "defaultValue": "10.0.0.5",
      "type": "String",
      "metadata": {
        "description": "Private IP Address of the VM"
      }
    },
    "vnetName": {
      "defaultValue": "myVNET",
      "type": "String",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "defaultValue": "Standard_A1",
      "type": "String",
      "metadata": {
        "description": "Size of the VM"
      }
    },
    "storageLocation": {
      "type": "String",
      "metadata": {
        "description": "Location of the Storage Account."
      }
    },
    "location": {
      "type": "String",
      "metadata": {
        "description": "Location to deploy all the resources in."
      }
    }
  },
  "variables": {
    "networkSecurityGroupName": "networkSecurityGroup1",
    "storageAccountType": "Standard_LRS",
    "subnetName": "Subnet-1",
    "publicIPAddressType": "Static",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('storageLocation')]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "type": "Microsoft.Network/networkSecurityGroups",
      "apiVersion": "2016-03-30",
      "name": "[variables('networkSecurityGroupName')]",
      "location": "[parameters('location')]",
      "properties": {
        "securityRules": []
      }
    },
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Static",
              "privateIpAddress": "[parameters('privateIpAddress')]",
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('vmName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
        "[parameters('nicName')]"
      ],
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computername": "[parameters('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "[parameters('imagePublisher')]",
            "offer": "[parameters('imageOffer')]",
            "sku": "[parameters('imageSKU')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',parameters('storageAccountName'),'.blob.',parameters('storageAccountDomain'),'/vhds/','osdisk', '.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
            }
          ]
        }
      }
    }
  ]
}
```
