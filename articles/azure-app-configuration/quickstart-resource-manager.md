---
title: Rychlé spuštění automatického virtuálního počítače pomocí rychlého spuštění konfigurace aplikací Azure
description: Tento rychlý start ukazuje, jak používat modul Azure PowerShell a šablony Azure Resource Manager k nasazení úložiště Konfigurace aplikací Azure. Pak použijte hodnoty v úložišti k nasazení virtuálního soudu.
author: lisaguthrie
ms.author: lcozzens
ms.date: 03/05/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: c45f6855c33dff2790ced306fd7f049b98dd1387
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79126378"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template"></a>Úvodní příručka: Automatické nasazení virtuálních počítačů pomocí šablony Konfigurace aplikace a Správce prostředků

Modul Azure PowerShell se používá k vytváření a správě prostředků Azure pomocí rutin nebo skriptů PowerShellu. Tento úvodní příručka ukazuje, jak používat šablony Azure PowerShell u Azure Resource Managerka k nasazení úložiště Konfigurace aplikací Azure. Pak se dozvíte, jak použít hodnoty klíče v úložišti k nasazení virtuálního virtuálního soudu.

Pomocí šablony předpokladů můžete vytvořit úložiště konfigurace aplikací a pak do úložiště přidat hodnoty klíčů pomocí portálu Azure nebo velpříkazového příkazového nastavení Azure. Primární šablona odkazuje na existující konfigurace klíč-hodnota z existujícího úložiště konfigurace. Načtené hodnoty se používají k nastavení vlastností prostředků vytvořených šablonou, jako je virtuální počítače v tomto příkladu.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet.](https://azure.microsoft.com/free/)

* Tento rychlý start vyžaduje modul Azure PowerShell. Pomocí příkazu `Get-Module -ListAvailable Az` vyhledejte verzi, která je nainstalovaná na místním počítači. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k `Connect-AzAccount` předplatnému Azure pomocí příkazu a zadejte svá přihlašovací údaje Azure v rozbalovacím prohlížeči:

```azurepowershell-interactive
# Connect to your Azure account
Connect-AzAccount
```

Pokud máte více než jedno předplatné, vyberte předplatné, které chcete použít pro tento rychlý start, spuštěním následujících rutin. Nezapomeňte nahradit `<your subscription name>` název předplatného:

```azurepowershell-interactive
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure s [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

```azurepowershell-interactive
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="deploy-an-azure-app-configuration-store"></a>Nasazení úložiště Konfigurace aplikací Azure

Než budete moct použít hodnoty klíčů na virtuální počítač, musíte mít existující Azure App Configuration store. Tato část podrobně popisuje, jak nasadit úložiště Konfigurace aplikací Azure pomocí šablony Azure Resource Manager. Pokud už máte obchod config aplikace, můžete přejít na další část tohoto článku. 

1. Zkopírujte a vložte následující kód json do nového souboru s názvem *prereq.azuredeploy.json*.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "type": "string",
        "metadata": {
          "description": "Specifies the name of the app configuration store."
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Specifies the Azure location where the app configuration store should be created."
        }
      },
      "skuName": {
        "type": "string",
        "defaultValue": "standard",
        "metadata": {
          "description": "Specifies the SKU of the app configuration store."
        }
      }
    },
    "resources": [
      {
        "type": "Microsoft.AppConfiguration/configurationStores",
        "name": "[parameters('configStoreName')]",
        "apiVersion": "2019-10-01",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('skuName')]"
        }
      }
    ]
   }
   ```

1. Zkopírujte a vložte následující kód json do nového souboru s názvem *prereq.azuredeploy.parameters.json*. Nahraďte **GET-UNIQUE** jedinečným názvem pro váš konfigurační obchod.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "value": "GET-UNIQUE"
      }
    }
   }
   ```

1. V okně PowerShellu spusťte následující příkaz pro nasazení úložiště Azure App Configuration Store. Nezapomeňte nahradit název skupiny prostředků, cestu k souboru šablony a cestu k souboru parametru šablony.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

## <a name="add-vm-configuration-key-values"></a>Přidání hodnot konfiguračního klíče virtuálního počítače

Úložiště konfigurace aplikací můžete vytvořit pomocí šablony Azure Resource Manager, ale potřebujete přidat hodnoty klíčů pomocí portálu Azure nebo azure cli. V tomto rychlém startu přidáte hodnoty klíče pomocí portálu Azure.

