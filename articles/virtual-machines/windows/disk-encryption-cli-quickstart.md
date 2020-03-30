---
title: Vytvoření a šifrování virtuálního počítače s Windows s využitím Azure CLI
description: V tomto rychlém startu se dozvíte, jak pomocí azure cli vytvořit a zašifrovat virtuální počítač s Windows
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 786a906241b355cdae403c6ed08b60eb27045d6f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385244"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Úvodní příručka: Vytvoření a šifrování virtuálního počítače s Windows pomocí azure cli

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Tento rychlý start ukazuje, jak pomocí azure cli vytvořit a zašifrovat virtuální počítač (VM) Windows Serveru 2016.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat Azure CLI místně, tento rychlý start vyžaduje, abyste spustili Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create). Následující příklad vytvoří virtuální hod s názvem *myVM*. Tento příklad používá jako uživatelské jméno správce *azureuser* a jako heslo *myPassword12*.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
```

Vytvoření virtuálního počítače a podpůrných prostředků trvá několik minut. Následující příklad ukazuje, že operace vytvoření virtuálního počítače byla úspěšná.

```
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

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Vytvoření trezoru klíčů nakonfigurovaného pro šifrovací klíče

Šifrování disku Azure ukládá svůj šifrovací klíč v trezoru klíčů Azure. Vytvořte trezor klíčů s [vytvořením trezoru az keyvault](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Chcete-li povolit trezor klíčů pro ukládání šifrovacích klíčů, použijte parametr --enabled-for-disk-encryption.
> [!Important]
> Každý trezor klíčů musí mít jedinečný název. Následující příklad vytvoří trezor klíčů s názvem *myKV*, ale musíte pojmenovat svůj něco jiného.

```azurecli-interactive
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Šifrování virtuálního počítače

Šifrujte svůj virtuální počítač pomocí [šifrování virtuálního počítače a](/cli/azure/vm/encryption?view=azure-cli-latest)zadejte jedinečný název trezoru klíčů parametru --disk-encryption-keyvault.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

Můžete ověřit, že šifrování je povoleno na vašem virtuálním počítači s [az vm show](/cli/azure/vm/encryption#az-vm-encryption-show)

```azurecli-interactive
az vm show --name MyVM -g MyResourceGroup
```

Ve vráceném výstupu uvidíte následující:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete, můžete pomocí příkazu [delete skupiny az](/cli/azure/group) odebrat skupinu prostředků, virtuální ho dispozičního virtuálního soudu a trezor klíčů.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili virtuální počítač, vytvořili trezor klíčů, který byl povolen pro šifrovací klíče, a zašifrovali virtuální počítač.  V dalším článku najdete další informace o požadavcích na službu Azure Disk Encryption pro virtuální počítače IaaS.

> [!div class="nextstepaction"]
> [Azure Disk Encryption – přehled](disk-encryption-overview.md)
