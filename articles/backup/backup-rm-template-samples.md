---
title: Šablony Azure Resource Manageru
description: Šablony Azure Resource Manageru pro použití s trezory služby Recovery Services a funkcemi Azure Backup
ms.topic: sample
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 3c05f345c6c5df5d0350c75e52381c24b0609501
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74172166"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Šablony Azure Resource Manageru pro službu Azure Backup

Následující tabulka obsahuje odkazy na šablony Azure Resource Manageru pro použití s trezory služby Recovery Services a funkcemi služby Azure Backup. Další informace o syntaxi a vlastnostech JSON naleznete v [tématu Typy prostředků Microsoft.RecoveryServices](/azure/templates/microsoft.recoveryservices/allversions).

|   |   |
|---|---|
|**Trezor služby Recovery Services** | |
| [Vytvoření trezoru služby Recovery Services](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Vytvořte trezor služby Recovery Services. Trezor je možné použít pro služby Azure Backup a Azure Site Recovery. |
|**Zálohování virtuálních počítačů**| |
| [Zálohování virtuálních počítačů Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Použijte stávající trezor služby Recovery Services a zásady zálohování k zálohování virtuálních počítačů Resource Manageru ve stejné skupině prostředků.|
| [Zálohování virtuálních počítačů IaaS do trezoru služby Recovery Services](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Šablona pro zálohování virtuálních počítačů Classic a Resource Manageru. |
| [Vytvoření zásady pro týdenní zálohování virtuálních počítačů IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | Šablona vytvoří trezor služby Recovery Services a týdenní zásady zálohování, které se používají k zálohování klasických virtuálních počítačů správce prostředků.|
| [Vytvoření zásady pro denní zálohování virtuálních počítačů IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | Šablona vytvoří trezor služby Recovery Services a zásady denního zálohování, které se používají k zálohování klasických virtuálních počítačů správce prostředků.|
| [Nasazení virtuálního počítače s Windows Serverem a povoleným zálohováním](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | Šablona vytvoří virtuální počítač s Windows Serverem a trezor služby Recovery Services s povolenou výchozí zásadou zálohování.|
|**Monitorovat úlohy zálohování** |  |
| [Použití protokolů Azure Monitor u Azure Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Šablona nasazuje protokoly Azure Monitor s Azure Backup, který umožňuje sledovat zálohování a obnovení úloh, výstrahy zálohování a cloudové úložiště používané ve vašich trezorech služby Recovery Services.|  
|**Zálohování SQL Serveru ve virtuálním počítači Azure** |  |
| [Zálohování SQL Serveru ve virtuálním počítači Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | Šablona vytvoří úložiště služby Recovery Services a zásady zálohování specifické pro úlohy. Zaregistruje virtuální počítač se službou Azure Backup a nakonfiguruje ochranu na tomto virtuálním počítači. V současné době funguje pouze pro bitové kopie GALERIE SQL. |
|   |   |
