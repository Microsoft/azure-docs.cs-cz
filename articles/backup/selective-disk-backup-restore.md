---
title: Selektivní zálohování a obnovení disku pro virtuální počítače Azure
description: V tomto článku se dozvíte o selektivním zálohování a obnovení disku pomocí řešení zálohování virtuálních počítačů Azure.
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: references_regions
ms.openlocfilehash: 1052e7e531f6762de660ba89e22c7fbb0d01f808
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92628758"
---
# <a name="selective-disk-backup-and-restore-for-azure-virtual-machines"></a>Selektivní zálohování a obnovení disku pro virtuální počítače Azure

Azure Backup podporuje zálohování všech disků (operačního systému a dat) na virtuálním počítači společně s použitím řešení zálohování virtuálních počítačů. Teď můžete pomocí funkce zálohování a obnovení selektivních disků zálohovat podmnožinu datových disků ve virtuálním počítači. To poskytuje efektivní a nákladově efektivní řešení pro potřeby zálohování a obnovení. Každý bod obnovení obsahuje pouze disky, které jsou součástí operace zálohování. Tím umožníte, aby se v průběhu operace obnovení obnovila podmnožina disků z daného bodu obnovení. To platí pro obnovení ze snímků i z trezoru.

## <a name="scenarios"></a>Scénáře

Toto řešení je užitečné zejména v následujících situacích:

1. Pokud máte kritická data, která se mají zálohovat jenom na jednom disku, nebo podmnožinu disků, a nechcete zálohovat zbývající disky připojené k virtuálnímu počítači, abyste minimalizovali náklady na úložiště zálohování.
2. Pokud máte jiná řešení zálohování pro součást virtuálního počítače nebo dat. Pokud například zálohujete databáze nebo data pomocí jiného řešení zálohování úloh a chcete použít zálohování na úrovni virtuálního počítače Azure pro zbývající data nebo disky k vytvoření efektivního a robustního systému s využitím nejlepších dostupných možností.

Pomocí PowerShellu nebo rozhraní příkazového řádku Azure můžete konfigurovat selektivní zálohování disku virtuálního počítače Azure.  Pomocí skriptu můžete zahrnout nebo vyloučit datové disky pomocí jejich čísel logické jednotky (LUN).  V současné době je možné nakonfigurovat zálohování selektivních disků pomocí Azure Portal jenom na možnost **zálohovat jenom disk s operačním systémem** . Takže můžete nakonfigurovat zálohování virtuálního počítače Azure pomocí disku s operačním systémem a vyloučit z něj všechny připojené datové disky.

>[!NOTE]
> Disk s operačním systémem se ve výchozím nastavení přidá do zálohy virtuálního počítače a nedá se vyloučit.

## <a name="using-azure-cli"></a>Použití Azure CLI

Ujistěte se, že používáte AZ CLI verze 2.0.80 nebo vyšší. Verzi rozhraní příkazového řádku můžete získat pomocí tohoto příkazu:

```azurecli
az --version
```

Přihlaste se k ID předplatného, kde existuje Recovery Services trezor a virtuální počítač:

```azurecli
az account set -s {subscriptionID}
```

>[!NOTE]
>V každém příkazu níže je potřeba jenom název **zdrojové** služby (ne objekt) odpovídající tomuto trezoru.

### <a name="configure-backup-with-azure-cli"></a>Konfigurace zálohování pomocí Azure CLI

Během operace konfigurace ochrany je třeba zadat nastavení seznamu disků s **inclusion**  /  parametrem **vyloučení** zahrnutí a zadat tak čísla logických jednotek disků, které mají být zahrnuty nebo vyloučeny v záloze.

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting exclude --diskslist 0 1
```

Pokud virtuální počítač není ve stejné skupině prostředků jako trezor **, pak skupina** prostředků odkazuje na skupinu prostředků, ve které se vytvořil trezor. Místo názvu virtuálního počítače zadejte ID virtuálního počítače, jak je uvedeno níže.

```azurecli
az backup protection enable-for-vm  --resource-group {ResourceGroup} --vault-name {vaultname} --vm $(az vm show -g VMResourceGroup -n MyVm --query id --output tsv) --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

### <a name="modify-protection-for-already-backed-up-vms-with-azure-cli"></a>Úprava ochrany pro už zálohované virtuální počítače pomocí Azure CLI

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting exclude --diskslist {LUN number(s) separated by space}
```

### <a name="backup-only-os-disk-during-configure-backup-with-azure-cli"></a>Zálohovat jenom disk s operačním systémem během konfigurace zálohování pomocí Azure CLI

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --exclude-all-data-disks
```

