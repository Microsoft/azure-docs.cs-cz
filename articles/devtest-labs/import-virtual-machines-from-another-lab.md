---
title: Import virtuálních počítačů z jiného testovacího prostředí v Azure DevTest Labs
description: Tento článek popisuje, jak importovat virtuální počítače z jiného testovacího prostředí do aktuálního testovacího prostředí v Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0f664a0ae399575ee936565adaf7364fd1c5ce5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85475930"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Import virtuálních počítačů z jiného testovacího prostředí v Azure DevTest Labs
Tento článek poskytuje informace o tom, jak importovat virtuální počítače z jiného testovacího prostředí do testovacího prostředí.

## <a name="scenarios"></a>Scénáře
Tady je několik scénářů, kdy potřebujete importovat virtuální počítače z jednoho testovacího prostředí do jiného testovacího prostředí:

- Jednotlivec v týmu se přesouvá do jiné skupiny v rámci podniku a chce vzít pracovní plochu pro vývojáře na DevTest Labs nového týmu.
- Tato skupina dosáhla [kvóty na úrovni předplatného](../azure-resource-manager/management/azure-subscription-service-limits.md) a chce rozdělit týmy na několik předplatných.
- Společnost se přesouvá do expresní trasy (nebo jiné nové síťové topologie) a tým chce přesunout Virtual Machines, aby používal tuto novou infrastrukturu.

## <a name="solution-and-constraints"></a>Řešení a omezení
Tato funkce umožňuje importovat virtuální počítače v jednom testovacím prostředí (Source) do jiného testovacího prostředí (cíle). Volitelně můžete pro cílový virtuální počítač v procesu zadat nový název. Proces importu zahrnuje všechny závislosti, jako jsou disky, plány, nastavení sítě atd.

Proces trvá nějakou dobu a má vliv na tyto faktory:

- Počet disků, které jsou připojené ke zdrojovému počítači (vzhledem k tomu, že se jedná o operaci kopírování, nikoli operace přesunutí)
- Vzdálenost k cíli (například Východní USA oblasti na jihovýchod Asie).

Po dokončení procesu zůstane zdrojový virtuální počítač vypnutý a v cílovém testovacím prostředí je spuštěný nový.

Při plánování importu virtuálních počítačů z jednoho testovacího prostředí do jiného testovacího prostředí je třeba vědět o dvou klíčových omezeních:

- Importy virtuálních počítačů mezi předplatnými a mezi jednotlivými oblastmi jsou podporovány, ale odběry musí být přidruženy ke stejnému Azure Active Directory tenantovi.
- Virtual Machines nesmí být ve zdrojovém testovacím prostředí ve stavu s nárokem na deklaraci.
- Jste vlastníkem virtuálního počítače ve zdrojové laboratoři a vlastníkem testovacího prostředí v cílovém testovacím prostředí.
- V současné době je tato funkce podporovaná jenom prostřednictvím PowerShellu a REST API.

## <a name="use-powershell"></a>Použití prostředí PowerShell
Stažení ImportVirtualMachines.ps1 souboru z [GitHubu](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). Skript můžete použít k importu jednoho virtuálního počítače nebo všech virtuálních počítačů ve zdrojovém prostředí do cílového testovacího prostředí.

### <a name="use-powershell-to-import-a-single-vm"></a>Import jednoho virtuálního počítače pomocí PowerShellu
Spuštění tohoto skriptu PowerShellu vyžaduje určení zdrojového virtuálního počítače a cílového testovacího prostředí a volitelně zadání nového názvu, který se má použít pro cílový počítač:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Použití PowerShellu k importování všech virtuálních počítačů ve zdrojovém testovacím prostředí
Pokud není zadaný zdrojový virtuální počítač, skript automaticky naimportuje všechny virtuální počítače v DevTest Labs.  Například:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Import virtuálního počítače pomocí protokolu HTTP REST
Volání REST je jednoduché. Poskytnete dostatek informací pro identifikaci zdrojového a cílového prostředku. Mějte na paměti, že operace se provádí na cílovém prostředku testovacího prostředí.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Nastavení zásad pro testovací prostředí](devtest-lab-set-lab-policy.md)
- [Nejčastější dotazy](devtest-lab-faq.md)
