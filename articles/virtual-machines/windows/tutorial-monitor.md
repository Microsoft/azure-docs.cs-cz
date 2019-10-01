---
title: Kurz – monitorování virtuálních počítačů s Windows v Azure | Microsoft Docs
description: V tomto kurzu se naučíte monitorovat výkon a zjištěné součásti aplikace spuštěné na virtuálních počítačích s Windows.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: a2f4083841c801db3edf1b2838b8d3271b700731
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71680061"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Kurz: monitorování virtuálního počítače s Windows v Azure

Azure Monitoring používá agenty ke shromažďování dat o spuštění a výkonu z virtuálních počítačů Azure, ukládání těchto dat do úložiště Azure a zpřístupnění přes portál, modul Azure PowerShell a Azure CLI. Rozšířené monitorování se dodává s Azure Monitor pro virtuální počítače shromažďováním metrik výkonu, zjišťováním komponent aplikace nainstalovaných na virtuálním počítači a zahrnuje grafy výkonu a mapu závislostí.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Povolení diagnostiky spouštění na virtuálním počítači
> * Zobrazit diagnostiku spouštění
> * Zobrazit metriky hostitele virtuálních počítačů
> * Povolit Azure Monitor pro virtuální počítače
> * Zobrazit metriky výkonu virtuálních počítačů
> * Vytvořit výstrahu

## <a name="launch-azure-cloud-shell"></a>Spustit Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku. Má společné nástroje Azure, které jsou předinstalované a nakonfigurované pro použití s vaším účtem. 

Chcete-li otevřít Cloud Shell, stačí vybrat příkaz **vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Vyberte **Kopírovat** pro zkopírování bloků kódu, vložení do Cloud Shell a stisknutím klávesy ENTER ji spusťte.

## <a name="create-virtual-machine"></a>Vytvořit virtuální počítač