### <a name="backup-only-os-disk-during-modify-protection-with-azure-cli"></a>Zálohovat jenom disk s operačním systémem během změny ochrany pomocí Azure CLI

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --exclude-all-data-disks
```

### <a name="restore-disks-with-azure-cli"></a>Obnovení disků pomocí Azure CLI

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --diskslist {LUN number of the disk(s) to be restored}
```

### <a name="restore-only-os-disk-with-azure-cli"></a>Obnovení jenom disku s operačním systémem pomocí Azure CLI

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} } --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-only-osdisk
```

### <a name="get-protected-item-to-get-disk-exclusion-details-with-azure-cli"></a>Získat chráněnou položku pro získání podrobností o vyloučení disku pomocí Azure CLI

```azurecli
az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

K chráněné položce se přidá další parametr **diskExclusionProperties** , jak je znázorněno níže:

```azurecli
"extendedProperties": {
      "diskExclusionProperties": {
        "diskLunList": [
          0,
          1
        ],
        "isInclusionList": true
      }
```

### <a name="get-backup-job-with-azure-cli"></a>Získat úlohu zálohování pomocí Azure CLI

```azurecli
az backup job show --vault-name {vaultname} --resource-group {resourcegroup} -n {BackupJobID}
```

Tento příkaz vám pomůže získat podrobnosti o zálohovaných discích a vyloučených discích, jak je znázorněno níže:

```output
   "Backed-up disk(s)": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b; diskextest_DataDisk_0; diskextest_DataDisk_1",  "Backup Size": "0 MB",
   "Excluded disk(s)": "diskextest_DataDisk_2",
```

### <a name="list-recovery-points-with-azure-cli"></a>Vypsání bodů obnovení pomocí Azure CLI

```azurecli
az backup recoverypoint list --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM
```

Tím se zajistí informace o počtu připojených a zálohovaných disků ve virtuálním počítači.

```azurecli
      "recoveryPointDiskConfiguration": {
        "excludedDiskList": null,
        "includedDiskList": null,
        "numberOfDisksAttachedToVm": 4,
        "numberOfDisksIncludedInBackup": 3
};
```

### <a name="get-recovery-point-with-azure-cli"></a>Získat bod obnovení pomocí Azure CLI

```azurecli
az backup recoverypoint show --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -n {recoverypointID}
```

Každý bod obnovení má informace o zahrnutých a vyloučených discích:

```azurecli
  "recoveryPointDiskConfiguration": {
      "excludedDiskList": [
        {
          "lun": 2,
          "name": "diskextest_DataDisk_2"
        }
      ],
      "includedDiskList": [
        {
          "lun": -1,
          "name": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b"
        },
        {
          "lun": 0,
          "name": "diskextest_DataDisk_0"
        },
        {
          "lun": 1,
          "name": "diskextest_DataDisk_1"
        }
      ],
      "numberOfDisksAttachedToVm": 4,
      "numberOfDisksIncludedInBackup": 3
```

### <a name="remove-disk-exclusion-settings-and-get-protected-item-with-azure-cli"></a>Odebrat nastavení vyloučení disku a získat chráněnou položku pomocí Azure CLI

```azurecli
az backup protection update-for-vm --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting resetexclusionsettings

az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

Když tyto příkazy spustíte, uvidíte `"diskExclusionProperties": null` .

## <a name="using-powershell"></a>Použití PowerShellu

Ujistěte se, že používáte Azure PowerShell verze 3.7.0 nebo novější.

### <a name="enable-backup-with-powershell"></a>Povolení zálohování pomocí PowerShellu

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -InclusionDisksList[Strings] -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExclusionDisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-configure-backup-with-powershell"></a>Zálohovat jenom disk s operačním systémem během konfigurace zálohování pomocí PowerShellu

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="get-backup-item-object-to-be-passed-in-modify-protection-with-powershell"></a>Získání objektu zálohované položky, která se má předat v rámci úpravy ochrana pomocí PowerShellu

```azurepowershell
$item= Get-AzRecoveryServicesBackupItem -BackupManagementType "AzureVM" -WorkloadType "AzureVM" -VaultId $Vault.ID -FriendlyName "V2VM"
```

Výše uvedený získaný objekt **$Item** musíte předat parametru **– Item** v následujících rutinách.

### <a name="modify-protection-for-already-backed-up-vms-with-powershell"></a>Úprava ochrany pro už zálohované virtuální počítače pomocí PowerShellu

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -InclusionDisksList[Strings] -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ExclusionDisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-modify-protection-with-powershell"></a>Zálohovat jenom disk s operačním systémem během změny ochrany pomocí PowerShellu

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="reset-disk-exclusion-setting-with-powershell"></a>Resetovat nastavení vyloučení disku pomocí PowerShellu

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ResetExclusionSettings -VaultId $targetVault.ID
```

