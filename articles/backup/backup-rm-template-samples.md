---
title: Šablony Azure Resource Manageru
description: Azure Resource Manager šablony pro použití s trezory Recovery Services a Azure Backupmi funkcemi
ms.topic: sample
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 29a2499bfd3125cad98e72f7543bb9a29293f624
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88755190"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Šablony Azure Resource Manageru pro službu Azure Backup

Následující tabulka obsahuje odkazy na šablony Azure Resource Manageru pro použití s trezory služby Recovery Services a funkcemi služby Azure Backup. Další informace o syntaxi a vlastnostech JSON najdete v tématu [typy prostředků Microsoft. RecoveryServices](/azure/templates/microsoft.recoveryservices/allversions).

| Šablona | Popis |
|---|---|
|**Trezor služby Recovery Services** | |
| [Vytvoření trezoru Služeb zotavení](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Vytvořte trezor služby Recovery Services. Trezor je možné použít pro služby Azure Backup a Azure Site Recovery. |
|**Zálohování virtuálních počítačů**| |
| [Zálohování virtuálních počítačů Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Použijte stávající trezor služby Recovery Services a zásady zálohování k zálohování virtuálních počítačů Resource Manageru ve stejné skupině prostředků.|
| [Zálohování virtuálních počítačů IaaS do trezoru služby Recovery Services](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Šablona pro zálohování virtuálních počítačů Classic a Resource Manageru. |
| [Vytvoření zásady pro týdenní zálohování virtuálních počítačů IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | Šablona vytvoří Recovery Services trezor a týdenní zásadu zálohování, která slouží k zálohování klasických a Správce prostředkůch virtuálních počítačů.|
| [Vytvoření zásady pro denní zálohování virtuálních počítačů IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | Šablona vytvoří Recovery Services trezor a zásady denního zálohování, které slouží k zálohování klasických a Správce prostředkůch virtuálních počítačů.|
| [Nasazení virtuálního počítače s Windows Serverem a povoleným zálohováním](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | Šablona vytvoří virtuální počítač s Windows Serverem a trezor služby Recovery Services s povolenou výchozí zásadou zálohování.|
|**Monitorování úloh zálohování** |  |
| [Použití protokolů Azure Monitor s Azure Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Šablona nasadí Azure Monitor protokoly pomocí Azure Backup, což vám umožní monitorovat úlohy zálohování a obnovení, výstrahy zálohování a cloudové úložiště, které se používají ve vašich úložištích Recovery Services.|  
|**Zálohování SQL Server na virtuálním počítači Azure** |  |
| [Zálohování SQL Server na virtuálním počítači Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | Šablona vytvoří Recovery Services trezor a zásady zálohování specifické pro úlohy. Registruje virtuální počítač pomocí služby Azure Backup Service a nakonfiguruje ochranu na tomto virtuálním počítači. V současné době funguje pouze pro image v galerii SQL. |
|   |   |
