---
title: Šifrování disků na virtuální počítač s Linuxem v Azure | Dokumentace Microsoftu
description: Postup zašifrování virtuální disky na virtuální počítač s Linuxem pro zvýšení zabezpečení pomocí Azure CLI
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
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 15bd3cf2ab6ea5285662610c2c0a850bb180e2f8
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55658760"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Jak šifrování virtuálního počítače s Linuxem v Azure

Vylepšené virtuálních počítačů (VM) zabezpečení a dodržování předpisů můžete šifrovat virtuální disky a virtuální počítač. Virtuální počítače jsou šifrované pomocí kryptografických klíčů, které jsou zabezpečené v Azure Key Vault. Řízení těchto kryptografických klíčů a auditovat jejich použití. Tento článek podrobně popisuje, jak šifrování virtuálních disků na virtuální počítač s Linuxem pomocí Azure CLI. 

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/bash](https://shell.azure.com/bash). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, tento článek vyžaduje použití Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="overview-of-disk-encryption"></a>Přehled šifrování disku
Virtuální disky na virtuální počítače s Linuxem jsou zašifrovaná pomocí rest [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Neplatí žádné poplatky pro šifrování virtuálních disků v Azure. Kryptografické klíče jsou uložené ve službě Azure Key Vault software ochrany, nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovaných podle standardů FIPS 140-2 úrovně 2 standardů. Uchování kontroly nad těmito kryptografické klíče a můžete kontrolovat jejich použití. Tyto klíče se používají k šifrování a dešifrování virtuální disky připojené k virtuálnímu počítači. 

Proces šifrování virtuálního počítače je následujícím způsobem:

1. Vytvoření kryptografického klíče do služby Azure Key Vault.
1. Nakonfigurujte kryptografického klíče má být použitelná pro šifrování disků.
1. Povolte disk encryption pro virtuální disky.
1. Vyžaduje kryptografické klíče jsou požadovány ze služby Azure Key Vault.
1. Virtuální disky jsou šifrované pomocí zadaného kryptografického klíče.


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
resourcegroup="myResourceGroup"
az group create --name $resourcegroup --location eastus
```

Azure Key Vault obsahující kryptografické klíče a přidružených výpočetních prostředků, jako jsou úložiště a virtuální počítač se musí nacházet ve stejné oblasti. Vytvoření služby Azure Key Vault s [az keyvault vytvořit](/cli/azure/keyvault#az-keyvault-create) a povolte službě Key Vault pro použití s šifrování disku. Zadejte jedinečný název služby Key Vault pro *keyvault_name* následujícím způsobem:

```azurecli-interactive
keyvault_name=myvaultname$RANDOM
az keyvault create \
    --name $keyvault_name \
    --resource-group $resourcegroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Můžete uložit šifrovací klíče pomocí softwaru nebo ochrana modelů hardwarového zabezpečení (HSM). Použití modulu hardwarového zabezpečení vyžaduje na úrovni premium služby Key Vault. Není jakýchkoli dalších nákladů vytváří na úrovni premium služby Key Vault, nikoli standardní služby Key Vault, který ukládá klíče chráněného softwarem. Vytvoření služby Key Vault na úrovni premium, v předchozím kroku přidat `--sku Premium` k příkazu. Následující příklad používá klíči chráněnými softwarem od vytvoření standardní služby Key Vault.

Pro oba modely ochrany platformy Azure musí mít udělen přístup k vyžádání kryptografických klíčů při spuštění virtuálního počítače k dešifrování virtuálních disků. Vytvoření kryptografické klíče v Key Vault s [az keyvault key vytvořit](/cli/azure/keyvault/key#az-keyvault-key-create). Následující příklad vytvoří klíč s názvem *myKey*:

```azurecli-interactive
az keyvault key create \
    --vault-name $keyvault_name \
    --name myKey \
    --protection software
```


## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
Vytvoření virtuálního počítače s [az vm vytvořit](/cli/azure/vm#az-vm-create) a připojit datový disk 5 Gb. Pouze určité Image marketplace podporují šifrování disku. Následující příklad vytvoří virtuální počítač s názvem *myVM* pomocí *Ubuntu 16.04 LTS* bitové kopie:

```azurecli-interactive
az vm create \
    --resource-group $resourcegroup \
    --name myVM \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

K virtuálnímu počítači pomocí SSH *publicIpAddress* zobrazí ve výstupu předchozího příkazu. Vytvořte oddíl a systém souborů a potom připojit datový disk. Další informace najdete v tématu [připojit k virtuálnímu počítači s Linuxem připojit nový disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Ukončete relaci SSH.


## <a name="encrypt-the-virtual-machine"></a>Šifrování virtuálního počítače


Šifrování virtuálního počítače s [az vm encryption povolit](/cli/azure/vm/encryption#az-vm-encryption-enable):

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Pro proces šifrování disku pro dokončení chvíli trvat. Monitorování stavu procesu s [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group $resourcegroup --name myVM --query 'status'
```

Jakmile budete hotovi, výstup bude vypadat podobně jako v následujícím příkladu:

```json
[
  {
    "code": "ProvisioningState/succeeded",
    "displayStatus": "Provisioning succeeded",
    "level": "Info",
    "message": "Encryption succeeded for all volumes",
    "time": null
  }
]
```


## <a name="add-additional-data-disks"></a>Přidat další datové disky
Po jste zašifrovali datové disky, můžete ke svému virtuálnímu počítači přidat další virtuální disky a jejich šifrování. 

Po přidání datového disku k virtuálnímu počítači znovu spusťte příkaz k šifrování virtuálních disků následujícím způsobem:

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type data
```


## <a name="next-steps"></a>Další postup
* Další informace o správě služby Azure Key Vault, včetně odstranění kryptografické klíče a trezory, naleznete v tématu [Správa služby Key Vault pomocí rozhraní příkazového řádku](../../key-vault/key-vault-manage-with-cli2.md).
* Další informace o šifrování disku, například příprava šifrované vlastního virtuálního počítače k nahrání do Azure, najdete v části [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
