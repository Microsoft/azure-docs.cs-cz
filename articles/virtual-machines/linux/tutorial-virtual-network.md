---
title: Kurz – Vytváření a správa virtuálních sítí Azure pro virtuální počítače s Linuxem | Microsoft Docs
description: V tomto kurzu zjistíte, jak pomocí Azure CLI vytvářet a spravovat virtuální sítě Azure pro virtuální počítače s Linuxem.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 664dc101232ed092b76e2642ec94c6e7aa5e7a88
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55660561"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-linux-virtual-machines-with-the-azure-cli"></a>Kurz: Vytvoření a správa virtuálních sítí Azure pro virtuální počítače s Linuxem pomocí Azure CLI

Virtuální počítače Azure používají pro interní i externí síťovou komunikaci sítě Azure. Tento kurz vás provede nasazením dvou virtuálních počítačů a konfigurací sítě Azure pro tyto virtuální počítače. Příklady v tomto kurzu předpokládají, že virtuální počítače hostují webovou aplikaci s back-endovou databází, ale aplikace se v tomto kurzu nenasazuje. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě a podsítě
> * Vytvoření veřejné IP adresy
> * Vytvoření front-endového virtuálního počítače
> * Zabezpečení provozu sítě
> * Vytvoření back-endového virtuálního počítače

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte pro tento kurz mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="vm-networking-overview"></a>Přehled sítí virtuálních počítačů

Virtuální sítě Azure umožňují zabezpečená síťová připojení mezi virtuálními počítači, internetem a dalšími službami Azure, jako je Databáze SQL Azure. Virtuální sítě se skládají z logických segmentů zvaných podsítě. Podsítě se používají k řízení toku sítě a jako hranice zabezpečení. Když nasazujete virtuální počítač, obvykle zahrnuje virtuální síťové rozhraní, které je připojené k podsíti.

V průběhu kurzu vytvoříte následující prostředky virtuální sítě:

