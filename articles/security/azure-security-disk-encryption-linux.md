---
title: Aktivace Azure Disk Encryption pro virtuální počítače s Linuxem v režimu IaaS
description: Tento článek obsahuje pokyny týkající se povolení Microsoft Azure Disk Encryption pro virtuální počítače IaaS s Linuxem.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 04/05/2019
ms.custom: seodec18
ms.openlocfilehash: 624ad22b1c63498e8ce936472cfc884910bc6f84
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59276945"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms"></a>Aktivace Azure Disk Encryption pro virtuální počítače s Linuxem v režimu IaaS 

Můžete povolit řadu scénářů šifrování disku a kroků může lišit v závislosti scénáři. Následující části se věnují scénáře podrobněji pro virtuální počítače IaaS s Linuxem. Než budete moct použít šifrování disku, [požadavky Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) musíte provést a [další požadavky pro virtuální počítače IaaS s Linuxem](azure-security-disk-encryption-prerequisites.md#bkmk_LinuxPrereq) části byste měli zkontrolovat.

Přijmout [snímku](../virtual-machines/windows/snapshot-copy-managed-disk.md) nebo před disky jsou šifrované zálohovat. Zálohy Ujistěte se, že možnost obnovení je možné, pokud dojde k neočekávané chybě při šifrování. Virtuální počítače se spravovanými disky vyžadují zálohu, než dojde k šifrování. Po provedení zálohy můžete použít rutinu Set-AzVMDiskEncryptionExtension zadáním parametru - skipVmBackup šifrování spravované disky. Další informace o tom, jak zálohování a obnovení šifrovaných virtuálních počítačů najdete v tématu [Azure Backup](../backup/backup-azure-vms-encryption.md) článku. 

>[!WARNING]
> - Pokud jste už dřív použili [Azure Disk Encryption pomocí Azure AD app](azure-security-disk-encryption-prerequisites-aad.md) pro šifrování tento virtuální počítač, budete muset pokračovat tuto možnost použijte k šifrování virtuálního počítače. Nemůžete použít [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) na tento šifrovaný virtuální počítač jako tato akce není podporovaný scénář význam přepnutí mimo aplikaci AAD pro tento šifrovaný virtuální počítač se zatím nepodporuje.
 > - Azure Disk Encryption musí být umístěné ve stejné oblasti služby Key Vault a virtuální počítače. Vytvoření a použití služby Key Vault, která je ve stejné oblasti jako virtuální počítač k šifrování.
> - Při šifrování svazků operačního systému Linux, virtuální počítač by měl být považuje za nedostupnou. Důrazně doporučujeme, aby se zabránilo přihlášení SSH, Probíhá šifrování abyste předešli problémům s blokuje všechny otevřené soubory, které bude třeba je získávat během procesu šifrování. Chcete-li zkontrolovat průběh, [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) nebo [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) příkazy lze použít. Tento proces můžete očekávat, že trvat několik hodin pro svazek operačního systému 30GB a další čas pro šifrování svazků s daty. Doba šifrování objem dat se přímo úměrná velikosti a množství datové svazky Pokud všechny možnost šifrovat formátu se používá. 
> - Zakázáním šifrování na virtuální počítače s Linuxem je podporována pouze pro datové svazky. Není podporován na data nebo svazků operačního systému, pokud byla dříve zašifrována svazek s operačním systémem.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningLinux"> </a> Povoluje šifrování na existující nebo spuštěné IaaS virtuálního počítače s Linuxem
V tomto scénáři můžete povolit šifrování pomocí šablony Resource Manageru, rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku. Pokud potřebujete informace o schématu pro rozšíření virtuálního počítače, přečtěte si [Azure Disk Encryption pro Linuxové rozšíření](../virtual-machines/extensions/azure-disk-enc-linux.md) článku.

>[!IMPORTANT]
 >Je nezbytně nutné k snímku a/nebo zálohování spravovaného disku na základě instance virtuálního počítače mimo a před povolením Azure Disk Encryption. Snímek spravovaného disku může být přijata z portálu, nebo [Azure Backup](../backup/backup-azure-vms-encryption.md) lze použít. Zálohy Ujistěte se, že možnost obnovení je možné v případě jakékoli došlo k neočekávané chybě při šifrování. Po provedení zálohy rutinu Set-AzVMDiskEncryptionExtension slouží k šifrování zadáním parametru - skipVmBackup spravované disky. Příkaz Set-AzVMDiskEncryptionExtension selže ve virtuálních počítačích spravovaných disků na základě dokud provedl zálohování a tento parametr nebyl zadán. 
>
>Šifrování nebo zakázáním šifrování může způsobit restartování virtuálního počítače. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Povoluje šifrování na existující nebo spuštěného virtuálního počítače s Linuxem pomocí Azure CLI 
Můžete povolit šifrování disku na virtuální pevný disk šifrovaný po instalaci a použití [příkazového řádku Azure CLI 2.0](/cli/azure) nástroj příkazového řádku. Můžete ho používat v prohlížeči pomocí služby [Azure Cloud Shell](../cloud-shell/overview.md) nebo nainstalovat na místním počítači a používat ho v jakékoli relaci PowerShellu. Chcete-li povolit šifrování na existující nebo běžící virtuální počítače IaaS s Linuxem v Azure, použijte následující příkazy rozhraní příkazového řádku:

Použití [az vm encryption povolit](/cli/azure/vm/encryption#az-vm-encryption-enable) příkaz, který povoluje šifrování na spuštěný virtuální počítač IaaS v Azure.

- **Šifrování spuštěného virtuálního počítače:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Šifrování pomocí KEK spuštěného virtuálního počítače:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Syntaxe pro hodnotu parametru disk šifrování – trezor klíčů je úplný identifikátor řetězce: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
Syntaxe pro hodnoty parametru klíč šifrovacího klíče je úplný identifikátor URI klíče KEK jako v: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ověřte, zda že jsou zašifrovány disky:** Chcete-li zkontrolovat stav šifrování virtuálního počítače IaaS, použijte [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) příkazu. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Zakážete šifrování:** Pokud chcete zakázat šifrování, použijte [az vm encryption zakázat](/cli/azure/vm/encryption#az-vm-encryption-disable) příkazu. Zakázáním šifrování je povolena pouze u svazků s daty pro virtuální počítače s Linuxem.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a> Povoluje šifrování na existující nebo spuštěného virtuálního počítače s Linuxem pomocí Powershellu
Použití [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) rutina pro povolení šifrování u spuštěného virtuálního počítače IaaS v Azure. Přijmout [snímku](../virtual-machines/windows/snapshot-copy-managed-disk.md) nebo zálohování virtuálního počítače s [Azure Backup](../backup/backup-azure-vms-encryption.md) před disky jsou šifrované. Parametr - skipVmBackup je už zadaná v skripty prostředí PowerShell k šifrování spuštěného virtuálního počítače s Linuxem.

-  **Šifrování spuštěného virtuálního počítače:** Níže uvedený skript inicializuje proměnných a spustí rutinu Set-AzVMDiskEncryptionExtension. Skupinu prostředků, virtuálního počítače a služby key vault, musí již byly vytvořeny jako požadavky. Nahraďte hodnoty MyVirtualMachineResourceGroup MySecureVM a MySecureVault. Upravte parametr - VolumeType k určení, které disky, který šifrujete.

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
- **Šifrování pomocí KEK spuštěného virtuálního počítače:** Budete muset přidat parametr - VolumeType Pokud šifrujete datové disky, nikoli disk s operačním systémem. 

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
    
- **Ověřte, zda že jsou zašifrovány disky:** Chcete-li zkontrolovat stav šifrování virtuálního počítače IaaS, použijte [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) rutiny. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Zakážete šifrování disku:** Pokud chcete zakázat šifrování, použijte [zakázat AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) rutiny. Zakázáním šifrování je povolena pouze u svazků s daty pro virtuální počítače s Linuxem.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Povoluje šifrování na existující nebo spuštěné IaaS virtuálního počítače s Linuxem pomocí šablony

Můžete povolit šifrování disku v existující nebo spuštěné IaaS virtuálního počítače s Linuxem v Azure pomocí [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Klikněte na tlačítko **nasadit do Azure** na šablony Azure pro rychlý start.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, parametry, právní podmínky a smlouvy. Klikněte na tlačítko **vytvořit** chcete povolit šifrování na existující nebo spuštěného virtuálního počítače IaaS.

V následující tabulce jsou uvedeny parametry šablony Resource Manageru pro existující nebo spouštění virtuálních počítačů:

| Parametr | Popis |
| --- | --- |
| vmName | Název virtuálního počítače ke spuštění operace šifrování. |
| keyVaultName | Název, který klíč Bitlockeru, musí být nahrán do trezoru klíčů. Můžete ho získat pomocí rutiny `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` nebo příkazového řádku Azure `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`|
| keyVaultResourceGroup | Název skupiny prostředků obsahující trezor klíčů|
|  KeyEncryptionKeyURL | Adresa URL šifrovací klíč klíče, který se používá k šifrování vygenerovaný klíč Bitlockeru. Tento parametr je nepovinný, pokud vyberete **nokek** v rozevíracím seznamu UseExistingKek. Pokud vyberete **kek** v rozevíracím seznamu UseExistingKek, je nutné zadat _keyEncryptionKeyURL_ hodnotu. |
| VolumeType | Typ svazku, který provádí operace šifrování na. Platné hodnoty jsou _OS_, _Data_, a _všechny_. 
| forceUpdateTag | Pokaždé, když se operace musí být vynutit spuštění se předá jedinečnou hodnotu jako identifikátor GUID. |
| resizeOSDisk | Oddíl operačního systému velikost tak, aby obsadily úplný operační systém virtuálního pevného disku před rozdělením systémový svazek. |
| location | Umístění pro všechny prostředky. |



## <a name="encrypt-virtual-machine-scale-sets"></a>Šifrování škálovací sady virtuálních počítačů
[Škálovací sady virtuálních počítačů Azure](../virtual-machine-scale-sets/overview.md) umožňují vytvářet a spravovat skupiny identických, virtuálních počítačů s vyrovnáváním zatížení. Počet instancí virtuálních počítačů se může automaticky zvyšovat nebo snižovat s ohledem na požadavky nebo definovaný plán. K šifrování škálovací sady virtuálních počítačů pomocí Azure Powershellu nebo rozhraní příkazového řádku. Šifrování jenom datových disků je podporována v systému Linux škálovací sady virtuálních počítačů.

Příklad souboru služby batch pro Linux škálovací sady dat šifrování disku najdete [tady](https://github.com/Azure-Samples/azure-cli-samples/tree/master/disk-encryption/vmss). Tento příklad vytvoří skupinu prostředků, škálovací sady pro Linux, připojí disk 5 GB dat a šifruje škálovací sadu virtuálních počítačů.

### <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Šifrování virtuálního počítače škálovací sady pomocí Azure CLI

Použití [povolit šifrování az vmss](/cli/azure/vmss/encryption#az-vmss-encryption-enable) chcete povolit šifrování na škálovací sadu virtuálních počítačů s Windows. Pokud nastavíte zásady upgradu pro škálovací sady na ruční, spusťte šifrování s [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). Skupinu prostředků, virtuální počítač a trezor klíčů by již byly vytvořeny jako požadavky. 

-  **Šifrování běžící škálovací sady virtuálních počítačů**
    ```azurecli-interactive
    az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --volume-type DATA --disk-encryption-keyvault "MySecureVault"
    ```

-  **Šifrování spuštěného virtuálního počítače škálovací sady s použitím KEK zabalit klíč**
     ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --volume-type DATA --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault"
     ```

    >[!NOTE]
    > Syntaxe pro hodnotu parametru disk šifrování – trezor klíčů je úplný identifikátor řetězce: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxe pro hodnoty parametru klíč šifrovacího klíče je úplný identifikátor URI klíče KEK jako v: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Načíst stav šifrování pro škálovací sadu virtuálních počítačů:** Použití [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
    az vmss encryption show --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

- **Zakažte šifrování u škálovací sady virtuálních počítačů**: Použití [zakázat šifrování az vmss](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
    az vmss encryption disable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Šifrování virtuálního počítače škálovací sady pomocí Azure Powershellu

Použití [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/set-azvmssdiskencryptionextension) rutiny, které chcete povolit šifrování na škálovací sadu virtuálních počítačů s Windows. Skupinu prostředků, virtuální počítač a trezor klíčů by již byly vytvořeny jako požadavky.

-  **Šifrování běžící škálovací sady virtuálních počítačů**:
      ```powershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
      $VmssName = "MySecureVmss";
      $KeyVaultName= "MySecureVault";
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -VolumeType Data -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
      ```

-  **Šifrování spuštěného virtuálního počítače škálovací sady s použitím KEK zabalit klíč**:
      ```powershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
      $VmssName = "MySecureVmss";
      $KeyVaultName= "MySecureVault";
      $keyEncryptionKeyName = "MyKeyEncryptionKey";
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $KeyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -VolumeType Data -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl  -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $KeyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
      ```

    >[!NOTE]
    > Syntaxe pro hodnotu parametru disk šifrování – trezor klíčů je úplný identifikátor řetězce: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxe pro hodnoty parametru klíč šifrovacího klíče je úplný identifikátor URI klíče KEK jako v: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Načíst stav šifrování pro sadu virtuálních počítačů**: Použití [Get-AzVmssVMDiskEncryption](/powershell/module/az.compute/get-azvmssvmdiskencryption) rutiny.
    
    ```powershell
    Get-AzVmssVMDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

- **Zakažte šifrování u škálovací sady virtuálních počítačů**: Použití [zakázat AzVmssDiskEncryption](/powershell/module/az.compute/disable-azvmssdiskencryption) rutiny. 

    ```powershell
    Disable-AzVmssDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-linux-virtual-machine-scale-sets"></a>Nastaví šablony Azure Resource Manageru pro škálování virtuálních počítačů Linux

Šifrování nebo dešifrování Linux virtuálního počítače škálovací sady, pomocí šablon Azure Resource Manageru a následující pokyny:

- [Povoluje šifrování na škálovací sadu virtuálních počítačů s Linuxem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)
- [Zakažte šifrování u škálovací sady virtuálních počítačů pro Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

     1. Klikněte na **Deploy to Azure** (Nasadit do Azure).
     2. Vyplňte požadovaná pole pak svůj souhlas s podmínkami a ujednáními.
     3. Klikněte na tlačítko **nákupní** k nasazení šablony.

## <a name="bkmk_EFA"> </a>Pomocí funkce encryptformatall šifrovaně pro datové disky na virtuální počítače IaaS s Linuxem

**Encryptformatall šifrovaně** parametr snižuje čas potřebný pro Linux datových disků k šifrování. Oddíly, které splňují určitá kritéria, bude ve formátu (s její aktuální systém souborů). Pak se budete znovu připojeny zpátky na místo, kde byla před provedením příkazu. Pokud budete chtít vyloučit datový disk, který splňuje kritéria, můžete jej odpojte před spuštěním příkazu.

 Po spuštění tohoto příkazu, jednotky, na kterých byly připojené dříve naformátovaný. Pak vrstvě šifrování bude spuštěna na jednotce teď prázdný. Pokud je vybraná tato možnost, budou se šifrovat taky dočasných prostředků disku připojeného k virtuálnímu počítači. Pokud na dočasné jednotce se vynuluje, bude přeformátovali a znovu zašifrována pomocí řešení Azure Disk Encryption při nejbližší příležitosti pro virtuální počítač.

>[!WARNING]
> Encryptformatall šifrovaně se nemá používat, když je potřebná data na svazcích data Virtuálního počítače. Podle jejich odpojení může vyloučit disky z šifrování. By měly nejprve vyzkoušet encryptformatall šifrovaně nejprve na testovacím virtuálním počítači, parametr funkce a její nepřímo před pokusem o produkční virtuální počítač. Možnost encryptformatall šifrovaně formátuje datový disk a všechna data na ni se ztratí. Než budete pokračovat, ověřte, že jsou správně odpojené disky, které chcete vyloučit. </br></br>
 >Pokud nastavení tohoto parametru během aktualizace nastavení šifrování může vést k restartování před skutečné šifrování. V takovém případě také můžete k odebrání disku, které nechcete formátovaný ze souboru fstab. Podobně měli byste přidat oddíl, který chcete zašifrovat ve formátu souboru fstab před inicializací operace šifrování. 

### <a name="bkmk_EFACriteria"> </a> Kritéria encryptformatall šifrovaně
Parametr přejde ale všechny oddíly a zašifruje tak dlouho, dokud nebudou splňovat **všechny** z následujících kritérií: 
- Není kořenový/operačního systému a spouštěcí oddíl
- Už není šifrovaný
- Není klíče BEK svazek
- Není svazek RAID
- Není svazku LVM
- Je připojený

Šifrování disků, které tvoří svazek RAID nebo LVM spíše než svazek RAID nebo LVM.

### <a name="bkmk_EFAPSH"> </a> Použijte parametr encryptformatall šifrovaně pomocí Azure CLI
Použití [az vm encryption povolit](/cli/azure/vm/encryption#az-vm-encryption-enable) příkaz, který povoluje šifrování na spuštěný virtuální počítač IaaS v Azure.

-  **Šifrování pomocí encryptformatall šifrovaně spuštěného virtuálního počítače:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="bkmk_EFAPSH"> </a> Použijte parametr encryptformatall šifrovaně pomocí rutiny Powershellu
Použití [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) rutinu s parametrem encryptformatall šifrovaně. 

**Šifrování pomocí encryptformatall šifrovaně spuštěného virtuálního počítače:** Níže uvedený skript jako příklad inicializuje proměnných a spustí rutinu Set-AzVMDiskEncryptionExtension s parametrem encryptformatall šifrovaně. Skupinu prostředků, virtuální počítač a trezor klíčů by již byly vytvořeny jako požadavky. Nahraďte hodnoty MyVirtualMachineResourceGroup MySecureVM a MySecureVault.
  
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
    
    4. Spusťte rutinu prostředí PowerShell Set-AzVMDiskEncryptionExtension s - encryptformatall šifrovaně k zašifrování těchto disků.
         ```azurepowershell-interactive
         Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Nastavte LVM nad tyto nové disky. Všimněte si, že po dokončení spuštění virtuálního počítače jsou odemknutí zašifrované jednotky. Připojení LVM tedy také muset následně zpozdit.


## <a name="bkmk_VHDpre"> </a> Nové virtuální počítače IaaS vytvořené z šifrované zákazníka virtuální pevný disk, šifrovacích klíčů
V tomto scénáři můžete povolit šifrování pomocí rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku. 

Postupujte podle pokynů v dodatku k přípravě předem šifrované imagí, které lze použít v Azure. Po vytvoření image můžete použít kroky v další části vytvořit šifrovaný virtuální počítač Azure.

* [Příprava virtuálního pevného disku předem šifrované Windows](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Připravit předem šifrované linuxového virtuálního pevného disku](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Je nezbytně nutné k snímku a/nebo zálohování spravovaného disku na základě instance virtuálního počítače mimo a před povolením Azure Disk Encryption. Snímek spravovaného disku může být přijata z portálu, nebo [Azure Backup](../backup/backup-azure-vms-encryption.md) lze použít. Zálohy Ujistěte se, že možnost obnovení je možné v případě jakékoli došlo k neočekávané chybě při šifrování. Po provedení zálohy rutinu Set-AzVMDiskEncryptionExtension slouží k šifrování zadáním parametru - skipVmBackup spravované disky. Příkaz Set-AzVMDiskEncryptionExtension selže ve virtuálních počítačích spravovaných disků na základě dokud provedl zálohování a tento parametr nebyl zadán. 
>
>Šifrování nebo zakázáním šifrování může způsobit restartování virtuálního počítače. 



### <a name="bkmk_VHDprePSH"> </a> Šifrování virtuálních počítačů IaaS s virtuálními pevnými disky předem šifrované pomocí Azure Powershellu 
Můžete povolit šifrování disku na virtuální pevný disk šifrovaný pomocí rutiny prostředí PowerShell [Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). Následující příklad obsahuje některé společné parametry. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Povoluje šifrování na nově přidaných datového disku

Můžete přidat nový disk data pomocí [az vm disk attach](../virtual-machines/linux/add-disk.md), nebo [prostřednictvím webu Azure portal](../virtual-machines/linux/attach-disk-portal.md). Předtím, než je možné šifrovat, musíte nejdřív připojit nově připojený datový disk. Musíte požádat o šifrování datovou jednotku, protože jednotka nepoužitelný, zatímco probíhá šifrování. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Povoluje šifrování na nově přidaný disk pomocí Azure CLI

 Pokud virtuální počítač byla dříve zašifrována pomocí "Vše" pak--svazek typ parametru by měla zůstat všechny. Zahrnuje všechny disky s operačním systémem a daty. Pokud virtuální počítač byla dříve zašifrována s typem svazek "OS", pak--typ svazku parametr by měl být změněn na všechny tak, aby operační systém i nový datový disk budou zahrnuty. Pokud virtuální počítač je zašifrovaný pomocí pouze typ svazku "Data", pak ho může zůstat "Data" jak je znázorněno níže. Přidání a připojení nového datového disku k virtuálnímu počítači není dostatek Příprava pro šifrování. Musí být také nově připojený disk ve formátu a správně připojeny virtuální počítač před povolením šifrování. V Linuxu musí být disk připojený v/etc/fstab pomocí [název zařízení trvalé bloku](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-device-names-problems).  

Na rozdíl od syntaxe Powershellu rozhraní příkazového řádku, aby uživatele k zadání verze jedinečný pořadí při povolení šifrování. Rozhraní příkazového řádku automaticky generuje a používá svůj vlastní jedinečný pořadí hodnotu verze.

-  **Šifrování datové svazky spuštěného virtuálního počítače:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Šifrování u spuštěného virtuálního počítače pomocí KEK datové svazky:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Povoluje šifrování na nově přidaný disk pomocí Azure Powershellu
 Při použití Powershellu k šifrování nový disk pro Linux, je potřeba zadat novou verzi pořadí. Pořadí verze musí být jedinečný. Níže uvedený skript vytvoří identifikátor GUID verze pořadí. Přijmout [snímku](../virtual-machines/windows/snapshot-copy-managed-disk.md) nebo zálohování virtuálního počítače s [Azure Backup](../backup/backup-azure-vms-encryption.md) před disky jsou šifrované. Parametr - skipVmBackup je už zadaná v skripty Powershellu pro šifrování disku nově přidaná data.
 

-  **Šifrování datové svazky spuštěného virtuálního počítače:** Níže uvedený skript inicializuje proměnných a spustí rutinu Set-AzVMDiskEncryptionExtension. Skupinu prostředků, virtuální počítač a trezor klíčů by již byly vytvořeny jako požadavky. Nahraďte hodnoty MyVirtualMachineResourceGroup MySecureVM a MySecureVault. Přijatelné hodnoty pro parametr - VolumeType jsou všechny, operačním systémem a daty. Pokud virtuální počítač byla dříve zašifrována s typem svazek "Operační systém" nebo "All", pak parametr - VolumeType by měl být změněn na všechny tak, aby operační systém i nový datový disk budou zahrnuty.

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
- **Šifrování u spuštěného virtuálního počítače pomocí KEK datové svazky:** Přijatelné hodnoty pro parametr - VolumeType jsou všechny, operačním systémem a daty. Pokud virtuální počítač byla dříve zašifrována s typem svazek "Operační systém" nebo "All", pak parametr - VolumeType by měl být změněn na všechny tak, aby operační systém i nový datový disk budou zahrnuty.

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
    > Syntaxe pro hodnotu parametru disk šifrování – trezor klíčů je úplný identifikátor řetězce: / subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxe pro hodnoty parametru klíč šifrovacího klíče je úplný identifikátor URI klíče KEK jako v: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Zakázat šifrování pro virtuální počítače s Linuxem
Můžete zakázat šifrování pomocí Azure Powershellu, rozhraní příkazového řádku Azure nebo pomocí šablony Resource Manageru. 

>[!IMPORTANT]
>Zakázáním šifrování v Azure Disk Encryption na virtuální počítače s Linuxem je podporována pouze pro datové svazky. Není podporován na data nebo svazků operačního systému, pokud byla dříve zašifrována svazek s operačním systémem.  

- **Zakážete disk encryption pomocí Azure Powershellu:** Pokud chcete zakázat šifrování, použijte [zakázat AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) rutiny. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Zakážete šifrování pomocí Azure CLI:** Pokud chcete zakázat šifrování, použijte [az vm encryption zakázat](/cli/azure/vm/encryption#az-vm-encryption-disable) příkazu. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Zakážete šifrování pomocí šablony Resource Manageru:** Použití [zakažte šifrování u spuštěného virtuálního počítače s Linuxem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) šablony můžete zakázat šifrování.
     1. Klikněte na **Deploy to Azure** (Nasadit do Azure).
     2. Vyberte předplatné, skupinu prostředků, umístění, virtuálních počítačů, právní podmínky a smlouvy.
     3.  Klikněte na tlačítko **nákupní** můžete zakázat šifrování disků spuštěného virtuálního počítače Windows. 


## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Aktivace Azure Disk Encryption pro Windows](azure-security-disk-encryption-windows.md)
