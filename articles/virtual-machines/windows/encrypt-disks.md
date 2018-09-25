---
title: Šifrování disků na virtuálním počítači s Windows v Azure | Dokumentace Microsoftu
description: Postup zašifrování virtuální disky na virtuálním počítači s Windows pro zvýšení zabezpečení pomocí Azure Powershellu
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 20d3568fa3f583c190f087de861d857fe3e793a9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985421"
---
# <a name="how-to-encrypt-virtual-disks-on-a-windows-vm"></a>Postup zašifrování virtuální disky na virtuálním počítači s Windows
Vylepšené virtuálních počítačů (VM) zabezpečení a dodržování předpisů mohou být šifrována virtuálních disků v Azure. Disky jsou šifrované pomocí kryptografických klíčů, které jsou zabezpečené v Azure Key Vault. Řízení těchto kryptografických klíčů a auditovat jejich použití. Tento článek podrobně popisuje, jak šifrování virtuálních disků na virtuálním počítači s Windows pomocí Azure Powershellu. Můžete také [zašifrovat virtuální počítač s Linuxem pomocí Azure CLI](../linux/encrypt-disks.md).

## <a name="overview-of-disk-encryption"></a>Přehled šifrování disku
Virtuální disky na virtuálních počítačích s Windows se šifrují při nečinnosti pomocí Bitlockeru. Neplatí žádné poplatky pro šifrování virtuálních disků v Azure. Kryptografické klíče jsou uložené ve službě Azure Key Vault software ochrany, nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovaných podle standardů FIPS 140-2 úrovně 2 standardů. Tyto klíče se používají k šifrování a dešifrování virtuální disky připojené k virtuálnímu počítači. Uchování kontroly nad těmito kryptografické klíče a můžete kontrolovat jejich použití. Instančního objektu služby Azure Active Directory poskytuje zabezpečené mechanismus pro vydávání těchto kryptografických klíčů, jako jsou virtuální počítače využívající zapnout a vypnout.

Proces šifrování virtuálního počítače je následujícím způsobem:

1. Vytvoření kryptografického klíče do služby Azure Key Vault.
2. Nakonfigurujte kryptografického klíče má být použitelná pro šifrování disků.
3. Čtení kryptografického klíče z Azure Key Vault, vytvoření služby Azure Active Directory instančního objektu s příslušnými oprávněními.
4. Vydejte příkaz k šifrování virtuálních discích, určení Azure Active Directory service instančního objektu a odpovídající kryptografický klíč se použije.
5. Instanční objekt služby Azure Active Directory vyžaduje požadovaný kryptografický klíč z Azure Key Vault.
6. Virtuální disky jsou šifrované pomocí zadaného kryptografického klíče.

## <a name="encryption-process"></a>Proces šifrování
Šifrování disku spoléhá na následující součásti:

* **Služba Azure Key Vault** – slouží k ochraně kryptografických klíčů a tajných kódů využívá pro proces šifrování a dešifrování disků. 
  * Pokud ano, můžete použít stávající Azure Key Vault. Není potřeba vyhradit trezor klíčů pro šifrování disků.
  * Pro oddělení klíčů viditelnosti a hranice správy, můžete vytvořit vyhrazený trezor klíčů.
* **Azure Active Directory** – zpracovává zabezpečené výměny vyžaduje kryptografické klíče a ověřování pro požadované akce. 
  * Můžete obvykle použít existující instanci služby Azure Active Directory pro uložení vaší aplikace.
  * Instanční objekt služby poskytuje mechanismus zabezpečeného k vyžádání a vydávají odpovídající kryptografické klíče. Nevyvíjíte aplikace skutečný, která se integruje s Azure Active Directory.

## <a name="requirements-and-limitations"></a>Požadavky a omezení
Podporované scénáře a požadavky na šifrování disku:

* Povolení šifrování na nové virtuální počítače Windows z Image Azure Marketplace nebo vlastní image virtuálního pevného disku.
* Povolení šifrování na stávajících virtuálních počítačích s Windows v Azure.
* Povolení šifrování na virtuálních počítačích s Windows, které jsou nakonfigurované pomocí prostorů úložiště.
* Zakázáním šifrování u operačního systému a datové disky pro virtuální počítače s Windows.
* Všechny prostředky (například služby Key Vault, účet úložiště a virtuálních počítačů) musí být ve stejné oblasti Azure a předplatné.
* Úroveň Standard virtuálních počítačů, jako je například A, D, DS, G a GS řady virtuálních počítačů.

Šifrování disku není aktuálně podporována v následujících scénářích:

* Úroveň Basic virtuálních počítačů.
* Virtuální počítače vytvořené pomocí modelu nasazení Classic.
* Aktualizuje se kryptografické klíče na Virtuálním počítači již šifrované.
* Integrace se službou správy klíčů v místním prostředí.

## <a name="create-azure-key-vault-and-keys"></a>Vytvoření služby Azure Key Vault a klíči
Než začnete, ujistěte se, že je nainstalovaná nejnovější verze modulu Azure PowerShell. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). V příkladech příkazového nahraďte veškeré ukázkovými parametry s názvy, umístění a hodnoty klíče. Následující příklady používají konvence *myResourceGroup*, *myKeyVault*, *myVM*atd.

