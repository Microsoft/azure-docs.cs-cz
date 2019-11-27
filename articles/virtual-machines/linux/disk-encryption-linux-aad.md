---
title: Azure Disk Encryption pomocí Azure AD App Linuxem v režimu IaaS virtuálních počítačů (předchozí verze)
description: Tento článek obsahuje pokyny týkající se povolení Microsoft Azure Disk Encryption pro virtuální počítače IaaS s Linuxem.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: d41f2138a453e4a34354c10bbebad41724a18d1d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457481"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Povolení Azure Disk Encryption s Azure AD na virtuálních počítačích se systémem Linux (předchozí verze)

Nová vydaná verze Azure Disk Encryption eliminuje požadavek na poskytnutí parametru aplikace Azure Active Directory (Azure AD) pro povolení šifrování disku virtuálního počítače. V nové verzi už nebudete muset zadávat přihlašovací údaje Azure AD během kroku povolení šifrování. Všechny nové virtuální počítače musí být zašifrované bez parametrů aplikace Azure AD pomocí nové vydané verze. Pokyny k povolení šifrování disku virtuálního počítače pomocí nové verze najdete v tématu [Azure Disk Encryption pro virtuální počítače se systémem Linux](disk-encryption-linux.md). Virtuální počítače, které jsou už šifrované pomocí parametrů aplikace Azure AD, se pořád podporují a měly by se udržovat dál se syntaxí AAD.

