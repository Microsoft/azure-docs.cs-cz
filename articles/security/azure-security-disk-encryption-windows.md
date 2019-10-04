---
title: Povolení Azure Disk Encryption pro virtuální počítače s Windows IaaS
description: Tento článek poskytuje pokyny k povolení Microsoft Azureho šifrování disku pro virtuální počítače s Windows IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: 109c954ab877d0b8d348fc4b9d7de01c19e41344
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958825"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>Povolení Azure Disk Encryption pro virtuální počítače s Windows IaaS

Tento článek poskytuje pokyny k povolení Microsoft Azureho šifrování disku pro virtuální počítače s Windows IaaS (VM). Než budete moct použít šifrování disku, musíte nejdřív dokončit [Azure Disk Encryption předpoklady](../security/azure-security-disk-encryption-prerequisites.md). 

Důrazně doporučujeme [vytvořit snímek](../virtual-machines/windows/snapshot-copy-managed-disk.md) a/nebo zálohovat disky před šifrováním. Zálohování zajišťuje možnost obnovení, pokud během šifrování dojde k neočekávané chybě. Virtuální počítače se spravovanými disky vyžadují zálohování před tím, než dojde k šifrování. Po provedení zálohy můžete použít [rutinu Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) k šifrování spravovaných disků zadáním parametru-skipVmBackup. Další informace o zálohování a obnovení šifrovaných virtuálních počítačů najdete v článku [zálohování a obnovení šifrovaných virtuálních počítačů Azure](../backup/backup-azure-vms-encryption.md) .

>[!WARNING]
> - Pokud jste už dříve použili [Azure Disk Encryption se službou Azure AD](azure-security-disk-encryption-prerequisites-aad.md) k šifrování tohoto virtuálního počítače, budete muset tuto možnost použít k ZAšifrování virtuálního počítače. V tomto šifrovaném virtuálním počítači nemůžete použít [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) , protože se nejedná o podporovaný scénář, což znamená, že pro tento zašifrovaný virtuální počítač se zatím nepodporuje přepínání z aplikace AAD. 
> - Azure Disk Encryption potřebuje Key Vault a virtuální počítače, které se mají umístit do stejné oblasti. Vytvořte a použijte Key Vault, který je ve stejné oblasti jako virtuální počítač, který chcete zašifrovat. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningWinVM"></a>Povolení šifrování u stávajících nebo spuštěných virtuálních počítačů s IaaS s Windows
Šifrování můžete povolit pomocí šablony, rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku. Pokud potřebujete informace o schématu pro rozšíření virtuálního počítače, přečtěte si článek [Azure Disk Encryption pro rozšíření pro Windows](../virtual-machines/extensions/azure-disk-enc-windows.md) .

>[!IMPORTANT]
 > Je nutné, abyste instanci virtuálního počítače na bázi spravovaného disku pocházeli z a před povolením Azure Disk Encryption. Snímek spravovaného disku se dá vzít z portálu, nebo se dá použít [Azure Backup](../backup/backup-azure-vms-encryption.md) . Zálohy zajišťují, že možnost obnovení je možné v případě jakékoli neočekávané chyby při šifrování. Po vytvoření zálohy se dá rutina Set-AzVMDiskEncryptionExtension použít k šifrování spravovaných disků zadáním parametru-skipVmBackup. Příkaz set-AzVMDiskEncryptionExtension se u virtuálních počítačů založených na discích nezdaří, dokud se neprovede záloha a tento parametr se zadal. 
>
> Šifrování nebo zakázání šifrování může způsobit, že se virtuální počítač restartuje. 
>

### <a name="bkmk_RunningWinVMPSH"></a>Povolení šifrování u stávajících nebo spuštěných virtuálních počítačů s Azure PowerShell 
Pomocí rutiny [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) Povolte šifrování na běžícím virtuálním počítači s IaaS v Azure. 

-  **Zašifrujte spuštěný virtuální počítač:** Skript níže inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. Skupina prostředků, virtuální počítač a trezor klíčů by už měly být vytvořené jako požadavky. Hodnoty MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM a MySecureVault nahraďte hodnotami.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Šifrování spuštěného virtuálního počítače pomocí KEK:** 

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

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > Syntaxe hodnoty parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:/subscriptions/[ID předplatného-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name]</br> Syntaxe hodnoty parametru klíč-šifrování klíče je úplný identifikátor URI pro KEK jako v: https://[název trezoru-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

