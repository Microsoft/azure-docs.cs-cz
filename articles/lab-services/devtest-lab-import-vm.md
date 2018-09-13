---
title: Import virtuálních počítačů z jiného testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak importovat virtuální počítače z jiného testovacího prostředí do aktuálního testovacího prostředí ve službě Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: 4585d151e286917c67586a02539a10ade32bdd4c
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643618"
---
# <a name="import-vms-from-another-lab-in-azure-devtest-labs"></a>Import virtuálních počítačů z jiného testovacího prostředí ve službě Azure DevTest Labs
Služba Azure DevTest Labs výrazně zlepšuje správu virtuálních počítačů (VM) pro vývoj a testování aktivity. Umožňuje přesunout virtuální počítač z jednoho prostředí do jiného jako tým, nebo změňte požadavky na infrastrukturu. Zde jsou uvedeny některé obvyklé scénáře, ve kterém budete muset udělat: 

- Osobu v týmu přesune do jiné skupiny v rámci podniku a chce využít vývoj pro virtuální počítače do testovacího prostředí nového týmu.
- Skupina dosáhl kvóty úrovni předplatného a chce rozdělit týmy do více předplatných.
- Společnosti plánuje přesunout do Expressroute (nebo nové síťové topologie) a chce tým přesunout virtuální počítače používat tato nová infrastruktura.

## <a name="solution-and-constraints"></a>Řešení a omezení
Azure DevTest Labs umožňuje vlastník testovacího prostředí pro import virtuálních počítačů v testovacím prostředí zdroje do cílového testovacího prostředí. Vlastník testovacího prostředí můžete volitelně zadat nový název pro cílový virtuální počítač v procesu. Proces importu zahrnuje všechny závislosti, jako jsou disky, plány, nastavení sítě atd. Proces nějakou dobu trvat a je ovlivněn počet/velikost disků připojených k zdrojovým počítačem a vzdálenost k cíli (například oblast východní USA do Asie – jihovýchod). Po dokončení procesu importu zůstane zdrojový virtuální počítač vypnout a nové, jeden je spuštěna v cílovém prostředí.

Existují dvě omezení klíčů je potřeba vědět při plánování pro import virtuálních počítačů do jiného testovacího prostředí:

- Import virtuálních počítačů mezi předplatnými a napříč oblasti jsou podporovány, ale předplatná musí být přidružený ke stejnému tenantovi Azure Active Directory.
- Virtuální počítače nesmí být ve stavu nárokovatelných v testovacím prostředí zdroje.

Kromě toho aby bylo možné importovat virtuální počítač z jednoho prostředí do druhého, musíte být vlastník virtuálního počítače ve zdrojové prostředí a vlastník testovacího prostředí v cílovém prostředí.

## <a name="steps-to-import-a-vm-from-another-lab"></a>Postup při importu virtuálního počítače z jiného testovacího prostředí
V současné době můžete importovat virtuální počítač z jednoho prostředí do jiného pouze pomocí prostředí Azure PowerShell a rozhraní REST API.

### <a name="use-powershell"></a>Použití prostředí PowerShell
Stažení souboru skriptu prostředí PowerShell ImportVirtualMachines.ps1 z [úložiště Git v Azure DevTest Lab](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImportVirtualMachines) na váš místní disk. 

#### <a name="import-a-single-vm"></a>Importovat jeden virtuální počítač
Spusťte skript ImportVirtualMachines.ps1 import jednoho virtuálního počítače ze zdrojového testovacího prostředí do cílového testovacího prostředí. Můžete zadat nový název pro virtuální počítač, který je kopírování s použitím DestinationVirtualMachineName paramer. 

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -SourceVirtualMachineName "<Name of the machine. Optional. If not specified, all VMs are copied>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VM is copied>" `
                            -DestinationVirtualMachineName "<New name for the VM. Optional>"
```


#### <a name="importing-all-vms"></a>Import všech virtuálních počítačů
Při spuštění skriptu ImportVirtualMachines.ps1, pokud nezadáte virtuálního počítače v testovacím prostředí zdroj, skript importuje všechny virtuální počítače v testovacím prostředí zdroje do cílového testovacího prostředí. 

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VMs are copied>"
```

### <a name="use-rest-api"></a>Použití rozhraní REST API
Volání rozhraní REST API proti testovacím/cílová a předejte mu zdrojové prostředí, předplatné a informace o virtuálním počítači jako parametry, jak je znázorněno v následujícím příkladu: 

```json
POST https://management.azure.com/subscriptions/<ID of the target/destination subscription>/resourceGroups/<Name of the resource group that contains the destination lab>/providers/Microsoft.DevTestLab/labs/<Name of the lab to which the VMs are copied>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<ID of the subscription that contains the source VM>/resourcegroups/<Name of the resource group that contains the source lab>/providers/microsoft.devtestlab/labs/<Name of the lab that contains the source VM>/virtualmachines/MyVm",
   destinationVirtualMachineName: "MyVmNew"
}
```

## <a name="next-steps"></a>Další postup

- Informace o změně velikosti virtuálního počítače najdete v tématu [změnit velikost virtuálního počítače](devtest-lab-resize-vm.md).
- Informace o opětovné nasazení virtuálního počítače najdete v tématu [opětovné nasazení virtuálního počítače](devtest-lab-redeploy-vm.md).


