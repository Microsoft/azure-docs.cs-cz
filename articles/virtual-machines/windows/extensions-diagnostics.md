---
title: Rozšíření diagnostiky Azure pro Windows | Dokumentace Microsoftu
description: Monitorování virtuálních počítačů Windows Azure pomocí rozšíření Azure Diagnostics
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
ms.openlocfilehash: 772c0a4b6cf57aa428f722fb90a0673f9e703627
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982790"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Rozšíření diagnostiky Azure pro virtuální počítače s Windows

## <a name="overview"></a>Přehled

Rozšíření virtuálního počítače Diagnostika Azure umožňuje shromažďovat data monitorování, jako jsou čítače výkonu a protokoly událostí z vašeho virtuálního počítače Windows. Můžete podrobně určit, jaká data chcete shromáždit a ve kterém chcete data chcete přejít, jako je například účet služby Azure Storage nebo centra událostí Azure. Tato data můžete použít také k vytváření grafů na webu Azure Portal nebo vytvářet upozornění metrik.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření Azure Diagnostics dají spustit pro klienta Windows 10, Windows Server 2008 R2, 2012, 2012 R2 a 2016.

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření Azure Diagnostics vyžaduje, aby cílový virtuální počítač je připojený k Internetu. 

## <a name="extension-schema"></a>Schéma rozšíření

[Rozšíření Azure Diagnostics schéma a hodnoty vlastností jsou popsány v tomto dokumentu.](../../azure-monitor/platform/diagnostics-extension-schema-1dot3.md)

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Schéma JSON, které jsou podrobně popsané v předchozí části lze použít v šabloně Azure Resource Manageru pro spuštění rozšíření Azure Diagnostics při nasazení šablony Azure Resource Manageru. Zobrazit [použití monitorováním a diagnostikou pomocí šablony Azure Resource Manageru a virtuální počítač Windows](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Nasazení v Azure CLI

Azure CLI slouží k nasazení rozšíření Azure Diagnostics do existujícího virtuálního počítače. Nahraďte platný kód JSON z výše uvedeného schématu rozšíření na chráněná nastavení a nastavení vlastnosti. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>Nasazení pomocí Powershellu

`Set-AzVMDiagnosticsExtension` Příkaz slouží k přidání rozšíření Azure Diagnostics do existujícího virtuálního počítače. Viz také [použití Powershellu k povolení diagnostiky Azure v rámci virtuálního počítače se systémem Windows](ps-extensions-diagnostics.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]


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

Data o stavu nasazení rozšíření se dají načíst z portálu Azure portal a pomocí rozhraní příkazového řádku Azure. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Najdete v článku](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) pro pokročilejší Průvodce odstraňováním potíží pro rozšíření Azure Diagnostics.

### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky
* [Další informace o rozšíření Azure Diagnostics](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [Projděte si rozšíření schématu a verze](../../azure-monitor/platform/diagnostics-extension-schema.md)
