---
title: "Upgrade na virtuální počítač Azure backup zásobníku V2 | Microsoft Docs"
description: "Upgrade proces a nejčastější dotazy pro zálohování virtuálních počítačů zásobníku V2"
services: backup, virtual-machines
documentationcenter: 
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/08/2018
ms.author: trinadhk
ms.openlocfilehash: b7e9f45c61d2af1940be50a368b87cd35c85b1dd
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="upgrade-to-vm-backup-stack-v2"></a>Upgrade na zálohování virtuálních počítačů zásobníku V2
Upgrade zálohování zásobníku V2 virtuální počítač (VM) obsahuje následující vylepšení funkce:
* Umožňuje zobrazit snímek pořízený jako součást úlohy zálohování, aby byly dostupné pro obnovení bez čekání na dokončení přenosu dat došlo k chybě.
Omezí čekání na snímku zkopírovány do trezoru než obnovení. Také odstraníte tím požadavek na další úložiště pro zálohování virtuálních počítačů premium s výjimkou první zálohy.  

* Snížení doby zálohování a obnovení zachováním snímky místně po dobu 7 dnů. 

* Podpora pro disk velikostí až 4 TB.  

* Možnost používat původní účty úložiště (i když virtuální počítač obsahuje disky, které jsou distribuovány mezi různými účty úložiště) při provádění obnovení nespravované virtuálního počítače. Bude obnovení rychlejší pro celou řadu konfigurací virtuálních počítačů. 
    > [!NOTE] 
    > Toto není stejný jako přepsání původní virtuální počítač. 
    > 
    >

## <a name="what-is-changing-in-the-new-stack"></a>Co je změna v novým zásobníkem?
V současné době úloha zálohování se skládá z dvě fáze:
1.  Pořízení snímku virtuálního počítače. 
2.  Přenos snímku virtuálního počítače do trezoru zálohování Azure. 

Bod obnovení je považován za vytvořené pouze po provádějí fáze 1 a 2. Jako součást novým zásobníkem je bod obnovení vytvořen při dokončení snímku. Můžete také obnovit z tohoto bodu obnovení pomocí stejného toku obnovení. Můžete určit tohoto bodu obnovení na portálu Azure pomocí "snímek" jako typ bodu obnovení. Po snímku se přenese do trezoru, typ bodu obnovení se změní na "Snímku a úložiště". 

![Úloha zálohování v zásobníku zálohování virtuálních počítačů V2](./media/backup-azure-vms/instant-rp-flow.jpg) 

Ve výchozím nastavení bude uchovávat snímky sedm dní. To umožňuje obnovení rychlejší provést z tyto snímky tím, že snižuje čas potřebný ke zkopírování dat z trezoru zpět k účtu úložiště zákazníka. 

## <a name="considerations-before-upgrade"></a>Důležité informace před provedením upgradu
* Toto je upgrade jednosměrnou zásobníku zálohování virtuálních počítačů. Ano všechny budoucí zálohy přejde do tohoto datového toku. Vzhledem k tomu **je povolena na úrovni předplatného, budou všechny virtuální počítače přejděte na tento tok**. Všechny nově přidané funkce budou založeny na se stejným zásobníkem. Uvolní možnost řízení, které to na úrovni zásady pochází v budoucnosti. 
* U virtuálních počítačů s prémiové disky během první zálohování Ujistěte se, že je prostor úložiště rovná velikosti virtuálního počítače je k dispozici v účtu úložiště až po dokončení první zálohy. 
* Vzhledem k tomu, že snímky jsou uložené místně pro zvýšení vytvoření bodu obnovení a také pro urychlení obnovení, zobrazí se náklady na úložiště odpovídající snímky během období sedmi dnů.
Pro spravované disky, je *žádné zvýšení ceny* protože restorePointCollections jsou zdarma. 
* Pokud provádíte obnovení z bodu obnovení snímku pro virtuální počítač, Premium, zobrazí se umístění dočasné úložiště používá jako součást obnovení je při vytváření virtuálního počítače. 

## <a name="how-to-upgrade"></a>Postup upgradu?
### <a name="the-azure-portal"></a>Portál Azure
Pokud používáte pomocí portálu Azure, bude zobrazit oznámení v řídícím panelu trezoru související s podporu velkých disků a zálohování a obnovení vylepšení rychlosti.

![Úloha zálohování v zásobníku zálohování virtuálních počítačů V2](./media/backup-azure-vms/instant-rp-banner.png) 

Otevření obrazovky pro upgrade na novou zásobníku, vyberte informační zprávě. 

![Úloha zálohování v zásobníku zálohování virtuálních počítačů V2](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
Spusťte následující rutiny z zvýšenými terminálu prostředí PowerShell:
1.  Přihlaste se k účtu Azure. 

```
PS C:> Login-AzureRmAccount
```

2.  Vyberte odběr, který chcete zaregistrovat pro verzi preview:

```
PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
```

3.  Zaregistrujte toto předplatné privátní Preview verzi:

```
PS C:>  Register-AzureRmProviderFeature -FeatureName “InstantBackupandRecovery” –ProviderNamespace Microsoft.RecoveryServices
```

## <a name="verify-whether-the-upgrade-is-complete"></a>Ověřte, zda bude dokončen upgrade
Z prostředí PowerShell terminálu se zvýšenými oprávněními spusťte následující rutinu:

```
Get-AzureRmProviderFeature -FeatureName “InstantBackupandRecovery” –ProviderNamespace Microsoft.RecoveryServices
```

Pokud se říká registrovaná, je vaše předplatné upgradován na zásobníku zálohování virtuálních počítačů V2. 



