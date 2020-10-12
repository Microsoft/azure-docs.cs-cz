---
title: Kódy chyb pro instance virtuálních počítačů a sad škálování na místě Azure
description: Přečtěte si o chybových kódech, které by mohly být zobrazeny při použití virtuálních počítačů s přímým odkazem a instancí sad.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 3e151145bd3c4d1db87ef5d1b0d553555a580279
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88816741"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>Chybové zprávy pro virtuální počítače a sady škálování na místě

Tady jsou některé možné kódy chyb, které byste mohli obdržet při použití virtuálních počítačů a sad škálování na místě.


| Klíč | Zpráva | Popis |
|-----|---------|-------------|
| SkuNotAvailable | Požadovaná úroveň pro prostředek \<resource\> není v současné době k dispozici v umístění \<location\> pro předplatné \<subscriptionID\> . Zkuste prosím jinou vrstvu nebo ji nasaďte do jiného umístění. | V tomto umístění není k dispozici dostatek úložné kapacity Azure k vytvoření virtuálního počítače nebo instance sady škálování. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  Zásada vyřazení se dá nastavit jenom v Azure na místě Virtual Machines. | Tento virtuální počítač není virtuálním počítačem s přímým odkazem, takže nemůžete nastavit zásady vyřazení. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Virtuální počítač se službou Azure na místě není v sadě dostupnosti podporovaný. | Musíte zvolit, že chcete použít virtuální počítač nebo virtuální počítač ve skupině dostupnosti, ale nemůžete použít obojí. |
| AzureSpotFeatureNotEnabledForSubscription  |  Předplatné není pro funkci Azure bodových funkcí zapnuté. | Použijte předplatné, které podporuje virtuální počítače s přímým použitím. |
| VMPriorityCannotBeApplied  |  Zadaná hodnota priority se {0} nedá použít u virtuálního počítače, {1} protože se při vytváření virtuálního počítače nezadala žádná priorita. | Zadejte prioritu při vytváření virtuálního počítače. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Operaci se nepovedlo provést, {0} protože zadaná maximální cena {1} USD je nižší než aktuální cena {2} za cenu USD pro velikost virtuálního počítače Azure na místě {3} . | Vyberte vyšší maximální cenu. Další informace najdete v tématu informace o cenách pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) nebo [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Hodnota maximální ceny je neplatná. Jediné podporované hodnoty pro max Price jsou-1 nebo desetinné číslo větší než nula. Maximální hodnota ceny-1 znamená, že virtuální počítač se službou Azure na místě nebude z důvodu cen vyřazen. | Zadejte platnou Maxu cenu. Další informace najdete v tématu ceny pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) nebo [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | Pokud je virtuální počítač aktuálně přidělený, není povolená Změna maximální ceny {0} . Navraťte se prosím a zkuste to znovu. | Stop\Deallocate virtuální počítač, abyste mohli změnit maximální cenu. |
| MaxPriceChangeNotAllowed | Maximální změna ceny není povolená. | Pro tento virtuální počítač nemůžete změnit maximální cenu. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Pro tuto verzi rozhraní API není podporováno Azure spot. | Verze rozhraní API musí být 2019-03-01. |
| AzureSpotIsNotSupportedForThisVMSize  |  Pro tuto velikost virtuálního počítače se nepodporují Azure na místě {0} . | Vyberte jinou velikost virtuálního počítače. Další informace najdete v tématu [přímá Virtual Machines](./spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  Maximální cena je podporovaná jenom pro Azure spot Virtual Machines. | Další informace najdete v tématu [přímá Virtual Machines](./spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  Požadavek na přesunutí prostředků obsahuje virtuální počítač se službou Azure na místě. To se v tuto chvíli nepodporuje. Podívejte se prosím na podrobnosti o chybách pro ID virtuálních počítačů. | Nemůžete přesunout virtuální počítače na místě. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  Požadavek na přesunutí prostředků obsahuje sadu škálování služby Virtual Machine na platformě Azure. To se v tuto chvíli nepodporuje. Podívejte se prosím na podrobnosti o chybách pro ID sady škálování virtuálních počítačů. | Nemůžete přesunout instance sady přímých škálování. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Virtuální počítač se službou Azure na úrovni škálování virtuálního počítače se nepodporuje v režimu orchestrace virtuálních počítačů. | Nastavte režim orchestrace na Virtual Machine Scale set, aby bylo možné použít instance bodového řízení. |


**Další kroky** Další informace najdete v tématu [přímá Virtual Machines](./spot-vms.md).