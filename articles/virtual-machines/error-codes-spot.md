---
title: Kódy chyb pro virtuální počítače Azure Spot a instance škálovacích sad
description: Přečtěte si o kódech chyb, které byste mohli vidět při použití virtuálních virtuálních virtuálních měn spotů a instancí škálovací sady.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 5a34dc2b9468c6c5af4af0e0addfd8b9ebb7e792
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547818"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>Chybové zprávy pro virtuální aplikace Spot a škálovací sady

Tady jsou některé možné kódy chyb, které můžete obdržet při použití virtuálních virtuálních měn spotů a škálovacích sad.


| Klíč | Zpráva | Popis |
|-----|---------|-------------|
| SkuNotAvailable | Požadovaná úroveň pro\<\>prostředek " prostředek " není\<\>momentálně k\<dispozici v\>umístění lokace pro id předplatného . Zkuste jinou úroveň nebo nasazení do jiného umístění. | V tomto umístění není dostatečná kapacita Azure Spot k vytvoření instance virtuálního počítače nebo škálovací sady. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  Zásady vyřazení lze nastavit pouze na virtuálních počítačích Azure Spot. | Tento virtuální počítač není virtuální počítač s bodem, takže nelze nastavit zásady vyřazení. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Virtuální počítač Azure Spot není v sadě dostupnosti podporován. | Musíte se rozhodnout použít virtuální virtuální hod spot nebo použít virtuální hovado v sadě dostupnosti, nemůžete vybrat obojí. |
| AzureSpotFeaturenotEnabledforsubscription  |  Předplatné není povoleno s funkcí Azure Spot. | Použijte předplatné, které podporuje virtuální chod spot. |
| VMPriority nelze použít.  |  Zadanou hodnotu{0}priority " nelze použít{1}pro virtuální počítač , protože při vytvoření virtuálního počítače nebyla zadána žádná priorita. | Určete prioritu při vytvoření virtuálního virtuálního soudu. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Operaci " "{0}nelze provést, protože{1} zadaný maximální cena ' USD{2} ' je nižší než{3}aktuální spotová cena ' USD ' pro velikost virtuálního počítače Azure Spot ' '. | Vyberte vyšší maximální cenu. Další informace naleznete v informacích o cenách pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) nebo [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueNe  |  Neplatná maximální cena. Jediné podporované hodnoty pro maximální cenu jsou -1 nebo desetinné číslo větší než nula. Maximální hodnota ceny -1 označuje, že virtuální počítač Azure Spot nebude vyřazen z důvodů ceny. | Zadejte platnou maximální cenu. Další informace naleznete v tématu ceny pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) nebo [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | Maximální změna ceny není povolena,{0}pokud je aktuálně přidělen virtuální virtuální město . Navrátit a akci opakujte. | Stop\Narozdělit virtuální ho disponizuje, abyste mohli změnit maximální cenu. |
| MaxPriceChangeNotAllowed | Maximální změna ceny není povolena. | Maximální cenu pro tento virtuální virtuální soud nelze změnit. |
| AzureSpotisnotsupportedForThisAPIversion  |  Azure Spot není pro tuto verzi rozhraní API podporován. | Verze rozhraní API musí být 2019-03-01. |
| AzureSpotisnot supportedforThisVMsize  |  Azure Spot není pro tuto {0}velikost virtuálního počítače podporovaný. | Vyberte jinou velikost virtuálního počítače. Další informace naleznete v tématu [Spot Virtual Machines](./linux/spot-vms.md). |
| MaxPriceissupportedOnlyForAzureSpotVirtualMachines MaxpriceissupportedOnlyForAzureSpotVirtualMachines MaxpriceissupportedOnlyForAzureSpotVirtualMachines Maxprice  |  Maximální cena je podporovaná jenom pro virtuální počítače Azure Spot. | Další informace naleznete v tématu [Spot Virtual Machines](./linux/spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  Požadavek přesunout prostředky obsahuje virtuální počítač Azure Spot. Tato tato podpora není v současné době podporována. Zkontrolujte podrobnosti o chybě pro ID virtuálních strojů. | Virtuální virtuální aplikace nelze přesunout. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  Požadavek přesunout prostředky obsahuje škálovací sadu virtuálních strojů Azure Spot. Tato tato podpora není v současné době podporována. Zkontrolujte podrobnosti o chybě pro ID škálovací sady virtuálních strojů. | Instance sady škálovacích stupňů nelze přesunout. |
| EfmeralOSDisksNotSupportedForSpotVMs | Dočasné disky operačního systému nejsou podporovány pro virtuální počítače Spot. | Použijte běžný disk operačního systému pro virtuální počítač Spot. |
| AzureSpotVMNotSupportedInVMssWithVMOrchestrationMode | Virtuální počítač Azure Spot není v režimu orchestrace virtuálních počítačů podporován v škálovací sadě virtuálních strojů. | Nastavte režim orchestrace na škálovací sadu virtuálních strojů, abyste mohli používat instance Spot. |


**Další kroky** Další informace naleznete v [tématu spot virtuální počítače](./linux/spot-vms.md).