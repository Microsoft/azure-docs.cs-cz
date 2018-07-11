---
title: Šifrování disků na virtuální počítač s Linuxem v Azure | Dokumentace Microsoftu
description: Postup zašifrování virtuální disky na virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure CLI 2.0 nabízí vyšší zabezpečení
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 75ec087536d6f833a9a2106b1fdf4ed1fd73ef8e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932407"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Jak šifrování virtuálního počítače s Linuxem v Azure
Vylepšené virtuálních počítačů (VM) zabezpečení a dodržování předpisů můžete šifrovat virtuální disky a virtuální počítač. Virtuální počítače jsou šifrované pomocí kryptografických klíčů, které jsou zabezpečené v Azure Key Vault. Řízení těchto kryptografických klíčů a auditovat jejich použití. Tento článek podrobně popisuje, jak šifrování virtuálních disků na virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure CLI 2.0. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, tento článek vyžaduje použití Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="overview-of-disk-encryption"></a>Přehled šifrování disku
Virtuální disky na virtuální počítače s Linuxem jsou zašifrovaná pomocí rest [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Neplatí žádné poplatky pro šifrování virtuálních disků v Azure. Kryptografické klíče jsou uložené ve službě Azure Key Vault software ochrany, nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovaných podle standardů FIPS 140-2 úrovně 2 standardů. Uchování kontroly nad těmito kryptografické klíče a můžete kontrolovat jejich použití. Tyto klíče se používají k šifrování a dešifrování virtuální disky připojené k virtuálnímu počítači. Instančního objektu služby Azure Active Directory poskytuje zabezpečené mechanismus pro vydávání těchto kryptografických klíčů, jako jsou virtuální počítače využívající zapnout a vypnout.

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

* Následující server Linux SKU – Ubuntu, CentOS, SUSE a operačním systémem SUSE Linux Enterprise Server (SLES) a Red Hat Enterprise Linux.
* Všechny prostředky (například služby Key Vault, účet úložiště a virtuálních počítačů) musí být ve stejné oblasti Azure a předplatné.
* Standardní A, D, DS, G, GS, atd, virtuální počítače řady.
* Aktualizuje se kryptografické klíče na virtuálním počítači s Linuxem již šifrované.

Šifrování disku není aktuálně podporována v následujících scénářích:

* Úroveň Basic virtuálních počítačů.
* Virtuální počítače vytvořené pomocí modelu nasazení Classic.
* Zakazuje šifrování disku operačního systému na virtuální počítače s Linuxem.
* Použití vlastních imagí Linuxu.

Další informace o podporovaných scénářích a omezeních najdete v tématu [Azure Disk Encryption pro virtuální počítače IaaS](../../security/azure-security-disk-encryption.md)


## <a name="create-an-azure-key-vault-and-keys"></a>Vytvoření služby Azure Key Vault a klíči
V následujících příkladech nahraďte ukázkové názvy parametrů s vlastními hodnotami. Zahrnout názvy parametrů příklad *myResourceGroup*, *myKey*, a *myVM*.

Prvním krokem je vytvoření služby Azure Key Vault pro ukládání kryptografických klíčů. Azure Key Vault umí ukládat klíče, tajné kódy nebo hesla, které umožňují bezpečný je implementovat ve svých aplikacích a službách. Šifrování virtuálních disků použijete k uložení kryptografického klíče, který slouží k šifrování nebo dešifrování virtuálních disků služby Key Vault.

Povolit zprostředkovatele služby Azure Key Vault v rámci vašeho předplatného Azure s [az provider register](/cli/azure/provider#az-provider-register) a vytvořte skupinu prostředků s [vytvořit skupiny az](/cli/azure/group#az-group-create). Následující příklad vytvoří název skupiny prostředků *myResourceGroup* v `eastus` umístění:

```azurecli-interactive
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location eastus
```

Azure Key Vault obsahující kryptografické klíče a přidružených výpočetních prostředků, jako jsou úložiště a virtuální počítač se musí nacházet ve stejné oblasti. Vytvoření služby Azure Key Vault s [az keyvault vytvořit](/cli/azure/keyvault#az-keyvault-create) a povolte službě Key Vault pro použití s šifrování disku. Zadejte jedinečný název služby Key Vault pro *keyvault_name* následujícím způsobem:

```azurecli-interactive
keyvault_name=myuniquekeyvaultname
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Můžete uložit šifrovací klíče pomocí softwaru nebo ochrana modelů hardwarového zabezpečení (HSM). Použití modulu hardwarového zabezpečení vyžaduje na úrovni premium služby Key Vault. Není jakýchkoli dalších nákladů vytváří na úrovni premium služby Key Vault, nikoli standardní služby Key Vault, který ukládá klíče chráněného softwarem. Vytvoření služby Key Vault na úrovni premium, v předchozím kroku přidat `--sku Premium` k příkazu. Následující příklad používá klíči chráněnými softwarem od vytvoření standardní služby Key Vault.

Pro oba modely ochrany platformy Azure musí mít udělen přístup k vyžádání kryptografických klíčů při spuštění virtuálního počítače k dešifrování virtuálních disků. Vytvoření kryptografické klíče v Key Vault s [az keyvault key vytvořit](/cli/azure/keyvault/key#az-keyvault-key-create). Následující příklad vytvoří klíč s názvem *myKey*:

```azurecli-interactive
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```


## <a name="create-an-azure-active-directory-service-principal"></a>Vytvoření objektu služby Azure Active Directory
Když virtuální disky jsou šifrované nebo dešifrovat, zadejte účet, který chcete zpracovávat ověřování a výměny klíče ze služby Key Vault. Tento účet instančního objektu služby Azure Active Directory umožňuje platformě Azure k vyžádání příslušná kryptografických klíčů jménem virtuálního počítače. Výchozí instance služby Azure Active Directory je dostupná ve vašem předplatném, ale mnoho organizací mít vyhrazené adresářů Azure Active Directory.

Vytvoření instančního objektu pomocí Azure Active Directory s [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac). Následující příklad načte hodnoty pro instanční objekt služby a heslo pro použití v pozdější příkazy:

```azurecli-interactive
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

Heslo se zobrazí jenom při vytváření instančního objektu služby. V případě potřeby, zobrazení a poznamenejte heslo (`echo $sp_password`). Můžete vytvořit seznam vašich instančních objektů s využitím [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) a zobrazit další informace o konkrétní instančního objektu s [az ad sp show](/cli/azure/ad/sp#az-ad-sp-show).

Chcete-li úspěšně šifrování nebo dešifrování virtuálních disků, na kryptografický klíč uložený ve službě Key Vault musí být nastaveno tak, aby povolovala objektu služby Azure Active Directory ke čtení klíče. Nastavit oprávnění pro Key Vault s [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). V následujícím příkladu je ID instančního objektu služby zadaný ve výstupu předchozího příkazu:

```azurecli-interactive
az keyvault set-policy --name $keyvault_name --spn $sp_id \
  --key-permissions wrapKey \
  --secret-permissions set
```


## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
Vytvoření virtuálního počítače s [az vm vytvořit](/cli/azure/vm#az-vm-create) a připojit datový disk 5 Gb. Pouze určité Image marketplace podporují šifrování disku. Následující příklad vytvoří virtuální počítač s názvem *myVM* pomocí *Ubuntu 16.04 LTS* bitové kopie:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

K virtuálnímu počítači pomocí SSH *publicIpAddress* zobrazí ve výstupu předchozího příkazu. Vytvořte oddíl a systém souborů a potom připojit datový disk. Další informace najdete v tématu [připojit k virtuálnímu počítači s Linuxem připojit nový disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Ukončete relaci SSH.


## <a name="encrypt-the-virtual-machine"></a>Šifrování virtuálního počítače
K šifrování virtuálních disků, které pohromadě všechny předchozí komponenty:

1. Zadejte instanční objekt Azure Active Directory a heslo.
2. Zadejte služby Key Vault k uložení metadat pro šifrované disky.
3. Zadejte kryptografické klíče pro skutečné šifrování a dešifrování.
4. Určete, jestli chcete šifrovat disk s operačním systémem, datové disky nebo všechny.

Šifrování virtuálního počítače s [az vm encryption povolit](/cli/azure/vm/encryption#az-vm-encryption-enable). V následujícím příkladu *$sp_id* a *$sp_password* proměnné z předchozího [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) příkaz:

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

Pro proces šifrování disku pro dokončení chvíli trvat. Monitorování stavu procesu s [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group myResourceGroup --name myVM
```

Výstup se podobá následujícímu příkladu zkrácený:

```json
[
  "dataDisk": "EncryptionInProgress",
  "osDisk": "EncryptionInProgress"
]
```

Počkejte, dokud se stav pro operační systém na disku sestavy **VMRestartPending**, restartujte virtuální počítač s [az vm restart](/cli/azure/vm#az-vm-restart):

```azurecli-interactive
az vm restart --resource-group myResourceGroup --name myVM
```

Proces šifrování disku se dokončuje během spouštění, proto Počkejte několik minut před kontroluje se stav šifrování s [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group myResourceGroup --name myVM
```

Disk s operačním systémem i datový disk jako teď hlásit stav **šifrované**.


## <a name="add-additional-data-disks"></a>Přidat další datové disky
Jakmile jste zašifrovali datové disky, můžete později ke svému virtuálnímu počítači přidat další virtuální disky a taky k šifrování je. Umožňuje například přidat druhý virtuální disk k virtuálnímu počítači následujícím způsobem:

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --disk myDataDisk \
    --new \
    --size-gb 5
```

Znovu spusťte příkaz k šifrování virtuálních disků následujícím způsobem:

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
* Další informace o správě služby Azure Key Vault, včetně odstranění kryptografické klíče a trezory, naleznete v tématu [Správa služby Key Vault pomocí rozhraní příkazového řádku](../../key-vault/key-vault-manage-with-cli2.md).
* Další informace o šifrování disku, například příprava šifrované vlastního virtuálního počítače k nahrání do Azure, najdete v části [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
