---
title: Scénáře služby Azure Disk Encryption na virtuálních počítačích s Linuxem
description: Tento článek obsahuje pokyny k povolení microsoft azure diskového šifrování pro virtuální počítače s Linuxem pro různé scénáře
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: d860b557c5c2d6b73fad53cdf84ee8b32182d1ee
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985452"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Scénáře služby Azure Disk Encryption na virtuálních počítačích s Linuxem


Azure Disk Encryption pro virtuální počítače s Linuxem (VM) používá funkci DM-Crypt linuxu k zajištění úplného šifrování disku operačního systému a datových disků. Navíc poskytuje šifrování dočasný chod disku při použití encryptformatall funkce.

Azure Disk Encryption je [integrovaný s Azure Key Vault,](disk-encryption-key-vault.md) který vám pomůže řídit a spravovat šifrovací klíče a tajné klíče disku. Přehled služby najdete v tématu [Azure Disk Encryption for Windows VMs](disk-encryption-overview.md).

Šifrování disku lze použít jenom na virtuální počítače [podporovaných velikostí virtuálních počítačů a operačních systémů](disk-encryption-overview.md#supported-vms-and-operating-systems). Musíte také splnit následující předpoklady:

- [Další požadavky na virtuální chod](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Požadavky na vytváření sítí](disk-encryption-overview.md#networking-requirements)
- [Požadavky na úložiště šifrovacího klíče](disk-encryption-overview.md#encryption-key-storage-requirements)

Ve všech případech byste měli [pořídit snímek](snapshot-copy-managed-disk.md) nebo vytvořit zálohu před zašifrováním disků. Zálohy zajistí, že možnost obnovení je možné, pokud dojde k neočekávané chybě během šifrování. Virtuální počítače se spravovanými disky vyžadují zálohu, než dojde k šifrování. Po vytvoření zálohy můžete pomocí [rutiny Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) šifrovat spravované disky zadáním parametru -skipVmBackup. Další informace o tom, jak zálohovat a obnovovat šifrované virtuální počítače, najdete v článku [Zálohování Azure.](../../backup/backup-azure-vms-encryption.md) 

>[!WARNING]
> - Pokud jste dříve používali Azure Disk Encryption s Azure AD k šifrování virtuálního počítače, musíte pokračovat v použití této možnosti k šifrování virtuálního počítače. Podrobnosti najdete [v tématu Azure Disk Encryption with Azure AD (předchozí verze).](disk-encryption-overview-aad.md) 
>
> - Při šifrování svazků operačního systému Linux by měl být virtuální počítač považován za nedostupný. Důrazně doporučujeme vyhnout se přihlášení SSH, zatímco šifrování probíhá, aby se zabránilo problémům, které blokují všechny otevřené soubory, které budou muset být přístupné během procesu šifrování. Chcete-li zkontrolovat průběh, použijte rutinu [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell nebo příkaz příkazu [příkazu](/cli/azure/vm/encryption#az-vm-encryption-show) příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkaz Tento proces může trvat několik hodin pro svazek operačního systému 30 GB a další čas pro šifrování datových svazků. Doba šifrování svazku dat bude úměrná velikosti a množství datových svazků, pokud nebude použit ašifrovací formát všechny možnosti. 
> - Zakázání šifrování na virtuálních počítačích s Linuxem je podporované jenom pro datové svazky. Není podporovánna svazcích dat nebo operačního systému, pokud byl svazek operačního systému zašifrován.  

## <a name="install-tools-and-connect-to-azure"></a>Instalace nástrojů a připojení k Azure

Azure Disk Encryption můžete povolit a spravovat prostřednictvím [Azure CLI](/cli/azure) a [Azure PowerShell](/powershell/azure/new-azureps-module-az). Chcete-li tak učinit, musíte nainstalovat nástroje místně a připojit se k předplatnému Azure.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) je nástroj příkazového řádku pro správu prostředků Azure. ClI je navržen tak, aby flexibilně dotazovat data, podporovat dlouhotrvající operace jako neblokující procesy a usnadnit skriptování. Můžete ji nainstalovat místně podle následujících kroků v [instalaci příkazového příkazového příkazu k webu Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

 

Pokud [se chcete přihlásit ke svému účtu Azure pomocí azure cli](/cli/azure/authenticate-azure-cli), použijte příkaz [az přihlášení.](/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli
az login
```

Pokud chcete vybrat klienta, který se má přihlásit, použijte:
    
```azurecli
az login --tenant <tenant>
```

Pokud máte více předplatných a chcete zadat konkrétní, získejte seznam odběrů se [seznamem účtů az](/cli/azure/account#az-account-list) a zadejte s [nastaveným účtem AZ](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Další informace najdete [v tématu Začínáme s Azure CLI 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Modul az Azure PowerShell](/powershell/azure/new-azureps-module-az) poskytuje sadu rutin, které používají model [Azure Resource Manager](../../azure-resource-manager/management/overview.md) pro správu prostředků Azure. Můžete ji použít ve svém prohlížeči s [Azure Cloud Shell](../../cloud-shell/overview.md), nebo můžete nainstalovat na místním počítači pomocí pokynů v nainstalovat modul Azure [PowerShell](/powershell/azure/install-az-ps). 

Pokud už máte nainstalovaný místně, ujistěte se, že ke konfiguraci šifrování disku Azure používáte nejnovější verzi sady Azure PowerShell SDK. Stáhněte si nejnovější verzi [Azure PowerShellu](https://github.com/Azure/azure-powershell/releases).

Pokud [se chcete přihlásit ke svému účtu Azure pomocí Azure PowerShellu](/powershell/azure/authenticate-azureps?view=azps-2.5.0), použijte rutinu [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```powershell
Connect-AzAccount
```

Pokud máte více předplatných a chcete zadat jeden, použijte [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) rutina jejich seznam, následovaný [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) rutina:

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Spuštění rutiny [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) ověří, zda bylo vybráno správné předplatné.

Chcete-li potvrdit, že jsou nainstalovány rutiny Azure Disk Encryption, použijte rutinu [get-command:](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6)
     
```powershell
Get-command *diskencryption*
```
Další informace najdete [v tématu Začínáme s Azure PowerShell](/powershell/azure/get-started-azureps). 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Povolení šifrování na existujícím nebo spuštěném virtuálním počítači s Linuxem
V tomto scénáři můžete povolit šifrování pomocí šablony Správce prostředků, rutin prostředí PowerShell nebo příkazů příkazu CLI. Pokud potřebujete informace o schématu pro rozšíření virtuálního počítače, přečtěte si článek [rozšíření Azure Disk Encryption for Linux.](../extensions/azure-disk-enc-linux.md)

>[!IMPORTANT]
 >Je povinné snímek nebo zálohování spravovaného disku na virtuální počítač mimo a před povolením Azure Disk Encryption. Snímek spravovaného disku lze převzít z portálu nebo prostřednictvím [služby Azure Backup](../../backup/backup-azure-vms-encryption.md). Zálohy zajišťují, že možnost obnovení je možná v případě neočekávaného selhání během šifrování. Po vytvoření zálohy lze rutinu Set-AzVMDiskEncryptionExtension použít k šifrování spravovaných disků zadáním parametru -skipVmBackup. Příkaz Set-AzVMDiskEncryptionExtension se nezdaří u virtuálních počítačů založených na spravovaném disku, dokud nebude provedena záloha a tento parametr nebude zadán. 
>
>Šifrování nebo zakázání šifrování může způsobit restartování virtuálního počítače. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Povolení šifrování na existujícím nebo spuštěném virtuálním počítači s Linuxem pomocí azure cli 

Šifrování disku na šifrovaném virtuálním pevném disku můžete povolit instalací a použitím nástroje příkazového řádku [Azure CLI.](/cli/azure/?view=azure-cli-latest) Můžete ho používat v prohlížeči pomocí služby [Azure Cloud Shell](../../cloud-shell/overview.md) nebo nainstalovat na místním počítači a používat ho v jakékoli relaci PowerShellu. Pokud chcete povolit šifrování na existujících nebo spuštěných virtuálních počítačích SI linuxv Azure, použijte následující příkazy příkazového příkazu:

Pomocí příkazu [povolit šifrování az vm](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) povolte šifrování na běžícím virtuálním počítači v Azure.

- **Šifrování spuštěného virtuálního virtuálního montova:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Šifrování spuštěného virtuálního virtuálního aplikace pomocí kek:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Syntaxe hodnoty parametru disk-encryption-keyvault je úplný řetězec identifikátoru: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
Syntaxe hodnoty parametru key-encryption-key je úplná identifikátor URI pro KEK jako: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ověřte, zda jsou disky zašifrovány:** Chcete-li zkontrolovat stav šifrování virtuálního počítače, použijte příkaz [az vm šifrování show.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Zakázat šifrování:** Chcete-li zakázat šifrování, použijte příkaz [zakázat šifrování az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) Zakázání šifrování je povoleno jenom na datových svazcích pro virtuální počítače s Linuxem.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Povolení šifrování na existujícím nebo spuštěném virtuálním počítači s Linuxem pomocí PowerShellu
Pomocí rutiny [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) povolte šifrování na spuštěném virtuálním počítači v Azure. Pořiďte [snímek](snapshot-copy-managed-disk.md) a/nebo zálohujte virtuální počítač pomocí [azure backuppřed](../../backup/backup-azure-vms-encryption.md) zašifrováním disků. Parametr -skipVmBackup je již zadán ve skriptech prostředí PowerShell pro šifrování spuštěného virtuálního počítače s Linuxem.

-  **Šifrování spuštěného virtuálního virtuálního montova:** Níže uvedený skript inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. Skupina prostředků, virtuální hokvem a trezor klíčů byly vytvořeny jako předpoklady. Nahraďte hodnoty MyVirtualMachineResourceGroup, MySecureVM a MySecureVault. Upravte parametr -VolumeType a určete, které disky šifrujete.

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
- **Šifrování spuštěného virtuálního virtuálního aplikace pomocí kek:** Pokud šifrujete datové disky, a ne disk operačního systému, bude pravděpodobně nutné přidat parametr -VolumeType. 

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
    > Syntaxe hodnoty parametru disk-encryption-keyvault je úplný řetězec identifikátoru: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxe hodnoty parametru key-encryption-key je úplná identifikátor URI pro KEK jako: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Ověřte, zda jsou disky zašifrovány:** Chcete-li zkontrolovat stav šifrování virtuálního počítače, použijte rutinu [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Zakázat šifrování disku:** Chcete-li šifrování zakázat, použijte rutinu [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Zakázání šifrování je povoleno jenom na datových svazcích pro virtuální počítače s Linuxem.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Povolení šifrování na existujícím nebo spuštěném virtuálním počítači s Linuxem pomocí šablony

Šifrování disku na existujícím nebo spuštěném virtuálním počítači SIP v Azure můžete povolit pomocí [šablony Správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Klikněte na **nasadit do Azure** na šabloně Azure quickstart.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, parametry, právní podmínky a smlouvu. Kliknutím na **Vytvořit** povolíte šifrování na existujícím nebo spuštěném virtuálním počítači.

V následující tabulce jsou uvedeny parametry šablony Správce prostředků pro existující nebo spuštěné virtuální počítače:

| Parametr | Popis |
| --- | --- |
| vmName | Název virtuálního virtuálního serveru pro spuštění operace šifrování. |
| keyVaultName | Název trezoru klíčů, do kterého by měl být šifrovací klíč nahrán. Můžete ji získat pomocí rutiny `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` nebo příkazu `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`Azure CLI .|
| keyVaultResourceGroup | Název skupiny prostředků, která obsahuje trezor klíčů. |
|  keyEncryptionKeyURL | Adresa URL šifrovacího klíče klíče, který se používá k šifrování šifrovacího klíče. Tento parametr je volitelný, pokud vyberete **nokek** v rozevíracím seznamu UseExistingKek. Pokud vyberete **kek** v rozevíracím seznamu UseExistingKek, musíte zadat hodnotu _keyEncryptionKeyURL._ |
| volumeType | Typ svazku, na které se provádí operace šifrování. Platné hodnoty jsou _OS_, _Data_a _All_. 
| forceUpdateTag | Předat v jedinečnou hodnotu, jako je IDENTIFIKÁTOR GUID pokaždé, když operace musí být force run. |
| location | Umístění pro všechny zdroje. |

Další informace o konfiguraci šablony šifrování disku virtuálního počítače v Linuxu najdete v [tématu Azure Disk Encryption for Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/azure-disk-enc-linux).

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Použití funkce EncryptFormatAll pro datové disky na virtuálních počítačích s Linuxem

Parametr **EncryptFormatAll** zkracuje dobu šifrování datových disků Linuxu. Oddíly splňující určitá kritéria budou formátovány (s aktuálním systémem souborů) a poté znovu nastoaleny zpět tam, kde byly před spuštěním příkazu. Chcete-li vyloučit datový disk, který splňuje kritéria, můžete jej před spuštěním příkazu odpojit.

 Po spuštění tohoto příkazu budou všechny jednotky, které byly připojeny dříve, formátovány a vrstva šifrování bude spuštěna nad nyní prázdnou jednotkou. Pokud je vybrána tato možnost, dočasný disk prostředků připojený k virtuálnímu počítače bude také zašifrován. Pokud se výplachová jednotka resetuje, bude přeformátována a znovu zašifrována pro virtuální počítač řešením Azure Disk Encryption při příští příležitosti. Jakmile se disk prostředků zašifruje, [agent Microsoft Azure Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) nebude moct spravovat disk prostředků a povolit odkládací soubor, ale můžete odkládací soubor nakonfigurovat ručně.

>[!WARNING]
> EncryptFormatAll by se neměl používat, když jsou na datových svazcích virtuálního počítače potřebná data. Disky můžete vyloučit z šifrování jejich odpojením. Měli byste nejprve vyzkoušet EncryptFormatAll nejprve na testovací virtuální počítač, pochopit parametr funkce a jeho důsledky před pokusem o to na produkční virtuální počítač. Možnost EncryptFormatAll formátuje datový disk a všechna data na něm budou ztracena. Než budete pokračovat, ověřte, zda jsou disky, které chcete vyloučit, správně odpojeny. </br></br>
 >Pokud nastavujete tento parametr při aktualizaci nastavení šifrování, může to vést k restartování před skutečným šifrováním. V takovém případě budete také chtít odstranit disk, který nechcete formátovat ze souboru fstab. Podobně byste měli přidat oddíl, který chcete šifrovat, do souboru fstab před zahájením operace šifrování. 

### <a name="encryptformatall-criteria"></a>EncryptFormatVšechna kritéria
Parametr jde i když všechny oddíly a šifruje je tak dlouho, dokud splňují **všechna** níže uvedená kritéria: 
- Není kořenový/OS/spouštěcí oddíl
- Ještě není zašifrována
- Není svazek BEK
- Není svazek RAID
- Není svazek LVM
- Je namontován

Šifrujte disky, které tvoří svazek RAID nebo LVM, nikoli svazek RAID nebo LVM.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Použití parametru EncryptFormatAll s rozhraním příkazového řádku Azure
Pomocí příkazu [povolit šifrování az vm](/cli/azure/vm/encryption#az-vm-encryption-enable) povolte šifrování na běžícím virtuálním počítači v Azure.

-  **Šifrování spuštěného virtuálního virtuálního montovapomocí EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Použití parametru EncryptFormatAll s rutinou prostředí PowerShell
Použijte rutinu [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) s parametrem EncryptFormatAll. 

**Šifrování spuštěného virtuálního virtuálního montovapomocí EncryptFormatAll:** Jako příklad skript níže inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension s parametrem EncryptFormatAll. Skupina prostředků, virtuální hokandinár a trezor klíčů byly vytvořeny jako předpoklady. Nahraďte hodnoty MyVirtualMachineResourceGroup, MySecureVM a MySecureVault.
  
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


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Použití parametru EncryptFormatAll se Správcem logických svazků (LVM) 
Doporučujeme nastavení LVM-on-crypt. Pro všechny následující příklady nahraďte cestu zařízení a přípojné body s co vyhovuje vašemu případu použití. Toto nastavení lze provést následujícím způsobem:

- Přidejte datové disky, které budou skládat virtuální počítače.
- Naformátujte, připojujte a přidejte tyto disky do souboru fstab.

    1. Zvolte standardní oddíl, vytvořte oddíl, který pokrývá celou jednotku, a pak oddíl naformátujte. Tady používáme symbolické odkazy generované Azure. Using symlinks avoids problems related to device names changing. Další informace naleznete v článku [Poradce při potížích s názvy zařízení.](troubleshoot-device-names-problems.md)
    
         ```azurepowershell-interactive
         parted /dev/disk/azure/scsi1/lun0 mklabel gpt
         parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
         
         mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
         ```
    
    1. Připojte disky.
         
         `mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint`
    
    1. Přidat do fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab`
    
    1. Spusťte rutinu Set-AzVMDiskEncryptionExtension PowerShell s -EncryptFormatAll k šifrování těchto disků.

       ```azurepowershell-interactive
       $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
           
       Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
       ```

    1. Nastavte LVM nad těmito novými disky. Všimněte si, že šifrované jednotky jsou odemčené po dokončení zavádění virtuálního zařízení. Takže montáž LVM bude muset být také následně zpožděna.


## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nové virtuální virtuální aplikace vytvořené ze zákazníkem šifrovaného virtuálního pevného disku a šifrovacích klíčů
V tomto scénáři můžete povolit šifrování pomocí rutin prostředí PowerShell nebo příkazů příkazu cli. 

Použijte pokyny v azure disku šifrování stejné skripty pro přípravu předem šifrované image, které lze použít v Azure. Po vytvoření image můžete pomocí kroků v další části vytvořit šifrovaný virtuální počítač Azure.

* [Příprava předem šifrovaného virtuálního pevného disku systému Linux](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Je povinné snímek nebo zálohování spravovaného disku na virtuální počítač mimo a před povolením Azure Disk Encryption. Snímek spravovaného disku lze převzít z portálu nebo lze použít [Azure Backup.](../../backup/backup-azure-vms-encryption.md) Zálohy zajišťují, že možnost obnovení je možná v případě neočekávaného selhání během šifrování. Po vytvoření zálohy lze rutinu Set-AzVMDiskEncryptionExtension použít k šifrování spravovaných disků zadáním parametru -skipVmBackup. Příkaz Set-AzVMDiskEncryptionExtension se nezdaří u virtuálních počítačů založených na spravovaném disku, dokud nebude provedena záloha a tento parametr nebude zadán. 
>
> Šifrování nebo zakázání šifrování může způsobit restartování virtuálního počítače. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Šifrování virtuálních počítačů pomocí předem šifrovaných virtuálních disponiálů pomocí Azure PowerShellu 
Šifrování disku v zašifrovaném virtuálním pevném disku můžete povolit pomocí rutiny PowerShell [Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). Následující příklad poskytuje některé běžné parametry. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Povolení šifrování na nově přidaném datovém disku

Nový datový disk můžete přidat pomocí [připojení disku AZ VM](add-disk.md)nebo [prostřednictvím portálu Azure](attach-disk-portal.md). Před šifrováním je třeba nejprve připojit nově připojený datový disk. Musíte požádat o šifrování datové jednotky, protože jednotka bude během šifrování nepoužitelná. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Povolení šifrování na nově přidaném disku pomocí azure cli

 Pokud byl virtuální virtuální byl dříve zašifrován s "Všechny", pak --parametr typu svazku by měl zůstat "Všechny". Všechny zahrnují operační systém i datové disky. Pokud byl virtuální modul dříve zašifrován pomocí typu svazku "OS", měl by být parametr --volume-type změněn na "Všechny", aby byl zahrnut operační systém i nový datový disk. Pokud byl virtuální virtuální ms šifrován pouze s typem svazku "Data", pak může zůstat "Data", jak je znázorněno níže. Přidání a připojení nového datového disku k virtuálnímu počítače není dostatečná příprava na šifrování. Nově připojený disk musí být také formátován a správně připojen v rámci virtuálního počítače před povolením šifrování. Na Linuxu musí být disk namontován v /etc/fstab s [trvalým názvem blokového zařízení](troubleshoot-device-names-problems.md).  

Na rozdíl od syntaxe prostředí Powershell nevyžaduje rozhraní řízení chování uživatele k zadání jedinečné sekvenční verze při povolení šifrování. ClI automaticky generuje a používá vlastní hodnotu jedinečné sekvence verze.

-  **Šifrování datových svazků spuštěného virtuálního mísy:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Šifrování datových svazků spuštěného virtuálního virtuálního aplikace pomocí kek:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Povolení šifrování na nově přidaném disku pomocí Azure PowerShellu
 Při použití Powershellu k šifrování nového disku pro Linux je třeba zadat novou sekvenční verzi. Sekvenční verze musí být jedinečná. Níže uvedený skript generuje identifikátor GUID pro sekvenční verzi. Pořiďte [snímek](snapshot-copy-managed-disk.md) a/nebo zálohujte virtuální počítač pomocí [azure backuppřed](../../backup/backup-azure-vms-encryption.md) zašifrováním disků. Parametr -skipVmBackup je již zadán ve skriptech prostředí PowerShell pro šifrování nově přidaného datového disku.
 

-  **Šifrování datových svazků spuštěného virtuálního mísy:** Níže uvedený skript inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. Skupina prostředků, virtuální hod a trezor klíčů už měly být vytvořeny jako předpoklady. Nahraďte hodnoty MyVirtualMachineResourceGroup, MySecureVM a MySecureVault. Přijatelné hodnoty parametru -VolumeType jsou Všechny, Operační a Data. Pokud byl virtuální modul dříve zašifrován pomocí typu svazku "OS" nebo "All", měl by být parametr -VolumeType změněn na "Všechny", aby byl zahrnut operační systém i nový datový disk.

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
- **Šifrování datových svazků spuštěného virtuálního virtuálního aplikace pomocí kek:** Přijatelné hodnoty parametru -VolumeType jsou Všechny, Operační a Data. Pokud byl virtuální modul dříve zašifrován pomocí typu svazku "OS" nebo "All", měl by být parametr -VolumeType změněn na Vše, aby byl zahrnut operační systém i nový datový disk.

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
    > Syntaxe hodnoty parametru disk-encryption-keyvault je úplný řetězec identifikátoru: /subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVaults/[keyvault-name]</br> Syntaxe hodnoty parametru key-encryption-key je úplná identifikátor URI pro KEK jako: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Zakázání šifrování pro virtuální počítače s Linuxem
Šifrování můžete zakázat pomocí Azure PowerShellu, Azure CLI nebo pomocí šablony Správce prostředků. 

>[!IMPORTANT]
>Zakázání šifrování pomocí Azure Disk Encryption na virtuálních počítačích S IP je podporované jenom pro datové svazky. Není podporovánna svazcích dat nebo operačního systému, pokud byl svazek operačního systému zašifrován.  

- **Zakázání šifrování disku pomocí Azure PowerShellu:** Chcete-li šifrování zakázat, použijte rutinu [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Zakažte šifrování pomocí azure cli:** Chcete-li zakázat šifrování, použijte příkaz [zakázat šifrování az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Zakázání šifrování pomocí šablony Správce prostředků:** Šifrování [zakažte](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) pomocí funkce Zakázat pomocí funkce Zakázat šifrování na spuštěné šabloně virtuálního počítače s Linuxem.
     1. Klikněte na **Nasadit do Azure**.
     2. Vyberte předplatné, skupinu prostředků, umístění, virtuální hod, právní podmínky a smlouvu.

## <a name="unsupported-scenarios"></a>Nepodporované scénáře

Azure Disk Encryption nefunguje pro následující scénáře, funkce a technologie Linuxu:

- Šifrování základních virtuálních virtuálních proudů nebo virtuálních vás vytvořených pomocí klasické metody vytváření virtuálních montovek.
- Zakázání šifrování na jednotce operačního systému nebo datové jednotce virtuálního počítače s Operačním systémem Linux, když je jednotka operačního systému šifrovaná.
- Šifrování jednotky operačního systému pro škálovací sady virtuálních strojů pro Linux.
- Šifrování vlastních irek na virtuálních počítačích s Linuxem.
- Integrace s místním systémem správy klíčů.
- Soubory Azure (sdílený systém souborů).
- systému souborů (NFS).
- Dynamické svazky.
- Dočasné disky operačního systému.
- Šifrování sdílených/distribuovaných souborových systémů, jako je (ale nejen): DFS, GFS, DRDB a CephFS.
- Přesunutí šifrovaného virtuálního min. do jiného předplatného.
- Výpis stavu jádra (kdump).
- Oracle ACFS (systém souborů ASM Cluster)
- Virtuální počítače Gen2 (viz: [Podpora pro virtuální počítače generace 2 v Azure)](generation-2.md#generation-1-vs-generation-2-capabilities)
- Virtuální zařízení řady Lsv2 (viz: [Řada Lsv2](../lsv2-series.md))

## <a name="next-steps"></a>Další kroky

- [Azure Disk Encryption – přehled](disk-encryption-overview.md)
- [Ukázkové skripty pro službu Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Řešení potíží se službou Azure Disk Encryption](disk-encryption-troubleshooting.md)
