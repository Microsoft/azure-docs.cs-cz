---
title: Import virtuálních počítačů z jiné laboratoře v Azure DevTest Labs
description: Tento článek popisuje, jak importovat virtuální počítače z jiného testovacího prostředí do aktuálního testovacího prostředí v Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 299d5c8758a13edded63b99abb2f12ddf9fa14be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759512"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Import virtuálních počítačů z jiné laboratoře v Azure DevTest Labs
Tento článek obsahuje informace o tom, jak importovat virtuální počítače z jiného testovacího prostředí do testovacího prostředí.

## <a name="scenarios"></a>Scénáře
Tady jsou některé scénáře, kde potřebujete importovat virtuální uživatele z jednoho testovacího prostředí do jiného testovacího prostředí:

- Jednotlivec v týmu se stěhuje do jiné skupiny v rámci podniku a chce převést plochu pro vývojáře do nového týmu DevTest Labs.
- Skupina dosáhla [kvóty na úrovni předplatného](../azure-resource-manager/management/azure-subscription-service-limits.md) a chce rozdělit týmy na několik předplatných.
- Společnost se stěhuje do Express Route (nebo nějaké jiné nové síťové topologie) a tým chce přesunout virtuální počítače k použití této nové infrastruktury

## <a name="solution-and-constraints"></a>Řešení a omezení
Tato funkce umožňuje importovat virtuální chod y v jednom testovacím prostředí (zdroj) do jiného testovacího prostředí (cíl). Volitelně můžete dát nový název pro cílový virtuální virtuální ms v procesu. Proces importu zahrnuje všechny závislosti, jako jsou disky, plány, nastavení sítě a tak dále.

Proces trvá nějakou dobu a je ovlivněn následujícími faktory:

- Počet/velikost disků, které jsou připojeny ke zdrojovému počítači (protože se jedná o operaci kopírování a nikoli o operaci přesunutí)
- Vzdálenost k cíli (například oblast Východní USA k jihovýchodní Asii).

Po dokončení procesu zůstane zdrojový virtuální počítač vypnutý a nový je spuštěn v cílové laboratoři.

Existují dvě klíčová omezení, která je třeba znát při plánování importu virtuálních klíčů z jednoho testovacího prostředí do jiného testovacího prostředí:

- Importy virtuálních strojů napříč předplatnými a napříč oblastmi jsou podporované, ale předplatná musí být přidružená ke stejnému tenantovi Služby Azure Active Directory.
- Virtuální počítače nesmí být ve stavu nárokovatelné ve zdrojové laboratoři.
- Jste vlastníkem virtuálního virtuálního soudu ve zdrojové laboratoři a vlastníkem testovacího prostředí v cílové laboratoři.
- V současné době je tato funkce podporována pouze prostřednictvím rozhraní Powershell a REST API.

## <a name="use-powershell"></a>Použití prostředí PowerShell
Stáhněte si soubor ImportVirtualMachines.ps1 z [GitHubu](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). Skript můžete použít k importu jednoho virtuálního aplikace nebo všech virtuálních mv ve zdrojovélaboratoři do cílové laboratoře.

### <a name="use-powershell-to-import-a-single-vm"></a>Použití PowerShellu k importu jednoho virtuálního aplikace
Spuštění tohoto skriptu prostředí powershell vyžaduje identifikaci zdrojového virtuálního počítače a cílové laboratoře a volitelně zadání nového názvu, který se má použít pro cílový počítač:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Použití PowerShellu k importu všech virtuálních min ve zdrojovém testovacím prostředí
Pokud není zadán zdrojový virtuální počítač, skript automaticky importuje všechny virtuální počítače v DevTest Labs.  Například:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Import virtuálního virtuálního mísy pomocí protokolu HTTP REST
Volání REST je jednoduché. Poskytnete dostatek informací k identifikaci zdrojových a cílových zdrojů. Nezapomeňte, že operace probíhá na prostředek cílovélaboratoře.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Nastavení zásad pro testovací prostředí](devtest-lab-get-started-with-lab-policies.md)
- [Nejčastější dotazy](devtest-lab-faq.md)
