---
title: Azure Disk Encryption pomocí Azure AD App Linuxem v režimu IaaS virtuálních počítačů (předchozí verze) | Dokumentace Microsoftu
description: Tento článek obsahuje pokyny týkající se povolení Microsoft Azure Disk Encryption pro virtuální počítače IaaS s Linuxem.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 95695b12-0218-4ebf-a0dd-21de52787477
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2018
ms.author: mstewart
ms.openlocfilehash: f711e5acb37eeb45cc2285b64b72102bfe2f44e6
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889738"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms-previous-release"></a>Povolit Azure Disk Encryption pro virtuální počítače s Linuxem IaaS (předchozí verze)

**Novou verzi sady Azure Disk Encryption eliminuje požadavek na poskytnutí parametrem aplikace Azure AD povolit šifrování disku virtuálního počítače. Nové verze se už nevyžadují zadejte přihlašovací údaje Azure AD během kroku povolení šifrování. Všechny nové virtuální počítače musí být zašifrován bez parametrů aplikace Azure AD pomocí nové verze. Chcete-li zobrazit pokyny, jak povolit šifrování disku virtuálního počítače pomocí nové verze, najdete v článku [Azure Disk Encryption pro virtuální počítače s Linuxem](azure-security-disk-encryption-linux.md). Virtuální počítače, které již byly šifrované pomocí aplikace Azure AD, parametry jsou stále podporovány a má pokračovat udržovat syntaxí AAD.**

