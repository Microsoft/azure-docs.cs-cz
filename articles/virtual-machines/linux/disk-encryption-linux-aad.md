---
title: Azure Disk Encryption s virtuálními počítači s Aplikace Azure AD Linux IaaS (předchozí verze)
description: Tento článek poskytuje pokyny k povolení Microsoft Azureho šifrování disku pro virtuální počítače s IaaS Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: c8228086eb67478d80aa041004e0da3eed71f896
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92741804"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Povolení Azure Disk Encryption s Azure AD na virtuálních počítačích se systémem Linux (předchozí verze)

Nová vydaná verze Azure Disk Encryption eliminuje požadavek na poskytnutí parametru aplikace Azure Active Directory (Azure AD) pro povolení šifrování disku virtuálního počítače. V nové verzi už nebudete muset zadávat přihlašovací údaje Azure AD během kroku povolení šifrování. Všechny nové virtuální počítače musí být zašifrované bez parametrů aplikace Azure AD pomocí nové vydané verze. Pokyny k povolení šifrování disku virtuálního počítače pomocí nové verze najdete v tématu [Azure Disk Encryption pro virtuální počítače se systémem Linux](disk-encryption-linux.md). Virtuální počítače, které jsou už šifrované pomocí parametrů aplikace Azure AD, se pořád podporují a měly by se udržovat dál se syntaxí AAD.

