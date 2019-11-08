---
title: Azure Disk Encryption s virtuálními počítači s Aplikace Azure AD Linux IaaS (předchozí verze)
description: Tento článek poskytuje pokyny k povolení Microsoft Azureho šifrování disku pro virtuální počítače s IaaS Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 35d14599ca9a7abdad88603deeb8431f77e92a92
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748936"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Povolení Azure Disk Encryption s Azure AD na virtuálních počítačích se systémem Linux (předchozí verze)

**Nová vydaná verze Azure Disk Encryption eliminuje požadavek na poskytnutí parametru aplikace Azure AD, aby bylo možné povolit šifrování disku virtuálního počítače. V nové verzi už nebudete muset zadávat přihlašovací údaje Azure AD během kroku povolení šifrování. Všechny nové virtuální počítače musí být zašifrované bez parametrů aplikace Azure AD, které používají novou verzi. Pokyny k povolení šifrování disků virtuálního počítače pomocí nové verze najdete v tématu [Azure Disk Encryption pro virtuální počítače se systémem Linux](disk-encryption-linux.md). Virtuální počítače, které jsou už šifrované pomocí parametrů aplikace Azure AD, se pořád podporují a měly by se udržovat dál se syntaxí AAD.**

Můžete povolit mnoho scénářů šifrování disku a postup se může lišit v závislosti na scénáři. Následující oddíly popisují scénáře podrobněji podrobněji pro virtuální počítače s IaaS Linux. Diskové šifrování můžete použít jenom pro virtuální počítače s [podporovanými velikostmi virtuálních počítačů a operačními systémy](disk-encryption-overview.md#supported-vms-and-operating-systems). Musíte splňovat i následující požadavky:

- [Další požadavky na virtuální počítače](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Sítě a Zásady skupiny](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Požadavky na úložiště šifrovacího klíče](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Pořídit [snímek](snapshot-copy-managed-disk.md) a/nebo zálohovat před zašifrováním disků Zálohování zajišťuje možnost obnovení, pokud během šifrování dojde k neočekávané chybě. Virtuální počítače se spravovanými disky vyžadují zálohování před tím, než dojde k šifrování. Po provedení zálohy můžete použít rutinu Set-AzVMDiskEncryptionExtension k šifrování spravovaných disků zadáním parametru-skipVmBackup. Další informace o zálohování a obnovení šifrovaných virtuálních počítačů najdete v článku o [Azure Backup](../../backup/backup-azure-vms-encryption.md) . 

>[!WARNING]
 > - Pokud jste už dříve použili [Azure Disk Encryption se službou Azure AD](disk-encryption-overview-aad.md) k šifrování tohoto virtuálního počítače, budete muset tuto možnost použít k ZAšifrování virtuálního počítače. V tomto šifrovaném virtuálním počítači nemůžete použít [Azure Disk Encryption](disk-encryption-overview.md) , protože se nejedná o podporovaný scénář, což znamená, že pro tento zašifrovaný virtuální počítač se zatím nepodporuje přepínání z aplikace AAD.
 > - Abyste se ujistili, že šifrovací tajná klíče nejsou mezi regionálními hranicemi, Azure Disk Encryption potřebuje Key Vault a virtuální počítače, které se mají umístit do stejné oblasti. Vytvořte a použijte Key Vault, který je ve stejné oblasti jako virtuální počítač, který chcete zašifrovat.
 > - Při šifrování svazků se systémem Linux může tento proces trvat několik hodin. Pro svazky operačního systému Linux je běžné, že pro šifrování trvá déle než objemy dat.
> - Při šifrování svazků se systémem Linux by se měl virtuální počítač považovat za nedostupný. Důrazně doporučujeme vyhnout se přihlášení SSH, zatímco probíhá šifrování, aby nedocházelo k blokování jakýchkoli otevřených souborů, ke kterým bude potřeba během procesu šifrování dostat. Chcete-li zjistit průběh, lze použít příkazy [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) nebo [šifrování virtuálního počítače](/cli/azure/vm/encryption#az-vm-encryption-show) . Pro tento proces může trvat několik hodin, než se 30 GB svazek s operačním systémem a další čas šifrování datových svazků. Doba šifrování datového svazku bude úměrná velikosti a množství datových svazků, pokud se nepoužije možnost šifrovat formát ALL. 
 > - Zakázání šifrování u virtuálních počítačů se systémem Linux je podporováno pouze pro datové svazky. Pokud je svazek s operačním systémem zašifrovaný, není podpora na svazcích dat nebo operačních systémů podporována.  

 

## <a name="bkmk_RunningLinux"></a> Povolení šifrování na stávajícím nebo BĚŽÍCÍm virtuálním počítači s IaaS Linux

V tomto scénáři můžete šifrování povolit pomocí šablony Správce prostředků, rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku. 

>[!IMPORTANT]
 >Je nutné, abyste instanci virtuálního počítače na bázi spravovaného disku pocházeli z a před povolením Azure Disk Encryption. Snímek spravovaného disku se dá vzít z portálu, nebo se dá použít [Azure Backup](../../backup/backup-azure-vms-encryption.md) . Zálohy zajišťují, že možnost obnovení je možné v případě jakékoli neočekávané chyby při šifrování. Po vytvoření zálohy se dá rutina Set-AzVMDiskEncryptionExtension použít k šifrování spravovaných disků zadáním parametru-skipVmBackup. Příkaz set-AzVMDiskEncryptionExtension se u virtuálních počítačů založených na discích nezdaří, dokud se neprovede záloha a tento parametr se zadal. 
>
>Šifrování nebo zakázání šifrování může způsobit, že se virtuální počítač restartuje. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Povolení šifrování na stávajícím nebo běžícím virtuálním počítači se systémem Linux pomocí Azure CLI 
Pomocí nástroje příkazového řádku [Azure CLI 2,0](/cli/azure) můžete povolit šifrování disku na zašifrovaném virtuálním pevném disku. Můžete ho používat v prohlížeči pomocí služby [Azure Cloud Shell](../../cloud-shell/overview.md) nebo nainstalovat na místním počítači a používat ho v jakékoli relaci PowerShellu. Pokud chcete povolit šifrování pro existující nebo běžící virtuální počítače s IaaS Linux v Azure, použijte následující příkazy rozhraní příkazového řádku:

Pomocí příkazu [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) Povolte šifrování na běžícím virtuálním počítači s IaaS v Azure.

-  **Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Zabalením spuštěného virtuálního počítače pomocí KEK zabalíte tajný klíč klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Syntaxe hodnoty parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:/subscriptions/[ID předplatného-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name]</br> </br>
Syntaxe hodnoty parametru klíč-šifrování klíče je úplný identifikátor URI pro KEK jako v: https://[název trezoru-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

- **Ověřte, že jsou disky šifrované:** Pokud chcete zjistit stav šifrování virtuálního počítače s IaaS, použijte příkaz [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Zakázat šifrování:** Pokud chcete šifrování zakázat, použijte příkaz [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Zakázání šifrování je povolené jenom u datových svazků pro virtuální počítače se systémem Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"></a> Povolení šifrování na stávajícím nebo běžícím virtuálním počítači se systémem Linux pomocí prostředí PowerShell
Pomocí rutiny [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) Povolte šifrování na běžícím virtuálním počítači s IaaS v Azure. Pořídit [snímek](snapshot-copy-managed-disk.md) nebo zálohovat virtuální počítač s [Azure Backup](../../backup/backup-azure-vms-encryption.md) před zašifrováním disků. Parametr-skipVmBackup je už ve skriptech PowerShellu určený k zašifrování běžícího virtuálního počítače se systémem Linux.

- **Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta:** Skript níže inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. U skupiny prostředků, virtuálního počítače, trezoru klíčů, aplikace AAD a tajného klíče klienta by se už měly vytvořit požadavky. Hodnoty MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, my-AAD-Client-ID a my-AAD-Client-Secret nahraďte hodnotami. Pokud chcete určit, které disky se šifrují, upravte parametr-VolumeType.

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
- **Zabalením spuštěného virtuálního počítače pomocí KEK zabalíte tajný klíč klienta:** Azure Disk Encryption umožňuje zadat existující klíč do trezoru klíčů pro zabalení tajných klíčů disku, které byly generovány při povolování šifrování. Když je zadaný klíč šifrování klíče, Azure Disk Encryption používá tento klíč k zabalení šifrovacích tajných kódů před zápisem do Key Vault. Pokud chcete určit, které disky se šifrují, upravte parametr-VolumeType. 

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
  > Syntaxe hodnoty parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:/subscriptions/[ID předplatného-GUID]/resourceGroups/[KVresource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name]</br> </br>
  Syntaxe hodnoty parametru klíč-šifrování klíče je úplný identifikátor URI pro KEK jako v: https://[název trezoru-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 
    
- **Ověřte, že jsou disky šifrované:** Pokud chcete zjistit stav šifrování virtuálního počítače s IaaS, použijte rutinu [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Zakázat šifrování disku:** Pokud chcete šifrování zakázat, použijte rutinu [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Zakázání šifrování je povolené jenom u datových svazků pro virtuální počítače se systémem Linux.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"></a> Povolení šifrování na stávajícím nebo BĚŽÍCÍm virtuálním počítači s IaaS Linux se šablonou

Pomocí [šablony Správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)můžete povolit šifrování disku na existujícím nebo běžícím virtuálním počítači s IaaS Linux v Azure.

1. V šabloně pro rychlý Start Azure klikněte na **nasadit do Azure** .

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, parametry, právní smlouvy a smlouvu. Kliknutím na **vytvořit** Povolte šifrování na stávajícím nebo SPUŠTĚNém virtuálním počítači s IaaS.

Následující tabulka uvádí Správce prostředků parametry šablony pro existující nebo běžící virtuální počítače, které používají ID klienta Azure AD:

| Parametr | Popis |
| --- | --- |
| AADClientID | ID klienta aplikace Azure AD, která má oprávnění k zápisu tajných kódů do trezoru klíčů. |
| AADClientSecret | Tajný kód klienta aplikace Azure AD, který má oprávnění k zápisu tajných kódů do trezoru klíčů. |
| keyVaultName | Název trezoru klíčů, do kterého se má klíč nahrát Můžete ji získat pomocí příkazu Azure CLI `az keyvault show --name "MySecureVault" --query KVresourceGroup`. |
|  keyEncryptionKeyURL | Adresa URL klíčového šifrovacího klíče, který se používá k zašifrování vygenerovaného klíče. Tento parametr je nepovinný, pokud v rozevíracím seznamu UseExistingKek vyberete **nokek** . Pokud v rozevíracím seznamu UseExistingKek vyberete možnost **KEK** , musíte zadat hodnotu _keyEncryptionKeyURL_ . |
| volumeType | Typ svazku, na kterém se operace šifrování provádí. Platné podporované hodnoty jsou _operační systém_ nebo _všechny_ (Další informace najdete v části Podporované systémy Linux distribuce a jejich verze pro operační systém a datové disky v předchozím oddílu). |
| sequenceVersion | Verze sekvence operace nástroje BitLocker Zvyšte číslo této verze pokaždé, když se na stejném virtuálním počítači provede operace šifrování disku. |
| vmName | Název virtuálního počítače, na kterém se má operace šifrování provést. |
| přístupové heslo | Jako šifrovací klíč dat zadejte silné heslo. |



## <a name="bkmk_EFA"> </a>Použití funkce EncryptFormatAll pro datové disky na virtuálních počítačích se systémem Linux IaaS
Parametr **EncryptFormatAll** zkracuje dobu, po kterou jsou datové disky platformy Linux šifrovány. Oddíly, které splňují určitá kritéria, se naformátují (s aktuálním systémem souborů). Pak se znovu připojí k umístění, kde bylo před provedením příkazu. Pokud chcete vyloučit datový disk, který splňuje kritéria, můžete ho před spuštěním příkazu odpojit.

 Po spuštění tohoto příkazu se naformátují všechny jednotky, které byly dříve připojené. Pak se vrstva šifrování spustí na začátku prázdné jednotky. Když se vybere tato možnost, bude se šifrovat i dočasný disk prostředku připojený k virtuálnímu počítači. Pokud se dopředná jednotka obnoví, přeformátuje a znovu zašifruje pro virtuální počítač řešení Azure Disk Encryption při další příležitosti.

>[!WARNING]
> EncryptFormatAll by neměl být použit, pokud jsou v datových svazcích virtuálního počítače potřebná data. Můžete vyloučit disky ze šifrování odpojováním. Nejdřív byste si měli vyzkoušet EncryptFormatAll nejprve na testovacím virtuálním počítači, pochopit parametr funkce a jeho nevýznam před tím, než se ho pokusíte na produkčním virtuálním počítači. Možnost EncryptFormatAll formátuje datový disk a všechna data, která na něm jsou, budou ztracena. Než budete pokračovat, ověřte, že disky, které chcete vyloučit, jsou správně odpojeny. </br></br>
 >Pokud tento parametr nastavujete při aktualizaci nastavení šifrování, může to vést k restartování před samotným šifrováním. V takovém případě budete chtít odebrat i disk, který nechcete naformátovat ze souboru fstab. Podobně byste před zahájením operace šifrování měli přidat do souboru fstab oddíl, který chcete zašifrovat. 

### <a name="bkmk_EFACriteria"></a> EncryptFormatAll kritéria
Parametr přechází do všech oddílů a zašifruje je tak dlouho, dokud splňují **všechna** následující kritéria: 
- Není kořenovým adresářem/operačním systémem nebo spouštěcím oddílem.
- Ještě není zašifrováno
- Není klíče bek svazek.
- Není svazek RAID.
- Není LVM svazek.
- Je připojeno

Zašifrujte disky, které tvoří svazek RAID nebo LVM, a ne svazek RAID nebo LVM.

### <a name="bkmk_EFATemplate"></a> Použití parametru EncryptFormatAll se šablonou
Pokud chcete použít možnost EncryptFormatAll, použijte existující šablonu Azure Resource Manager, která šifruje virtuální počítač Linux a změňte pole **EncryptionOperation** pro prostředek AzureDiskEncryption.

1. Jako příklad použijte [šablonu správce prostředků k šifrování spuštěného virtuálního počítače se systémem Linux IaaS](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. V šabloně pro rychlý Start Azure klikněte na **nasadit do Azure** .
3. Změňte **EncryptionOperation** z **EnableEncryption** na **EnableEncryptionFormatAl**
4. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, další parametry, právní výrazy a smlouvy. Kliknutím na **vytvořit** Povolte šifrování na stávajícím nebo SPUŠTĚNém virtuálním počítači s IaaS.


### <a name="bkmk_EFAPSH"></a> Použití parametru EncryptFormatAll s rutinou prostředí PowerShell
Použijte rutinu [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) s parametrem `EncryptFormatAll`.

**Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta a EncryptFormatAll:** Například skript níže inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension s parametrem EncryptFormatAll. U skupiny prostředků, virtuálního počítače, trezoru klíčů, aplikace AAD a tajného klíče klienta by se už měly vytvořit požadavky. Hodnoty MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, my-AAD-Client-ID a my-AAD-Client-Secret nahraďte hodnotami.
  
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
Doporučujeme LVM instalaci. Pro všechny následující příklady nahraďte zařízení-Path a mountpoints bez ohledu na to, jaký je váš případ použití. Tuto instalaci můžete provést takto:

- Přidejte datové disky, které budou tvořit virtuální počítač.
- Naformátujte, připojte a přidejte tyto disky do souboru fstab.

    1. Naformátujte nově přidaný disk. Symbolických odkazů vygenerované v Azure používáme tady. Použití symbolických odkazů zabraňuje problémům souvisejícím se změnou názvů zařízení. Další informace najdete v článku řešení potíží s chybami [názvů zařízení](troubleshoot-device-names-problems.md) .
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Připojte disky.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Přidejte do fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Spuštěním rutiny prostředí PowerShell set-AzVMDiskEncryptionExtension s-EncryptFormatAll Zašifrujte tyto disky.
         ```azurepowershell-interactive
         Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Nastavte LVM na těchto nových discích. Poznámka: šifrované jednotky jsou po dokončení spuštění virtuálního počítače odemčeny. Proto bude nutné připojení LVM také později zpozdit.




## <a name="bkmk_VHDpre"></a> Nové virtuální počítače s IaaS vytvořené z VHD a šifrovacích klíčů šifrovaných zákazníkem
V tomto scénáři můžete povolit šifrování pomocí Správce prostředků šablony, rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku. Následující části podrobněji popisují Správce prostředků šablon a příkazů rozhraní příkazového řádku. 

Použijte pokyny v příloze pro přípravu předem šifrovaných imagí, které je možné použít v Azure. Po vytvoření image můžete pomocí kroků v následující části vytvořit zašifrovaný virtuální počítač Azure.

* [Příprava předem zašifrovaného virtuálního pevného disku se systémem Linux](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Je nutné, abyste instanci virtuálního počítače na bázi spravovaného disku pocházeli z a před povolením Azure Disk Encryption. Snímek spravovaného disku se dá vzít z portálu, nebo se dá použít [Azure Backup](../../backup/backup-azure-vms-encryption.md) . Zálohy zajišťují, že možnost obnovení je možné v případě jakékoli neočekávané chyby při šifrování. Po vytvoření zálohy se dá rutina Set-AzVMDiskEncryptionExtension použít k šifrování spravovaných disků zadáním parametru-skipVmBackup. Příkaz set-AzVMDiskEncryptionExtension se u virtuálních počítačů založených na discích nezdaří, dokud se neprovede záloha a tento parametr se zadal. 
>
>Šifrování nebo zakázání šifrování může způsobit, že se virtuální počítač restartuje. 



### <a name="bkmk_VHDprePSH"></a> Použití Azure PowerShell k šifrování virtuálních počítačů s IaaS pomocí předem šifrovaných virtuálních pevných disků 
Pomocí rutiny PowerShellu [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples)můžete povolit šifrování disku na šifrovaném virtuálním pevném disku. V následujícím příkladu jsou uvedeny některé běžné parametry. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Povolení šifrování u nově přidaného datového disku
Nový datový disk můžete přidat pomocí [AZ VM disk Attach](add-disk.md)nebo [prostřednictvím Azure Portal](attach-disk-portal.md). Než budete moct šifrování, musíte nejdřív připojit nově připojený datový disk. Musíte požádat o šifrování datové jednotky, protože jednotka nebude v průběhu šifrování použitelná. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Povolení šifrování u nově přidaného disku pomocí Azure CLI
 Pokud byl virtuální počítač předtím zašifrovaný pomocí příkazu All, musí parametr--Volume-Type zůstat všech. Vše zahrnuje operační systém i datové disky. Pokud byl virtuální počítač předtím zašifrovaný pomocí typu svazku "OS", pak by měl být parametr--Volume-Type změněn na hodnotu All, aby byl operační systém i nový datový disk zahrnut. Pokud byl virtuální počítač zašifrován pouze s typem svazku "data", může zůstat "data", jak je znázorněno níže. Přidání a připojení nového datového disku k virtuálnímu počítači není dostatečně přípravné na šifrování. Předtím, než povolíte šifrování, musí být nově připojený disk naformátovaný a správně připojený k virtuálnímu počítači. V systému Linux musí být disk připojen v/etc/fstab s [názvem trvalého blokování zařízení](troubleshoot-device-names-problems.md).  

Na rozdíl od syntaxe PowerShellu rozhraní příkazového řádku nevyžaduje, aby uživatel při povolování šifrování poskytoval jedinečnou verzi sekvence. Rozhraní příkazového řádku automaticky vygeneruje a použije svou vlastní jedinečnou hodnotu verze sekvence.

-  **Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta:** 

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Zabalením spuštěného virtuálního počítače pomocí KEK zabalíte tajný klíč klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Povolit šifrování na nově přidaném disku s Azure PowerShell
 Při použití prostředí PowerShell k šifrování nového disku pro Linux je nutné zadat novou verzi sekvence. Verze sekvence musí být jedinečná. Následující skript vygeneruje identifikátor GUID pro verzi sekvence. 
 

-  **Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta:** Skript níže inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. U skupiny prostředků, virtuálního počítače, trezoru klíčů, aplikace AAD a tajného klíče klienta by se už měly vytvořit požadavky. Hodnoty MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, my-AAD-Client-ID a my-AAD-Client-Secret nahraďte hodnotami. Parametr-VolumeType je nastaven na datové disky, nikoli na disk s operačním systémem. Pokud byl virtuální počítač předtím zašifrovaný pomocí typu svazku "OS" nebo "All", měl by být parametr-VolumeType změněn na hodnotu All, aby byl operační systém i nový datový disk zahrnut.

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
- **Zabalením spuštěného virtuálního počítače pomocí KEK zabalíte tajný klíč klienta:** Azure Disk Encryption umožňuje zadat existující klíč do trezoru klíčů pro zabalení tajných klíčů disku, které byly generovány při povolování šifrování. Když je zadaný klíč šifrování klíče, Azure Disk Encryption používá tento klíč k zabalení šifrovacích tajných kódů před zápisem do Key Vault. Parametr-VolumeType je nastaven na datové disky, nikoli na disk s operačním systémem. Pokud byl virtuální počítač předtím zašifrovaný pomocí typu svazku "OS" nebo "All", měl by být parametr-VolumeType změněn na hodnotu All, aby byl operační systém i nový datový disk zahrnut.

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
> Syntaxe hodnoty parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:/subscriptions/[ID předplatného-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name] </br> </br>
Syntaxe hodnoty parametru klíč-šifrování klíče je úplný identifikátor URI pro KEK jako v: https://[název trezoru-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

## <a name="disable-encryption-for-linux-vms"></a>Zakázat šifrování pro virtuální počítače se systémem Linux
Šifrování můžete zakázat pomocí Azure PowerShell, rozhraní příkazového řádku Azure nebo pomocí Správce prostředků šablony. 

>[!IMPORTANT]
>Zakázání šifrování pomocí Azure Disk Encryption na virtuálních počítačích se systémem Linux je podporováno pouze pro datové svazky. Pokud je svazek s operačním systémem zašifrovaný, není podpora na svazcích dat nebo operačních systémů podporována.  

- **Zakázat šifrování disku pomocí Azure PowerShell:** Pokud chcete šifrování zakázat, použijte rutinu [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Zakázat šifrování pomocí Azure CLI:** Pokud chcete šifrování zakázat, použijte příkaz [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Zakázat šifrování pomocí šablony Správce prostředků:** Zakáže šifrování pomocí [zákazu šifrování na spuštěné šabloně virtuálního počítače se systémem Linux](https://aka.ms/decrypt-linuxvm) .
     1. Klikněte na **Deploy to Azure** (Nasadit do Azure).
     2. Vyberte předplatné, skupinu prostředků, umístění, virtuální počítač, právní podmínku a smlouvu.
     3.  Kliknutím na **koupit** zakažte šifrování disku na běžícím virtuálním počítači s Windows. 


## <a name="next-steps"></a>Další kroky

- [Přehled Azure Disk Encryption pro Linux](disk-encryption-overview-aad.md)
- [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption s využitím Azure AD (předchozí verze)](disk-encryption-key-vault-aad.md)
