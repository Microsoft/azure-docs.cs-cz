---
title: Kurz vysoké dostupnosti pro virtuální počítače s Linuxem v Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak pomocí Azure CLI nasazovat vysoce dostupné virtuální počítače ve skupinách dostupnosti.
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: gwallace
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
ms.openlocfilehash: 718f2e3391fe89bcc64426c37401f9bf91643201
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641136"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-the-azure-cli"></a>Kurz: Vytvoření a nasazení virtuálních počítačů s vysokou dostupností pomocí Azure CLI

V tomto kurzu zjistíte, jak zvýšit dostupnost a spolehlivost svých řešení, která využívají virtuální počítače v Azure, pomocí funkce označované jako skupiny dostupnosti. Skupiny dostupnosti zajišťují distribuci virtuálních počítačů nasazených v Azure napříč několika izolovanými hardwarovými clustery. To zajišťuje, že pokud dojde k selhání hardwaru nebo softwaru v rámci Azure, ovlivní to pouze podmnožinu vašich virtuálních počítačů a vaše celkové řešení zůstane dostupné a funkční.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny dostupnosti
> * Vytvoření virtuálního počítače ve skupině dostupnosti
> * Kontrola dostupných velikostí virtuálních počítačů

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte pro tento kurz mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="high-availability-in-azure-overview"></a>Přehled vysoké dostupnosti v Azure
Vysokou dostupnost v Azure je možné vytvořit mnoha různými způsoby. K dispozici jsou dvě možnosti nastavení dostupnosti a zóny dostupnosti. Pomocí skupin dostupnosti budou vaše virtuální počítače chráněny před chybami, ke kterým může dojít v rámci datového centra. To zahrnuje selhání hardwaru a chyby softwaru Azure. Pomocí zón dostupnosti budou vaše virtuální počítače umístěné na fyzicky samostatné infrastruktuře bez sdílených prostředků a budou se proto chránit před celými selháními Datacenter.

Pokud chcete v Azure nasadit spolehlivé řešení založená na virtuálních počítačích, použijte skupiny dostupnosti nebo Zóny dostupnosti.

### <a name="availability-set-overview"></a>Přehled skupiny dostupnosti

Skupina dostupnosti je funkce logického seskupení, pomocí které můžete v Azure zajistit, že prostředky virtuálních počítačů, které do ní umístíte, jsou při nasazení v datacentru Azure od sebe navzájem izolované. Azure zajišťuje, že virtuální počítače, které umístíte do skupiny dostupnosti, se budou spouštět napříč několika fyzickými servery, výpočetními racky, jednotkami úložiště a síťovými přepínači. Pokud dojde k selhání hardwaru nebo softwaru Azure, ovlivní to pouze dílčí část vašich virtuálních počítačů a váš výpočetní systém zůstane v provozu a bude pro zákazníky dál dostupný. Skupiny dostupnosti představují základní schopnost při sestavování spolehlivých cloudových řešení.

Zvažte typické řešení založené na virtuálních počítačích, kdy máte čtyři front-end webové servery a používáte dva back-end virtuální počítače hostující databázi. V případě Azure byste před nasazením virtuálních počítačů měli definovat dvě skupiny dostupnosti: jednu skupinu dostupnosti pro webovou vrstvu a jednu skupinu dostupnosti pro databázovou vrstvu. Při vytváření nového virtuálního počítače pak můžete zadat skupinu dostupnosti jako parametr příkazu az vm create a Azure automaticky zajistí izolaci virtuálních počítačů vytvořených v rámci skupiny dostupnosti napříč více fyzickými hardwarovými prostředky. Pokud dojde k problému s fyzickým hardwarem, na kterém běží virtuální počítače s webovým nebo databázovým serverem, máte jistotu, že ostatní instance virtuálních počítačů s webovým serverem a databází zůstanou spuštěné, protože jsou na jiném hardwaru.

### <a name="availability-zone-overview"></a>Přehled zóny dostupnosti

Zóny dostupnosti je nabídka s vysokou dostupností, která chrání vaše aplikace a data při selhání datacentra. Zóny dostupnosti jsou jedinečná fyzická umístění v rámci oblasti Azure. Každá zóna se skládá z jednoho nebo více datových Center vybavených nezávislým napájením, chlazením a sítí. Aby se zajistila odolnost, existuje minimálně tři samostatné zóny ve všech povolených oblastech. Fyzické oddělení Zóny dostupnosti v rámci oblasti chrání aplikace a data před selháními datových center. Redundantní služby v zóně replikují aplikace a data napříč Zóny dostupnosti, aby se chránily před jednotlivými chybami. Díky Zóny dostupnosti Azure nabízí smlouvu SLA pro nejlepší 99,99% dostupnost virtuálního počítače.

Podobně jako u skupin dostupnosti Uvažujme o typickém řešení založeném na virtuálním počítači, kde můžete mít čtyři přední webové servery a používat dva back-endové virtuální počítače, které hostují databázi. Podobně jako u skupin dostupnosti budete chtít nasadit virtuální počítače ve dvou samostatných zónách dostupnosti: jednu zónu dostupnosti pro webovou vrstvu a jednu zónu dostupnosti pro úroveň databáze. Když vytvoříte nový virtuální počítač a zadáte zónu dostupnosti jako parametr do příkazu AZ VM Create, Azure automaticky zajistí, že virtuální počítače, které vytvoříte, budou izolované v rámci zcela odlišných zón dostupnosti. Pokud se v celém datovém centru, na kterém je spuštěný některý z virtuálních počítačů webového serveru nebo databázového serveru, nachází nějaký problém, znamená to, že ostatní instance virtuálních počítačů s webovým serverem a databází zůstanou spuštěné, protože jsou spuštěné v zcela odděleném datacentru.

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

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření skupiny dostupnosti
> * Vytvoření virtuálního počítače ve skupině dostupnosti
> * Kontrola dostupných velikostí virtuálních počítačů

Přejděte k dalšímu kurzu, kde se seznámíte se škálovacími sadami virtuálních počítačů.

> [!div class="nextstepaction"]
> [Vytvoření škálovací sady virtuálních počítačů](tutorial-create-vmss.md)

* Další informace o zónách dostupnosti najdete v dokumentaci k [zóny dostupnosti](../../availability-zones/az-overview.md).
* Další dokumentace k dispozici pro obě skupiny dostupnosti i zóny dostupnosti jsou k dispozici také [zde](./manage-availability.md).
* Pokud si chcete vyzkoušet zóny dostupnosti, přejděte na téma [Vytvoření virtuálního počítače se systémem Linux v zóně dostupnosti pomocí Azure CLI](./create-cli-availability-zone.md) .
