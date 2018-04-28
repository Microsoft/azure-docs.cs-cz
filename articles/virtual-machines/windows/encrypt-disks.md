---
title: Šifrování disky na virtuální počítač s Windows v Azure | Microsoft Docs
description: Postup zašifrování virtuální disky na virtuální počítač s Windows pro zvýšení zabezpečení pomocí Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/07/2018
ms.author: iainfou
ms.openlocfilehash: 442ff942150af8a8dec89164fbc017a9e6f360e8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-encrypt-virtual-disks-on-a-windows-vm"></a>Postup zašifrování virtuální disky na virtuální počítač s Windows
Pro lepší virtuální počítač (VM) zabezpečení a dodržování předpisů je možné zašifrovat virtuální disky v Azure. Disky jsou šifrované pomocí kryptografických klíčů, které jsou zabezpečené v Azure Key Vault. Řízení těchto kryptografické klíče a můžete auditovat jejich použití. Tento článek podrobně popisují zašifrovat virtuální disky na virtuální počítač s Windows pomocí Azure PowerShell. Můžete také [šifrování virtuálního počítače s Linuxem pomocí Azure CLI 2.0](../linux/encrypt-disks.md).

## <a name="overview-of-disk-encryption"></a>Přehled šifrování disku
Virtuální disky na virtuální počítače Windows jsou zašifrovaná přinejmenším pomocí nástroje Bitlocker. Není nijak zpoplatněn pro šifrování virtuálních disků v Azure. Kryptografické klíče ukládají v Azure Key Vault pomocí ochrany proti softwaru, nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovány pro FIPS 140-2 úroveň 2 standardů. Tyto klíče se používají k šifrování a dešifrování virtuálních disků připojených k virtuálnímu počítači. Uchování kontroly nad těchto kryptografické klíče a můžete auditovat jejich použití. Hlavní služby Azure Active Directory poskytuje zabezpečené mechanismus pro vydávání tyto kryptografické klíče jako virtuální počítače jsou zapnuté zapnout a vypnout.

Proces šifrování virtuálního počítače je následující:

1. Vytvoření kryptografické klíče v Azure Key Vault.
2. Nakonfigurujte kryptografický klíč možné používat pro šifrování disků.
3. Čtení kryptografického klíče z Azure Key Vault, vytvořte služby Azure Active Directory objekt s příslušnými oprávněními.
4. Příkaz pro zašifrování virtuální disky, zadávání Azure Active Directory service hlavní a vhodný kryptografické klíče k použití.
5. Objekt služby Azure Active Directory vyžaduje požadované kryptografický klíč z Azure Key Vault.
6. Virtuální disky jsou šifrované pomocí zadaný kryptografický klíč.

## <a name="encryption-process"></a>Proces šifrování
Šifrování disku spoléhá na následující součásti:

* **Azure Key Vault** – používané k ochraně kryptografické klíče a tajné klíče pro proces šifrování a dešifrování disku. 
  * Pokud ano, můžete použít existující Azure Key Vault. Není nutné vyhradit Key Vault pro šifrování disků.
  * K oddělení hranicemi správy a klíče viditelnost, můžete vytvořit vyhrazený Key Vault.
* **Azure Active Directory** -zpracovává zabezpečené výměna požadované kryptografické klíče a ověřování pro požadované akce. 
  * Pro uložení aplikace můžete obvykle použít existující instanci služby Azure Active Directory.
  * Objekt služby poskytuje zabezpečené mechanismus k vyžádání a vydávají příslušné kryptografické klíče. Nevyvíjíte skutečné aplikace, která se integruje se službou Azure Active Directory.

## <a name="requirements-and-limitations"></a>Požadavky a omezení
Podporované scénáře a požadavky na šifrování disku:

* Povoluje šifrování na nové virtuální počítače Windows z Azure Marketplace obrázky nebo vlastní image virtuálního pevného disku.
* Povoluje šifrování na stávajících virtuálních počítačích Windows v Azure.
* Povoluje šifrování na virtuálních počítačích Windows, které jsou nakonfigurované pomocí prostorů úložiště.
* Zakázáním šifrování na operačního systému a datové disky pro virtuální počítače Windows.
* Všechny prostředky (například Key Vault, účet úložiště a virtuálních počítačů) musí být ve stejné oblasti Azure a předplatné.
* Úroveň Standard virtuálních počítačů, jako je například, D, DS, G a GS řadu virtuálních počítačů.

Šifrování disku není aktuálně podporováno v následujících scénářích:

* Úroveň Basic virtuálních počítačů.
* Virtuální počítače vytvořené pomocí modelu nasazení Classic.
* Aktualizuje se kryptografické klíče již šifrované virtuálního počítače.
* Integrace s místní službu správy klíčů.

## <a name="create-azure-key-vault-and-keys"></a>Vytvoření Azure Key Vault a klíče
Než začnete, ujistěte se, že je nainstalovaná nejnovější verze modulu Azure PowerShell. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). V příkladech nahraďte všechny parametry příklad vlastní názvy, umístění a hodnoty klíče. Následující příklady použití konvence *myResourceGroup*, *myKeyVault*, *Můjvp*atd.

Prvním krokem je vytvoření Azure Key Vault pro ukládání kryptografických klíčů. Azure Key Vault můžete uložit klíčů, tajné klíče nebo hesla, které vám umožní bezpečně je implementovat v vašim aplikacím a službám. Šifrování virtuálního disku vytvoříte Key Vault pro uložení kryptografického klíče, který se používá k šifrování a dešifrování virtuální disky. 

