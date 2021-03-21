---
title: Nasazení virtuálních počítačů ve skupině dostupnosti pomocí Azure CLI
description: V tomto kurzu zjistíte, jak pomocí Azure CLI nasazovat vysoce dostupné virtuální počítače ve skupinách dostupnosti.
documentationcenter: ''
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 6a54e0d808ef734a26a0fa309bd7367e73316856
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507061"
---
# <a name="create-and-deploy-virtual-machines-in-an-availability-set-using-azure-cli"></a>Vytvoření a nasazení virtuálních počítačů ve skupině dostupnosti pomocí Azure CLI

V tomto kurzu zjistíte, jak zvýšit dostupnost a spolehlivost svých řešení využívajících virtuální počítače v Azure pomocí schopnosti označované jako skupiny dostupnosti. Skupiny dostupnosti zajišťují distribuci virtuálních počítačů nasazených v Azure napříč několika izolovanými hardwarovými clustery. To zajišťuje, že pokud dojde k selhání hardwaru nebo softwaru v rámci Azure, ovlivní to pouze podmnožinu vašich virtuálních počítačů a vaše celkové řešení zůstane dostupné a funkční.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny dostupnosti
> * Vytvoření virtuálního počítače ve skupině dostupnosti
> * Kontrola dostupných velikostí virtuálních počítačů

V tomto kurzu se používá CLI v rámci [Azure Cloud Shell](../../cloud-shell/overview.md), který se průběžně aktualizuje na nejnovější verzi. Chcete-li otevřít Cloud Shell, vyberte možnost **vyzkoušet** v horní části libovolného bloku kódu.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti

Skupinu dostupnosti můžete vytvořit pomocí příkazu [az vm availability-set create](/cli/azure/vm/availability-set). V tomto příkladu nastavíme počet aktualizačních domén a domén selhání na *2* pro skupinu dostupnosti *myAvailabilitySet* ve skupině prostředků *myResourceGroupAvailability*.

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

Při vytvoření virtuálního počítače pomocí příkazu [az vm create](/cli/azure/vm) použijte parametr `--availability-set` k zadání názvu skupiny dostupnosti.

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

* Další informace o zónách dostupnosti najdete v dokumentaci k  [zóny dostupnosti](../../availability-zones/az-overview.md).
* Další dokumentace k dispozici pro obě skupiny dostupnosti i zóny dostupnosti jsou k dispozici také [zde](../availability.md).
* Pokud si chcete vyzkoušet zóny dostupnosti, přejděte na téma [Vytvoření virtuálního počítače se systémem Linux v zóně dostupnosti pomocí Azure CLI](./create-cli-availability-zone.md) .