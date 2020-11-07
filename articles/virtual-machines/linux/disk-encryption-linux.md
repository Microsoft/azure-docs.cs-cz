---
title: Scénáře použití služby Azure Disk Encryption na virtuálních počítačích se systémem Linux
description: Tento článek poskytuje pokyny k povolení Microsoft Azureho šifrování disku pro virtuální počítače se systémem Linux v různých scénářích.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: e8f71b277a4f4b538d501b1fa825bc6ec43428c8
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359387"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Scénáře použití služby Azure Disk Encryption na virtuálních počítačích se systémem Linux


Azure Disk Encryption pro virtuální počítače se systémem Linux používá funkce DM-Crypt systému Linux k zajištění úplného šifrování disku s diskem operačního systému a datových disků. Kromě toho poskytuje šifrování dočasného disku při použití funkce EncryptFormatAll.

Azure Disk Encryption je [integrována s Azure Key Vault](disk-encryption-key-vault.md) , která vám pomůžou řídit a spravovat klíče a tajné kódy disku. Přehled služby najdete v tématu [Azure Disk Encryption pro virtuální počítače se systémem Linux](disk-encryption-overview.md).

Diskové šifrování můžete použít jenom pro virtuální počítače s [podporovanými velikostmi virtuálních počítačů a operačními systémy](disk-encryption-overview.md#supported-vms-and-operating-systems). Musíte splňovat i následující požadavky:

- [Další požadavky na virtuální počítače](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Požadavky na síť](disk-encryption-overview.md#networking-requirements)
- [Požadavky na úložiště šifrovacího klíče](disk-encryption-overview.md#encryption-key-storage-requirements)

Ve všech případech byste si měli [udělat snímek](snapshot-copy-managed-disk.md) a/nebo vytvořit zálohu před tím, než se disky zašifrují. Zálohování zajišťuje možnost obnovení, pokud během šifrování dojde k neočekávané chybě. Virtuální počítače se spravovanými disky vyžadují zálohování před tím, než dojde k šifrování. Po provedení zálohy můžete použít [rutinu Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) k šifrování spravovaných disků zadáním parametru-skipVmBackup. Další informace o zálohování a obnovení šifrovaných virtuálních počítačů najdete v článku o [Azure Backup](../../backup/backup-azure-vms-encryption.md) . 

>[!WARNING]
> - Pokud jste předtím používali Azure Disk Encryption se službou Azure AD k šifrování virtuálního počítače, musíte tuto možnost použít k zašifrování virtuálního počítače. Podrobnosti najdete v tématu [Azure Disk Encryption s Azure AD (předchozí verze)](disk-encryption-overview-aad.md) . 
>
> - Při šifrování svazků se systémem Linux by se měl virtuální počítač považovat za nedostupný. Důrazně doporučujeme vyhnout se přihlášení SSH, zatímco probíhá šifrování, aby nedocházelo k blokování jakýchkoli otevřených souborů, ke kterým bude potřeba během procesu šifrování dostat. Pokud chcete sledovat průběh, použijte příkaz PowerShellu [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) nebo příkaz pro [šifrování virtuálního počítače](/cli/azure/vm/encryption#az-vm-encryption-show) , který se zobrazí. Pro tento proces může trvat několik hodin, než se 30 GB svazek s operačním systémem a další čas šifrování datových svazků. Doba šifrování datového svazku bude úměrná velikosti a množství datových svazků, pokud se nepoužije možnost šifrovat formát ALL. 
> - Zakázání šifrování u virtuálních počítačů se systémem Linux je podporováno pouze pro datové svazky. Pokud je svazek s operačním systémem zašifrovaný, není podpora na svazcích dat nebo operačních systémů podporována.  

## <a name="install-tools-and-connect-to-azure"></a>Nainstalovat nástroje a připojit se k Azure

Azure Disk Encryption můžete povolit a spravovat prostřednictvím rozhraní příkazového [řádku Azure](/cli/azure) a [Azure PowerShell](/powershell/azure/new-azureps-module-az). Pokud to chcete udělat, musíte nástroje nainstalovat místně a připojit se k předplatnému Azure.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2,0](/cli/azure) je nástroj příkazového řádku pro správu prostředků Azure. Rozhraní příkazového řádku je navržené k flexibilnímu dotazování dat, podpoře dlouhotrvajících operací jako neblokujících procesů a snadného skriptování. Můžete ji nainstalovat místně pomocí postupu v části [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

 

Pokud se chcete [přihlásit ke svému účtu Azure pomocí Azure CLI](/cli/azure/authenticate-azure-cli), použijte příkaz [AZ Login](/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```azurecli
az login
```

Pokud chcete vybrat tenanta pro přihlášení, použijte:
    
```azurecli
az login --tenant <tenant>
```

Pokud máte více předplatných a chcete zadat konkrétní konkrétní, získejte seznam předplatných pomocí [AZ Account list](/cli/azure/account#az-account-list) a určete pomocí [AZ Account set](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Další informace najdete v tématu Začínáme [s Azure CLI 2,0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az) poskytuje sadu rutin, které používají model [Azure Resource Manager](../../azure-resource-manager/management/overview.md) ke správě prostředků Azure. Můžete ji použít v prohlížeči pomocí [Azure Cloud Shell](../../cloud-shell/overview.md)nebo ji můžete nainstalovat na místní počítač podle pokynů v tématu [instalace Azure PowerShell modulu](/powershell/azure/install-az-ps). 

Pokud jste ho již nainstalovali místně, ujistěte se, že používáte nejnovější verzi sady Azure PowerShell SDK ke konfiguraci Azure Disk Encryption. Stáhněte si nejnovější verzi [Azure PowerShell verze](https://github.com/Azure/azure-powershell/releases).

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
V tomto scénáři můžete šifrování povolit pomocí šablony Správce prostředků, rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku. Pokud potřebujete informace o schématu pro rozšíření virtuálního počítače, přečtěte si článek [Azure Disk Encryption pro rozšíření pro Linux](../extensions/azure-disk-enc-linux.md) .

>[!IMPORTANT]
 >Je nutné, abyste instanci virtuálního počítače na bázi spravovaného disku pocházeli z a před povolením Azure Disk Encryption. Snímek spravovaného disku se dá vzít z portálu nebo prostřednictvím [Azure Backup](../../backup/backup-azure-vms-encryption.md). Zálohy zajišťují, že možnost obnovení je možné v případě jakékoli neočekávané chyby při šifrování. Po vytvoření zálohy se rutina Set-AzVMDiskEncryptionExtension dá použít k šifrování spravovaných disků zadáním parametru-skipVmBackup. Příkaz Set-AzVMDiskEncryptionExtension se nezdařil proti virtuálním počítačům založeným na disku, dokud nebyla provedena záloha a nebyl zadán tento parametr. 
>
>Šifrování nebo zakázání šifrování může způsobit, že se virtuální počítač restartuje. 
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
    > Syntaxe hodnoty parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:/subscriptions/[ID předplatného-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name]</br>
Syntaxe hodnoty parametru klíč-šifrování klíče je úplný identifikátor URI pro KEK jako v: https://[název trezoru-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

- **Ověřte, že jsou disky šifrované:** Pokud chcete zjistit stav šifrování virtuálního počítače, použijte příkaz [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Zakázat šifrování:** Pokud chcete šifrování zakázat, použijte příkaz [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Zakázání šifrování je povolené jenom u datových svazků pro virtuální počítače se systémem Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "data"
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
    > Syntaxe hodnoty parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:/subscriptions/[ID předplatného-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name]</br> Syntaxe hodnoty parametru klíč-šifrování klíče je úplný identifikátor URI pro KEK jako v: https://[název trezoru-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 
    
- **Ověřte, že jsou disky šifrované:** Pokud chcete zjistit stav šifrování virtuálního počítače, použijte rutinu [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Zakázat šifrování disku:** Pokud chcete šifrování zakázat, použijte rutinu [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Zakázání šifrování je povolené jenom u datových svazků pro virtuální počítače se systémem Linux.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Povolení šifrování na stávajícím nebo běžícím virtuálním počítači se systémem Linux se šablonou

Pomocí [šablony Správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)můžete povolit šifrování disku na stávajícím nebo BĚŽÍCÍm virtuálním počítači se systémem Linux v Azure.

1. V šabloně pro rychlý Start Azure klikněte na **nasadit do Azure** .

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, parametry, právní smlouvy a smlouvu. Kliknutím na **vytvořit** Povolte šifrování na stávajícím nebo SPUŠTĚNém virtuálním počítači.

Následující tabulka uvádí Správce prostředků parametry šablony pro existující nebo běžící virtuální počítače:

| Parametr | Popis |
| --- | --- |
| vmName | Název virtuálního počítače, pro který se má spustit operace šifrování |
| keyVaultName | Název trezoru klíčů, do kterého se má šifrovací klíč nahrát Můžete ji získat pomocí rutiny `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` nebo příkazu rozhraní příkazového řádku Azure CLI `az keyvault list --resource-group "MyKeyVaultResourceGroupName"` .|
| keyVaultResourceGroup | Název skupiny prostředků, která obsahuje Trezor klíčů. |
|  keyEncryptionKeyURL | Adresa URL klíčového šifrovacího klíče, který se používá k šifrování šifrovacího klíče. Tento parametr je nepovinný, pokud v rozevíracím seznamu UseExistingKek vyberete **nokek** . Pokud v rozevíracím seznamu UseExistingKek vyberete možnost **KEK** , musíte zadat hodnotu _keyEncryptionKeyURL_ . |
| volumeType | Typ svazku, na kterém se operace šifrování provádí. Platné hodnoty jsou _operační systém_ , _data_ a _vše_. 
| forceUpdateTag | Pokaždé, když je potřeba vynutit spuštění operace, předat jedinečnou hodnotu, třeba identifikátor GUID. |
| location | Umístění pro všechny prostředky |

Další informace o tom, jak nakonfigurovat šablonu pro šifrování disků virtuálního počítače Linux, najdete v tématu [Azure Disk Encryption pro Linux](../extensions/azure-disk-enc-linux.md).

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Použití funkce EncryptFormatAll pro datové disky na virtuálních počítačích se systémem Linux

Parametr **EncryptFormatAll** zkracuje dobu, po kterou jsou datové disky platformy Linux šifrovány. Oddíly, které splňují určitá kritéria, se naformátují společně se svými aktuálními systémy souborů a pak se znovu připojí k, kde byly před provedením příkazu. Pokud chcete vyloučit datový disk, který splňuje kritéria, můžete ho před spuštěním příkazu odpojit.

 Po spuštění tohoto příkazu se naformátují všechny jednotky, které byly připojené dříve, a vrstva šifrování se spustí na začátku prázdné jednotky. Když je vybraná tato možnost, bude se taky šifrovat dočasný disk připojený k virtuálnímu počítači. Pokud dojde k resetování dočasného disku, bude řešení Azure Disk Encryption po další příležitosti znovu naformátováno a znovu zašifrováno pro virtuální počítač. Po zašifrování disku prostředků nebude moci [Agent Microsoft Azure Linux](../extensions/agent-linux.md) spravovat disk prostředků a povolit soubor odkládacího souboru, ale můžete odkládací soubor ručně nakonfigurovat.

>[!WARNING]
> EncryptFormatAll by neměl být použit, pokud jsou v datových svazcích virtuálního počítače potřebná data. Můžete vyloučit disky ze šifrování odpojováním. Nejdřív byste si měli vyzkoušet EncryptFormatAll nejprve na testovacím virtuálním počítači, pochopit parametr funkce a jeho nevýznam před tím, než se ho pokusíte na produkčním virtuálním počítači. Možnost EncryptFormatAll formátuje datový disk a všechna data, která na něm jsou, budou ztracena. Než budete pokračovat, ověřte, že disky, které chcete vyloučit, jsou správně odpojeny. </br></br>
 >Pokud tento parametr nastavujete při aktualizaci nastavení šifrování, může to vést k restartování před samotným šifrováním. V takovém případě budete chtít odebrat i disk, který nechcete naformátovat ze souboru fstab. Podobně byste před zahájením operace šifrování měli přidat do souboru fstab oddíl, který chcete zašifrovat. 

### <a name="encryptformatall-criteria"></a>EncryptFormatAll kritéria
Parametr přechází do všech oddílů a zašifruje je tak dlouho, dokud splňují **všechna** následující kritéria:
- Není kořenovým adresářem/operačním systémem nebo spouštěcím oddílem.
- Ještě není zašifrováno
- Není klíče bek svazek.
- Není svazek RAID.
- Není LVM svazek.
- Je připojeno

Zašifrujte disky, které tvoří svazek RAID nebo LVM, a ne svazek RAID nebo LVM.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Použití parametru EncryptFormatAll s rozhraním příkazového řádku Azure
Pomocí příkazu [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) Povolte šifrování na běžícím virtuálním počítači v Azure.

-  **Šifrování spuštěného virtuálního počítače pomocí EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "data" --encrypt-format-all
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

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "data" -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Použití parametru EncryptFormatAll u Správce logických svazků (LVM) 
Doporučujeme LVM instalaci. Pro všechny následující příklady nahraďte zařízení-Path a mountpoints bez ohledu na to, jaký je váš případ použití. Tuto instalaci můžete provést takto:

1.  Přidejte datové disky, které budou tvořit virtuální počítač.

1. Naformátujte, připojte a přidejte tyto disky do souboru fstab.

1. Zvolte oddíl Standard, vytvořte oddíl, který pokrývá celou jednotku, a pak oddíl naformátujte. Symbolických odkazů vygenerované v Azure používáme tady. Použití symbolických odkazů zabraňuje problémům souvisejícím se změnou názvů zařízení. Další informace najdete v článku řešení potíží s chybami [názvů zařízení](../troubleshooting/troubleshoot-device-names-problems.md) .
    
    ```bash
    parted /dev/disk/azure/scsi1/lun0 mklabel gpt
    parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
    
    mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
    ```

1. Připojte disky:

    ```bash
    mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint
    ````
    
    Přidat do souboru fstab:

    ```bash
    echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab
    ```
    
1. Spuštěním rutiny Azure PowerShell [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-3.8.0) s-EncryptFormatAll Zašifrujte tyto disky.

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
    ```

    Pokud chcete použít klíč šifrovacího klíče (KEK), předejte identifikátor URI vašeho KEK a ResourceID vašeho trezoru klíčů do parametrů-KeyEncryptionKeyUrl a-KeyEncryptionKeyVaultId v uvedeném pořadí:

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    $KEKKeyVault = Get-AzKeyVault -VaultName "MyKEKVault" -ResourceGroupName "MySecureGroup"
    $KEK = Get-AzKeyVaultKey -VaultName "myKEKVault" -KeyName "myKEKName"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data -KeyEncryptionKeyUrl $$KEK.id -KeyEncryptionKeyVaultId $KEKKeyVault.ResourceId
    ```

1. Nastavte LVM na těchto nových discích. Poznámka: šifrované jednotky jsou po dokončení spuštění virtuálního počítače odemčeny. Proto bude nutné připojení LVM také později zpozdit.

## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nové virtuální počítače vytvořené z VHD a šifrovacích klíčů šifrovaných zákazníkem
V tomto scénáři můžete povolit šifrování pomocí rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku (CLI). 

Pro přípravu předem šifrovaných imagí, které je možné použít v Azure, použijte stejné pokyny jako v tématu Azure Disk Encryption – stejné skripty. Po vytvoření image můžete pomocí kroků v následující části vytvořit zašifrovaný virtuální počítač Azure.

* [Příprava předem zašifrovaného virtuálního pevného disku se systémem Linux](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Je nutné, abyste instanci virtuálního počítače na bázi spravovaného disku pocházeli z a před povolením Azure Disk Encryption. Snímek spravovaného disku se dá vzít z portálu, nebo se dá použít [Azure Backup](../../backup/backup-azure-vms-encryption.md) . Zálohy zajišťují, že možnost obnovení je možné v případě jakékoli neočekávané chyby při šifrování. Po vytvoření zálohy se rutina Set-AzVMDiskEncryptionExtension dá použít k šifrování spravovaných disků zadáním parametru-skipVmBackup. Příkaz Set-AzVMDiskEncryptionExtension se nezdařil proti virtuálním počítačům založeným na disku, dokud nebyla provedena záloha a nebyl zadán tento parametr. 
>
> Šifrování nebo zakázání šifrování může způsobit, že se virtuální počítač restartuje. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Použití Azure PowerShell k šifrování virtuálních počítačů s předem šifrovanými virtuálními počítači 
Pomocí rutiny PowerShellu [set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples)můžete povolit šifrování disku na šifrovaném virtuálním pevném disku. V následujícím příkladu jsou uvedeny některé běžné parametry. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Povolení šifrování u nově přidaného datového disku

Nový datový disk můžete přidat pomocí [AZ VM disk Attach](add-disk.md)nebo [prostřednictvím Azure Portal](attach-disk-portal.md). Než budete moct šifrování, musíte nejdřív připojit nově připojený datový disk. Musíte požádat o šifrování datové jednotky, protože jednotka nebude v průběhu šifrování použitelná. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Povolení šifrování u nově přidaného disku pomocí Azure CLI

 Pokud byl virtuální počítač dříve zašifrován pomocí možnosti "vše", parametr--Volume-Type by měl zůstat "All". Vše zahrnuje operační systém i datové disky. Pokud byl virtuální počítač předtím zašifrovaný pomocí typu svazku "OS", měl by být parametr--Volume-Type změněn na hodnotu "All", takže bude zahrnut jak operační systém, tak i nový datový disk. Pokud byl virtuální počítač zašifrován pouze s typem svazku "data", může zůstat "data", jak je znázorněno níže. Přidání a připojení nového datového disku k virtuálnímu počítači není dostatečně přípravné na šifrování. Předtím, než povolíte šifrování, musí být nově připojený disk naformátovaný a správně připojený k virtuálnímu počítači. V systému Linux musí být disk připojen v/etc/fstab s [názvem trvalého blokování zařízení](../troubleshooting/troubleshoot-device-names-problems.md).  

Na rozdíl od syntaxe PowerShellu rozhraní příkazového řádku nevyžaduje, aby uživatel při povolování šifrování poskytoval jedinečnou verzi sekvence. Rozhraní příkazového řádku automaticky vygeneruje a použije svou vlastní jedinečnou hodnotu verze sekvence.

-  **Šifrování datových svazků spuštěného virtuálního počítače:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Šifrování datových svazků spuštěného virtuálního počítače pomocí KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Povolit šifrování na nově přidaném disku s Azure PowerShell
 Při použití prostředí PowerShell k šifrování nového disku pro Linux je nutné zadat novou verzi sekvence. Verze sekvence musí být jedinečná. Následující skript vygeneruje identifikátor GUID pro verzi sekvence. Pořídit [snímek](snapshot-copy-managed-disk.md) nebo zálohovat virtuální počítač s [Azure Backup](../../backup/backup-azure-vms-encryption.md) před zašifrováním disků. Parametr-skipVmBackup je už ve skriptech PowerShellu určený k zašifrování nově přidaného datového disku.
 

-  **Šifrování datových svazků spuštěného virtuálního počítače:** Skript níže inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. Skupina prostředků, virtuální počítač a trezor klíčů by už měly být vytvořené jako požadavky. Nahraďte MyVirtualMachineResourceGroup, MySecureVM a MySecureVault hodnotami. Přijatelné hodnoty pro parametr-VolumeType jsou všechny, operační systém a data. Pokud byl virtuální počítač předtím zašifrovaný pomocí typu svazku "OS" nebo "All", měl by být parametr-VolumeType změněn na "All", takže bude zahrnut jak operační systém, tak i nový datový disk.

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
- **Šifrování datových svazků spuštěného virtuálního počítače pomocí KEK:** Přijatelné hodnoty pro parametr-VolumeType jsou všechny, operační systém a data. Pokud byl virtuální počítač předtím zašifrovaný pomocí typu svazku "OS" nebo "All", měl by být parametr-VolumeType změněn na hodnotu All, aby byl operační systém i nový datový disk zahrnut.

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
    > Syntaxe hodnoty parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:/subscriptions/[ID předplatného-GUID]/resourceGroups/[KVresource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name]</br> Syntaxe hodnoty parametru klíč-šifrování klíče je úplný identifikátor URI pro KEK jako v: https://[název trezoru-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 


## <a name="disable-encryption-for-linux-vms"></a>Zakázat šifrování pro virtuální počítače se systémem Linux
[!INCLUDE [disk-encryption-disable-encryption-cli](../../../includes/disk-encryption-disable-cli.md)]

## <a name="unsupported-scenarios"></a>Nepodporované scénáře

Azure Disk Encryption nefunguje pro následující scénáře, funkce a technologie pro Linux:

- Šifrování virtuálních počítačů nebo virtuálních počítačů na úrovni Basic vytvořených prostřednictvím metody vytváření virtuálních počítačů
- Zakázání šifrování na jednotce operačního systému nebo datové jednotce virtuálního počítače se systémem Linux, pokud je jednotka operačního systému zašifrovaná.
- Šifrování jednotky operačního systému pro virtuální počítače se systémem Linux Virtual Machine Scale Sets.
- Šifrování vlastních imagí na virtuálních počítačích se systémem Linux.
- Integrace s místním systémem správy klíčů.
- Soubory Azure (sdílený systém souborů).
- Systém souborů NFS (Network File System).
- Dynamické svazky.
- Dočasné disky s operačním systémem.
- Šifrování sdílených/distribuovaných systémů souborů, jako je (ale ne omezené): DFS, GFS, DRDB a CephFS.
- Přesunutí šifrovaného virtuálního počítače do jiného předplatného nebo oblasti.
- Vytvoření bitové kopie nebo snímku šifrovaného virtuálního počítače a jeho použití k nasazení dalších virtuálních počítačů.
- Výpis stavu systému jádra (kdump).
- Oracle ACFS (systém souborů clusteru ASM).
- Virtuální počítače s Gen2 (viz: [Podpora pro virtuální počítače 2. generace v Azure](../generation-2.md#generation-1-vs-generation-2-capabilities)).
- Disky NVMe virtuálních počítačů Lsv2 Series (viz: [Lsv2-Series](../lsv2-series.md)).
- Virtuální počítač s "vnořenými přípojnými body"; To znamená, že několik přípojných bodů v jedné cestě (například "/1stmountpoint/data/2stmountpoint").
- Virtuální počítač s datovou jednotkou připojenou nad složku operačního systému.
- Virtuální počítače řady M-Series s Akcelerátor zápisu disky.
- Použití ADE na virtuální počítač, který obsahuje disky šifrované pomocí [šifrování na straně serveru s klíči spravovanými zákazníkem](disk-encryption.md) (SSE + CMK). Použití SSE + CMK na datový disk na virtuálním počítači zašifrovaném pomocí ADE je také nepodporovaný scénář.
- Migrace virtuálního počítače, který je zašifrovaný pomocí ADE nebo který byl **někdy** ZAŠIFROVANÝ pomocí ADE, na [šifrování na straně serveru pomocí klíčů spravovaných zákazníkem](disk-encryption.md).
- [Velikosti virtuálních počítačů Azure bez místního dočasného disku](../azure-vms-no-temp-disk.md); konkrétně dv4, Dsv4, Ev4 a Esv4.

## <a name="next-steps"></a>Další kroky

- [Přehled Azure Disk Encryption](disk-encryption-overview.md)
- [Ukázkové skripty pro službu Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Řešení potíží se službou Azure Disk Encryption](disk-encryption-troubleshooting.md)
