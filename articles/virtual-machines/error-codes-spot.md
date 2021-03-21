---
title: Kódy chyb pro instance sad Azure spot Virtual Machines a Scale Sets
description: Přečtěte si o kódech chyb, které by se mohly zobrazit při použití Virtual Machines a škálování instancí sad Azure na místě.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 6d43935365580233063deb0e523d222351c22d54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670606"
---
# <a name="error-messages-for-azure-spot-virtual-machines-and-scale-sets"></a>Chybové zprávy pro sady Azure Virtual Machines a škálování na místě

Tady jsou některé možné kódy chyb, které byste mohli obdržet při použití Virtual Machines a škálování na místě Azure.


| Klíč | Zpráva | Popis |
|-----|---------|-------------|
| SkuNotAvailable | Požadovaná úroveň pro prostředek \<resource\> není v současné době k dispozici v umístění \<location\> pro předplatné \<subscriptionID\> . Vyzkoušejte jinou vrstvu nebo nasaďte ji do jiného umístění. | V tomto umístění není dostatek kapacity virtuálního počítače Azure, aby bylo možné vytvořit virtuální počítač nebo instanci sady škálování. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  Zásada vyřazení se dá nastavit jenom v Azure na místě Virtual Machines. | Tento virtuální počítač není virtuálním počítačem Azure na místě, takže nemůžete nastavit zásady vyřazení. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Virtuální počítač se službou Azure na místě není v sadě dostupnosti podporovaný. | Musíte zvolit buď použití virtuálního počítače se systémem Azure nebo virtuální počítač ve skupině dostupnosti, ale nemůžete použít obojí. |
| AzureSpotFeatureNotEnabledForSubscription  |  U předplatného není zapnutá funkce Azure bodový virtuální počítač. | Použijte předplatné, které podporuje Azure spot Virtual Machines. |
| VMPriorityCannotBeApplied  |  Zadaná hodnota priority se {0} nedá použít u virtuálního počítače, {1} protože se při vytváření virtuálního počítače nezadala žádná priorita. | Zadejte prioritu při vytváření virtuálního počítače. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Operaci se nepovedlo provést, {0} protože zadaná maximální cena v {1} USD je nižší než aktuální cena {2} za cenu USD pro velikost virtuálního počítače se službou Azure bodových virtuálních počítačů {3} . | Vyberte vyšší maximální cenu. Další informace najdete v tématu informace o cenách pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) nebo [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Hodnota maximální ceny je neplatná. Jediné podporované hodnoty pro max Price jsou-1 nebo desetinné číslo větší než nula. Maximální hodnota ceny-1 znamená, že virtuální počítač se službou Azure na místě nebude z důvodu cen vyřazen. | Zadejte platnou Maxu cenu. Další informace najdete v tématu ceny pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) nebo [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | Pokud je virtuální počítač aktuálně přidělený, není povolená Změna maximální ceny {0} . Zrušte přidělení a zkuste to znovu. | Stop\Deallocate virtuální počítač, abyste mohli změnit maximální cenu. |
| MaxPriceChangeNotAllowed | Maximální změna ceny není povolená. | Pro tento virtuální počítač nemůžete změnit maximální cenu. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Virtuální počítač s přímým odkazem na Azure se pro tuto verzi rozhraní API nepodporuje. | Verze rozhraní API musí být 2019-03-01. |
| AzureSpotIsNotSupportedForThisVMSize  |  Virtuální počítač s přímým odkazem na Azure se pro tuto velikost virtuálního počítače nepodporuje {0} . | Vyberte jinou velikost virtuálního počítače. Další informace najdete v tématu [Azure na místě Virtual Machines](./spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  Maximální cena je podporovaná jenom pro Azure spot Virtual Machines. | Další informace najdete v tématu [Azure na místě Virtual Machines](./spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  Požadavek na přesunutí prostředků obsahuje virtuální počítač se službou Azure na místě. Nepodporováno Projděte si podrobnosti o chybě pro identifikátory virtuálních počítačů. | Nemůžete přesunout Virtual Machines Azure na místě. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  Požadavek na přesunutí prostředků obsahuje sadu škálování služby Virtual Machine na platformě Azure. Nepodporováno Podívejte se na podrobnosti o chybách pro ID sady škálování virtuálních počítačů. | Nemůžete přesunout instance sady škálování pro virtuální počítače Azure na místě. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Virtuální počítač se službou Azure na úrovni škálování virtuálního počítače se nepodporuje v režimu orchestrace virtuálních počítačů. | Nastavte režim orchestrace na Virtual Machine Scale set, aby bylo možné používat instance virtuálních počítačů Azure na místě. |


**Další kroky** Další informace najdete v tématu [přímá Virtual Machines](./spot-vms.md).