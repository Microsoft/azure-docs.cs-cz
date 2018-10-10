---
title: Upgrade na V2 zásobník záloh virtuálních počítačů Azure
description: Upgrade procesu a nejčastější dotazy pro zásobník záloh virtuálních počítačů, modelu nasazení Resource Manager
services: backup
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 8/1/2018
ms.author: trinadhk
ms.openlocfilehash: 6f19a536861d236a82cc77a17570d8e3004a2ba1
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888267"
---
# <a name="upgrade-to-azure-vm-backup-stack-v2"></a>Upgrade na zásobník záloh virtuálních počítačů Azure V2

Model nasazení Resource Manageru pro upgrade na zásobník záloh virtuálních počítačů (VM) obsahuje následující vylepšení funkcí:

* Možnost zobrazit snímkům pořízeným jako součást, která je k dispozici pro obnovení bez čekání na přenos dat do dokončení úlohy zálohování. Snižuje dobu čekání pro snímky ke zkopírování do trezoru před aktivace operace obnovení. Kromě toho tato funkce eliminuje požadavek na další úložiště pro zálohování virtuálních počítačů úrovně premium, s výjimkou první zálohy.  

* Zkracuje dobu zálohování a obnovení tak, že zachová snímky místně, po dobu sedmi dní.

* Podpora pro disk o velikosti až 4 TB.

* Možnost používat nespravovaného virtuálního počítače na původní účty úložiště, při obnovování. Tato schopnost existuje i v případě, že virtuální počítač obsahuje disky, které jsou distribuovány mezi různými účty úložiště. Urychluje operace obnovení pro celou řadu konfigurací virtuálních počítačů.
    > [!NOTE]
    > Tato možnost není stejný jako přepsání původního virtuálního počítače.
    >

## <a name="whats-changing-in-the-new-stack"></a>Co se mění v nový zásobník?
V současné době úloha zálohování se skládá ze dvou fází:
1.  Pořízení snímku virtuálního počítače.
2.  Přenos snímku virtuálního počítače do Azure Backup vault.

Vytvoření bodu obnovení se považuje za pouze po dokončení fáze 1 a 2. Jako součást nového zásobníku je bod obnovení vytvořen ihned po snímku. Můžete také obnovit z tohoto bodu obnovení pomocí stejný tok obnovení. Tento bod obnovení na portálu Azure portal můžete identifikovat pomocí "snímku" jako typ bodu obnovení. Po snímku se přenesou do trezoru, typ bodu obnovení se změní na "snímek a trezor."

![Úloha zálohování ve virtuálním počítači zásobník záloh modelu nasazení Resource Manager – úložiště a trezoru](./media/backup-azure-vms/instant-rp-flow.jpg)

Ve výchozím nastavení snímky uchovávají po dobu sedmi dní. Tato funkce umožňuje obnovení na rychlejší dokončení z těchto snímků. Snižuje dobu potřebnou pro kopírování dat z trezoru do účtu úložiště zákazníka.

## <a name="considerations-before-upgrade"></a>Důležité informace před provedením upgradu

* Upgrade zásobník záloh virtuálních počítačů je jedním směrové, všechny zálohy přejít do tohoto toku. Protože změně na úrovni předplatného, všechny virtuální počítače přejít do tohoto toku. Všechny nové funkce Doplňky jsou založeny na se stejným zásobníkem. Momentálně nemůžete určit zásobníku na úrovni zásad.

* Snímky se ukládají místně, a zvýšit tak vytvoření bodu obnovení a také ke zrychlení operací obnovení. V důsledku toho se zobrazí náklady na úložiště, které odpovídají snímkům pořízeným období sedm dní.

