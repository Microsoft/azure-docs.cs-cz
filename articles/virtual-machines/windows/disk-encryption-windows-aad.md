---
title: Šifrování disku Azure pomocí Azure AD pro virtuální počítače s Windows (předchozí verze)
description: Tento článek obsahuje pokyny k povolení microsoft azure disk šifrování pro Windows IaaS virtuální počítače.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: d06be85e4d18bc0867835a307b44ec8813c79d7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72245003"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Šifrování disku Azure pomocí Azure AD pro virtuální počítače s Windows (předchozí verze)

**Nová verze Azure Disk Encryption eliminuje požadavek na poskytování parametru aplikace Azure AD pro povolení šifrování disku virtuálního počítače. S novou verzí už nemusíte poskytovat přihlašovací údaje Azure AD během kroku povolení šifrování. Všechny nové virtuální počítače musí být zašifrované bez parametrů aplikace Azure AD pomocí nové verze. Pokyny k povolení šifrování disku virtuálního počítače pomocí nové verze najdete v [tématu Azure Disk Encryption for Windows VMS](disk-encryption-windows.md). Virtuální počítače, které už byly zašifrované pomocí parametrů aplikace Azure AD, jsou stále podporované a měly by být nadále udržovány pomocí syntaxe AAD.**


Můžete povolit mnoho scénářů šifrování disku a kroky se mohou lišit podle scénáře. Následující části popisují scénáře podrobněji pro windows IaaS virtuální chod. Před použitím šifrování disku je třeba dokončit [požadavky azure diskového šifrování.](disk-encryption-overview-aad.md) 


>[!IMPORTANT]
> - Měli byste [pořídit snímek](snapshot-copy-managed-disk.md) nebo vytvořit zálohu před zašifrováním disků. Zálohy zajistí, že možnost obnovení je možné, pokud dojde k neočekávané chybě během šifrování. Virtuální počítače se spravovanými disky vyžadují zálohu, než dojde k šifrování. Po vytvoření zálohy můžete pomocí [rutiny Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) šifrovat spravované disky zadáním parametru -skipVmBackup. Další informace o tom, jak zálohovat a obnovovat šifrované virtuální počítače, najdete v [tématu Zálohování a obnovení šifrovaného virtuálního počítače Azure](../../backup/backup-azure-vms-encryption.md). 
>
> - Šifrování nebo zakázání šifrování může způsobit restartování virtuálního počítače.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Povolení šifrování na nových virtuálních počítačích IaaS vytvořených z webu Marketplace
Šifrování disku na novém virtuálním počítači IaaS windows můžete povolit z marketplace v Azure pomocí šablony Správce prostředků. Šablona vytvoří nový šifrovaný virtuální virtuální počítače se systémem Windows pomocí bitové kopie galerie Windows Serveru 2012.

1. V [šabloně Správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)klikněte na **Nasadit do Azure**.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, parametry, právní podmínky a smlouvu. Kliknutím na **Koupit** nasaďte nový virtuální mon iaas, kde je povoleno šifrování.

