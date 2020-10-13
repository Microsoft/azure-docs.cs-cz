---
title: Nasazení virtuálních počítačů Azure na místě pomocí portálu
description: Jak používat Azure PowerShell k nasazení virtuálních počítačů na místě, aby se ušetřily náklady.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 0da650646c35a9a663dd29589f963d23cbe552cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828408"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Nasazení virtuálních počítačů na místě pomocí Azure Portal

Použití [přímých virtuálních počítačů](spot-vms.md) vám umožní využít výhod naší nevyužité kapacity s významnou úsporou nákladů. V jakémkoli okamžiku, kdy Azure potřebuje kapacitu zpátky, vyřadí infrastruktura Azure virtuální počítače na místě. Proto jsou virtuální počítače Skvělé pro úlohy, které mohou zpracovávat přerušení, jako jsou úlohy dávkového zpracování, vývojové a testovací prostředí, velké výpočetní úlohy a další.

Ceny pro virtuální počítače na místě jsou proměnné na základě oblastí a SKU. Další informace najdete v tématu ceny virtuálních počítačů pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Další informace o nastavení maximální ceny najdete v tématu [virtuální počítače – ceny](spot-vms.md#pricing).

Máte možnost nastavit maximální cenu, kterou jste ochotni zaplatit za hodinu pro virtuální počítač. Maximální cena za virtuální počítač na místě se dá nastavit v amerických dolarech (USD), a to s využitím až 5 desetinných míst. Hodnota by měla být například `0.05701` maximální cena $0,05701 USD za hodinu. Pokud nastavíte maximální cenu `-1` , nebude se virtuální počítač vyřadit podle ceny. Cena za virtuální počítač bude aktuální cena za bod nebo cena za standardní virtuální počítač, který je stále menší, pokud je dostupná kapacita a kvóta.

Když je virtuální počítač vyřazený, máte možnost odstranit virtuální počítač a příslušný disk nebo zrušit přidělení virtuálního počítače, aby se mohl později restartovat.


## <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Při nasazení virtuálního počítače se můžete rozhodnout použít instanci bodu Azure.


Na kartě **základy** se v části **Podrobnosti instance** **nejedná o** výchozí nastavení pro použití instance bodu Azure.

![Snímek obrazovky pro výběr ne, nepoužívat instanci bodu Azure](./media/spot-portal/no.png)

Pokud vyberete **Ano**, oddíl se rozšíří a můžete zvolit [Typ vyřazení a zásadu vyřazení](spot-vms.md#eviction-policy). 

![Snímek obrazovky pro výběr Ano, použití instance bodu Azure](./media/spot-portal/yes.png)

Můžete také porovnat ceny a tarify vyřazení s ostatními podobnými oblastmi výběrem možnosti **Zobrazit historii cen a porovnat ceny v přilehlých oblastech**.

V tomto příkladu je centrální oblast Kanady levnější a má nižší sazbu vyřazení než Východní USA oblast.

:::image type="content" source="./media/spot-portal/pricing.png" alt-text="Snímek obrazovky s možnostmi oblastí s rozdílem v cenách a sazbách vyřazení":::

Oblast můžete změnit výběrem možnosti, která pro vás nejlépe vyhovuje, a pak vyberte **OK**.

## <a name="simulate-an-eviction"></a>Simulace vyřazení

Můžete [simulovat vyřazení](/rest/api/compute/virtualmachines/simulateeviction) virtuálních počítačů s virtuálním počítačem, abyste mohli otestovat, jak dobře bude vaše aplikace schopná vyřadit do náhlého vyřazení. 

Pro vaše informace nahraďte následující údaje: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

## <a name="next-steps"></a>Další kroky

Virtuální počítače s přímým použitím můžete vytvořit také pomocí [PowerShellu](./windows/spot-powershell.md), [CLI](./linux/spot-cli.md)nebo [šablony](./linux/spot-template.md).
