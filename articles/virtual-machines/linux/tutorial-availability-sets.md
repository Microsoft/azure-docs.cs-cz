---
title: Kurz vysoké dostupnosti pro virtuální počítače s Linuxem v Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak používat Azure CLI 2.0 k nasazení vysoce dostupných virtuálních počítačů ve skupinách dostupnosti.
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 08/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a31ca4dc326eb6ada37813cacd8bb1dc337c7fdc
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43044238"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-the-azure-cli-20"></a>Kurz: Vytvoření a nasazení vysoce dostupných virtuálních počítačů pomocí Azure CLI 2.0

V tomto kurzu zjistíte, jak zvýšit dostupnost a spolehlivost svých řešení, která využívají virtuální počítače v Azure, pomocí funkce označované jako skupiny dostupnosti. Skupiny dostupnosti zajišťují distribuci virtuálních počítačů nasazených v Azure napříč několika izolovanými hardwarovými clustery. To zajišťuje, že pokud dojde k selhání hardwaru nebo softwaru v rámci Azure, ovlivní to pouze podmnožinu vašich virtuálních počítačů a vaše celkové řešení zůstane dostupné a funkční.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny dostupnosti
> * Vytvoření virtuálního počítače ve skupině dostupnosti
> * Kontrola dostupných velikostí virtuálních počítačů

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte pro tento kurz mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="availability-set-overview"></a>Přehled skupiny dostupnosti

Skupina dostupnosti je funkce logického seskupení, pomocí které můžete v Azure zajistit, že prostředky virtuálních počítačů, které do ní umístíte, jsou při nasazení v datacentru Azure od sebe navzájem izolované. Azure zajišťuje, že virtuální počítače, které umístíte do skupiny dostupnosti, se budou spouštět napříč několika fyzickými servery, výpočetními racky, jednotkami úložiště a síťovými přepínači. Pokud dojde k selhání hardwaru nebo softwaru Azure, ovlivní to pouze dílčí část vašich virtuálních počítačů a váš výpočetní systém zůstane v provozu a bude pro zákazníky dál dostupný. Skupiny dostupnosti představují základní schopnost při sestavování spolehlivých cloudových řešení.

Zvažte typické řešení založené na virtuálních počítačích, kdy máte čtyři front-end webové servery a používáte dva back-end virtuální počítače hostující databázi. V případě Azure byste před nasazením virtuálních počítačů měli definovat dvě skupiny dostupnosti: jednu skupinu dostupnosti pro webovou vrstvu a jednu skupinu dostupnosti pro databázovou vrstvu. Při vytváření nového virtuálního počítače pak můžete zadat skupinu dostupnosti jako parametr příkazu az vm create a Azure automaticky zajistí izolaci virtuálních počítačů vytvořených v rámci skupiny dostupnosti napříč více fyzickými hardwarovými prostředky. Pokud dojde k problému s fyzickým hardwarem, na kterém běží virtuální počítače s webovým nebo databázovým serverem, máte jistotu, že ostatní instance virtuálních počítačů s webovým serverem a databází zůstanou spuštěné, protože jsou na jiném hardwaru.

Skupiny dostupnosti použijte v případě, že chcete v Azure nasazovat spolehlivá řešení založená na virtuálních počítačích.


## <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti

Skupinu dostupnosti můžete vytvořit pomocí příkazu [az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create). V tomto příkladu nastavíme počet aktualizačních domén a domén selhání na *2* pro skupinu dostupnosti *myAvailabilitySet* ve skupině prostředků *myResourceGroupAvailability*.

Nejprve vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create) a pak vytvořte skupinu dostupnosti:

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Skupiny dostupnosti umožňují izolovat prostředky napříč doménami selhání a aktualizačními doménami. **Doména selhání** představuje izolovanou kolekci prostředků serveru, sítě a úložiště. V předchozím příkladu je po nasazení virtuálních počítačů skupina dostupnosti distribuována napříč alespoň dvěma doménami selhání. Skupina dostupnosti je také distribuována napříč dvěma **aktualizačními doménami**. Dvě aktualizační domény zajistí izolaci prostředků virtuálních počítačů při tom, když Azure provádí aktualizace softwaru, a tím zabrání současné aktualizaci veškerého softwaru spuštěného na virtuálních počítačích.


## <a name="create-vms-inside-an-availability-set"></a>Vytvoření virtuálních počítačů ve skupině dostupnosti

Virtuální počítače je nutné vytvořit ve skupině dostupnosti, abyste měli jistotu, že jsou správně distribuované napříč hardwarem. Existující virtuální počítač není možné přidat do vytvořené skupiny dostupnosti.

Při vytvoření virtuálního počítače pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create) použijte parametr `--availability-set` k zadání názvu skupiny dostupnosti.

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --vnet-name myVnet \
     --subnet mySubnet \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys
done
```

V rámci sady dostupnosti nyní existují dva virtuální počítače. Vzhledem k tomu, že jsou ve stejné skupině dostupnosti, Azure zajistí distribuci virtuálních počítačů a všech jejich prostředků (včetně datových disků) napříč izolovaným fyzickým hardwarem. Tato distribuce pomáhá zajistit mnohem vyšší dostupnost celkového řešení využívajícího virtuální počítače.

Distribuci sadu dostupnosti lze zobrazit na portálu tak, že přejdete na Skupiny prostředků > myResourceGroupAvailability > myAvailabilitySet. Virtuální počítače jsou distribuované ve dvou doménách selhání a aktualizačních doménách, jak je znázorněno v následujícím příkladu:

![Skupina dostupnosti na portálu](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Kontrola dostupných velikostí virtuálních počítačů

Další virtuální počítače lze přidat do skupiny dostupnosti později, pokud jsou na hardwaru k dispozici velikosti virtuálních počítačů. Pomocí příkazu [az vm availability-set list-sizes](/cli/azure/vm/availability-set#az-vm-availability-set-list-sizes) vypíšete všechny dostupné velikosti na hardwarovém clusteru pro skupinu dostupnosti:

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření skupiny dostupnosti
> * Vytvoření virtuálního počítače ve skupině dostupnosti
> * Kontrola dostupných velikostí virtuálních počítačů

Přejděte k dalšímu kurzu, kde se seznámíte se škálovacími sadami virtuálních počítačů.

> [!div class="nextstepaction"]
> [Vytvoření škálovací sady virtuálních počítačů](tutorial-create-vmss.md)
