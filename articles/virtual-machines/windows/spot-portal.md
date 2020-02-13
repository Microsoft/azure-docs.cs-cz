---
title: Nasazení virtuálních počítačů Azure na místě pomocí portálu
description: Naučte se používat Azure PowerShell k nasazení virtuálních počítačů na místě za účelem úspory nákladů.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 8845d0f9277384c1cc32b31b5ea5151cd0668c79
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77158974"
---
# <a name="preview-deploy-spot-vms-using-the-azure-portal"></a>Verze Preview: nasazení virtuálních počítačů s virtuálními počítači pomocí Azure Portal

Použití [přímých virtuálních počítačů](spot-vms.md) vám umožní využít výhod naší nevyužité kapacity s významnou úsporou nákladů. V jakémkoli okamžiku, kdy Azure potřebuje kapacitu zpátky, vyřadí infrastruktura Azure virtuální počítače na místě. Proto jsou virtuální počítače Skvělé pro úlohy, které mohou zpracovávat přerušení, jako jsou úlohy dávkového zpracování, vývojové a testovací prostředí, velké výpočetní úlohy a další.

Ceny pro virtuální počítače na místě jsou proměnné na základě oblastí a SKU. Další informace najdete v tématu ceny virtuálních počítačů pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Další informace o nastavení maximální ceny najdete v tématu [virtuální počítače – ceny](spot-vms.md#pricing).

Máte možnost nastavit maximální cenu, kterou jste ochotni zaplatit za hodinu pro virtuální počítač. Maximální cena za virtuální počítač na místě se dá nastavit v amerických dolarech (USD), a to s využitím až 5 desetinných míst. Například hodnota `0.05701`by byla maximální cena $0,05701 USD za hodinu. Pokud nastavíte maximální cenu, která se má `-1`, virtuální počítač se nevyřadí na základě ceny. Cena za virtuální počítač bude aktuální cena za bod nebo cena za standardní virtuální počítač, který je stále menší, pokud je dostupná kapacita a kvóta.

> [!IMPORTANT]
> Instance přímých instancí jsou momentálně ve verzi Public Preview.
> Tato verze Preview se nedoporučuje pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>


## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Postup vytvoření virtuálního počítače, který používá bodové virtuální počítače, je stejný jako podrobný průvodce [rychlým startem](quick-create-portal.md). Při nasazení virtuálního počítače se můžete rozhodnout použít instanci bodu Azure.


Na kartě **základy** se v části **Podrobnosti instance** **nejedná o** výchozí nastavení pro použití instance bodu Azure.

![Snímek obrazovky pro výběr ne, nepoužívat instanci bodu Azure](media/spot-portal/no.png)

Vyberete-li možnost **Ano**, oddíl se rozšíří a můžete zvolit [Typ vyřazení a zásadu vyřazení](spot-vms.md#eviction-policy). 

![Snímek obrazovky pro výběr Ano, použití instance bodu Azure](media/spot-portal/yes.png)


## <a name="next-steps"></a>Další kroky

Virtuální počítače s přímým použitím [prostředí PowerShell](spot-powershell.md)můžete také vytvořit.