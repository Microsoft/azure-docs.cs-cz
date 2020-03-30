---
title: Diagnostika problému se směrováním v síti virtuálních počítači – Azure CLI
titleSuffix: Azure Network Watcher
description: V tomto článku se dozvíte, jak diagnostikovat problém směrování sítě virtuálních strojů pomocí další možnosti směrování služby Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
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
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: ae139ea7aca7c3896fcd7b0acf2bf6673490a2f4
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382898"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnostika problému směrování sítě virtuálních strojů – Azure CLI

V tomto článku nasadíte virtuální počítač (VM) a potom zkontrolujte komunikaci s IP adresou a adresou URL. Určíte příčinu selhání komunikace a najdete jeho řešení.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat Azure CLI místně, tento článek vyžaduje, abyste spouštěli Azure CLI verze 2.0.28 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). Po ověření verze rozhraní příkazového `az login` příkazu Azure spusťte a vytvořte připojení k Azure. Příkazy Azure CLI v tomto článku jsou formátovány tak, aby se spouštěli v prostředí Bash.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Než vytvoříte virtuální počítač, musíte vytvořit skupinu prostředků, která bude virtuální počítač obsahovat. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az-vm-create). Pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`. Následující příklad vytvoří virtuální počítač *myVm*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Nepokračujte se zbývajícími kroky, dokud se nevytvoří virtuální počítač a azure CLI vrátí výstup.

## <a name="test-network-communication"></a>Test síťové komunikace

Chcete-li otestovat síťovou komunikaci s sledovacím procesem sítě, musíte nejprve povolit sledovací proces sítě v oblasti, ve které je virtuální modul, ve kterém chcete testovat, a potom k testování komunikace použít další schopnost směrování sledovacího programu sítě.

### <a name="enable-network-watcher"></a>Povolení Network Watcheru

Pokud již máte v oblasti USA –, přejděte na [Použít další směrování](#use-next-hop). Pomocí příkazu [konfigurace sledování sítě az vytvořte](/cli/azure/network/watcher#az-network-watcher-configure) sledovací proces sítě v oblasti USA – východ:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Použití dalšího směrování

Azure automaticky vytváří trasy pro výchozí cíle. Můžete vytvořit vlastní trasy, které přepíšou ty výchozí. Někdy můžou vlastní trasy způsobit selhání komunikace. Chcete-li otestovat směrování z virtuálního pracovního procesu, použijte [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) k určení dalšího směrování směrování, když je provoz určen pro určitou adresu.

Otestujte odchozí komunikaci z virtuálního počítače na jednu z IP adres stránky www.bing.com:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Po několika sekundách výstup informuje, že **další HopType** je **Internet**a že **routeTableId** je **systémová trasa**. Tento výsledek vám umožní vědět, že existuje platná trasa k cíli.

Otestujte odchozí komunikaci z virtuálního počítače na IP adresu 172.31.0.100:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Vrácený výstup vás informuje, že **None** je **další HopType**a že **routeTableId** je také **systémová trasa**. Tento výsledek říká, že i když existuje trasa systému do cíle, neexistuje žádné další směrování, které by do cíle směrovalo provoz.

## <a name="view-details-of-a-route"></a>Zobrazení podrobností o trase

Chcete-li dále analyzovat směrování, zkontrolujte efektivní trasy pro síťové rozhraní pomocí [příkazu az network nic show-effective-route-table:](/cli/azure/network/nic#az-network-nic-show-effective-route-table)

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Následující text je součástí vrácené výstupu:

```
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

Při použití `az network watcher show-next-hop` příkazu k testování odchozí komunikace na 13.107.21.200 v [použití dalšího směrování](#use-next-hop)byla k směrování provozu na adresu použita trasa s **adresouPrefix** 0.0.0.0/0**, protože žádná jiná trasa ve výstupu neobsahuje adresu. Standardně se všechny adresy, které se nezadaly v předponě adresy jiné trasy, směrují do internetu.

Při použití `az network watcher show-next-hop` příkazu k testování odchozí komunikace na 172.31.0.100 však výsledek informoval, že neexistuje žádný další typ směrování. Ve vráceném výstupu vidíte také následující text:

```
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

Jak můžete vidět ve výstupu `az network watcher nic show-effective-route-table` z příkazu, i když je výchozí trasa k 172.16.0.0/12 předpona, která obsahuje adresu 172.31.0.100, **nextHopType** je **None**. Azure vytváří výchozí trasu do 172.16.0.0/12, ale neurčuje typ dalšího směrování, dokud k tomu není důvod. Pokud jste například přidali rozsah adres 172.16.0.0/12 do adresního prostoru virtuální sítě, Azure změní **další HopType** na **virtuální síť** pro trasu. Kontrola by pak zobrazit **virtuální síť** jako **další HopType**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít příkaz [az group delete](/cli/azure/group#az-group-delete):

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili virtuální ho a diagnostikovali síťové směrování z virtuálního soudu. Dozvěděli jste se, že Azure vytváří několik výchozích tras, a otestovali jste směrování na dva různé cíle. Přečtěte si i další informace o [směrování v Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a o tom, jak [vytvářet vlastní trasy](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

U odchozích připojení virtuálních zařízení můžete také určit latenci a povolený a odepřený síťový provoz mezi virtuálním serverem a koncovým bodem pomocí funkce [řešení potíží s připojením](network-watcher-connectivity-cli.md) sledování sítě. Můžete sledovat komunikaci mezi virtuálním počítačům a koncovým bodem, jako je například IP adresa nebo adresa URL, v průběhu času pomocí funkce sledování připojení sledování sítě. Informace o tom naleznete [v tématu Sledování síťového připojení](connection-monitor.md).
