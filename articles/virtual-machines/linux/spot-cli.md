---
title: Použití rozhraní příkazového řádku k nasazení virtuálních počítačů Azure na místě (Preview)
description: Naučte se používat rozhraní příkazového řádku k nasazení virtuálních počítačů Azure na místě, abyste ušetřili náklady.
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
ms.date: 11/20/2019
ms.author: cynthn
ms.openlocfilehash: 0635be14937a3688792f65208dcb9d482b9e6d44
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781992"
---
# <a name="preview-deploy-spot-vms-using-the-azure-cli"></a>Verze Preview: nasazení virtuálních počítačů s virtuálními počítači pomocí rozhraní příkazového řádku Azure

Použití [virtuálních počítačů Azure](spot-vms.md) se dá využít při využití naší nevyužité kapacity při výrazném úsporách nákladů. V jakémkoli okamžiku, kdy Azure potřebuje kapacitu zpátky, vyřadí infrastruktura Azure virtuální počítače na místě. Proto jsou virtuální počítače Skvělé pro úlohy, které mohou zpracovávat přerušení, jako jsou úlohy dávkového zpracování, vývojové a testovací prostředí, velké výpočetní úlohy a další.

Ceny pro virtuální počítače na místě jsou proměnné na základě oblastí a SKU. Další informace najdete v tématu ceny virtuálních počítačů pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Máte možnost nastavit maximální cenu, kterou jste ochotni zaplatit za hodinu pro virtuální počítač. Maximální cena za virtuální počítač na místě se dá nastavit v amerických dolarech (USD), a to s využitím až 5 desetinných míst. Například hodnota `0.98765`by byla maximální cena $0,98765 USD za hodinu. Pokud nastavíte maximální cenu, která se má `-1`, virtuální počítač se nevyřadí na základě ceny. Cena za virtuální počítač bude aktuální cena za bod nebo cena za standardní virtuální počítač, který je stále menší, pokud je dostupná kapacita a kvóta. Další informace o nastavení maximální ceny najdete v tématu [virtuální počítače – ceny](spot-vms.md#pricing).

Postup vytvoření virtuálního počítače s využitím rozhraní příkazového řádku Azure CLI je stejný, jak je uvedeno v [článku rychlý Start](/azure/virtual-machines/linux/quick-create-cli). Stačí přidat parametr--priority a zadat Max Price nebo `-1`.

> [!IMPORTANT]
> Instance přímých instancí jsou momentálně ve verzi Public Preview.
> Tato verze Preview se nedoporučuje pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> V první části verze Public Preview budou mít instance pro tisk pevnou cenu, takže se neúčtují žádné vyřazení na základě cen.


## <a name="install-azure-cli"></a>Instalace Azure CLI

Pokud chcete vytvořit virtuální počítače se systémem, musíte mít spuštěnou verzi Azure CLI 2.0.74 nebo novější. Verzi zjistíte spuštěním příkazu **az --version**. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

Přihlaste se k Azure pomocí [AZ Login](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Vytvoření virtuálního počítače s přímým sebou

Tento příklad ukazuje, jak nasadit virtuální počítač se systémem Linux, který nebude vyřazení na základě ceny. 

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

Po vytvoření virtuálního počítače se můžete dotazovat, aby se zobrazila maximální fakturovaná cena za všechny virtuální počítače ve skupině prostředků.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**Další kroky**

Pomocí [Azure PowerShell](../windows/spot-powershell.md) nebo [šablony](spot-template.md)můžete také vytvořit virtuální počítač s přímým použitím.

Pokud dojde k chybě, přečtěte si [kódy chyb](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