Můžete povolit řadu scénářů šifrování disku a kroků může lišit v závislosti scénáři. Následující části se věnují scénáře podrobněji pro virtuální počítače IaaS s Linuxem. Než budete moct použít šifrování disku, [požadavky Azure Disk Encryption](azure-security-disk-encryption-prerequisites-aad.md) musíte provést a [další požadavky pro virtuální počítače IaaS s Linuxem](azure-security-disk-encryption-prerequisites-aad.md#bkmk_LinuxPrereq) části byste měli zkontrolovat.

Přijmout [snímku](../virtual-machines/windows/snapshot-copy-managed-disk.md) nebo před disky jsou šifrované zálohovat. Zálohy Ujistěte se, že možnost obnovení je možné, pokud dojde k neočekávané chybě při šifrování. Virtuální počítače se spravovanými disky vyžadují zálohu, než dojde k šifrování. Po provedení zálohy můžete použít rutinu Set-AzureRmVMDiskEncryptionExtension zadáním parametru - skipVmBackup šifrování spravované disky. Další informace o tom, jak zálohování a obnovení šifrovaných virtuálních počítačů najdete v tématu [Azure Backup](../backup/backup-azure-vms-encryption.md) článku. 

>[!WARNING]
 >Pokud chcete mít jistotu, že šifrování tajných kódů není překračují hranice regionální, Azure Disk Encryption musí být umístěné ve stejné oblasti služby Key Vault a virtuální počítače. Vytvoření a použití služby Key Vault, která je ve stejné oblasti jako virtuální počítač k šifrování.</br></br>

> Při šifrování svazků operačního systému Linux, proces může trvat několik hodin. Je běžné pro svazky operačního systému Linux trvat déle než datové svazky k šifrování. 

>Zakázáním šifrování na virtuální počítače s Linuxem je podporována pouze pro datové svazky. Není podporován na data nebo svazků operačního systému, pokud byla dříve zašifrována svazek s operačním systémem.  


## <a name="bkmk_NewLinux"></a> Nasazení nového virtuálního počítače s Linuxem IaaS s povoleným šifrováním disku 

1. Použití [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel) vytvořte nový šifrovaných virtuálních počítačů IaaS s Linuxem. Šablona vytvoří nový virtuální počítač RedHat Linuxu 7.2 s 200 GB diskového pole RAID-0 pole a šifrování celého disku použití spravovaných disků. Na [nejčastější dotazy k](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) článku, můžete si všimnout, že některých Linuxových distribucích pro datové disky podporují pouze šifrování. Tato šablona však nabízí příležitost seznámit se s nasazením šablony a ověřuje stav šifrování pomocí několika metod. 
 
1. Klikněte na tlačítko **nasadit do Azure** na šablony Azure Resource Manageru.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, parametry, právní podmínky a smlouvy. Klikněte na tlačítko **vytvořit** chcete povolit šifrování na existující nebo spuštěného virtuálního počítače IaaS.

3. Po nasazení šablony, ověřte stav šifrování virtuálního počítače pomocí upřednostňované metody:
     - Ověření pomocí Azure CLI pomocí [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) příkazu. 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
         ```

     - Ověření pomocí Azure Powershellu pomocí [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) rutiny. 

         ```azurepowershell-interactive
         Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
         ```

     - Vyberte virtuální počítač a potom klikněte na **disky** pod **nastavení** záhlaví a ověřit stav šifrování na portálu. V grafu v části **šifrování**, uvidíte, jestli je povolené. 

| Parametr | Popis |
| --- | --- |
| ID klienta AAD | ID klienta aplikace Azure AD, který má oprávnění k zápisu tajných klíčů do služby key vault. |
| Tajný kód klienta AAD | Tajný kód klienta aplikace Azure AD, který má oprávnění k zápisu tajných klíčů do trezoru klíčů. |
| Název trezoru klíčů | Název trezoru klíčů, klíč by měl umístit. |
| Skupina prostředků trezoru klíčů | Skupina prostředků trezoru klíčů. |


## <a name="bkmk_RunningLinux"> </a> Povoluje šifrování na existující nebo spuštěné IaaS virtuálního počítače s Linuxem
V tomto scénáři můžete povolit šifrování pomocí šablony Resource Manageru, rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku. 

>[!IMPORTANT]
 >Je nezbytně nutné k snímku a/nebo zálohování spravovaného disku na základě instance virtuálního počítače mimo a před povolením Azure Disk Encryption. Snímek spravovaného disku může být přijata z portálu, nebo [Azure Backup](../backup/backup-azure-vms-encryption.md) lze použít. Zálohy Ujistěte se, že možnost obnovení je možné v případě jakékoli došlo k neočekávané chybě při šifrování. Po provedení zálohy rutinu Set-AzureRmVMDiskEncryptionExtension slouží k šifrování zadáním parametru - skipVmBackup spravované disky. Příkaz Set-AzureRmVMDiskEncryptionExtension selže ve virtuálních počítačích spravovaných disků na základě dokud provedl zálohování a tento parametr nebyl zadán. 
>
>Šifrování nebo zakázáním šifrování může způsobit restartování virtuálního počítače. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Povoluje šifrování na existující nebo spuštěného virtuálního počítače s Linuxem pomocí Azure CLI 
Můžete povolit šifrování disku na virtuální pevný disk šifrovaný po instalaci a použití [příkazového řádku Azure CLI 2.0](/cli/azure) nástroj příkazového řádku. Můžete ho používat v prohlížeči pomocí služby [Azure Cloud Shell](../cloud-shell/overview.md) nebo nainstalovat na místním počítači a používat ho v jakékoli relaci PowerShellu. Chcete-li povolit šifrování na existující nebo běžící virtuální počítače IaaS s Linuxem v Azure, použijte následující příkazy rozhraní příkazového řádku:

Použití [az vm encryption povolit](/cli/azure/vm/encryption#az-vm-encryption-enable) příkaz, který povoluje šifrování na spuštěný virtuální počítač IaaS v Azure.

-  **Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Šifrování spuštěného virtuálního počítače pomocí KEK zabalit tajný kód klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Syntaxe pro hodnotu parametru disk šifrování – trezor klíčů je úplný identifikátor řetězce: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
Syntaxe pro hodnoty parametru klíč šifrovacího klíče je úplný identifikátor URI klíče KEK jako v: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ověřte, disky jsou šifrované:** Pokud chcete zkontrolovat stav šifrování virtuálního počítače IaaS, použijte [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) příkazu. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Zakázat šifrování:** zakázat šifrování, použijte [az vm encryption zakázat](/cli/azure/vm/encryption#az-vm-encryption-disable) příkazu. Zakázáním šifrování je povolena pouze u svazků s daty pro virtuální počítače s Linuxem.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a> Povoluje šifrování na existující nebo spuštěného virtuálního počítače s Linuxem pomocí Powershellu
Použití [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) rutina pro povolení šifrování u spuštěného virtuálního počítače IaaS v Azure. 

-  **Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta:** níže uvedený skript inicializuje proměnných a spustí rutinu Set-AzureRmVMDiskEncryptionExtension. Skupina prostředků, virtuální počítač, trezor klíčů, aplikace AAD a tajný kód klienta by měl již byly vytvořeny jako požadavky. Nahraďte hodnoty MySecureRg MySecureVM, MySecureVault, My-AAD-client-ID a My-AAD-client-secret. Budete muset přidat parametr - VolumeType Pokud šifrujete datové disky, nikoli disk s operačním systémem. 

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Šifrování spuštěného virtuálního počítače pomocí KEK zabalit tajný kód klienta:** Azure Disk Encryption umožňuje určit existujícího klíče v trezoru klíčů při zabalení šifrování tajných kódů disku, které byly generovány při povolení šifrování. Pokud je zadaný šifrovací klíč klíče, Azure Disk Encryption používá tento klíč k šifrování tajných kódů zabalení před zápisem do služby Key Vault. Budete muset přidat parametr - VolumeType Pokud šifrujete datové disky, nikoli disk s operačním systémem. 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

    >[!NOTE]
    > Syntaxe pro hodnotu parametru disk šifrování – trezor klíčů je úplný identifikátor řetězce: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxe pro hodnoty parametru klíč šifrovacího klíče je úplný identifikátor URI klíče KEK jako v: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Ověřte, disky jsou šifrované:** Pokud chcete zkontrolovat stav šifrování virtuálního počítače IaaS, použijte [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) rutiny. 
    
     ```azurepowershell-interactive 
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName MySecureRg -VMName MySecureVM
     ```
    
- **Zakázat šifrování disku:** můžete zakázat šifrování, použijte [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) rutiny. Zakázáním šifrování je povolena pouze u svazků s daty pro virtuální počítače s Linuxem.
     
     ```azurepowershell-interactive 
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Povoluje šifrování na existující nebo spuštěné IaaS virtuálního počítače s Linuxem pomocí šablony

Můžete povolit šifrování disku v existující nebo spuštěné IaaS virtuálního počítače s Linuxem v Azure pomocí [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Klikněte na tlačítko **nasadit do Azure** na šablony Azure pro rychlý start.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, parametry, právní podmínky a smlouvy. Klikněte na tlačítko **vytvořit** chcete povolit šifrování na existující nebo spuštěného virtuálního počítače IaaS.

V následující tabulce jsou uvedeny parametry šablony Resource Manageru pro existující nebo spouštění virtuálních počítačů, které používají ID klienta Azure AD:

| Parametr | Popis |
| --- | --- |
| AADClientID | ID klienta aplikace Azure AD, který má oprávnění k zápisu tajných klíčů do služby key vault. |
| AADClientSecret | Tajný kód klienta aplikace Azure AD, který má oprávnění k zápisu tajných klíčů do trezoru klíčů. |
| keyVaultName | Název, který klíč musí být nahrán do trezoru klíčů. Můžete ho získat pomocí příkazu Azure CLI `az keyvault show --name "MySecureVault" --query resourceGroup`. |
|  KeyEncryptionKeyURL | Adresa URL šifrovací klíč klíče, který se používá k šifrování vygenerovaný klíč. Tento parametr je nepovinný, pokud vyberete **nokek** v rozevíracím seznamu UseExistingKek. Pokud vyberete **kek** v rozevíracím seznamu UseExistingKek, je nutné zadat _keyEncryptionKeyURL_ hodnotu. |
| VolumeType | Typ svazku, který provádí operace šifrování na. Platné podporované hodnoty jsou _OS_ nebo _všechny_ (viz podporované distribuce Linuxu a jejich verze pro operační systém a datové disky v požadované části výše). |
| SequenceVersion | Pořadí verze Bitlockeru operace. Toto číslo verze postupně zvyšuje vždy, když je provedena operace šifrování disku ve stejném virtuálním počítači. |
| vmName | Název virtuálního počítače, který má být proveden na operace šifrování. |
| přístupové heslo | Jako datový šifrovací klíč, zadejte silné heslo. |



## <a name="bkmk_EFA"> </a>Pomocí funkce encryptformatall šifrovaně pro datové disky na virtuální počítače IaaS s Linuxem
**Encryptformatall šifrovaně** parametr snižuje čas potřebný pro Linux datových disků k šifrování. Oddíly, které splňují určitá kritéria, bude ve formátu (s její aktuální systém souborů). Pak se budete znovu připojeny zpátky na místo, kde byla před provedením příkazu. Pokud budete chtít vyloučit datový disk, který splňuje kritéria, můžete jej odpojte před spuštěním příkazu.

 Po spuštění tohoto příkazu, jednotky, na kterých byly připojené dříve naformátovaný. Pak vrstvě šifrování bude spuštěna na jednotce teď prázdný. Pokud je vybraná tato možnost, budou se šifrovat taky dočasných prostředků disku připojeného k virtuálnímu počítači. Pokud na dočasné jednotce se vynuluje, bude přeformátovali a znovu zašifrována pomocí řešení Azure Disk Encryption při nejbližší příležitosti pro virtuální počítač.

>[!WARNING]
> Encryptformatall šifrovaně se nemá používat, když je potřebná data na svazcích data Virtuálního počítače. Podle jejich odpojení může vyloučit disky z šifrování. By měly nejprve vyzkoušet encryptformatall šifrovaně nejprve na testovacím virtuálním počítači, parametr funkce a její nepřímo před pokusem o produkční virtuální počítač. Možnost encryptformatall šifrovaně formátuje datový disk a všechna data na ni se ztratí. Než budete pokračovat, ověřte, že disky, které chcete vyloučit správně unmouted. </br></br>
 >Pokud nastavení tohoto parametru během aktualizace nastavení šifrování může vést k restartování před skutečné šifrování. V takovém případě také můžete k odebrání disku, které nechcete formátovaný ze souboru fstab. Podobně měli byste přidat oddíl, který chcete zašifrovat ve formátu souboru fstab před inicializací operace šifrování. 

### <a name="bkmk_EFACriteria"> </a> Kritéria encryptformatall šifrovaně
Parametr přejde ale všechny oddíly a zašifruje tak dlouho, dokud nebudou splňovat **všechny** z následujících kritérií: 
- Není kořenový/operačního systému a spouštěcí oddíl
- Už není šifrovaný
- Není klíče BEK svazek
- Je připojený


### <a name="bkmk_EFATemplate"> </a> Použijte parametr encryptformatall šifrovaně pomocí šablony
Možnost encryptformatall šifrovaně použít všechny existující šablonu Azure Resource Manageru, který šifruje virtuálního počítače s Linuxem a změnit **EncryptionOperation** pole AzureDiskEncryption prostředku.

1. Jako příklad, použijte [šablony Resource Manageru k šifrování spuštěného virtuálního počítače s Linuxem IaaS](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Klikněte na tlačítko **nasadit do Azure** na šablony Azure pro rychlý start.
3. Změnit **EncryptionOperation** z **EnableEncryption** k **EnableEncryptionFormatAl**
4. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, další parametry, právní podmínky a smlouvy. Klikněte na tlačítko **vytvořit** chcete povolit šifrování na existující nebo spuštěného virtuálního počítače IaaS.


### <a name="bkmk_EFAPSH"> </a> Použijte parametr encryptformatall šifrovaně pomocí rutiny Powershellu
Použití [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) rutinu s [parametr encryptformatall šifrovaně](https://www.powershellgallery.com/packages/AzureRM/5.0.0). 

**Šifrování spuštěného virtuálního počítače pomocí tajný klíč klienta a encryptformatall šifrovaně:** například níže uvedený skript inicializuje proměnných a spustí rutinu Set-AzureRmVMDiskEncryptionExtension s parametrem encryptformatall šifrovaně. Skupina prostředků, virtuální počítač, trezor klíčů, aplikace AAD a tajný kód klienta by měl již byly vytvořeny jako požadavky. Nahraďte hodnoty MySecureRg MySecureVM, MySecureVault, My-AAD-client-ID a My-AAD-client-secret.
  
   ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MySecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="bkmk_EFALVM"> </a> Použijte parametr encryptformatall šifrovaně s Správce logických svazků (LVM) 
Doporučujeme, abyste nastavení LVM-na crypt. Všechny tyto příklady nahraďte cesta zařízení a přípojné body cokoli, co vyhovuje vašemu případu použití. Toto nastavení lze provést následujícím způsobem:

- Přidáte datové disky, které budou tvoří virtuální počítač.
- Formátování, připojování a přidat tyto disky do souboru fstab.

    1. Naformátujte nově přidaný disk. Využíváme Azure vygeneruje zde symbolických odkazů. Problémy související se změny názvů zařízení pomocí symbolických odkazů se vyhnete. Další informace najdete v tématu [Poradce při potížích s názvy zařízení problémy](../virtual-machines/linux/troubleshoot-device-names-problems.md) článku.
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Připojte disky.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Přidejte do fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Spusťte rutinu prostředí PowerShell Set-AzureRmVMDiskEncryptionExtension s - encryptformatall šifrovaně k zašifrování těchto disků.
         ```azurepowershell-interactive
         Set-AzureRmVMDiskEncryptionExtension -ResouceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Nastavte LVM nad tyto nové disky. Všimněte si, že po dokončení spuštění virtuálního počítače jsou odemknutí zašifrované jednotky. Připojení LVM tedy také muset následně zpozdit.




## <a name="bkmk_VHDpre"> </a> Nové virtuální počítače IaaS vytvořené z šifrované zákazníka virtuální pevný disk, šifrovacích klíčů
V tomto scénáři můžete povolit šifrování pomocí šablony Resource Manageru, rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku. Následující části popisují podrobněji šablonu Resource Manageru a příkazech rozhraní příkazového řádku. 

Postupujte podle pokynů v dodatku k přípravě předem šifrované imagí, které lze použít v Azure. Po vytvoření image můžete použít kroky v další části vytvořit šifrovaný virtuální počítač Azure.

* [Příprava virtuálního pevného disku předem šifrované Windows](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Připravit předem šifrované linuxového virtuálního pevného disku](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Je nezbytně nutné k snímku a/nebo zálohování spravovaného disku na základě instance virtuálního počítače mimo a před povolením Azure Disk Encryption. Snímek spravovaného disku může být přijata z portálu, nebo [Azure Backup](../backup/backup-azure-vms-encryption.md) lze použít. Zálohy Ujistěte se, že možnost obnovení je možné v případě jakékoli došlo k neočekávané chybě při šifrování. Po provedení zálohy rutinu Set-AzureRmVMDiskEncryptionExtension slouží k šifrování zadáním parametru - skipVmBackup spravované disky. Příkaz Set-AzureRmVMDiskEncryptionExtension selže ve virtuálních počítačích spravovaných disků na základě dokud provedl zálohování a tento parametr nebyl zadán. 
>
>Šifrování nebo zakázáním šifrování může způsobit restartování virtuálního počítače. 



### <a name="bkmk_VHDprePSH"> </a> Šifrování virtuálních počítačů IaaS s virtuálními pevnými disky předem šifrované pomocí Azure Powershellu 
Můžete povolit šifrování disku na virtuální pevný disk šifrovaný pomocí rutiny prostředí PowerShell [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples). Následující příklad obsahuje některé společné parametry. 

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResouceGroupName "MySecureRG"
```

### <a name="bkmk_VHDpreRM"> </a> Pomocí šablony Resource Manageru k šifrování virtuálních počítačů IaaS s předem šifrované virtuální pevné disky 
Můžete povolit šifrování disku na virtuální pevný disk šifrovaný pomocí [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. Na šablony Azure pro rychlý start, klikněte na tlačítko **nasadit do Azure**.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, parametry, právní podmínky a smlouvy. Klikněte na tlačítko **vytvořit** chcete povolit šifrování na nový virtuální počítač IaaS.

Následující tabulka uvádí parametry šablony Resource Manageru pro šifrované virtuální pevný disk:

| Parametr | Popis |
| --- | --- |
| newStorageAccountName | Název účtu úložiště pro uložení šifrovaného virtuálního pevného disku operačního systému. Tento účet úložiště by měl již byly vytvořeny ve stejné skupině prostředků a stejném umístění jako virtuální počítač. |
| osVhdUri | Identifikátor URI virtuálního pevného disku operačního systému z účtu úložiště. |
| osType | Typ produktu operačního systému (Windows/Linux). |
| virtualNetworkName | Název virtuální sítě, síťové karty virtuálního počítače by měly patřit do. Název by měl již byly vytvořeny ve stejné skupině prostředků a stejném umístění jako virtuální počítač. |
| subnetName | Název podsítě ve virtuální síti, která síťové karty virtuálního počítače by měly patřit do. |
| vmSize | Velikost virtuálního počítače. V současné době jsou podporovány pouze standardní A, D a G series. |
| keyVaultResourceID | ID prostředku, který identifikuje prostředek trezoru klíčů v Azure Resource Manageru. Můžete ho získat pomocí rutiny prostředí PowerShell `(Get-AzureRmKeyVault -VaultName &lt;MyKeyVaultName&gt; -ResourceGroupName &lt;MyResourceGroupName&gt;).ResourceId` nebo pomocí příkazového řádku Azure `az keyvault show --name "MySecureVault" --query id`|
| keyVaultSecretUrl | Adresa URL klíče šifrování disku, který je nastavený v trezoru klíčů. |
| keyVaultKekUrl | Adresa URL šifrovací klíč klíče pro šifrování klíč vygenerovaný šifrování disku. |
| vmName | Název virtuálního počítače IaaS. |
## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Povoluje šifrování na nově přidaných datového disku
Můžete přidat nový disk data pomocí [az vm disk attach](../virtual-machines/linux/add-disk.md), nebo [prostřednictvím webu Azure portal](../virtual-machines/linux/attach-disk-portal.md). Předtím, než je možné šifrovat, musíte nejdřív připojit nově připojený datový disk. Musíte požádat o šifrování datovou jednotku, protože jednotka nepoužitelný, zatímco probíhá šifrování. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Povoluje šifrování na nově přidaný disk pomocí Azure CLI
 Pomocí příkazu Azure CLI automaticky poskytne nové verze pořadí, při spuštění příkazu povolit šifrování. 
-  **Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Šifrování spuštěného virtuálního počítače pomocí KEK zabalit tajný kód klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Povoluje šifrování na nově přidaný disk pomocí Azure Powershellu
 Při použití Powershellu k šifrování nový disk pro Linux, je potřeba zadat novou verzi pořadí. Pořadí verze musí být jedinečný. Níže uvedený skript vytvoří identifikátor GUID verze pořadí. 
 

-  **Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta:** níže uvedený skript inicializuje proměnných a spustí rutinu Set-AzureRmVMDiskEncryptionExtension. Skupina prostředků, virtuální počítač, trezor klíčů, aplikace AAD a tajný kód klienta by měl již byly vytvořeny jako požadavky. Nahraďte hodnoty MySecureRg MySecureVM, MySecureVault, My-AAD-client-ID a My-AAD-client-secret. Parametr - VolumeType je nastaven na datové disky, nikoli disk s operačním systémem. 

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
    ```
- **Šifrování spuštěného virtuálního počítače pomocí KEK zabalit tajný kód klienta:** Azure Disk Encryption umožňuje určit existujícího klíče v trezoru klíčů při zabalení šifrování tajných kódů disku, které byly generovány při povolení šifrování. Pokud je zadaný šifrovací klíč klíče, Azure Disk Encryption používá tento klíč k šifrování tajných kódů zabalení před zápisem do služby Key Vault. Budete muset přidat parametr - VolumeType Pokud šifrujete datové disky, nikoli disk s operačním systémem. 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

    >[!NOTE]
    > Syntaxe pro hodnotu parametru disk šifrování – trezor klíčů je úplný identifikátor řetězce: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxe pro hodnoty parametru klíč šifrovacího klíče je úplný identifikátor URI klíče KEK jako v: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Zakázat šifrování pro virtuální počítače s Linuxem
Můžete zakázat šifrování pomocí Azure Powershellu, rozhraní příkazového řádku Azure nebo pomocí šablony Resource Manageru. 

>[!IMPORTANT]
>Zakázáním šifrování v Azure Disk Encryption na virtuální počítače s Linuxem je podporována pouze pro datové svazky. Není podporován na data nebo svazků operačního systému, pokud byla dříve zašifrována svazek s operačním systémem.  

- **Zakázat disk encryption pomocí Azure Powershellu:** můžete zakázat šifrování, použijte [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) rutiny. 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Zakázat šifrování pomocí Azure CLI:** zakázat šifrování, použijte [az vm encryption zakázat](/cli/azure/vm/encryption#az-vm-encryption-disable) příkazu. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **Zakázat šifrování pomocí šablony Resource Manageru:** použití [zakažte šifrování u spuštěného virtuálního počítače s Linuxem](https://aka.ms/decrypt-linuxvm) šablony můžete zakázat šifrování.
     1. Klikněte na **Deploy to Azure** (Nasadit do Azure).
     2. Vyberte předplatné, skupinu prostředků, umístění, virtuálních počítačů, právní podmínky a smlouvy.
     3.  Klikněte na tlačítko **nákupní** můžete zakázat šifrování disků spuštěného virtuálního počítače Windows. 


## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Aktivace Azure Disk Encryption pro Windows](azure-security-disk-encryption-windows-aad.md)