Povolit Azure Key Vault zprostředkovatele v rámci vašeho předplatného Azure s [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider), pak vytvořte skupinu prostředků s [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Následující příklad vytvoří název skupiny prostředků *myResourceGroup* v *východní USA* umístění:

```powershell
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

Azure Key Vault, který obsahuje kryptografické klíče a přidružené výpočetní prostředky, jako je například úložiště a virtuální počítač se musí nacházet ve stejné oblasti. Vytvoření Azure Key Vault s [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) a povolte Key Vault pro použití s šifrování disku. Zadejte jedinečný název pro Key Vault *keyVaultName* následujícím způsobem:

```powershell
$keyVaultName = "myUniqueKeyVaultName"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Můžete uložit kryptografické klíče pomocí softwaru nebo ochrany modelu hardwarového zabezpečení (HSM). Použití modulu hardwarového zabezpečení vyžaduje premium Key Vault. Není dalších nákladů na vytváření premium Key Vault, nikoli standardní Key Vault, který ukládá klíče chráněné softwarem. Chcete-li vytvořit premium Key Vault, přidejte v předchozím kroku *- Sku "Premium"* parametry. Následující příklad používá klíče chráněné softwarem, protože jsme vytvořili standardní Key Vault. 

Pro oba modely ochrany musí mít udělen přístup k žádosti o kryptografických klíčů, když se virtuální počítač spustí do dešifrovat virtuální disky platformy Azure. Vytvoření kryptografické klíče v Key Vault s [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey). Následující příklad vytvoří klíč s názvem *myKey*:

```powershell
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```


## <a name="create-the-azure-active-directory-service-principal"></a>Vytvořit objekt služby Azure Active Directory
Když virtuální disky jsou zašifrovaná nebo dešifrovat, je třeba zadat účet, který chcete zpracovávat ověřování a výměna kryptografických klíčů z Key Vault. Tento účet objektu zabezpečení služby Azure Active Directory umožňuje platformy Azure k vyžádání příslušné kryptografické klíče jménem virtuálního počítače. Výchozí instance služby Azure Active Directory je ve vašem předplatném dostupná, když máte mnoho organizací vyhrazené adresáře služby Azure Active Directory.

Vytvoření instančního objektu v Azure Active Directory s [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal). Chcete-li zadat zabezpečeného hesla, postupujte podle [zásady hesel a omezení v Azure Active Directory](../../active-directory/authentication/concept-sspr-policy.md):

```powershell
$appName = "My App"
$securePassword = ConvertTo-SecureString -String "P@ssw0rd!" -AsPlainText -Force
$app = New-AzureRmADApplication -DisplayName $appName `
    -HomePage "https://myapp.contoso.com" `
    -IdentifierUris "https://contoso.com/myapp" `
    -Password $securePassword
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
```

Pro úspěšně šifrování nebo dešifrování virtuálních disků, musí se nastavit oprávnění na kryptografický klíč uložený v Key Vault tak, aby povolovala objektu služby Azure Active Directory ke čtení klíče. Nastavení oprávnění pro Key Vault s [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy):

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyvaultName `
    -ServicePrincipalName $app.ApplicationId `
    -PermissionsToKeys "WrapKey" `
    -PermissionsToSecrets "Set"
```


## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače
Chcete-li otestovat proces šifrování, vytvořte virtuální počítač s [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm). Následující příklad vytvoří virtuální počítač s názvem *Můjvp* pomocí *Windows Server 2016 Datacenter* bitové kopie. Po zobrazení výzvy k zadání pověření zadejte uživatelské jméno a heslo, který se má použít pro virtuální počítač:

```powershell
$cred = Get-Credential

New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-virtual-machine"></a>Šifrování virtuálního počítače
Pokud chcete zašifrovat virtuální disky, přepnutím společně předchozí komponenty:

1. Zadejte objektu služby Azure Active Directory a heslo.
2. Zadejte klíč trezoru k ukládání metadat pro šifrované disky.
3. Zadejte kryptografické klíče k použití pro skutečné šifrování a dešifrování.
4. Zadejte, jestli chcete šifrovat disk operačního systému, datových disků nebo všechny.

Šifrování virtuálního počítače s [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) pomocí Azure Key Vault klíče a hlavní přihlašovací údaje služby Azure Active Directory. Následující příklad načte všechny klíčové informace potom šifruje virtuálního počítače s názvem *Můjvp*:

```powershell
$keyVault = Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -AadClientID $app.ApplicationId `
    -AadClientSecret (New-Object PSCredential "user",$securePassword).GetNetworkCredential().Password `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Přijetí výzvy a pokračujte šifrování virtuálních počítačů. Virtuální počítač se restartuje během procesu. Po dokončení procesu šifrování a virtuální počítač byl restartován, zkontrolujte stav šifrování s [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus):

```powershell
Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

Výstup se podobá následujícímu příkladu:

```powershell
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Další postup
* Další informace o správě Azure Key Vault najdete v tématu [nastavit Key Vault pro virtuální počítače](key-vault-setup.md).
* Další informace o šifrování disku, jako je například příprava šifrované vlastních virtuálních počítačů se nahrát do Azure, najdete v části [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
