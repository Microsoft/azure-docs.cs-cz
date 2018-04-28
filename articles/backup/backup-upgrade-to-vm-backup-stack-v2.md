---
title: Upgrade na modelu nasazení Azure Resource Manager pro zálohování zásobník virtuálního počítače Azure | Microsoft Docs
description: Upgrade proces a nejčastější dotazy pro zásobník zálohování virtuálních počítačů, modelu nasazení Resource Manager
services: backup, virtual-machines
documentationcenter: ''
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/08/2018
ms.author: trinadhk, sogup
ms.openlocfilehash: 224cd365e6b3ca4fd963b530dbaa289b763d53ee
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="upgrade-to-the-azure-resource-manager-deployment-model-for-azure-vm-backup-stack"></a>Upgrade na modelu nasazení Azure Resource Manager pro zálohování zásobník virtuálního počítače Azure
Model nasazení Resource Manager pro upgrade na zálohování zásobník virtuálního počítače (VM) obsahuje následující vylepšení funkce:
* Umožňuje zobrazit snímky, které jsou použity jako součást úlohy zálohování, která je k dispozici pro obnovení bez čekání na přenos dat do dokončení. Snižuje dobu čekání snímky zkopírovat do trezoru než obnovení. Kromě toho tato funkce eliminuje požadavek na další úložiště pro zálohování virtuálních počítačů úrovně premium, s výjimkou první zálohy.  

* Snížení doby zálohování a obnovení zachováním snímky místně pro sedm dní.

* Podpora pro disk velikostí až 4 TB.

* Možnost používat účty úložiště původní nespravované počítač při obnovení. Tato možnost existuje i v případě, že virtuální počítač obsahuje disky, které jsou distribuovány mezi různými účty úložiště. Umožňuje obnovení rychlejší pro celou řadu konfigurací virtuálních počítačů.
    > [!NOTE] 
    > Tato možnost není stejný jako přepsání původní virtuální počítač. 
    >

## <a name="whats-changing-in-the-new-stack"></a>Co je změna v novým zásobníkem?
V současné době úloha zálohování se skládá ze dvou fází:
1.  Vytvoření snímku virtuálního počítače. 
2.  Přenos snímku virtuálního počítače do trezoru zálohování Azure. 

Bod obnovení je považován za vytvořené pouze po provádějí fáze 1 a 2. Jako součást novým zásobníkem je bod obnovení vytvořen při dokončení snímku. Můžete také obnovit z tohoto bodu obnovení pomocí stejného toku obnovení. Tento bod obnovení na portálu Azure můžete identifikovat pomocí "snímek" jako typ bodu obnovení. Po snímku se přenese do trezoru, typ bodu obnovení se změní na "snímku a trezoru." 

![Úloha zálohování v VM zálohování zásobníku modelu nasazení Resource Manager – úložiště a trezoru](./media/backup-azure-vms/instant-rp-flow.jpg) 

Ve výchozím nastavení jsou snímky v sedm dní. Tato funkce umožňuje obnovení na dokončení rychlejší z tyto snímky. Snižuje čas, které je nutné zkopírovat data zpět z trezoru do účtu úložiště zákazníka. 

## <a name="considerations-before-upgrade"></a>Důležité informace před provedením upgradu
* Upgrade zásobníku zálohování virtuálních počítačů je jedním směrovou. Proto všechny zálohy, přejděte do tohoto datového toku. Protože je povolena na úrovni předplatného, všechny virtuální počítače přejít do tohoto datového toku. Všechny nové funkce přidané jsou založené na stejné zásobníku. Uvolní možnost řízení, které to na úrovni zásady pochází v budoucnosti.

* U virtuálních počítačů s prémiové disky během a do prvního dokončení zálohování, ujistěte se, že v účtu úložiště není dostatek místa. Musí být roven velikost virtuálního počítače.

* Snímky jsou uložené místně pro zvýšení vytvoření bodu obnovení a také pro urychlení obnovení. Proto zobrazí náklady na úložiště, které odpovídají snímky během období sedmi dnů.

* Přírůstkové snímky jsou uloženy jako objekty BLOB stránky. Všem zákazníkům, kteří používají nespravovaná disky budou účtovat sedm dní, které snímky jsou uložené v účtu zákazníka místní úložiště. Podle aktuální model tvorby cen je pro zákazníky na spravované disky bez nákladů.

* Pokud provedete obnovení z bodu obnovení snímku Premium virtuálních počítačů, zobrazí se umístění dočasné úložiště, který se používá při vytvoření virtuálního počítače jako součást obnovení.

* Pro účty úložiště premium zabírají snímky, které se provádějí pro rychlé obnovení 10 TB přidělené místo.

## <a name="upgrade"></a>Upgrade
### <a name="the-azure-portal"></a>Portál Azure
Pokud používáte portál Azure, uvidíte oznámení na řídicím panelu trezoru. Toto oznámení má vztah k velké diskové podpora a vylepšení rychlosti zálohování a obnovení.

![Úloha zálohování v modelu nasazení Resource Manager zálohování zásobník virtuálního počítače – podpora oznámení](./media/backup-azure-vms/instant-rp-banner.png) 

Otevření obrazovky pro upgrade na novou zásobníku, vyberte informační zprávě. 

![Úloha zálohování v zásobníku zálohování virtuálních počítačů modelu nasazení Resource Manager – upgrade](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
Spusťte následující rutiny z zvýšenými terminálu prostředí PowerShell:
1.  Přihlaste se k účtu Azure: 

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  Vyberte odběr, který chcete zaregistrovat pro verzi preview:

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  Zaregistrujte toto předplatné privátní Preview verzi:

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="verify-that-the-upgrade-is-finished"></a>Ověřte, že dokončení upgradu
Z prostředí PowerShell terminálu se zvýšenými oprávněními spusťte následující rutinu:

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
```

Pokud se říká "Registrovaná", je vaše předplatné upgradován na modelu nasazení Resource Manager zálohování zásobníku virtuálních počítačů.