![Virtuální síť se dvěma podsítěmi](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* – virtuální síť, kterou virtuální počítače používají ke komunikaci mezi sebou a internetem.
- *myFrontendSubnet* – podsíť ve virtuální síti *myVNet*, kterou používají front-endové prostředky.
- *myPublicIPAddress* – veřejná IP adresa, pomocí které se můžete z internetu připojit k virtuálnímu počítači *myFrontendVM*.
- *myFrontentNic* – síťové rozhraní, které virtuální počítač *myFrontendVM* používá ke komunikaci s virtuálním počítačem *myBackendVM*.
- *myFrontendVM* – virtuální počítač, pomocí kterého můžete komunikovat mezi internetem a virtuálním počítačem *myBackendVM*.
- *myBackendNSG* – skupina zabezpečení sítě, která řídí komunikaci mezi virtuálními počítači *myFrontendVM* a *myBackendVM*.
- *myBackendSubnet* – podsíť přidružená ke skupině zabezpečení sítě *myBackendNSG*, kterou používají back-endové prostředky.
- *myBackendNic* – síťové rozhraní, které virtuální počítač *myBackendVM* používá ke komunikaci s virtuálním počítačem *myFrontendVM*.
- *myBackendVM* – virtuální počítač, který používá porty 22 a 3306 ke komunikaci s virtuálním počítačem *myFrontendVM*.

## <a name="create-a-virtual-network-and-subnet"></a>Vytvoření virtuální sítě a podsítě

V tomto kurzu vytvoříte virtuální síť se dvěma podsítěmi. Front-endovou podsíť k hostování webové aplikace a back-endovou podsíť pro hostování databázového serveru.

Před vytvořením virtuální sítě vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myRGNetwork* v umístění eastus (USA – východ).

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### <a name="create-virtual-network"></a>Vytvoření virtuální sítě

Pomocí příkazu [az network vnet create](/cli/azure/network/vnet) vytvořte virtuální síť. V tomto příkladu se síť jmenuje *myVNet* a má předponu adresy *10.0.0.0/16*. Vytvoří se také podsíť s názvem *myFrontendSubnet* a předponou *10.0.1.0/24*. Později v tomto kurzu se k této síti připojí front-endový virtuální počítač. 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVNet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myFrontendSubnet \
  --subnet-prefix 10.0.1.0/24
```

### <a name="create-subnet"></a>Vytvoření podsítě

Novou podsíť přidáte k virtuální síti pomocí příkazu [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). V tomto příkladu se podsíť jmenuje *myBackendSubnet* a má předponu adresy *10.0.2.0/24*. Tato podsíť se používá se všemi back-endovými službami.

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --address-prefix 10.0.2.0/24
```

Nyní jste vytvořili síť rozdělenou do dvou podsítí – jedné pro front-endové služby a druhé pro back-endové služby. V další části vytvoříme virtuální počítače a připojíme je k těmto podsítím.

## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Veřejná IP adresa umožňuje připojení k prostředkům Azure z internetu. Metodu přidělování veřejné IP adresy je možné nakonfigurovat dynamicky nebo staticky. Ve výchozím nastavení se veřejná IP adresa přiděluje dynamicky. Dynamické IP adresy se vydávají při zrušení přidělení virtuálního počítače. Toto chování znamená, že se IP adresa změní při libovolné operaci, která zahrnuje zrušení přidělení virtuálního počítače.

Metodu přidělování je možné nastavit staticky, což zajistí, že IP adresa zůstane virtuálnímu počítači přidělená i ve stavu zrušeného přidělení. Když používáte staticky přidělenou IP adresu, není ji možné určit. Místo toho se přiděluje z fondu dostupných adres.

```azurecli-interactive
az network public-ip create --resource-group myRGNetwork --name myPublicIPAddress
```

Když vytváříte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create), výchozí metoda přidělování veřejné IP adresy je dynamická. Pokud chcete při vytváření virtuálního počítače pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create) přidělit statickou veřejnou IP adresu, použijte argument `--public-ip-address-allocation static`. Tato operace sice není v tomto kurzu ukázaná, ale v další části je ukázaná změna z dynamicky přidělené IP adresy na staticky přidělenou adresu. 

### <a name="change-allocation-method"></a>Změna metody přidělování

