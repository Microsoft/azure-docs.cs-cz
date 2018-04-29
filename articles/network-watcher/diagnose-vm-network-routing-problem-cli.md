---
title: Diagnostika virtuálního počítače směrování potížím se sítí - rozhraní příkazového řádku Azure | Microsoft Docs
description: V tomto článku zjistěte, jak diagnostikovat problém směrování sítě virtuálního počítače pomocí další funkce směrování služby sledovací proces sítě Azure.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: fcb7ec2e40b5c0e8794d2f4d70395dcbecca019c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnostika virtuálního počítače směrování potížím se sítí - rozhraní příkazového řádku Azure

V tomto článku nasazení virtuálního počítače (VM) a zkontrolujte komunikace na IP adresu a adresu URL. Je-li určit příčinu selhání komunikace a jak ho mohli vyřešit.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud si zvolíte instalaci a použití rozhraní příkazového řádku místně, v tomto článku vyžaduje, že používáte Azure CLI verze 2.0.28 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Po ověření verze rozhraní příkazového řádku, spusťte `az login` vytvořit připojení s Azure. Příkazy rozhraní příkazového řádku v tomto článku jsou naformátovaná tak, aby spustit v prostředí Bash.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Než bude možné vytvořit virtuální počítač, musíte vytvořit skupinu prostředků tak, aby obsahovala virtuálního počítače. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`. Následující příklad vytvoří virtuální počítač s názvem *Můjvp*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. V zbývající kroky nebudete pokračovat, dokud je virtuální počítač vytvořený a vrátí výstup rozhraní příkazového řádku.

## <a name="test-network-communication"></a>Test síťové komunikace

K testování komunikaci sítě s sledovací proces sítě, musíte nejprve povolit sledovací proces sítě ve virtuálním počítači, který chcete testovat je v oblasti a pak použít další funkce směrování sledovací proces sítě k testování komunikace.

### <a name="enable-network-watcher"></a>Povolit sledovací proces sítě

Pokud již máte sledovací proces sítě povolené v oblasti Východ USA, přejděte k [další segment používá](#use-next-hop). Použití [sledovací proces sítě az konfigurace](/cli/azure/network/watcher#az-network-watcher-configure) příkaz pro vytvoření sledovací proces sítě v oblasti Východ USA:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Použít další směrování

Azure automaticky vytvoří trasy do výchozího umístění. Můžete vytvořit vlastní trasy, které potlačí výchozí trasy. Vlastní trasy v některých případech může způsobit selhání komunikace. Chcete-li otestovat směrování z virtuálního počítače, použijte [az sítě sledovacích procesů zobrazit next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) k určení další segment směrování, kdy je provoz určený pro konkrétní adresu.

Testovací odchozí komunikaci z virtuálního počítače na některou z IP adres pro www.bing.com:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Za několik sekund, výstup vás upozorní, který **nextHopType** je **Internet**a že **routeTableId** je **systémová trasa**. Tento výsledek umožňuje vědět, že je platný trasu k cíli.

Testovací odchozí komunikaci z virtuálního počítače do 172.31.0.100:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Výstup vás upozorní, který **žádné** je **nextHopType**a že **routeTableId** je také **systémová trasa**. Tento výsledek umožňuje vědět, že při trasu platné systému do cílového umístění neexistuje žádné další směrování směrovat provoz do cílového umístění.

## <a name="view-details-of-a-route"></a>Zobrazení podrobností trasu

K analýze další směrování, zkontrolujte efektivní trasy pro síťové rozhraní s [az sítě seskupování zobrazit platné trasy tabulky](/cli/azure/network/nic#az-network-nic-show-effective-route-table) příkaz:

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Tento text je zahrnuta ve vrácené výstupu:

```azurecli
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
  },
  "addressPrefix": [
    "0.0.0.0/0"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "Internet",
  "source": "Default",
  "state": "Active"
},
```

Při použití `az network watcher show-next-hop` příkaz k testování odchozí komunikaci 13.107.21.200 v [další segment používá](#use-next-hop), trasa **addressPrefix** 0.0.0.0/0** byl použit pro směrování provozu na adresu, protože žádná další trasa ve výstupu zahrnuje adresu. Ve výchozím nastavení jsou všechny adresy nejsou zadané v rámci předpona adresy jiného trasy směrovány na internet.

Pokud jste použili `az network watcher show-next-hop` příkazu k testování odchozí komunikaci 172.31.0.100 ale, výsledek informováni, že se žádná další typ směrování. Ve výstupu vráceného zobrazí také následující text:

```azurecli
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
      },
  "addressPrefix": [
    "172.16.0.0/12"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "None",
  "source": "Default",
  "state": "Active"
},
```

Jak je vidět ve výstupu `az network watcher nic show-effective-route-table` příkaz, přestože je výchozí trasu k 172.16.0.0/12 předponu, která zahrnuje 172.31.0.100 adresu, **nextHopType** je **žádné**. Azure vytvoří výchozí trasu k 172.16.0.0/12, ale není zadat typ dalšího směrování, dokud je důvod se. Pokud jste například přidali rozsah adres 172.16.0.0/12 do adresního prostoru virtuální sítě, Azure změní **nextHopType** k **virtuální síť** pro trasu. Kontrola by pak zobrazíte **virtuální síť** jako **nextHopType**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít příkaz [az group delete](/cli/azure/group#az_group_delete):

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili virtuální počítač a diagnostice, směrování sítě z virtuálního počítače. Jste zjistili, že Azure vytvoří několik výchozích tras a testovat směrování na dva různé cíle. Další informace o [směrování v Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a postup [vytvořit vlastní trasy](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Pro odchozí připojení virtuálních počítačů, můžete také určit latence a povolené a odepřen síťový provoz mezi virtuálního počítače a koncový bod pomocí sledovací proces sítě [řešení potíží s připojení](network-watcher-connectivity-cli.md) schopností. Můžete monitorovat komunikace mezi virtuálního počítače a koncový bod, jako je například IP adresu nebo adresy URL, v čase pomocí funkce monitorování připojení sledovací proces sítě. Další informace, jak zjistit, [monitorování připojení k síti](connection-monitor.md).