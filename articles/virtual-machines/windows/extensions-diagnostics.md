---
title: Rozšíření diagnostiky Azure pro Windows | Microsoft Docs
description: Monitorování virtuálních počítačích Windows Azure pomocí rozšíření Windows Azure Diagnostics
services: virtual-machines-windows
documentationcenter: ''
author: johnkemnetz
manager: ashwink
editor: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.openlocfilehash: a9621f6f93d8e14e53cfe05ca4a714e88b9d8572
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="windows-azure-diagnostics-extension"></a>Windows Azure Diagnostics rozšíření

## <a name="overview"></a>Přehled

Rozšíření virtuálního počítače služby Windows Azure Diagnostics umožňuje shromažďovat data monitorování, jako je například čítače výkonu a protokoly událostí, z virtuálního počítače Windows. Zadávat lze granularly, jaká data chcete shromažďovat a kam chcete data chcete přejít, jako je například účet služby Azure Storage nebo centra událostí Azure. Tato data můžete také použít k vytvoření grafu na portálu Azure nebo vytvořit metriky výstrahy.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření Windows Azure Diagnostics lze spustit proti klienta Windows 10, Windows Server 2008 R2, 2012, 2012 R2 a 2016.

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření Windows Azure Diagnostics vyžaduje, aby cílový virtuální počítač je připojený k Internetu. 

## <a name="extension-schema"></a>Rozšíření schématu

[Windows Azure Diagnostics rozšíření schématu a vlastnost hodnoty jsou popsané v tomto dokumentu.](../../monitoring-and-diagnostics/azure-diagnostics-schema-1dot3-and-later.md)

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure se dá nasadit pomocí šablon Azure Resource Manager. Schéma JSON, které jsou popsané v předchozí části lze použít v šablonu Azure Resource Manager ke spuštění rozšíření Windows Azure Diagnostics při nasazení šablony Azure Resource Manager. V tématu [použití monitorování a Diagnostika pomocí šablony virtuálního počítače s Windows a Azure Resource Manager](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Nasazení Azure CLI

Rozhraní příkazového řádku Azure můžete použít k nasazení rozšíření Windows Azure Diagnostics do existujícího virtuálního počítače. Chráněné nastavení a nastavení vlastnosti nahraďte platný kód JSON z výše uvedeného rozšíření schématu. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>Nasazení prostředí PowerShell

`Set-AzureRmVMDiagnosticsExtension` Příkaz lze použít k přidání rozšíření Windows Azure Diagnostics do existujícího virtuálního počítače. Viz také [pomocí Powershellu povolit v virtuálního počítače se systémem Windows Azure Diagnostics](ps-extensions-diagnostics.md).
```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Z portálu Azure a pomocí rozhraní příkazového řádku Azure je možné načíst data o stavu nasazení rozšíření. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí rozhraní příkazového řádku Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Najdete v článku](../../monitoring-and-diagnostics/azure-diagnostics-troubleshooting.md) více komplexní řešení problémů s pokyny pro rozšíření Windows Azure Diagnostics.

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte Get podpory. Informace o používání Azure podporovat, najdete v tématu [podporu Microsoft Azure – nejčastější dotazy](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky
* [Další informace o rozšíření Windows Azure Diagnostics](../../monitoring-and-diagnostics/azure-diagnostics.md)
* [Rozšíření schématu a verze](../../monitoring-and-diagnostics/azure-diagnostics-schema.md)
