---
title: Použití šablon Azure Resource Manager v zásobníku Azure | Microsoft Docs
description: Další informace o použití šablon Azure Resource Manageru v Azure zásobníku k přidělení prostředků.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 9c4d538f77ae056163fd17aa547162a4ad3eff63
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34301674"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Použití šablon Azure Resource Manageru v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Šablony Azure Resource Manager můžete použít k nasazení a zřizování všechny prostředky pro aplikaci v rámci jediné koordinované operace. Znovu nasadit šablony provádět změny prostředky ve skupině prostředků.

Tyto šablony se dají nasadit na portálu Microsoft Azure zásobníku, PowerShell, příkazový řádek a Visual Studio.

Následující šablony rychlý Start jsou k dispozici na [Githubu](http://aka.ms/azurestackgithub).

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Nasazení serveru SharePoint (vysoké dostupnosti nasazení)

Použití rozšíření DSC prostředí PowerShell vytvořit farmu SharePoint Server 2013, který obsahuje následující zdroje:

* Virtuální síť
* Tři účty úložiště
* Dva nástroje pro vyrovnávání zatížení externí
* Jeden virtuální počítač (VM) nakonfigurovaný jako řadič domény v nové doménové struktuře s jednou doménou
* Jeden virtuální počítač nakonfigurovaný jako samostatný server SQL Server 2014
* Jeden virtuální počítač nakonfigurovaný jako jeden počítač farmy služby SharePoint Server 2013

## <a name="deploy-ad-non-high-availability-deployment"></a>Nasazení AD (bez vysokou dostupnosti nasazení)

Rozšíření DSC prostředí PowerShell použijte k vytvoření server řadiče domény služby AD, který obsahuje následující zdroje:

* Virtuální síť
* Jeden účet úložiště
* Jeden externím vyrovnáváním zatížení
* Jeden virtuální počítač (VM) nakonfigurovaný jako řadič domény v nové doménové struktuře s jednou doménou

## <a name="deploy-adsql-non-high-availability-deployment"></a>Nasazení AD/SQL (bez vysokou dostupnosti nasazení)

Pomocí rozšíření DSC prostředí PowerShell můžete vytvořit samostatný server SQL Server 2014, která obsahuje následující zdroje:

* Virtuální síť
* Dva účty úložiště
* Jeden externím vyrovnáváním zatížení
* Jeden virtuální počítač (VM) nakonfigurovaný jako řadič domény v nové doménové struktuře s jednou doménou
* Jeden virtuální počítač nakonfigurovaný jako samostatný server SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Použití rozšíření DSC prostředí PowerShell ke konfiguraci existujícího virtuálního počítače místní Configuration Manager (LCM) a zaregistrovat ho Azure Automation DSC pro vyžádání obsahu Server účtů.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Vytvoření virtuálního počítače z uživatelského image

Vytvořte virtuální počítač z bitové kopie vlastní uživatele. Tato šablona také nasadí virtuální sítě (pomocí DNS), veřejné IP adresy a síťové rozhraní.

## <a name="basic-virtual-machine"></a>Základní virtuální počítač

Nasaďte virtuální počítač Windows, která obsahuje virtuální síť (s DNS), veřejné IP adresy a síťové rozhraní.

## <a name="cancel-a-running-template-deployment"></a>Zrušit spuštěného nasazení šablony

Chcete-li zrušit spuštěného šablony nasazení, použijte `Stop-AzureRmResourceGroupDeployment` rutiny prostředí PowerShell.

## <a name="next-steps"></a>Další postup

* [Nasazení šablon pomocí portálu](azure-stack-deploy-template-portal.md)
* [Přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md)
