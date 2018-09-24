---
title: Diagnostika problému s filtrováním síťového provozu virtuálního počítače – rychlý start – Azure CLI | Microsoft Docs
description: V tomto rychlém startu zjistíte, jak diagnostikovat problém s filtrováním síťového provozu virtuálního počítače pomocí funkce ověření toku protokolů IP služby Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose a virtual machine (VM) network traffic filter problem that prevents communication to and from a VM.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 4be1fac519ee0a7bcd61bd4cced4d829c275679d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990106"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-cli"></a>Rychlý start: Diagnostika problému s filtrováním síťového provozu virtuálního počítače – Azure CLI

V tomto rychlém startu nasadíte virtuální počítač a potom zkontrolujete obousměrnou komunikaci mezi IP adresou a adresou URL. Určíte příčinu selhání komunikace a najdete jeho řešení.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.28 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Po ověření verze CLI spusťte příkaz `az login`, abyste vytvořili připojení k Azure. Příkazy rozhraní CLI v tomto rychlém startu jsou naformátované ke spuštění v prostředí Bash.

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

Pokud chcete otestovat síťovou komunikaci pomocí služby Network Watcher, musíte nejprve sledovací proces sítě povolit v oblasti, ve které se nachází daný virtuální počítač, a potom teprve můžete k otestování komunikace použít funkci ověření toků protokolu IP služby Network Watcher.

### <a name="enable-network-watcher"></a>Povolení sledovacího procesu sítě

