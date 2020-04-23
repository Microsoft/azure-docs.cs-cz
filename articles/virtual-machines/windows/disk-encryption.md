---
title: Šifrování spravovaných disků Azure na straně serveru – PowerShell
description: Azure Storage chrání vaše data šifrováním v klidovém stavu, než je uchovává do clusterů úložiště. Pro šifrování spravovaných disků se můžete spolehnout na klíče spravované společností Microsoft nebo můžete pomocí klíčů spravovaných zákazníkem spravovat šifrování pomocí vlastních klíčů.
author: roygara
ms.date: 04/21/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: 930fcb4c023dc58fe0eeea65aa3fa5f78569e628
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085667"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Šifrování spravovaných disků Azure na straně serveru

Disky spravované Azure automaticky šifrují vaše data ve výchozím nastavení, když je uchovávají do cloudu. Šifrování na straně serveru chrání vaše data a pomáhá vám plnit závazky organizace v oblasti zabezpečení a dodržování předpisů. Data na spravovaných discích Azure se šifrují transparentně pomocí 256bitového [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), což je jedna z nejsilnějších blokových šifer, která je k dispozici, a je kompatibilní s FIPS 140-2.

Šifrování nemá vliv na výkon spravovaných disků. Šifrování neplatí žádné další náklady.

