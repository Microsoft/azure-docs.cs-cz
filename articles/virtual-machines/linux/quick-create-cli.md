---
title: Rychlý start – Vytvoření virtuálního počítače s Linuxem pomocí Azure CLI 2.0 | Microsoft Docs
description: V tomto rychlém startu zjistíte, jak pomocí Azure CLI 2.0 vytvořit virtuální počítač s Linuxem
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6536860bb75d068a96899f2d30ec7a6126a28436
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927627"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli-20"></a>Rychlý start: Vytvoření virtuálního počítače s Linuxem pomocí Azure CLI 2.0

Azure CLI 2.0 slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. V tomto rychlém startu se dozvíte, jak pomocí Azure CLI 2.0 nasadit do Azure virtuální počítač s Linuxem Ubuntu. Pak se k virtuálnímu počítači připojíte přes SSH a nainstalujete na něj webový server NGINX, abyste virtuální počítač viděli v akci.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create).

Následující příklad vytvoří virtuální počítač s názvem *myVM*, přidá uživatelský účet s názvem *azureuser* a vygeneruje klíče SSH, pokud ještě neexistují ve výchozím umístění klíčů (*~/.ssh*). Pokud chcete použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
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
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Poznamenejte si vlastní adresu `publicIpAddress` ve výstupu z vašeho virtuálního počítače. Tato adresa se používá pro přístup k virtuálnímu počítači v dalších krocích.

## <a name="open-port-80-for-web-traffic"></a>Otevření portu 80 pro webový provoz

Ve výchozím nastavení jsou při vytváření virtuálního počítače s Linuxem v Azure otevřená pouze připojení SSH. Pomocí příkazu [az vm open-port](/cli/azure/vm#az_vm_open_port) otevřete port TCP 80 pro použití s webovým serverem NGINX:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Připojení k virtuálnímu počítači

Připojte se k virtuálnímu počítači přes SSH jako obvykle. Nahraďte adresu **publicIpAddress** veřejnou IP adresou vašeho virtuálního počítače, kterou jste si poznamenali v předchozím výstupu z virtuálního počítače:

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>Instalace webového serveru

Pokud se chcete podívat na virtuální počítač v akci, nainstalujte webový server NGINX. Spuštěním následujících příkazů z vaší relace SSH aktualizujte zdroje balíčku a nainstalujte nejnovější balíček NGINX:

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Potom ukončete relaci SSH pomocí příkazu `exit`.

## <a name="view-the-web-server-in-action"></a>Zobrazení webového serveru v akci

Po instalaci serveru NGINX a otevření portu 80 na virtuálním počítači pro přístup z internetu přejděte pomocí libovolného webového prohlížeče na výchozí úvodní stránku serveru NGINX. Použijte veřejnou IP adresu virtuálního počítače, kterou jste získali v předchozím kroku. Následující příklad ukazuje výchozí web NGINX:

![Výchozí web NGINX](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az_group_delete). Přesvědčte se, že jste ukončili relaci SSH k vašemu virtuálnímu počítači a pak odstraňte prostředky následujícím způsobem:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jednoduchý virtuální počítač, otevřeli jste síťový port pro webový provoz a nainstalovali jste základní webový server. Další informace o virtuálních počítačích Azure najdete v kurzu pro virtuální počítače s Linuxem.


> [!div class="nextstepaction"]
> [Kurzy pro virtuální počítače Azure s Linuxem](./tutorial-manage-vm.md)
