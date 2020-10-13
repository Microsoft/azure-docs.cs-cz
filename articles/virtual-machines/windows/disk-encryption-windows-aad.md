---
title: Azure Disk Encryption s Azure AD pro virtuální počítače s Windows (předchozí verze)
description: Tento článek poskytuje pokyny k povolení Microsoft Azureho šifrování disku pro virtuální počítače s Windows IaaS.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: security
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 0e8ea218aa9c557fb109aee0dba318cfd5f605c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87836237"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Azure Disk Encryption s Azure AD pro virtuální počítače s Windows (předchozí verze)

**Nová vydaná verze Azure Disk Encryption eliminuje požadavek na poskytnutí parametru aplikace Azure AD, aby bylo možné povolit šifrování disku virtuálního počítače. V nové verzi už nebudete muset zadávat přihlašovací údaje Azure AD během kroku povolení šifrování. Všechny nové virtuální počítače musí být zašifrované bez parametrů aplikace Azure AD, které používají novou verzi. Pokyny k povolení šifrování disků virtuálního počítače pomocí nové verze najdete v tématu [Azure Disk Encryption pro virtuální počítače s Windows](disk-encryption-windows.md). Virtuální počítače, které jsou už šifrované pomocí parametrů aplikace Azure AD, se pořád podporují a měly by se udržovat dál se syntaxí AAD.**


Můžete povolit mnoho scénářů šifrování disku a postup se může lišit v závislosti na scénáři. Následující oddíly popisují scénáře podrobněji podrobněji pro virtuální počítače s Windows IaaS. Než budete moct používat diskové šifrování, je potřeba dokončit [Azure Disk Encryption předpoklady](disk-encryption-overview-aad.md) . 


>[!IMPORTANT]
> - Předtím, než se disky zašifrují, byste měli [udělat snímek](snapshot-copy-managed-disk.md) nebo vytvořit zálohu. Zálohování zajišťuje možnost obnovení, pokud během šifrování dojde k neočekávané chybě. Virtuální počítače se spravovanými disky vyžadují zálohování před tím, než dojde k šifrování. Po provedení zálohy můžete použít [rutinu Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) k šifrování spravovaných disků zadáním parametru-skipVmBackup. Další informace o zálohování a obnovení šifrovaných virtuálních počítačů najdete v tématu [zálohování a obnovení šifrovaného virtuálního počítače Azure](../../backup/backup-azure-vms-encryption.md). 
>
> - Šifrování nebo zakázání šifrování může způsobit, že se virtuální počítač restartuje.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Povolení šifrování u nových virtuálních počítačů s IaaS vytvořených z Marketplace
Pomocí šablony Správce prostředků můžete povolit šifrování disku na novém virtuálním počítači s Windows IaaS z Marketplace v Azure. Šablona vytvoří nový zašifrovaný virtuální počítač s Windows pomocí Image Galerie Windows Serveru 2012.

1. V [šabloně správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)klikněte na **nasadit do Azure**.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, parametry, právní smlouvy a smlouvu. Kliknutím na **koupit** nasadíte nový virtuální počítač s IaaS, ve kterém je povolené šifrování.

3. Po nasazení šablony ověřte pomocí preferované metody stav šifrování virtuálního počítače:
     - Pomocí příkazu [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) ověřte pomocí Azure CLI. 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Pomocí rutiny [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) ověřte pomocí Azure PowerShell. 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Vyberte virtuální počítač a potom kliknutím na **disky** v záhlaví **Nastavení** ověřte stav šifrování na portálu. V grafu pod možností **šifrování**uvidíte, jestli je povolený. 
           ![Azure Portal – šifrování disku povoleno](../media/disk-encryption/disk-encryption-fig2.png)

V následující tabulce jsou uvedeny parametry šablon Správce prostředků pro nové virtuální počítače ze scénáře Marketplace pomocí ID klienta Azure AD:

| Parametr | Popis | 
| --- | --- |
| adminUserName | Uživatelské jméno správce pro virtuální počítač. |
| adminPassword | Uživatelské heslo správce pro virtuální počítač. |
| newStorageAccountName | Název účtu úložiště, na který se mají ukládat virtuální pevné disky s operačním systémem a daty |
| vmSize | Velikost virtuálního počítače. V současné době jsou podporovány pouze řady Standard A, D a G. |
| virtualNetworkName | Název virtuální sítě, ke které by měl patřit síťová karta virtuálního počítače. |
| subnetName | Název podsítě ve virtuální síti, do které by měl patřit síťová karta virtuálního počítače. |
| AADClientID | ID klienta aplikace Azure AD, která má oprávnění k zápisu tajných kódů do trezoru klíčů. |
| AADClientSecret | Tajný kód klienta aplikace Azure AD, který má oprávnění k zápisu tajných kódů do trezoru klíčů. |
| keyVaultURL | Adresa URL trezoru klíčů, do kterého se má klíč BitLocker nahrát Můžete ji získat pomocí rutiny `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` nebo rozhraní příkazového řádku Azure CLI. `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | Adresa URL klíčového šifrovacího klíče, který se používá k zašifrování vygenerovaného klíče nástroje BitLocker (volitelné). </br> </br>KeyEncryptionKeyURL je nepovinný parametr. Vlastní KEK můžete použít k dalšímu zabezpečení šifrovacího klíče pro přístup k datům (tajné heslo) ve vašem trezoru klíčů. |
| keyVaultResourceGroup | Skupina prostředků trezoru klíčů. |
| vmName | Název virtuálního počítače, na kterém se má operace šifrování provést. |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a> Povolení šifrování u stávajících nebo spuštěných virtuálních počítačů s IaaS s Windows
V tomto scénáři můžete šifrování povolit pomocí šablony, rutin PowerShellu nebo příkazů rozhraní příkazového řádku. Následující části podrobněji popisují, jak Azure Disk Encryption povolit. 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a> Povolení šifrování u stávajících nebo spuštěných virtuálních počítačů s Azure PowerShell 
Pomocí rutiny [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) Povolte šifrování na běžícím virtuálním počítači s IaaS v Azure. Informace o povolení šifrování u Azure Disk Encryption pomocí rutin PowerShellu najdete v blogových příspěvcích [prozkoumat Azure Disk Encryption s Azure PowerShell – 1.1](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell) . [Prozkoumejte Azure Disk Encryption s Azure PowerShell-Part 2](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell-part-2).

-  **Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta:** Skript níže inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. U skupiny prostředků, virtuálního počítače, trezoru klíčů, aplikace AAD a tajného klíče klienta by se už měly vytvořit požadavky. Hodnoty MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, my-AAD-Client-ID a my-AAD-Client-Secret nahraďte hodnotami.
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
- **Zabalením spuštěného virtuálního počítače pomocí KEK zabalíte tajný klíč klienta:** Azure Disk Encryption umožňuje zadat existující klíč do trezoru klíčů pro zabalení tajných klíčů disku, které byly generovány při povolování šifrování. Když je zadaný klíč šifrování klíče, Azure Disk Encryption používá tento klíč k zabalení šifrovacích tajných kódů před zápisem do Key Vault. 

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
   > Syntaxe hodnoty parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:/subscriptions/[ID předplatného-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name]</br> Syntaxe hodnoty parametru klíč-šifrování klíče je úplný identifikátor URI pro KEK jako v: https://[název trezoru-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

- **Ověřte, že jsou disky šifrované:** Pokud chcete zjistit stav šifrování virtuálního počítače s IaaS, použijte rutinu [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Zakázat šifrování disku:** Pokud chcete šifrování zakázat, použijte rutinu [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a>Povolení šifrování u stávajících nebo spuštěných virtuálních počítačů pomocí Azure CLI
Pomocí příkazu [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) Povolte šifrování na běžícím virtuálním počítači s IaaS v Azure.

- **Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Zabalením spuštěného virtuálního počítače pomocí KEK zabalíte tajný klíč klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Syntaxe hodnoty parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:/subscriptions/[ID předplatného-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name] </br> Syntaxe hodnoty parametru klíč-šifrování klíče je úplný identifikátor URI pro KEK jako v: https://[název trezoru-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

- **Ověřte, že jsou disky šifrované:** Pokud chcete zjistit stav šifrování virtuálního počítače s IaaS, použijte příkaz [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Zakázat šifrování:** Pokud chcete šifrování zakázat, použijte příkaz [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>Použití šablony Správce prostředků
Můžete povolit šifrování disku na existujícím nebo běžícím virtuálním počítači s IaaS s Windows v Azure pomocí [šablony Správce prostředků k šifrování spuštěného virtuálního počítače s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. V šabloně pro rychlý Start Azure klikněte na **nasadit do Azure**.

2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, parametry, právní smlouvy a smlouvu. Kliknutím na **koupit** povolíte šifrování na stávajícím nebo BĚŽÍCÍm virtuálním počítači s IaaS.

V následující tabulce jsou uvedeny parametry šablon Správce prostředků pro existující nebo běžící virtuální počítače, které používají ID klienta služby Azure AD:

| Parametr | Popis |
| --- | --- |
| AADClientID | ID klienta aplikace Azure AD, která má oprávnění k zápisu tajných kódů do trezoru klíčů. |
| AADClientSecret | Tajný kód klienta aplikace Azure AD, který má oprávnění k zápisu tajných kódů do trezoru klíčů. |
| keyVaultName | Název trezoru klíčů, do kterého se má klíč BitLocker nahrát Můžete ji získat pomocí rutiny `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` nebo příkazu rozhraní příkazového řádku Azure CLI. `az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | Adresa URL klíčového šifrovacího klíče, který se používá k zašifrování vygenerovaného klíče nástroje BitLocker. Tento parametr je nepovinný, pokud v rozevíracím seznamu UseExistingKek vyberete **nokek** . Pokud v rozevíracím seznamu UseExistingKek vyberete možnost **KEK** , musíte zadat hodnotu _keyEncryptionKeyURL_ . |
| volumeType | Typ svazku, na kterém se operace šifrování provádí. Platné hodnoty jsou _operační systém_, _data_a _vše_. |
| sequenceVersion | Verze sekvence operace nástroje BitLocker Zvyšte číslo této verze pokaždé, když se na stejném virtuálním počítači provede operace šifrování disku. |
| vmName | Název virtuálního počítače, na kterém se má operace šifrování provést. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>Nové virtuální počítače s IaaS vytvořené z VHD a šifrovacích klíčů šifrovaných zákazníkem
V tomto scénáři můžete povolit šifrování pomocí Správce prostředků šablony, rutin prostředí PowerShell nebo příkazů rozhraní příkazového řádku. Následující části podrobněji popisují Správce prostředků šablon a příkazů rozhraní příkazového řádku. 