* Přírůstkové snímky se ukládají jako objekty BLOB stránky. Všichni zákazníci, které používají nespravované disky se vám účtuje sedm dní, po které snímky se ukládají v účtu místní úložiště zákazníka. Protože kolekce bodů obnovení, který se používá záloh virtuálních počítačů spravovaných pomocí snímky objektů blob na základní úrovni úložiště, za spravované disky se zobrazí náklady odpovídající [blob snapshot ceny](https://docs.microsoft.com/rest/api/storageservices/understanding-how-snapshots-accrue-charges) a jsou přírůstkové.

* Pokud obnovíte na úrovni premium virtuálního počítače ze snímku do konkrétního bodu obnovení, umístění dočasného úložiště se používá při vytváření virtuálního počítače.

* Pro účty služby premium storage snímkům pořízeným pro okamžité obnovení počet bodů vůči limit 10 TB přidělené místo.

> [!NOTE]
> Upgrade na zásobník záloh virtuálních počítačů Azure V2 na získat podporu Azure Backup pro [Standard Managed SSD Disks](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/) a virtuální počítače s až 32 datových disků.

## <a name="upgrade"></a>Upgrade
### <a name="the-azure-portal"></a>Azure Portal
Pokud použijete Azure portal, uvidíte oznámení na řídicím panelu trezoru. Toto oznámení se týká podporu velkých disků a zvýšení rychlosti zálohování a obnovení. Případně můžete přejít na stránku vlastností zobrazíte možnost upgrade trezoru.

![Úloha zálohování v modelu nasazení Resource Manager zásobník záloh virtuálních počítačů – podpora oznámení](./media/backup-azure-vms/instant-rp-banner.png)

Otevření obrazovky pro upgrade na nový zásobník, klikněte na banner.

![Úloha zálohování v zásobníku zálohování virtuálních počítačů modelu nasazení Resource Manager – upgrade](./media/backup-azure-vms/instant-rp.png)

### <a name="powershell"></a>PowerShell
Z terminálu Powershellu se zvýšenými oprávněními spusťte následující rutiny:
1.  Přihlaste se ke svému účtu Azure:

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  Vyberte předplatné, pro kterou chcete zaregistrovat:

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  Zaregistrujte toto předplatné:

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="verify-that-the-upgrade-is-finished"></a>Ověřte, že dokončení upgradu
Prostředí PowerShell terminálu se zvýšenými oprávněními spusťte následující rutinu:

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
```

Když je ve stavu "Registrováno", vaše předplatné upgradovat na modelu nasazení Resource Manager zásobník záloh virtuálních počítačů.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Následující otázky a odpovědi mají byly shromážděny z fór a dotazy zákazníků.

### <a name="will-upgrading-to-v2-impact-current-backups"></a>Upgrade na V2 aktuální zálohování ovlivní?
Pokud upgradujete na V2, neexistuje žádný vliv na vaše aktuální zálohování a není potřeba změnit konfiguraci vašeho prostředí. Upgrade a prostředí zálohování dál pokračoval je na něm.

### <a name="what-does-it-cost-to-upgrade-to-azure-vm-backup-stack-v2"></a>Kolik stojí upgradovat na v2 zásobníku zálohování virtuálních počítačů Azure?
Se neúčtují žádné poplatky k upgradu zásobníku v2. Snímky se ukládají místně zrychlit vytvoření bodu obnovení a obnovení operací. V důsledku toho se zobrazí náklady na úložiště, které odpovídají snímkům pořízeným období sedm dní.

### <a name="does-upgrading-to-stack-v2-increase-the-premium-storage-account-snapshot-limit-by-10-tb"></a>Premium storage účet omezení počtu snímků pomocí 10 TB zvýší upgrade na zásobník v2?
Ne, limit celkový počet snímků za úložiště účet stále zůstává na 10TB.

### <a name="in-premium-storage-accounts-do-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>V účtech Premium Storage snímkům pořízeným okamžité obnovení bodu zabírat omezení počtu snímků 10 TB?
Ano, pro účty služby premium storage, snímkům pořízeným okamžité obnovení bodu zabírat přidělené 10 TB místa.

### <a name="how-does-the-snapshot-work-during-the-seven-day-period"></a>Jak funguje snímku během sedmidenní období?
Každý den se používá nový snímek. Existuje sedm jednotlivé snímky. Službou **není** pořiďte si první den a přidat změny dalších šest dní.

### <a name="is-a-v2-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Snímek v2 je přírůstkový snímek nebo úplné snímku?
Přírůstkových snímků se používají pro nespravované disky. Bod kolekce vytvořené pomocí Azure Backup používá snímků objektů blob a proto se přičítají obnovení v případě, že za spravované disky.

### <a name="how-to-get-standard-ssd-managed-disk-support-for-a-virtual-machine"></a>Jak získat SSD na úrovni standard managed podporu disků pro virtuální počítač?
Upgrade na zásobník záloh virtuálních počítačů Azure V2 potřebujete podporu Azure Backup pro [Standard Managed SSD Disks](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/). Po upgradu můžete také zálohovat virtuální počítače s až 32 datových disků.
