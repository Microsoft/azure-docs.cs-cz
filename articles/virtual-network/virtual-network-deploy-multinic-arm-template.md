---
title: "Vytvoření virtuálního počítače s více síťovými kartami - šablony Azure Resource Manageru | Microsoft Docs"
description: "Vytvoření virtuálního počítače s více síťovými kartami pomocí šablony Azure Resource Manager."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 486f7dd5-cf2f-434c-85d1-b3e85c427def
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 85bfa264c6cf2b0586816a47b3ab72f3aee8ec96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-multiple-nics-using-a-template"></a>Vytvoření virtuálního počítače s více síťovými kartami pomocí šablony
[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../resource-manager-deployment-model.md).  Tento článek se věnuje modelu nasazení Resource Manager, který Microsoft doporučuje pro většinu nových nasazení namísto [klasického modelu nasazení](virtual-network-deploy-multinic-classic-ps.md).
> 

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Následující postup použijte skupinu prostředků s názvem *IaaSStory* pro webové servery a skupinu prostředků s názvem *IaaSStory back-end* pro servery DB.

## <a name="prerequisites"></a>Požadavky
Před vytvořením servery DB, je potřeba vytvořit *IaaSStory* skupina prostředků se všechny potřebné prostředky pro tento scénář. Pokud chcete vytvořit tyto prostředky, proveďte následující kroky:

1. Přejděte na [na stránku šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Na stránce šablony napravo od **nadřazené skupiny prostředků**, klikněte na tlačítko **nasadit do Azure**.
3. V případě potřeby změňte hodnoty parametrů a potom postupujte podle kroků v portálu Azure preview nasazení skupiny prostředků.

> [!IMPORTANT]
> Ujistěte se, že vaše názvy účtů úložiště jsou jedinečné. Názvy účtů úložiště duplicitní nemůže mít v Azure.
> 

## <a name="understand-the-deployment-template"></a>Pochopení šablony nasazení
Před nasazením šablony dodávané s tuto dokumentaci, ujistěte se, že chápete, jak funguje. Následující kroky poskytují dobrý přehled o šabloně:

1. Přejděte na [na stránku šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Klikněte na tlačítko **azuredeploy.json** otevřete soubor šablony.
3. Upozornění *osType* parametr uvedené níže. Tento parametr slouží k výběru jaké image virtuálního počítače používat pro databázový server, společně s více operačního systému související nastavení.

    ```json
    "osType": {
      "type": "string",
      "defaultValue": "Windows",
      "allowedValues": [
        "Windows",
        "Ubuntu"
      ],
      "metadata": {
      "description": "Type of OS to use for VMs: Windows or Ubuntu."
      }
    },
    ```

4. Posuňte se dolů a seznam proměnných a zkontrolujte definici **dbVMSetting** proměnné, které jsou uvedeny níže. Obdrží jeden prvků pole, které jsou součástí **dbVMSettings** proměnné. Pokud jste obeznámeni s terminologie vývoj softwaru, můžete zobrazit **dbVMSettings** proměnné jako zatřiďovací tabulku nebo slovník.

    ```json
    "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"
    ```

5. Předpokládejme, že se rozhodnete nasadit virtuální počítače Windows se systémem SQL v back-end. Pak hodnota **osType** by *Windows*a **dbVMSetting** proměnná by obsahovat element uvedené níže, který představuje první hodnota v **dbVMSettings** proměnné.

    ```json
    "Windows": {
      "vmSize": "Standard_DS3",
      "publisher": "MicrosoftSQLServer",
      "offer": "SQL2014SP1-WS2012R2",
      "sku": "Standard",
      "version": "latest",
      "vmName": "DB",
      "osdisk": "osdiskdb",
      "datadisk": "datadiskdb",
      "nicName": "NICDB",
      "ipAddress": "192.168.2.",
      "extensionDeployment": "",
      "avsetName": "ASDB",
      "remotePort": 3389,
      "dbPort": 1433
    },
    ```

6. Upozornění **vmSize** obsahuje hodnotu *Standard_DS3*. Povolit jenom určité velikosti virtuálních počítačů použít několik síťových adaptérů. Můžete ověřit, které velikosti virtuálních počítačů ve čtení podporovat několik síťových adaptérů [velikosti virtuálních počítačů Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a [velikosti virtuálního počítače s Linuxem](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) články.

7. Přejděte dolů k položce **prostředky** a Všimněte si první prvek. Popisuje účet úložiště. Tento účet úložiště se použije k udržování datové disky používané každou databázi virtuálních počítačů. V tomto scénáři každou databázi virtuálních počítačů má disk s operačním systémem, který je uložen v pravidelných úložiště a dvě datové disky uložené v úložiště SSD (premium).

    ```json
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('prmStorageName')]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "Storage Account - Premium"
      },
      "properties": {
      "accountType": "[parameters('prmStorageType')]"
      }
    },
    ```

8. Posuňte se dolů a další zdroje, jak je uvedeno dále. Tento prostředek představuje síťová karta použitá pro přístup k databázi v každé databázi virtuálních počítačů. Všimněte si použití **kopie** funkce v tento prostředek. Šablona umožňuje nasadit jako hodně virtuálních počítačů tak, jak chcete, založené na **dbCount** parametr. Proto musíte vytvořit stejnou úroveň síťové karty pro přístup k databázi, jednu pro každý virtuální počítač.

    ```json
    {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
    "location": "[variables('location')]",
    "tags": {
      "displayName": "NetworkInterfaces - DB DA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "privateIPAllocationMethod": "Static",
            "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
            "subnet": {
              "id": "[variables('backEndSubnetRef')]"
            }
          }
         }
       ] 
     }
    },
    ```

9. Posuňte se dolů a další zdroje, jak je uvedeno dále. Tento prostředek představuje síťový adaptér používá pro správu v každé databázi virtuálních počítačů. Znovu budete potřebovat jeden z tyto síťové adaptéry pro každou databázi virtuálních počítačů. Upozornění **skupinu zabezpečení sítě** elementu, skupinu NSG, které umožňuje přístup k protokolu RDP/SSH pro tuto síťovou kartu pouze propojení.

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "NetworkInterfaces - DB RA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "networkSecurityGroup": {
             "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
             },
             "privateIPAllocationMethod": "Static",
             "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
             "subnet": {
              "id": "[variables('backEndSubnetRef')]"
             }
           }
          }
        ]
      }
    },
