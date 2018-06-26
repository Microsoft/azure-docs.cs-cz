---
title: Šifrování disky na virtuální počítač s Linuxem v Azure | Microsoft Docs
description: Postup zašifrování virtuální disky na virtuální počítač s Linuxem pro zvýšení zabezpečení pomocí Azure CLI 2.0
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/31/2018
ms.author: iainfou
ms.openlocfilehash: 343408366c2970d10a952634ac671721caed74d4
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936866"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Postup zašifrování virtuální počítač s Linuxem v Azure
Pro lepší virtuální počítač (VM) zabezpečení a dodržování předpisů se můžou šifrovat virtuální disky a virtuální počítač. Virtuální počítače jsou šifrované pomocí kryptografických klíčů, které jsou zabezpečené v Azure Key Vault. Řízení těchto kryptografické klíče a můžete auditovat jejich použití. Tento článek podrobně popisují zašifrovat virtuální disky na virtuální počítač s Linuxem pomocí Azure CLI 2.0. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud si zvolíte instalaci a použití rozhraní příkazového řádku místně, v tomto článku vyžaduje, že používáte Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="overview-of-disk-encryption"></a>Přehled šifrování disku
Virtuální disky na virtuální počítače s Linuxem se šifrují pomocí rest [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Není nijak zpoplatněn pro šifrování virtuálních disků v Azure. Kryptografické klíče ukládají v Azure Key Vault pomocí ochrany proti softwaru, nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovány pro FIPS 140-2 úroveň 2 standardů. Uchování kontroly nad těchto kryptografické klíče a můžete auditovat jejich použití. Tyto klíče se používají k šifrování a dešifrování virtuálních disků připojených k virtuálnímu počítači. Hlavní služby Azure Active Directory poskytuje zabezpečené mechanismus pro vydávání tyto kryptografické klíče jako virtuální počítače jsou zapnuté zapnout a vypnout.

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

* Následující server Linux SKU - Ubuntu, CentOS, SUSE a SUSE Linux Enterprise Server (SLES) a Red Hat Enterprise Linux.
* Všechny prostředky (například Key Vault, účet úložiště a virtuálních počítačů) musí být ve stejné oblasti Azure a předplatné.
* Standardní A, D, DS, G, GS, atd, virtuální počítače řady.
* Aktualizuje se kryptografické klíče na virtuální počítač již šifrované Linux.

Šifrování disku není aktuálně podporováno v následujících scénářích:

* Úroveň Basic virtuálních počítačů.
* Virtuální počítače vytvořené pomocí modelu nasazení Classic.
* Zakázáním šifrování disku operačního systému na virtuální počítače s Linuxem.
* Použití vlastních bitových kopií systému Linux.

Další informace o podporovaných scénářích a omezeních najdete v tématu [Azure Disk Encryption pro virtuální počítače IaaS](../../security/azure-security-disk-encryption.md)


## <a name="create-an-azure-key-vault-and-keys"></a>Vytvoření Azure Key Vault a klíče
V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Zahrnout názvy parametrů příklad *myResourceGroup*, *myKey*, a *Můjvp*.

Prvním krokem je vytvoření Azure Key Vault pro ukládání kryptografických klíčů. Azure Key Vault můžete uložit klíčů, tajné klíče nebo hesla, které vám umožní bezpečně je implementovat v vašim aplikacím a službám. Šifrování virtuálního disku použijete k uložení kryptografického klíče, který se používá k šifrování a dešifrování virtuální disky Key Vault.

Povolit Azure Key Vault zprostředkovatele v rámci vašeho předplatného Azure s [registrace zprostředkovatele az](/cli/azure/provider#az-provider-register) a vytvořte skupinu prostředků s [vytvořit skupinu az](/cli/azure/group#az-group-create). Následující příklad vytvoří název skupiny prostředků *myResourceGroup* v `eastus` umístění:

```azurecli-interactive
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location eastus
```

Azure Key Vault, který obsahuje kryptografické klíče a přidružené výpočetní prostředky, jako je například úložiště a virtuální počítač se musí nacházet ve stejné oblasti. Vytvoření Azure Key Vault s [vytvořit az keyvault](/cli/azure/keyvault#az-keyvault-create) a povolte Key Vault pro použití s šifrování disku. Zadejte jedinečný název pro Key Vault *keyvault_name* následujícím způsobem:

```azurecli-interactive
keyvault_name=myuniquekeyvaultname
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Můžete uložit kryptografické klíče pomocí softwaru nebo ochrany modelu hardwarového zabezpečení (HSM). Použití modulu hardwarového zabezpečení vyžaduje premium Key Vault. Není dalších nákladů na vytváření premium Key Vault, nikoli standardní Key Vault, který ukládá klíče chráněné softwarem. Chcete-li vytvořit premium Key Vault, v předchozím kroku přidejte `--sku Premium` k příkazu. Následující příklad používá klíče chráněné softwarem vzhledem k tomu, že jste vytvořili standardní Key Vault.

Pro oba modely ochrany musí mít udělen přístup k žádosti o kryptografických klíčů, když se virtuální počítač spustí do dešifrovat virtuální disky platformy Azure. Vytvoření kryptografické klíče v Key Vault s [vytvořit klíč keyvault az](/cli/azure/keyvault/key#az-keyvault-key-create). Následující příklad vytvoří klíč s názvem *myKey*:

```azurecli-interactive
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```


## <a name="create-an-azure-active-directory-service-principal"></a>Vytvoření objektu služby Azure Active Directory
Když virtuální disky jsou zašifrovaná nebo dešifrovat, je třeba zadat účet, který chcete zpracovávat ověřování a výměna kryptografických klíčů z Key Vault. Tento účet objektu zabezpečení služby Azure Active Directory umožňuje platformy Azure k vyžádání příslušné kryptografické klíče jménem virtuálního počítače. Výchozí instance služby Azure Active Directory je ve vašem předplatném dostupná, když máte mnoho organizací vyhrazené adresáře služby Azure Active Directory.

Vytvořit objekt služby pomocí služby Azure Active Directory s [az ad sp vytvořit pro rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac). Následující příklad načte v hodnoty pro objekt služby a heslo pro použití v novější příkazy:

```azurecli-interactive
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

Heslo se zobrazí jenom při vytvoření objektu služby. V případě potřeby, zobrazení a zaznamenejte heslo (`echo $sp_password`). Můžete vytvořit seznam hlavních objektů s několika vaší služby [az ad sp seznamu](/cli/azure/ad/sp#az-ad-sp-list) a zobrazit další informace o konkrétní objekt s služby [az ad sp zobrazit](/cli/azure/ad/sp#az-ad-sp-show).

Pro úspěšně šifrování nebo dešifrování virtuálních disků, musí se nastavit oprávnění na kryptografický klíč uložený v Key Vault tak, aby povolovala objektu služby Azure Active Directory ke čtení klíče. Nastavení oprávnění pro Key Vault s [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). V následujícím příkladu je zadané ID objektu zabezpečení služby z předchozí příkaz:

```azurecli-interactive
az keyvault set-policy --name $keyvault_name --spn $sp_id \
  --key-permissions wrapKey \
  --secret-permissions set
```


## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
Vytvoření virtuálního počítače s [vytvořit virtuální počítač az](/cli/azure/vm#az-vm-create) a připojit datový disk 5 Gb. Pouze některé bitové kopie marketplace podporují šifrování disku. Následující příklad vytvoří virtuální počítač s názvem *Můjvp* pomocí *Ubuntu 16.04 LTS* bitové kopie:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

SSH pro virtuální počítač pomocí *publicIpAddress* zobrazené ve výstupu předchozí příkaz. Vytvořit oddíl a systému souborů a pak připojit datový disk. Další informace najdete v tématu [připojit k virtuální počítač s Linuxem připojit nový disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Ukončení relace SSH.


## <a name="encrypt-the-virtual-machine"></a>Šifrování virtuálního počítače
Pokud chcete zašifrovat virtuální disky, přepnutím společně předchozí komponenty:

1. Zadejte objektu služby Azure Active Directory a heslo.
2. Zadejte klíč trezoru k ukládání metadat pro šifrované disky.
3. Zadejte kryptografické klíče k použití pro skutečné šifrování a dešifrování.
4. Zadejte, jestli chcete šifrovat disk operačního systému, datových disků nebo všechny.

Šifrování virtuálního počítače s [povolit šifrování virtuálních počítačů az](/cli/azure/vm/encryption#az-vm-encryption-enable). Následující příklad používá *$sp_id* a *$sp_password* proměnné z předchozí [az ad sp vytvořit pro rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) příkaz:

```azurecli-interactive
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Pro proces šifrování disku pro dokončení chvíli trvat. Monitorování stavu procesu s [zobrazit šifrování virtuálních počítačů az](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group myResourceGroup --name myVM
```

Výstup bude vypadat v následujícím příkladu zkrácený:

```json
[
  "dataDisk": "EncryptionInProgress",
  "osDisk": "EncryptionInProgress"
]
```

Počkejte, dokud stav operačního systému na disku sestavy **VMRestartPending**, potom restartujte virtuální počítač s [restartování virtuálního počítače az](/cli/azure/vm#az-vm-restart):

```azurecli-interactive
az vm restart --resource-group myResourceGroup --name myVM
```

Proces šifrování disku je dokončené během spouštění, proto Počkejte několik minut před zaškrtnutím stav šifrování znovu s [zobrazit šifrování virtuálních počítačů az](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group myResourceGroup --name myVM
```

Stav má teď disk operačního systému i datový disk jako **šifrovaný**.


## <a name="add-additional-data-disks"></a>Přidání dalších datových disků
Jakmile jste zašifrovali datových disků, můžete později přidat další virtuální disky pro virtuální počítač a také zašifrovat. Umožňuje například přidat druhý virtuální disk k virtuálnímu počítači následujícím způsobem:

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --disk myDataDisk \
    --new \
    --size-gb 5
```

Spusťte znovu příkaz pro zašifrování virtuální disky následujícím způsobem:

```azurecli-interactive
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```


## <a name="next-steps"></a>Další postup
* Další informace o správě Azure Key Vault, včetně odstraňování kryptografické klíče a trezory, najdete v části [Key Vault spravovat pomocí rozhraní příkazového řádku](../../key-vault/key-vault-manage-with-cli2.md).
* Další informace o šifrování disku, jako je například příprava šifrované vlastních virtuálních počítačů se nahrát do Azure, najdete v části [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
