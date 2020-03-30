---
title: Nasazení virtuálních počítačů Azure Spot (preview) pomocí příkazového příkazového příkazu
description: Zjistěte, jak pomocí příkazového příkazového příkazu nasadit virtuální počítače Azure Spot, abyste ušetřili náklady.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 110e935671ab1d640b2ff3dc26c203b262e999fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77163088"
---
# <a name="preview-deploy-spot-vms-using-the-azure-cli"></a>Předběžná verze: Nasazení virtuálních počítačích Spot pomocí azure CLI

Použití [virtuálních počítačů Azure Spot](spot-vms.md) vám umožní využít naši nevyužitou kapacitu s výraznými úsporami nákladů. Kdykoli v okamžiku, kdy Azure potřebuje kapacitu zpět, infrastruktura Azure vystěhovává virtuální počítače Spot. Virtuální počítače Spot jsou proto skvělé pro úlohy, které zvládnou přerušení, jako jsou úlohy dávkového zpracování, vývojová a testovací prostředí, velké výpočetní úlohy a další.

Ceny pro spotové virtuální počítače jsou variabilní na základě oblasti a skladové položky. Další informace najdete v tématu Ceny virtuálních počítačů pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Máte možnost nastavit maximální cenu, kterou jste ochotni zaplatit za hodinu za virtuální hod. Maximální cenu pro spotový virtuální virtuální mísu lze nastavit v amerických dolarech (USD) s použitím až 5 desetinných míst. Například hodnota `0.98765`by byla maximální cena $0.98765 USD za hodinu. Pokud nastavíte maximální `-1`cenu , virtuální počítač nebude vystěhován na základě ceny. Cena za virtuální ho virtuálního času bude aktuální cena spotu nebo cena za standardní virtuální ms, která je vždy nižší, pokud je k dispozici kapacita a kvóta. Další informace o nastavení maximální ceny najdete v tématu [Spot Virtuální virtuální chod – ceny](spot-vms.md#pricing).

Proces vytvoření virtuálního počítače s bodem pomocí azure cli je stejný jako podrobné v [článku rychlého startu](/azure/virtual-machines/linux/quick-create-cli). Stačí přidat parametr '--priority Spot' a `-1`poskytnout maximální cenu nebo .

> [!IMPORTANT]
> Instance spotů jsou aktuálně ve verzi Public Preview.
> Tato verze preview se nedoporučuje pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>



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
