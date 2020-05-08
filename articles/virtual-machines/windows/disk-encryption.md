---
title: Šifrování na straně serveru Azure Managed Disks – PowerShell
description: Azure Storage chrání vaše data tím, že je před tím, než je zachová v clusterech úložiště, v klidovém prostředí. Pro šifrování svých spravovaných disků můžete spoléhat na klíče spravované Microsoftem, případně můžete pomocí klíčů spravovaných zákazníkem spravovat šifrování pomocí vlastních klíčů.
author: roygara
ms.date: 04/21/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: f6b6e261b5cdfee00a9346338b6eb5d7e1f2a2a0
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982101"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Šifrování na straně serveru Azure Managed disks

Služba Azure Managed disks automaticky šifruje vaše data ve výchozím nastavení při uchování do cloudu. Šifrování na straně serveru chrání vaše data a pomáhá splnit závazky zabezpečení a dodržování předpisů vaší organizace. Data ve službě Azure Managed disks jsou transparentně šifrovaná pomocí 256 [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), což je jedna z nejúčinnějších šifrovacích šifr, která jsou kompatibilní se standardem FIPS 140-2.

Šifrování nemá vliv na výkon spravovaných disků. Pro šifrování se neúčtují žádné další náklady.

Další informace o kryptografických modulech založených na službě Azure Managed disks najdete v tématu [kryptografie API: Next Generation.](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Pro šifrování spravovaného disku můžete spoléhat na klíče spravované platformou, nebo můžete šifrování spravovat pomocí vlastních klíčů. Pokud se rozhodnete spravovat šifrování pomocí vlastních klíčů, můžete zadat *klíč spravovaný zákazníkem* , který se použije k šifrování a dešifrování všech dat ve službě Managed disks. 

V následujících částech jsou podrobněji popsány všechny možnosti správy klíčů.

## <a name="platform-managed-keys"></a>Klíče spravované platformou

Ve výchozím nastavení používají spravované disky šifrovací klíče spravované platformou. Od 10. června 2017 se všechny nové spravované disky, snímky, image a nová data zapsaná na stávající spravované disky automaticky zašifrují bez použití klíčů spravovaných platformou.

## <a name="customer-managed-keys"></a>Klíče spravované zákazníkem

Můžete zvolit správu šifrování na úrovni každého spravovaného disku s vlastními klíči. Šifrování na straně serveru pro spravované disky pomocí klíčů spravovaných zákazníkem nabízí integrované prostředí s Azure Key Vault. Můžete buď importovat [klíče RSA](../../key-vault/keys/hsm-protected-keys.md) do svého Key Vault, nebo vygenerovat nové klíče rsa v Azure Key Vault. 

Azure Managed disks zpracovává šifrování a dešifrování plně transparentním způsobem pomocí [šifrování obálek](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Šifruje data pomocí šifrovacího klíče založeného na [standardu AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 (klíč DEK), který je zase chráněn pomocí vašich klíčů. Služba úložiště generuje klíče pro šifrování dat a šifruje je pomocí klíčů spravovaných zákazníkem pomocí šifrování RSA. Šifrování obálek vám umožní pravidelně otáčet (měnit) klíče podle zásad dodržování předpisů, aniž by to mělo vliv na vaše virtuální počítače. Po otočení klíče služba úložiště znovu zašifruje klíče šifrování dat pomocí nových klíčů spravovaných zákazníkem. 

Abyste mohli používat klíče pro šifrování a dešifrování klíč DEK, musíte udělit přístup ke spravovaným diskům ve vašem Key Vault. To vám umožní plnou kontrolu nad daty a klíči. Můžete kdykoli zakázat vaše klíče nebo odvolat přístup ke spravovaným diskům. Pomocí monitorování Azure Key Vault taky můžete auditovat použití šifrovacího klíče a zajistit, aby se ke klíčům přistupovaly jenom spravované disky nebo jiné důvěryhodné služby Azure.

Pro Premium SSD, Standard SSD a Standard HDD: když zakážete nebo odstraníte klíč, všechny virtuální počítače s disky, které tento klíč používají, se automaticky vypnou. Po tomto případě nebudou virtuální počítače použitelné, pokud se klíč znovu nepovolí nebo přiřadíte nový klíč.

Pokud zakážete nebo odstraníte klíč, budou se všechny virtuální počítače s disky Ultra, které používají tento klíč, automaticky vypnout. Po zrušení přidělení a restartu virtuálních počítačů disky přestanou používat klíč a virtuální počítače se nevrátí do režimu online. Chcete-li virtuální počítače převést zpět do režimu online, je nutné přiřadit nový klíč nebo povolit existující klíč.

Následující diagram ukazuje, jak spravované disky používají Azure Active Directory a Azure Key Vault k vytváření požadavků pomocí klíče spravovaného zákazníkem:

![Pracovní postup spravovaného disku a klíčů spravovaných zákazníkem. Správce vytvoří Azure Key Vault a pak vytvoří sadu šifrování disku a nastaví sadu šifrování disku. Sada je přidružená k virtuálnímu počítači, který umožňuje disku využívat Azure AD k ověřování.](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


Následující seznam popisuje diagram podrobněji:

1. Správce Azure Key Vault vytvoří prostředky trezoru klíčů.
1. Správce trezoru klíčů buď naimportuje své klíče RSA, aby Key Vault nebo vygeneroval nové klíče RSA v Key Vault.
1. Tento správce vytvoří instanci prostředku sady šifrování disků a určí ID Azure Key Vault a adresu URL klíče. Sada šifrování disků je nově zavedený prostředek, který zjednodušuje správu klíčů pro spravované disky. 
1. Když se vytvoří sada pro šifrování disků, vytvoří se [spravovaná identita přiřazená systémem](../../active-directory/managed-identities-azure-resources/overview.md) v Azure Active Directory (AD) a přidružená k sadě šifrování disku. 
1. Správce trezoru klíčů Azure pak udělí oprávnění spravované identity k provádění operací v trezoru klíčů.
1. Uživatel virtuálního počítače vytvoří disky jejich přidružením k sadě šifrování disku. Uživatel virtuálního počítače může také povolit šifrování na straně serveru pomocí klíčů spravovaných zákazníkem pro existující prostředky jejich přidružením k sadě šifrování disku. 
1. Spravované disky používají spravovanou identitu k posílání požadavků do Azure Key Vault.
1. Pro čtení nebo zápis dat odesílají spravované disky požadavky na Azure Key Vault k šifrování (zabalení) a dešifrování (rozbalení) šifrovacího klíče dat, aby bylo možné provádět šifrování a dešifrování dat. 

Pokud chcete odvolat přístup k klíčům spravovaným zákazníkem, přečtěte si téma [Azure Key Vault PowerShellu](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) a [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Odvolání přístupu efektivně zablokuje přístup ke všem datům v účtu úložiště, protože šifrovací klíč je nepřístupný Azure Storage.

### <a name="supported-regions"></a>Podporované oblasti

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Omezení

Klíče spravované zákazníkem teď mají následující omezení:

- Pokud je tato funkce pro disk povolená, nemůžete ji zakázat.
    Pokud potřebujete tento problém obejít, musíte [zkopírovat všechna data](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) na zcela jiný spravovaný disk, který nepoužívá klíče spravované zákazníkem.
- Podporovány jsou pouze ["měkké" a "pevné" klíče RSA](../../key-vault/keys/about-keys.md) o velikosti 2080, žádné jiné klíče ani velikosti.
- Disky vytvořené z vlastních imagí šifrovaných pomocí šifrování na straně serveru a klíčů spravovaných zákazníkem musí být šifrované pomocí stejných klíčů spravovaných zákazníkem a musí být ve stejném předplatném.
- Snímky vytvořené z disků šifrovaných pomocí šifrování na straně serveru a klíčů spravovaných zákazníkem musí být šifrované pomocí stejných klíčů spravovaných zákazníkem.
- Vlastní image šifrované pomocí šifrování na straně serveru a klíčů spravovaných zákazníkem se nedají použít v galerii sdílených imagí.
- Všechny prostředky, které souvisejí s vašimi klíči spravovanými zákazníky (trezory klíčů Azure, sady šifrování disků, virtuální počítače, disky a snímky), musí být ve stejném předplatném a oblasti.
- Disky, snímky a image šifrované pomocí klíčů spravovaných zákazníkem se nedají přesunout do jiného předplatného.
- Pokud k vytvoření sady pro šifrování disků použijete Azure Portal, nemůžete teď snímky použít.
- Spravované disky šifrované pomocí šifrování na straně serveru s použitím klíčů spravovaných zákazníkem se nedají zašifrovat taky Azure Disk Encryption a naopak.

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Nastavení Azure Key Vault a DiskEncryptionSet

1. Ujistěte se, že máte nainstalovanou nejnovější [verzi Azure PowerShell](/powershell/azure/install-az-ps)a že jste k účtu Azure přihlášení pomocí Connect-AzAccount.

1. Vytvořte instanci Azure Key Vault a šifrovací klíč.

    Při vytváření instance Key Vault musíte povolit ochranu po tichém odstranění a vyprázdnění. Obnovitelné odstranění zajistí, že Key Vault obsahuje odstraněný klíč pro dané období uchování (výchozí hodnota je 90 den). Vyčištění ochrany zajišťuje, že odstraněný klíč nebude možné trvale odstranit, dokud doba uchování neuplyne. Tato nastavení chrání před ztrátou dat kvůli náhodnému odstranění. Tato nastavení jsou povinná při použití Key Vault k šifrování spravovaných disků.

    > [!IMPORTANT]
    > Neve stylu camelcasete-li se na oblast, v takovém případě se můžete setkat s problémy při přiřazování dalších disků prostředku v Azure Portal.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.    Vytvoří instanci třídy DiskEncryptionSet. 
    
        ```powershell
        $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned
        
        $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
        ```

1.    Udělte DiskEncryptionSet prostředku přístup k trezoru klíčů.

        > [!NOTE]
        > V případě, že Azure může v Azure Active Directory vytvořit identitu vašeho DiskEncryptionSetu, může to několik minut trvat. Pokud při spuštění následujícího příkazu dojde k chybě, například "Nejde najít objekt služby Active Directory", počkejte pár minut a zkuste to znovu.
        
        ```powershell
        $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
         
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
         
        New-AzRoleAssignment -ResourceName $keyVaultName -ResourceGroupName $ResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader" 
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Vytvoření virtuálního počítače pomocí Image Marketplace, šifrování OS a datových disků pomocí klíčů spravovaných zákazníkem

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
    
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Vytvořte prázdný disk zašifrovaný pomocí šifrování na straně serveru s použitím klíčů spravovaných zákazníkem a připojte ho k virtuálnímu počítači.

```PowerShell
$vmName = "yourVMName"
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = 30
$diskLUN = 1
$diskEncryptionSetName="yourDiskEncryptionSetName"


$vm = Get-AzVM -Name $vmName -ResourceGroupName $ResourceGroupName 

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Empty -DiskSizeInGB $diskSizeinGiB -StorageAccountType $diskSKU -Lun $diskLUN -DiskEncryptionSetId $diskEncryptionSet.Id 

Update-AzVM -ResourceGroupName $ResourceGroupName -VM $vm

```

#### <a name="encrypt-existing-managed-disks"></a>Zašifrovat existující spravované disky 

Vaše existující disky se nesmí připojit ke spuštěnému virtuálnímu počítači, aby je bylo možné zašifrovat pomocí následujícího skriptu:

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Vytvoření sady škálování virtuálních počítačů pomocí Image Marketplace, šifrování operačních systémů a datových disků pomocí klíčů spravovaných zákazníkem

```PowerShell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic'

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption at rest with customer managed keys for OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted at rest with customer managed keys by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Změna klíče DiskEncryptionSet pro otočení klíče pro všechny prostředky odkazující na DiskEncryptionSet

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$keyVaultName="yourKeyVaultName"
$keyName="yourKeyName"
$diskEncryptionSetName="yourDiskEncryptionSetName"

$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $ResourceGroupName

$keyVaultKey = Get-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName

Update-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -SourceVaultId $keyVault.ResourceId -KeyUrl $keyVaultKey.Id
```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Vyhledá stav šifrování disku na straně serveru.

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$DiskName="yourDiskName"

$disk=Get-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName
$disk.Encryption.Type

```

> [!IMPORTANT]
> Klíče spravované zákazníkem spoléhají na spravované identity prostředků Azure, což je funkce Azure Active Directory (Azure AD). Při konfiguraci klíčů spravovaných zákazníkem se spravovaná identita automaticky přiřadí k vašim prostředkům v rámci pokrývání. Pokud později přesunete předplatné, skupinu prostředků nebo spravovaný disk z jednoho adresáře služby Azure AD do jiného, nepřesune se do nového tenanta spravovaná identita přidružená ke spravovaným diskům, takže klíče spravované zákazníkem už možná nebudou fungovat. Další informace najdete v tématu [přenos předplatného mezi adresáři služby Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem spoléhají na spravované identity prostředků Azure, což je funkce Azure Active Directory (Azure AD). Při konfiguraci klíčů spravovaných zákazníkem se spravovaná identita automaticky přiřadí k vašim prostředkům v rámci pokrývání. Pokud později přesunete předplatné, skupinu prostředků nebo spravovaný disk z jednoho adresáře služby Azure AD do jiného, nepřesune se do nového tenanta spravovaná identita přidružená ke spravovaným diskům, takže klíče spravované zákazníkem už možná nebudou fungovat. Další informace najdete v tématu [přenos předplatného mezi adresáři služby Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Šifrování na straně serveru oproti službě Azure Disk Encryption

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) využívá funkci [nástroje BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) systému Windows a funkci [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) systému Linux k šifrování spravovaných disků pomocí klíčů spravovaných zákazníkem v rámci virtuálního počítače hosta.  Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem se zlepšuje v ADE tím, že vám umožní používat pro vaše virtuální počítače jakékoli typy operačních systémů a image šifrováním dat ve službě úložiště.

## <a name="next-steps"></a>Další kroky

- [Prozkoumejte šablony Azure Resource Manager pro vytváření šifrovaných disků pomocí klíčů spravovaných zákazníkem](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Co je Azure Key Vault?](../../key-vault/general/overview.md)
- [Replikace počítačů s povolenými disky spravovanými zákazníky](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Nastavení zotavení po havárii virtuálních počítačů VMware do Azure pomocí PowerShellu](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Nastavení zotavení po havárii do Azure pro virtuální počítače Hyper-V pomocí PowerShellu a Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
