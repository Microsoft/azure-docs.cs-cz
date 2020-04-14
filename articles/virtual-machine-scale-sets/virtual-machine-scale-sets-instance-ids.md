---
title: Principy ID instancí pro virtuální počítače škálovací sady virtuálních míchací kapacity Azure
description: Principy ID instancí pro škálování virtuálních počítačů Azure sady virtuálních počítačů a různé způsoby, které povrch.
author: mimckitt
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: mimckitt
ms.openlocfilehash: aa8b88bde4b6f8f02f6c9c81d0742d0dede761ac
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273677"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Principy ID instancí pro virtuální počítače škálovací sady virtuálních míchací kapacity Azure
Tento článek popisuje ID instancí pro škálovací sady a různé způsoby jejich povrchu.

## <a name="scale-set-instance-ids"></a>ID instancí škálovací sady

Každý virtuální virtuální virtuální ms ve škálovací sadě získá ID instance, která ho jednoznačně identifikuje. Toto ID instance se používá v škálovací sadě API k operacím na konkrétním virtuálním počítači v škálovací sadě. Můžete například zadat konkrétní ID instance, které se má znovu znázornit při použití rozhraní REIMAGE API:

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (další informace naleznete v [dokumentaci k rozhraní REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

Powershell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (další informace naleznete v [dokumentaci k powershellu](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm))

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (další informace naleznete v [dokumentaci k cli](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Seznam ID instancí můžete získat tak, že vypíšete všechny instance ve škálovací sadě:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (další informace naleznete v [dokumentaci k rozhraní REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list))

Powershell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (další informace naleznete v [dokumentaci k powershellu](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm))

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (další informace naleznete v [dokumentaci k cli](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Můžete také použít [resources.azure.com](https://resources.azure.com) nebo [sad Azure SDK](https://azure.microsoft.com/downloads/) k seznamu virtuálních počítačů ve škálovací sadě.

Přesná prezentace výstupu závisí na možnostech, které zadáte příkazu, ale zde je několik ukázkový výstup z příkazu příkazu příkazu:

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

Jak můžete vidět, vlastnost "instanceId" je pouze desetinné číslo. ID instance mohou být znovu použity pro nové instance po odstranění starých instancí.

>[!NOTE]
> Neexistuje **žádná záruka** na způsob, jakým jsou ID instancí přiřazeny k virtuálním počítačům ve škálovací sadě. Mohou se zdát postupně zvyšuje občas, ale to není vždy případ. Nepoužívejte závislost na konkrétní způsob, ve kterém instancí ID jsou přiřazeny k virtuálním počítačům.

## <a name="scale-set-vm-names"></a>Škálovací sada názvů virtuálních počítačů

Ve výstupu ukázky výše je také "název" pro virtuální ho. Tento název má podobu "{scale-set-name}_{instance-id}". Tento název se zobrazí na webu Azure Portal, když vškálovací sadě uvedete instance:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

Část názvu {instance-id} je stejné desetinné číslo jako vlastnost "instanceId", která byla popsána dříve.

## <a name="instance-metadata-vm-name"></a>Název virtuálního modulu metadat instance

Pokud dotaz [metadat instance](../virtual-machines/windows/instance-metadata-service.md) z v rámci škálovací sady virtuálního času, uvidíte "název" ve výstupu:

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

## <a name="scale-set-vm-computer-name"></a>Název počítače škálovací sady virtuálních počítačů

Každý virtuální počítač ve škálovací sadě také získá název počítače, který je mu přiřazen. Tento název počítače je název hostitele virtuálního počítače v [překladu názvů DNS v rámci virtuální sítě za předpokladu Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Tento název počítače je ve tvaru "{computer-name-prefix}{base-36-instance-id}".

{base-36-instance-id} je v [základu 36](https://en.wikipedia.org/wiki/Base36) a je vždy šest číslic na délku. Pokud základní 36 reprezentace čísla trvá méně než šest číslic, {base-36-instance-id} je doplněnnulnulnula nulami, aby bylo šest číslic na délku. Například instance s {computer-name-prefix} "nsgvmss" a instance ID 85 bude mít název počítače "nsgvms00002D".

>[!NOTE]
> Předpona názvu počítače je vlastnost modelu škálovací sady, kterou můžete nastavit, takže se může lišit od samotného názvu škálovací sady.