Můžete povolit mnoho scénářů šifrování disku a postup se může lišit v závislosti na scénáři. Následující oddíly popisují scénáře podrobněji podrobněji pro virtuální počítače s infrastrukturou pro Linux jako službu (IaaS). Diskové šifrování můžete použít jenom pro virtuální počítače s [podporovanými velikostmi virtuálních počítačů a operačními systémy](disk-encryption-overview.md#supported-vms-and-operating-systems). Musíte splňovat i následující požadavky:

- [Další požadavky na virtuální počítače](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Sítě a Zásady skupiny](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Požadavky na úložiště šifrovacího klíče](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Před šifrováním [disků si poznamenejte, vytvořte](snapshot-copy-managed-disk.md)zálohu nebo obojí. Zálohování zajišťuje možnost obnovení, pokud během šifrování dojde k neočekávané chybě. Virtuální počítače se spravovanými disky vyžadují zálohování před tím, než dojde k šifrování. Po provedení zálohy můžete použít rutinu Set-AzVMDiskEncryptionExtension k šifrování spravovaných disků zadáním parametru-skipVmBackup. Další informace o zálohování a obnovení šifrovaných virtuálních počítačů najdete v tématu [Azure Backup](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - Pokud jste dřív použili [Azure Disk Encryption s aplikací Azure AD](disk-encryption-overview-aad.md) k šifrování tohoto virtuálního počítače, musíte tuto možnost použít k ZAšifrování virtuálního počítače. V tomto šifrovaném virtuálním počítači nemůžete použít [Azure Disk Encryption](disk-encryption-overview.md) , protože to není podporovaný scénář, což znamená, že se zatím nepodporují přepnutí z aplikace Azure AD pro tento zašifrovaný virtuální počítač.
 > - Abyste se ujistili, že šifrovací tajná klíče nejsou mezi regionálními hranicemi, Azure Disk Encryption potřebuje Trezor klíčů a virtuální počítače, které se mají umístit do stejné oblasti. Vytvořte a použijte Trezor klíčů, který je ve stejné oblasti jako virtuální počítač, který se má šifrovat.
 > - Když zašifrujete svazky operačního systému Linux, proces může trvat několik hodin. Pro svazky operačního systému Linux je běžné, že k šifrování trvá déle než objemy dat.
> - Při šifrování svazků operačního systému Linux by se měl virtuální počítač považovat za nedostupný. Důrazně doporučujeme, abyste se vyhnuli přihlášením přes SSH, zatímco probíhá šifrování, aby nedošlo k blokování otevřených souborů, ke kterým je potřeba během procesu šifrování dostat. Ke kontrole průběhu použijte příkazy pro [zobrazení šifrování virtuálního počítače](/cli/azure/vm/encryption#az-vm-encryption-show) [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) nebo VM. Můžete očekávat, že tento proces bude trvat několik hodin na 30 GB svazku s operačním systémem a dodatečnou dobu pro šifrování datových svazků. Doba šifrování datového svazku je úměrná velikosti a množství datových svazků, pokud se nepoužije možnost **Šifrovat formát All** . 
 > - Zakázání šifrování u virtuálních počítačů se systémem Linux je podporováno pouze pro datové svazky. Pokud je svazek s operačním systémem zašifrovaný, není podpora na svazcích dat nebo operačních systémů podporovaná. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"> </a> Povolení šifrování na stávajícím nebo BĚŽÍCÍm virtuálním počítači s IaaS Linux

V tomto scénáři můžete šifrování povolit pomocí šablony Azure Resource Manager, rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku Azure CLI. 

>[!IMPORTANT]
 >Je nutné pořídit snímek nebo zálohovat instanci virtuálního počítače na bázi spravovaného disku mimo rámec a před povolením Azure Disk Encryption. Můžete si pořídit snímek spravovaného disku z Azure Portal, nebo můžete použít [Azure Backup](../../backup/backup-azure-vms-encryption.md). Zálohy zajišťují, že možnost obnovení je možné v případě jakékoli neočekávané chyby při šifrování. Po provedení zálohy pomocí rutiny Set-AzVMDiskEncryptionExtension Zašifrujte spravované disky zadáním parametru-skipVmBackup. Příkaz Set-AzVMDiskEncryptionExtension se u virtuálních počítačů založených na discích nespustí, dokud se neprovede zálohování a tento parametr není zadaný. 
>
>Šifrování nebo zakázání šifrování může způsobit, že se virtuální počítač restartuje. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Povolení šifrování na stávajícím nebo BĚŽÍCÍm virtuálním počítači se systémem Linux pomocí rozhraní příkazového řádku Azure 
Pomocí nástroje příkazového řádku [Azure CLI 2,0](/cli/azure) můžete povolit šifrování disku na zašifrovaném virtuálním pevném disku. Můžete ho používat v prohlížeči pomocí služby [Azure Cloud Shell](../../cloud-shell/overview.md) nebo nainstalovat na místním počítači a používat ho v jakékoli relaci PowerShellu. Pokud chcete povolit šifrování pro existující nebo běžící virtuální počítače s IaaS Linux v Azure, použijte následující příkazy rozhraní příkazového řádku:

Pomocí příkazu [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) Povolte šifrování na běžícím virtuálním počítači s IaaS v Azure.

-  **Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Zabalení spuštěného virtuálního počítače pomocí KEK k zabalení tajného klíče klienta:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > Syntaxe hodnoty parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:/subscriptions/[ID předplatného-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name].</br> </br> Syntaxe pro hodnotu parametru klíč-šifrovací klíč je úplný identifikátor URI pro KEK, jako v: https://[klíč trezor-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID].

- **Ověřte, že jsou disky šifrované:** Pokud chcete zjistit stav šifrování virtuálního počítače s IaaS, použijte příkaz [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Zakázat šifrování:** Pokud chcete šifrování zakázat, použijte příkaz [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Zakázání šifrování je povolené jenom u datových svazků pro virtuální počítače se systémem Linux.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"> </a> Povolení šifrování na stávajícím nebo běžícím virtuálním počítači se systémem Linux pomocí prostředí PowerShell
Pomocí rutiny [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) Povolte šifrování na běžícím virtuálním počítači s IaaS v Azure. Pořídit [snímek](snapshot-copy-managed-disk.md) nebo vytvořit zálohu virtuálního počítače s [Azure Backup](../../backup/backup-azure-vms-encryption.md) , než budou disky zašifrované. Parametr-skipVmBackup je už ve skriptech PowerShellu určený k zašifrování běžícího virtuálního počítače se systémem Linux.

- **Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta:** Následující skript inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. U skupiny prostředků, virtuálního počítače, trezoru klíčů, aplikace Azure AD a tajného klíče klienta by se už měly vytvořit požadavky. Hodnoty MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, my-AAD-Client-ID a my-AAD-Client-Secret nahraďte hodnotami. Pokud chcete určit, které disky se šifrují, upravte parametr-VolumeType.

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
- **Zabalení spuštěného virtuálního počítače pomocí KEK k zabalení tajného klíče klienta:** Azure Disk Encryption umožňuje zadat existující klíč do trezoru klíčů pro zabalení tajných klíčů disku, které byly generovány při povolování šifrování. Když je zadaný klíč šifrování klíče, Azure Disk Encryption používá tento klíč k zabalení šifrovacích tajných kódů před zápisem do trezoru klíčů. Pokud chcete určit, které disky se šifrují, upravte parametr-VolumeType. 

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
  > Syntaxe hodnoty parametru Disk-Encryption-Trezor klíčů je úplný řetězec identifikátoru:/subscriptions/[ID předplatného--GUID]/resourceGroups/[KVresource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name].</br> </br>
  Syntaxe pro hodnotu parametru klíč-šifrovací klíč je úplný identifikátor URI pro KEK, jako v: https://[klíč trezor-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID]. 
    
- **Ověřte, že jsou disky šifrované:** Pokud chcete zjistit stav šifrování virtuálního počítače s IaaS, použijte rutinu [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Zakázat šifrování disku:** Pokud chcete šifrování zakázat, použijte rutinu [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Zakázání šifrování je povolené jenom u datových svazků pro virtuální počítače se systémem Linux.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"> </a> Povolení šifrování na stávajícím nebo BĚŽÍCÍm virtuálním počítači s IaaS Linux se šablonou

Pomocí [šablony Správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)můžete povolit šifrování disku na existujícím nebo běžícím virtuálním počítači s IaaS Linux v Azure.

1. V šabloně pro rychlý Start Azure vyberte **nasadit do Azure** .

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, parametry, právní smlouvy a smlouvu. Vyberte **vytvořit** , pokud chcete povolit šifrování na stávajícím nebo SPUŠTĚNém virtuálním počítači s IaaS.

Následující tabulka uvádí Správce prostředků parametry šablony pro existující nebo běžící virtuální počítače, které používají ID klienta Azure AD:

| Parametr | Popis |
| --- | --- |
| AADClientID | ID klienta aplikace Azure AD, která má oprávnění k zápisu tajných kódů do trezoru klíčů. |
| AADClientSecret | Tajný kód klienta aplikace Azure AD, který má oprávnění k zápisu tajných kódů do trezoru klíčů. |
| keyVaultName | Název trezoru klíčů, do kterého se má klíč nahrát Můžete ji získat pomocí příkazu rozhraní příkazového řádku Azure CLI `az keyvault show --name "MySecureVault" --query KVresourceGroup` . |
|  keyEncryptionKeyURL | Adresa URL klíčového šifrovacího klíče, který se používá k zašifrování vygenerovaného klíče. Tento parametr je nepovinný, pokud v rozevíracím seznamu **UseExistingKek** vyberete **nokek** . Pokud v rozevíracím seznamu **UseExistingKek** vyberete možnost **KEK** , musíte zadat hodnotu _keyEncryptionKeyURL_ . |
| volumeType | Typ svazku, na kterém se operace šifrování provádí. Platné podporované hodnoty jsou _OS_ nebo _All_ . (Další informace najdete v části Podporované distribuce systému Linux a jejich verze pro operační systémy a datové disky v oddílu požadavky dříve.) |
| sequenceVersion | Verze sekvence operace nástroje BitLocker Zvyšte číslo této verze pokaždé, když se na stejném virtuálním počítači provede operace šifrování disku. |
| vmName | Název virtuálního počítače, na kterém se má operace šifrování provést. |
| přístupové heslo | Jako šifrovací klíč dat zadejte silné heslo. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>Použití funkce EncryptFormatAll pro datové disky na virtuálních počítačích se systémem Linux IaaS
Parametr EncryptFormatAll zkracuje dobu, po kterou jsou datové disky platformy Linux šifrovány. Oddíly, které splňují určitá kritéria, jsou formátovány (s jejich aktuálním systémem souborů). Pak se znovu připojí k umístění, kde byly před provedením příkazu. Pokud chcete vyloučit datový disk, který splňuje kritéria, můžete ho odpojit před spuštěním příkazu.

 Po spuštění tohoto příkazu se naformátují všechny dříve připojené jednotky. Pak se vrstva šifrování spustí v horní části jednotky, která je právě prázdná. Když je vybraná tato možnost, bude taky zašifrovaný dočasný disk připojený k virtuálnímu počítači. Pokud dojde k resetování dočasné jednotky, přeformátujte a znovu Zašifrujte pro virtuální počítač pomocí řešení Azure Disk Encryption při další příležitosti.

>[!WARNING]
> EncryptFormatAll by neměl být použit, pokud jsou potřebná data na datových svazcích virtuálního počítače. Můžete vyloučit disky ze šifrování odpojováním. Vyzkoušejte nejprve parametr EncryptFormatAll na testovacím virtuálním počítači, abyste pochopili parametr funkce a jeho nevýznam před tím, než ho zkusíte na provozním virtuálním počítači. Možnost EncryptFormatAll formátuje datový disk, takže všechna data, která na něm jsou, budou ztracena. Než budete pokračovat, ověřte, že všechny disky, které chcete vyloučit, jsou správně odpojeny. </br></br>
 >Pokud tento parametr nastavíte při aktualizaci nastavení šifrování, může to vést k restartování před samotným šifrováním. V takovém případě budete chtít odebrat i disk, který nechcete naformátovat ze souboru fstab. Podobně byste měli před zahájením operace šifrování přidat do souboru fstab oddíl, který chcete šifrovat. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"> </a> EncryptFormatAll kritéria
Parametr projde všemi oddíly a zašifruje je tak dlouho, dokud splňují *všechna* následující kritéria: 
- Není kořenovým adresářem/operačním systémem nebo spouštěcím oddílem.
- Ještě není zašifrováno
- Není klíče bek svazek.
- Není svazek RAID.
- Není LVM svazek.
- Je připojeno

Zašifrujte disky, které tvoří svazek RAID nebo LVM, a ne svazek RAID nebo LVM.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"> </a> Použití parametru EncryptFormatAll se šablonou
Pokud chcete použít možnost EncryptFormatAll, použijte libovolnou existující šablonu Azure Resource Manager, která šifruje virtuální počítač Linux a změňte pole **EncryptionOperation** pro prostředek AzureDiskEncryption.

1. Jako příklad použijte [šablonu správce prostředků k šifrování spuštěného virtuálního počítače se systémem Linux IaaS](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. V šabloně pro rychlý Start Azure vyberte **nasadit do Azure** .
3. Změňte pole **EncryptionOperation** z **EnableEncryption** na **EnableEncryptionFormatAl** .
4. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, další parametry, právní výrazy a smlouvy. Vyberte **vytvořit** , pokud chcete povolit šifrování na stávajícím nebo SPUŠTĚNém virtuálním počítači s IaaS.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"> </a> Použití parametru EncryptFormatAll s rutinou prostředí PowerShell
Použijte rutinu [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) s parametrem EncryptFormatAll.

**Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta a EncryptFormatAll:** Například následující skript inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension s parametrem EncryptFormatAll. U skupiny prostředků, virtuálního počítače, trezoru klíčů, aplikace Azure AD a tajného klíče klienta by se už měly vytvořit požadavky. Hodnoty MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, my-AAD-Client-ID a my-AAD-Client-Secret nahraďte hodnotami.
  
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


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"> </a> Použití parametru EncryptFormatAll u Správce logických svazků (LVM) 
Doporučujeme LVM instalaci. Pro všechny následující příklady nahraďte zařízení-Path a mountpoints libovolnými vašimi vlastními případy použití. Tuto instalaci můžete provést takto:

- Přidejte datové disky, které budou tvořit virtuální počítač.
- Naformátujte, připojte a přidejte tyto disky do souboru fstab.

    1. Naformátujte nově přidaný disk. Symbolických odkazů vygenerované v Azure používáme tady. Použití symbolických odkazů zabraňuje problémům souvisejícím se změnou názvů zařízení. Další informace najdete v tématu [řešení potíží s názvy zařízení](../troubleshooting/troubleshoot-device-names-problems.md).
    
        ```console
        mkfs -t ext4 /dev/disk/azure/scsi1/lun0
        ```

    2. Připojte disky.

        ```console
        mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint
        ```

    3. Přidejte do fstab.

        ```console
        echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab
        ```

    4. Spusťte rutinu Set-AzVMDiskEncryptionExtension PowerShellu s-EncryptFormatAll a Zašifrujte tyto disky.

       ```azurepowershell-interactive
        Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
        ```

    5. Nastavte LVM na těchto nových discích. Poznámka: šifrované jednotky jsou po dokončení spuštění virtuálního počítače odemčeny. Proto bude nutné připojení LVM také později zpozdit.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a> Nové virtuální počítače s IaaS vytvořené z VHD a šifrovacích klíčů šifrovaných zákazníkem
V tomto scénáři můžete povolit šifrování pomocí Správce prostředků šablony, rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku. Následující části podrobněji popisují Správce prostředků šablon a příkazů rozhraní příkazového řádku. 

Použijte pokyny v příloze pro přípravu předem šifrovaných imagí, které je možné použít v Azure. Po vytvoření image můžete pomocí kroků v následující části vytvořit zašifrovaný virtuální počítač Azure.

* [Příprava předem zašifrovaného virtuálního pevného disku se systémem Linux](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Je nutné pořídit snímek nebo zálohovat instanci virtuálního počítače na bázi spravovaného disku mimo rámec a před povolením Azure Disk Encryption. Můžete si pořídit snímek spravovaného disku z portálu nebo můžete použít [Azure Backup](../../backup/backup-azure-vms-encryption.md). Zálohy zajišťují, že možnost obnovení je možné v případě jakékoli neočekávané chyby při šifrování. Po provedení zálohy pomocí rutiny Set-AzVMDiskEncryptionExtension Zašifrujte spravované disky zadáním parametru-skipVmBackup. Příkaz Set-AzVMDiskEncryptionExtension se u virtuálních počítačů založených na discích nespustí, dokud se neprovede zálohování a tento parametr není zadaný. 
>
>Šifrování nebo zakázání šifrování může způsobit, že se virtuální počítač restartuje.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"> </a> Použití Azure PowerShell k šifrování virtuálních počítačů s IaaS pomocí předem šifrovaných virtuálních pevných disků 
Pomocí rutiny PowerShellu [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples)můžete povolit šifrování disku na šifrovaném virtuálním pevném disku. V následujícím příkladu jsou uvedeny některé běžné parametry. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Povolení šifrování u nově přidaného datového disku
Nový datový disk můžete přidat pomocí příkazem [AZ VM disk Attach](add-disk.md) nebo [prostřednictvím Azure Portal](attach-disk-portal.md). Než budete moct šifrování, musíte nejdřív připojit nově připojený datový disk. Musíte požádat o šifrování datové jednotky, protože jednotku nebude možné použít, když probíhá šifrování. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Povolení šifrování u nově přidaného disku pomocí Azure CLI
 Pokud byl virtuální počítač dříve zašifrován pomocí "All", parametr--Volume-Type by měl zůstat všechny. Vše zahrnuje operační systém i datové disky. Pokud byl virtuální počítač dříve zašifrován pomocí typu svazku "OS", pak by měl být parametr--Volume-Type změněn na hodnotu All, aby byl operační systém i nový datový disk zahrnut. Pokud byl virtuální počítač zašifrovaný pouze s typem svazku "data", pak může zůstat data, jak je znázorněno zde. Přidání a připojení nového datového disku k virtuálnímu počítači nemá dostatečnou přípravu na šifrování. Předtím, než povolíte šifrování, musí být nově připojený disk ve virtuálním počítači naformátovaný a správně připojený. V systému Linux musí být disk připojen v/etc/fstab s [názvem trvalého blokování zařízení](../troubleshooting/troubleshoot-device-names-problems.md). 

Na rozdíl od syntaxe PowerShellu rozhraní příkazového řádku nevyžaduje, abyste při povolování šifrování zadali jedinečnou verzi sekvence. Rozhraní příkazového řádku automaticky vygeneruje a použije svou vlastní jedinečnou hodnotu verze sekvence.

-  **Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Zabalení spuštěného virtuálního počítače pomocí KEK k zabalení tajného klíče klienta:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Povolit šifrování na nově přidaném disku s Azure PowerShell
 Když použijete PowerShell k šifrování nového disku pro Linux, je nutné zadat novou verzi sekvence. Verze sekvence musí být jedinečná. Následující skript vygeneruje identifikátor GUID pro verzi sekvence. 
 

-  **Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta:** Následující skript inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. U skupiny prostředků, virtuálního počítače, trezoru klíčů, aplikace Azure AD a tajného klíče klienta by se už měly vytvořit požadavky. Hodnoty MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, my-AAD-Client-ID a my-AAD-Client-Secret nahraďte hodnotami. Parametr-VolumeType je nastaven na datové disky, nikoli na disk s operačním systémem. Pokud byl virtuální počítač předtím zašifrovaný pomocí typu svazku "OS" nebo "All", měl by být parametr-VolumeType změněn na hodnotu All, aby byl operační systém i nový datový disk zahrnut.

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
- **Zabalení spuštěného virtuálního počítače pomocí KEK k zabalení tajného klíče klienta:** Azure Disk Encryption umožňuje zadat existující klíč do trezoru klíčů pro zabalení tajných klíčů disku, které byly generovány při povolování šifrování. Když je zadaný klíč šifrování klíče, Azure Disk Encryption používá tento klíč k zabalení šifrovacích tajných kódů před zápisem do trezoru klíčů. Parametr-VolumeType je nastaven na datové disky, nikoli na disk s operačním systémem. Pokud byl virtuální počítač předtím zašifrovaný pomocí typu svazku "OS" nebo "All", měl by být parametr-VolumeType změněn na hodnotu All, aby byl operační systém i nový datový disk zahrnut.

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
> Syntaxe hodnoty parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:/subscriptions/[ID předplatného-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name]. </br> </br>
Syntaxe pro hodnotu parametru klíč-šifrovací klíč je úplný identifikátor URI pro KEK, jako v: https://[klíč trezor-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID].

## <a name="disable-encryption-for-linux-vms"></a>Zakázat šifrování pro virtuální počítače se systémem Linux
Šifrování můžete zakázat pomocí Azure PowerShell, rozhraní příkazového řádku Azure nebo šablony Správce prostředků. 

>[!IMPORTANT]
>Zakázání šifrování pomocí Azure Disk Encryption na virtuálních počítačích se systémem Linux je podporováno pouze pro datové svazky. Pokud je svazek s operačním systémem zašifrovaný, není podpora na svazcích dat nebo operačních systémů podporovaná. 

- **Zakázat šifrování disku pomocí Azure PowerShell:** Pokud chcete šifrování zakázat, použijte rutinu [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Zakázat šifrování pomocí Azure CLI:** Pokud chcete šifrování zakázat, použijte příkaz [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Zakázat šifrování pomocí šablony Správce prostředků:** Šifrování zakážete tak, že použijete [zakázat šifrování v šabloně virtuálního počítače se systémem Linux](https://aka.ms/decrypt-linuxvm) .
     1. Vyberte **nasadit do Azure** .
     2. Vyberte předplatné, skupinu prostředků, umístění, virtuální počítač, právní podmínku a smlouvu.
     3. Pokud chcete zakázat šifrování disku na běžícím virtuálním počítači s Windows, vyberte **koupit** . 


## <a name="next-steps"></a>Další kroky

- [Přehled Azure Disk Encryption pro Linux](disk-encryption-overview-aad.md)
- [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption s využitím Azure AD (předchozí verze)](disk-encryption-key-vault-aad.md)
