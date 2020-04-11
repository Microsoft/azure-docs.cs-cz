---
title: Scénáře služby Azure Disk Encryption na virtuálních počítačích s Windows
description: Tento článek obsahuje pokyny k povolení šifrování disku Microsoft Azure pro virtuální počítače s Windows pro různé scénáře
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: f7b6e667df95d9279ad5c44caa4ba33a17909935
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113156"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Scénáře služby Azure Disk Encryption na virtuálních počítačích s Windows

Azure Disk Encryption pro virtuální počítače s Windows (VM) používá bitlocker funkce systému Windows poskytnout úplné šifrování disku operačního systému a datového disku. Navíc poskytuje šifrování dočasný prostředek disku při VolumeType parametr je vše.

Azure Disk Encryption je [integrovaný s Azure Key Vault,](disk-encryption-key-vault.md) který vám pomůže řídit a spravovat šifrovací klíče a tajné klíče disku. Přehled služby najdete v tématu [Azure Disk Encryption for Windows VMs](disk-encryption-overview.md).

Šifrování disku lze použít jenom na virtuální počítače [podporovaných velikostí virtuálních počítačů a operačních systémů](disk-encryption-overview.md#supported-vms-and-operating-systems). Musíte také splnit následující předpoklady:

- [Požadavky na vytváření sítí](disk-encryption-overview.md#networking-requirements)
- [Požadavky zásad skupiny](disk-encryption-overview.md#group-policy-requirements)
- [Požadavky na úložiště šifrovacího klíče](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Pokud jste dříve používali Azure Disk Encryption s Azure AD k šifrování virtuálního počítače, musíte pokračovat v použití této možnosti k šifrování virtuálního počítače. Podrobnosti najdete [v tématu Azure Disk Encryption with Azure AD (předchozí verze).](disk-encryption-overview-aad.md) 
>
> - Měli byste [pořídit snímek](snapshot-copy-managed-disk.md) nebo vytvořit zálohu před zašifrováním disků. Zálohy zajistí, že možnost obnovení je možné, pokud dojde k neočekávané chybě během šifrování. Virtuální počítače se spravovanými disky vyžadují zálohu, než dojde k šifrování. Po vytvoření zálohy můžete pomocí [rutiny Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) šifrovat spravované disky zadáním parametru -skipVmBackup. Další informace o tom, jak zálohovat a obnovovat šifrované virtuální počítače, najdete v [tématu Zálohování a obnovení šifrovaného virtuálního počítače Azure](../../backup/backup-azure-vms-encryption.md). 
>
> - Šifrování nebo zakázání šifrování může způsobit restartování virtuálního počítače.

## <a name="install-tools-and-connect-to-azure"></a>Instalace nástrojů a připojení k Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Povolení šifrování na existujícím nebo spuštěném virtuálním počítači se systémem Windows
V tomto scénáři můžete povolit šifrování pomocí šablony Správce prostředků, rutin prostředí PowerShell nebo příkazů příkazu CLI. Pokud potřebujete informace o schématu pro rozšíření virtuálního počítače, přečtěte si článek rozšíření [Azure Disk Encryption for Windows.](../extensions/azure-disk-enc-windows.md)

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Povolení šifrování na stávajících nebo spuštěných virtuálních počítačích pomocí Azure PowerShellu 
Pomocí rutiny [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) povolte šifrování na běžícím virtuálním počítači IaaS v Azure. 

-  **Šifrování spuštěného virtuálního virtuálního montova:** Níže uvedený skript inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. Skupina prostředků, virtuální hod a trezor klíčů už měly být vytvořeny jako předpoklady. Nahraďte hodnoty MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM a MySecureVault.

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
- **Šifrování spuštěného virtuálního virtuálního aplikace pomocí kek:** 

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
   > Syntaxe hodnoty parametru disk-encryption-keyvault je úplný řetězec identifikátoru: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxe hodnoty parametru key-encryption-key je úplná identifikátor URI pro KEK jako: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ověřte, zda jsou disky zašifrovány:** Chcete-li zkontrolovat stav šifrování virtuálního počítače IaaS, použijte rutinu [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Zakázat šifrování disku:** Chcete-li šifrování zakázat, použijte rutinu [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Zakázání šifrování datových disků u virtuálních počítačů Windows, když byly zašifrovány disky s operačním systémem a datové disky, nefungujte podle očekávání. Místo toho zakažte šifrování na všech discích.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Povolení šifrování na stávajících nebo spuštěných virtuálních počítačích pomocí příkazového příkazového příkazu Kontu Azure
Pomocí příkazu [povolit šifrování az vm](/cli/azure/vm/encryption#az-vm-encryption-enable) povolte šifrování na běžícím virtuálním počítači IaaS v Azure.

- **Šifrování spuštěného virtuálního virtuálního montova:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Šifrování spuštěného virtuálního virtuálního aplikace pomocí kek:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Syntaxe hodnoty parametru disk-encryption-keyvault je úplný řetězec identifikátoru: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> Syntaxe hodnoty parametru key-encryption-key je úplná identifikátor URI pro KEK jako: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ověřte, zda jsou disky zašifrovány:** Chcete-li zkontrolovat stav šifrování virtuálního počítače IaaS, použijte příkaz [az vm šifrování show.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Zakázat šifrování:** Chcete-li zakázat šifrování, použijte příkaz [zakázat šifrování az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) Zakázání šifrování datových disků u virtuálních počítačů Windows, když byly zašifrovány disky s operačním systémem a datové disky, nefungujte podle očekávání. Místo toho zakažte šifrování na všech discích.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>Použití šablony Správce prostředků

Šifrování disku na existujících nebo spuštěných virtuálních počítačích IaaS windows v Azure můžete povolit pomocí [šablony Správce prostředků k šifrování spuštěného virtuálního počítače se systémem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. V šabloně Azure quickstart klikněte na **Deploy to Azure**.

2. Vyberte předplatné, skupinu prostředků, umístění, nastavení, právní podmínky a smlouvu. Kliknutím na **Koupit** povolíte šifrování na existujícím nebo spuštěném virtuálním počítači IaaS.

V následující tabulce jsou uvedeny parametry šablony Správce prostředků pro existující nebo spuštěné virtuální počítače:

| Parametr | Popis |
| --- | --- |
| vmName | Název virtuálního virtuálního serveru pro spuštění operace šifrování. |
| keyVaultName | Název trezoru klíčů, do kterého by měl být klíč nástroje BitLocker odeslán. Můžete ji získat pomocí rutiny `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` nebo příkazu Azure CLI`az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | Název skupiny prostředků, která obsahuje trezor klíčů|
|  keyEncryptionKeyURL | Adresa URL šifrovacího klíče&lt;klíče ve&gt;formátu&lt;https:// název&gt;klíče .vault.azure.net/key/ název klíče . Pokud nechcete použít KEK, ponechte toto pole prázdné. |
| volumeType | Typ svazku, na které se provádí operace šifrování. Platné hodnoty jsou _OS_, _Data_a _All_. 
| forceUpdateTag | Předat v jedinečnou hodnotu, jako je IDENTIFIKÁTOR GUID pokaždé, když operace musí být force run. |
| změna velikostiOSDisk | Měla by být velikost oddílu operačního systému před rozdělením systémového svazku zabírat celý virtuální disk Operačního systému. |
| location | Umístění pro všechny zdroje. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nové virtuální typy IaaS vytvořené ze zákazníkem šifrovaného virtuálního pevného disku a šifrovacích klíčů

V tomto scénáři můžete vytvořit nový virtuální počítače z předem zašifrovaného virtuálního pevného disku a přidružených šifrovacích klíčů pomocí rutin prostředí PowerShell nebo příkazů příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazy příkazu příkazu příkazu. 

Použijte pokyny v [části Příprava předšifrovaného virtuálního pevného disku systému Windows](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd). Po vytvoření image můžete pomocí kroků v další části vytvořit šifrovaný virtuální počítač Azure.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>Šifrování virtuálních počítačů pomocí předem šifrovaných virtuálních discích pomocí Azure PowerShellu
Šifrování disku v zašifrovaném virtuálním pevném disku můžete povolit pomocí rutiny PowerShell [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). Následující příklad poskytuje některé běžné parametry. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Povolení šifrování na nově přidaném datovém disku
Nový disk můžete [přidat do virtuálního počítače s Windows pomocí PowerShellu](attach-disk-ps.md)nebo [prostřednictvím portálu Azure](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Povolení šifrování na nově přidaném disku pomocí Azure PowerShellu
 Při použití prostředí Powershell k šifrování nového disku pro virtuální počítače se systémem Windows by měla být zadána nová verze sekvence. Sekvenční verze musí být jedinečná. Níže uvedený skript generuje identifikátor GUID pro sekvenční verzi. V některých případech může být nově přidaný datový disk automaticky zašifrován rozšířením Azure Disk Encryption. Automatické šifrování se obvykle vyskytuje, když se virtuální počítač restartuje po přepne nový disk do režimu online. To je obvykle způsobeno, protože "Vše" bylo zadáno pro typ svazku při šifrování disku dříve spuštěno na virtuálním počítači. Pokud na nově přidaném datovém disku dojde k automatickému šifrování, doporučujeme znovu spustit rutinu Set-AzVmDiskEncryptionExtension s novou sekvenční verzí. Pokud je nový datový disk automaticky zašifrován a nechcete být šifrován, dešifrujte nejprve všechny jednotky a poté znovu šifrujte novou sekvenční verzí určující operační systém pro typ svazku. 
  
 

-  **Šifrování spuštěného virtuálního virtuálního montova:** Níže uvedený skript inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. Skupina prostředků, virtuální hod a trezor klíčů už měly být vytvořeny jako předpoklady. Nahraďte hodnoty MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM a MySecureVault. Tento příklad používá "All" pro parametr -VolumeType, který zahrnuje svazky operačního a datového. Pokud chcete šifrovat pouze svazek operačního systému, použijte "OS" pro parametr -VolumeType. 

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
- **Šifrování spuštěného virtuálního virtuálního aplikace pomocí kek:** Tento příklad používá "All" pro parametr -VolumeType, který zahrnuje svazky operačního a datového. Pokud chcete šifrovat pouze svazek operačního systému, použijte "OS" pro parametr -VolumeType.

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
    > Syntaxe hodnoty parametru disk-encryption-keyvault je úplný řetězec identifikátoru: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxe hodnoty parametru key-encryption-key je úplná identifikátor URI pro KEK jako: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Povolení šifrování na nově přidaném disku pomocí azure cli
 Příkaz Azure CLI automaticky poskytne novou sekvenční verzi při spuštění příkazu pro povolení šifrování. Příklad používá "Vše" pro parametr typu svazku. Pokud šifrujete pouze disk operačního systému, bude pravděpodobně nutné změnit parametr typu svazku na operační systém. Na rozdíl od syntaxe prostředí Powershell nevyžaduje rozhraní řízení chování uživatele k zadání jedinečné sekvenční verze při povolení šifrování. ClI automaticky generuje a používá vlastní hodnotu jedinečné sekvence verze.   

-  **Šifrování spuštěného virtuálního virtuálního montova:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Šifrování spuštěného virtuálního virtuálního aplikace pomocí kek:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Zakázat šifrování
Šifrování můžete zakázat pomocí Azure PowerShellu, Azure CLI nebo pomocí šablony Správce prostředků. Zakázání šifrování datových disků u virtuálních počítačů Windows, když byly zašifrovány disky s operačním systémem a datové disky, nefungujte podle očekávání. Místo toho zakažte šifrování na všech discích.

- **Zakázání šifrování disku pomocí Azure PowerShellu:** Chcete-li šifrování zakázat, použijte rutinu [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Zakažte šifrování pomocí azure cli:** Chcete-li zakázat šifrování, použijte příkaz [zakázat šifrování az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Zakázání šifrování pomocí šablony Správce prostředků:** 

    1. Klikněte **na Nasadit do Azure** z [zakázat šifrování disku na spuštěné šabloně virtuálního počítače windows.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
    2. Vyberte předplatné, skupinu prostředků, umístění, virtuální hod, typ svazku, právní podmínky a smlouvu.
    3.  Chcete-li zakázat šifrování disku na spuštěném virtuálním počítači se systémem Windows, klepněte na tlačítko **Koupit.** 

## <a name="unsupported-scenarios"></a>Nepodporované scénáře

Azure Disk Encryption nefunguje pro následující scénáře, funkce a technologie:

- Šifrování základních virtuálních virtuálních proudů nebo virtuálních vás vytvořených pomocí klasické metody vytváření virtuálních montovek.
- Šifrování virtuálních počítače nakonfigurovaných pomocí softwarových systémů RAID.
- Šifrování virtuálních počítače nakonfigurovaných pomocí storage spaces direct (S2D) nebo verzí Windows Serveru před rokem 2016 nakonfigurovaných s prostory úložiště Windows.
- Integrace s místním systémem správy klíčů.
- Soubory Azure (sdílený systém souborů).
- systému souborů (NFS).
- Dynamické svazky.
- Kontejnery systému Windows Server, které vytvářejí dynamické svazky pro každý kontejner.
- Dočasné disky operačního systému.
- Šifrování sdílených/distribuovaných souborových systémů, jako je (ale nejen) DFS, GFS, DRDB a CephFS.
- Přesunutí šifrovaných virtuálních aplikací do jiného předplatného.
- Virtuální počítače Gen2 (viz: [Podpora pro virtuální počítače generace 2 v Azure)](generation-2.md#generation-1-vs-generation-2-capabilities)
- Virtuální zařízení řady Lsv2 (viz: [Řada Lsv2](../lsv2-series.md))

## <a name="next-steps"></a>Další kroky

- [Azure Disk Encryption – přehled](disk-encryption-overview.md)
- [Ukázkové skripty pro službu Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Řešení potíží se službou Azure Disk Encryption](disk-encryption-troubleshooting.md)
