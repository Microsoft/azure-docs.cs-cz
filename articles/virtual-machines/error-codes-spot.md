---
title: Kódy chyb pro instance virtuálních počítačů a sad škálování na místě Azure
description: Přečtěte si o chybových kódech, které by mohly být zobrazeny při použití virtuálních počítačů s přímým odkazem a instancí sad.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/20/2019
ms.author: cynthn
ms.openlocfilehash: 97e68f4d0d671ffa0f697c484c502b9070a3f20f
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781940"
---
# <a name="preview-error-messages-for-spot-vms-and-scale-sets"></a>Preview: chybové zprávy pro virtuální počítače s přímým zobrazením a sady škálování


> [!IMPORTANT]
> Virtuální počítače a služba Virtual Machine Scale Sets jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Tady jsou některé možné kódy chyb, které byste mohli obdržet při použití virtuálních počítačů a sad škálování na místě.


| Klíč | Zpráva | Popis |
|-----|---------|-------------|
| SkuNotAvailable | Požadovaná úroveň prostředku\<\>prostředků není v tuto chvíli k dispozici v umístění\<umístění\>pro předplatné\<subscriptionID\>. Zkuste prosím jinou vrstvu nebo ji nasaďte do jiného umístění. | V tomto umístění není k dispozici dostatek úložné kapacity Azure k vytvoření virtuálního počítače nebo instance sady škálování. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  Zásada vyřazení se dá nastavit jenom v Azure na místě Virtual Machines. | Tento virtuální počítač není virtuálním počítačem s přímým odkazem, takže nemůžete nastavit zásady vyřazení. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Virtuální počítač se službou Azure na místě není v sadě dostupnosti podporovaný. | Musíte zvolit, že chcete použít virtuální počítač nebo virtuální počítač ve skupině dostupnosti, ale nemůžete použít obojí. |
| AzureSpotFeatureNotEnabledForSubscription  |  Předplatné není pro funkci Azure bodových funkcí zapnuté. | Musíte mít předplatné, které podporuje virtuální počítače na místě. |
| VMPriorityCannotBeApplied  |  Zadanou hodnotu priority{0}nejde použít u virtuálního počítače{1}, protože při vytváření virtuálního počítače se nezadala žádná priorita. | Při vytváření virtuálního počítače musíte zadat prioritu. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Operaci '{0}' nelze provést, protože zadaná maximální cena '{1} USD ' je nižší než aktuální cena za cenu '{2} USD ' pro velikost virtuálního počítače Azure "{3}". | Vyberte vyšší maximální cenu. Další informace najdete v tématu informace o cenách pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) nebo [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Hodnota maximální ceny je neplatná. Jediné podporované hodnoty pro max Price jsou-1 nebo desetinné číslo větší než nula. Maximální hodnota ceny-1 znamená, že virtuální počítač se službou Azure na místě nebude z důvodu cen vyřazen. | Zadejte platnou Maxu cenu. Další informace najdete v tématu ceny pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) nebo [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | Pokud je virtuální počítač{0}aktuálně přidělený, není povolená Změna maximální ceny. Navraťte se prosím a zkuste to znovu. | Stop\Deallocate virtuální počítač, abyste mohli změnit maximální cenu. |
| MaxPriceChangeNotAllowed | Maximální změna ceny není povolená. | Pro tento virtuální počítač nemůžete změnit maximální cenu. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Pro tuto verzi rozhraní API není podporováno Azure spot. | Verze rozhraní API musí být 2019-03-01. |
| AzureSpotIsNotSupportedForThisVMSize  |  Pro tuto velikost virtuálního počítače se nepodporují Azure spot {0}. | Vyberte jinou velikost virtuálního počítače. Další informace najdete v tématu [přímá Virtual Machines](./linux/spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  Maximální cena je podporovaná jenom pro Azure spot Virtual Machines. | Další informace najdete v tématu [přímá Virtual Machines](./linux/spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  Požadavek na přesunutí prostředků obsahuje virtuální počítač se službou Azure na místě. To se v tuto chvíli nepodporuje. Podívejte se prosím na podrobnosti o chybách pro ID virtuálních počítačů. | Nemůžete přesunout virtuální počítače na místě. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  Požadavek na přesunutí prostředků obsahuje sadu škálování služby Virtual Machine na platformě Azure. To se v tuto chvíli nepodporuje. Podívejte se prosím na podrobnosti o chybách pro ID sady škálování virtuálních počítačů. | Nemůžete přesunout instance sady přímých škálování. |
| EphemeralOSDisksNotSupportedForSpotVMs | Dočasné disky s operačním systémem se pro virtuální počítače s přímým odkazem nepodporují. | Musíte používat běžný disk s operačním systémem pro virtuální počítač s přímým použitím. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Virtuální počítač se službou Azure na úrovni škálování virtuálního počítače se nepodporuje v režimu orchestrace virtuálních počítačů. | Nastavte režim orchestrace na Virtual Machine Scale set, aby bylo možné použít instance bodového řízení. |


**Další kroky** Další informace najdete v tématu [přímá Virtual Machines](./linux/spot-vms.md).