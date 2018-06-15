---
title: Šifrování disky na virtuální počítač s Linuxem pomocí Azure CLI 1.0 | Microsoft Docs
description: Postup zašifrování disky na virtuální počítač s Linuxem pomocí Azure CLI 1.0 a modelu nasazení Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2017
ms.author: iainfou
ms.openlocfilehash: d1704ba37b1d2acc595cb0c354b22bfcf1c57036
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30911675"
---
# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli-10"></a>Šifrování disky na virtuální počítač s Linuxem pomocí Azure CLI 1.0
Pro lepší virtuální počítač (VM) zabezpečení a dodržování předpisů je možné zašifrovat virtuální disky v Azure v klidovém stavu. Disky jsou šifrované pomocí kryptografických klíčů, které jsou zabezpečené v Azure Key Vault. Řízení těchto kryptografické klíče a můžete auditovat jejich použití. Tento článek podrobně popisují zašifrovat virtuální disky na virtuální počítač s Linuxem pomocí Azure CLI 1.0 a modelu nasazení Resource Manager.

## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

- [Azure CLI 1.0](#quick-commands) – naše rozhraní příkazového řádku pro classic a resource správu modelech nasazení (v tomto článku)
- [Azure CLI 2.0](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – naše rozhraní příkazového řádku nové generace pro model nasazení správy prostředků

## <a name="quick-commands"></a>Rychlé příkazy
Pokud potřebujete rychle provedení úlohy, následující část podrobně popisuje základní příkazy pro zašifrování virtuální disky na virtuální počítač. Podrobnější informace a kontext pro každý krok naleznete zbývající části dokumentu, [od zde](#overview-of-disk-encryption).

Je nutné [nejnovější Azure CLI 1.0](../../xplat-cli-install.md) nainstalován a přihlášení pomocí režimu Resource Manager takto:

```azurecli
azure config mode arm
```

V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Zahrnout názvy parametrů příklad `myResourceGroup`, `myKeyVault`, a `myVM`.

Nejprve povolte Azure Key Vault zprostředkovatele v rámci vašeho předplatného Azure a vytvořte skupinu prostředků. Následující příklad vytvoří název skupiny prostředků `myResourceGroup` v `WestUS` umístění:

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Vytvoření Azure Key Vault. Následující příklad vytvoří Key Vault s názvem `myKeyVault`:

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Vytvoření kryptografické klíče v Key Vault a povolit šifrování disku. Následující příklad vytvoří klíč s názvem `myKey`:

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

Vytvořte koncový bod pomocí Azure Active Directory pro zpracování ověření a výměna kryptografických klíčů z Key Vault. `--home-page` a `--identifier-uris` nemusí být skutečné směrovatelné adresa. Pro nejvyšší úroveň zabezpečení je třeba použít klienta tajné klíče místo hesla. Rozhraní příkazového řádku Azure nelze vygenerovat aktuálně tajné klíče klienta. Tajné klíče klienta lze vygenerovat pouze na portálu Azure. Následující příklad vytvoří koncový bod služby Azure Active Directory s názvem `myAADApp` a používá na heslo `myPassword`. Zadejte své heslo následujícím způsobem:

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Poznámka: `applicationId` zobrazené ve výstupu z předchozí příkaz. Toto ID aplikace se používá v následujících krocích:

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

Přidáte datový disk do stávajícího virtuálního počítače. Následující příklad přidá datový disk k virtuálnímu počítači s názvem `myVM`:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Podrobné informace pro Key Vault a klíče, který jste vytvořili. Je třeba ID klíč trezoru, identifikátor URI a klíč adresy URL v posledním kroku. Následující příklad zkontroluje podrobnosti pro Key Vault s názvem `myKeyVault` a klíč s názvem `myKey`:

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Šifrování vaše disky následujícím způsobem zadávání vlastní názvy parametrů v průběhu:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Rozhraní příkazového řádku Azure neposkytuje podrobné chyby během procesu šifrování. Další informace o řešení potíží, `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`. Předchozí příkaz má mnoho proměnné a nelze získat množství informací, proč proces selže, například dokončení příkazu vypadat takto:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Nakonec zkontrolujte stav šifrování znovu pro potvrzení, že virtuální disky mají nyní šifrována. Následující příklad ověří stav virtuálního počítače s názvem `myVM` v `myResourceGroup` skupiny prostředků:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>Přehled šifrování disku
Virtuální disky na virtuální počítače s Linuxem se šifrují pomocí rest [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Není nijak zpoplatněn pro šifrování virtuálních disků v Azure. Kryptografické klíče ukládají v Azure Key Vault pomocí ochrany proti softwaru, nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovány pro FIPS 140-2 úroveň 2 standardů. Uchování kontroly nad těchto kryptografické klíče a můžete auditovat jejich použití. Tyto klíče se používají k šifrování a dešifrování virtuálních disků připojených k virtuálnímu počítači. Koncový bod služby Azure Active Directory poskytuje zabezpečené mechanismus pro vydávání tyto kryptografické klíče jako virtuální počítače jsou zapnuté zapnout a vypnout.

Proces šifrování virtuálního počítače je následující:

1. Vytvoření kryptografické klíče v Azure Key Vault.
2. Nakonfigurujte kryptografický klíč možné používat pro šifrování disků.
3. Čtení kryptografického klíče z Azure Key Vault, vytvořte koncový bod pomocí služby Azure Active Directory s příslušnými oprávněními.
4. Příkaz pro zašifrování virtuální disky, koncový bod služby Azure Active Directory a příslušné kryptografický klíč, který se má použít.
5. Koncový bod služby Azure Active Directory vyžaduje požadované kryptografický klíč z Azure Key Vault.
6. Virtuální disky jsou šifrované pomocí zadaný kryptografický klíč.

## <a name="supporting-services-and-encryption-process"></a>Podpora služby a proces šifrování
Šifrování disku spoléhá na následující součásti:

* **Azure Key Vault** – používané k ochraně kryptografické klíče a tajné klíče pro proces šifrování a dešifrování disku.
  * Pokud ano, můžete použít existující Azure Key Vault. Není nutné vyhradit Key Vault pro šifrování disků.
  * K oddělení hranicemi správy a klíče viditelnost, můžete vytvořit vyhrazený Key Vault.
* **Azure Active Directory** -zpracovává zabezpečené výměna požadované kryptografické klíče a ověřování pro požadované akce.
  * Pro uložení aplikace můžete obvykle použít existující instanci služby Azure Active Directory.
  * Aplikace je více koncový bod pro služby Key Vault a virtuální počítač k vyžádání a získání vydané příslušnou kryptografické klíče. Nevyvíjíte skutečné aplikace, která se integruje se službou Azure Active Directory.

## <a name="requirements-and-limitations"></a>Požadavky a omezení
Podporované scénáře a požadavky na šifrování disku:

* Následující server Linux SKU - Ubuntu, CentOS, SUSE a SUSE Linux Enterprise Server (SLES) a Red Hat Enterprise Linux.
* Všechny prostředky (například Key Vault, účet úložiště a virtuálních počítačů) musí být ve stejné oblasti Azure a předplatné.
* Standardní A, D, DS, G a GS řada virtuálních počítačů.

Šifrování disku není aktuálně podporováno v následujících scénářích:

* Úroveň Basic virtuálních počítačů.
* Virtuální počítače vytvořené pomocí modelu nasazení Classic.
* Zakázáním šifrování disku operačního systému na virtuální počítače s Linuxem.
* Aktualizuje se kryptografické klíče na virtuální počítač již šifrované Linux.

## <a name="create-the-azure-key-vault-and-keys"></a>Vytvoření Azure Key Vault a klíče
K dokončení zbytek tohoto průvodce, musíte [nejnovější Azure CLI 1.0](../../xplat-cli-install.md) nainstalován a přihlášení pomocí režimu Resource Manager takto:

```azurecli
azure config mode arm
```

V příkladech nahraďte všechny parametry příklad vlastní názvy, umístění a hodnoty klíče. Následující příklady použití konvence `myResourceGroup`, `myKeyVault`, `myAADApp`atd.

Prvním krokem je vytvoření Azure Key Vault pro ukládání kryptografických klíčů. Azure Key Vault můžete uložit klíčů, tajné klíče nebo hesla, které vám umožní bezpečně je implementovat v vašim aplikacím a službám. Šifrování virtuálního disku použijete k uložení kryptografického klíče, který se používá k šifrování a dešifrování virtuální disky Key Vault.

Povolit zprostředkovatele Azure Key Vault ve vašem předplatném Azure a pak vytvořte skupinu prostředků. Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v `WestUS` umístění:

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Azure Key Vault, který obsahuje kryptografické klíče a přidružené výpočetní prostředky, jako je například úložiště a virtuální počítač se musí nacházet ve stejné oblasti. Následující příklad vytvoří Azure Key Vault s názvem `myKeyVault`:

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Můžete uložit kryptografické klíče pomocí softwaru nebo ochrany modelu hardwarového zabezpečení (HSM). Použití modulu hardwarového zabezpečení vyžaduje premium Key Vault. Není dalších nákladů na vytváření premium Key Vault, nikoli standardní Key Vault, který ukládá klíče chráněné softwarem. Chcete-li vytvořit premium Key Vault, v předchozím kroku přidejte `--sku Premium` k příkazu. Následující příklad používá klíče chráněné softwarem, protože jsme vytvořili standardní Key Vault.

Pro oba modely ochrany musí mít udělen přístup k žádosti o kryptografických klíčů, když se virtuální počítač spustí do dešifrovat virtuální disky platformy Azure. Vytvořit šifrovací klíč v Key Vault a pak ji povolit pro použití s šifrováním virtuálního disku. Následující příklad vytvoří klíč s názvem `myKey` a povolí ho šifrování disku:

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory
Když virtuální disky jsou zašifrovaná nebo dešifrovat, použijete koncový bod pro zpracování ověření a výměna kryptografických klíčů z Key Vault. Tento koncový bod, aplikaci Azure Active Directory umožňuje platformy Azure k vyžádání příslušné kryptografické klíče jménem virtuálního počítače. Výchozí instance služby Azure Active Directory je ve vašem předplatném dostupná, když máte mnoho organizací vyhrazené adresáře služby Azure Active Directory.

Jako nejsou vytvoření úplné aplikace Azure Active Directory, `--home-page` a `--identifier-uris` parametry v následujícím příkladu nemusíte být skutečné směrovatelné adresa. Následující příklad určuje také tajného klíče založené na heslech, nikoli generování klíčů z v portálu Azure. Jako tento čas generování klíčů nelze provést z příkazového řádku Azure.

Vytvoření aplikace Azure Active Directory. Následující příklad vytvoří aplikaci s názvem `myAADApp` a používá na heslo `myPassword`. Zadejte své heslo následujícím způsobem:

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Poznamenejte si `applicationId` , je vrácen ve výstupu z předchozí příkaz. Toto ID aplikace se používá v některé z zbývající kroky. Dále vytvořte hlavní název služby (SPN) tak, aby aplikace je přístupná ve vašem prostředí. Pro úspěšně šifrování nebo dešifrování virtuálních disků, musí se nastavit oprávnění na kryptografický klíč uložený v Key Vault tak, aby povolovala aplikaci Azure Active Directory číst klíče.

Vytvořit název SPN a nastavte příslušná oprávnění následujícím způsobem:

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>Přidat virtuální disk a zkontrolovat stav šifrování
Ve skutečnosti šifrování některé virtuální disky, umožňuje přidat disk do stávajícího virtuálního počítače. Přidáte datový disk 5Gb na existující virtuální počítač následujícím způsobem:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Aktuálně nejsou šifrovány virtuální disky. Zkontrolujte aktuální stav šifrování virtuální počítač následujícím způsobem:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>Šifrování virtuálních disků
Pokud chcete nyní zašifrovat virtuální disky, přepnutím společně předchozí komponenty:

1. Zadejte aplikaci Azure Active Directory a heslo.
2. Zadejte klíč trezoru k ukládání metadat pro šifrované disky.
3. Zadejte kryptografické klíče k použití pro skutečné šifrování a dešifrování.
4. Zadejte, jestli chcete šifrovat disk operačního systému, datových disků nebo všechny.

Umožňuje, zkontrolujte podrobnosti pro Azure Key Vault a klíče, který jste vytvořili, potřebujete ID klíč trezoru, identifikátor URI, a potom klíče adresy URL v posledním kroku:

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Zašifrovat virtuální disky pomocí výstup `azure keyvault show` a `azure keyvault key show` příkazy následujícím způsobem:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Předchozí příkaz má mnoho proměnné, je v následujícím příkladu příkaz dokončení pro referenci:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Rozhraní příkazového řádku Azure neposkytuje podrobné chyby během procesu šifrování. Další informace o řešení potíží, `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` ve virtuálním počítači se šifrují.

Nakonec umožňuje zkontrolovat stav šifrování znovu pro potvrzení, že mít teď šifrované virtuální disky:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>Přidání dalších datových disků
Jakmile jste zašifrovali datových disků, můžete později přidat další virtuální disky pro virtuální počítač a také zašifrovat. Při spuštění `azure vm enable-disk-encryption` příkaz, zvýšit pořadí verzi pomocí `--sequence-version` parametr. Tento parametr pořadí verzi můžete k provedení operací s stejného virtuálního počítače.

Umožňuje například přidat druhý virtuální disk k virtuálnímu počítači následujícím způsobem:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Spusťte znovu příkaz pro zašifrování virtuální disky, tento čas přidání `--sequence-version` parametr a přírůstkovou hodnotou z našich první spustit takto:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>Další postup
* Další informace o správě Azure Key Vault, včetně odstraňování kryptografické klíče a trezory, najdete v části [Key Vault spravovat pomocí rozhraní příkazového řádku](../../key-vault/key-vault-manage-with-cli2.md).
* Další informace o šifrování disku, jako je například příprava šifrované vlastních virtuálních počítačů se nahrát do Azure, najdete v části [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
