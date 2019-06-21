---
title: Vytvoření a šifrování virtuálního počítače s Linuxem pomocí Azure CLI
description: V tomto rychlém startu se dozvíte, jak používat rozhraní příkazového řádku Azure k vytvoření a šifrování virtuálního počítače s Linuxem
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 01c5c9b2e823c00d0a750cd466bb3a1ebf73debf
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67294951"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Rychlý start: Vytvoření a šifrování virtuálního počítače s Linuxem pomocí Azure CLI

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. V tomto rychlém startu se dozvíte, jak používat rozhraní příkazového řádku Azure k vytvoření a šifrování virtuálního počítače s Linuxem (VM).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). Následující příklad vytvoří virtuální počítač *myVM*.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --size Standard_D2S_V3 \
```

Vytvoření virtuálního počítače a podpůrných prostředků trvá několik minut. Následující příklad ukazuje, že operace vytvoření virtuálního počítače byla úspěšná.

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Vytvořte službu Key Vault nakonfigurované pro šifrovací klíče

Azure disk encryption ukládá jeho šifrovacího klíče do služby Azure Key Vault. Vytvořte službu Key Vault s [az keyvault vytvořit](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Chcete-li povolit služby Key Vault pro ukládání šifrovacích klíčů, použijte parametr--povolené pro šifrování disků.

> [!Important]
> Každá služba Key Vault musí mít jedinečný název. Následující příklad vytvoří trezor klíčů s názvem *myKV*, ale je nutné jenom pojmenovat něco jiného.

```azurecli
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Šifrování virtuálního počítače

Šifrování virtuálního počítače s [az vm encryption](/cli/azure/vm/encryption?view=azure-cli-latest), poskytují jedinečné služby Key Vault název parametru--disk šifrování – trezor klíčů.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

Za okamžik, který vrátí procesu "šifrování požadavek byl přijat. Zobrazit příkaz ke sledování průběhu použijte. ". Příkaz "show" je [az vm show](/cli/azure/vm/encryption#az-vm-encryption-show).

```azurecli-interactive
az vm show --name MyVM -g MyResourceGroup
```

Když je povoleno šifrování, zobrazí se následující ve vrácené výstupu:

```azurecli-interactive
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete použít [odstranění skupiny az](/cli/azure/group) příkazu odeberte skupinu prostředků, virtuálního počítače a služby Key Vault. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili virtuální počítač, vytvořili službu Key Vault, který byl povolit pro šifrovací klíče a šifrované virtuální počítač.  V dalším článku najdete další informace o požadavcích na službu Azure Disk Encryption pro virtuální počítače IaaS.

> [!div class="nextstepaction"]
> [Požadavky Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)