Pokud už máte sledovací proces sítě v oblasti USA – východ povolený, přeskočte vpřed na část [Použití ověření toku protokolu IP](#use-ip-flow-verify). K vytvoření sledovacího procesu sítě v oblasti USA – východ použijte příkaz [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure):

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-ip-flow-verify"></a>Použití ověření toku protokolu IP

Když vytvoříte virtuální počítač, Azure u něj ve výchozím nastavení blokuje příchozí i odchozí síťový provoz. Později můžete výchozí nastavení Azure přepsat a povolit nebo odepřít další typy provozu. Když chcete otestovat, zda je provoz na různé cíle a ze zdrojových IP adres povolený nebo zakázaný, použijte příkaz [az network watcher test-ip-flow](/cli/azure/network/watcher#az-network-watcher-test-ip-flow).

Otestujte odchozí komunikaci z virtuálního počítače na jednu z IP adres stránky www.bing.com:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 13.107.21.200:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Po několika sekundách vás vrácený výsledek informuje, že přístup je povolený pravidlem zabezpečení s názvem **AllowInternetOutbound**.

Otestujte odchozí komunikaci z virtuálního počítače na IP adresu 172.31.0.100:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 172.31.0.100:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Vrácený výsledek vás informuje o tom, že přístup byl odepřen pravidlem zabezpečení s názvem **DefaultOutboundDenyAll**.

Otestujte příchozí komunikaci do virtuálního počítače z IP adresy 172.31.0.100:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction inbound \
  --local 10.0.0.4:80 \
  --protocol TCP \
  --remote 172.31.0.100:60000 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Vrácený výsledek vás informuje o tom, že přístup byl odepřen kvůli pravidlu zabezpečení s názvem **DefaultInboundDenyAll**. Nyní, když už víte, která pravidla zabezpečení umožňují nebo odepírají příchozí a odchozí provoz virtuálního počítače, můžete určit, jak tyto problémy vyřešit.

## <a name="view-details-of-a-security-rule"></a>Zobrazení podrobností pravidla zabezpečení

Když chcete určit, proč pravidla v části [Použití ověření toku protokolu IP](#use-ip-flow-verify) komunikaci povolují nebo blokují, zkontrolujte platná pravidla zabezpečení síťového rozhraní pomocí příkazu [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg):

```azurecli-interactive
az network nic list-effective-nsg \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Vrácený výstup obsahuje následující text pravidla **AllowInternetOutbound**, které v části [Použití ověření toku protokolu IP](#use-ip-flow-verify) umožnilo v předchozím kroku odchozí přístup k adrese www.bing.com:

```azurecli
{
 "access": "Allow",
 "additionalProperties": {},
 "destinationAddressPrefix": "Internet",
 "destinationAddressPrefixes": [
  "Internet"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": [
  "1.0.0.0/8",
  "2.0.0.0/7",
  "4.0.0.0/6",
  "8.0.0.0/7",
  "11.0.0.0/8",
  "12.0.0.0/6",
  ...
 ],
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/AllowInternetOutBound",
 "priority": 65001,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

V předchozím výstupu můžete vidět, že **předponou zdrojové adresy** (destinationAddressPrefix) je **Internet**. Není však jasné, jak adresa 13.107.21.200 souvisí s **Internetem**. V části **expandedDestinationAddressPrefix** můžete vidět několik předpon adres a jednou z nich je **12.0.0.0/6**, která zahrnuje rozsah IP adres od 12.0.0.1 do 15.255.255.254. Vzhledem k tomu, že adresa 13.107.21.200 do tohoto rozsahu adres patří, pravidlo **AllowInternetOutBound** jí odchozí provoz umožní. Kromě toho v předchozím výstupu nejsou zobrazená žádná jiná pravidla s vyšší prioritou (nižším číslem), která by toto pravidlo přepisovala. Pokud byste chtěli odchozí komunikaci na IP adresu odepřít, mohli byste přidat pravidlo zabezpečení s vyšší prioritou, které portu 80 odchozí komunikaci na tuto IP adresu zakáže.

Když jste spustili příkaz `az network watcher test-ip-flow`, abyste v části [Použití ověření toku protokolu IP](#use-ip-flow-verify) otestovali odchozí komunikaci na IP adresu 172.131.0.100, výstup vás informoval, že pravidlo **DefaultOutboundDenyAll** komunikaci odepřelo. Pravidlo **DefaultOutboundDenyAll** plní stejnou funkci jako pravidlo **DenyAllOutBound** uvedené v následujícím výstupu z příkazu `az network nic list-effective-nsg`:

```azurecli
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllOutBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
}
```

Toto pravidlo uvádí jako **předponu zdrojové adresy** (destinationAddressPrefix) adresu **0.0.0.0/0**. Toto pravidlo zakazuje odchozí komunikaci na IP adresu 172.131.0.100, protože tato adresa se nenachází v **předponě zdrojové adresy** (destinationAddressPrefix) žádného jiného odchozího pravidla ve výstupu z příkazu `az network nic list-effective-nsg`. Pokud chcete odchozí komunikaci povolit, můžete přidat pravidlo zabezpečení s vyšší prioritou, které IP adrese 172.131.0.100 na portu 80 umožní odchozí komunikaci.

Když jste spustili příkaz `az network watcher test-ip-flow`, abyste v části [Použití ověření toku protokolu IP](#use-ip-flow-verify) otestovali příchozí komunikaci z IP adresy 172.131.0.100, výstup vás informoval, že pravidlo **DefaultInboundDenyAll** komunikaci odepřelo. Pravidlo **DefaultInboundDenyAll** plní stejnou funkci jako pravidlo **DenyAllInBound** uvedené v následujícím výstupu z příkazu `az network nic list-effective-nsg`:

```azurecli
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Inbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllInBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

Pravidlo **DenyAllInBound** se použilo, protože jak je vidět ve výstupu, žádné jiné pravidlo s vyšší prioritou, které by umožnovalo portu 80 příchozí komunikaci na virtuální počítač z IP adresy 172.131.0.100, ve výstupu z příkazu `az network nic list-effective-nsg` neexistuje. Pokud chcete příchozí komunikaci povolit, mohli byste přidat pravidlo zabezpečení s vyšší prioritou, které by portu 80 příchozí komunikaci z IP adresy 172.131.0.100 umožnilo.

Kontroly v tomto rychlém startu testovaly konfiguraci Azure. Pokud kontroly vrátily očekávané výsledky a problémy se sítí přetrvávají, přesvědčte se, že mezi virtuálním počítačem a koncovým bodem, se kterým komunikujete, se nenachází brána firewall a že operační systém ve virtuálním počítači nemá bránu firewall, která by komunikaci povolovala nebo odepírala.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít příkaz [az group delete](/cli/azure/group#az_group_delete):

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili virtuální počítač a diagnostikovali jste příchozí a odchozí filtry síťového provozu. Zjistili jste, že pravidla skupiny zabezpečení sítě umožňují nebo odepírají příchozí i odchozí provoz virtuálního počítače. Zjistěte více o [pravidlech zabezpečení](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a způsobu [jejich vytvoření](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule).

Přestože budete používat správné filtry síťového provozu, komunikace s virtuálním počítačem může kvůli konfigurací směrování stejně selhat. Získejte více informací o [diagnostice potíží se směrováním sítě virtuálních počítačů](diagnose-vm-network-routing-problem-cli.md) nebo si přečtěte článek o [řešení potíží s připojením](network-watcher-connectivity-cli.md), abyste mohli diagnostikovat potíže s odchozím směrováním, latencí a filtrováním provozu.