- **Ověřte, že jsou disky šifrované:** Pokud chcete zjistit stav šifrování virtuálního počítače s IaaS, použijte rutinu [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Zakázat šifrování disku:** Pokud chcete šifrování zakázat, použijte rutinu [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Zakázání šifrování datového disku na virtuálním počítači s Windows, když se zašifrují operační systémy i datové disky, nefungují podle očekávání. Místo toho zakažte šifrování na všech discích.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Povolení šifrování u stávajících nebo spuštěných virtuálních počítačů pomocí Azure CLI
Pomocí příkazu [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) Povolte šifrování na běžícím virtuálním počítači s IaaS v Azure.

- **Zašifrujte spuštěný virtuální počítač:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Šifrování spuštěného virtuálního počítače pomocí KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Syntaxe hodnoty parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:/subscriptions/[ID předplatného-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name] </br> Syntaxe hodnoty parametru klíč-šifrování klíče je úplný identifikátor URI pro KEK jako v: https://[název trezoru-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

- **Ověřte, že jsou disky šifrované:** Pokud chcete zjistit stav šifrování virtuálního počítače s IaaS, použijte příkaz [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Zakázat šifrování:** Pokud chcete šifrování zakázat, použijte příkaz [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Zakázání šifrování datového disku na virtuálním počítači s Windows, když se zašifrují operační systémy i datové disky, nefungují podle očekávání. Místo toho zakažte šifrování na všech discích.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
 
  > [!NOTE]
  >Je nutné, abyste instanci virtuálního počítače na bázi spravovaného disku pocházeli z a před povolením Azure Disk Encryption. Snímek spravovaného disku se dá vzít z portálu, nebo se dá použít [Azure Backup](../backup/backup-azure-vms-encryption.md) . Zálohy zajišťují, že možnost obnovení je možné v případě jakékoli neočekávané chyby při šifrování. Po vytvoření zálohy se dá rutina Set-AzVMDiskEncryptionExtension použít k šifrování spravovaných disků zadáním parametru-skipVmBackup. Tento příkaz se nezdařil proti virtuálním počítačům na bázi spravované disky, dokud se neprovede zálohování a tento parametr je zadaný. 
  >
  >Šifrování nebo zakázání šifrování může způsobit, že se virtuální počítač restartuje. 

### <a name="bkmk_RunningWinVMwRM"> </a>Použití šablony Správce prostředků

Můžete povolit šifrování disku na existujícím nebo běžícím virtuálním počítači s IaaS s Windows v Azure pomocí [šablony Správce prostředků k šifrování spuštěného virtuálního počítače s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. V šabloně pro rychlý Start Azure klikněte na **nasadit do Azure**.

2. Vyberte předplatné, skupinu prostředků, umístění, nastavení, právní smlouvy a smlouvu. Kliknutím na **koupit** povolíte šifrování na stávajícím nebo BĚŽÍCÍm virtuálním počítači s IaaS.

V následující tabulce jsou uvedeny parametry šablon Správce prostředků pro existující nebo běžící virtuální počítače:

| Parametr | Popis |
| --- | --- |
| vmName | Název virtuálního počítače, pro který se má spustit operace šifrování |
| keyVaultName | Název trezoru klíčů, do kterého se má klíč BitLocker nahrát Můžete ji získat pomocí rutiny `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` nebo příkazu rozhraní příkazového řádku Azure CLI `az keyvault list --resource-group "MyKeyVaultResourceGroup"`.|
| keyVaultResourceGroup | Název skupiny prostředků, která obsahuje Trezor klíčů|
|  keyEncryptionKeyURL | Adresa URL šifrovacího klíče klíče ve formátu https://@no__t -0keyvault-name&gt;.vault.azure.net/Key/&lt;key-Name @ no__t-3. Pokud nechcete používat KEK, nechte toto pole prázdné. |
| volumeType | Typ svazku, na kterém se operace šifrování provádí. Platné hodnoty jsou _operační systém_, _data_a _vše_. 
| forceUpdateTag | Pokaždé, když je potřeba vynutit spuštění operace, předat jedinečnou hodnotu, třeba identifikátor GUID. |
| resizeOSDisk | Měl by se změnit velikost oddílu operačního systému tak, aby zabírala plný virtuální pevný disk s operačním systémem, než se rozdělí systémový svazek. |
| umístění | Umístění pro všechny prostředky. |

## <a name="encrypt-virtual-machine-scale-sets"></a>Šifrování virtuálních počítačů Scale Sets

[Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md) umožňují vytvářet a spravovat skupiny identických virtuálních počítačů s vyrovnáváním zatížení. Počet instancí virtuálních počítačů se může automaticky zvětšit nebo zmenšit v reakci na poptávku nebo podle definovaného plánu. K šifrování virtuálních počítačů můžete použít rozhraní příkazového řádku nebo Azure PowerShell.

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Zašifrujte Virtual Machine Scale Sets pomocí Azure PowerShell

Pomocí rutiny [set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/set-azvmssdiskencryptionextension) Povolte šifrování v sadě škálování virtuálních počítačů s Windows. Skupina prostředků, sada škálování virtuálního počítače a trezor klíčů by už měly být vytvořené jako předpoklady.

-  **Zašifrujte běžící sadu škálování virtuálního počítače**:
    ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:

    ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $KeyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     Set-AzRmVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $KeyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

   >[!NOTE]
   > Syntaxe hodnoty parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:/subscriptions/[ID předplatného-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name]</br> Syntaxe hodnoty parametru klíč-šifrování klíče je úplný identifikátor URI pro KEK jako v: https://[název trezoru-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

- **Získat stav šifrování pro sadu škálování virtuálního počítače:** Použijte rutinu [Get-AzVmssDiskEncryption](/powershell/module/az.compute/get-azvmssdiskencryption) .
    
    ```azurepowershell-interactive
    get-AzVmssVMDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

- **Zakázat šifrování u sady škálování virtuálních počítačů**: použijte rutinu [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/disable-azvmssdiskencryption) . 

    ```azurepowershell-interactive
    Disable-AzVmssDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Šifrování služby Virtual Machine Scale Sets pomocí Azure CLI

K povolení šifrování v sadě škálování virtuálních počítačů s Windows použijte možnost [AZ VMSS Encryption Enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable) . Pokud nastavíte zásady upgradu na ruční škálování nastavené na ruční, spusťte šifrování pomocí [AZ VMSS Update-Instances](/cli/azure/vmss#az-vmss-update-instances). Skupina prostředků, sada škálování virtuálního počítače a trezor klíčů by už měly být vytvořené jako předpoklady.

-  **Šifrování spuštěné sady škálování virtuálních počítačů**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **Zabalením spuštěné sady škálování virtuálních počítačů pomocí KEK zabalte klíč**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
     
   >[!NOTE]
   > Syntaxe hodnoty parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:/subscriptions/[ID předplatného-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name]</br> Syntaxe hodnoty parametru klíč-šifrování klíče je úplný identifikátor URI pro KEK jako v: https://[název trezoru-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

- **Získat stav šifrování pro sadu škálování virtuálního počítače:** Použití [AZ VMSS Encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
     az vmss encryption show --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

- **Zakázat šifrování u sady škálování virtuálních počítačů**: použijte [AZ VMSS Encryption Disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-windows-virtual-machine-scale-sets"></a>Šablony Azure Resource Manager pro Windows Virtual Machine Scale Sets

K šifrování nebo dešifrování Windows Virtual Machine Scale Sets použijte Azure Resource Manager šablony a pokyny níže:

- [Povolení šifrování v sadě škálování virtuálních počítačů s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)
- [Zakázat šifrování v sadě škálování virtuálních počítačů s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

     1. Klikněte na **nasadit do Azure**.
     2. Vyplňte požadovaná pole a pak vyjádřete souhlas s podmínkami a ujednáními.
     3. Šablonu nasadíte kliknutím na **koupit** .

## <a name="bkmk_VHDpre"></a> Nové virtuální počítače s IaaS vytvořené z VHD a šifrovacích klíčů šifrovaných zákazníkem

V tomto scénáři můžete povolit šifrování pomocí rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku (CLI). 

Použijte pokyny v příloze pro přípravu předem šifrovaných imagí, které je možné použít v Azure. Po vytvoření image můžete pomocí kroků v následující části vytvořit zašifrovaný virtuální počítač Azure.

* [Příprava předem zašifrovaného virtuálního pevného disku s Windows](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Příprava předem zašifrovaného virtuálního pevného disku se systémem Linux](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Je nutné, abyste instanci virtuálního počítače na bázi spravovaného disku pocházeli z a před povolením Azure Disk Encryption. Snímek spravovaného disku se dá vzít z portálu, nebo se dá použít [Azure Backup](../backup/backup-azure-vms-encryption.md) . Zálohy zajišťují, že možnost obnovení je možné v případě jakékoli neočekávané chyby při šifrování. Po vytvoření zálohy se dá rutina Set-AzVMDiskEncryptionExtension použít k šifrování spravovaných disků zadáním parametru-skipVmBackup. Příkaz set-AzVMDiskEncryptionExtension se u virtuálních počítačů založených na discích nezdaří, dokud se neprovede záloha a tento parametr se zadal. 
>
>Šifrování nebo zakázání šifrování může způsobit, že se virtuální počítač restartuje. 


### <a name="bkmk_VHDprePSH"></a> Šifrování virtuálních počítačů s předem šifrovanými virtuálními počítači pomocí Azure PowerShell
Pomocí rutiny PowerShellu [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples)můžete povolit šifrování disku na šifrovaném virtuálním pevném disku. V následujícím příkladu jsou uvedeny některé běžné parametry. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Povolení šifrování u nově přidaného datového disku
[Nový disk můžete přidat do virtuálního počítače s Windows pomocí PowerShellu](../virtual-machines/windows/attach-disk-ps.md)nebo [prostřednictvím Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Povolit šifrování na nově přidaném disku s Azure PowerShell
 Při použití PowerShellu k šifrování nového disku pro virtuální počítače s Windows by se měla zadat nová verze sekvence. Verze sekvence musí být jedinečná. Následující skript vygeneruje identifikátor GUID pro verzi sekvence. V některých případech může být nově přidaný datový disk automaticky zašifrovaný pomocí rozšíření Azure Disk Encryption. K automatickému šifrování obvykle dochází, když se virtuální počítač restartuje po přechodu nového disku do režimu online. To je obvykle způsobeno tím, že pro daný typ svazku byly zadány možnosti All, když se na virtuálním počítači dříve spustilo šifrování disku. Pokud automatické šifrování probíhá na nově přidaném datovém disku, doporučujeme znovu spustit rutinu Set-AzVmDiskEncryptionExtension s novou verzí sekvence. Pokud je nový datový disk automaticky zašifrovaný a nechcete být zašifrovaný, Dešifrujte všechny jednotky a pak znovu Zašifrujte novou verzí sekvence určující operační systém pro daný typ svazku. 
  
 

-  **Zašifrujte spuštěný virtuální počítač:** Skript níže inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. Skupina prostředků, virtuální počítač a trezor klíčů by už měly být vytvořené jako požadavky. Hodnoty MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM a MySecureVault nahraďte hodnotami. V tomto příkladu se používá "All" pro parametr-VolumeType, který zahrnuje operační systém i svazky dat. Pokud chcete svazek operačního systému zašifrovat jenom, použijte pro parametr-VolumeType "OS". 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Šifrování spuštěného virtuálního počítače pomocí KEK:** V tomto příkladu se používá "All" pro parametr-VolumeType, který zahrnuje operační systém i svazky dat. Pokud chcete svazek operačního systému zašifrovat jenom, použijte pro parametr-VolumeType "OS".

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

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Syntaxe hodnoty parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:/subscriptions/[ID předplatného-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name]</br> Syntaxe hodnoty parametru klíč-šifrování klíče je úplný identifikátor URI pro KEK jako v: https://[název trezoru-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Povolení šifrování u nově přidaného disku pomocí Azure CLI
 Příkaz Azure CLI vám při spuštění příkazu pro povolení šifrování automaticky poskytne novou verzi sekvence. V příkladu se pro parametr typu svazku používá "All". Pokud šifrujete jenom disk s operačním systémem, možná budete muset změnit parametr typu svazku na operační systém. Na rozdíl od syntaxe PowerShellu rozhraní příkazového řádku nevyžaduje, aby uživatel při povolování šifrování poskytoval jedinečnou verzi sekvence. Rozhraní příkazového řádku automaticky vygeneruje a použije svou vlastní jedinečnou hodnotu verze sekvence.   

-  **Zašifrujte spuštěný virtuální počítač:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Šifrování spuštěného virtuálního počítače pomocí KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Zakázat šifrování
Šifrování můžete zakázat pomocí Azure PowerShell, rozhraní příkazového řádku Azure nebo pomocí Správce prostředků šablony. Zakázání šifrování datového disku na virtuálním počítači s Windows, když se zašifrují operační systémy i datové disky, nefungují podle očekávání. Místo toho zakažte šifrování na všech discích.

- **Zakázat šifrování disku pomocí Azure PowerShell:** Pokud chcete šifrování zakázat, použijte rutinu [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Zakázat šifrování pomocí Azure CLI:** Pokud chcete šifrování zakázat, použijte příkaz [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Zakázat šifrování pomocí šablony Správce prostředků:** 

    1. Kliknutím na **nasadit do Azure** z části [zakázat šifrování disku při spuštění šablony virtuálního počítače s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) .
    2. Vyberte předplatné, skupinu prostředků, umístění, virtuální počítač, typ svazku, právní výrazy a smlouvu.
    3.  Kliknutím na **koupit** zakažte šifrování disku na běžícím virtuálním počítači s Windows. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Povolit Azure Disk Encryption pro Linux](azure-security-disk-encryption-linux.md)