Můžete povolit mnoho scénářů šifrování disku a postup se může lišit v závislosti na scénáři. Následující oddíly popisují scénáře podrobněji podrobněji pro virtuální počítače s infrastrukturou pro Linux jako službu (IaaS). Diskové šifrování můžete použít jenom pro virtuální počítače s [podporovanými velikostmi virtuálních počítačů a operačními systémy](disk-encryption-overview.md#supported-vms-and-operating-systems). Musíte splňovat i následující požadavky:

- [Další požadavky na virtuální počítače](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Sítě a Zásady skupiny](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Požadavky na úložiště šifrovacího klíče](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Před šifrováním [disků si poznamenejte, vytvořte](snapshot-copy-managed-disk.md)zálohu nebo obojí. Zálohy Ujistěte se, že možnost obnovení je možné, pokud dojde k neočekávané chybě při šifrování. Virtuální počítače se spravovanými disky vyžadují zálohu, než dojde k šifrování. Po provedení zálohy můžete použít rutinu Set-AzVMDiskEncryptionExtension k šifrování spravovaných disků zadáním parametru-skipVmBackup. Další informace o zálohování a obnovení šifrovaných virtuálních počítačů najdete v tématu [Azure Backup](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - Pokud jste dřív použili [Azure Disk Encryption s aplikací Azure AD](disk-encryption-overview-aad.md) k šifrování tohoto virtuálního počítače, musíte tuto možnost použít k ZAšifrování virtuálního počítače. V tomto šifrovaném virtuálním počítači nemůžete použít [Azure Disk Encryption](disk-encryption-overview.md) , protože to není podporovaný scénář, což znamená, že se zatím nepodporují přepnutí z aplikace Azure AD pro tento zašifrovaný virtuální počítač.
 > - Abyste se ujistili, že šifrovací tajná klíče nejsou mezi regionálními hranicemi, Azure Disk Encryption potřebuje Trezor klíčů a virtuální počítače, které se mají umístit do stejné oblasti. Vytvořte a použijte Trezor klíčů, který je ve stejné oblasti jako virtuální počítač, který se má šifrovat.
 > - Když zašifrujete svazky operačního systému Linux, proces může trvat několik hodin. Pro svazky operačního systému Linux je běžné, že k šifrování trvá déle než objemy dat.
> - Při šifrování svazků operačního systému Linux by se měl virtuální počítač považovat za nedostupný. Důrazně doporučujeme, abyste se vyhnuli přihlášením přes SSH, zatímco probíhá šifrování, aby nedošlo k blokování otevřených souborů, ke kterým je potřeba během procesu šifrování dostat. Ke kontrole průběhu použijte příkazy pro [zobrazení šifrování virtuálního počítače](/cli/azure/vm/encryption#az-vm-encryption-show) [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) nebo VM. Můžete očekávat, že tento proces bude trvat několik hodin na 30 GB svazku s operačním systémem a dodatečnou dobu pro šifrování datových svazků. Doba šifrování datového svazku je úměrná velikosti a množství datových svazků, pokud se nepoužije možnost **Šifrovat formát All** . 
 > - Zakázáním šifrování na virtuální počítače s Linuxem je podporována pouze pro datové svazky. Pokud je svazek s operačním systémem zašifrovaný, není podpora na svazcích dat nebo operačních systémů podporovaná. 

 

## <a name="bkmk_RunningLinux"></a> Povolení šifrování na stávajícím nebo BĚŽÍCÍm virtuálním počítači s IaaS Linux

V tomto scénáři můžete šifrování povolit pomocí šablony Azure Resource Manager, rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku Azure CLI. 

>[!IMPORTANT]
 >Je nutné pořídit snímek nebo zálohovat instanci virtuálního počítače na bázi spravovaného disku mimo rámec a před povolením Azure Disk Encryption. Můžete si pořídit snímek spravovaného disku z Azure Portal, nebo můžete použít [Azure Backup](../../backup/backup-azure-vms-encryption.md). Zálohy Ujistěte se, že možnost obnovení je možné v případě jakékoli došlo k neočekávané chybě při šifrování. Po vytvoření zálohy pomocí rutiny Set-AzVMDiskEncryptionExtension Zašifrujte spravované disky zadáním parametru-skipVmBackup. Příkaz set-AzVMDiskEncryptionExtension se u virtuálních počítačů založených na discích nespustí, dokud se neprovede zálohování a tento parametr není zadaný. 
>
>Šifrování nebo zakázání šifrování může způsobit, že se virtuální počítač restartuje. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Povolení šifrování na stávajícím nebo BĚŽÍCÍm virtuálním počítači se systémem Linux pomocí rozhraní příkazového řádku Azure 
Pomocí nástroje příkazového řádku [Azure CLI 2,0](/cli/azure) můžete povolit šifrování disku na zašifrovaném virtuálním pevném disku. Můžete ho používat v prohlížeči pomocí služby [Azure Cloud Shell](../../cloud-shell/overview.md) nebo nainstalovat na místním počítači a používat ho v jakékoli relaci PowerShellu. Chcete-li povolit šifrování na existující nebo běžící virtuální počítače IaaS s Linuxem v Azure, použijte následující příkazy rozhraní příkazového řádku:

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

- **Zakázat šifrování:** Pokud chcete šifrování zakázat, použijte příkaz [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Zakázáním šifrování je povolena pouze u svazků s daty pro virtuální počítače s Linuxem.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"></a> Povolení šifrování na stávajícím nebo běžícím virtuálním počítači se systémem Linux pomocí prostředí PowerShell
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
    
- **Zakázat šifrování disku:** Pokud chcete šifrování zakázat, použijte rutinu [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Zakázáním šifrování je povolena pouze u svazků s daty pro virtuální počítače s Linuxem.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"></a> Povolení šifrování na stávajícím nebo BĚŽÍCÍm virtuálním počítači s IaaS Linux se šablonou

Pomocí [šablony Správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)můžete povolit šifrování disku na existujícím nebo běžícím virtuálním počítači s IaaS Linux v Azure.

1. V šabloně pro rychlý Start Azure vyberte **nasadit do Azure** .

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, parametry, právní podmínky a smlouvy. Vyberte **vytvořit** , pokud chcete povolit šifrování na stávajícím nebo SPUŠTĚNém virtuálním počítači s IaaS.

V následující tabulce jsou uvedeny parametry šablony Resource Manageru pro existující nebo spouštění virtuálních počítačů, které používají ID klienta Azure AD:

| Parametr | Popis |
| --- | --- |
| AADClientID | ID klienta aplikace Azure AD, který má oprávnění k zápisu tajných klíčů do služby key vault. |
| AADClientSecret | Tajný kód klienta aplikace Azure AD, který má oprávnění k zápisu tajných klíčů do trezoru klíčů. |
| keyVaultName | Název, který klíč musí být nahrán do trezoru klíčů. Můžete ji získat pomocí příkazu Azure CLI `az keyvault show --name "MySecureVault" --query KVresourceGroup`. |
|  KeyEncryptionKeyURL | Adresa URL šifrovací klíč klíče, který se používá k šifrování vygenerovaný klíč. Tento parametr je nepovinný, pokud v rozevíracím seznamu **UseExistingKek** vyberete **nokek** . Pokud v rozevíracím seznamu **UseExistingKek** vyberete možnost **KEK** , musíte zadat hodnotu _keyEncryptionKeyURL_ . |
| VolumeType | Typ svazku, který provádí operace šifrování na. Platné podporované hodnoty jsou _OS_ nebo _All_. (Další informace najdete v části Podporované distribuce systému Linux a jejich verze pro operační systémy a datové disky v oddílu požadavky dříve.) |
| SequenceVersion | Pořadí verze Bitlockeru operace. Toto číslo verze postupně zvyšuje vždy, když je provedena operace šifrování disku ve stejném virtuálním počítači. |
| vmName | Název virtuálního počítače, který má být proveden na operace šifrování. |
| přístupové heslo | Jako datový šifrovací klíč, zadejte silné heslo. |



## <a name="bkmk_EFA"> </a>Použití funkce EncryptFormatAll pro datové disky na virtuálních počítačích se systémem Linux IaaS
Parametr EncryptFormatAll zkracuje dobu, po kterou jsou datové disky platformy Linux šifrovány. Oddíly, které splňují určitá kritéria, jsou formátovány (s jejich aktuálním systémem souborů). Pak se znovu připojí k umístění, kde byly před provedením příkazu. Pokud chcete vyloučit datový disk, který splňuje kritéria, můžete ho odpojit před spuštěním příkazu.

 Po spuštění tohoto příkazu se naformátují všechny dříve připojené jednotky. Pak se vrstva šifrování spustí v horní části jednotky, která je právě prázdná. Když je vybraná tato možnost, bude se taky šifrovat dočasný disk prostředku připojený k virtuálnímu počítači. Pokud dojde k resetování dočasné jednotky, přeformátujte a znovu Zašifrujte pro virtuální počítač pomocí řešení Azure Disk Encryption při další příležitosti.

>[!WARNING]
> EncryptFormatAll by neměl být použit, pokud jsou potřebná data na datových svazcích virtuálního počítače. Můžete vyloučit disky ze šifrování odpojováním. Vyzkoušejte nejprve parametr EncryptFormatAll na testovacím virtuálním počítači, abyste pochopili parametr funkce a jeho nevýznam před tím, než ho zkusíte na provozním virtuálním počítači. Možnost EncryptFormatAll formátuje datový disk, takže všechna data, která na něm jsou, budou ztracena. Než budete pokračovat, ověřte, že všechny disky, které chcete vyloučit, jsou správně odpojeny. </br></br>
 >Pokud tento parametr nastavíte při aktualizaci nastavení šifrování, může to vést k restartování před samotným šifrováním. V takovém případě budete chtít odebrat i disk, který nechcete naformátovat ze souboru fstab. Podobně byste měli před zahájením operace šifrování přidat do souboru fstab oddíl, který chcete šifrovat. 

### <a name="bkmk_EFACriteria"></a> EncryptFormatAll kritéria
Parametr projde všemi oddíly a zašifruje je tak dlouho, dokud splňují *všechna* následující kritéria: 
- Není kořenový/operačního systému a spouštěcí oddíl
- Už není šifrovaný
- Není klíče BEK svazek
- Není svazek RAID
- Není svazku LVM
- Je připojený

Šifrování disků, které tvoří svazek RAID nebo LVM spíše než svazek RAID nebo LVM.

### <a name="bkmk_EFATemplate"></a> Použití parametru EncryptFormatAll se šablonou
Pokud chcete použít možnost EncryptFormatAll, použijte libovolnou existující šablonu Azure Resource Manager, která šifruje virtuální počítač Linux a změňte pole **EncryptionOperation** pro prostředek AzureDiskEncryption.

1. Jako příklad použijte [šablonu správce prostředků k šifrování spuštěného virtuálního počítače se systémem Linux IaaS](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. V šabloně pro rychlý Start Azure vyberte **nasadit do Azure** .
3. Změňte pole **EncryptionOperation** z **EnableEncryption** na **EnableEncryptionFormatAl**.
4. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, další parametry, právní podmínky a smlouvy. Vyberte **vytvořit** , pokud chcete povolit šifrování na stávajícím nebo SPUŠTĚNém virtuálním počítači s IaaS.


### <a name="bkmk_EFAPSH"></a> Použití parametru EncryptFormatAll s rutinou prostředí PowerShell
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


### <a name="bkmk_EFALVM"></a> Použití parametru EncryptFormatAll u Správce logických svazků (LVM) 
Doporučujeme, abyste nastavení LVM-na crypt. Pro všechny následující příklady nahraďte zařízení-Path a mountpoints libovolnými vašimi vlastními případy použití. Toto nastavení lze provést následujícím způsobem:

- Přidáte datové disky, které budou tvoří virtuální počítač.
- Formátování, připojování a přidat tyto disky do souboru fstab.

    1. Naformátujte nově přidaný disk. Využíváme Azure vygeneruje zde symbolických odkazů. Problémy související se změny názvů zařízení pomocí symbolických odkazů se vyhnete. Další informace najdete v tématu [řešení potíží s názvy zařízení](troubleshoot-device-names-problems.md).
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. Připojte disky.
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. Přidejte do fstab.
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. Spuštěním rutiny prostředí PowerShell set-AzVMDiskEncryptionExtension s-EncryptFormatAll Zašifrujte tyto disky.
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. Nastavte LVM nad tyto nové disky. Všimněte si, že po dokončení spuštění virtuálního počítače jsou odemknutí zašifrované jednotky. Připojení LVM tedy také muset následně zpozdit.




## <a name="bkmk_VHDpre"></a> Nové virtuální počítače s IaaS vytvořené z VHD a šifrovacích klíčů šifrovaných zákazníkem
V tomto scénáři můžete povolit šifrování pomocí šablony Resource Manageru, rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku. Následující části popisují podrobněji šablonu Resource Manageru a příkazech rozhraní příkazového řádku. 

Postupujte podle pokynů v dodatku k přípravě předem šifrované imagí, které lze použít v Azure. Po vytvoření image můžete použít kroky v další části vytvořit šifrovaný virtuální počítač Azure.

* [Příprava předem zašifrovaného virtuálního pevného disku se systémem Linux](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Je nutné pořídit snímek nebo zálohovat instanci virtuálního počítače na bázi spravovaného disku mimo rámec a před povolením Azure Disk Encryption. Můžete si pořídit snímek spravovaného disku z portálu nebo můžete použít [Azure Backup](../../backup/backup-azure-vms-encryption.md). Zálohy Ujistěte se, že možnost obnovení je možné v případě jakékoli došlo k neočekávané chybě při šifrování. Po vytvoření zálohy pomocí rutiny Set-AzVMDiskEncryptionExtension Zašifrujte spravované disky zadáním parametru-skipVmBackup. Příkaz set-AzVMDiskEncryptionExtension se u virtuálních počítačů založených na discích nespustí, dokud se neprovede zálohování a tento parametr není zadaný. 
>
>Šifrování nebo zakázání šifrování může způsobit, že se virtuální počítač restartuje.



### <a name="bkmk_VHDprePSH"></a> Použití Azure PowerShell k šifrování virtuálních počítačů s IaaS pomocí předem šifrovaných virtuálních pevných disků 
Pomocí rutiny PowerShellu [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples)můžete povolit šifrování disku na šifrovaném virtuálním pevném disku. V následujícím příkladu jsou uvedeny některé běžné parametry. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Povoluje šifrování na nově přidaných datového disku
Nový datový disk můžete přidat pomocí příkazem [AZ VM disk Attach](add-disk.md) nebo [prostřednictvím Azure Portal](attach-disk-portal.md). Předtím, než je možné šifrovat, musíte nejdřív připojit nově připojený datový disk. Musíte požádat o šifrování datové jednotky, protože jednotku nebude možné použít, když probíhá šifrování. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Povolení šifrování u nově přidaného disku pomocí Azure CLI
 Pokud byl virtuální počítač dříve zašifrován pomocí "All", parametr--Volume-Type by měl zůstat všechny. Zahrnuje všechny disky s operačním systémem a daty. Pokud byl virtuální počítač dříve zašifrován pomocí typu svazku "OS", pak by měl být parametr--Volume-Type změněn na hodnotu All, aby byl operační systém i nový datový disk zahrnut. Pokud byl virtuální počítač zašifrovaný pouze s typem svazku "data", pak může zůstat data, jak je znázorněno zde. Přidání a připojení nového datového disku k virtuálnímu počítači nemá dostatečnou přípravu na šifrování. Předtím, než povolíte šifrování, musí být nově připojený disk ve virtuálním počítači naformátovaný a správně připojený. V systému Linux musí být disk připojen v/etc/fstab s [názvem trvalého blokování zařízení](troubleshoot-device-names-problems.md). 

Na rozdíl od syntaxe PowerShellu rozhraní příkazového řádku nevyžaduje, abyste při povolování šifrování zadali jedinečnou verzi sekvence. Rozhraní příkazového řádku automaticky generuje a používá svůj vlastní jedinečný pořadí hodnotu verze.

-  **Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Zabalení spuštěného virtuálního počítače pomocí KEK k zabalení tajného klíče klienta:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Povoluje šifrování na nově přidaný disk pomocí Azure Powershellu
 Když použijete PowerShell k šifrování nového disku pro Linux, je nutné zadat novou verzi sekvence. Pořadí verze musí být jedinečný. Následující skript vygeneruje identifikátor GUID pro verzi sekvence. 
 

-  **Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta:** Následující skript inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. U skupiny prostředků, virtuálního počítače, trezoru klíčů, aplikace Azure AD a tajného klíče klienta by se už měly vytvořit požadavky. Hodnoty MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, my-AAD-Client-ID a my-AAD-Client-Secret nahraďte hodnotami. Parametr - VolumeType je nastaven na datové disky, nikoli disk s operačním systémem. Pokud byl virtuální počítač předtím zašifrovaný pomocí typu svazku "OS" nebo "All", měl by být parametr-VolumeType změněn na hodnotu All, aby byl operační systém i nový datový disk zahrnut.

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
- **Zabalení spuštěného virtuálního počítače pomocí KEK k zabalení tajného klíče klienta:** Azure Disk Encryption umožňuje zadat existující klíč do trezoru klíčů pro zabalení tajných klíčů disku, které byly generovány při povolování šifrování. Když je zadaný klíč šifrování klíče, Azure Disk Encryption používá tento klíč k zabalení šifrovacích tajných kódů před zápisem do trezoru klíčů. Parametr - VolumeType je nastaven na datové disky, nikoli disk s operačním systémem. Pokud byl virtuální počítač předtím zašifrovaný pomocí typu svazku "OS" nebo "All", měl by být parametr-VolumeType změněn na hodnotu All, aby byl operační systém i nový datový disk zahrnut.

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

## <a name="disable-encryption-for-linux-vms"></a>Zakázat šifrování pro virtuální počítače s Linuxem
Šifrování můžete zakázat pomocí Azure PowerShell, rozhraní příkazového řádku Azure nebo šablony Správce prostředků. 

>[!IMPORTANT]
>Zakázáním šifrování v Azure Disk Encryption na virtuální počítače s Linuxem je podporována pouze pro datové svazky. Pokud je svazek s operačním systémem zašifrovaný, není podpora na svazcích dat nebo operačních systémů podporovaná. 

- **Zakázat šifrování disku pomocí Azure PowerShell:** Pokud chcete šifrování zakázat, použijte rutinu [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Zakázat šifrování pomocí Azure CLI:** Pokud chcete šifrování zakázat, použijte příkaz [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Zakázat šifrování pomocí šablony Správce prostředků:** Šifrování zakážete tak, že použijete [zakázat šifrování v šabloně virtuálního počítače se systémem Linux](https://aka.ms/decrypt-linuxvm) .
     1. Vyberte **nasadit do Azure**.
     2. Vyberte předplatné, skupinu prostředků, umístění, virtuálních počítačů, právní podmínky a smlouvy.
     3. Pokud chcete zakázat šifrování disku na běžícím virtuálním počítači s Windows, vyberte **koupit** . 


## <a name="next-steps"></a>Další kroky

- [Přehled Azure Disk Encryption pro Linux](disk-encryption-overview-aad.md)
- [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption s využitím Azure AD (předchozí verze)](disk-encryption-key-vault-aad.md)
