---
title: Azure Diagnostics rozšíření pro Windows
description: Monitorování virtuálních počítačů Azure s Windows pomocí rozšíření Azure Diagnostics
author: johnkemnetz
manager: ashwink
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c981214c69653b3a4a687a861cb348f3587def7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89078754"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Rozšíření Azure Diagnostics pro virtuální počítače s Windows

## <a name="overview"></a>Přehled

Rozšíření Azure Diagnostics VM umožňuje shromažďovat data monitorování, například čítače výkonu a protokoly událostí, z virtuálního počítače s Windows. Můžete přesně určit, jaká data chcete shromažďovat a kam chcete data přejít, jako je například účet Azure Storage nebo centrum událostí Azure. Tato data můžete také použít k sestavování grafů v Azure Portal nebo vytváření výstrah metrik.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření Azure Diagnostics lze spustit pro klienta Windows 10, Windows Server 2008 R2, 2012, 2012 R2 a 2016.

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření Azure Diagnostics vyžaduje, aby byl cílový virtuální počítač připojený k Internetu. 

## <a name="extension-schema"></a>Schéma rozšíření

[Schéma rozšíření Azure Diagnostics a hodnoty vlastností jsou popsány v tomto dokumentu.](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure je možné nasadit pomocí šablon Azure Resource Manager. Schéma JSON popsané v předchozí části lze použít v šabloně Azure Resource Manager ke spuštění rozšíření Azure Diagnostics během nasazování šablony Azure Resource Manager. Přečtěte si téma [použití monitorování a diagnostiky s virtuálními počítači s Windows a šablonami Azure Resource Manager](../extensions/diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Nasazení Azure CLI

Pomocí rozhraní příkazového řádku Azure můžete nasadit rozšíření Azure Diagnostics do existujícího virtuálního počítače. Nahraďte vlastnosti Protected Settings a Settings platným JSON ze schématu rozšíření výše. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>Nasazení PowerShellu

`Set-AzVMDiagnosticsExtension`Příkaz lze použít k přidání rozšíření Azure Diagnostics do existujícího virtuálního počítače. Další informace najdete [v tématu použití PowerShellu k povolení Azure Diagnostics ve virtuálním počítači s Windows](../extensions/diagnostics-windows.md).

 


```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z Azure Portal a pomocí rozhraní příkazového řádku Azure CLI. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Podrobný průvodce odstraňováním potíží pro Azure Diagnostics rozšíření [najdete v tomto článku](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) .

### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky
* [Další informace o rozšíření Azure Diagnostics](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [Zkontrolovat schéma a verze rozšíření](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)
