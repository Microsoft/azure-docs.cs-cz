---
title: Diagnostika problému se směrováním sítě virtuálních počítačů – Azure CLI
titleSuffix: Azure Network Watcher
description: V tomto článku se dozvíte, jak pomocí Azure CLI diagnostikovat potíže s směrováním sítě virtuálního počítače pomocí funkce dalšího směrování Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 5dac16e5cc1e88d833bf6d3c2660570fcf8b8e9e
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216951"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnostika potíží se síťovým směrováním virtuálního počítače – Azure CLI

V tomto článku nasadíte virtuální počítač (VM) a pak zkontrolujete komunikaci s IP adresou a adresou URL. Určíte příčinu selhání komunikace a najdete jeho řešení.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná. 

- Příkazy Azure CLI v tomto článku jsou formátované tak, aby se spouštěly v prostředí bash.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Než vytvoříte virtuální počítač, musíte vytvořit skupinu prostředků, která bude virtuální počítač obsahovat. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

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

Vytvoření virtuálního počítače trvá několik minut. Dokud se virtuální počítač nevytvoří a Azure CLI vrátí výstup, nepokračujte podle zbývajících kroků.

## <a name="test-network-communication"></a>Test síťové komunikace

Chcete-li otestovat síťovou komunikaci s Network Watcher, je třeba nejprve povolit sledovací proces sítě v oblasti, ve které je virtuální počítač, který chcete testovat, a poté použít možnost Network Watcher dalšího směrování k otestování komunikace.

### <a name="enable-network-watcher"></a>Povolení Network Watcheru

Pokud už máte sledovací proces sítě povolený v oblasti Východní USA, přeskočte na [použít další segment směrování](#use-next-hop). Pomocí příkazu [AZ Network Watch-Configure](/cli/azure/network/watcher#az-network-watcher-configure) vytvořte sledovací proces sítě v oblasti východní USA:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Použití dalšího směrování

Azure automaticky vytváří trasy pro výchozí cíle. Můžete vytvořit vlastní trasy, které přepíšou ty výchozí. Někdy můžou vlastní trasy způsobit selhání komunikace. Pokud chcete testovat směrování z virtuálního počítače, použijte [příkaz AZ Network sledovacího procesu show-Next-Hop](/cli/azure/network/watcher#az-network-watcher-show-next-hop) a určete další směrování směrování, když je provoz určený pro konkrétní adresu.

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

Po několika sekundách vám výstup informuje o tom, že je **typem** **Internet** a že **routeTableId** je **systémovou trasou**. Tento výsledek vám umožní zjistit, zda existuje platná trasa k cíli.

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

Vrácený výstup vás informuje o tom, že **žádný** je **typem** a že **RouteTableId** je také **systémovou trasou**. Tento výsledek říká, že i když existuje trasa systému do cíle, neexistuje žádné další směrování, které by do cíle směrovalo provoz.

## <a name="view-details-of-a-route"></a>Zobrazení podrobností o trase

Pokud chcete dál analyzovat směrování, Projděte si efektivní trasy pro síťové rozhraní pomocí příkazu [AZ Network nic show--Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) :

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Vrácený výstup obsahuje následující text:

```console
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

Když jste `az network watcher show-next-hop` pomocí příkazu otestovali odchozí komunikaci s 13.107.21.200 při [použití dalšího směrování](#use-next-hop), použila se trasa s **addressPrefix** 0.0.0.0/0 * * ke směrování provozu na adresu, protože žádná jiná trasa ve výstupu neobsahuje adresu. Standardně se všechny adresy, které se nezadaly v předponě adresy jiné trasy, směrují do internetu.

Při použití `az network watcher show-next-hop` příkazu k otestování odchozí komunikace k 172.31.0.100 ale výsledek oznamuje, že neexistuje typ dalšího segmentu směrování. V vráceném výstupu se zobrazí také následující text:

```console
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

Jak vidíte ve výstupu `az network watcher nic show-effective-route-table` příkazu, i když je výchozí trasa k předponě 172.16.0.0/12, která zahrnuje adresu 172.31.0.100, **typem** je **none**. Azure vytváří výchozí trasu do 172.16.0.0/12, ale neurčuje typ dalšího směrování, dokud k tomu není důvod. Pokud jste například přidali rozsah adres 172.16.0.0/12 do adresního prostoru virtuální sítě, Azure změní **typem** na **virtuální síť** pro trasu. Při kontrole se pak jako **typem** zobrazí **virtuální síť** .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít příkaz [az group delete](/cli/azure/group#az-group-delete):

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili virtuální počítač a diagnostikovat síťové směrování z virtuálního počítače. Dozvěděli jste se, že Azure vytváří několik výchozích tras, a otestovali jste směrování na dva různé cíle. Přečtěte si i další informace o [směrování v Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a o tom, jak [vytvářet vlastní trasy](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

U odchozích připojení virtuálních počítačů můžete také určit latenci a povolený a zakázaný síťový provoz mezi virtuálním počítačem a koncovým bodem pomocí funkce [řešení potíží s připojením](network-watcher-connectivity-cli.md) Network Watcher. Komunikaci mezi virtuálním počítačem a koncovým bodem, jako je IP adresa nebo adresa URL, můžete v průběhu času sledovat pomocí možnosti monitorování připojení Network Watcher. Informace o postupu najdete v tématu [monitorování síťového připojení](connection-monitor.md).
