---
title: Nasazení služby Azure spot Virtual Machines pomocí portálu
description: Jak použít Azure PowerShell k nasazení Virtual Machines na místě, aby se ušetřily náklady.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 879a3e9b3d3f651a1dea17e76dba503cd2816b9e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098566"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-portal"></a>Nasazení služby Azure spot Virtual Machines pomocí Azure Portal

Použití [Azure Spot Virtual Machines](spot-vms.md) umožňuje využít výhod naší nevyužité kapacity s významnou úsporou nákladů. V jakémkoli okamžiku, kdy Azure potřebuje kapacitu zpátky, vyřadí infrastruktura Azure místo Virtual Machines Azure. Proto jsou Azure spot Virtual Machines Skvělé pro úlohy, které mohou zpracovávat přerušení, jako jsou úlohy dávkového zpracování, vývojové a testovací prostředí, velké výpočetní úlohy a další.

Ceny za Azure na místě Virtual Machines jsou proměnné na základě oblastí a SKU. Další informace najdete v tématu ceny virtuálních počítačů pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Další informace o nastavení maximální ceny najdete v tématu [Azure Spot Virtual Machines – ceny](spot-vms.md#pricing).

Máte možnost nastavit maximální cenu, kterou jste ochotni zaplatit za hodinu pro virtuální počítač. Maximální cena pro virtuální počítač se službou Azure se dá nastavit v amerických dolarech (USD), a to s využitím až 5 desetinných míst. Hodnota by měla být například `0.05701` maximální cena $0,05701 USD za hodinu. Pokud nastavíte maximální cenu `-1` , nebude se virtuální počítač vyřadit podle ceny. Cena za virtuální počítač bude aktuální cena za bod nebo cena za standardní virtuální počítač, který je stále menší, pokud je dostupná kapacita a kvóta.

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

Můžete [simulovat vyřazení](/rest/api/compute/virtualmachines/simulateeviction) virtuálních počítačů se systémem Azure na místě, abyste mohli otestovat, jak dobře bude aplikace reagovat na náhlé vyřazení. 

Pro vaše informace nahraďte následující údaje: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` znamená, že simulované vyřazení bylo úspěšné. 

## <a name="next-steps"></a>Další kroky

Pomocí [PowerShellu](./windows/spot-powershell.md), rozhraní příkazového [řádku](./linux/spot-cli.md)nebo [šablony](./linux/spot-template.md)můžete také vytvořit službu Azure spot Virtual Machines.
