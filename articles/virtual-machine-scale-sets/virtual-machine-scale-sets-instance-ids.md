---
title: Vysvětlení ID instancí pro virtuální počítače sady škálování virtuálních počítačů Azure | Dokumentace Microsoftu
description: Vysvětlení ID instancí pro Azure VM scale sady virtuálních počítačů
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: manayar
ms.openlocfilehash: 6aeba722a0661979664f8d61efdb9b2bf47ad801
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981651"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Vysvětlení ID instancí pro Azure VM scale sady virtuálních počítačů
Tento článek popisuje ID instancí škálovací sady a různé způsoby, že surface.

## <a name="scale-set-instance-ids"></a>Škálovací sada ID instancí

Každý virtuální počítač ve škálovací sadě získá ID instance, který ji jednoznačně identifikuje. Této instance, ID se používá ve škálovací sadě rozhraní API, provádět operace s konkrétní sadě virtuálních počítačů ve škálovací. Při použití rozhraní API obnovení z Image, můžete zadat ID konkrétní instanci pro obnovení z Image:

Rozhraní REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (Další informace najdete v tématu [dokumentace k rozhraní REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

Prostředí PowerShell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (Další informace najdete v tématu [dokumentaci k Powershellu](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm))

Rozhraní příkazového řádku: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (Další informace najdete v tématu [dokumentace k rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Výpis všech instancí ve škálovací sadě, můžete získat seznam ID instance:

Rozhraní REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (Další informace najdete v tématu [dokumentace k rozhraní REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list))

Prostředí PowerShell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (Další informace najdete v tématu [dokumentaci k Powershellu](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm))

Rozhraní příkazového řádku: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (Další informace najdete v tématu [dokumentace k rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Můžete také použít [resources.azure.com](https://resources.azure.com) nebo [sady Azure SDK](https://azure.microsoft.com/downloads/) seznam virtuálních počítačů ve škálovací sadě.

Přesné prezentace výstup závisí na možnostech, které zadáte do příkazu, ale tady je několik ukázkový výstup z rozhraní příkazového řádku:

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

Jak vidíte, je vlastnost "instanceId" pouze desítkové číslo. ID instance mohou být znovu použity pro nové instance, jakmile se odstraní původní instancí.

>[!NOTE]
> Je **zaručeno** na instanci způsob, jak jsou ID přiřazené k virtuálním počítačům ve škálovací sadě. Se zdát, že postupně rostoucí čas od času, ale není to vždy. Nelze zavést závislost na konkrétní způsob, ve kterém jsou instance ID přiřazené k virtuálním počítačům.

## <a name="scale-set-vm-names"></a>Škálovací sada názvy virtuálních počítačů

V ukázkovém výstupu výše je také "name" pro virtuální počítač. Tento název má podobu "{název škálovací sady} _ {instance id}". Tento název je ten, který se zobrazí na webu Azure Portal, kdy zobrazíte seznam instancí ve škálovací sadě:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

{Instance id} část názvu je stejný desetinné číslo jako vlastnost "instanceId", jak jsme vysvětlili výše.

## <a name="instance-metadata-vm-name"></a>Název instance Metadata virtuálního počítače

Když odešlete dotaz [instance metadata](../virtual-machines/windows/instance-metadata-service.md) z v rámci škálovací sady virtuálních počítačů, se zobrazí "name" ve výstupu:

```
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Tento název je stejný jako název, jak jsme vysvětlili výše.

## <a name="scale-set-vm-computer-name"></a>Škálovací sady virtuálních počítačů název počítače

Každý virtuální počítač ve škálovací sadě také získá název počítače přiřazené k němu. Tento název počítače je název hostitele virtuálního počítače v [překlad názvů DNS, které poskytuje Azure v rámci virtuální sítě](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Tento název počítače je ve formátu "{computer-name-prefix}{base-36-instance-id}".

{Base – 36-instance-id} se v [základní 36](https://en.wikipedia.org/wiki/Base36) a je vždy šesti číslic. Pokud 36 reprezentace čísla, trvá méně než šest číslic, {base – 36-instance-id} doplněno nulami, aby šestičíselné délku. Například instance se stavem {počítače předpona názvu} "nsgvmss" a instance ID 85 bude mít název počítače "nsgvmss00002D".

>[!NOTE]
> Předpona názvu počítače je vlastnost modelu škálovací sady, kterou můžete nastavit, takže může lišit od samotný název škálovací sady.