Pokud chcete v tomto kurzu nakonfigurovat službu Azure Monitoring and Update Management, potřebujete virtuální počítač s Windows v Azure. Nejdřív pro virtuální počítač nastavte uživatelské jméno a heslo správce pomocí [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Nyní vytvořte virtuální počítač pomocí [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Následující příklad vytvoří virtuální počítač s názvem *myVM* v umístění *EastUS* . Pokud ještě neexistují, vytvoří se skupina prostředků *myResourceGroupMonitorMonitor* a podpůrné síťové prostředky:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Vytvoření prostředků a virtuálního počítače trvá několik minut.

## <a name="view-boot-diagnostics"></a>Zobrazit diagnostiku spouštění

Při spuštění virtuálních počítačů s Windows zachytí agent diagnostiky spouštění výstup obrazovky, který se dá použít pro účely odstraňování potíží. Tato funkce je ve výchozím nastavení povolená. Zachycené snímky obrazovky se ukládají v účtu služby Azure Storage, který je také vytvořen ve výchozím nastavení.

Data diagnostiky spouštění můžete získat pomocí příkazu [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/az.compute/get-azvmbootdiagnosticsdata) . V následujícím příkladu se Diagnostika spouštění stáhne do kořenové složky jednotky * c: \*.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Zobrazit metriky hostitele

Virtuální počítač s Windows má vyhrazený hostitelský virtuální počítač v Azure, se kterým komunikuje. Metriky se automaticky shromažďují pro hostitele a dají se zobrazit v Azure Portal.

1. V Azure Portal klikněte na **skupiny prostředků**, vyberte **myResourceGroupMonitor**a potom v seznamu prostředků vyberte **myVM** .
2. V okně virtuálního počítače klikněte na **metriky** a potom v části **dostupná metriky** vyberte libovolnou metriky hostitele, abyste viděli, jak funguje hostitelský virtuální počítač.

    ![Zobrazit metriky hostitele](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Povolit rozšířené monitorování

Postup povolení monitorování virtuálního počítače Azure pomocí Azure Monitor pro virtuální počítače:

1. V Azure Portal klikněte na **skupiny prostředků**, vyberte **myResourceGroupMonitor**a potom v seznamu prostředků vyberte **myVM** .

2. Na stránce virtuální počítač v části **monitorování** vyberte **přehledy (Preview)** .

3. Na stránce **přehledy (Preview)** vyberte **vyzkoušet nyní**.

    ![Povolení Azure Monitor pro virtuální počítače pro virtuální počítač](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

4. Pokud máte v rámci stejného předplatného pracovní prostor Log Analytics, v rozevíracím seznamu vyberte na stránce pro **registraci Azure monitor Insights** .  

    Seznam předchází výchozí pracovní prostor a umístění, kde je virtuální počítač nasazený v předplatném. 

    >[!NOTE]
    >Informace o vytvoření nového pracovního prostoru Log Analytics pro uložení dat monitorování z virtuálního počítače najdete v tématu [Vytvoření pracovního prostoru Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Váš pracovní prostor Log Analytics musí patřit do jedné z [podporovaných oblastí](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics).

Po povolení monitorování může být nutné počkat několik minut, než budete moci zobrazit metriky výkonu pro virtuální počítač.

![Povolení zpracování nasazení Azure Monitor pro virtuální počítače monitorování](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Zobrazit metriky výkonu virtuálních počítačů

Azure Monitor pro virtuální počítače obsahuje sadu grafů výkonu, které cílí na několik klíčových ukazatelů výkonu (KPI), které vám pomůžou určit, jak dobře je virtuální počítač prováděn. Pokud chcete získat přístup z virtuálního počítače, proveďte následující kroky.

1. V Azure Portal klikněte na **skupiny prostředků**, vyberte **myResourceGroupMonitor**a potom v seznamu prostředků vyberte **myVM** .

2. Na stránce virtuální počítač v části **monitorování** vyberte **přehledy (Preview)** .

3. Vyberte kartu **výkon** .

Tato stránka nejen zahrnuje grafy využití výkonu, ale také tabulku ukazující pro každý zjištěný logický disk, jeho kapacitu, využití a celkový průměr podle jednotlivých měr.

## <a name="create-alerts"></a>Vytváření výstrah

Můžete vytvářet výstrahy na základě konkrétních metrik výkonu. Výstrahy můžete použít k upozornění, když průměrné využití procesoru překročí určitou prahovou hodnotu nebo je dostupné volné místo na disku pod určitou velikostí, například. Výstrahy se zobrazují v Azure Portal nebo je lze odeslat e-mailem. V reakci na vygenerované výstrahy můžete také aktivovat Azure Automation Runbooky nebo Azure Logic Apps.

Následující příklad vytvoří výstrahu pro průměrné využití procesoru.

1. V Azure Portal klikněte na **skupiny prostředků**, vyberte **myResourceGroupMonitor**a potom v seznamu prostředků vyberte **myVM** .

2. V okně virtuálního počítače klikněte na **pravidla výstrah** a pak v horní části okna výstrahy klikněte na **Přidat upozornění na metriku** .

3. Zadejte **název** upozornění, například *myAlertRule* .

4. Pokud chcete aktivovat upozornění, když procento procesoru překročí 1,0 po dobu pěti minut, ponechte vybrané ostatní výchozí hodnoty.

5. Volitelně můžete zaškrtnout políčko *vlastníkům e-mailu, přispěvatelům a čtenářům* poslat oznámení e-mailem. Výchozí akcí je zobrazení oznámení na portálu.

6. Klikněte na tlačítko **OK** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali a prohlíželi výkon svého virtuálního počítače. Zjistili jste, jak:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků a virtuálního počítače
> * Povolení diagnostiky spouštění na virtuálním počítači
> * Zobrazit diagnostiku spouštění
> * Zobrazit metriky hostitele
> * Povolit Azure Monitor pro virtuální počítače
> * Zobrazit metriky virtuálních počítačů
> * Vytvořit výstrahu

V dalším kurzu se dozvíte o Azure Security Center.

> [!div class="nextstepaction"]
> [Správa zabezpečení virtuálních počítačů](../../security/fundamentals/overview.md)
