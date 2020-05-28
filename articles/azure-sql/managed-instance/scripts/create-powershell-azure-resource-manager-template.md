---
title: 'Vytvoření instance (šablona ARM & PowerShell) '
titleSuffix: Azure SQL Managed Instance
description: Pomocí tohoto ukázkového skriptu Azure PowerShell vytvořit spravovanou instanci SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-dt-2019
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 55b0c8f569a91075d4cd87541af7aeff5da69f9a
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053967"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-an-azure-sql-managed-instance"></a>Použití PowerShellu se šablonou Azure Resource Manager k vytvoření spravované instance Azure SQL
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Spravovanou instanci Azure SQL je možné vytvořit pomocí knihovny Azure PowerShell a šablon Azure Resource Manager.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, je nutné, aby tento kurz byl AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, spusťte příkaz `Connect-AzAccount` a vytvořte připojení k Azure.

Azure PowerShell příkazy mohou spustit nasazení pomocí předdefinované Azure Resource Manager šablony. V šabloně lze zadat následující vlastnosti:

- Název spravované instance SQL
- Uživatelské jméno a heslo správce SQL.
- Velikost instance (počet jader a maximální velikost úložiště).
- Virtuální síť a podsíť, do které bude instance umístěna.
- Kolace na úrovni serveru instance (Preview).

Název instance, uživatelské jméno správce SQL, virtuální síť/podsíť a kolaci nelze později změnit. Vlastnosti jiných instancí lze změnit.

## <a name="prerequisites"></a>Požadavky

V této ukázce se předpokládá, že jste [vytvořili platné síťové prostředí](../virtual-network-subnet-create-arm-template.md) nebo [upravili stávající virtuální síť](../vnet-existing-add-subnet.md) pro spravovanou instanci SQL. V případě potřeby můžete v případě potřeby připravit síťové prostředí pomocí samostatné [spravované šablony prostředků Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment). 


Ukázka používá rutiny [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) a [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) , takže se ujistěte, že máte nainstalované následující moduly PowerShellu:

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

Po dokončení skriptu se ke spravované instanci SQL dostanete ze všech služeb Azure a nakonfigurovaných IP adres.

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu spravované instance SQL najdete v [skriptech PowerShell spravované instance Azure SQL](../../database/powershell-script-content-guide.md).
