---
title: Automatizované nasazení virtuálních počítačů s rychlým startem konfigurace Azure App
description: Tento rychlý Start ukazuje, jak použít modul Azure PowerShell a šablony Azure Resource Manager k nasazení úložiště konfigurace aplikace Azure. Pak použijte hodnoty z úložiště k nasazení virtuálního počítače.
author: lisaguthrie
ms.author: lcozzens
ms.date: 03/05/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 702c03aec1980b12debeef3afeb84e0cbaef55b6
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671588"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template"></a>Rychlý Start: automatizované nasazení virtuálních počítačů s konfigurací aplikace a šablonou Správce prostředků

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure pomocí rutin PowerShellu nebo skriptů. V tomto rychlém startu se dozvíte, jak používat šablony Azure PowerShell a Azure Resource Manager k nasazení úložiště konfigurace aplikace Azure. Pak se dozvíte, jak pomocí hodnot klíč-hodnoty v úložišti nasadit virtuální počítač.

Pomocí šablony požadovaných součástí můžete vytvořit úložiště konfigurace aplikace a potom do úložiště přidat klíčové hodnoty pomocí Azure Portal nebo rozhraní příkazového řádku Azure CLI. Primární šablona odkazuje na stávající konfigurace klíč-hodnota z existujícího úložiště konfigurace. Načtené hodnoty slouží k nastavení vlastností prostředků vytvořených šablonou, jako je třeba virtuální počítač v tomto příkladu.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Tento rychlý Start vyžaduje modul Azure PowerShell. Pomocí příkazu `Get-Module -ListAvailable Az` vyhledejte verzi, která je nainstalovaná na místním počítači. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu `Connect-AzAccount` a zadejte svoje přihlašovací údaje Azure v místním prohlížeči:

```azurepowershell-interactive
# Connect to your Azure account
Connect-AzAccount
```

Pokud máte více než jedno předplatné, vyberte předplatné, které chcete použít pro tento rychlý Start, a spusťte následující rutiny. Nezapomeňte nahradit `<your subscription name>` názvem vašeho předplatného:

```azurepowershell-interactive
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

```azurepowershell-interactive
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="deploy-an-azure-app-configuration-store"></a>Nasazení úložiště konfigurace aplikace Azure

Než budete moct použít hodnoty klíč-hodnota k virtuálnímu počítači, musíte mít existující úložiště konfigurace aplikace Azure. Tato část podrobně popisuje, jak nasadit úložiště konfigurace aplikace Azure pomocí šablony Azure Resource Manager. Pokud již máte úložiště konfigurace aplikace, můžete přejít k další části tohoto článku. 

1. Zkopírujte následující kód JSON a vložte ho do nového souboru s názvem *požadavků ohlásila. azuredeploy. JSON*.

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
        "defaultValue": "free",
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

1. Zkopírujte následující kód JSON a vložte ho do nového souboru s názvem *požadavků ohlásila. azuredeploy. Parameters. JSON*. Nahraďte **příkaz Get-Unique** jedinečným názvem úložiště konfigurace.

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

1. V okně PowerShellu spusťte následující příkaz, který nasadí úložiště konfigurace aplikace Azure. Nezapomeňte nahradit název skupiny prostředků, cestu k souboru šablony a cestu k souboru parametrů šablony.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

## <a name="add-vm-configuration-key-values"></a>Přidat konfigurační klíč virtuálního počítače – hodnoty

Můžete vytvořit úložiště konfigurace aplikace pomocí šablony Azure Resource Manager, ale je potřeba přidat klíčové hodnoty pomocí Azure Portal nebo rozhraní příkazového řádku Azure CLI. V tomto rychlém startu přidáte klíčové hodnoty pomocí Azure Portal.

1. Po dokončení nasazení přejděte do nově vytvořeného úložiště konfigurace aplikace v [Azure Portal](https://portal.azure.com).

1. Vyberte **nastavení** > **přístupové klíče**. Poznamenejte si primární připojovací řetězec klíče jen pro čtení. Pomocí tohoto připojovacího řetězce později nakonfigurujete aplikaci tak, aby komunikovala s úložištěm konfigurace aplikace, které jste vytvořili.

1. Vyberte **Průzkumník** konfigurace > **vytvořit** a přidejte následující páry klíč-hodnota:

   |Klíč|Hodnota|
   |-|-|
   |windowsOsVersion|2019 – Datacenter|
   |diskSizeGB|1023|
  
   Zadejte *šablonu* pro **popisek**, ale **typ obsahu** zůstane prázdný.

## <a name="deploy-vm-using-stored-key-values"></a>Nasazení virtuálního počítače pomocí uložených hodnot klíčů

Teď, když jste do úložiště přidali klíčové hodnoty, jste připraveni nasadit virtuální počítač pomocí šablony Azure Resource Manager. Šablona odkazuje na klíče **windowsOsVersion** a **diskSizeGB** , které jste vytvořili.

1. Zkopírujte následující kód JSON a vložte ho do nového souboru s názvem *azuredeploy. JSON*nebo ho stáhněte ze šablon pro [rychlý Start Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.json).

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
                        "sku": "[listKeyValue(variables('appConfigRef'), '2019-02-01-preview', variables('windowsOSVersionParameters')).value]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "createOption": "FromImage"
                    },
                    "dataDisks": [
                        {
                            "diskSizeGB": "[listKeyValue(variables('appConfigRef'), '2019-02-01-preview', variables('diskSizeGBParameters')).value]",
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

1. Zkopírujte následující kód JSON a vložte ho do nového souboru s názvem *azuredeploy. Parameters. JSON*nebo ho stáhněte ze šablon pro [rychlý Start Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.parameters.json).

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
   |adminPassword|Heslo správce pro virtuální počítač.|
   |appConfigStoreName|Název úložiště konfigurace aplikace Azure.|
   |appConfigStoreResourceGroup|Skupina prostředků, která obsahuje vaše úložiště konfigurace aplikace.|
   |vmSkuKey|*windowsOSVersion*|
   |diskSizeKey|*diskSizeGB*|
   |adminUsername|Uživatelské jméno správce pro virtuální počítač.|
   |storageAccountName|Jedinečný název účtu úložiště, který je přidružený k virtuálnímu počítači.|
   |domainNameLabel|Jedinečný název domény.|

1. V okně PowerShellu spusťte následující příkaz, který nasadí úložiště konfigurace aplikace Azure. Nezapomeňte nahradit název skupiny prostředků, cestu k souboru šablony a cestu k souboru parametrů šablony.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

Blahopřejeme! Nasadili jste virtuální počítač pomocí konfigurací uložených v konfiguraci aplikace Azure.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, úložiště konfigurace aplikace, virtuální počítač a všechny související prostředky. Pokud plánujete používat úložiště konfigurace aplikace nebo virtuální počítač v budoucnu, můžete ho odstranit. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené tímto rychlým startem spuštěním následující rutiny:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili virtuální počítač pomocí šablony Azure Resource Manager a hodnot klíč-hodnota z konfigurace aplikace Azure.

Další informace o vytváření dalších aplikací s konfigurací aplikace Azure najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Rychlý Start: Vytvoření aplikace ASP.NET Core s využitím konfigurace aplikace Azure](quickstart-aspnet-core-app.md)
