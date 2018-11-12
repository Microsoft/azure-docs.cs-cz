---
title: Použití šablon Azure Resource Manageru ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o použití šablon Azure Resource Manageru ve službě Azure Stack pro zřízení prostředků.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2018
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: d520a33fd6729f4a9d4778cfdb8d3380c9ac09b0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51237838"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Použití šablon Azure Resource Manageru ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Šablony Azure Resource Manageru můžete použít k nasazení a zřiďte všechny prostředky pro vaši aplikaci v rámci jediné koordinované operace. Znovu nasadit šablony měnit prostředky ve skupině prostředků.

Tyto šablony se dá nasadit pomocí portálu Microsoft Azure Stack, Powershellu, příkazového řádku a sady Visual Studio.

Jsou k dispozici v následujících šablon rychlý Start [Githubu](https://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Nasazení serveru SharePoint (nasazení vysoké dostupnosti)

Pomocí rozšíření PowerShell DSC pro [Vytvoření farmy serverů SharePoint Server 2013](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/AzureStackTechnicalPreview1/sharepoint-2013-non-ha) , který obsahuje následující zdroje:

* Virtuální síť
* 3 účty úložiště
* Dva nástroje pro vyrovnávání zatížení externí
* Jeden virtuální počítač (VM) nakonfigurovaný jako řadič domény v nové doménové struktuře s jednou doménou
* Jeden virtuální počítač nakonfigurovaný jako samostatný server SQL Server 2014
* Jeden virtuální počítač nakonfigurovaný jako farmy služby SharePoint Server 2013 jeden počítač

## <a name="deploy-ad-non-high-availability-deployment"></a>Nasazení AD (bez vysoce – dostupnost – nasazení)

Pomocí rozšíření PowerShell DSC pro [vytvoření serveru řadiče domény AD](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/AzureStackTechnicalPreview1/ad-non-ha) , který obsahuje následující zdroje:

* Virtuální síť
* Jeden účet úložiště
* Jeden nástroj pro vyrovnávání zatížení externí
* Jeden virtuální počítač (VM) nakonfigurovaný jako řadič domény v nové doménové struktuře s jednou doménou

## <a name="deploy-adsql-non-high-availability-deployment"></a>Nasazení AD/SQL (bez vysoce – dostupnost – nasazení)

Pomocí rozšíření PowerShell DSC pro [vytvořit samostatný server SQL Server 2014](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/AzureStackTechnicalPreview1/sql-2014-non-ha) , který obsahuje následující zdroje:

* Virtuální síť
* Dva účty úložiště
* Jeden nástroj pro vyrovnávání zatížení externí
* Jeden virtuální počítač (VM) nakonfigurovaný jako řadič domény v nové doménové struktuře s jednou doménou
* Jeden virtuální počítač nakonfigurovaný jako samostatný server SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Pomocí rozšíření PowerShell DSC ke konfiguraci existujícího virtuálního počítače místní Configuration Manageru (LCM) a zaregistrovat do Azure Automation DSC o přijetí změn Server účtů.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Vytvoření virtuálního počítače z uživatelské image

[Vytvoření virtuálního počítače z vlastní uživatelská image](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/AzureStackTechnicalPreview1/101-vm-from-user-image). Tato šablona také nasadí virtuální sítě (pomocí DNS), veřejné IP adresy a síťové rozhraní.

## <a name="basic-virtual-machine"></a>Základní virtuální počítač

[Nasazení virtuálního počítače s Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/AzureStackTechnicalPreview1/101-simple-windows-vm) , který zahrnuje virtuální síť (pomocí DNS), veřejné IP adresy a síťové rozhraní.

## <a name="cancel-a-running-template-deployment"></a>Zrušit běžící šablona nasazení

Chcete-li zrušit spuštěné nasazení šablony, použijte [Stop-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/stop-azurermresourcegroupdeployment) rutiny Powershellu.

## <a name="next-steps"></a>Další postup

* [Nasazení šablon pomocí portálu](azure-stack-deploy-template-portal.md)
* [Přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md)
