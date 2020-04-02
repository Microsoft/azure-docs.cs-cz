---
title: Nasazení virtuálních virtuálních počítačů Azure Spot pomocí příkazového příkazového příkazu
description: Zjistěte, jak pomocí příkazového příkazového příkazu nasadit virtuální počítače Azure Spot, abyste ušetřili náklady.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 5b6a07bfbcf56f3ca78fa4991e7741a3d44c25b9
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80544357"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>Nasazení virtuálních počítačů spotpomocí příkazového příkazového příkazu Azure

Použití [virtuálních počítačů Azure Spot](spot-vms.md) vám umožní využít naši nevyužitou kapacitu s výraznými úsporami nákladů. Kdykoli v okamžiku, kdy Azure potřebuje kapacitu zpět, infrastruktura Azure vystěhovává virtuální počítače Spot. Virtuální počítače Spot jsou proto skvělé pro úlohy, které zvládnou přerušení, jako jsou úlohy dávkového zpracování, vývojová a testovací prostředí, velké výpočetní úlohy a další.

Ceny pro spotové virtuální počítače jsou variabilní na základě oblasti a skladové položky. Další informace najdete v tématu Ceny virtuálních počítačů pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Máte možnost nastavit maximální cenu, kterou jste ochotni zaplatit za hodinu za virtuální hod. Maximální cenu pro spotový virtuální virtuální mísu lze nastavit v amerických dolarech (USD) s použitím až 5 desetinných míst. Například hodnota `0.98765`by byla maximální cena $0.98765 USD za hodinu. Pokud nastavíte maximální `-1`cenu , virtuální počítač nebude vystěhován na základě ceny. Cena za virtuální ho virtuálního času bude aktuální cena spotu nebo cena za standardní virtuální ms, která je vždy nižší, pokud je k dispozici kapacita a kvóta. Další informace o nastavení maximální ceny najdete v tématu [Spot Virtuální virtuální chod – ceny](spot-vms.md#pricing).

Proces vytvoření virtuálního počítače s bodem pomocí azure cli je stejný jako podrobné v [článku rychlého startu](/azure/virtual-machines/linux/quick-create-cli). Stačí přidat parametr '--priority Spot' a `-1`poskytnout maximální cenu nebo .


## <a name="install-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI

Chcete-li vytvořit virtuální počítače Spot, musíte spustit Azure CLI verze 2.0.74 nebo novější. Verzi zjistíte spuštěním příkazu **az --version**. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). 

Přihlaste se k Azure pomocí [az přihlášení](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Vytvoření virtuálního virtuálního virtuálního mísy spot

Tento příklad ukazuje, jak nasadit virtuální počítač S ním spot linuxu, který nebude vyřazen na základě ceny. 

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1
```

Po vytvoření virtuálního virtuálního účtu můžete dotaz zobrazí maximální fakturační cenu pro všechny virtuální chody ve skupině prostředků.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**Další kroky**

Virtuální počítač Spot můžete taky vytvořit pomocí [Azure PowerShellu](../windows/spot-powershell.md) nebo [šablony](spot-template.md).

Pokud narazíte na chybu, přečtěte si informace [o chybových kódech](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
