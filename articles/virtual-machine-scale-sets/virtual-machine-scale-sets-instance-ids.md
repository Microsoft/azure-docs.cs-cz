---
title: Pochopení ID instance pro virtuální počítače sady škálování virtuálního počítače Azure | Microsoft Docs
description: Srozumitelná že ID instance pro škálování virtuálního počítače Azure nastavit virtuální počítače
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
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
ms.author: negat
ms.openlocfilehash: 3a43dc86f1fb53dfde4bce3938faaa30e18f5a6d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
ms.locfileid: "29742773"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Srozumitelná že ID instance pro škálování virtuálního počítače Azure nastavit virtuální počítače
Tento článek popisuje ID instance pro sady škálování a různé způsoby, jejich surface.

## <a name="scale-set-instance-ids"></a>ID instance sady škálování

Každý virtuální počítač ve škálovací sadě získá ID instance, které jednoznačně identifikuje. Tato instance ID se používá v sadě škálování rozhraní API provádět operace, na konkrétní sadě virtuálních počítačů v měřítka. Například můžete zadat ID konkrétní instanci pro obnovení z Image při použití obnovení z Image rozhraní API:

Rozhraní API REST: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (Další informace najdete v tématu [dokumentace k REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

Prostředí PowerShell: `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (Další informace najdete v tématu [dokumentace k prostředí Powershell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm))

Rozhraní příkazového řádku: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (Další informace najdete v tématu [dokumentaci k rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage)).

Můžete získat seznam identifikátorů instance vypsáním všech instancí v sadě škálování:

Rozhraní API REST: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (Další informace najdete v tématu [dokumentace k REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list))

Prostředí PowerShell: `Get-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (Další informace najdete v tématu [dokumentace k prostředí Powershell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm))

Rozhraní příkazového řádku: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (Další informace najdete v tématu [dokumentaci k rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_list_instances)).

Můžete také použít [resources.azure.com](https://resources.azure.com) nebo [sady Azure SDK](https://azure.microsoft.com/downloads/) seznam virtuálních počítačů v sadě škálování.

Přesný prezentace výstupu závisí na možnosti, které poskytnete příkazu, ale tady je ukázka výstupu z rozhraní příkazového řádku:

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

Jak vidíte, vlastnost "instanceId" je právě desetinné číslo. ID instance mohou být znovu použity pro nové instance, jakmile se odstraní původní instancí.

>[!NOTE]
> Je **žádná záruka** v instanci způsob ID přiřazené k virtuálním počítačům v sadě škálování. Možná zjistíte, že jsou postupně roste v některých případech, ale není to vždy. Nepřebírají závislost na určitým způsobem, ve kterém jsou přiřazeny ID instance virtuálních počítačů.

## <a name="scale-set-vm-names"></a>Názvy virtuálních počítačů sady škálování

Ve výše uvedený ukázkový výstup vzrůstá také "název" pro virtuální počítač. Tento název má formu "_ {škálování set-name} {instance id}". Tento název je ten, který se zobrazí na portálu Azure při uvedení instancí v sadě škálování:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

{Instance id} celý název je stejný desetinné číslo jako vlastnost "instanceId", jak jsme vysvětlili výše.

## <a name="instance-metadata-vm-name"></a>Název instance metadat virtuálního počítače

Pokud můžete zadat dotaz [instance metadata](../virtual-machines/windows/instance-metadata-service.md) z v rámci sady škálování virtuálních počítačů, uvidíte "název" ve výstupu:

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

## <a name="scale-set-vm-computer-name"></a>Sady škálování virtuálního počítače, název počítače

Každý virtuální počítač v škálování, nastavte také získá přiřazen název počítače. Tento název počítače je název hostitele virtuálního počítače v [překlad DNS poskytnutých platformou Azure v rámci virtuální sítě](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Tento název počítače je ve formátu "{computer-name-prefix}{base-36-instance-id}".

{Základní-36--id instance} je v [základní 36](https://en.wikipedia.org/wiki/Base36) a je vždy šest číslic délku. Pokud základní 36 vyjádření čísla trvá méně než šest číslic, {základní-36--id instance} doplněno nulami, chcete-li délka šesti číslic. Název počítače "nsgvmss00002D" bude mít například instance s {počítače předpona názvu} "nsgvmss" a instance ID 85.

>[!NOTE]
> Předpony názvu počítače je vlastnost modelu sady škálování, kterou můžete nastavit, takže může lišit od samotný název sady škálování.