1. Po dokončení nasazení přejděte do nově vytvořeného úložiště Konfigurace aplikací na [webu Azure Portal](https://portal.azure.com).

1. Vyberte **možnost Nastavení** > **přístupových kláves**. Poznamenejte si připojovací řetězec primárního klíče jen pro čtení. Tento připojovací řetězec budete později používat ke konfiguraci aplikace pro komunikaci s úložištěm konfigurace aplikací, které jste vytvořili.

1. Vyberte Vytvořit **průzkumníka** > **konfigurace,** chcete-li přidat následující dvojice klíč-hodnota:

   |Klíč|Hodnota|
   |-|-|
   |windowsOsVersion|2019-Datové centrum|
   |diskVelikostGB|1023|
  
   Zadejte *šablonu* pro **Popisek**, ale ponechte **typ obsahu** prázdný.

## <a name="deploy-vm-using-stored-key-values"></a>Nasazení virtuálního mísa pomocí uložených hodnot klíčů

Teď, když jste do úložiště přidali hodnoty klíčů, jste připraveni nasadit virtuální počítač pomocí šablony Azure Resource Manager. Šablona odkazuje na **windowsOsVersion** a **diskSizeGB** klíče, které jste vytvořili.

1. Zkopírujte a vložte následující kód json do nového souboru s názvem *azuredeploy.json*nebo stáhněte soubor ze [šablon Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.json).

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "Admin user name."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "appConfigStoreName": {
            "type": "string",
            "metadata": {
                "description": "App configuration store name."
            }
        },
        "appConfigStoreResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Name of the resource group for the app config store."
            }
        },
        "domainNameLabel": {
            "type": "string",
            "metadata": {
                "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_D2_v3",
            "metadata": {
                "description": "Size of the VM"
            }
        },
        "vmSkuKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM windows sku"
            }
        },
        "diskSizeKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM disk size"
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "The name of the storage account."
            }
        }
    },
    "variables": {
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressName": "myPublicIP",
        "vmName": "SimpleWinVM",
        "virtualNetworkName": "MyVNET",
        "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
        "appConfigRef": "[resourceId(parameters('appConfigStoreResourceGroup'), 'Microsoft.AppConfiguration/configurationStores', parameters('appConfigStoreName'))]",
        "windowsOSVersionParameters": {
            "key": "[parameters('vmSkuKey')]",
            "label": "template"
        },
        "diskSizeGBParameters": {
            "key": "[parameters('diskSizeKey')]",
            "label": "template"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-11-01",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[variables('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('domainNameLabel')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2018-11-01",
            "name": "[variables('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2018-11-01",
            "name": "[variables('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[resourceId('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            },
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
            "apiVersion": "2018-10-01",
            "name": "[variables('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computerName": "[variables('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('windowsOSVersionParameters')).value]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "createOption": "FromImage"
                    },
                    "dataDisks": [
                        {
                            "diskSizeGB": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('diskSizeGBParameters')).value]",
                            "lun": 0,
                            "createOption": "Empty"
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                        }
                    ]
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))).primaryEndpoints.blob]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "hostname": {
            "type": "string",
            "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]"
        }
    }
   }
   ```

1. Zkopírujte a vložte následující kód json do nového souboru s názvem *azuredeploy.parameters.json*nebo stáhněte soubor ze [šablon Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.parameters.json).

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminPassword": {
        "value": "GEN-PASSWORD"
      },
      "appConfigStoreName":{
        "value": "GEN-APPCONFIGSTORE-NAME"
      },
      "appConfigStoreResourceGroup": {
         "value": "GEN-APPCONFIGSTORE-RESOURCEGROUP-NAME"
      },
      "vmSkuKey":{
        "value": "GEN-APPCONFIGSTORE-WINDOWSOSVERSION"
      },
      "diskSizeKey" :{
         "value": "GEN-APPCONFIGSTORE-DISKSIZEGB"
      },
      "adminUsername":{
        "value": "GEN-UNIQUE"
      },
      "storageAccountName":{
        "value": "GEN-UNIQUE"
      },
      "domainNameLabel":{
        "value": "GEN-UNIQUE"
      }
    }
   }
   ```

   Nahraďte hodnoty parametrů v šabloně následujícími hodnotami:

   |Parametr|Hodnota|
   |-|-|
   |adminPassword|Heslo správce pro virtuální počítače.|
   |název appConfigStore|Název vašeho úložiště Konfigurace aplikací Azure.|
   |appConfigStoreResourceGroup|Skupina prostředků, která obsahuje úložiště konfigurace aplikací.|
   |vmSkuKlíč|*windowsOSVersion*|
   |diskVelikostklíče|*diskVelikostGB*|
   |adminUsername|Uživatelské jméno správce pro virtuální ho.|
   |storageAccountName|Jedinečný název pro účet úložiště přidružený k virtuálnímu virtuálnímu ms.|
   |domainNameLabel|Jedinečný název domény.|

1. V okně PowerShellu spusťte následující příkaz pro nasazení úložiště Azure App Configuration Store. Nezapomeňte nahradit název skupiny prostředků, cestu k souboru šablony a cestu k souboru parametru šablony.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

Blahopřejeme! Virtuální počítač jste nasadili pomocí konfigurací uložených v konfiguraci aplikací Azure.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete, odstraňte skupinu prostředků, úložiště konfigurace aplikací, virtuální počítače a všechny související prostředky. Pokud plánujete v budoucnu používat úložiště konfigurace aplikací nebo virtuální počítače, můžete jeho odstranění přeskočit. Pokud nebudete pokračovat v používání této úlohy, odstraňte všechny prostředky vytvořené tímto rychlým startem spuštěním následující rutiny:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili virtuální počítač pomocí šablony Azure Resource Manager a hodnoty klíčů z konfigurace aplikace Azure.

Další informace o vytváření dalších aplikací pomocí konfigurace aplikací Azure najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Úvodní příručka: Vytvoření aplikace ASP.NET Core s konfigurací aplikací Azure](quickstart-aspnet-core-app.md)
