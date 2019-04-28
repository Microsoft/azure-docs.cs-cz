---
title: Import virtuálních počítačů z jiného testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak importovat virtuální počítače z jiného testovacího prostředí v aktuálním testovacím prostředí.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: ca6ed58cfabb5027830828812c4820c1b586875c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61322839"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Import virtuálních počítačů z jiného testovacího prostředí ve službě Azure DevTest Labs
Tento článek obsahuje informace o tom, jak importovat virtuální počítače z jiného testovacího prostředí do testovacího prostředí.

## <a name="scenarios"></a>Scénáře
Tady je několik scénářů, které je potřeba importovat virtuální počítače z jednoho prostředí do jiného testovacího prostředí:

- Jednotlivec v týmu se přesouvá do jiné skupiny v rámci podniku a chce využít desktop pro vývojáře pro nový tým DevTest Labs.
- Skupině dosáhne [kvóty na úrovni předplatného](../azure-subscription-service-limits.md) a chce rozdělit týmy do několika předplatných
- Společnost přechází na Expressroute (nebo nové síťové topologie) a chce tým přesunout virtuální počítače používat tato nová infrastruktura

## <a name="solution-and-constraints"></a>Řešení a omezení
Tato funkce umožňuje naimportovat virtuální počítače v jedné laboratoři (zdroj) do jiného prostředí (cíl). Můžete volitelně zadejte nový název pro cílový virtuální počítač v procesu. Proces importu zahrnuje všechny závislosti, jako jsou disky, plány, nastavení sítě a tak dále.

Proces trvat delší dobu a to má vliv následující faktory:

- Počet/velikost disků, které jsou připojeny ke zdrojovému počítači (protože se jedná o operaci kopírování a operace přesunu)
- Vzdálenost k cíli (například oblast východní USA do Asie – jihovýchod).

Po dokončení procesu zdrojového virtuálního počítače zůstane vypnout a nové, jeden je spuštěna v cílovém prostředí.

Existují dvě omezení klíčů je potřeba vědět při plánování pro import virtuálních počítačů z jedné laboratoři v do jiného testovacího prostředí:

- Importy virtuálních počítačů mezi předplatnými a v oblastech se podporují, ale předplatná musí být přidružený ke stejnému tenantovi Azure Active Directory.
- Virtuální počítače nesmí být ve stavu nárokovatelných v testovacím prostředí zdroje.
- Vlastník virtuálního počítače ve zdrojové prostředí a vlastník testovacího prostředí v cílovém prostředí.
- Tato funkce v současné době je podporována pouze prostřednictvím prostředí Powershell a rozhraní REST API.

## <a name="use-powershell"></a>Použití prostředí PowerShell
Stáhněte si soubor ImportVirtualMachines.ps1 z [Githubu](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). Skript můžete použít pro import jednoho virtuálního počítače nebo všech virtuálních počítačů v testovacím prostředí zdroje do cílového testovacího prostředí.

### <a name="use-powershell-to-import-a-single-vm"></a>Pomocí prostředí PowerShell pro import jednoho virtuálního počítače
Spuštěním tohoto skriptu prostředí powershell vyžaduje určení zdrojového virtuálního počítače a cílové testovací prostředí a volitelně poskytuje nový název má použít pro cílový počítač:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Použití Powershellu k importu všech virtuálních počítačů v testovacím prostředí zdroje
Pokud zdrojový virtuální počítač není zadán, skript automaticky importuje všechny virtuální počítače ve službě DevTest Labs.  Příklad:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Použití HTTP REST k importu virtuálního počítače
Volání REST je jednoduché. Můžete poskytnout dostatek informací pro identifikaci prostředků, které zdroj a cíl. Mějte na paměti, že operace probíhá na cílového prostředku testovacího prostředí.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Další postup
Viz následující články:

- [Nastavení zásad pro testovací prostředí](devtest-lab-get-started-with-lab-policies.md)
- [Nejčastější dotazy](devtest-lab-faq.md)
