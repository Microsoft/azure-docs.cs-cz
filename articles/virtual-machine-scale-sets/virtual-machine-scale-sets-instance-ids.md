---
title: Pochopení ID instancí pro virtuální počítače Azure VM Scale set
description: Porozumět ID instancí virtuálních počítačů Azure VM Scale Sets a různým způsobům, které jsou na nich plochy.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: manayar
ms.openlocfilehash: aa2b0013818f897f01945d394266a57016ecb0bb
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275854"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Pochopení ID instancí pro virtuální počítače Azure VM Scale set
Tento článek popisuje ID instancí pro sady škálování a různé způsoby jejich povrchu.

## <a name="scale-set-instance-ids"></a>ID instance sady škálování

Každý virtuální počítač v sadě škálování získá ID instance, která ho jednoznačně identifikuje. Toto ID instance se používá v rozhraních API sady škálování pro provádění operací na konkrétním virtuálním počítači v sadě škálování. Můžete například zadat konkrétní ID instance pro obnovení bitové kopie při použití rozhraní API pro přeimagí:

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (Další informace najdete v dokumentaci k [REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

PowerShell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (Další informace najdete v [dokumentaci k PowerShellu](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm))

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (Další informace najdete v [dokumentaci k](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)rozhraní příkazového řádku).

Seznam ID instancí můžete získat zobrazením všech instancí v sadě škálování:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (Další informace najdete v dokumentaci k [REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list))

PowerShell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (Další informace najdete v [dokumentaci k PowerShellu](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm))

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (Další informace najdete v [dokumentaci k](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)rozhraní příkazového řádku).

K vypsání virtuálních počítačů v sadě škálování můžete použít taky [Resources.Azure.com](https://resources.azure.com) nebo [Azure SDK](https://azure.microsoft.com/downloads/) .

Přesnější prezentace výstupu závisí na možnostech, které zadáte do příkazu, ale tady je ukázkový výstup z rozhraní příkazového řádku:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
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

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

Část názvu {instance-ID} je stejné desetinné číslo jako výše popsaná vlastnost instanceId.

## <a name="instance-metadata-vm-name"></a>Název virtuálního počítače metadat instance

Pokud se dotazuje [metadata instance](../virtual-machines/windows/instance-metadata-service.md) z virtuálního počítače sady škálování, ve výstupu se zobrazí "název":

```
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
