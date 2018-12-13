---
title: Zálohování Azure Virtual Machines ve velkém měřítku v Azure
description: Tento kurz podrobně popisuje zálohování několika virtuálních počítačů Azure do trezoru služby Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
keywords: virtual machine backup; back up virtual machine; backup and disaster recovery
ms.service: backup
ms.topic: tutorial
ms.date: 09/06/2017
ms.author: trinadhk
ms.custom: mvc
ms.openlocfilehash: d2b83963f7af52101ed298e85b6c7fd64fc99a07
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52875582"
---
# <a name="back-up-azure-virtual-machines-in-azure-at-scale"></a>Zálohování virtuálních počítačů Azure ve velkém měřítku v Azure

Tento kurz podrobně popisuje zálohování virtuálních počítačů Azure do trezoru služby Recovery Services. Většina práce při zálohování virtuálních počítačů spočívá v přípravě. Než budete moct zálohovat (nebo chránit) virtuální počítač, musíte dokončit [požadavky](backup-azure-arm-vms-prepare.md) a připravit své prostředí na ochranu virtuálních počítačů. 

> [!IMPORTANT]
> Tento kurz předpokládá, že už máte vytvořenou skupinu prostředků a virtuální počítač Azure.

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

[Trezor služby Recovery Services](backup-azure-recovery-services-vault-overview.md) je kontejner, který uchovává body obnovení zálohovaných položek. Trezor služby Recovery Services je prostředek Azure, který je možné nasadit a spravovat v rámci skupiny prostředků Azure. V tomto kurzu vytvoříte trezor služby Recovery Services ve stejné skupině prostředků jako chráněný virtuální počítač.


Při prvním použití služby Azure Backup musíte ve svém předplatném zaregistrovat poskytovatele služby Azure Recovery Services. Pokud jste už ve svém předplatném poskytovatele zaregistrovali, přejděte k dalšímu kroku.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
```

Vytvořte trezor služby Recovery Services pomocí rutiny **New-AzureRmRecoveryServicesVault**. Nezapomeňte zadat název skupiny prostředků a umístění použité při konfiguraci virtuálního počítače, který chcete zálohovat. 

```powershell
New-AzureRmRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
```

Řada rutin služby Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services. Z tohoto důvodu je vhodné uložit objekt trezoru služby Recovery Services do proměnné. Pak pomocí rutiny **Set-AzureRmRecoveryServicesBackupProperties** nastavte možnost **-BackupStorageRedundancy** na [Geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy-grs.md). 

```powershell
$vault1 = Get-AzureRmRecoveryServicesVault –Name myRSVault
Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```

## <a name="back-up-azure-virtual-machines"></a>Zálohování virtuálních počítačů Azure

Než budete moct spustit prvotní zálohování, musíte nastavit kontext trezoru. Kontext trezoru představuje typ chráněných dat v trezoru. Při vytváření trezoru služby Recovery Services se vytvoří i výchozí zásady ochrany a uchovávání informací. Výchozí zásady ochrany aktivují úlohu zálohování každý den v určenou dobu. Výchozí zásady uchovávání informací uchovávají denní bod obnovení po dobu 30 dnů. Pro účely tohoto kurzu přijměte výchozí zásady. 

Pomocí rutiny **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** nastavte kontext trezoru. Po nastavení se kontext trezoru použije pro všechny další rutiny. 

```powershell
Get-AzureRmRecoveryServicesVault -Name myRSVault | Set-AzureRmRecoveryServicesVaultContext
```

Pomocí rutiny **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** aktivujte úlohu zálohování. Úloha zálohování vytvoří bod obnovení. Pokud se jedná o prvotní zálohování, bodem obnovení je úplná záloha. Při dalších zálohováních se vytváří přírůstková kopie.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

## <a name="delete-the-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services

Pokud chcete odstranit trezor služby Recovery Services, musíte z něj nejprve odstranit všechny body obnovení a pak zrušit registraci trezoru. Následující příkazy obsahují podrobnosti o těchto krocích. 


```powershell
$Cont = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzureRmRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzureRmRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzureRmRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzureRmRecoveryServicesVault -Vault $vault1
```

## <a name="troubleshooting-errors"></a>Řešení chyb
Pokud při zálohování virtuálního počítače narazíte na problémy, přečtěte si nápovědu v článku [Řešení potíží se zálohováním virtuálních počítačů Azure](backup-azure-vms-troubleshoot.md).

## <a name="next-steps"></a>Další postup
Teď, když jste nastavili ochranu svých virtuálních počítačů, si přečtěte následující články, kde se seznámíte s úlohami správy a postupem při obnovování virtuálních počítačů z bodu obnovení.

* Informace o úpravách zásad zálohování najdete v tématu [Použití rutin AzureRM.RecoveryServices.Backup k zálohování virtuálních počítačů](backup-azure-vms-automation.md#create-a-protection-policy).
* [Správa a monitorování virtuálních počítačů](backup-azure-manage-vms.md)
* [Obnovení virtuálních počítačů](backup-azure-arm-restore-vms.md)