### <a name="restore-selective-disks-with-powershell"></a>Obnovení selektivních disků pomocí PowerShellu

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreDiskList [Strings]
```

### <a name="restore-only-os-disk-with-powershell"></a>Obnovení jenom disku s operačním systémem pomocí PowerShellu

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreOnlyOSDisk
```

## <a name="using-the-azure-portal"></a>Použití webu Azure Portal

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

Pomocí Azure Portal můžete zobrazit zahrnuté a vyloučené disky z podokna Podrobnosti zálohy virtuálního počítače a podokna podrobností úlohy zálohování.  Když při obnovení vyberete bod obnovení, ze kterého se má obnovit, můžete v tomto bodu obnovení zobrazit zálohované disky.

Tady si můžete zobrazit zahrnuté a vyloučené disky pro virtuální počítač na portálu v podokně Podrobnosti o záložním VIRTUÁLNÍm počítači:

![Zobrazit zahrnuté a vyloučené disky z podokna podrobností zálohování](./media/selective-disk-backup-restore/backup-details.png)

Tady si můžete zobrazit zahrnuté a vyloučené disky v podokně podrobností úlohy v části zálohování:

![Zobrazit zahrnuté a vyloučené disky z podokna podrobností úlohy](./media/selective-disk-backup-restore/job-details.png)

Tady si můžete zobrazit zálohované disky během obnovení, když vyberete bod obnovení, ze kterého se má obnovit:

![Zobrazit zálohované disky během obnovování](./media/selective-disk-backup-restore/during-restore.png)

Konfigurace možnosti zálohování selektivních disků pro virtuální počítač prostřednictvím Azure Portal je omezená jenom na možnost **zálohovat jenom disk s operačním systémem** . Pokud chcete použít zálohování selektivních disků na už zálohovaném virtuálním počítači nebo pro pokročilé zahrnutí nebo vyloučení konkrétních datových disků virtuálního počítače, použijte PowerShell nebo Azure CLI.

>[!NOTE]
>Pokud se data nacházejí napříč disky, ujistěte se, že jsou všechny závislé disky zahrnuté do zálohy. Pokud ve svazku Nezálohujte všechny závislé disky, nevytvoří se během obnovování svazku, který se skládá z některých nezálohovaných disků.

### <a name="backup-os-disk-only-in-the-azure-portal"></a>Zálohovat disk s operačním systémem jenom v Azure Portal

Pokud povolíte zálohování pomocí Azure Portal, můžete zvolit jenom možnost **zálohovat jenom disk s operačním systémem** . Takže můžete nakonfigurovat zálohování virtuálního počítače Azure pomocí disku s operačním systémem a vyloučit všechny datové disky, které jsou k němu připojené.

![Konfigurace zálohování jenom pro disk s operačním systémem](./media/selective-disk-backup-restore/configure-backup-operating-system-disk.png)

## <a name="using-azure-rest-api"></a>Používání Azure REST API

Zálohování virtuálního počítače Azure můžete nakonfigurovat na několik vybraných disků, nebo můžete upravit stávající ochranu virtuálního počítače tak, aby zahrnovala nebo vyloučila několik disků, jak je popsáno [zde](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup).

## <a name="selective-disk-restore"></a>Obnovení selektivního disku

Možnost obnovení selektivního disku je přidaná funkce, která se zobrazí po povolení funkce zálohování selektivních disků. Pomocí této funkce můžete obnovit selektivní disky ze všech disků zálohovaných v bodu obnovení. Je efektivnější a pomáhá ušetřit čas ve scénářích, kdy víte, které disky je potřeba obnovit.

- Disk s operačním systémem je ve výchozím nastavení součástí zálohování a obnovení virtuálních počítačů a nelze ho vyloučit.
- Funkce obnovení selektivního disku je podporovaná jenom pro body obnovení, které se vytvořily po povolení možnosti vyloučení disku.
- Zálohy **s nastavením vyloučení** disku podporují jenom možnost **obnovení disku** . Existující možnosti obnovení **virtuálního počítače** nebo **nahrazení** nejsou v tomto případě podporované.