Další informace o kryptografických modulech, na kterých jsou založeny spravované disky Azure, najdete v [tématu Cryptography API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Pro šifrování spravovaného disku se můžete spolehnout na klíče spravované platformou nebo můžete spravovat šifrování pomocí vlastních klíčů. Pokud se rozhodnete spravovat šifrování pomocí vlastních klíčů, můžete zadat *klíč spravovaný zákazníkem,* který se použije pro šifrování a dešifrování všech dat na spravovaných discích. 

Následující části popisují každou z možností správy klíčů podrobněji.

## <a name="platform-managed-keys"></a>Klíče spravované platformou

Ve výchozím nastavení používají spravované disky šifrovací klíče spravované platformou. června 2017 jsou všechny nové spravované disky, snímky, obrázky a nová data zapsaná na existující spravované disky automaticky šifrovány v klidovém stavu pomocí klíčů spravovaných platformou.

## <a name="customer-managed-keys"></a>Klíče spravované zákazníkem

Šifrování můžete spravovat na úrovni každého spravovaného disku pomocí vlastních klíčů. Šifrování na straně serveru pro spravované disky s klíči spravovanými zákazníky nabízí integrované prostředí s azure key vaultem. Můžete buď importovat [klíče RSA](../../key-vault/keys/hsm-protected-keys.md) do trezoru klíčů nebo generovat nové klíče RSA v úložišti klíčů Azure. 

Disky spravované Azure zpracovávají šifrování a dešifrování zcela transparentním způsobem pomocí [šifrování obálek](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Šifruje data pomocí šifrovacího klíče (DEK) založeného na [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256, který je zase chráněn pomocí vašich klíčů. Služba Storage generuje šifrovací klíče dat a šifruje je pomocí klíčů spravovaných zákazníkem pomocí šifrování RSA. Šifrování obálek umožňuje pravidelně otáčet (měnit) klíče podle zásad dodržování předpisů, aniž by to mělo vliv na vaše virtuální počítače. Při otočení klíčů služba Storage znovu šifruje šifrovací klíče dat pomocí nových klíčů spravovaných zákazníkem. 

Chcete-li používat klíče k šifrování a dešifrování sady DEK, musíte udělit přístup ke spravovaným diskům v trezoru klíčů. To vám umožní plnou kontrolu nad daty a klíči. Klíče můžete kdykoli zakázat nebo odvolat přístup ke spravovaným diskům. Využití šifrovacího klíče můžete také auditovat pomocí monitorování azure trezoru klíčů, abyste zajistili, že k vašim klíčům přistupují jenom spravované disky nebo jiné důvěryhodné služby Azure.

U prémiových disků SSD, standardních disků SSD a standardních pevných disků: Když klíč zakážete nebo odstraníte, všechny virtuální počítače s disky používajícími tento klíč se automaticky vypnou. Poté virtuální chody nebude použitelné, pokud klíč je povolen znovu nebo přiřadit nový klíč.

U ultra disků se při zakázání nebo odstranění klíče všechny virtuální počítače s ultra disky používající klíč automaticky nevypnou. Jakmile navrátíte a restartujete virtuální počítače, disky přestanou klíč používat a virtuální počítače se nevrátí do režimu online. Chcete-li virtuální chod znovu uvést do režimu online, musíte přiřadit nový klíč nebo povolit existující klíč.

Následující diagram znázorňuje, jak spravované disky používají Azure Active Directory a Azure Key Vault k vytváření požadavků pomocí klíče spravovaného zákazníkem:

![Pracovní postup spravovaného disku a klíčů spravovaných zákazníkem. Správce vytvoří Azure Key Vault, pak vytvoří sadu šifrování disku a nastaví sadu šifrování disku. Sada je přidružena k virtuálnímu počítači, který umožňuje disku využívat Azure AD k ověření](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


Následující seznam vysvětluje diagram podrobněji:

1. Správce trezoru klíčů Azure vytvoří prostředky trezoru klíčů.
1. Správce trezoru klíčů buď importuje své klíče RSA do trezoru klíčů, nebo vygeneruje nové klíče RSA v trezoru klíčů.
1. Tento správce vytvoří instanci prostředku sada šifrování disku a zadává ID trezoru klíčů Azure a adresu URL klíče. Sada šifrování disku je nový prostředek zavedený pro zjednodušení správy klíčů pro spravované disky. 
1. Při vytvoření sady šifrování disku se ve službě Azure Active Directory (AD) vytvoří [spravovaná identita přiřazená systémem](../../active-directory/managed-identities-azure-resources/overview.md) a bude přidružena k sadě šifrování disku. 
1. Správce trezoru klíčů Azure pak uděluje oprávnění spravované identity k provádění operací v trezoru klíčů.
1. Uživatel virtuálního počítače vytvoří disky tak, že je přisuzuje k sadě šifrování disku. Uživatel virtuálního počítače můžete také povolit šifrování na straně serveru s klíči spravovanými zákazníkem pro stávající prostředky jejich přidruženou sadou šifrování disku. 
1. Spravované disky používají spravovanou identitu k odesílání požadavků do trezoru klíčů Azure.
1. Pro čtení nebo zápis dat, spravované disky odesílá požadavky na Azure Key Vault šifrovat (zalomit) a dešifrovat (rozbalit) šifrovací klíč dat za účelem provedení šifrování a dešifrování dat. 

Pokud chcete odvolat přístup ke klíčům spravovaným zákazníkem, přečtěte si informace o [powershellu Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) a [cli trezoru klíčů Azure](https://docs.microsoft.com/cli/azure/keyvault). Zrušení přístupu efektivně blokuje přístup ke všem datům v účtu úložiště, protože šifrovací klíč není pro Azure Storage přístupný.

### <a name="supported-regions"></a>Podporované oblasti

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Omezení

Prozatím mají klíče spravované zákazníkem následující omezení:

- Pokud je tato funkce pro disk povolena, nelze ji zakázat.
    Pokud to potřebujete obejít, musíte [zkopírovat všechna data](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) na zcela jiný spravovaný disk, který nepoužívá klíče spravované zákazníkem.
- Podporovány jsou pouze ["měkké" a "tvrdé" RSA klíče](../../key-vault/keys/about-keys.md) velikosti 2080, žádné jiné klíče nebo velikosti.
- Disky vytvořené z vlastních bitových kopií, které jsou šifrovány pomocí šifrování na straně serveru a klíčů spravovaných zákazníkem, musí být šifrovány pomocí stejných klíčů spravovaných zákazníkem a musí být ve stejném předplatném.
- Snímky vytvořené z disků, které jsou šifrovány pomocí šifrování na straně serveru a klíčů spravovaných zákazníkem, musí být šifrovány stejnými klíči spravovanými zákazníky.
- Vlastní bitové kopie šifrované pomocí šifrování na straně serveru a klíčů spravovaných zákazníkem nelze použít ve sdílené galerii obrázků.
- Všechny prostředky související s vašimi klíči spravovanými zákazníky (trezory klíčů Azure, sady šifrování disku, virtuální počítače, disky a snímky) musí být ve stejném předplatném a oblasti.
- Disky, snímky a bitové kopie zašifrované pomocí klíčů spravovaných zákazníkem se nemohou přesunout do jiného předplatného.
- Pokud k vytvoření sady šifrování disku použijete portál Azure, nemůžete prozatím použít snímky.
- Spravované disky zašifrované pomocí klíčů spravovaných zákazníkem nelze šifrovat také pomocí Azure Disk Encryption.

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Nastavení trezoru klíčů Azure a sady DiskEncryptionSet

1. Ujistěte se, že jste nainstalovali nejnovější [verzi Azure PowerShellu](/powershell/azure/install-az-ps)a že jste přihlášení k účtu Azure pomocí connect-azaccount.

1. Vytvořte instanci trezoru klíčů Azure a šifrovacího klíče.

    Při vytváření instance trezoru klíčů je nutné povolit ochranu proti odstranění a vymazání pomocí funkce Obnovitelné odstranění a vymazání. Obnovitelné odstranění zajistí, že trezor klíčů obsahuje odstraněný klíč po danou dobu uchovávání (výchozí nastavení 90 dnů). Ochrana proti vymazání zajišťuje, že odstraněný klíč nelze trvale odstranit, dokud nevyprší doba uchování. Tato nastavení vás chrání před ztrátou dat v důsledku náhodného odstranění. Tato nastavení jsou povinná při použití trezoru klíčů pro šifrování spravovaných disků.

    > [!IMPORTANT]
    > Nepoužívejte camel případě oblasti, pokud tak učiníte může dojít k problémům při přiřazování další disky k prostředku na webu Azure Portal.
    
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

1.    Vytvořte instanci sady DiskEncryptionSet. 
    
        ```powershell
        $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned
        
        $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
        ```

1.    Udělte prostředku DiskEncryptionSet přístup k trezoru klíčů.

        > [!NOTE]
        > Může trvat několik minut, než Azure vytvoří identitu sady DiskEncryptionSet ve službě Azure Active Directory. Pokud se při spuštění následujícího příkazu zobrazí chyba jako Nelze najít objekt služby Active Directory, počkejte několik minut a akci opakujte.
        
        ```powershell
        $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
         
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
         
        New-AzRoleAssignment -ResourceName $keyVaultName -ResourceGroupName $ResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader" 
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Vytvoření virtuálního počítače pomocí bitové kopie Marketplace, šifrování operačního systému a datových disků pomocí klíčů spravovaných zákazníkem

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

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Vytvoření prázdného disku zašifrovaného pomocí šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a jeho připojení k virtuálnímu počítače

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

#### <a name="encrypt-existing-unattached-managed-disks"></a>Šifrování existujících nepřipojených spravovaných disků 

Existující disky nesmí být připojené ke spuštěnému virtuálnímu počítači, abyste je mohli zašifrovat pomocí následujícího skriptu:

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Vytvoření škálovací sady virtuálních počítačů pomocí bitové kopie Marketplace, šifrování operačního systému a datových disků pomocí klíčů spravovaných zákazníkem

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

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Změna klíče sady DiskEncryptionSet otočení matou klíče pro všechny prostředky odkazující na sadu DiskEncryptionSet

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$keyVaultName="yourKeyVaultName"
$keyName="yourKeyName"
$diskEncryptionSetName="yourDiskEncryptionSetName"

$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $ResourceGroupName

$keyVaultKey = Get-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName

Update-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -SourceVaultId $keyVault.ResourceId -KeyUrl $keyVaultKey.Id
```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Nalezení stavu šifrování disku na straně serveru

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$DiskName="yourDiskName"

$disk=Get-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName
$disk.Encryption.Type

```

> [!IMPORTANT]
> Klíče spravované zákazníkem spoléhají na spravované identity pro prostředky Azure, což je funkce Azure Active Directory (Azure AD). Při konfiguraci klíčů spravovaných zákazníkem je spravovaná identita automaticky přiřazena k vašim prostředkům pod kryty. Pokud následně přesunete předplatné, skupinu prostředků nebo spravovaný disk z jednoho adresáře Azure AD do jiného, spravovaná identita přidružená ke spravovaným diskům se nepřenese do nového klienta, takže klíče spravované zákazníkem už nemusí fungovat. Další informace najdete [v tématu Přenos předplatného mezi adresáři Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem spoléhají na spravované identity pro prostředky Azure, což je funkce Azure Active Directory (Azure AD). Při konfiguraci klíčů spravovaných zákazníkem je spravovaná identita automaticky přiřazena k vašim prostředkům pod kryty. Pokud následně přesunete předplatné, skupinu prostředků nebo spravovaný disk z jednoho adresáře Azure AD do jiného, spravovaná identita přidružená ke spravovaným diskům se nepřenese do nového klienta, takže klíče spravované zákazníkem už nemusí fungovat. Další informace najdete [v tématu Přenos předplatného mezi adresáři Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Šifrování na straně serveru versus šifrování disku Azure

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) využívá funkci [Nástroje BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) systému Windows a [funkce DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) linuxu k šifrování spravovaných disků pomocí klíčů spravovaných zákazníky v rámci hostovaného virtuálního počítače.  Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem vylepšuje ade tím, že umožňuje používat všechny typy operačních systémů a image pro virtuální počítače šifrováním dat ve službě Storage.

## <a name="next-steps"></a>Další kroky

- [Prozkoumejte šablony Azure Resource Manageru pro vytváření šifrovaných disků pomocí klíčů spravovaných zákazníky](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Co je Azure Key Vault?](../../key-vault/general/overview.md)
- [Replikace počítačů s disky s povolenými klíči spravovanými zákazníky](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Nastavení zotavení virtuálních počítačů VMware v oblasti havárií do Azure pomocí PowerShellu](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Nastavení zotavení po havárii na Azure for Hyper-V Virtuální počítače pomocí PowerShellu a Azure Resource Manageru](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
