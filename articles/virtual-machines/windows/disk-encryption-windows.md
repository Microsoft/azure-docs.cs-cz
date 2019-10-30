---
title: Scénáře Azure Disk Encryption na virtuálních počítačích s Windows
description: Tento článek poskytuje pokyny k povolení Microsoft Azureho šifrování disku pro virtuální počítače s Windows pro různé scénáře.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: b4795eeb24d1d0ac373a700a6b60b8facec0e37d
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064011"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Scénáře Azure Disk Encryption na virtuálních počítačích s Windows

Azure Disk Encryption využívá ochranu pomocí ochrany externích klíčů BitLockeru k poskytování šifrování svazku pro operační systém a datové disky virtuálních počítačů Azure a jsou integrované do Azure Key Vault, které vám pomůžou řídit a spravovat šifrovací klíče a tajné kódy disku. Přehled služby najdete v tématu [Azure Disk Encryption pro virtuální počítače s Windows](disk-encryption-overview.md).

Existuje mnoho scénářů šifrování disku a postup se může lišit v závislosti na scénáři. Následující oddíly popisují scénáře podrobněji podrobněji pro virtuální počítače s Windows.

Diskové šifrování můžete použít jenom pro virtuální počítače s [podporovanými velikostmi virtuálních počítačů a operačními systémy](disk-encryption-overview.md#supported-vms-and-operating-systems). Musíte splňovat i následující požadavky:

- [Požadavky na síť](disk-encryption-overview.md#networking-requirements)
- [Zásady skupiny požadavky](disk-encryption-overview.md#group-policy-requirements)
- [Požadavky na úložiště šifrovacího klíče](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Pokud jste předtím používali Azure Disk Encryption se službou Azure AD k šifrování virtuálního počítače, musíte tuto možnost použít k zašifrování virtuálního počítače. Podrobnosti najdete v tématu [Azure Disk Encryption s Azure AD (předchozí verze)](disk-encryption-overview-aad.md) . 
>
> - Předtím, než se disky zašifrují, byste měli [udělat snímek](snapshot-copy-managed-disk.md) nebo vytvořit zálohu. Zálohování zajišťuje možnost obnovení, pokud během šifrování dojde k neočekávané chybě. Virtuální počítače se spravovanými disky vyžadují zálohování před tím, než dojde k šifrování. Po provedení zálohy můžete použít [rutinu Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) k šifrování spravovaných disků zadáním parametru-skipVmBackup. Další informace o zálohování a obnovení šifrovaných virtuálních počítačů najdete v tématu [zálohování a obnovení šifrovaného virtuálního počítače Azure](../../backup/backup-azure-vms-encryption.md). 
>
> - Šifrování nebo zakázání šifrování může způsobit, že se virtuální počítač restartuje.

## <a name="install-tools-and-connect-to-azure"></a>Nainstalovat nástroje a připojit se k Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Povolení šifrování na stávajícím nebo spuštěném virtuálním počítači s Windows
V tomto scénáři můžete šifrování povolit pomocí šablony Správce prostředků, rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku. Pokud potřebujete informace o schématu pro rozšíření virtuálního počítače, přečtěte si článek [Azure Disk Encryption pro rozšíření pro Windows](../extensions/azure-disk-enc-windows.md) .

## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a>Povolení šifrování u stávajících nebo spuštěných virtuálních počítačů s IaaS s Windows
Šifrování můžete povolit pomocí šablony, rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku. Pokud potřebujete informace o schématu pro rozšíření virtuálního počítače, přečtěte si článek [Azure Disk Encryption pro rozšíření pro Windows](../extensions/azure-disk-enc-windows.md) .

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Povolení šifrování u stávajících nebo spuštěných virtuálních počítačů s Azure PowerShell 
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

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Povolení šifrování u stávajících nebo spuštěných virtuálních počítačů pomocí Azure CLI
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

### <a name="using-the-resource-manager-template"></a>Použití šablony Správce prostředků

Můžete povolit šifrování disku na existujícím nebo běžícím virtuálním počítači s IaaS s Windows v Azure pomocí [šablony Správce prostředků k šifrování spuštěného virtuálního počítače s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. V šabloně pro rychlý Start Azure klikněte na **nasadit do Azure**.

2. Vyberte předplatné, skupinu prostředků, umístění, nastavení, právní smlouvy a smlouvu. Kliknutím na **koupit** povolíte šifrování na stávajícím nebo BĚŽÍCÍm virtuálním počítači s IaaS.

V následující tabulce jsou uvedeny parametry šablon Správce prostředků pro existující nebo běžící virtuální počítače:

| Parametr | Popis |
| --- | --- |
| vmName | Název virtuálního počítače, pro který se má spustit operace šifrování |
| keyVaultName | Název trezoru klíčů, do kterého se má klíč BitLocker nahrát Můžete ji získat pomocí rutiny `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` nebo příkazu rozhraní příkazového řádku Azure CLI `az keyvault list --resource-group "MyKeyVaultResourceGroup"`.|
| keyVaultResourceGroup | Název skupiny prostředků, která obsahuje Trezor klíčů|
|  keyEncryptionKeyURL | Adresa URL šifrovacího klíče klíče ve formátu https://&lt;název trezoru klíčů&gt;. vault.azure.net/key/&lt;&gt;. Pokud nechcete používat KEK, nechte toto pole prázdné. |
| volumeType | Typ svazku, na kterém se operace šifrování provádí. Platné hodnoty jsou _operační systém_, _data_a _vše_. 
| forceUpdateTag | Pokaždé, když je potřeba vynutit spuštění operace, předat jedinečnou hodnotu, třeba identifikátor GUID. |
| resizeOSDisk | Měl by se změnit velikost oddílu operačního systému tak, aby zabírala plný virtuální pevný disk s operačním systémem, než se rozdělí systémový svazek. |
| location | Umístění pro všechny prostředky. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nové virtuální počítače s IaaS vytvořené z VHD a šifrovacích klíčů šifrovaných zákazníkem

V tomto scénáři můžete vytvořit nový virtuální počítač z předem zašifrovaného virtuálního pevného disku a přidružené šifrovací klíče pomocí rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku (CLI). 

Postupujte podle pokynů v tématu [Příprava předem zašifrovaného virtuálního pevného disku s Windows](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd). Po vytvoření image můžete pomocí kroků v následující části vytvořit zašifrovaný virtuální počítač Azure.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>Šifrování virtuálních počítačů s předem šifrovanými virtuálními počítači pomocí Azure PowerShell
Pomocí rutiny PowerShellu [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples)můžete povolit šifrování disku na šifrovaném virtuálním pevném disku. V následujícím příkladu jsou uvedeny některé běžné parametry. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Povolení šifrování u nově přidaného datového disku
[Nový disk můžete přidat do virtuálního počítače s Windows pomocí PowerShellu](attach-disk-ps.md)nebo [prostřednictvím Azure Portal](attach-managed-disk-portal.md). 

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

## <a name="unsupported-scenarios"></a>Nepodporované scénáře

Azure Disk Encryption nefunguje v následujících scénářích, funkcích a technologiích:

- Šifrování virtuálních počítačů nebo virtuálních počítačů na úrovni Basic vytvořených prostřednictvím metody vytváření virtuálních počítačů
- Šifrování virtuálních počítačů nakonfigurovaných s využitím softwarových systémů RAID.
- Šifrování virtuálních počítačů nakonfigurovaných s využitím Prostory úložiště s přímým přístupem (S2D) nebo verzí Windows serveru před 2016 nakonfigurovaným pomocí prostorů úložiště Windows.
- Integrace s místním systémem správy klíčů.
- Soubory Azure (sdílený systém souborů).
- Systém souborů NFS (Network File System).
- Dynamické svazky.
- Kontejnery Windows serveru, které vytvářejí dynamické svazky pro každý kontejner.
- Dočasné disky s operačním systémem.
- Šifrování sdílených/distribuovaných systémů souborů (ale ne omezené na) DFS, GFS, DRDB a CephFS.

## <a name="next-steps"></a>Další kroky

- [Přehled Azure Disk Encryption](disk-encryption-overview.md)
- [Azure Disk Encryption ukázkové skripty](disk-encryption-sample-scripts.md)
- [Řešení potíží s Azure Disk Encryption](disk-encryption-troubleshooting.md)