3. Po nasazení šablony ověřte stav šifrování virtuálního počítače pomocí upřednostňované metody:
     - Ověřte pomocí rozhraní příkazového příkazu Azure CLI pomocí příkazu [az vm šifrování.](/cli/azure/vm/encryption#az-vm-encryption-show) 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Ověřte pomocí Prostředí Azure PowerShell pomocí rutiny [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Vyberte virtuální počítač a kliknutím na **Disky** pod záhlavím **Nastavení** ověřte stav šifrování na portálu. V grafu v části **Šifrování**uvidíte, jestli je povolený. 
           ![Portál Azure – povoleno šifrování disku](../media/disk-encryption/disk-encryption-fig2.png)

V následující tabulce jsou uvedeny parametry šablony Správce prostředků pro nové virtuální počítače ze scénáře Marketplace pomocí ID klienta Azure AD:

| Parametr | Popis | 
| --- | --- |
| adminUserName | Uživatelské jméno správce virtuálního počítače. |
| adminPassword | Heslo pro uživatele správce pro virtuální počítač. |
| newStorageAccountName | Název účtu úložiště pro ukládání operačního serveru a datových virtuálních discích. |
| vmSize | Velikost virtuálního počítače. V současné době jsou podporovány pouze standardní řady A, D a G. |
| virtualNetworkName | Název virtuální sítě, do které by měla patřit síťnica virtuálního rozhraní. |
| název podsítě | Název podsítě ve virtuální síti, do které by měla patřit síť NIC virtuálního rozhraní. |
| AADClientID | ID klienta aplikace Azure AD, která má oprávnění k zápisu tajných kódů do trezoru klíčů. |
| AADClientSecret | Tajný klíč klienta aplikace Azure AD, která má oprávnění k zápisu tajných kódů do trezoru klíčů. |
| keyVaultURL | Adresa URL trezoru klíčů, do kterého by měl být klíč nástroje BitLocker odeslán. Můžete ji získat pomocí rutiny `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` nebo azure cli`az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | Adresa URL šifrovacího klíče klíče, který se používá k šifrování generovaného klíče nástroje BitLocker (volitelné). </br> </br>KeyEncryptionKeyURL je volitelný parametr. Můžete si přinést vlastní KEK k dalšímu zabezpečení šifrovacího klíče (tajný klíč passphrase) ve vašem trezoru klíčů. |
| keyVaultResourceGroup | Skupina prostředků trezoru klíčů |
| vmName | Název virtuálního počítače, na který má být provedena operace šifrování. |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a>Povolení šifrování na existujících nebo spuštěných virtuálních počítačích IaaS windows
V tomto scénáři můžete povolit šifrování pomocí šablony, rutin prostředí PowerShell nebo příkazů příkazu cli. V následujících částech podrobněji vysvětleno, jak povolit Azure Disk Encryption. 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a>Povolení šifrování na stávajících nebo spuštěných virtuálních počítačích pomocí Azure PowerShellu 
Pomocí rutiny [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) povolte šifrování na běžícím virtuálním počítači IaaS v Azure. Informace o povolení šifrování pomocí azure diskového šifrování pomocí rutin PowerShellu najdete v blogových příspěvcích [Prozkoumat Azure Disk Encryption pomocí Azure PowerShellu – část 1](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) a [Prozkoumejte šifrování disku Azure pomocí Azure PowerShellu – část 2](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

-  **Šifrování spuštěného virtuálního virtuálního klienta pomocí tajného klíče klienta:** Níže uvedený skript inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. Skupina prostředků, virtuální hod, trezor klíčů, aplikace AAD a tajný klíč klienta už měly být vytvořeny jako předpoklady. Nahraďte myKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID a My-AAD-client-secret s vašimi hodnotami.
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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Šifrování spuštěného virtuálního virtuálního klienta pomocí kek zabalit tajný klíč klienta:** Azure Disk Encryption umožňuje zadat existující klíč v trezoru klíčů zabalit tajné kódy šifrování disku, které byly generovány při povolení šifrování. Když je zadán klíč šifrovací klíč, Azure Disk Encryption používá tento klíč k zabalení tajných kódů šifrování před zápisem do trezoru klíčů. 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > Syntaxe hodnoty parametru disk-encryption-keyvault je úplný řetězec identifikátoru: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxe hodnoty parametru key-encryption-key je úplná identifikátor URI pro KEK jako: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ověřte, zda jsou disky zašifrovány:** Chcete-li zkontrolovat stav šifrování virtuálního počítače IaaS, použijte rutinu [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Zakázat šifrování disku:** Chcete-li zakázat šifrování, použijte rutinu [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a>Povolení šifrování na stávajících nebo spuštěných virtuálních počítačích pomocí azure cli
Pomocí příkazu [povolit šifrování az vm](/cli/azure/vm/encryption#az-vm-encryption-enable) povolte šifrování na běžícím virtuálním počítači IaaS v Azure.

- **Šifrování spuštěného virtuálního virtuálního klienta pomocí tajného klíče klienta:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Šifrování spuštěného virtuálního virtuálního klienta pomocí kek zabalit tajný klíč klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Syntaxe hodnoty parametru disk-encryption-keyvault je úplný řetězec identifikátoru: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> Syntaxe hodnoty parametru key-encryption-key je úplná identifikátor URI pro KEK jako: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ověřte, zda jsou disky zašifrovány:** Chcete-li zkontrolovat stav šifrování virtuálního počítače IaaS, použijte příkaz [az vm šifrování show.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Zakázat šifrování:** Chcete-li zakázat šifrování, použijte příkaz [zakázat šifrování az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>Použití šablony Správce prostředků
Šifrování disku na existujících nebo spuštěných virtuálních počítačích IaaS windows v Azure můžete povolit pomocí [šablony Správce prostředků k šifrování spuštěného virtuálního počítače se systémem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. V šabloně Azure quickstart klikněte na **Deploy to Azure**.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, parametry, právní podmínky a smlouvu. Kliknutím na **Koupit** povolíte šifrování na existujícím nebo spuštěném virtuálním počítači IaaS.

V následující tabulce jsou uvedeny parametry šablony Správce prostředků pro existující nebo spuštěné virtuální počítače, které používají ID klienta Azure AD:

| Parametr | Popis |
| --- | --- |
| AADClientID | ID klienta aplikace Azure AD, která má oprávnění k zápisu tajných kódů do trezoru klíčů. |
| AADClientSecret | Tajný klíč klienta aplikace Azure AD, která má oprávnění k zápisu tajných kódů do trezoru klíčů. |
| keyVaultName | Název trezoru klíčů, do kterého by měl být klíč nástroje BitLocker odeslán. Můžete ji získat pomocí rutiny `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` nebo příkazu Azure CLI`az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | Adresa URL šifrovacího klíče klíče, který se používá k šifrování generovaného klíče nástroje BitLocker. Tento parametr je volitelný, pokud vyberete **nokek** v rozevíracím seznamu UseExistingKek. Pokud vyberete **kek** v rozevíracím seznamu UseExistingKek, musíte zadat hodnotu _keyEncryptionKeyURL._ |
| volumeType | Typ svazku, na které se provádí operace šifrování. Platné hodnoty jsou _OS_, _Data_a _All_. |
| sekvenceVerze | Sekvenční verze operace nástroje BitLocker. Přírůstek toto číslo verze pokaždé, když se provádí operace šifrování disku na stejném virtuálním počítači. |
| vmName | Název virtuálního počítače, na který má být provedena operace šifrování. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>Nové virtuální typy IaaS vytvořené ze zákazníkem šifrovaného virtuálního pevného disku a šifrovacích klíčů
V tomto scénáři můžete povolit šifrování pomocí šablony Správce prostředků, rutin prostředí PowerShell nebo příkazů příkazu CLI. V následujících částech podrobněji vysvětlete šablonu správce prostředků a příkazy příkazu příkazu příkazu příkazu příkazu příkazu příkazu. 

Pomocí pokynů v dodatku pro přípravu předem zašifrované image, které lze použít v Azure. Po vytvoření image můžete pomocí kroků v další části vytvořit šifrovaný virtuální počítač Azure.

* [Příprava předem šifrovaného virtuálního pevného disku systému Windows](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"> </a> Šifrování virtuálních počítačů pomocí předem šifrovaných virtuálních discích pomocí Azure PowerShellu
Šifrování disku v zašifrovaném virtuálním pevném disku můžete povolit pomocí rutiny PowerShell [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). Následující příklad poskytuje některé běžné parametry. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Povolení šifrování na nově přidaném datovém disku
Nový disk můžete [přidat do virtuálního počítače s Windows pomocí PowerShellu](attach-disk-ps.md)nebo [prostřednictvím portálu Azure](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Povolení šifrování na nově přidaném disku pomocí Azure PowerShellu
 Při použití prostředí Powershell k šifrování nového disku pro virtuální počítače se systémem Windows by měla být zadána nová verze sekvence. Sekvenční verze musí být jedinečná. Níže uvedený skript generuje identifikátor GUID pro sekvenční verzi. V některých případech může být nově přidaný datový disk automaticky zašifrován rozšířením Azure Disk Encryption. Automatické šifrování se obvykle vyskytuje, když se virtuální počítač restartuje po přepne nový disk do režimu online. To je obvykle způsobeno, protože "Vše" bylo zadáno pro typ svazku při šifrování disku dříve spuštěno na virtuálním počítači. Pokud na nově přidaném datovém disku dojde k automatickému šifrování, doporučujeme znovu spustit rutinu Set-AzVmDiskEncryptionExtension s novou sekvenční verzí. Pokud je nový datový disk automaticky zašifrován a nechcete být šifrován, dešifrujte nejprve všechny jednotky a poté znovu šifrujte novou sekvenční verzí určující operační systém pro typ svazku. 
 

-  **Šifrování spuštěného virtuálního virtuálního klienta pomocí tajného klíče klienta:** Níže uvedený skript inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. Skupina prostředků, virtuální hod, trezor klíčů, aplikace AAD a tajný klíč klienta už měly být vytvořeny jako předpoklady. Nahraďte myKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID a My-AAD-client-secret s vašimi hodnotami. Tento příklad používá "All" pro parametr -VolumeType, který zahrnuje svazky operačního a datového. Pokud chcete šifrovat pouze svazek operačního systému, použijte "OS" pro parametr -VolumeType. 

     ```azurepowershell
      $sequenceVersion = [Guid]::NewGuid();
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;
    ```
- **Šifrování spuštěného virtuálního virtuálního klienta pomocí kek zabalit tajný klíč klienta:** Azure Disk Encryption umožňuje zadat existující klíč v trezoru klíčů zabalit tajné kódy šifrování disku, které byly generovány při povolení šifrování. Když je zadán klíč šifrovací klíč, Azure Disk Encryption používá tento klíč k zabalení tajných kódů šifrování před zápisem do trezoru klíčů. Tento příklad používá "All" pro parametr -VolumeType, který zahrnuje svazky operačního a datového. Pokud chcete šifrovat pouze svazek operačního systému, použijte "OS" pro parametr -VolumeType. 

     ```azurepowershell
     $sequenceVersion = [Guid]::NewGuid();
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

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Syntaxe hodnoty parametru disk-encryption-keyvault je úplný řetězec identifikátoru: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxe hodnoty parametru key-encryption-key je úplná identifikátor URI pro KEK jako: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Povolení šifrování na nově přidaném disku pomocí azure cli
  Příkaz Azure CLI automaticky poskytne novou sekvenční verzi při spuštění příkazu pro povolení šifrování. Přijatelné hodnoty parametru volume-yype jsou Všechny, OS a Data. Možná budete muset změnit parametr typu svazku na operační systém nebo data, pokud šifrujete jenom jeden typ disku pro virtuální počítače. Příklady používají "Vše" pro parametr typu svazku. 

-  **Šifrování spuštěného virtuálního virtuálního klienta pomocí tajného klíče klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Šifrování spuštěného virtuálního virtuálního klienta pomocí kek zabalit tajný klíč klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Povolte šifrování pomocí ověřování na základě klientského certifikátu Azure AD.
Ověřování klientského certifikátu můžete používat s kek nebo bez něj. Před použitím skriptů Prostředí PowerShell byste už měli mít certifikát nahraný do trezoru klíčů a nasazený do virtuálního soudu. Pokud používáte KEK příliš, KEK by již měla existovat. Další informace najdete v části [ověřování na základě certifikátu pro Azure AD](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) článku požadavky.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Povolení šifrování pomocí ověřování na základě certifikátu pomocí Azure PowerShellu

```powershell
## Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$VMRGName = 'MyVirtualMachineResourceGroup'
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Povolení šifrování pomocí ověřování na základě certifikátu a kek u Azure PowerShellu

```powershell
# Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$VMRGName = 'MyVirtualMachineResourceGroup';
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable.

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>Zakázat šifrování
Šifrování můžete zakázat pomocí Azure PowerShellu, Azure CLI nebo pomocí šablony Správce prostředků. 

- **Zakázání šifrování disku pomocí Azure PowerShellu:** Chcete-li zakázat šifrování, použijte rutinu [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Zakažte šifrování pomocí azure cli:** Chcete-li zakázat šifrování, použijte příkaz [zakázat šifrování az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Zakázání šifrování pomocí šablony Správce prostředků:** 

    1. Klikněte **na Nasadit do Azure** z [zakázat šifrování disku na spuštěné šabloně virtuálního počítače windows.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)
    2. Vyberte předplatné, skupinu prostředků, umístění, virtuální hod, právní podmínky a smlouvu.
    3.  Chcete-li zakázat šifrování disku na spuštěném virtuálním počítači se systémem Windows, klepněte na tlačítko **Koupit.** 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Azure Disk Encryption – přehled](disk-encryption-overview.md)
