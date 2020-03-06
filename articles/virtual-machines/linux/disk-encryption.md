---
title: Šifrování na straně serveru Azure Managed Disks – Azure CLI
description: Azure Storage chrání vaše data tím, že je před tím, než je zachová v clusterech úložiště, v klidovém prostředí. Pro šifrování svých spravovaných disků můžete spoléhat na klíče spravované Microsoftem, případně můžete pomocí klíčů spravovaných zákazníkem spravovat šifrování pomocí vlastních klíčů.
author: roygara
ms.date: 01/13/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 48935dc74d6d22bcafa31afebbdfcb829e122815
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399241"
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

Můžete zvolit správu šifrování na úrovni každého spravovaného disku s vlastními klíči. Šifrování na straně serveru pro spravované disky pomocí klíčů spravovaných zákazníkem nabízí integrované prostředí s Azure Key Vault. Můžete buď importovat [klíče RSA](../../key-vault/key-vault-hsm-protected-keys.md) do svého Key Vault, nebo vygenerovat nové klíče rsa v Azure Key Vault. Azure Managed disks zpracovává šifrování a dešifrování plně transparentním způsobem pomocí [šifrování obálek](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Šifruje data pomocí šifrovacího klíče založeného na [standardu AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 (klíč DEK), který je zase chráněn pomocí vašich klíčů. Abyste mohli používat klíče pro šifrování a dešifrování klíč DEK, musíte udělit přístup ke spravovaným diskům ve vašem Key Vault. To vám umožní plnou kontrolu nad daty a klíči. Můžete kdykoli zakázat vaše klíče nebo odvolat přístup ke spravovaným diskům. Pomocí monitorování Azure Key Vault taky můžete auditovat použití šifrovacího klíče a zajistit, aby se ke klíčům přistupovaly jenom spravované disky nebo jiné důvěryhodné služby Azure.

Následující diagram ukazuje, jak spravované disky používají Azure Active Directory a Azure Key Vault k vytváření požadavků pomocí klíče spravovaného zákazníkem:

![Pracovní postup spravovaného disku a klíčů spravovaných zákazníkem. Správce vytvoří Azure Key Vault a pak vytvoří sadu šifrování disku a nastaví sadu šifrování disku. Sada je přidružená k virtuálnímu počítači, který umožňuje, aby se disk používal k ověřování Azure AD.](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


Následující seznam vysvětluje diagram ještě více podrobností:

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

V současné době jsou podporovány pouze následující oblasti:

- K dispozici jako nabídka GA v oblastech Východní USA, Západní USA 2 a Střed USA – jih.
- Je dostupná jako veřejná verze Preview v oblastech Středozápadní USA, Východní USA 2, Kanada – střed a Severní Evropa.

### <a name="restrictions"></a>Omezení

Klíče spravované zákazníkem teď mají následující omezení:

- Podporovány jsou pouze ["měkké" a "pevné" klíče RSA](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) o velikosti 2080, žádné jiné klíče ani velikosti.
- Disky vytvořené z vlastních imagí šifrovaných pomocí šifrování na straně serveru a klíčů spravovaných zákazníkem musí být šifrované pomocí stejných klíčů spravovaných zákazníkem a musí být ve stejném předplatném.
- Snímky vytvořené z disků šifrovaných pomocí šifrování na straně serveru a klíčů spravovaných zákazníkem musí být šifrované pomocí stejných klíčů spravovaných zákazníkem.
- Vlastní image šifrované pomocí šifrování na straně serveru a klíčů spravovaných zákazníkem se nedají použít v galerii sdílených imagí.
- Všechny prostředky, které souvisejí s vašimi klíči spravovanými zákazníky (trezory klíčů Azure, sady šifrování disků, virtuální počítače, disky a snímky), musí být ve stejném předplatném a oblasti.
- Disky, snímky a image šifrované pomocí klíčů spravovaných zákazníkem se nedají přesunout do jiného předplatného.
- Pokud k vytvoření sady pro šifrování disků použijete Azure Portal, nemůžete teď snímky použít.
- Spravované disky šifrované pomocí klíčů spravovaných zákazníkem se nedají taky šifrovat pomocí Azure Disk Encryption.

### <a name="cli"></a>Rozhraní příkazového řádku
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Nastavení Azure Key Vault a DiskEncryptionSet

1. Ujistěte se, že máte nainstalované nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a protokolujte ho do účtu Azure v rámci pomocí [AZ Login](/cli/azure/reference-index).

1. Vytvořte instanci Azure Key Vault a šifrovací klíč.

    Při vytváření instance Key Vault musíte povolit ochranu po tichém odstranění a vyprázdnění. Obnovitelné odstranění zajistí, že Key Vault obsahuje odstraněný klíč pro dané období uchování (výchozí hodnota je 90 den). Vyčištění ochrany zajišťuje, že odstraněný klíč nebude možné trvale odstranit, dokud doba uchování neuplyne. Tato nastavení chrání před ztrátou dat kvůli náhodnému odstranění. Tato nastavení jsou povinná při použití Key Vault k šifrování spravovaných disků.

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=WestCentralUS
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.  Vytvoří instanci třídy DiskEncryptionSet. 
    
    ```azurecli
    keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)

    keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

    az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
    ```

1.  Udělte DiskEncryptionSet prostředku přístup k trezoru klíčů. 

    > [!NOTE]
    > V případě, že Azure může v Azure Active Directory vytvořit identitu vašeho DiskEncryptionSetu, může to několik minut trvat. Pokud při spuštění následujícího příkazu dojde k chybě, například "Nejde najít objekt služby Active Directory", počkejte pár minut a zkuste to znovu.

    ```azurecli
    desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)

    az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get

    az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
    ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Vytvoření virtuálního počítače pomocí Image Marketplace, šifrování OS a datových disků pomocí klíčů spravovaných zákazníkem

```azurecli
rgName=yourResourceGroupName
vmName=yourVMName
location=WestCentralUS
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName -n $vmName -l $location --image $image --size $vmSize --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 128 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```


#### <a name="encrypt-existing-unattached-managed-disks"></a>Zašifrovat existující nepřipojené spravované disky 

Vaše existující disky se nesmí připojit ke spuštěnému virtuálnímu počítači, aby je bylo možné zašifrovat pomocí následujícího skriptu:

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Vytvoření sady škálování virtuálních počítačů pomocí Image Marketplace, šifrování operačních systémů a datových disků pomocí klíčů spravovaných zákazníkem

```azurecli
rgName=yourResourceGroupName
vmssName=yourVMSSName
location=WestCentralUS
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)
az vmss create -g $rgName -n $vmssName --image UbuntuLTS --upgrade-policy automatic --admin-username azureuser --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 64 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Vytvořte prázdný disk zašifrovaný pomocí šifrování na straně serveru s použitím klíčů spravovaných zákazníkem a připojte ho k virtuálnímu počítači.

```azurecli
vmName=yourVMName
rgName=yourResourceGroupName
diskName=yourDiskName
diskSkuName=Premium_LRS
diskSizeinGiB=30
location=WestCentralUS
diskLUN=2
diskEncryptionSetName=yourDiskEncryptionSetName


diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az disk create -n $diskName -g $rgName -l $location --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId --size-gb $diskSizeinGiB --sku $diskSkuName

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId 

```

> [!IMPORTANT]
> Klíče spravované zákazníkem spoléhají na spravované identity prostředků Azure, což je funkce Azure Active Directory (Azure AD). Při konfiguraci klíčů spravovaných zákazníkem se spravovaná identita automaticky přiřadí k vašim prostředkům v rámci pokrývání. Pokud později přesunete předplatné, skupinu prostředků nebo spravovaný disk z jednoho adresáře služby Azure AD do jiného, nepřesune se do nového tenanta spravovaná identita přidružená ke spravovaným diskům, takže klíče spravované zákazníkem už možná nebudou fungovat. Další informace najdete v tématu [přenos předplatného mezi adresáři služby Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem spoléhají na spravované identity prostředků Azure, což je funkce Azure Active Directory (Azure AD). Při konfiguraci klíčů spravovaných zákazníkem se spravovaná identita automaticky přiřadí k vašim prostředkům v rámci pokrývání. Pokud později přesunete předplatné, skupinu prostředků nebo spravovaný disk z jednoho adresáře služby Azure AD do jiného, nepřesune se do nového tenanta spravovaná identita přidružená ke spravovaným diskům, takže klíče spravované zákazníkem už možná nebudou fungovat. Další informace najdete v tématu [přenos předplatného mezi adresáři služby Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Šifrování na straně serveru oproti službě Azure Disk Encryption

[Azure Disk Encryption pro virtuální počítače a službu Virtual Machine Scale Sets](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) využívá funkci [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) systému Windows a funkci [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) systému Linux k šifrování spravovaných disků pomocí klíčů spravovaných zákazníkem v rámci virtuálního počítače hosta.  Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem se zlepšuje v ADE tím, že vám umožní používat pro vaše virtuální počítače jakékoli typy operačních systémů a image šifrováním dat ve službě úložiště.

## <a name="next-steps"></a>Další kroky

- [Prozkoumejte šablony Azure Resource Manager pro vytváření šifrovaných disků pomocí klíčů spravovaných zákazníkem](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Co je Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Replikace počítačů s povolenými disky spravovanými zákazníky](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Nastavení zotavení po havárii virtuálních počítačů VMware do Azure pomocí PowerShellu](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Nastavení zotavení po havárii do Azure pro virtuální počítače Hyper-V pomocí PowerShellu a Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
