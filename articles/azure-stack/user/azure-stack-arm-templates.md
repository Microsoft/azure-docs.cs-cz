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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: 456f27b97ee644aef34f9bb9e2c0525bd61c1c84
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42058656"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Použití šablon Azure Resource Manageru ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Šablony Azure Resource Manageru můžete použít k nasazení a zřiďte všechny prostředky pro vaši aplikaci v rámci jediné koordinované operace. Znovu nasadit šablony měnit prostředky ve skupině prostředků.

Tyto šablony se dá nasadit pomocí portálu Microsoft Azure Stack, Powershellu, příkazového řádku a sady Visual Studio.

Jsou k dispozici v následujících šablon rychlý Start [Githubu](http://aka.ms/azurestackgithub).

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Nasazení serveru SharePoint (nasazení vysoké dostupnosti)

Pomocí rozšíření PowerShell DSC můžete vytvořit farmu služby SharePoint Server 2013, která zahrnuje následující prostředky:

* Virtuální síť
* 3 účty úložiště
* Dva nástroje pro vyrovnávání zatížení externí
* Jeden virtuální počítač (VM) nakonfigurovaný jako řadič domény v nové doménové struktuře s jednou doménou
* Jeden virtuální počítač nakonfigurovaný jako samostatný server SQL Server 2014
* Jeden virtuální počítač nakonfigurovaný jako farmy služby SharePoint Server 2013 jeden počítač

## <a name="deploy-ad-non-high-availability-deployment"></a>Nasazení AD (bez vysoce – dostupnost – nasazení)

Pomocí rozšíření PowerShell DSC můžete vytvořit server řadiče domény AD, která zahrnuje následující prostředky:

* Virtuální síť
* Jeden účet úložiště
* Jeden nástroj pro vyrovnávání zatížení externí
* Jeden virtuální počítač (VM) nakonfigurovaný jako řadič domény v nové doménové struktuře s jednou doménou

## <a name="deploy-adsql-non-high-availability-deployment"></a>Nasazení AD/SQL (bez vysoce – dostupnost – nasazení)

Pomocí rozšíření PowerShell DSC můžete vytvořit samostatný server SQL Server 2014, která zahrnuje následující prostředky:

* Virtuální síť
* Dva účty úložiště
* Jeden nástroj pro vyrovnávání zatížení externí
* Jeden virtuální počítač (VM) nakonfigurovaný jako řadič domény v nové doménové struktuře s jednou doménou
* Jeden virtuální počítač nakonfigurovaný jako samostatný server SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Pomocí rozšíření PowerShell DSC ke konfiguraci existujícího virtuálního počítače místní Configuration Manageru (LCM) a zaregistrovat do Azure Automation DSC o přijetí změn Server účtů.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Vytvoření virtuálního počítače z uživatelské image

Vytvoření virtuálního počítače z vlastní uživatelská image. Tato šablona také nasadí virtuální sítě (pomocí DNS), veřejné IP adresy a síťové rozhraní.

## <a name="basic-virtual-machine"></a>Základní virtuální počítač

Nasazení virtuálního počítače s Windows, který zahrnuje virtuální síť (pomocí DNS), veřejné IP adresy a síťové rozhraní.

## <a name="cancel-a-running-template-deployment"></a>Zrušit běžící šablona nasazení

Chcete-li zrušit spuštěné nasazení šablony, použijte `Stop-AzureRmResourceGroupDeployment` rutiny Powershellu.

## <a name="next-steps"></a>Další postup

* [Nasazení šablon pomocí portálu](azure-stack-deploy-template-portal.md)
* [Přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md)