Prvním krokem je vytvoření služby Azure Key Vault pro ukládání kryptografických klíčů. Azure Key Vault umí ukládat klíče, tajné kódy nebo hesla, které umožňují bezpečný je implementovat ve svých aplikacích a službách. Šifrování virtuálních disků vytvoříte trezor klíčů k uložení kryptografického klíče, který slouží k šifrování nebo dešifrování virtuálních disků. 

Povolit zprostředkovatele služby Azure Key Vault v rámci vašeho předplatného Azure s [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider), vytvořte skupinu prostředků pomocí [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Následující příklad vytvoří název skupiny prostředků *myResourceGroup* v *USA – východ* umístění:

```powershell
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

Azure Key Vault obsahující kryptografické klíče a přidružených výpočetních prostředků, jako jsou úložiště a virtuální počítač se musí nacházet ve stejné oblasti. Vytvoření služby Azure Key Vault s [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) a povolte službě Key Vault pro použití s šifrování disku. Zadejte jedinečný název služby Key Vault pro *keyVaultName* následujícím způsobem:

```powershell
$keyVaultName = "myUniqueKeyVaultName"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Můžete uložit šifrovací klíče pomocí softwaru nebo ochrana modelů hardwarového zabezpečení (HSM). Použití modulu hardwarového zabezpečení vyžaduje na úrovni premium služby Key Vault. Není jakýchkoli dalších nákladů vytváří na úrovni premium služby Key Vault, nikoli standardní služby Key Vault, který ukládá klíče chráněného softwarem. K vytvoření služby Key Vault na úrovni premium, v předchozím kroku přidejte *- Sku "Premium"* parametry. Následující příklad používá softwarově chráněné klíče, protože jsme vytvořili standardní služby Key Vault. 

Pro oba modely ochrany platformy Azure musí mít udělen přístup k vyžádání kryptografických klíčů při spuštění virtuálního počítače k dešifrování virtuálních disků. Vytvoření kryptografické klíče v Key Vault s [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey). Následující příklad vytvoří klíč s názvem *myKey*:

```powershell
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```


## <a name="create-the-azure-active-directory-service-principal"></a>Vytvoření instančního objektu služby Azure Active Directory
Když virtuální disky jsou šifrované nebo dešifrovat, zadejte účet, který chcete zpracovávat ověřování a výměny klíče ze služby Key Vault. Tento účet instančního objektu služby Azure Active Directory umožňuje platformě Azure k vyžádání příslušná kryptografických klíčů jménem virtuálního počítače. Výchozí instance služby Azure Active Directory je dostupná ve vašem předplatném, ale mnoho organizací mít vyhrazené adresářů Azure Active Directory.

Vytvoření instančního objektu v Azure Active Directory s [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal). K zadání hesla, postupujte [zásady hesel a omezení v Azure Active Directory](../../active-directory/authentication/concept-sspr-policy.md):

```powershell
$appName = "My App"
$securePassword = ConvertTo-SecureString -String "P@ssw0rd!" -AsPlainText -Force
$app = New-AzureRmADApplication -DisplayName $appName `
    -HomePage "https://myapp.contoso.com" `
    -IdentifierUris "https://contoso.com/myapp" `
    -Password $securePassword
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
```

Chcete-li úspěšně šifrování nebo dešifrování virtuálních disků, na kryptografický klíč uložený ve službě Key Vault musí být nastaveno tak, aby povolovala objektu služby Azure Active Directory ke čtení klíče. Nastavit oprávnění pro Key Vault s [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy):

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyvaultName `
    -ServicePrincipalName $app.ApplicationId `
    -PermissionsToKeys "WrapKey" `
    -PermissionsToSecrets "Set"
```


## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače
Pokud chcete otestovat proces šifrování, vytvořte virtuální počítač s [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm). Následující příklad vytvoří virtuální počítač s názvem *myVM* pomocí *systému Windows Server 2016 Datacenter* bitové kopie. Po zobrazení výzvy k zadání pověření zadejte uživatelské jméno a heslo, které se použije pro váš virtuální počítač:

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
K šifrování virtuálních disků, které pohromadě všechny předchozí komponenty:

1. Zadejte instanční objekt Azure Active Directory a heslo.
2. Zadejte služby Key Vault k uložení metadat pro šifrované disky.
3. Zadejte kryptografické klíče pro skutečné šifrování a dešifrování.
4. Určete, jestli chcete šifrovat disk s operačním systémem, datové disky nebo všechny.

Šifrování virtuálního počítače s [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) pomocí klíče Azure Key Vault a přihlašovacích údajů instančního objektu služby Azure Active Directory. Následující příklad načte všechny informace o klíči pak zašifruje virtuální počítač s názvem *myVM*:

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

Přijetí výzvy a pokračujte šifrování virtuálního počítače. Během procesu restartování virtuálního počítače. Jakmile se dokončí proces šifrování a virtuální počítač byl restartován, zkontrolujte stav šifrování s [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus):

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
* Další informace o správě služby Azure Key Vault najdete v tématu [nastavení služby Key Vault pro virtuální počítače](key-vault-setup.md).
* Další informace o šifrování disku, například příprava šifrované vlastního virtuálního počítače k nahrání do Azure, najdete v části [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
