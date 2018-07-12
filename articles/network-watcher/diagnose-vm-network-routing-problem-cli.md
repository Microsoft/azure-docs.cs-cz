---
title: Diagnostika potíží se virtuální počítač sítě směrováním – rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: V tomto článku se dozvíte, jak k diagnostice problému směrování sítě virtuálního počítače pomocí dalšího segmentu směrování funkce služby Azure Network Watcher.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38618946"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnostika potíží se virtuální počítač sítě směrováním – rozhraní příkazového řádku Azure

V tomto článku nasazení virtuálního počítače (VM) a potom zkontrolujte komunikaci na IP adresu a adresu URL. Určíte příčinu selhání komunikace a najdete jeho řešení.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, tento článek vyžaduje použití Azure CLI verze 2.0.28 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Po ověření verze CLI spusťte příkaz `az login`, abyste vytvořili připojení k Azure. Příkazy rozhraní příkazového řádku v tomto článku jsou formátovány ke spuštění v prostředí Bash.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Než vytvoříte virtuální počítač, musíte vytvořit skupinu prostředků, která bude virtuální počítač obsahovat. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`. Následující příklad vytvoří virtuální počítač *myVm*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Dokud se virtuální počítač nevytvoří a CLI nevrátí výstup, nepokračujte ve zbývajících krocích.

## <a name="test-network-communication"></a>Test síťové komunikace

Otestovat síťová komunikace pomocí služby Network Watcher, musíte nejprve povolit network watcher ve službě oblast, kterou virtuální počítač, který chcete testovat a pak použít další možnosti směrování ve službě Network Watcher k otestování komunikace.

### <a name="enable-network-watcher"></a>Povolení sledovacího procesu sítě

Pokud už máte k dispozici sledovací proces sítě povolené v oblasti USA – východ, přejděte k [použití dalšího segmentu směrování](#use-next-hop). Použití [konfigurace network watcheru az](/cli/azure/network/watcher#az-network-watcher-configure) příkazu vytvořte network watcheru v oblasti USA – východ:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Použití dalšího směrování

Azure automaticky vytváří trasy pro výchozí cíle. Můžete vytvořit vlastní trasy, které přepíšou ty výchozí. Někdy můžou vlastní trasy způsobit selhání komunikace. K otestování směrování z virtuálního počítače použijte [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) můžete určit další segment směrování, kdy je provoz určený pro konkrétní adresu.

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

Po pár sekundách výstupu uvidíte, které **nextHopType** je **Internet**a že **routeTableId** je **systémová trasa**. Tento výsledek vám umožňuje vědět, že je platná trasa do cíle.

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

Výstupu vráceného vás informuje, který **žádný** je **nextHopType**a že **routeTableId** je také **systémová trasa**. Tento výsledek říká, že i když existuje trasa systému do cíle, neexistuje žádné další směrování, které by do cíle směrovalo provoz.

## <a name="view-details-of-a-route"></a>Zobrazení podrobností o trase

Pokud chcete analyzovat další směrování, zkontrolujte efektivní trasy pro síťové rozhraní s [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) příkaz:

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Následující text je zahrnuta ve vrácené výstupu:

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

Při použití `az network watcher show-next-hop` příkazu k testování odchozí komunikace na 13.107.21.200 v [použití dalšího segmentu směrování](#use-next-hop), trasa s **addressPrefix** 0.0.0.0/0** byl použit pro směrování provozu na adresu, protože žádné jiné trasy ve výstupu obsahuje adresu. Standardně se všechny adresy, které se nezadaly v předponě adresy jiné trasy, směrují do internetu.

Pokud jste použili `az network watcher show-next-hop` příkazu k testování odchozí komunikace na 172.31.0.100 ale, výsledek informován, že neexistuje žádný typ dalšího segmentu směrování. Ve výstupu vráceného zobrazí také následující text:

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

Jak je vidět ve výstupu `az network watcher nic show-effective-route-table` příkazu, přestože je výchozí trasu k předponě 172.16.0.0/12, která zahrnuje 172.31.0.100 adresu, **nextHopType** je **žádný**. Azure vytváří výchozí trasu do 172.16.0.0/12, ale neurčuje typ dalšího směrování, dokud k tomu není důvod. Pokud například přidáte 172.16.0.0/12 rozsah adres do adresního prostoru virtuální sítě, Azure změní **nextHopType** k **virtuální síť** trasy. Pak by zobrazil kontrolu **virtuální síť** jako **nextHopType**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít příkaz [az group delete](/cli/azure/group#az_group_delete):

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili virtuální počítač a diagnostikovat směrováním v síti z virtuálního počítače. Dozvěděli jste se, že Azure vytváří několik výchozích tras, a otestovali jste směrování na dva různé cíle. Přečtěte si i další informace o [směrování v Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a o tom, jak [vytvářet vlastní trasy](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Pro odchozí připojení virtuálního počítače, můžete také určit, latence a povolený a odepřen síťový provoz mezi virtuálním počítači a koncový bod pomocí Network Watcheru [řešení potíží s připojením](network-watcher-connectivity-cli.md) funkce. Komunikace mezi virtuálním Počítačem a koncový bod, jako je například IP adresa nebo adresa URL, v čase pomocí funkce monitorování připojení Network Watcher můžete monitorovat. Další informace o postupu [monitorování připojení k síti](connection-monitor.md).