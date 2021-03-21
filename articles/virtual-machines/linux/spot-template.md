---
title: Použití šablony k nasazení Virtual Machines Azure na místě
description: Naučte se používat šablonu k nasazení služby Azure spot Virtual Machines za účelem úspory nákladů.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/25/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 04319066c59dda5d240f527d86894674a505eaed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669349"
---
# <a name="deploy-azure-spot-virtual-machines-using-a-resource-manager-template"></a>Nasazení služby Azure spot Virtual Machines pomocí šablony Správce prostředků

Použití [Azure Spot Virtual Machines](../spot-vms.md) umožňuje využít výhod naší nevyužité kapacity s významnou úsporou nákladů. V jakémkoli okamžiku, kdy Azure potřebuje kapacitu zpátky, vyřadí infrastruktura Azure místo Virtual Machines Azure. Proto jsou Azure spot Virtual Machines Skvělé pro úlohy, které mohou zpracovávat přerušení, jako jsou úlohy dávkového zpracování, vývojové a testovací prostředí, velké výpočetní úlohy a další.

Ceny za Azure na místě Virtual Machines jsou proměnné na základě oblastí a SKU. Další informace najdete v tématu ceny virtuálních počítačů pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

Máte možnost nastavit maximální cenu, kterou jste ochotni zaplatit za hodinu pro virtuální počítač. Maximální cena pro virtuální počítač se službou Azure se dá nastavit v amerických dolarech (USD), a to s využitím až 5 desetinných míst. Hodnota by měla být například `0.98765` maximální cena $0,98765 USD za hodinu. Pokud nastavíte maximální cenu `-1` , nebude se virtuální počítač vyřadit podle ceny. Cena za virtuální počítač bude aktuální cena za Azure na místě Virtual Machines nebo cena za standardní virtuální počítač, který je stále menší, pokud je dostupná kapacita a kvóta. Další informace o nastavení maximální ceny najdete v tématu [Azure Spot Virtual Machines – ceny](../spot-vms.md#pricing).


## <a name="use-a-template"></a>Použití šablony

Pro nasazení šablon virtuálních počítačů se službou Azure použijte `"apiVersion": "2019-03-01"` nebo novější. Do `priority` šablony přidejte `evictionPolicy` vlastnosti a a `billingProfile` :

```json
"priority": "Spot",
"evictionPolicy": "Deallocate",
"billingProfile": {
    "maxPrice": -1
}
```

Tady je Ukázková šablona s přidanými vlastnostmi pro virtuální počítač se službou Azure na místě. Názvy prostředků nahraďte vlastními a `<password>` heslo pro účet místního správce na virtuálním počítači.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2019-03-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "vnetId": "/subscriptions/ec9fcd04-e188-48b9-abfc-abcd515f1836/resourceGroups/spotVM/providers/Microsoft.Network/virtualNetworks/spotVM",
        "subnetName": "default",
        "networkInterfaceName": "spotVMNIC",
        "publicIpAddressName": "spotVM-ip",
        "publicIpAddressType": "Dynamic",
        "publicIpAddressSku": "Basic",
        "virtualMachineName": "spotVM",
        "osDiskType": "Premium_LRS",
        "virtualMachineSize": "Standard_D2s_v3",
        "adminUsername": "azureuser",
        "adminPassword": "<password>",
        "diagnosticsStorageAccountName": "diagstoragespot2019",
        "diagnosticsStorageAccountId": "Microsoft.Storage/storageAccounts/diagstoragespot2019",
        "diagnosticsStorageAccountType": "Standard_LRS",
        "diagnosticsStorageAccountKind": "Storage",
        "subnetRef": "[concat(variables('vnetId'), '/subnets/', variables('subnetName'))]"
    },
    "resources": [
        {
            "name": "spotVM",
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            },
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIpAddress": {
                                "id": "[resourceId(resourceGroup().name, 'Microsoft.Network/publicIpAddresses', variables('publicIpAddressName'))]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "name": "[variables('publicIpAddressName')]",
            "type": "Microsoft.Network/publicIpAddresses",
            "apiVersion": "2019-02-01",
            "location": "eastus",
            "properties": {
                "publicIpAllocationMethod": "[variables('publicIpAddressType')]"
            },
            "sku": {
                "name": "[variables('publicIpAddressSku')]"
            }
        },
        {
            "name": "[variables('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', variables('networkInterfaceName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[variables('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[variables('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[variables('virtualMachineName')]",
                    "adminUsername": "[variables('adminUsername')]",
                    "adminPassword": "[variables('adminPassword')]"
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('https://', variables('diagnosticsStorageAccountName'), '.blob.core.windows.net/')]"
                    }
                },
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            }
        },
        {
            "name": "[variables('diagnosticsStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "location": "eastus",
            "properties": {},
            "kind": "[variables('diagnosticsStorageAccountKind')]",
            "sku": {
                "name": "[variables('diagnosticsStorageAccountType')]"
            }
        }
    ],
    "outputs": {
        "adminUsername": {
            "type": "string",
            "value": "[variables('adminUsername')]"
        }
    }
}
```

## <a name="simulate-an-eviction"></a>Simulace vyřazení

Můžete [simulovat vyřazení](/rest/api/compute/virtualmachines/simulateeviction) virtuálních počítačů se systémem Azure na místě, abyste mohli otestovat, jak dobře bude vaše aplikace schopná vyřadit do náhlého vyřazení. 

Pro vaše informace nahraďte následující údaje: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

## <a name="next-steps"></a>Další kroky

Virtuální počítač se službou Azure můžete vytvořit také pomocí [Azure PowerShell](../windows/spot-powershell.md) nebo rozhraní příkazového [řádku Azure CLI](spot-cli.md).

Dotaz na aktuální informace o cenách pomocí [API maloobchodních cen Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) najdete v informacích o cenách virtuálních počítačů Azure na místě. `meterName`A `skuName` bude obsahovat oba `Spot` .

Pokud dojde k chybě, přečtěte si [kódy chyb](../error-codes-spot.md).