---
title: Příklad šablony – vytvoření spravované instance v Azure SQL Database
description: Pomocí tohoto ukázkového skriptu Azure PowerShellu vytvořte spravovanou instanci v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-dt-2019
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: a349be9ada756742f5fd5ba4819caa1d2a2d3268
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373156"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-a-managed-instance-in-azure-sql-database"></a>Vytvoření spravované instance v Azure SQL Database pomocí PowerShellu se šablonou Azure Resource Manager

Spravovaná instance Azure SQL Database se dá vytvořit pomocí knihovny Azure PowerShell a šablon Azure Resource Manager.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat prostředí PowerShell místně, tento kurz vyžaduje AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

Příkazy Azure PowerShellu můžou začít nasazovat pomocí předdefinované šablony Azure Resource Manageru. V šabloně lze zadat následující vlastnosti:

- Název instance
- Uživatelské jméno a heslo správce SQL.
- Velikost instance (počet jader a maximální velikost úložiště).
- Virtuální síť a podsíť, kde bude instance umístěna.
- Řazení instance na úrovni serveru (náhled).

Název instance, uživatelské jméno správce SQL, virtuální síť/podsíť a řazení nelze později změnit. Ostatní vlastnosti instance lze změnit.

## <a name="prerequisites"></a>Požadavky

Tato ukázka předpokládá, že jste [vytvořili platné síťové prostředí](../sql-database-managed-instance-create-vnet-subnet.md) nebo [upravili existující virtuální síť](../sql-database-managed-instance-configure-vnet-subnet.md) pro spravovanou instanci. Ukázka používá rutiny [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) a [Get-AzVirtualNetwork,](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) takže se ujistěte, že jste nainstalovali následující moduly prostředí PowerShell:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

Následující obsah by měl být umístěn do souboru, který představuje šablonu, která bude použita k vytvoření instance:

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

Předpoklad em je, že virtuální síť Azure se správně nakonfigurovanou podsítí už existuje. Pokud nemáte správně nakonfigurovanou podsíť, připravte síťové prostředí pomocí samostatné [šablony Azure Resource Managed,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) která může být spuštěna nezávisle nebo zahrnuta do této šablony.

Uložte obsah tohoto souboru jako soubor JSON, vložte cestu k souboru do následujícího skriptu prostředí PowerShell a změňte názvy objektů ve skriptu:

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

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