![Možnost obnovit virtuální počítač a nahradit existující nejsou během operace obnovení k dispozici.](./media/selective-disk-backup-restore/options-not-available.png)

## <a name="limitations"></a>Omezení

Funkce zálohování selektivních disků se u klasických virtuálních počítačů a šifrovaných virtuálních počítačů nepodporuje. Takže virtuální počítače Azure, které jsou zašifrované pomocí Azure Disk Encryption (ADE) pomocí nástroje BitLocker pro šifrování virtuálního počítače s Windows, a funkce dm-crypt pro virtuální počítače se systémem Linux nejsou podporované.

Možnosti obnovení pro **Vytvoření nového virtuálního počítače** a **nahradit existující** nejsou podporované pro virtuální počítač, u kterého je povolená funkce zálohování na selektivních discích.

V současné době zálohování virtuálních počítačů Azure nepodporuje virtuální počítače s extrémně disky nebo sdílenými disky, které jsou k nim připojené. V takových případech nelze použít zálohování na selektivním disku, která tento disk vyloučí a zálohuje virtuální počítač.

## <a name="billing"></a>Fakturace

Zálohování virtuálního počítače Azure se řídí stávajícím cenovým modelem, který je podrobně [vysvětlen.](https://azure.microsoft.com/pricing/details/backup/)

**Náklady na chráněnou instanci (pi)** se vypočítávají pro disk s operačním systémem jenom v případě, že se rozhodnete zálohovat jenom pomocí možnosti **disku s operačním systémem** .  Pokud nakonfigurujete zálohování a vyberete alespoň jeden datový disk, budou náklady na PI vypočítány pro všechny disky připojené k virtuálnímu počítači. **Náklady na úložiště zálohování** se vypočítávají jenom na zahrnutých discích, takže se budete moct uložit na náklady na úložiště. **Náklady na snímek** se vždycky vypočítávají pro všechny disky ve virtuálním počítači (zahrnuté i vyloučené disky).

Pokud jste zvolili funkci obnovení mezi oblastmi (CRR), pak se [ceny crr](https://azure.microsoft.com/pricing/details/backup/) vztahují na náklady na úložiště zálohování po vyloučení disku.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-is-protected-instance-pi-cost-calculated-for-only-os-disk-backup-in-windows-and-linux"></a>Jak se náklady na chráněnou instanci (PI) vypočítávají jenom pro zálohování na disk s operačním systémem Windows a Linux?

Náklady na PI se počítají na základě skutečné (použité) velikosti virtuálního počítače.

- Pro Windows: použitý výpočet místa je založený na jednotce, na které je uložený operační systém (obvykle C:).
- Pro Linux: použitý výpočet místa je založený na zařízení, na kterém je připojený kořenový systém souborů (/).

### <a name="i-have-configured-only-os-disk-backup-why-is-the-snapshot-happening-for-all-the-disks"></a>Mám nakonfigurovanou jenom zálohu disku s operačním systémem, proč se u všech disků děje snímek?

Funkce zálohování na základě selektivního disku vám umožní ušetřit náklady na úložiště záloh tím, že posílí zahrnuté disky, které jsou součástí zálohy. Snímek se ale povede pro všechny disky, které jsou připojené k virtuálnímu počítači. Takže náklady na snímek se vždycky vypočítávají pro všechny disky ve virtuálním počítači (zahrnuté i vyloučené disky). Další informace najdete v tématu [fakturace](#billing).

### <a name="i-cant-configure-backup-for-the-azure-virtual-machine-by-excluding-ultra-disk-or-shared-disks-attached-to-the-vm"></a>Nejde nakonfigurovat zálohování pro virtuální počítač Azure tím, že se mají vyloučit disky nebo sdílené disky připojené k VIRTUÁLNÍmu počítači.

Funkce zálohování na základě selektivního disku je funkce poskytovaná nad řešením zálohování virtuálních počítačů Azure. V současné době zálohování virtuálních počítačů Azure nepodporuje virtuální počítače, které jsou k nim připojené pomocí Ultra disk nebo sdíleného disku.

## <a name="next-steps"></a>Další kroky

- [Matice podpory pro zálohování virtuálních počítačů Azure](backup-support-matrix-iaas.md)
- [Nejčastější dotazy – zálohování virtuálních počítačů Azure](backup-azure-vm-backup-faq.md)
