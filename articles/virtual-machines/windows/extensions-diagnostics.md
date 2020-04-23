---
title: Rozšíření Diagnostika Azure pro Windows
description: Monitorování virtuálních počítačů Azure pro Windows pomocí rozšíření Diagnostika Azure
author: johnkemnetz
manager: ashwink
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.openlocfilehash: 1d38a3cac5525de6835bbb0f9873cbd0636d44a9
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869677"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Rozšíření Diagnostika Azure pro virtuální počítače s Windows

## <a name="overview"></a>Přehled

Rozšíření virtuálního počítače Azure Diagnostics umožňuje shromažďovat data monitorování, jako jsou čítače výkonu a protokoly událostí, z virtuálního počítače s Windows. Můžete podrobné určit, jaká data chcete shromažďovat a kam chcete data jít, jako je například účet Azure Storage nebo Centrum událostí Azure. Tato data můžete také použít k vytváření grafů na webu Azure Portal nebo k vytváření upozornění na metriky.

## <a name="prerequisites"></a>Požadované součásti

### <a name="operating-system"></a>Operační systém

Rozšíření Diagnostika Azure lze spustit s Windows 10 Client, Windows Server 2008 R2, 2012, 2012 R2 a 2016.

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření Diagnostika Azure vyžaduje, aby cílový virtuální počítač je připojený k internetu. 

## <a name="extension-schema"></a>Schéma rozšíření

[Schéma rozšíření diagnostiky Azure a hodnoty vlastností jsou popsány v tomto dokumentu.](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure se můžou nasadit pomocí šablon Azure Resource Manageru. Schéma JSON popsané v předchozí části lze použít v šabloně Azure Resource Manager ke spuštění rozšíření Diagnostika Azure během nasazení šablony Azure Resource Manager. Viz [Použití monitorování a diagnostiky se šablonami virtuálních aplikací pro Windows a Azure Resource Manager .](extensions-diagnostics-template.md)

## <a name="azure-cli-deployment"></a>Nasazení azure cli

Azure CLI se dá použít k nasazení rozšíření Diagnostika Azure do existujícího virtuálního počítače. Nahraďte chráněné nastavení a vlastnosti nastavení platným iSON ze schématu rozšíření výše. 

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

Příkaz `Set-AzVMDiagnosticsExtension` lze použít k přidání rozšíření Diagnostika Azure do existujícího virtuálního počítače. Najdete v [tématu použití PowerShellu k povolení diagnostiky Azure ve virtuálním počítači se systémem Windows](ps-extensions-diagnostics.md).

 


```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Poradce při potížích a podpora

### <a name="troubleshoot"></a>Odstraňování potíží

Data o stavu nasazení rozšíření lze načíst z portálu Azure a pomocí azure cli. Chcete-li zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí příkazu Příkaz příkazu Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[V tomto článku](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) najdete podrobnější průvodce odstraňováním potíží pro rozšíření Diagnostika Azure.

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat podporu. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky
* [Další informace o rozšíření Diagnostika Azure](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [Kontrola schématu rozšíření a verzí](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)
