---
title: Vytvoření spravované instance (šablona ARM & PowerShell)
titleSuffix: Azure SQL Managed Instance
description: Pomocí tohoto Azure PowerShell příkladu skriptu vytvořte spravovanou instanci.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-dt-2019, devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 7c364fe7fa0ac4dd70a01a75478289ea861dee7f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790827"
---
# <a name="use-powershell-with-an-azure-resource-manager-template-to-create-a-managed-instance"></a>Použití PowerShellu se šablonou Azure Resource Manager k vytvoření spravované instance

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Spravovanou instanci můžete vytvořit pomocí knihovny Azure PowerShell a šablon Azure Resource Manager.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento kurz Azure PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, spusťte příkaz `Connect-AzAccount` a vytvořte připojení k Azure.

Azure PowerShell příkazy mohou spustit nasazení pomocí předdefinované Azure Resource Manager šablony. V šabloně lze zadat následující vlastnosti:

- Název spravované instance
- Uživatelské jméno a heslo správce SQL.
- Velikost instance (počet jader a maximální velikost úložiště).
- Virtuální síť a podsíť, do které bude instance umístěna.
- Kolace na úrovni serveru instance (Preview).

Název instance, uživatelské jméno správce SQL, virtuální síť/podsíť a kolaci nelze později změnit. Vlastnosti jiných instancí lze změnit.

## <a name="prerequisites"></a>Předpoklady

V této ukázce se předpokládá, že jste [vytvořili platné síťové prostředí](../virtual-network-subnet-create-arm-template.md) nebo [upravili stávající virtuální síť](../vnet-existing-add-subnet.md) pro spravovanou instanci. V případě potřeby můžete v případě potřeby připravit síťové prostředí pomocí samostatné [šablony Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment). 


Ukázka používá rutiny [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) a [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork), takže se ujistěte, že máte nainstalované následující moduly PowerShellu:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru


Uložte následující skript do souboru. JSON a poznamenejte si umístění souboru: 

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
                "name": "GP_Gen5",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
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

Aktualizujte následující skript PowerShellu se správnou cestou k souboru. JSON, který jste předtím uložili, a změňte názvy objektů ve skriptu:

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed instance properties
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

# Deploy instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

Po dokončení skriptu se ke spravované instanci dostanete ze všech služeb Azure a nakonfigurovaných IP adres.

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShell najdete v [dokumentaci k Azure PowerShell](/powershell/azure/).

Další ukázkové skripty PowerShellu pro spravovanou instanci Azure SQL najdete ve [skriptech PowerShellu spravovaných instancí Azure SQL](../../database/powershell-script-content-guide.md).