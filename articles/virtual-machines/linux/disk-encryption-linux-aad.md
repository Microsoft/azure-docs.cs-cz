---
title: Šifrování disku Azure pomocí virtuálních počítačů Azure AD App Linux IaaS (předchozí verze)
description: Tento článek obsahuje pokyny k povolení microsoft azure diskšifrování pro linuxové virtuální počítače IaaS.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: ee365d37a957350fa8a68da0f34149d3210d6238
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970608"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Povolení šifrování disku Azure pomocí Azure AD na virtuálních počítačích s Linuxem (předchozí verze)

Nová verze Azure Disk Encryption eliminuje požadavek na poskytování parametru aplikace Azure Active Directory (Azure AD) k povolení šifrování disku virtuálního počítače. S novou verzí už nemusíte poskytovat přihlašovací údaje Azure AD během kroku povolení šifrování. Všechny nové virtuální počítače musí být šifrované bez parametrů aplikace Azure AD pomocí nové verze. Pokyny k povolení šifrování disku virtuálního počítače pomocí nové verze najdete v tématu [Azure Disk Encryption for Linux VMS](disk-encryption-linux.md). Virtuální počítače, které už byly zašifrované pomocí parametrů aplikace Azure AD, jsou stále podporované a měly by být nadále udržovány pomocí syntaxe AAD.

