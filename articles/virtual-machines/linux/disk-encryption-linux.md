---
title: Scénáře Azure Disk Encryption na virtuálních počítačích se systémem Linux
description: Tento článek poskytuje pokyny k povolení Microsoft Azureho šifrování disku pro virtuální počítače se systémem Linux v různých scénářích.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 19dcfb96f29939fd92f49ba288ddb6d9264e0f9a
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970599"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Scénáře Azure Disk Encryption na virtuálních počítačích se systémem Linux

Azure Disk Encryption využívá funkci DM-crypt systému Linux k poskytování šifrování svazku pro operační systém a datové disky virtuálních počítačů Azure a je integrována s Azure Key Vault, která vám pomůžou řídit a spravovat klíče šifrování disku a tajné kódy. Přehled služby najdete v tématu [Azure Disk Encryption pro virtuální počítače se systémem Linux](disk-encryption-overview.md).

Existuje mnoho scénářů šifrování disku a postup se může lišit v závislosti na scénáři. Následující oddíly popisují scénáře podrobněji podrobněji pro virtuální počítače se systémem Linux.

Diskové šifrování můžete použít jenom pro virtuální počítače s [podporovanými velikostmi virtuálních počítačů a operačními systémy](disk-encryption-overview.md#supported-vms-and-operating-systems). Musíte splňovat i následující požadavky:

- [Další požadavky na virtuální počítače](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Požadavky na síť](disk-encryption-overview.md#networking-requirements)
- [Požadavky na úložiště šifrovacího klíče](disk-encryption-overview.md#encryption-key-storage-requirements)

Ve všech případech byste si měli [udělat snímek](snapshot-copy-managed-disk.md) a/nebo vytvořit zálohu před tím, než se disky zašifrují. Zálohy Ujistěte se, že možnost obnovení je možné, pokud dojde k neočekávané chybě při šifrování. Virtuální počítače se spravovanými disky vyžadují zálohu, než dojde k šifrování. Po provedení zálohy můžete použít [rutinu Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) k šifrování spravovaných disků zadáním parametru-skipVmBackup. Další informace o zálohování a obnovení šifrovaných virtuálních počítačů najdete v článku o [Azure Backup](../../backup/backup-azure-vms-encryption.md) . 

>[!WARNING]
> - Pokud jste předtím používali Azure Disk Encryption se službou Azure AD k šifrování virtuálního počítače, musíte tuto možnost použít k zašifrování virtuálního počítače. Podrobnosti najdete v tématu [Azure Disk Encryption s Azure AD (předchozí verze)](disk-encryption-overview-aad.md) . 
>
> - Při šifrování svazků se systémem Linux by se měl virtuální počítač považovat za nedostupný. Důrazně doporučujeme vyhnout se přihlášení SSH, zatímco probíhá šifrování, aby nedocházelo k blokování jakýchkoli otevřených souborů, ke kterým bude potřeba během procesu šifrování dostat. Pokud chcete sledovat průběh, použijte příkaz PowerShellu [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) nebo příkaz pro [šifrování virtuálního počítače](/cli/azure/vm/encryption#az-vm-encryption-show) , který se zobrazí. Pro tento proces může trvat několik hodin, než se 30 GB svazek s operačním systémem a další čas šifrování datových svazků. Doba šifrování datového svazku bude úměrná velikosti a množství datových svazků, pokud se nepoužije možnost šifrovat formát ALL. 
> - Zakázáním šifrování na virtuální počítače s Linuxem je podporována pouze pro datové svazky. Není podporován na data nebo svazků operačního systému, pokud byla dříve zašifrována svazek s operačním systémem.  

## <a name="install-tools-and-connect-to-azure"></a>Nainstalovat nástroje a připojit se k Azure

Azure Disk Encryption můžete povolit a spravovat prostřednictvím rozhraní příkazového [řádku Azure](/cli/azure) a [Azure PowerShell](/powershell/azure/new-azureps-module-az). K tomu je potřeba nainstalovat nástroje místně a připojit se k předplatnému Azure.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2,0](/cli/azure) je nástroj příkazového řádku pro správu prostředků Azure. Rozhraní příkazového řádku je určené pro flexibilní dotazování na data, podporují dlouho běžící operace jako neblokující procesy a usnadňovalo skriptování. Můžete ji nainstalovat místně pomocí postupu v části [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

 

Pokud se chcete [přihlásit ke svému účtu Azure pomocí Azure CLI](/cli/azure/authenticate-azure-cli), použijte příkaz [AZ Login](/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```azurecli
az login
```

Pokud chcete vybrat tenanta se přihlásit pod, použijte:
    
```azurecli
az login --tenant <tenant>
```

Pokud máte více předplatných a chcete zadat konkrétní konkrétní, získejte seznam předplatných pomocí [AZ Account list](/cli/azure/account#az-account-list) a určete pomocí [AZ Account set](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Další informace najdete v tématu Začínáme [s Azure CLI 2,0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure Powershell
[Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az) poskytuje sadu rutin, které používají model [Azure Resource Manager](../../azure-resource-manager/management/overview.md) ke správě prostředků Azure. Můžete ji použít v prohlížeči pomocí [Azure Cloud Shell](../../cloud-shell/overview.md)nebo ji můžete nainstalovat na místní počítač podle pokynů v tématu [instalace Azure PowerShell modulu](/powershell/azure/install-az-ps). 

Pokud už ho máte nainstalovaný místně, ujistěte se, že používáte nejnovější verzi sady SDK Azure Powershellu ke konfiguraci Azure Disk Encryption. Stáhněte si nejnovější verzi [Azure PowerShell verze](https://github.com/Azure/azure-powershell/releases).

Pokud se chcete [přihlásit ke svému účtu Azure pomocí Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0), použijte rutinu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```powershell
Connect-AzAccount
```

Pokud máte více předplatných a chcete ho zadat, použijte k jejich zobrazení rutinu [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) a potom rutinu [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) :

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Spuštěním rutiny [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) ověříte, že jste vybrali správné předplatné.

Pokud chcete potvrdit, že se nainstalují rutiny Azure Disk Encryption, použijte rutinu [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) :
     
```powershell
Get-command *diskencryption*
```
Další informace najdete v tématu [Začínáme s Azure PowerShell](/powershell/azure/get-started-azureps). 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Povolení šifrování na stávajícím nebo běžícím virtuálním počítači se systémem Linux
V tomto scénáři můžete povolit šifrování pomocí šablony Resource Manageru, rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku. Pokud potřebujete informace o schématu pro rozšíření virtuálního počítače, přečtěte si článek [Azure Disk Encryption pro rozšíření pro Linux](../extensions/azure-disk-enc-linux.md) .

>[!IMPORTANT]
 >Je nezbytně nutné k snímku a/nebo zálohování spravovaného disku na základě instance virtuálního počítače mimo a před povolením Azure Disk Encryption. Snímek spravovaného disku se dá vzít z portálu nebo prostřednictvím [Azure Backup](../../backup/backup-azure-vms-encryption.md). Zálohy Ujistěte se, že možnost obnovení je možné v případě jakékoli došlo k neočekávané chybě při šifrování. Po vytvoření zálohy se dá rutina Set-AzVMDiskEncryptionExtension použít k šifrování spravovaných disků zadáním parametru-skipVmBackup. Příkaz set-AzVMDiskEncryptionExtension se u virtuálních počítačů založených na discích nezdaří, dokud se neprovede záloha a tento parametr se zadal. 
>
>Šifrování nebo zakázáním šifrování může způsobit restartování virtuálního počítače. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Povolení šifrování na stávajícím nebo běžícím virtuálním počítači se systémem Linux pomocí Azure CLI 

Pomocí nástroje příkazového řádku [Azure CLI](/cli/azure/?view=azure-cli-latest) můžete povolit šifrování disku na zašifrovaném virtuálním pevném disku. Můžete ho používat v prohlížeči pomocí služby [Azure Cloud Shell](../../cloud-shell/overview.md) nebo nainstalovat na místním počítači a používat ho v jakékoli relaci PowerShellu. Pokud chcete povolit šifrování pro existující nebo běžící virtuální počítače se systémem Linux v Azure, použijte následující příkazy rozhraní příkazového řádku:

Pomocí příkazu [AZ VM Encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) Povolte šifrování na běžícím virtuálním počítači v Azure.

- **Zašifrujte spuštěný virtuální počítač:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Šifrování spuštěného virtuálního počítače pomocí KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Syntaxe pro hodnotu parametru disk šifrování – trezor klíčů je úplný identifikátor řetězce: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
Syntaxe pro hodnoty parametru klíč šifrovacího klíče je úplný identifikátor URI klíče KEK jako v: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ověřte, že jsou disky šifrované:** Pokud chcete zjistit stav šifrování virtuálního počítače, použijte příkaz [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Zakázat šifrování:** Pokud chcete šifrování zakázat, použijte příkaz [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Zakázáním šifrování je povolena pouze u svazků s daty pro virtuální počítače s Linuxem.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Povolení šifrování na stávajícím nebo běžícím virtuálním počítači se systémem Linux pomocí prostředí PowerShell
Pomocí rutiny [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) Povolte šifrování na běžícím virtuálním počítači v Azure. Pořídit [snímek](snapshot-copy-managed-disk.md) nebo zálohovat virtuální počítač s [Azure Backup](../../backup/backup-azure-vms-encryption.md) před zašifrováním disků. Parametr-skipVmBackup je už ve skriptech PowerShellu určený k zašifrování běžícího virtuálního počítače se systémem Linux.

-  **Zašifrujte spuštěný virtuální počítač:** Skript níže inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. Skupina prostředků, virtuální počítač a trezor klíčů byly vytvořeny jako požadavky. Nahraďte MyVirtualMachineResourceGroup, MySecureVM a MySecureVault hodnotami. Pokud chcete určit, které disky se šifrují, upravte parametr-VolumeType.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Šifrování spuštěného virtuálního počítače pomocí KEK:** Pokud šifrujete datové disky, a ne disk s operačním systémem, možná budete muset přidat parametr-VolumeType. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > Syntaxe pro hodnotu parametru disk šifrování – trezor klíčů je úplný identifikátor řetězce: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxe pro hodnoty parametru klíč šifrovacího klíče je úplný identifikátor URI klíče KEK jako v: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Ověřte, že jsou disky šifrované:** Pokud chcete zjistit stav šifrování virtuálního počítače, použijte rutinu [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Zakázat šifrování disku:** Pokud chcete šifrování zakázat, použijte rutinu [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Zakázáním šifrování je povolena pouze u svazků s daty pro virtuální počítače s Linuxem.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Povolení šifrování na stávajícím nebo běžícím virtuálním počítači se systémem Linux se šablonou

Pomocí [šablony Správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)můžete povolit šifrování disku na stávajícím nebo BĚŽÍCÍm virtuálním počítači se systémem Linux v Azure.

1. V šabloně pro rychlý Start Azure klikněte na **nasadit do Azure** .

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, parametry, právní podmínky a smlouvy. Kliknutím na **vytvořit** Povolte šifrování na stávajícím nebo SPUŠTĚNém virtuálním počítači.

V následující tabulce jsou uvedeny parametry šablony Resource Manageru pro existující nebo spouštění virtuálních počítačů:

| Parametr | Popis |
| --- | --- |
| vmName | Název virtuálního počítače ke spuštění operace šifrování. |
| keyVaultName | Název trezoru klíčů, do kterého se má šifrovací klíč nahrát Můžete ji získat pomocí rutiny `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` nebo příkazu rozhraní příkazového řádku Azure CLI `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`.|
| keyVaultResourceGroup | Název skupiny prostředků, která obsahuje Trezor klíčů. |
|  KeyEncryptionKeyURL | Adresa URL klíčového šifrovacího klíče, který se používá k šifrování šifrovacího klíče. Tento parametr je nepovinný, pokud v rozevíracím seznamu UseExistingKek vyberete **nokek** . Pokud v rozevíracím seznamu UseExistingKek vyberete možnost **KEK** , musíte zadat hodnotu _keyEncryptionKeyURL_ . |
| VolumeType | Typ svazku, který provádí operace šifrování na. Platné hodnoty jsou _operační systém_, _data_a _vše_. 
| forceUpdateTag | Pokaždé, když se operace musí být vynutit spuštění se předá jedinečnou hodnotu jako identifikátor GUID. |
| resizeOSDisk | Oddíl operačního systému velikost tak, aby obsadily úplný operační systém virtuálního pevného disku před rozdělením systémový svazek. |
| location | Umístění pro všechny prostředky. |


## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Použití funkce EncryptFormatAll pro datové disky na virtuálních počítačích se systémem Linux

Parametr **EncryptFormatAll** zkracuje dobu, po kterou jsou datové disky platformy Linux šifrovány. Oddíly, které splňují určitá kritéria, se naformátují (s aktuálním systémem souborů) a pak se znovu připojí zpátky do umístění, kde to bylo před provedením příkazu. Pokud budete chtít vyloučit datový disk, který splňuje kritéria, můžete jej odpojte před spuštěním příkazu.

 Po spuštění tohoto příkazu se naformátují všechny jednotky, které byly připojené dříve, a vrstva šifrování se spustí na začátku prázdné jednotky. Pokud je vybraná tato možnost, budou se šifrovat taky dočasných prostředků disku připojeného k virtuálnímu počítači. Pokud na dočasné jednotce se vynuluje, bude přeformátovali a znovu zašifrována pomocí řešení Azure Disk Encryption při nejbližší příležitosti pro virtuální počítač. Po zašifrování disku prostředků nebude moci [Agent Microsoft Azure Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) spravovat disk prostředků a povolit soubor odkládacího souboru, ale můžete odkládací soubor ručně nakonfigurovat.

>[!WARNING]
> Encryptformatall šifrovaně se nemá používat, když je potřebná data na svazcích data Virtuálního počítače. Podle jejich odpojení může vyloučit disky z šifrování. By měly nejprve vyzkoušet encryptformatall šifrovaně nejprve na testovacím virtuálním počítači, parametr funkce a její nepřímo před pokusem o produkční virtuální počítač. Možnost encryptformatall šifrovaně formátuje datový disk a všechna data na ni se ztratí. Než budete pokračovat, ověřte, že jsou správně odpojené disky, které chcete vyloučit. </br></br>
 >Pokud nastavení tohoto parametru během aktualizace nastavení šifrování může vést k restartování před skutečné šifrování. V takovém případě také můžete k odebrání disku, které nechcete formátovaný ze souboru fstab. Podobně měli byste přidat oddíl, který chcete zašifrovat ve formátu souboru fstab před inicializací operace šifrování. 

### <a name="encryptformatall-criteria"></a>EncryptFormatAll kritéria
Parametr přechází do všech oddílů a zašifruje je tak dlouho, dokud splňují **všechna** následující kritéria: 
- Není kořenový/operačního systému a spouštěcí oddíl
- Už není šifrovaný
- Není klíče BEK svazek
- Není svazek RAID
- Není svazku LVM
- Je připojený

Šifrování disků, které tvoří svazek RAID nebo LVM spíše než svazek RAID nebo LVM.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Použití parametru EncryptFormatAll s rozhraním příkazového řádku Azure
Pomocí příkazu [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) Povolte šifrování na běžícím virtuálním počítači v Azure.

-  **Šifrování spuštěného virtuálního počítače pomocí EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Použití parametru EncryptFormatAll s rutinou prostředí PowerShell
Použijte rutinu [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) s parametrem EncryptFormatAll. 

**Šifrování spuštěného virtuálního počítače pomocí EncryptFormatAll:** Například skript níže inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension s parametrem EncryptFormatAll. Skupina prostředků, virtuální počítač a trezor klíčů byly vytvořeny jako požadavky. Nahraďte MyVirtualMachineResourceGroup, MySecureVM a MySecureVault hodnotami.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Použití parametru EncryptFormatAll u Správce logických svazků (LVM) 
Doporučujeme, abyste nastavení LVM-na crypt. Všechny tyto příklady nahraďte cesta zařízení a přípojné body cokoli, co vyhovuje vašemu případu použití. Toto nastavení lze provést následujícím způsobem:

- Přidáte datové disky, které budou tvoří virtuální počítač.
- Formátování, připojování a přidat tyto disky do souboru fstab.

    1. Naformátujte nově přidaný disk. Využíváme Azure vygeneruje zde symbolických odkazů. Problémy související se změny názvů zařízení pomocí symbolických odkazů se vyhnete. Další informace najdete v článku řešení potíží s chybami [názvů zařízení](troubleshoot-device-names-problems.md) .
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    1. Připojte disky.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    1. Přidejte do fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    1. Spuštěním rutiny prostředí PowerShell set-AzVMDiskEncryptionExtension s-EncryptFormatAll Zašifrujte tyto disky.

       ```azurepowershell-interactive
       $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
           
       Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
       ```

    1. Nastavte LVM nad tyto nové disky. Všimněte si, že po dokončení spuštění virtuálního počítače jsou odemknutí zašifrované jednotky. Připojení LVM tedy také muset následně zpozdit.


## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nové virtuální počítače vytvořené z VHD a šifrovacích klíčů šifrovaných zákazníkem
V tomto scénáři můžete povolit šifrování pomocí rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku. 

Pro přípravu předem šifrovaných imagí, které je možné použít v Azure, použijte stejné pokyny jako v tématu Azure Disk Encryption – stejné skripty. Po vytvoření image můžete použít kroky v další části vytvořit šifrovaný virtuální počítač Azure.

* [Příprava předem zašifrovaného virtuálního pevného disku se systémem Linux](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Je nezbytně nutné k snímku a/nebo zálohování spravovaného disku na základě instance virtuálního počítače mimo a před povolením Azure Disk Encryption. Snímek spravovaného disku se dá vzít z portálu, nebo se dá použít [Azure Backup](../../backup/backup-azure-vms-encryption.md) . Zálohy Ujistěte se, že možnost obnovení je možné v případě jakékoli došlo k neočekávané chybě při šifrování. Po vytvoření zálohy se dá rutina Set-AzVMDiskEncryptionExtension použít k šifrování spravovaných disků zadáním parametru-skipVmBackup. Příkaz set-AzVMDiskEncryptionExtension se u virtuálních počítačů založených na discích nezdaří, dokud se neprovede záloha a tento parametr se zadal. 
>
> Šifrování nebo zakázáním šifrování může způsobit restartování virtuálního počítače. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Použití Azure PowerShell k šifrování virtuálních počítačů s předem šifrovanými virtuálními počítači 
Pomocí rutiny PowerShellu [set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples)můžete povolit šifrování disku na šifrovaném virtuálním pevném disku. Následující příklad obsahuje některé společné parametry. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Povoluje šifrování na nově přidaných datového disku

Nový datový disk můžete přidat pomocí [AZ VM disk Attach](add-disk.md)nebo [prostřednictvím Azure Portal](attach-disk-portal.md). Předtím, než je možné šifrovat, musíte nejdřív připojit nově připojený datový disk. Musíte požádat o šifrování datovou jednotku, protože jednotka nepoužitelný, zatímco probíhá šifrování. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Povoluje šifrování na nově přidaný disk pomocí Azure CLI

 Pokud byl virtuální počítač dříve zašifrován pomocí možnosti "vše", parametr--Volume-Type by měl zůstat "All". Zahrnuje všechny disky s operačním systémem a daty. Pokud byl virtuální počítač předtím zašifrovaný pomocí typu svazku "OS", měl by být parametr--Volume-Type změněn na hodnotu "All", takže bude zahrnut jak operační systém, tak i nový datový disk. Pokud virtuální počítač je zašifrovaný pomocí pouze typ svazku "Data", pak ho může zůstat "Data" jak je znázorněno níže. Přidání a připojení nového datového disku k virtuálnímu počítači není dostatek Příprava pro šifrování. Musí být také nově připojený disk ve formátu a správně připojeny virtuální počítač před povolením šifrování. V systému Linux musí být disk připojen v/etc/fstab s [názvem trvalého blokování zařízení](troubleshoot-device-names-problems.md).  

Na rozdíl od syntaxe Powershellu rozhraní příkazového řádku, aby uživatele k zadání verze jedinečný pořadí při povolení šifrování. Rozhraní příkazového řádku automaticky generuje a používá svůj vlastní jedinečný pořadí hodnotu verze.

-  **Šifrování datových svazků spuštěného virtuálního počítače:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Šifrování datových svazků spuštěného virtuálního počítače pomocí KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Povoluje šifrování na nově přidaný disk pomocí Azure Powershellu
 Při použití Powershellu k šifrování nový disk pro Linux, je potřeba zadat novou verzi pořadí. Pořadí verze musí být jedinečný. Níže uvedený skript vytvoří identifikátor GUID verze pořadí. Pořídit [snímek](snapshot-copy-managed-disk.md) nebo zálohovat virtuální počítač s [Azure Backup](../../backup/backup-azure-vms-encryption.md) před zašifrováním disků. Parametr-skipVmBackup je už ve skriptech PowerShellu určený k zašifrování nově přidaného datového disku.
 

-  **Šifrování datových svazků spuštěného virtuálního počítače:** Skript níže inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. Skupinu prostředků, virtuální počítač a trezor klíčů by již byly vytvořeny jako požadavky. Nahraďte MyVirtualMachineResourceGroup, MySecureVM a MySecureVault hodnotami. Přijatelné hodnoty pro parametr - VolumeType jsou všechny, operačním systémem a daty. Pokud byl virtuální počítač předtím zašifrovaný pomocí typu svazku "OS" nebo "All", měl by být parametr-VolumeType změněn na "All", takže bude zahrnut jak operační systém, tak i nový datový disk.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Šifrování datových svazků spuštěného virtuálního počítače pomocí KEK:** Přijatelné hodnoty pro parametr-VolumeType jsou všechny, operační systém a data. Pokud virtuální počítač byla dříve zašifrována s typem svazek "Operační systém" nebo "All", pak parametr - VolumeType by měl být změněn na všechny tak, aby operační systém i nový datový disk budou zahrnuty.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > Syntaxe hodnoty parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:/subscriptions/[ID předplatného-GUID]/resourceGroups/[KVresource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name]</br> Syntaxe pro hodnoty parametru klíč šifrovacího klíče je úplný identifikátor URI klíče KEK jako v: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Zakázat šifrování pro virtuální počítače s Linuxem
Můžete zakázat šifrování pomocí Azure Powershellu, rozhraní příkazového řádku Azure nebo pomocí šablony Resource Manageru. 

>[!IMPORTANT]
>Zakázáním šifrování v Azure Disk Encryption na virtuální počítače s Linuxem je podporována pouze pro datové svazky. Není podporován na data nebo svazků operačního systému, pokud byla dříve zašifrována svazek s operačním systémem.  

- **Zakázat šifrování disku pomocí Azure PowerShell:** Pokud chcete šifrování zakázat, použijte rutinu [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Zakázat šifrování pomocí Azure CLI:** Pokud chcete šifrování zakázat, použijte příkaz [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Zakázat šifrování pomocí šablony Správce prostředků:** Zakáže šifrování pomocí [zákazu šifrování na spuštěné šabloně virtuálního počítače se systémem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) .
     1. Klikněte na **Deploy to Azure** (Nasadit do Azure).
     2. Vyberte předplatné, skupinu prostředků, umístění, virtuálních počítačů, právní podmínky a smlouvy.

## <a name="unsupported-scenarios"></a>Nepodporované scénáře

Azure Disk Encryption nefunguje pro následující scénáře, funkce a technologie pro Linux:

- Šifrování virtuálních počítačů nebo virtuálních počítačů na úrovni Basic vytvořených prostřednictvím metody vytváření virtuálních počítačů
- Zakázání šifrování na jednotce operačního systému nebo datové jednotce virtuálního počítače se systémem Linux, pokud je jednotka operačního systému zašifrovaná.
- Šifrování jednotky operačního systému pro systém Linux Virtual Machine Scale Sets.
- Šifrování vlastních imagí na virtuálních počítačích se systémem Linux.
- Integrace s místním systémem správy klíčů.
- Soubory Azure (sdílený systém souborů).
- Network File System (NFS).
- Dynamické svazky.
- Dočasné disky s operačním systémem.
- Šifrování sdílených/distribuovaných systémů souborů, jako je (ale ne omezené): DFS, GFS, DRDB a CephFS.
- Výpis stavu systému jádra (kdump).

## <a name="next-steps"></a>Další kroky

- [Přehled Azure Disk Encryption](disk-encryption-overview.md)
- [Ukázkové skripty pro službu Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Řešení potíží se službou Azure Disk Encryption](disk-encryption-troubleshooting.md)