Použijte pokyny v příloze pro přípravu předem šifrovaných imagí, které je možné použít v Azure. Po vytvoření image můžete pomocí kroků v následující části vytvořit zašifrovaný virtuální počítač Azure.

* [Příprava předem zašifrovaného virtuálního pevného disku s Windows](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"> </a> Šifrování virtuálních počítačů s předem šifrovanými virtuálními počítači pomocí Azure PowerShell
Pomocí rutiny PowerShellu [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples)můžete povolit šifrování disku na šifrovaném virtuálním pevném disku. V následujícím příkladu jsou uvedeny některé běžné parametry. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Povolení šifrování u nově přidaného datového disku
[Nový disk můžete přidat do virtuálního počítače s Windows pomocí PowerShellu](attach-disk-ps.md)nebo [prostřednictvím Azure Portal](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Povolit šifrování na nově přidaném disku s Azure PowerShell
 Při použití PowerShellu k šifrování nového disku pro virtuální počítače s Windows by se měla zadat nová verze sekvence. Verze sekvence musí být jedinečná. Následující skript vygeneruje identifikátor GUID pro verzi sekvence. V některých případech může být nově přidaný datový disk automaticky zašifrovaný pomocí rozšíření Azure Disk Encryption. K automatickému šifrování obvykle dochází, když se virtuální počítač restartuje po přechodu nového disku do režimu online. To je obvykle způsobeno tím, že pro daný typ svazku byly zadány možnosti All, když se na virtuálním počítači dříve spustilo šifrování disku. Pokud automatické šifrování probíhá na nově přidaném datovém disku, doporučujeme znovu spustit rutinu Set-AzVmDiskEncryptionExtension s novou verzí sekvence. Pokud je nový datový disk automaticky zašifrovaný a nechcete být zašifrovaný, Dešifrujte všechny jednotky a pak znovu Zašifrujte novou verzí sekvence určující operační systém pro daný typ svazku. 
 

-  **Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta:** Skript níže inicializuje proměnné a spustí rutinu Set-AzVMDiskEncryptionExtension. U skupiny prostředků, virtuálního počítače, trezoru klíčů, aplikace AAD a tajného klíče klienta by se už měly vytvořit požadavky. Hodnoty MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, my-AAD-Client-ID a my-AAD-Client-Secret nahraďte hodnotami. V tomto příkladu se používá "All" pro parametr-VolumeType, který zahrnuje operační systém i svazky dat. Pokud chcete svazek operačního systému zašifrovat jenom, použijte pro parametr-VolumeType "OS". 

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
- **Zabalením spuštěného virtuálního počítače pomocí KEK zabalíte tajný klíč klienta:** Azure Disk Encryption umožňuje zadat existující klíč do trezoru klíčů pro zabalení tajných klíčů disku, které byly generovány při povolování šifrování. Když je zadaný klíč šifrování klíče, Azure Disk Encryption používá tento klíč k zabalení šifrovacích tajných kódů před zápisem do Key Vault. V tomto příkladu se používá "All" pro parametr-VolumeType, který zahrnuje operační systém i svazky dat. Pokud chcete svazek operačního systému zašifrovat jenom, použijte pro parametr-VolumeType "OS". 

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
    > Syntaxe hodnoty parametru Disk-Encryption-trezor je úplný řetězec identifikátoru:/subscriptions/[ID předplatného-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[Trezor klíčů-name]</br> Syntaxe hodnoty parametru klíč-šifrování klíče je úplný identifikátor URI pro KEK jako v: https://[název trezoru-name]. trezor. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Povolení šifrování u nově přidaného disku pomocí Azure CLI
  Příkaz Azure CLI vám při spuštění příkazu pro povolení šifrování automaticky poskytne novou verzi sekvence. Přijatelné hodnoty pro parametr Volume-yype jsou všechny, operační systém a data. Pokud šifrujete jenom jeden typ disku pro virtuální počítač, možná budete muset změnit parametr typu svazku na operační systém nebo na data. V příkladech se pro parametr Volume-Type používá "All". 

-  **Šifrování spuštěného virtuálního počítače pomocí tajného klíče klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Zabalením spuštěného virtuálního počítače pomocí KEK zabalíte tajný klíč klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Povolte šifrování pomocí ověřování na základě certifikátu klienta služby Azure AD.
Ověřování klientským certifikátem můžete použít s KEK nebo bez něj. Před použitím skriptů PowerShell byste už měli mít certifikát uložený do trezoru klíčů a nasadili ho do virtuálního počítače. Pokud používáte KEK, KEK by již měl existovat. Další informace najdete v části  [ověřování na základě certifikátů pro Azure AD](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) v článku požadavky.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Povolení šifrování pomocí ověřování na základě certifikátů pomocí Azure PowerShell

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
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Povolení šifrování pomocí ověřování založeného na certifikátu a KEK s Azure PowerShell

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
Šifrování můžete zakázat pomocí Azure PowerShell, rozhraní příkazového řádku Azure nebo pomocí Správce prostředků šablony. 

- **Zakázat šifrování disku pomocí Azure PowerShell:** Pokud chcete šifrování zakázat, použijte rutinu [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Zakázat šifrování pomocí Azure CLI:** Pokud chcete šifrování zakázat, použijte příkaz [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Zakázat šifrování pomocí šablony Správce prostředků:** 

    1. Kliknutím na **nasadit do Azure** z části [zakázat šifrování disku při spuštění šablony virtuálního počítače s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)  .
    2. Vyberte předplatné, skupinu prostředků, umístění, virtuální počítač, právní podmínku a smlouvu.
    3.  Kliknutím na **koupit** zakažte šifrování disku na běžícím virtuálním počítači s Windows. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přehled Azure Disk Encryption](disk-encryption-overview.md)