---
title: Pochopení ID instancí pro virtuální počítače Azure VM Scale set
description: Porozumět ID instancí virtuálních počítačů Azure VM Scale Sets a různým způsobům, které jsou na nich plochy.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 02/22/2018
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 07f72d54c0d62748196302ed1b77ea750dede8ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87080450"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Pochopení ID instancí pro virtuální počítače Azure VM Scale set
Tento článek popisuje ID instancí pro sady škálování a různé způsoby jejich povrchu.

## <a name="scale-set-instance-ids"></a>ID instance sady škálování

Každý virtuální počítač v sadě škálování získá ID instance, která ho jednoznačně identifikuje. Toto ID instance se používá v rozhraních API sady škálování pro provádění operací na konkrétním virtuálním počítači v sadě škálování. Můžete například zadat konkrétní ID instance pro obnovení bitové kopie při použití rozhraní API pro přeimagí:

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/reimage?api-version={apiVersion}` (Další informace najdete v dokumentaci k [REST API](/rest/api/compute/virtualmachinescalesetvms/reimage)).

PowerShell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (Další informace najdete v [dokumentaci k PowerShellu](/powershell/module/az.compute/set-azvmssvm))

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (Další informace najdete v [dokumentaci k](/cli/azure/vmss?view=azure-cli-latest)rozhraní příkazového řádku).

Seznam ID instancí můžete získat zobrazením všech instancí v sadě škálování:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (Další informace najdete v dokumentaci k [REST API](/rest/api/compute/virtualmachinescalesetvms/list)).

PowerShell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (Další informace najdete v [dokumentaci k PowerShellu](/powershell/module/az.compute/get-azvmssvm))

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (Další informace najdete v [dokumentaci k](/cli/azure/vmss?view=azure-cli-latest)rozhraní příkazového řádku).

K vypsání virtuálních počítačů v sadě škálování můžete použít taky [Resources.Azure.com](https://resources.azure.com) nebo [Azure SDK](https://azure.microsoft.com/downloads/) .

Přesnější prezentace výstupu závisí na možnostech, které zadáte do příkazu, ale tady je ukázkový výstup z rozhraní příkazového řádku:

```azurecli
az vmss show -g {resourceGroupName} -n {vmScaleSetName}
```

```output
[
  {
    "instanceId": "85",
    "latestModelApplied": true,
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Jak vidíte, vlastnost instanceId je jenom desítkové číslo. Po odstranění starých instancí se ID instancí můžou znovu použít pro nové instance.

>[!NOTE]
> Neexistuje **žádná záruka** , jak jsou ID instancí přiřazena k virtuálním počítačům v sadě škálování. Může se zdát, že se postupně zvyšují v časech, ale to není vždy v případě případu. Neprovádějte závislost na konkrétním způsobu, jakým jsou přiřazena ID instancí k virtuálním počítačům.

## <a name="scale-set-vm-names"></a>Nastavení škálování pro názvy virtuálních počítačů

Ve výše uvedeném příkladu je pro virtuální počítač k dispozici také "název". Tento název má podobu "{Scale-set-Name} _ {instance-ID}". Tento název je ten, který se zobrazí v Azure Portal při výpisu instancí v sadě škálování:

![Snímek obrazovky zobrazující seznam instancí v sadě škálování virtuálního počítače v Azure Portal.](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

Část názvu {instance-ID} je stejné desetinné číslo jako výše popsaná vlastnost instanceId.

## <a name="instance-metadata-vm-name"></a>Název virtuálního počítače metadat instance

Pokud se dotazuje [metadata instance](../virtual-machines/windows/instance-metadata-service.md) z virtuálního počítače sady škálování, ve výstupu se zobrazí "název":

```output
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Tento název je stejný jako název popsaný dříve.

## <a name="scale-set-vm-computer-name"></a>Název virtuálního počítače pro škálování sady škálování

Každý virtuální počítač v sadě škálování taky získá přiřazený název počítače. Název tohoto počítače je název hostitele virtuálního počítače v Azure, který je v [rámci virtuální sítě určený pro překlad názvů DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Tento název počítače má formát "{Computer-Name-prefix} {Base-36-instance-ID}".

Identifikátor {Base-36-instance-ID} je v [základní třídě 36](https://en.wikipedia.org/wiki/Base36) a má vždy šest číslic. Pokud základní reprezentace 36 čísla trvá méně než šest číslic, pak se {Base-36-instance-ID} doplní nulami, aby byla číslice šest číslic. Například instance s názvem {Computer-Name-prefix} "nsgvmss" a ID instance 85 bude mít název počítače "nsgvmss00002D".

>[!NOTE]
> Předpona názvu počítače je vlastnost modelu sady škálování, kterou můžete nastavit, takže se může lišit od samotného názvu sady škálování.