Metodu přidělování IP adresy je možné změnit pomocí příkazu [az network public-ip update](/cli/azure/network/public-ip#az_network_public_ip_update). V tomto příkladu se metoda přidělování IP adresy front-endového virtuálního počítače mění na statickou.

Nejprve zrušte přidělení virtuálního počítače.

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontendVM
```

Příkazem [az network public-ip update](/cli/azure/network/public-ip#az_network_public_ip_update) aktualizujte metodu přidělení. V tomto příkladu se parametr `--allocation-method` nastaví na hodnotu *static*.

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myPublicIPAddress --allocation-method static
```

Spusťte virtuální počítač.

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontendVM --no-wait
```

### <a name="no-public-ip-address"></a>Žádná veřejná IP adresa

U virtuálního počítače často není potřeba, aby byl přístupný z internetu. Pokud chcete vytvořit virtuální počítač bez veřejné IP adresy, použijte argument `--public-ip-address ""` s prázdnou sadou dvojitých uvozovek. Ukázku této konfigurace uvidíte později v tomto kurzu.

## <a name="create-a-front-end-vm"></a>Vytvoření front-endového virtuálního počítače

Pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create) a adresy *myPublicIPAddress* vytvořte virtuální počítač s názvem *myFrontendVM*.

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --vnet-name myVNet \
  --subnet myFrontendSubnet \
  --nsg myFrontendNSG \
  --public-ip-address myPublicIPAddress \
  --image UbuntuLTS \
  --generate-ssh-keys
```

## <a name="secure-network-traffic"></a>Zabezpečení provozu sítě

Skupina zabezpečení sítě (NSG) obsahuje seznam pravidel zabezpečení, která prostředkům připojeným k virtuálním sítím Azure povolují nebo odpírají síťový provoz. Skupinu zabezpečení sítě můžete přidružit k podsítím nebo jednotlivým síťovým rozhraním. Pokud skupinu zabezpečení sítě přidružíte k síťovému rozhraní, bude se vztahovat pouze k přidruženému virtuálnímu počítači. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla se vztahují na všechny prostředky, které jsou připojené k příslušné podsíti. 

### <a name="network-security-group-rules"></a>Pravidla skupiny zabezpečení sítě

Pravidla skupiny zabezpečení sítě definují síťové porty, které provoz buď povolují, nebo zakazují. Pravidla mohou zahrnovat zdrojové a cílové rozsahy IP adres, aby se provoz řídil mezi určitými systémy nebo podsítěmi. Pravidla skupiny zabezpečení sítě také obsahují prioritu (1–4096). Pravidla se vyhodnocují v pořadí podle priority. Pravidlo s prioritou 100 se vyhodnotí před pravidlem s prioritou 200.

Všechny skupiny NSG obsahují sadu výchozích pravidel. Výchozí pravidla se nedají odstranit, ale protože je jim přiřazená nejnižší priorita, dají se přepsat pravidly, která vytvoříte.

Výchozí pravidla skupin zabezpečení sítě jsou následující:

- **Virtuální síť** – provoz směřující z virtuální sítě a do ní je povolený v příchozím i odchozím směru.
- **Internet** – odchozí provoz je povolený, ale příchozí provoz se blokuje.
- **Nástroj pro vyrovnávání zatížení** – umožňuje nástroji pro vyrovnávání zatížení Azure testovat stav virtuálních počítačů a instancí rolí. Pokud sadu s vyrovnáváním zatížení nepoužíváte, můžete toto pravidlo přepsat.

### <a name="create-network-security-groups"></a>Vytvoření skupin zabezpečení sítě

Skupinu zabezpečení sítě je možné vytvořit zároveň s virtuálním počítačem pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Když to tak uděláte, skupina zabezpečení sítě se přidruží k síťovému rozhraní virtuálních počítačů a automaticky se vytvoří pravidlo skupiny zabezpečení sítě, které na portu *22* umožní provoz z libovolného zdroje. Dříve v tomto kurzu se společně s front-endovým virtuálním počítačem automaticky vytvořila front-endová skupina zabezpečení sítě. Automaticky se u ní také vytvořilo pravidlo pro port 22. 

V některých případech může být užitečné vytvořit skupinu zabezpečení sítě předem – například když by se neměla vytvářet výchozí pravidla SSH nebo když by se skupina zabezpečení sítě měla připojit k podsíti. 

Skupinu zabezpečení sítě vytvoříte pomocí příkazu [az network nsg create](/cli/azure/network/nsg).

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myBackendNSG
```

Místo přidružení k síťovému rozhraní se přidruží k podsíti. Libovolný virtuální počítač, který je v této konfiguraci přidružený k podsíti, získá pravidla skupiny zabezpečení sítě.

Existující podsíť s názvem *myBackendSubnet* aktualizujte novou skupinou zabezpečení sítě.

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --network-security-group myBackendNSG
```

### <a name="secure-incoming-traffic"></a>Zabezpečení příchozího provozu

V okamžiku vytvoření front-endového virtuálního počítače se vytvořilo pravidlo skupiny zabezpečení sítě, které umožňuje příchozí provoz na portu 22. Toto pravidlo umožňuje připojení SSH k virtuálnímu počítači. V tomto příkladu by měl být provoz umožněn i na portu *80*. Tato konfigurace umožňuje přistupovat k webové aplikaci z virtuálního počítače.

Pomocí příkazu [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) vytvořte pravidlo pro port *80*.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myFrontendNSG \
  --name http \
  --access allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 80
```

Front-endový virtuální počítač je přístupný pouze na portech *22* a *80*. Veškerý ostatní příchozí provoz je skupinou zabezpečení sítě zablokovaný. Někdy může být užitečné si konfigurace pravidla skupiny zabezpečení sítě vizualizovat. Konfiguraci pravidla skupiny zabezpečení sítě vrátíte pomocí příkazu [az network rule list](/cli/azure/network/nsg/rule). 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myFrontendNSG --output table
```

### <a name="secure-vm-to-vm-traffic"></a>Zabezpečení virtuálního počítače vůči provozu virtuálních počítačů

Pravidla skupiny zabezpečení sítě je možné použít i mezi virtuálními počítači. V tomto příkladu potřebuje front-endový virtuální počítač komunikovat s back-endovým virtuálním počítačem na portech *22* a *3306*. Tato konfigurace umožňuje připojení SSH z front-endového virtuálního počítače a také umožňuje aplikaci na front-endovém virtuálním počítači komunikovat s back-endovou databází MySQL. Veškerý ostatní provoz by měl být mezi front-endovými a back-endovými virtuálním počítači blokovaný.

Pomocí příkazu [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) vytvořte pravidlo pro port 22. Všimněte si, že argument `--source-address-prefix` určuje hodnotu *10.0.1.0/24*. Tato konfigurace zajistí, že skrz skupinu zabezpečení sítě projde pouze provoz z front-endové podsítě.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name SSH \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "22"
```

Teď přidejte pravidlo pro provoz databáze MySQL na portu 3306.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name MySQL \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "3306"
```

Vzhledem k tomu, že skupiny zabezpečení sítě mají výchozí pravidlo, které umožňuje veškerý provoz mezi virtuálními počítači v rámci stejné virtuální sítě, můžete nakonec vytvořit pravidlo pro back-endové skupiny zabezpečení sítě, které zablokuje veškerý provoz. Všimněte si, že parametr `--priority` má hodnotu *300*, která je nižší než pravidla skupiny zabezpečení sítě i databáze MySQL. Tato konfigurace zajistí, že provoz SSH a MySQL bude nadále skrz skupinu zabezpečení sítě možný.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name denyAll \
  --access Deny \
  --protocol Tcp \
  --direction Inbound \
  --priority 300 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "*"
```

## <a name="create-back-end-vm"></a>Vytvoření back-endového virtuálního počítače

Nyní vytvořte virtuální počítač, který je připojený k podsíti *myBackendSubnet*. Všimněte si, že argument `--nsg` má hodnotu prázdných dvojitých uvozovek. Skupinu zabezpečení sítě není potřeba s tímto virtuálním počítačem vytvářet. Tento virtuální počítač je připojený k back-endové podsíti, která je chráněná dříve vytvořenou back-endovou skupinou zabezpečení sítě. Tato skupina zabezpečení sítě se vztahuje i na tento virtuální počítač. Všimněte si také, že i argument `--public-ip-address` má hodnotu prázdných dvojitých uvozovek. Tato konfigurace vytvoří virtuální počítač bez veřejné IP adresy. 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Back-endový virtuální počítač je přístupný pouze na portech *22* a *3306* z front-endové podsítě. Veškerý ostatní příchozí provoz je skupinou zabezpečení sítě zablokovaný. Někdy může být užitečné si konfigurace pravidla skupiny zabezpečení sítě vizualizovat. Konfiguraci pravidla skupiny zabezpečení sítě vrátíte pomocí příkazu [az network rule list](/cli/azure/network/nsg/rule). 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myBackendNSG --output table
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste v souvislosti s virtuálními počítači vytvořili a zabezpečené sítě Azure. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření virtuální sítě a podsítě
> * Vytvoření veřejné IP adresy
> * Vytvoření front-endového virtuálního počítače
> * Zabezpečení provozu sítě
> * Vytvoření back-endového virtuálního počítače

Přejděte na další kurs, abyste se dozvěděli o zabezpečení dat na virtuálních počítačích pomocí služby Azure Backup. 

> [!div class="nextstepaction"]
> [Záloha virtuálních počítačů s Linuxem v Azure](./tutorial-backup-vms.md)
