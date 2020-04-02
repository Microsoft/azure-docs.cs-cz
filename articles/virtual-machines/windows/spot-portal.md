---
title: Nasazení virtuálních virtuálních počítačů Azure Spot pomocí portálu
description: Zjistěte, jak pomocí Azure PowerShellu nasadit virtuální počítače Spot, abyste ušetřili náklady.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 045cec080b9b1b8f2e4cb589b053c421897db5be
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547387"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Nasazení virtuálních počítačů spotů pomocí portálu Azure

Použití [spotových virtuálních měn](spot-vms.md) vám umožní využít naši nevyužitou kapacitu s výraznými úsporami nákladů. Kdykoli v okamžiku, kdy Azure potřebuje kapacitu zpět, infrastruktura Azure vystěhovává virtuální počítače Spot. Virtuální počítače Spot jsou proto skvělé pro úlohy, které zvládnou přerušení, jako jsou úlohy dávkového zpracování, vývojová a testovací prostředí, velké výpočetní úlohy a další.

Ceny pro spotové virtuální počítače jsou variabilní na základě oblasti a skladové položky. Další informace najdete v tématu Ceny virtuálních počítačů pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Další informace o nastavení maximální ceny najdete v tématu [Spot Virtuální virtuální chod – ceny](spot-vms.md#pricing).

Máte možnost nastavit maximální cenu, kterou jste ochotni zaplatit za hodinu za virtuální hod. Maximální cenu pro spotový virtuální virtuální mísu lze nastavit v amerických dolarech (USD) s použitím až 5 desetinných míst. Například hodnota `0.05701`by byla maximální cena $0.05701 USD za hodinu. Pokud nastavíte maximální `-1`cenu , virtuální počítač nebude vystěhován na základě ceny. Cena za virtuální ho virtuálního mítna bude aktuální cena za spot nebo cena za standardní virtuální ms, který je vždy menší, pokud je k dispozici kapacita a kvóta.


## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Proces vytvoření virtuálního virtuálního ms, který používá virtuální chod Spot, je stejný jako podrobný [postup na úvodním startu](quick-create-portal.md). Když nasazujete virtuální počítač, můžete použít instanci Azure spot.


Na kartě **Základy** v části **Podrobnosti instance** je **ne** výchozí pro použití instance Azure spot.

![Snímání obrazovky pro výběr ne, nepoužívejte instanci webu Azure spot](media/spot-portal/no.png)

Zvolíte-li **možnost Ano**, oddíl se rozbalí a můžete zvolit [typ vyřazovacího a vyřazovacího příkazu](spot-vms.md#eviction-policy). 

![Snímání obrazovky pro výběr ano, použijte instanci azure spot](media/spot-portal/yes.png)


## <a name="next-steps"></a>Další kroky

Virtuální virtuální aplikace Spot můžete také vytvořit pomocí [prostředí PowerShell](spot-powershell.md).