```

10. Posuňte se dolů a další zdroje, jak je uvedeno dále. Tento prostředek představuje sadu ke sdílení všechny virtuální počítače databáze dostupnosti. Tímto způsobem můžete zaručit, že bude vždy jeden virtuální počítač v sadě během údržby.

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/availabilitySets",
      "name": "[variables('dbVMSetting').avsetName]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "AvailabilitySet - DB"
      }
    },
    ```

11. Posuňte se dolů a další prostředků. Tento prostředek představuje databázi virtuálních počítačů, tak v prvním několika řádků, které jsou uvedeny níže. Všimněte si použití **kopie** funkce znovu, zajistíte, že se vytvářejí na základě několika virtuálními počítači **dbCount** parametr. Všimněte si také **dependsOn** kolekce. Zobrazí se seznam dva síťové adaptéry je nutné vytvořit před nasazením virtuálního počítače, společně s skupiny dostupnosti a účet úložiště.

    ```json
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
    "location": "[variables('location')]",
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
      "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
      "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
    ],
    "tags": {
      "displayName": "VMs - DB"
    },
    "copy": {
      "name": "dbvmcount",
      "count": "[parameters('dbCount')]"
    },
    ```

12. Přejděte dolů v prostředek virtuálního počítače, který **networkProfile** elementu, jak je uvedeno dále. Všimněte si, že existují dva síťové adaptéry se referenční informace pro každý virtuální počítač. Když vytvoříte několik síťových adaptérů pro virtuální počítač, je nutné nastavit **primární** vlastnost jedné síťové karty na *true*a zbytek na *false*.

    ```json
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
          "properties": { "primary": true }
        },
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
          "properties": { "primary": false }
        }
      ]
    }
    ```

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Nasazení šablony ARM pomocí metody Click to Deploy

> [!IMPORTANT]
> Použijte [předpoklady](#Pre-requisites) kroky před níže uvedených pokynů.
> 

Ukázková šablona, která je k dispozici ve veřejném úložišti, používá soubor parametrů obsahující výchozí hodnoty, které se použijí k vygenerování výše popsaného scénáře. K nasazení této šablony metody click to deploy, postupujte podle [tento odkaz](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC), napravo od **skupina prostředků back-end (viz dokumentace)** klikněte na tlačítko **nasadit do Azure**, nahradí výchozí hodnoty parametrů v případě potřeby a postupujte podle pokynů v portálu.

Následující obrázek znázorňuje obsah novou skupinu prostředků, po nasazení.

![Skupina prostředků back-end](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>Nasazení šablony pomocí prostředí PowerShell
Pokud chcete nasadit šablonu stáhnout pomocí prostředí PowerShell, instalace a konfigurace prostředí PowerShell pomocí kroků v [instalace a konfigurace prostředí PowerShell](/powershell/azure/overview) článku a potom proveďte následující kroky:

Spustit  **`New-AzureRmResourceGroup`**  vytvořte skupinu prostředků pomocí šablony.

```powershell
New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
-TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'
```

Očekávaný výstup:

    ResourceGroupName : IaaSStory-Backend
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions  NotActions
                        =======  ==========
                        *
        Resources         :
                        Name                 Type                                 Location
                        ===================  ===================================  ========
                        ASDB                 Microsoft.Compute/availabilitySets   westus  
                        DB1                  Microsoft.Compute/virtualMachines    westus  
                        DB2                  Microsoft.Compute/virtualMachines    westus  
                        NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                        wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  
    ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Nasazení šablony pomocí rozhraní příkazového řádku Azure
Pokud chcete nasadit šablonu pomocí rozhraní příkazového řádku Azure, použijte následující postup.

1. Pokud jste rozhraní příkazového řádku Azure nikdy nepoužívali, přejděte na téma [Instalace a konfigurace rozhraní příkazového řádku Azure](../cli-install-nodejs.md) a postupujte podle pokynů až do chvíle, kdy můžete vybrat svůj účet a předplatné Azure.
2. Spuštěním příkazu **`azure config mode`** přepněte do režimu Resource Manager, jak vidíte níže.

    ```azurecli
    azure config mode arm
    ```

    Očekávaný výstup zahrnuje:

        info:    New mode is arm

3. Otevřete [soubor parametrů](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), vyberte svůj obsah a uložte je do souboru v počítači. V tomto příkladu jsme uložili soubor parametrů do souboru *parameters.json*.
4. Spuštěním rutiny **`azure group deployment create`** nasadíte novou síť VNet pomocí šablony a souborů parametrů, které jste stáhli a upravili v předchozích krocích. Seznam uvedený za výstupem vysvětluje použité parametry.

    ```azurecli
    azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json
    ```

    Očekávaný výstup:
   
        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

