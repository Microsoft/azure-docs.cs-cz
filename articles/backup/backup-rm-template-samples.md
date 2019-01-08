---
title: Šablony Azure Resource Manageru pro službu Azure Backup
description: Ukázky PowerShellu pro službu Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 04/18/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: ffd153490938c232bdf8b5d4e40a2d43ced9f57b
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063264"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Šablony Azure Resource Manageru pro službu Azure Backup

Následující tabulka obsahuje odkazy na šablony Azure Resource Manageru pro použití s trezory služby Recovery Services a funkcemi služby Azure Backup. Další informace o syntaxi JSON a vlastnostech najdete v tématu [Microsoft.recoveryservices typy prostředků](/azure/templates/microsoft.recoveryservices/allversions).

|   |   |
|---|---|
|**Trezor služby Recovery Services** | |
| [Vytvoření trezoru služby Recovery Services](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Vytvořte trezor služby Recovery Services. Trezor je možné použít pro služby Azure Backup a Azure Site Recovery. |
|**Zálohování virtuálních počítačů**| |
| [Zálohování virtuálních počítačů Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Použijte stávající trezor služby Recovery Services a zásady zálohování k zálohování virtuálních počítačů Resource Manageru ve stejné skupině prostředků.|
| [Zálohování virtuálních počítačů IaaS do trezoru služby Recovery Services](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Šablona pro zálohování virtuálních počítačů Classic a Resource Manageru. |
| [Vytvoření zásady pro týdenní zálohování virtuálních počítačů IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | Šablona vytvoří trezor služby Recovery Services a zásadu pro týdenní zálohování, která se použije k zálohování virtuálních počítačů Classic a Resource Manageru.|
| [Vytvoření zásady pro denní zálohování virtuálních počítačů IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | Šablona vytvoří trezor služby Recovery Services a zásadu pro denní zálohování, která se použije k zálohování virtuálních počítačů Classic a Resource Manageru.|
| [Nasazení virtuálního počítače s Windows Serverem a povoleným zálohováním](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | Šablona vytvoří virtuální počítač s Windows Serverem a trezor služby Recovery Services s povolenou výchozí zásadou zálohování.|
|**Monitorování úloh služby Backup** |  |
| [Použití Log Analytics k monitorování služby Azure Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Šablona nasadí monitorování Log Analytics pro službu Azure Backup, které umožňuje monitorovat úlohy zálohování a obnovení, výstrahy zálohování a cloudové úložiště využívané ve vašich trezorech služby Recovery Services.|  
|   |   |

