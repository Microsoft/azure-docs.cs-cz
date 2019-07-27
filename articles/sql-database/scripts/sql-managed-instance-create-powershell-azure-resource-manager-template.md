---
title: Příklad PowerShellu – vytvoření spravované instance v Azure SQL Database | Microsoft Docs
description: Azure PowerShell ukázkový skript pro vytvoření spravované instance v Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 5a60e8efad41c94deeedd545e6e0c1c96ff04e25
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569726"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>Použití PowerShellu se šablonou Azure Resource Manager k vytvoření spravované instance v Azure SQL Database

Azure SQL Database spravovanou instanci lze vytvořit pomocí Azure PowerShell knihovny a šablon Azure Resource Manager.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

Příkazy Azure PowerShell mohou spustit nasazení pomocí předdefinované Azure Resource Manager šablony. V šabloně lze zadat následující vlastnosti:

- Název instance
- Uživatelské jméno a heslo správce SQL.
- Velikost instance (počet jader a maximální velikost úložiště).
- Virtuální síť a podsíť, do které bude instance umístěna.
- Kolace na úrovni serveru instance (Preview).

Název instance, uživatelské jméno správce SQL, virtuální síť/podsíť a kolaci nelze později změnit. Vlastnosti jiných instancí lze změnit.

## <a name="prerequisites"></a>Požadavky

V této ukázce se předpokládá, že jste [vytvořili platné síťové prostředí](../sql-database-managed-instance-create-vnet-subnet.md) nebo [upravili stávající virtuální síť](../sql-database-managed-instance-configure-vnet-subnet.md) pro spravovanou instanci. Ukázka používá rutiny [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) a [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) , takže se ujistěte, že máte nainstalované následující moduly PowerShellu:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

Následující obsah by měl být umístěn v souboru, který představuje šablonu, která bude použita k vytvoření instance:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen4",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen4",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

Předpokladem je, že virtuální síť Azure s řádně nakonfigurovanou podsítí již existuje. Pokud nemáte správně nakonfigurovanou podsíť, připravte síťové prostředí pomocí samostatné [spravované šablony prostředků Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) , kterou je možné spustit nezávisle nebo do této šablony.

Uložte obsah tohoto souboru jako soubor. JSON, vložte cestu k souboru do následujícího skriptu PowerShellu a změňte názvy objektů ve skriptu:

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed Instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy Instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

Po úspěšném spuštění skriptu bude služba SQL Database přístupná ze všech služeb Azure a nakonfigurovaných IP adres.

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