Můžete povolit mnoho scénářů šifrování disku a kroky se mohou lišit podle scénáře. Následující části popisují scénáře podrobněji pro infrastrukturu Linuxu jako virtuální počítače služby (IaaS). Šifrování disku lze použít jenom na virtuální počítače [podporovaných velikostí virtuálních počítačů a operačních systémů](disk-encryption-overview.md#supported-vms-and-operating-systems). Musíte také splnit následující předpoklady:

- [Další požadavky na virtuální chod](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Zásady vytváření sítí a skupin](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Požadavky na úložiště šifrovacího klíče](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Před šifrováním disků vytvořte [snímek](snapshot-copy-managed-disk.md), vytvořte zálohu nebo obojí. Zálohy zajistí, že možnost obnovení je možné, pokud dojde k neočekávané chybě během šifrování. Virtuální počítače se spravovanými disky vyžadují zálohu, než dojde k šifrování. Po vytvoření zálohy můžete pomocí rutiny Set-AzVMDiskEncryptionExtension šifrovat spravované disky zadáním parametru -skipVmBackup. Další informace o zálohování a obnovení šifrovaných virtuálních počítačů najdete v [tématu Zálohování Azure](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - Pokud jste dříve používali [Azure Disk Encryption s aplikací Azure AD](disk-encryption-overview-aad.md) k šifrování tohoto virtuálního počítače, musíte pokračovat v použití této možnosti k šifrování virtuálního počítače. Na tomto šifrovaném virtuálním počítači nelze použít [Azure Disk Encryption,](disk-encryption-overview.md) protože se nejedná o podporovaný scénář, což znamená, že přechod od aplikace Azure AD pro tento šifrovaný virtuální počítač ještě není podporovaný.
 > - Chcete-li se ujistit, že tajné kódy šifrování nepřekračují místní hranice, Azure Disk Encryption potřebuje trezor klíčů a virtuální počítače, které mají být umístěny společně ve stejné oblasti. Vytvořte a použijte trezor klíčů, který je ve stejné oblasti jako virtuální hod, který má být šifrován.
 > - Když šifrujete svazky operačního systému Linux, proces může trvat několik hodin. Je normální, že šifrovat svazky operačního systému Linux trvá déle než datové svazky.
> - Když šifrujete svazky operačního systému Linux, virtuální počítač by měl být považován za nedostupný. Důrazně doporučujeme vyhnout se přihlášení SSH, zatímco šifrování probíhá, aby se zabránilo blokování žádné otevřené soubory, které je třeba přistupovat během procesu šifrování. Chcete-li zkontrolovat průběh, použijte příkazy Zobrazit příkazy [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) nebo [vm šifrování.](/cli/azure/vm/encryption#az-vm-encryption-show) Můžete očekávat, že tento proces bude trvat několik hodin pro svazek operačního systému 30 GB a další čas pro šifrování datových svazků. Doba šifrování svazku dat je úměrná velikosti a množství datových svazků, pokud není použita možnost **šifrovacího formátu.** 
 > - Zakázání šifrování na virtuálních počítačích s Linuxem je podporované jenom pro datové svazky. Není podporovánna svazcích dat nebo operačního systému, pokud byl svazek operačního systému zašifrován. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"> </a> Povolení šifrování na existujícím nebo spuštěném virtuálním počítači IaaS Linux

V tomto scénáři můžete povolit šifrování pomocí šablony Azure Resource Manager, rutiny PowerShell nebo příkazy Azure CLI. 

>[!IMPORTANT]
 >Je povinné pořídit snímek nebo zálohovat instanci spravovaného virtuálního počítače na disku mimo a před povolením Azure Disk Encryption. Můžete pořídit snímek spravovaného disku z webu Azure Portal nebo můžete použít [Azure Backup](../../backup/backup-azure-vms-encryption.md). Zálohy zajišťují, že možnost obnovení je možná v případě neočekávaného selhání během šifrování. Po vytvoření zálohy použijte rutinu Set-AzVMDiskEncryptionExtension k šifrování spravovaných disků zadáním parametru -skipVmBackup. Příkaz Set-AzVMDiskEncryptionExtension se nezdaří u virtuálních počítačů založených na spravovaném disku, dokud není provedena záloha a tento parametr není zadán. 
>
>Šifrování nebo zakázání šifrování může způsobit restartování virtuálního počítače. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Povolení šifrování na existujícím nebo spuštěném virtuálním počítači s Linuxem pomocí příkazového příkazového příkazu Azure 
Šifrování disku na šifrovaném virtuálním pevném disku můžete povolit instalací a použitím nástroje příkazového řádku [Azure CLI 2.0.](/cli/azure) Můžete ho používat v prohlížeči pomocí služby [Azure Cloud Shell](../../cloud-shell/overview.md) nebo nainstalovat na místním počítači a používat ho v jakékoli relaci PowerShellu. Chcete-li povolit šifrování na stávajících nebo spuštěných virtuálních počítačích IaaS Linux v Azure, použijte následující příkazy příkazového příkazu:

Pomocí příkazu [povolit šifrování az vm](/cli/azure/vm/encryption#az-vm-encryption-enable) povolte šifrování na běžícím virtuálním počítači IaaS v Azure.

-  **Šifrování spuštěného virtuálního virtuálního aplikace pomocí tajného klíče klienta:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Šifrování spuštěného virtuálního virtuálního aplikace pomocí kek ulomit tajný klíč klienta:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > Syntaxe hodnoty parametru disk-encryption-keyvault je úplný řetězec identifikátoru: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br> Syntaxe hodnoty parametru key-encryption-key je úplný identifikátor URI pro KEK jako v: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

- **Ověřte, zda jsou disky zašifrovány:** Chcete-li zkontrolovat stav šifrování virtuálního počítače IaaS, použijte příkaz [az vm šifrování show.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Zakázat šifrování:** Chcete-li zakázat šifrování, použijte příkaz [zakázat šifrování az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) Zakázání šifrování je povoleno jenom na datových svazcích pro virtuální počítače s Linuxem.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"> </a> Povolení šifrování na existujícím nebo spuštěném virtuálním počítači s Linuxem pomocí PowerShellu
Pomocí rutiny [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) povolte šifrování na běžícím virtuálním počítači IaaS v Azure. Pořiďte [snímek](snapshot-copy-managed-disk.md) nebo vytvořte zálohu virtuálního počítače pomocí [azure backup](../../backup/backup-azure-vms-encryption.md) před zašifrováním disků. Parametr -skipVmBackup je již zadán ve skriptech prostředí PowerShell pro šifrování spuštěného virtuálního počítače s Linuxem.

- **Šifrování spuštěného virtuálního virtuálního aplikace pomocí tajného klíče klienta:** Následující skript inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. Skupina prostředků, virtuální počítač, trezor klíčů, aplikace Azure AD a tajný klíč klienta už měly být vytvořeny jako předpoklady. Nahraďte myvirtualmachineresourcegroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID a My-AAD-client-secret s vašimi hodnotami. Upravte parametr -VolumeType a určete, které disky šifrujete.

     ```azurepowershell
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $KVRGname = 'MyKeyVaultResourceGroup';
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Šifrování spuštěného virtuálního virtuálního aplikace pomocí kek ulomit tajný klíč klienta:** Azure Disk Encryption umožňuje zadat existující klíč v trezoru klíčů zabalit tajné kódy šifrování disku, které byly generovány při povolení šifrování. Když je zadán klíč šifrovací klíč, Azure Disk Encryption používá tento klíč k zabalení tajných kódů šifrování před zápisem do trezoru klíčů. Upravte parametr -VolumeType a určete, které disky šifrujete. 

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > Syntaxe hodnoty parametru disk-encryption-keyvault je úplný řetězec identifikátoru: /subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br>
  Syntaxe hodnoty parametru key-encryption-key je úplný identifikátor URI pro KEK jako v: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]. 
    
- **Ověřte, zda jsou disky zašifrovány:** Chcete-li zkontrolovat stav šifrování virtuálního počítače IaaS, použijte rutinu [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Zakázat šifrování disku:** Chcete-li zakázat šifrování, použijte rutinu [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Zakázání šifrování je povoleno jenom na datových svazcích pro virtuální počítače s Linuxem.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"> </a> Povolení šifrování na existujícím nebo spuštěném virtuálním počítači IaaS Linux pomocí šablony

Šifrování disku na existujícím nebo spuštěném virtuálním počítači IaaS Linux v Azure můžete povolit pomocí [šablony Správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. V šabloně rychlého startu Azure vyberte **Nasazení do Azure.**

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, parametry, právní podmínky a smlouvu. Vyberte **Vytvořit,** chcete-li povolit šifrování na existujícím nebo spuštěném virtuálním počítači IaaS.

V následující tabulce jsou uvedeny parametry šablony Správce prostředků pro existující nebo spuštěné virtuální počítače, které používají ID klienta Azure AD:

| Parametr | Popis |
| --- | --- |
| AADClientID | ID klienta aplikace Azure AD, která má oprávnění k zápisu tajných kódů do trezoru klíčů. |
| AADClientSecret | Tajný klíč klienta aplikace Azure AD, která má oprávnění k zápisu tajných kódů do trezoru klíčů. |
| keyVaultName | Název trezoru klíčů, do kterého by měl být klíč odeslán. Můžete ji získat pomocí příkazu `az keyvault show --name "MySecureVault" --query KVresourceGroup`Azure CLI . |
|  keyEncryptionKeyURL | Adresa URL šifrovacího klíče klíče, který se používá k šifrování generovaného klíče. Tento parametr je volitelný, pokud vyberete **nokek** v rozevíracím seznamu **UseExistingKek.** Pokud vyberete **kek** v rozevíracím seznamu **UseExistingKek,** musíte zadat hodnotu _keyEncryptionKeyURL._ |
| volumeType | Typ svazku, na které se provádí operace šifrování. Platné podporované hodnoty jsou _Operační systém_ nebo _Vše_. (Viz podporované distribuce Linuxu a jejich verze pro operační systém a datové disky v části požadavky dříve.) |
| sekvenceVerze | Sekvenční verze operace nástroje BitLocker. Přírůstek toto číslo verze pokaždé, když se provádí operace šifrování disku na stejném virtuálním počítači. |
| vmName | Název virtuálního počítače, na který má být provedena operace šifrování. |
| přístupové heslo | Jako šifrovací klíč dat zadejte silné přístupové heslo. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>Použití funkce EncryptFormatAll pro datové disky na virtuálních počítačích IaaS v Linuxu
Parametr EncryptFormatAll zkracuje dobu šifrování datových disků Linuxu. Oddíly, které splňují určitá kritéria, jsou formátovány (s jejich aktuálním systémem souborů). Pak jsou znovu namontováni tam, kde byli před popravou velení. Chcete-li vyloučit datový disk, který splňuje kritéria, můžete jej před spuštěním příkazu odpojit.

 Po spuštění tohoto příkazu jsou naformátovány všechny jednotky, které byly dříve připojeny. Vrstva šifrování se pak spustí nad nyní prázdnou jednotkou. Když je vybrána tato možnost, dočasný prostředek disk připojený k virtuálnímu počítače je také šifrována. Pokud se výplachová jednotka resetuje, přeformátuje se a znovu zašifruje pro virtuální počítač řešením Azure Disk Encryption při příští příležitosti.

>[!WARNING]
> EncryptFormatAll by se neměl používat, když jsou na datových svazcích virtuálního počítače potřebná data. Disky můžete vyloučit z šifrování jejich odpojením. Vyzkoušejte encryptformatall parametr na testovací ms nejprve pochopit parametr funkce a jeho důsledky před pokusem na produkčním virtuálním počítači. Možnost EncryptFormatAll formátuje datový disk, takže všechna data na něm budou ztracena. Než budete pokračovat, ověřte, zda jsou všechny disky, které chcete vyloučit, správně odpojeny. </br></br>
 >Pokud nastavíte tento parametr při aktualizaci nastavení šifrování, může to vést k restartování před skutečným šifrováním. V takovém případě chcete také odebrat disk, který nechcete formátovat ze souboru fstab. Podobně byste měli přidat oddíl, který chcete šifrovat, do souboru fstab před zahájením operace šifrování. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"> </a> EncryptFormatVšechna kritéria
Parametr prochází všechny oddíly a šifruje je tak dlouho, dokud splňují *všechna* následující kritéria: 
- Není kořenový/OS/spouštěcí oddíl
- Ještě není zašifrována
- Není svazek BEK
- Není svazek RAID
- Není svazek LVM
- Je namontován

Šifrujte disky, které tvoří svazek RAID nebo LVM, nikoli svazek RAID nebo LVM.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"> </a> Použití parametru EncryptFormatAll se šablonou
Chcete-li použít možnost EncryptFormatAll, použijte libovolnou již existující šablonu Správce prostředků Azure, která šifruje virtuální počítač s Linuxem, a změňte pole **EncryptionOperation** pro prostředek AzureDiskEncryption.

1. Jako příklad použijte [šablonu Správce prostředků k šifrování spuštěného virtuálního počítače IaaS systému Linux IaaS](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. V šabloně rychlého startu Azure vyberte **Nasazení do Azure.**
3. Změňte pole **EncryptionOperation** z **enableencryption** na **EnableEncryptionFormatAl**.
4. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, další parametry, právní podmínky a dohodu. Vyberte **Vytvořit,** chcete-li povolit šifrování na existujícím nebo spuštěném virtuálním počítači IaaS.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"> </a> Použití parametru EncryptFormatAll s rutinou prostředí PowerShell
Použijte rutinu [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) s parametrem EncryptFormatAll.

**Šifrování spuštěného virtuálního virtuálního aplikace pomocí tajného klíče klienta a EncryptFormatAll:** Následující skript například inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension s parametrem EncryptFormatAll. Skupina prostředků, virtuální počítač, trezor klíčů, aplikace Azure AD a tajný klíč klienta už měly být vytvořeny jako předpoklady. Nahraďte myKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID a My-AAD-client-secret s vašimi hodnotami.
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"> </a> Použití parametru EncryptFormatAll se Správcem logických svazků (LVM) 
Doporučujeme nastavení LVM-on-crypt. U všech následujících příkladů nahraďte cestu zařízení a přípojné body všemi, které vyhovují vašemu případu použití. Toto nastavení lze provést následujícím způsobem:

- Přidejte datové disky, které budou skládat virtuální počítače.
- Naformátujte, připojujte a přidejte tyto disky do souboru fstab.

    1. Naformátujte nově přidaný disk. Tady používáme symbolické odkazy generované Azure. Using symlinks avoids problems related to device names changing. Další informace naleznete [v tématu Poradce při potížích s názvy zařízení](troubleshoot-device-names-problems.md).
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. Připojte disky.
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. Přidat do fstab.
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. Spusťte rutinu Set-AzVMDiskEncryptionExtension PowerShell s -EncryptFormatAll k šifrování těchto disků.
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. Nastavte LVM nad těmito novými disky. Všimněte si, že šifrované jednotky jsou odemčené po dokončení zavádění virtuálního zařízení. Takže montáž LVM bude muset být také následně zpožděna.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a> Nové virtuální typy IaaS vytvořené ze zákazníkem šifrovaného virtuálního pevného disku a šifrovacích klíčů
V tomto scénáři můžete povolit šifrování pomocí šablony Správce prostředků, rutin prostředí PowerShell nebo příkazů příkazu CLI. V následujících částech podrobněji vysvětlete šablonu správce prostředků a příkazy příkazu příkazu příkazu příkazu příkazu příkazu příkazu. 

Pomocí pokynů v dodatku pro přípravu předem zašifrované image, které lze použít v Azure. Po vytvoření image můžete pomocí kroků v další části vytvořit šifrovaný virtuální počítač Azure.

* [Příprava předem šifrovaného virtuálního pevného disku systému Linux](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Je povinné pořídit snímek nebo zálohovat instanci spravovaného virtuálního počítače na disku mimo a před povolením Azure Disk Encryption. Můžete pořídit snímek spravovaného disku z portálu nebo můžete použít [Azure Backup](../../backup/backup-azure-vms-encryption.md). Zálohy zajišťují, že možnost obnovení je možná v případě neočekávaného selhání během šifrování. Po vytvoření zálohy použijte rutinu Set-AzVMDiskEncryptionExtension k šifrování spravovaných disků zadáním parametru -skipVmBackup. Příkaz Set-AzVMDiskEncryptionExtension se nezdaří u virtuálních počítačů založených na spravovaném disku, dokud není provedena záloha a tento parametr není zadán. 
>
>Šifrování nebo zakázání šifrování může způsobit restartování virtuálního počítače.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"> </a> Šifrování virtuálních počítačů IaaS pomocí předem šifrovaných virtuálních disponiálů pomocí Azure PowerShellu 
Šifrování disku v zašifrovaném virtuálním pevném disku můžete povolit pomocí rutiny PowerShell [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). Následující příklad poskytuje některé běžné parametry. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Povolení šifrování na nově přidaném datovém disku
Nový datový disk můžete přidat pomocí [připojení disku az vm](add-disk.md) nebo [prostřednictvím portálu Azure](attach-disk-portal.md). Před šifrováním je třeba nejprve připojit nově připojený datový disk. Musíte požádat o šifrování datové jednotky, protože jednotka bude během šifrování nepoužitelná. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Povolení šifrování na nově přidaném disku pomocí příkazového příkazového příkazu k řešení Azure
 Pokud byl virtuální virtuální byl dříve zašifrován s "Vše", pak --parametr typu svazku by měl zůstat Všechny. Všechny zahrnují operační systém i datové disky. Pokud byl virtuální modul dříve zašifrován pomocí typu svazku "OS", měl by být parametr --volume-type změněn na Vše, aby byl zahrnut operační systém i nový datový disk. Pokud byl virtuální virtuální ms šifrován pouze s typem svazku "Data", může zůstat data, jak je ukázáno zde. Přidání a připojení nového datového disku k virtuálnímu počítače není dostatečná příprava na šifrování. Nově připojený disk musí být také formátován a správně připojen v rámci virtuálního počítače před povolením šifrování. Na Linuxu musí být disk připojen v /etc/fstab s [trvalým názvem blokového zařízení](troubleshoot-device-names-problems.md). 

Na rozdíl od syntaxe prostředí Powershell nevyžaduje rozhraní řízení o registraci, abyste při povolení šifrování poskytli jedinečnou sekvenční verzi. ClI automaticky generuje a používá vlastní hodnotu jedinečné sekvence verze.

-  **Šifrování spuštěného virtuálního virtuálního aplikace pomocí tajného klíče klienta:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Šifrování spuštěného virtuálního virtuálního aplikace pomocí kek ulomit tajný klíč klienta:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Povolení šifrování na nově přidaném disku pomocí Azure PowerShellu
 Při použití powershellu k šifrování nového disku pro Linux je třeba zadat novou sekvenční verzi. Sekvenční verze musí být jedinečná. Následující skript generuje identifikátor GUID pro sekvenční verzi. 
 

-  **Šifrování spuštěného virtuálního virtuálního aplikace pomocí tajného klíče klienta:** Následující skript inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. Skupina prostředků, virtuální počítač, trezor klíčů, aplikace Azure AD a tajný klíč klienta už měly být vytvořeny jako předpoklady. Nahraďte myvirtualmachineresourcegroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID a My-AAD-client-secret s vašimi hodnotami. Parametr -VolumeType je nastaven na datové disky a nikoli na disk operačního systému. Pokud byl virtuální modul dříve zašifrován pomocí typu svazku "OS" nebo "All", měl by být parametr -VolumeType změněn na Vše, aby byl zahrnut operační systém i nový datový disk.

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup'; 
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **Šifrování spuštěného virtuálního virtuálního aplikace pomocí kek ulomit tajný klíč klienta:** Azure Disk Encryption umožňuje zadat existující klíč v trezoru klíčů zabalit tajné kódy šifrování disku, které byly generovány při povolení šifrování. Když je zadán klíč šifrovací klíč, Azure Disk Encryption používá tento klíč k zabalení tajných kódů šifrování před zápisem do trezoru klíčů. Parametr -VolumeType je nastaven na datové disky a nikoli na disk operačního systému. Pokud byl virtuální modul dříve zašifrován pomocí typu svazku "OS" nebo "All", měl by být parametr -VolumeType změněn na Vše, aby byl zahrnut operační systém i nový datový disk.

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $vmName = 'MyExtraSecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> Syntaxe hodnoty parametru disk-encryption-keyvault je úplný řetězec identifikátoru: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]. </br> </br>
Syntaxe hodnoty parametru key-encryption-key je úplný identifikátor URI pro KEK jako v: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

## <a name="disable-encryption-for-linux-vms"></a>Zakázání šifrování pro virtuální počítače s Linuxem
Šifrování můžete zakázat pomocí Azure PowerShellu, Azure CLI nebo šablony Správce prostředků. 

>[!IMPORTANT]
>Zakázání šifrování pomocí Azure Disk Encryption na virtuálních počítačích S IP je podporované jenom pro datové svazky. Není podporovánna svazcích dat nebo operačního systému, pokud byl svazek operačního systému zašifrován. 

- **Zakázání šifrování disku pomocí Azure PowerShellu:** Chcete-li zakázat šifrování, použijte rutinu [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Zakažte šifrování pomocí azure cli:** Chcete-li zakázat šifrování, použijte příkaz [zakázat šifrování az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Zakázání šifrování pomocí šablony Správce prostředků:** Chcete-li zakázat šifrování, použijte [zakázat šifrování na spuštěné šabloně virtuálního počítače s Linuxem.](https://aka.ms/decrypt-linuxvm)
     1. Vyberte **Nasazení do Azure**.
     2. Vyberte předplatné, skupinu prostředků, umístění, virtuální hod, právní podmínky a smlouvu.
     3. Vyberte **Koupit,** chcete-li zakázat šifrování disku na spuštěném virtuálním počítači se systémem Windows. 


## <a name="next-steps"></a>Další kroky

- [Přehled šifrování disku Azure pro Linux](disk-encryption-overview-aad.md)
- [Vytvoření a konfigurace trezoru klíčů pro šifrování disku Azure pomocí Azure AD (předchozí verze)](disk-encryption-key-vault-aad.md)
