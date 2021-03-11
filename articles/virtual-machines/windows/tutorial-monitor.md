---
title: Kurz – monitorování virtuálních počítačů s Windows v Azure
description: V tomto kurzu se naučíte monitorovat výkon a zjištěné součásti aplikace spuštěné na virtuálních počítačích s Windows.
author: mgoedtel
manager: carmonm
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 8c4345d16efe296413ea9d995c2307bba62d33f8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560697"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Kurz: monitorování virtuálního počítače s Windows v Azure

Azure Monitoring používá agenty ke shromažďování dat o spuštění a výkonu z virtuálních počítačů Azure, ukládání těchto dat do úložiště Azure a zpřístupnění přes portál, modul Azure PowerShell a Azure CLI. Rozšířené monitorování se dodává s Azure Monitor pro virtuální počítače shromažďováním metrik výkonu, zjišťováním komponent aplikace nainstalovaných na virtuálním počítači a zahrnuje grafy výkonu a mapu závislostí.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Povolení diagnostiky spouštění na virtuálním počítači
> * Zobrazení diagnostiky spouštění
> * Zobrazení metrik hostitele virtuálního počítače
> * Povolit Azure Monitor pro virtuální počítače
> * Zobrazit metriky výkonu virtuálních počítačů
> * Vytvoření upozornění

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Ke konfiguraci monitorování a správy aktualizací Azure v tomto kurzu budete potřebovat virtuální počítač s Windows v Azure. Nejdřív pomocí rutiny [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) nastavte uživatelské jméno a heslo správce virtuálního počítače:

```azurepowershell-interactive
$cred = Get-Credential
```

Nyní vytvořte virtuální počítač pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří virtuální počítač s názvem *myVM* v umístění *EastUS*. Pokud ještě neexistuje, vytvoří se skupina prostředků *myResourceGroupMonitorMonitor* a podpůrné síťové prostředky:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Vytvoření prostředků a virtuálního počítače trvá několik minut.

## <a name="view-boot-diagnostics"></a>Zobrazení diagnostiky spouštění

Při spouštění virtuálních počítačů s Windows zachytí agent diagnostiky spouštění výstup na obrazovce, který můžete použít pro účely řešení potíží. Tato funkce je ve výchozím nastavení zapnuta. Zachycené snímky obrazovky se ukládají v účtu služby Azure Storage, který je také vytvořen ve výchozím nastavení.

Diagnostická data spouštění můžete získat pomocí příkazu [Get-AzureRmVMBootDiagnosticsData](/powershell/module/az.compute/get-azvmbootdiagnosticsdata). V následujícím příkladu se diagnostika spouštění stáhne do kořenové složky na jednotce *c:\*.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Zobrazení metrik hostitele

Virtuální počítač s Windows má vyhrazený virtuální počítač hostitele v Azure, který s ním komunikuje. Metriky se pro hostitele shromažďují automaticky a lze je zobrazit na portálu Azure Portal.

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.
2. Pokud chcete zjistit, jaký je výkon virtuálního počítače hostitele, klikněte na **Metriky** v okně virtuálního počítače a potom vyberte některou z metrik hostitele v části **Dostupné metriky**.

    ![Zobrazení metrik hostitele](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Povolit rozšířené monitorování

Postup povolení monitorování virtuálního počítače Azure pomocí Azure Monitor pro virtuální počítače:

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.

2. Na stránce virtuální počítač v části **monitorování** vyberte **přehledy (Preview)**.

3. Na stránce **přehledy (Preview)** vyberte **vyzkoušet nyní**.

    ![Povolení Azure Monitor pro virtuální počítače pro virtuální počítač](../../azure-monitor/vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

4. Pokud máte v rámci stejného předplatného pracovní prostor Log Analytics, v rozevíracím seznamu vyberte na stránce pro **registraci Azure monitor Insights** .  

    Seznam předchází výchozí pracovní prostor a umístění, kde je virtuální počítač nasazený v předplatném. 

    >[!NOTE]
    >Informace o vytvoření nového pracovního prostoru Log Analytics pro uložení dat monitorování z virtuálního počítače najdete v tématu [Vytvoření pracovního prostoru Log Analytics](../../azure-monitor/logs/quick-create-workspace.md). Pracovní prostor musí patřit do jedné z [podporovaných oblastí](../../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).

Po povolení monitorování může být nutné počkat několik minut, než budete moci zobrazit metriky výkonu pro virtuální počítač.

![Povolení zpracování nasazení Azure Monitor pro virtuální počítače monitorování](../../azure-monitor/vm/media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Zobrazit metriky výkonu virtuálních počítačů

Azure Monitor pro virtuální počítače obsahuje sadu grafů výkonu, které cílí na několik klíčových ukazatelů výkonu (KPI), které vám pomůžou určit, jak dobře je virtuální počítač prováděn. Pokud chcete získat přístup z virtuálního počítače, proveďte následující kroky.

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.

2. Na stránce virtuální počítač v části **monitorování** vyberte **přehledy (Preview)**.

3. Vyberte kartu **Výkon**.

Tato stránka nejen zahrnuje grafy využití výkonu, ale také tabulku ukazující pro každý zjištěný logický disk, jeho kapacitu, využití a celkový průměr podle jednotlivých měr.

## <a name="create-alerts"></a>Vytváření upozornění

Na základě konkrétních metrik výkonu můžete vytvořit výstrahy. Výstrahy lze například použít k upozornění, že průměrné využití procesoru překračuje prahovou hodnotu nebo že volné místo na disku kleslo pod určitou velikost. Výstrahy ze zobrazují v portálu Azure Portal nebo je lze odeslat e-mailem. V reakci na vygenerované výstrahy můžete také aktivovat runbooky Azure Automation nebo Azure Logic Apps.

Následující příklad vytvoří výstrahu týkající se průměrného využití procesoru.

1. Na portálu Azure Portal klikněte na tlačítko **Skupiny prostředků**, vyberte **myResourceGroupMonitor** a potom v seznamu prostředků vyberte **myVM**.

2. Klikněte na tlačítko **Pravidla výstrah** v okně virtuálního počítače a potom na **Přidat upozornění metriky** v horní části okna výstrahy.

3. Zadejte **Název** výstrahy, například *mojePravidloVystrahy*.

4. Pokud chcete spustit výstrahu, pokud procento využití procesoru překročí hodnotu 1,0 po dobu pěti minut, ponechte výchozí výběr všech ostatních nastavení.

5. Volitelně můžete zaškrtnutím políčka *Vlastníci, přispěvatelé a čtenáři e-mailů* odesílat oznámení e-mailem. Výchozí akce je zobrazení oznámení na portálu.

6. Klikněte na tlačítko **OK** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali a prohlíželi výkon svého virtuálního počítače. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků a virtuálního počítače
> * Povolení diagnostiky spouštění ve virtuálním počítači
> * Zobrazení diagnostiky spouštění
> * Zobrazení metrik hostitele
> * Povolit Azure Monitor pro virtuální počítače
> * Zobrazení metrik virtuálního počítače
> * Vytvoření upozornění

V dalším kurzu se dozvíte něco o Azure Security Center.

> [!div class="nextstepaction"]
> [Správa zabezpečení virtuálního počítače](../../security/fundamentals/overview.md)
